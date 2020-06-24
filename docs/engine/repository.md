---
id: repository
title: Cafienne Repository
sidebar_label: Repository
---

## Introduction
The Cafienne Engine runs CMMN based case instances. [CMMN](../cmmn/overview) is the official language to describe the definition of a case.
If you want to start a case in the Cafienne Engine, you must provide the definition.

**Start Case example**
```
HTTP:POST from [sending-user] to http://localhost:2027/cases
{
  "inputs": {
    "Greeting": { ... }
  },
  "caseTeam": { ... },
  "definition": "helloworld.xml",
  "tenant": "World-Wide-Test-Tenant",
}
```
The **definition** property is topic of interest for this page. The remainder of the format is explained in the [Cafienne API](../api/start-case) section.
<br/>In this example, **definition** seems to refer to some sort of .xml file called _helloworld_.

But how does the Cafienne Engine know where to find that file? This is achieved through the Cafienne Repository.

This page describes the various configuration options for providing case definitions.

- File Based Repository (default)
- Custom Repository 
- No Repository

All types of repository implement a rather straightforward API which is exposed through the Cafienne Engine.

## Cafienne Repository API overview
The repository can be loaded and queried through the Cafienne Repository API.

- **`GET /repository/list`**<br/>
Returns a list with all names and descriptions of case definitions in the current repository.
- **`GET /repository/load`**<br/>
Returns the contents of a single CMMN definitions document in the repository.
- **`POST /repository/deploy`**<br/>
This will ask the repository to store the posted CMMN contents.
- **`POST /repository/validate`**<br/>
To validate a CMMN definition. This is used from e.g. the Cafienne IDE.<br/>
Note that this API bypasses the configured repository since it works solely on the posted content.

## Repository Configuration
Most use cases of the Cafienne Engine can be handled with the File Based Repository described below.

Nevertheless, Cafienne Engine has made the repository a configurable interface. You can configure this in the **application.conf** through the **provider** property.


![Image](assets/engine/repository-configuration.png)


Please contact us when you have a use case that requires a custom repository.

## File Based Repository
The default setup for the Cafienne Engine is to read definitions from the local file system. 
Whenever a StartCase call is done, the engine will ask the repository to give the contents of the filename passed.

For the example request above, the repository will be asked to return the contents of 'helloworld.xml'.

In the [**application.conf**](configuration) file you can specify the following parameters for the File Based Repository

### Location
The location property indicates the root folder in which to read or write definitions. Files will be opened through the plain Java runtime file access of the engine. 
This means it can also point to e.g. a shared folder.

In addition, although the Repository API enables you to deploy definitions, you can also copy, edit, and version files through standard file system routines and source control mechanisms.

### Cache
The File Based Repository will cache the definitions in memory, to avoid XML definition parsing for each and every StartCase request.
Nevertheless, each time StartCase is invoked, the repository will first check whether a new version of the file is found in the file system, based on file timestamp comparison (allowing you to rollback to an older version as well).

You can configure the size of the cache. By default it stores 100 definitions on a least recently used (LRU) algoritm.

## Without Cafienne Repository
Cafienne ships out of the box with a special implementation that can be configured by setting the provider to **`org.cafienne.cmmn.repository.StartCaseDefinitionProvider`**


![Image](assets/engine/repository-configuration-scdp.png)


The implementation does not support listing, loading, deploying or reading like the File Base Repository.
It will simply ignore those calls.

Instead, this type of repository requires you to pass the actual case definition when you invoke StartCase itself.
<br/>Underneath the Cafienne Engine parses and caches the definition in a manner similar to the File Based Repository, so you can also specify the same LRU cache.

### Each Case has its own Definition
This type of repository gives you the ultimate freedom to start each case with its own definition.

> Please note that the **entire Cafienne Engine** has been built on this concept.

Every case has its own definition. So in theory querying for cases with the same definition is possible, but will never result in more than one case.
In practice this may not sound handy. Therefore we recommend you to read up on business identifiers.

### StartCase example

An example of starting a case looks something like this

```
HTTP:POST[9] from [sending-user] to http://localhost:2027/cases
{
  "inputs": {
    "Greeting": { ... }
  },
  "caseTeam": { ... },
  "definition": "<definitions><case id="helloworld.case" name="HelloWorld" description="Hello World">...</definitions>",
  "tenant": "World-Wide-Test-Tenant",
}
```

Note that when you configure this repository, the first StartCase example that reference the definition through a file helloworld.xml will return a failure. 