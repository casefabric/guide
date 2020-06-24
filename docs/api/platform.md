---
id: platform
title: Cafienne Platform and Tenant Registration
sidebar_label: Joining the platform
---

This page describes how to manage tenants, users and their roles in the Cafienne Platform.

We recommend that you first read up on the concepts of the Cafienne [authorization model](../engine/authorization).

## /platform
Platform maintenance APIs, like adding tenants, changing platform owners, checking platform health.

#### Create a Tenant: `POST /platform`
Through this method you can create a new Tenant inside the Cafienne Engine.
It is mandatory to pass one or more Tenant User objects. Each of these users will become Tenant Owner and has the authorization to manage the new Tenant.

After creation of the new Tenant, the platform owners no longer have access to the Tenant.

#### Platform Health
- `GET /platform/version` retrieves version information of the Cafienne Engine currently running.
- `GET /platform/health` retrieves a report with various health indicators (e.g. db connectivity).
- `GET /platform/status` retrieves a health summary code, either **`200 OK`** or **`503 Unavailable`**.

#### Authorization for /platform
In order to use the /platform APIs your user id must be configured as a platform owner in the [Cafienne Engine configuration](../engine/configuration).


## /tenant
For maintaining the tenant registration, like adding users, changing roles, etc.


