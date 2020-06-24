---
id: case-plan
title: Modelling the Case Plan
sidebar_label: Modelling the Case Plan
---

In CMMN the Case Plan is a description of the lifecycle of the Case. It holds Stages, Tasks, EventListeners and Milestones, and dependencies between them through Entry and Exit Criteria. Stages, Tasks, Milestones and EventListeners are referred to as plan items (items in the plan), and entry and exit criteria are also referred to as sentries. A sentry is a guard on a tower that watches for events of interest.

Events of interest in a case can come from both the Case Plan and the Case File. E.g., when uploading a new document, a task has to be given to a user to review the document.

This dependency is modeled through an creating a Case File Item for the document, a HumanTask called 'Review' and an entry criterion on that task to start it when a document is created.

It is important to realize that the dependency is noted on the task and not on the document. The sentries describe the criteria under which the task starts (or ends - in the case of exit criteria). That can be because a document was uploaded, or because a certain Milestone was achieved, or because of the combination of both.


## Stage

**In CMMN**, a Stage provides a means to group plan items within the context of a case. A stage serves as building block for Case. A Stage contains plan items and sentries. Stages bundle other plan items. When creating our case models, sometimes we have plan items that are closely related and need to be grouped together. These items could be multiple tasks that must be completed before the case can proceed.

**In the Cafienne IDE**, you can drag the Stage icon to the canvas and model its properties using the Properties Palette.

## Task

**In CMMN**, a Task is an atomic unit of work. Therefore, a task represents the execution of actual work. CMMN differentiates between Human Tasks, Process Tasks, Case Tasks and Decision Tasks. A Human task requires a user to perform an action. A Process Task will execute a business process modeled in another modelling language or it could call an external service which triggers external actions. A Case Task will execute another case. This case can run standalone or as a child of the current case. This provides a way of breaking down complex case models into smaller, more manageable pieces. A Decision Task can be used to invoke a decision. 

All tasks use parameters to pass information. These parameters refer to Case File Items. A Human Task needs a performer and therefore the Role concept us used. Also, a Human Task can have a Planning Table and therefore Appicability Rules to a Task.

**In the Cafienne IDE**, you can model a human task, a process task and a case task. Drag the icon for the task to the canvas and model its properties using the Properties Palette.

### Implementation of Human Task and Process Task
**In the Cafienne IDE** for both the Human Task and the Process Task, you can add an implementation. Note that **in CMMN** an implementation of a Process Task –a reference to a Process– is specified, whereas an implementation for a Human Task is not.

You can create an implementation in the IDE, clicking on labels `Processes` or `Human Task Model` and create it by entering a Name and Description, and next using the button Create+Open.

For both implementations you can add using their `Editors` the input and output parameters. Then you can add the implementation to the properties of the Human Task or the Process Task.

> Note that in the IDE you must use this implementation to add a Case File Item to the input and output parameters of a Human or Process Task

Next, in the *process task implementation* you can add an URI that calls the real implementation, eg using `<cafienne:implementation xmlns:cafienne="org.cafienne" class="org.cafienne.cmmn.instance.process.http.HTTPCallDefinition" async="true"/>`.

For using the Human Task Model, see [How to use task UI rendering](genericUIHTM).
​

## EventListener

**In CMMN**, an event is something that “happens” during the course a Case. Events may trigger life cycle transtions of other Plan Items or of Case File Items and their cause is mostly a life cycle transition of another Plan Item or Case File Item. Elapse of time cannot be captured via these “standard events”. Also, it will often lead to very indirect modeling, when any user event, such as approval or rejection of something, has to be captured through impact on data in the Case File or through transitions in lifcycles of Plan Items.

For this reason, CMMN introduces EventListeners, which specialise into TimerEventListeners and UserEventListeners. This enables CMMN to handle any event in a uniform way, namely as “standard events” that denote transitions in lifecycles. These standard events are handled via Sentries.

**In the Cafienne IDE**, you can drag the icons of both EventListeners to the canvas and fill in their properties and attach Sentries from it to other Plan Items. Note that a UserEventListener can only be triggered by a Authorized Role. A TimerEventListener can be triggered by a lifecycle transition on a Plan Item or a Case File Item. 

## Milestones

A Milestone represents an achievable target. They can be used to show that a certain goal has been reached in the case. The conditions that must be met to achieve a milestone can be clearly defined by attaching a sentry to it. A Milestone can also be used as an entry criteria for other plan items. 

Milestones are beneficial plan items in a case, because they allow us to assess a case’s progress. They are not associated with work like a task is, but rather indicate certain conditions have been reached within a case.

**In the Cafienne IDE** you can drag the Milestone Icon to the canvas and model its properties using the Properties Palette.

## Plan item Properties

### Repeat

**In CMMN**, you can model a Repetition Rule that specifies under which conditions a Plan Item will have repetition. Each repetition is an new instance for it. The first instance is nat considered a repetition.

**In the Cafienne IDE**, you can select the property Repeat, and next add the Rule as an expression and add a Case File Item as the context of the Rule. If the Rule is not added, than the Plan Item can be executed infinite, unless the containing Case or Stage is completed or terminated. Default is not repeatable.

### Required

**In CMMN**, you can model a Required Rule. This Rule specifies under which conditions Plan Items will be required to complete or terminate before their containing Stage or Case can complete.

**In the Cafienne IDE**, you can select the property Required, and next optionally add the Rule as an expression and add a Case File Item as the context of the Rule. If the Rule is not added, than the Plan Item is 100% required. Default is not required.

### Manual Activation

**In CMMN**, you can model an Manual Activation Rule. This Rule specifies under wich conditions a Plan Item, once enabled, start manually or automatically.

**In the Cafienne IDE**, you can select the property Manual Activation, and next optionally add the Rule as an expression and add a Case File Item as the context of the Rule. Default is manual activated.

### Auto Complete

**In CMMN**, if a Plan Item is set to “Auto Complete is false” then it requires a user to manually complete it. This is appropriate for Stages and Cases that contain Discretionary Items or non-required Plan Items. 

If AutoComplete set to true, a Plan Item will complete if there are no active children and all required children are in lifecycle state disabled, completed, terminated or failed. If set to false, it will complete if there are no active children, no Discretionary Items planned, all required tasks are evaluated to true.

**In the Cafienne IDE**, you can in the Properties Palette just click AutoComple on or off.

### Plan Item Properties

Various properties can set for plan item:

<table>
  <tr>
    <th style="text-align:left;">Property</th>
    <th style="text-align:center;">Case</th>
    <th style="text-align:center;">Stage</th>
    <th style="text-align:center;">Task</th>
    <th style="text-align:center;">Milestone</th>
  </tr>
  <tr>
    <td style="text-align:left;">Required</td>
    <td style="text-align:center;"> </td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
  </tr>
  <tr>
    <td style="text-align:left;">Repetion</td>
    <td style="text-align:center;"> </td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
  </tr>
  <tr>
    <td style="text-align:left;">Manual Activation</td>
    <td style="text-align:center;"> </td>
    <td style="text-align:center;"></td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
  </tr>
  <tr>
    <td style="text-align:left;">Auto Complete</td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;"></td>
    <td style="text-align:center;"></td>
  </tr>
  <tr>
    <td style="text-align:left;">Applicability</td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">√</td>
    <td style="text-align:center;">human</td>
    <td style="text-align:center;"></td>
  </tr>
</table>
