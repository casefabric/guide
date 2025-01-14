---
id: business-identifiers
title: Business Identifiers
sidebar_label: Business Identifiers
---

## Introduction
The CaseFabric APIs to retrieve cases and tasks has certain filtering options.<br/>
Just to name a few, you can ask for:
- Cases in state `Completed`, or
- Cases of type `Helloworld`, or 
- Tasks that are assigned to me, or
- Tasks that are due, or
- Tasks in cases of type `Helloworld`

But how about asking for:
- Tasks for `Customers located in India`, or
- Cases where `CustomerLevel=Gold and (Location=India or Location=Netherlands)`

In order to enable these queries, CaseFabric has implemented the notion of **Business Identifiers**

## Concept of Business Identifiers
Within CMMN, you can design an hierarchical Case File structure with nested items and properties. 
Below you see the structure that is modeled in the TravelRequest example.
A TravelRequest has details on the travellers: each Traveller must be listed with name, email address, passport number, etc.

![Image](assets/extensions/casefile-structure.png)

The properties are not set in the Case File Item itself, but rather in the underlying Case File Item Definition. CMMN has added this additional layer in order to make the definition reusable across various types of Case definitions. E.g. the same Traveller structure can be used in the TravelRequest case, but also in a BusTrip case.

The Case File Item Definition holds a list of properties. Within CaseFabric, you can indicate for each property whether it is a Business Identifier or not.
<br/>When it is, CaseFabric will detect changes on that property, and store the value in an index that can be used to query cases and tasks on that property.
<br/>In the example below this means that whenever the Nationality of a Traveller is set to e.g. Netherlands, a query to get cases for Dutch nationality would return both BusTrips and TravelRequests with Dutch travellers.

![Image](assets/extensions/casefile-properties.png)

## GetCases, GetTasks
Within the CaseFabric Engine, the query database has a business_identifier table that holds the case instance id, the name of the business identifier and the value.
Whenever a call to **`GET /cases`** or **`GET /tasks`** is made, next to the other query parameter options we can also pass an identifier filter.

- **`GET /cases?identifiers=Nationality=Netherlands`**
- **`GET /tasks?identifiers=Nationality=Netherlands`**

## Identifier options
The **`identifiers`** query parameter supports some basic search patterns. Combinations need to be passed in a comma separated string.

Below are a number of query examples. In a nutshell, the basic structure and operators are

- **Equality `name=value`** 
- **Inequality `name!=value`** 
- **Any value `name`**
- **Combinations `name1=value, name2=value2`** 

### Multiple identifiers
It is possible to join multiple business identifiers in the same request. The query will be an intersection of results that match both identifiers.

E.g. searching for gold level customers that are Dutch:

**`GET /cases?identifiers=Nationality=Netherlands,CustomerLevel=Gold`**

### Multiple identifier values
When multiple values are passed for the same identifier, cases and tasks will return results that match either value.

E.g. searching for gold or silver level customers:

**`GET /cases?identifiers=CustomerLevel=Gold,CustomerLevel=Silver`**

Combining it with a different parameter is again an intersection, below a query to get Dutch customers with silver or gold level.

**`GET /cases?identifiers=Nationality=Netherlands,CustomerLevel=Gold,CustomerLevel=Silver`**

### Excluding values

With the inequality operator **`!=`** it is possible to rule out mismatching values.

Below query will return all non-Dutch customers.

- **`GET /cases?identifiers=Nationality!=Netherlands`**

### Any value
When the equality operator is not used, the query will return all matches where the business identifier has a value.

E.g. below query returns all tasks for cases that contain a Social Security Number (SSN)

- **`GET /tasks?identifiers=SSN`**



