---
id: genericUI
title: CaseFabric Generic UI
sidebar_label: Generic UI
---

CaseFabric provides a generic user interface to enable quick testing of the case that you are creating.
The user interface is agnostic to the type of case model that it renders.

In most use cases you will want to develop your own user interface, that fits better with the domain it is intended for.

## Overview of Cases and Tasks
The Generic UI has a simple structure. When a user logs in, the list of tasks and cases that the user has access to will be rendered.
- **My Tasks** are the tasks assigned to the current user.
- **Unclaimed Tasks** are not assigned to a user, and the current user is allowed to claim them.
- **Start Case** will render a list of possible cases that the user can start.
- **My Cases** contains the list of cases in which the user participates.

![Image](assets/ui/myTasks.png)

## Individual Case and Task Instances
Navigating into a task or case will render that specific instance.
 - For a case, the items in the case plan and their state will be rendered.
 - For a task, buttons to e.g. claim or complete the task are rendered.

## Rendering Case and Task Data with JSON Schema
For starting a Case or completing a Task, in most use cases it is required to pass data. The logic of the case depends on the data entered by the user. In most applications using the CaseFabric Engine this is built into the custom User Interface of that application. However, the CaseFabric Engine also provides for a placeholder in which you can store e.g. a JSON document that holds rendering information.

To be more concrete, the Generic UI can render Start Case and Task screens that hold such a JSON schema to enable you to test the wiring of data into the case model.

For instance, the JSON for starting the business application Hello World looks like:

```sh
{
  "schema":  {
    "title": "Greeting",
    "type": "object",
      "required": [
      "Greeting"
    ],
    "properties": {
      "Greeting": {
      "title": "Your greeting to the World",
      "type": "object",
        "properties": {
           "Message": {
              "type": "string",
              "title": "Your message"
            },
            "To": { 
              "type": "string",
              "title": "To whom?"
              },
            "From": {
              "type": "string",
              "title": "From",
              "default": "$CURRENT_USER"
              }
            }
          }
       }
     }
   }
```
If you want to learn more on how to use this method, go to [How to use task UI rendering](genericUIHTM).