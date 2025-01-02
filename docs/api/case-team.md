---
id: case-team
title: Case Team membership
sidebar_label: Case Team membership
---

This page describes how to manage the Case Team through the API.

We recommend that you first read about the basic [CaseFabric authorization model](../engine/authorization).

## Case Team Membership and its Implications
The heart of the CaseFabric Authorization is extremely fine-grained. 

> **Every case instance has its own authorization**

And that means, for every case instance, you have to configure and set a Case Team.

Access control to the case instance is enforced by team membership validation. In other words: if you're not in the team, you cannot see the case, nor can you perform any activity in the case.

> **If you're not in the team, you cannot see the case**

### Querying Cases and Tasks
- **`GET /cases`**
- **`GET /tasks`**

All REST APIs to retrieve lists or individual cases and tasks validate case team membership and will only return cases and tasks in which the current user is a member.

### Team Members
The Case Team consists of a set of members. Each member may have case roles. The case roles are used to enforce authorization within the case instance. This is based on the CMMN Authorization structures. E.g., if a HumanTask has set a specific Case Role as performer, then only team members with that Case Role can pick up and perform that task.

### Case Owners
Team members can also get assigned as Case Owners. Ownership extends the rights of the team member. A Case Owner can change the team and also directly intervene and override the CMMN defined authorizations. E.g., if a HumanTask is assigned to a member that has fallen ill, the Case Owner can simply assign that task to another member.

### Tenant Roles as Members
The word "Team" may mislead you to think that only users can become member. 
And, indeed, that was the initial implementation in CaseFabric. That made the case team administration rather cumbersome. Each case needed a team of users to handle it.

This has been overcome with the option to add Tenant Roles as Case Team Member. When a Case Team contains Tenant Roles, then every Tenant User having that role automatically is part of that Case Team.

```json
[{ 
  "memberId": "Employee",
  "memberType": "role",
  "caseRoles": [
    "Requestor"
  ]
}, { 
  "memberId": "Manager",
  "memberType": "role",
  "caseRoles": [
    "Requestor", "Approver"
  ],
  "isOwner" : true
}]
```

## APIs to manage the Case Team

### Get Case Team
- **`GET /cases/{case-id}/caseteam`** retrieves the team of a case instance.<br/>
The call returns a list of members with their case roles and whether they are plain member or also case owner. Note that team membership is required to get a result.

### Set or Replace the Case Team
- **`POST /cases`** the case team can be set when creating a new case instance.<br/>
If a case team is not passed, a team is formed having the user creating the case as its only member and owner.
- **`POST /cases/{case-id}/caseteam`** replaces the current Case Team.<br/>
This call can only be performed by a Case Owner.

#### Case Team Format
Each member in the team must have a `memberId`. This is either the userId of a Tenant User or the name of a Tenant Role. Members without a `memberType` are assumed to be of type `"user"`
```json
[{ 
  "memberId": "lana@example.com",
  "memberType": "user",
  "caseRoles": [
    "Requestor", "Approver"
  ],
  "isOwner" : true
}, { 
  "memberId": "Employee",
  "memberType": "role",
  "caseRoles": [
    "Requestor"
  ]
}]
```
> **`"memberType" :"role"` refers to a Tenant Role, NOT to a Case Role**

### Managing Team Members and their Case Roles
- **`DELETE /cases/{case-id}/caseteam/{memberId}?memberType=role`** removes a Tenant Role from the team
- **`DELETE /cases/{case-id}/caseteam/{memberId}?memberType=user`** removes a Tenant User from the team
- **`DELETE /cases/{case-id}/caseteam/{memberId}`** removes a Tenant User from the team

All these methods will validate whether the memberId is actually part of the team.

- **`PUT /cases/{case-id}/caseteam`** adds or updates an individual Team Member<br/>

#### Member Update Format
To add or update a member, the `memberId` must be specified. `memberType` defaults to `"user"`.
```json
[{ 
  "memberId": "lana@example.com",
  "memberType": "user", // memberType defaults to "user";
  "isOwner" : true|false, // if this field is set, it will update ownership with the value. 
  "caseRoles": [
    // Below case roles will be added if the member does not yet have them.
    "Requestor", "Approver"
  ],
  "removeRoles": [ 
    // Below case roles will be removed from the member
    "Requestor"
  ]
}]
```
> **Updating team membership can only be done by Case Owners.**

> **There must always be one owner in the Case Team. The last owner cannot remove him- or herself.**

> **Case Role names are validated against the Case Definition. An error will be returned if the role is not defined**
