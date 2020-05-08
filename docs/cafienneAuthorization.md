---
id: cafienneAuthorization
title: Cafienne Authorization Overview
sidebar_label: Authorization
---

## Introduction
The Cafienne Engine holds a multitenancy architecture that enables resource sharing while preserving strict data isolation.
This page gives an overview of the authorization model in the Cafienne Engine.
If you prefer to read pictures rather than text, you can go to [this page](cafienneAuthorizationPictures.md).

This page gives an overview of
- Authentication model through [OpenID Connect](https://en.wikipedia.org/wiki/OpenID_Connect)
- Authorization models at various levels
    - User authorization
    - Platform level authorization
    - Tenant level authorization
    - Case level authorization
        - CMMN language level authorization
        - Cafienne specific authorizations that are not part of CMMN.

The basic concept of ownership is implemented at each of these levels. A level has a group of owners, and owners can change the rights of users and manage each other (add/remove another owner).

## Users and Authentication
A detailed overview of authentication can be found in [Cafienne Authentication](cafienneAuthentication.md).

The short version: users can access Cafienne through `JWT` tokens.
Internally, Cafienne transforms a `JWT` token into a `Platform User` structure, holding a list of all the tenants the user belongs to.

## Platform level authorization
A platform user does not have any privileges inside the Cafienne Engine except for one - retrieve it's own details.

### Get User Information
The REST call `GET /platform/user` retrieves the user details. This consists of the `user id` and the list of tenants that the user is a member of, including the set of `roles` the user has within the tenant, and the optional fields `name` and `email` address.
So with a valid JWT token it is possible to retrieve your own user details. Other than that, nothing can be done as a platform user, unless the platform user belongs to the group of `platform owners`. 

### Platform Owners
In the configuration file of the Cafienne Engine, the property `platform.owners` can be filled with a list of `user ids` that are platform owner.
This means: when a `JWT token` is valid, and the value of the `sub` claim is part of that list, then without any further ado this platform user is a `platform owner`. Platform owners have the following privileges:
- add or disable other platform owners
- create tenants
- disable/enable tenants

#### Creating but not maintaining
A platform owner has no rights inside any of the tenants.
When creating a tenant, a list of users must be passed that belong to the new tenant. These users automatically become the `tenant owners`.

So, a platform owner can _create_ a tenant, but once the tenant is created, the platform owner can no longer _make changes_ to the group of owners of that tenant.

Obviously a platform owner can also add himself to the list of tenant owners when creating the tenant.

#### Platform bootstrapping
The configuration file of the Cafienne Engine has an option to load and create a tenant with users when it is started. If the tenant already exists, the call is not executed. So this mechanism cannot be used to maintain the tenant.

## Tenant level authorization
Tenant level authorization is also based on two groups:
- Tenant users 
- Tenant owners

Tenant users can work on cases that belong to the tenant. This can be starting of cases, reading tasks of cases, claiming and completing task, etc.
Note that this does not mean that tenant users can do this on all cases. Fine grained case level authorizations are described below. But in order to do anything on a case a user must at least be a `TenantUser`.

Tenant owners can register and maintain `TenantUsers`. Furthermore they can add or remove tenant owners, including themself. Only the last tenant owner cannot remove himself.

### Tenant user maintenance
Tenant owners can perform the following operations on tenant users:
- `Create tenant user` - The user must have a `userid` and a set of `roles`, e.g. `["Employee", "Purchaser"]`. Note that the set can be empty as well, meaning that the new tenant user has no roles.
- `Add role` and `Remove role` on a tenant user.
- `Disable` and `Enable` a tenant user. Note that a tenant user cannot be removed, only disabled.

### Tenant user rights
Tenant users can retrieve the list of tenant users, and also individual tenant users by id.

## Case level authorization
At the level of individual cases we can also set various authorizations.
The [CMMN specification](https://www.omg.org/spec/CMMN) defines a limited set of authorization rules:
- Execution of Human Tasks can be limited to a specific role
- Raising of User Events can be limited to a set of roles
- Planning of Discretionary Items can be limited to a set of roles

### Tenant Roles are not Case Roles
Note that the term `role` in the CMMN defined authorization is something quite different than the roles that tenant users have. The `CMMN role` is defined within a single case, and only applies to that single case. Tenant roles apply to all cases across the tenant.

A good example is a case of a Patient Treatment record. Within a hospital, there can be many persons with the role `Doctor`. Such a role would typically be associated with the `TenantUser` in the Cafienne Engine.
At the case instance level, where a particular patient is being treated, we have the case role `Treating Physician`, which can only be filled by a `Doctor` obviously, but not all doctors in the hospital are the treating physician for one particular patient.

### Case Team
In order to honour this distinction, every case instance inside the Cafienne Engine has a `CaseTeam`. This team consists of a set of `TenantUsers` that each have one or more `CaseRoles` assigned.

Generally speaking we can say that a `CMMN role` applies _within_ a particaly case instance; we refer to this as a `Case Role` or `Case Team Role`. A tenant user's role can also be referred to as an `organizational role`.

### Cafienne specific implementation
In addition to the above mentioned CMMN rules, the Cafienne Engine also enforces some more authorizations.

#### Human Task authorization
- When claiming a HumanTask, the Cafienne Engine validates whether the user has the required `Case Role` within the Case Team.
- When a HumanTask is assigned to a specific person, only that person can revoke the task.
