---
id: fault-handling
title: CMMN Fault Handling Extensions
sidebar_label: Fault Handling
---

## Overview

The CMMN language has very little support for handling task failures.
<br/>
Task failures may typically occur when a web service cannot be reached due to outage.

![Image](assets/extensions/classic-fault-handling-model.png)
**Classic fault handling**

The image above shows an example of fault handling through modeling with pure CMMN.

## CMMN Drawbacks


### Multiple instances of failing task is the only way
The big conceptual drawback here is that new instances of the task "Repeating Call" are created over and over again, until finally one succeeds.
<br/>
There is no option to reactivate the task from a modeling perspective, even though the state machine of Task / Stage below suggests there is one.

![Image](assets/engine/taskStageLifeCycle.png)


### "Failed" is a semi-terminal state / State transitions do not bubble up

Another interesting design element of the language is the fact that Stage instance state propagation is only defined in a top-down manner.
This is described in table 8.9 (on page 117) of the language specification.

Bubbling completion and termination up the chain is described in the auto-complete rule in paragraph 8.6.1 ("Stage.autoComplete").

However, bubbling failures is not defined. Even worse - Failed is defined as a 'semi-terminal state', causing the completion rule of the surrounding stage to trigger.
<br/>
The consequence of this trigger is felt if there are no 'required' rules configured that can only be achieved upon succesful task completion: 
if the task is in failed state, the stage surrounding it may consider itself as completed, bubbling up completion to its parent, up to the case - even though a task inside failed.

We have received feedback from CMMN designers that experienced this as somewhat counter-intuitive.

## CaseFabric Fault Handling

CaseFabric provides for an elegant mechanism to fix the mentioned drawbacks.

### Bubbling failures
First of all, the engine by default bubbles fault transitions to the parent of the failing task or stage, all the way up the instantiation chain.
<br/>
Not only does this stop completion of cases that have failed tasks, the task failures have a much higher level of visibility (at case level).
<br/>
Note that this behavior can be disabled by configuring `interpreter.cmmn-fault-handling = true`.


### Reactivation Criterion

The reactivation criterion has been introduced to enable model driven fault handling, as visible in the image below.

When a task is in Failed state, and the conditions of the criterion are satisfied, the engine will trigger the reactivate transition on the task.

Note that also the reactivation is bubbled into the parent chain, causing the surrounding stage and case to go into state Active again as well.

Note further that it is no longer needed to define a repetition rule at the task to handle a failure.

![Image](assets/extensions/new-fault-handling-model.png)
