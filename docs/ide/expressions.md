---
id: expressions
title: Expressions
sidebar_label: Expressions
---

This section explains how to use expressions inside Cafienne.

## CMMN Expressions
CMMN supports expressions in many different places of the case model.
It however is not very explicit about the actual implementation, other than specifying the expression language and the expression body.
The body must be a `String`. Some expressions must result in a `Boolean` value.

![Image](assets/ide/cmmn-expression.png)

### Default language

Each expression inside a case model needs a different language. But, as we can read in the above snippet of the CMMN 1.1 version, the language can take a default from the overall definition.
Inside the Cafienne IDE this can be set at the case level properties.

![Image](assets/ide/default-language.png)

## Cafienne Expressions - Pick the language of choice

The Cafienne engine has been designed for openness. This goes into the veins of the product, including the level of the expression language.
The engine ships with a number of languages - but the default language of choice is `spel` the Spring Expression Language

> For handling REST services responses in Process Tasks, the [Jayway JsonPath](https://github.com/json-path/JsonPath) is a better match, as it has shorter filtering notations.
> `json` is the attribute value to set on these expressions.

### Spring Expression Language (SPEL)

[Spring.io](https://spring.io/) comes with a powerful expression language. Cafienne exposes the benefits of this language as the default in case models.

### Case model expressions
Expressions can be used in the following places of a case model
- Tasks (see also [parameter-mapping](parameter-mapping))
- Entry and exit criteria - if only
- Item control - repeat & require
- Planning - Applicability rules
- Timer expressions
- Workflow assignment and due dates

![Image](assets/ide/various-expressions.png)

## Expression Context
In quite a few occasions, the context of the expression is required as an input parameter to the expression.
Cafienne provides a rich API to use detailed case information inside expressions.
This section describes the properties and methods available.

### Root Object
In each expression a number of fixed properties and methods is available at the root level.
| Name       | Description   | 
|------------|------------|
| `case` | The current case at hand |
| `user` | The current tenant user context |
| `map(...)` | A method to create a map of <key, value> pairs. This is typically used in task output mappings. |
| `list(...)` | A method to create a list of objects, also for use in the output mappings, to create a structure that matches the target Case File Item for update or replace. | 

For item control rules (repeat, required), criterion if parts and applicability rules the following additional root properties are available.
| Name       | Description   | 
|------------|------------|
| `task` | If the rule is defined on a task |
| `stage` | If the rule is defined on a stage |
| `milestone` | If the rule is on a milestone |
| `timer` | If the rule is on a timer event listener |
| `caseFileItem` | A reference to the case file item passed in the context (see also CaseFileItemAPI) |
| `[name]` | A named reference to the _value_ of the case file item passed in the context. E.g., in the Helloworld example above, `Greeting` directly refers to the Case File Item value, enabling access to the `To` property. |


### Case API
This section describes the properties and methods available on the root `case` object.
| Name       | Description   | 
|------------|------------|
| `plan` | The case plan, see [Case Plan API]()  |
| `file` | A reference to the Case File, which enables access by name, e.g. `case.file.Greeting` returns the value of the `Greeting` case file item |
| `team` | The case team, see [Case Team API]() |
| `id` | The case instance identifier | 
| `name` | The name of the case definition | 
| `tenant` | The name of the tenant in which the case instance is running | 
| `parent` | If the case is a sub case, this refers to the identifier of the parent case | 
| `root` | If the case is a sub case, this refers to the identifier of the root case | 

### Case Plan API
Tasks, Stages, Milestones and Events all have the following properties
| Name       | Description   | 
|------------|------------|
| `id` | The identifier of the item | 
| `name` | The name of the item | 
| `index` | The index of the item, for usage when the item has a repetition rule defined |
| `state` | The current state of the item |
| `stage` | The stage in which the item is created |

#### Additional Stage Properties
| Name       | Description   | 
|------------|------------|
| `items` | A list of all items currently created in the stage. This list is accessible by name. It can also be used to e.g. count the number of items currently created in the stage | 
| `[item_name]` | all items inside the stage are directly accessible by name. Note that for a repeating item, the name based accessor returns a list of all created items with that name |

#### Additional Task Properties
| Name       | Description   | 
|------------|------------|
| `input` | A name based map with all task _implementation_ input parameters, i.e. the parameters as passed into the task's implementation | 

### Case Team API
Every case instance has it's own team with members. The Case Team API enables access to the team.

> Note: this is an experimental API, which may be subject to change

| Name       | Description   | 
|------------|------------|
| `members` | A list of all members in the team. Note that this can be both tenant users and tenant roles. | 
| `users` | A list of all members of type `user` | 
| `owners` | A list of all members that are case owner | 
| `roles` | A list of all case role names | 
| `role.[role_name]` | A container to access a specific case role and the members that are assigned to that role |

### User API
The root level `user` property exposes the current user's id, and, if registered in the system, name, email address and tenant roles.
| Name       | Description   | 
|------------|------------|
| `id` | The user id | 
| `name` | Optional user name | 
| `email` | Optional email address of the user | 
| `roles` | A set with the roles of the user within the tenant in which this case is running. Note these is **not** the roles that the user has within the case | 
