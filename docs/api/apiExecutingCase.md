---
id: apiExecutingCase
title: Executing the case
sidebar_label: Executing the case
---

Executing a case means that case workers execute tasks, post the completion of the task to CaseFabric. This will procees untill all required planned items are completed; other not required or discretionary tasks may have been available but did not need to be executed according the case team. 

First, you need to know which tasks are avaliable. For this,  retrieve all **discretionary tasks**. Next, you can plan the discretionary task which will become a *planned item*. Then, you can retrieve all **planned items**.

> See for further exploration of retrieving this information [Retrieving cases and tasks](query-cases-tasks).

On human tasks and planned discretionary items you can perform **workflow actions**. Next you can perform **lifecycle actions** on all planned items. 

## Planning Discretionary tasks

In the case model discretionary human tasks can be designed. This tasks can be planned by the discretion of a case team member. Available discretionary tasks can be retrieved using **GET /cases/{caseinstanceId}/discretionaryitems**. 

Next, you can plan at your discretion such a task by invoking **POST /cases/{caseinstanceId}/discretionaryitems/plan** using the body
```ssh
{ 
  "name":"...", 
  "definitionId":"...", 
  "parentId":"..." 
}
```
This call will respond with a planitemId. This plan item will also be retrieved by calling again **GET /cases/{caseinstanceID}/planitems**. 

## The lifecycle of tasks

Using workflow, a user fulfilling a role in a case instance can claim, revoke or delegate a human task. Also, a user fulfilling a “planning role” can assign directly a planned task to a user. There is an sequence in the workflow you have to adhere to.
- First a task must be assigned or claimed. 
- A task claimed or assigned to you can be revoked or delegated to another user.
- A delegated task can be revoked, in which case it will be assigned again to the previous user.
- If the original user also revokes the task, it can again be assigned or claimed.
- Task completion can only be done when the task is assigned to user.
- Task data that has been entered by the user can also be saved temporarily. This will not complete the task.
- Task data can also be verified through an external REST call. This is an experimental feature.

You can orchestrate this workflow of planned human tasks using the below APIs.

![Image](assets/api/taskAPI.png)
