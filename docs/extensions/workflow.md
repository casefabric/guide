---
id: workflow
title: Workflow Extensions for a HumanTask
sidebar_label: Workflow
---

![Image](assets/extensions/workflow-extensions.png)

## Overview

Cafienne provides for a series of extensions to the CMMN HumanTask in order to support enhanced workflow handling.

- Reusable HumanTask implementations
- Extensions to the lifecycle to support Workflow
- Applying Workflow at design time
  - Role based authorizations
  - Dynamic assignment of Human Tasks to users
  - Setting due dates for Human Tasks
- Human Task rendering in Cafienne UI
- Dealing with Task Data
  - Mandatory Task output parameters
  - Storing, Validating and Completing with Task output

## Reusable HumanTask implementations

The Repository Browser holds a list of independent Human Task implementations.
You can drag/drop them onto any case model in order to create a Human Task with that implementation.
<br />
The implementation of the Human Task is stored as a separate document with extension `.humantask`.
<br />
This enables fine-grained source control and reusability across other cases.

![Image](assets/extensions/reusable-implementations.png)

## Lifecycle Extensions

In CMMN, a task and stage have a predefined set of states to support the lifecycle, with `Available`, `Active` and `Completed` as the primary states.
Task input is set when a task becomes `Active`.
In typical workflow scenarios, it may be necessary to know whether an Active task is already picked up by someone, or to delegate one of my tasks to someone else if I'm too busy.

Cafienne Engine added a few states to a Human Task in `Active` state. You can consider them 'sub states'. They do not have any effect in the Case Plan, but merely help in querying tasks.
These states are stored in a custom field `taskState` to avoid confusion with the normal CMMN lifecycle.

Furthermore, Cafienne stores an `owner` and an `assignee` for each HumanTask.

Note that the extra lifecycle steps are optional. E.g. A user with the proper authorizations can complete a task without claiming it.

#### Lifecycle operations
When the task becomes `Active`, it is in sub-state `Unassigned`. It can then be claimed by or assigned to team members with appropriate authorization.

![Image](assets/extensions/task-lifecycle-api.png)

If the task is claimed by a user (or assigned to a user), that user becomes both `owner` and `assignee`, and the task goes into sub-state `Assigned`.

When the user delegates the task to another user, the initial user is still the `owner`, but the other user becomes the `assignee`. The task goes into sub-state `Delegated`.

This process can be reverted through the `revoke` action.

## Workflow Properties - Assignment and DueDate

![Image](assets/extensions/workflow-properties-extended.png)

### CMMN Performer Role
CMMN supports Human Task Authorization through the notion of the "Performer" role. 

Setting this role implies that the Human Task is only available for members of the case team with that role.

> Note: authorizations are managed by case owners. 
> 
> Case owners can directly override actions on tasks. This includes e.g. revoking assigned tasks or directly completing a task that is currently assigned to another user.


### Due Date
The due date of a Human Task can be filled through an expression.

The expression is evaluated in the optional context of a Case File Item, similar to the other expressions in CMMN.

Due date does not imply any behavior in the case engine. It is merely a date/time field that is passed on to the query database,
where it can be used to select, filter and sort tasks.

![Image](assets/extensions/duedate-query.png)

### Dynamic Assignment - Case Team consequences

> Note: dynamic assignment does *not* validate user existence.

The last property in the menu allows for dynamically assigning a Human Task to a user based on an expression.
Using this property has some consequences.
<br />

![Image](assets/extensions/dynamic-assignment.png)

#### Case Team changes
First of all, assigning a task to a user _also_ assigns the user to the case.

Users that are not part of the case team have no access to the case or its tasks. But if a user is assigned dynamically to the task,
that user automatically is added to the case team.
Furthermore, if the user does not have the proper roles, the user will get the required role in the team.

## Form definitions for Cafienne generic User Interface

The [**Cafienne UI**](../getting-started/genericUI) can be used to render any case modeled with Cafienne IDE. 

Human Tasks are rendered through special set of open source components that can interpret JSON Schema.

This set is called [**React JSON Schema Forms**](https://react-jsonschema-form.readthedocs.io).
Please visit their website for extensive documentation.

The format of the task model needs 2 properties
- `schema` describing the data that must be rendered
- `uiSchema` optional rendering suggestions

Cafienne IDE supports basic JSON validation and a simple preview of the form.

![Image](assets/extensions/task-form-rendering.png)

> Note: Cafienne Engine does not put any restrictions on the content of the task model. <br/> The information is passed through the engine as a string and then gets stored into the query database.

## Task Data - Saving, Validating & Completing

Next to completing a task, Cafienne supports saving intermediate task output and output validation.

![Image](assets/extensions/task-data-api.png)

### Saving intermediate task data

If a user does not yet want to complete the task, but needs to switch to a different context, a user interface component can decide to save the data as entered by the user.
Next time the task is opened, the data is available to continue editing.

Furthermore Cafienne Engine supports 2 additional features for validation before the task can be completed to ensure a that task output will not corrupt the case at hand.

### Mandatory output

When you open the input/output mappings of a Task, there is a small checkbox for output parameters,

titled `R`. With this checkbox, it is possible to mark an output parameter as `required`, making task completion validate that the parameter has a value.

If the parameter has no value, the task cannot be completed.


![Image](assets/extensions/mandatory-output.png)

### Intermediate validation

> Note: task output validation is an experimental feature

Cafienne Engine supports a new API call to enable validation of (potential) human task output.
This can be used e.g. to validate a ZipCode or the existence of an email address in your database.

The current functionality is rather basic. The validator must be implemented as a REST service.
The content to validate is posted to the Cafienne Engine. The engine then invokes the REST service with that content.

- If the REST service returns without any content, it is considered valid, and Cafienne Engine responds `202 Accepted`.
- If the REST service returns with some JSON, the content is considered invalid, but Cafienne Engine also responds `202 Accepted`, along with the JSON received from the service.
- If the REST service is unavailable or returns an error, the Cafienne Engine responds with `400 Bad Request`.

![Image](assets/extensions/task-output-validation.png)
