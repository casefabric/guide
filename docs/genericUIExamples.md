---
id: genericUIExamples
title: Two business applications
sidebar_label: Two business applications
---

## HelloWorld
Let's start with Hello World. A user can send a greeting to another user. From the Generic UI, navigate to Start Case and select the HelloWorld case definition. That will render the following user interface. The form is based on the JSON shown in [Cafienne UI](genericUI.md).

![Image](assets/ui/startUIhelloWorld.png)

In the UI above some user that is known as The administrator sends Lana the greeting “goedemorgen”. Lana can read this message once she has logged in into Cafienne UI. There she will see a Task “Receive Greeting and respond” assigned to her. She can now respond to The administrator. The administrator will receive the response and after that he or she can complete the case. The myCases UI will show the following:

![Image](assets/ui/helloWorldCompleted.png)

You can try it yourself when you have access to the `getting-started` repository. The environment contains some predefined user next to the administrator that you can use in the demo: Lana, Gerald, Hank and Suzy.

## Travel Request
Travel Request has a more elaborated case model. It supports a common procedure in many organizations: business travel. One of the team members needs to organize a trip to visit a client. They would have to fill in a form, email it to their supervisor, and get the approved form back from the supervisor. After the trip, they would need to fill in another form detailing travel expenses, submit this form for approval, and finally submit their approved expenses to accounting for reimbursement. This example relies heavily on the requestor to manage the entire process by keeping track of where they are in the process and moving it along manually. 

> Perhaps travel request approval feels more like a business process to you than a case. So, yes, you can use Cafienne to build business processes as well. 

Using Cafienne, you can break down this request into smaller processes: Requesting Travel, Approving Travel, and Submitting the Expenses, as shown in the model below.

![Image](assets/ui/modelTravel.png)

We can make sure there are no more incomplete request forms by requiring specific information before it is submitted. We can define an approver role so that once the travel request is created, the appropriate supervisor is notified of the request. After approval, the process automatically moves onto the final step and lets the requestor know their request is approved. It assigns them a new task to submit their travel expenses after the trip.

The order of these tasks is important, and Cafienne manages the entire process. It delegates tasks to the correct team members since these roles can be clearly defined when designing the model. This removes the burden from the requestor of figuring out who they should send a request to, remembering where they are in the process, and prevents them from sending it to the wrong team member.

> You can try it yourself when you have access to `getting-started`.

## Explaining the flow of the business apps

### Log in
So assume Hank want to sent a message to Lana. For that he can use the HelloWorld case model in `getting-started`. First of all, Hank needs to log in. For this, he will be redirected to the Dex IDP login page. Using `hank@example.com` with password `hank` he will get a security token that allows him to start a case and send a greeting to Lana.

### Start a case
Using post/cases Hank can start the Case. The body will look like:

```sh
{  
  "definition":"HelloWorld.xml",
  "inputs": {
    "Greeting": {
      "Message":"Hi",
      "To":"lana",
      "From":"hank"       
      }    
   }
}
```
`post/cases` will respond with a message including the caseInstanceID and the name of the case:

```sh
{  
  "caseInstanceId": "2fdfba88_3331_4fe0_a3cf_83e4f6cc5b55",
  "name": "HelloWorld"
}
```
### My Cases
Hank can now see his Cases by calling the method get/cases. He can also look at the case he just started calling get/cases/{caseInstanceID} using the parameters caseInstanceID. Following is an excerpt of the response of the latter:

```sh
{
  "casefile": "..",
  "lastModifiedBy": "hank",
  "definition": "HelloWorld",
  "planitems": [ 
    {
      "name": "HelloWorld",
      "currentState": "Active",
      "type": "CasePlan",
      ...
    },
    { 
      "name": "Receive Greeting and Send response",
      "type": "HumanTask",
      "transition": "Start",
      ... 
    },     
    {
      "name": "Read response",
      "currentState": "Available",
      "type": "HumanTask",
    } 
  }  
}
```
> You can follow the API-calls using your browser inspector

Note that the engine returns a JSON in which all plan items and case file items are comprised. This case file, identified by a key, has a definition ‘HelloWorld” and is last modified by Hank. The case file compromises all the plan items of the case. The first plan item in the list is the Case itself, described with name “HelloWorld’ and type “CasePlan”. The two Human Tasks in the model are also loaded into the JSON of this Case Instance. The data on currentState, historyState, and transition reflect the lifecycle of each Plan Item. Publishing all modeled Plan Items in the Case Instance implicates that the Case Plan is immutable: the plan of the instance is not aware of later changes in the plan of the model.
Next to the Plan Items, the JSON comprises the team, which is not populated by Hank, a description of the attached casefile, and the file itself, which reflects the Case File Items and its properties.

### My Tasks
Next in the executing of the case, Lana should perform the human task Receive Greeting and Send Respond. Using the API, Lana must first login using post/identity/login. Next, she can retrieve all cases in which she is a team member and tasks that are assigned to her by invoking the API-method **GET /cases/user**. Then Lana can select a Task and execute it.

### Executing a task
Executing the task results in a call to the API-method **POST /tasks/{taskId}/complete**. The taskId is the ID-value in the JSON structure that refers to the Human Task Receive Greeting and Send Response. The transition is one of the actions that sets the state of the plan item lifecycle, default “complete”.

![Image](assets/ui/postTask.png)

Now, Lana has sent in a transition in the life cycle of a plan item. Cafienne will evaluate this transition and accordingly set other plan item states to available, disabled, or completed.
