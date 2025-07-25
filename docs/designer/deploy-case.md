---
id: deploy-case
title: Deploying a case model
sidebar_label: Deploying
---

Within the Halo of the Case Plan you can select the **Deploy** option.


![Image](assets/designer/deploy.png)


## View CMMN
Within the file system under the Case Designer, cases, processes, tasks, etc. are stored as independent files.
For CaseFabric Engine to run a case, these files must be collected and put into a single XML document conforming the [XML Schema Definition of CMMN1.1](https://www.omg.org/spec/CMMN/20151109/CMMN11CaseModel.xsd).

When pressing the View CMMN button, the IDE will compose such a file. It will not store it anywhere, but will only render it in the text area below it.

## Validate
The Validate button can be used to let the CaseFabric Engine validate the composed document (this will do additional parsing that is also needed for execution).

## Deploy
The Deploy button does not do much. It will **NOT** deploy the model into the CaseFabric Engine. Rather it will write a file to the directory that has been specified in the configuration of the Case Designer.

By default this will write into `/repository_deploy`.

In the default configuration of [Getting Started](../getting-started/overview), the CaseFabric Engine has been configured to read definitions from the same folder as where the IDE deploys definitions.

This scenario is pretty straightforward and a simple mechanism to help developers quickly deploy and test their case models.
