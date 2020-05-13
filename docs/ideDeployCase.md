---
id: ideDeployCase
title: Deploying a case model
sidebar_label: Deploying
---

Within the Halo of the Case Plan you can select the **Deploy** option.

<p align="center">
  <img src="assets/ide/deploy.png">
</p>

## View CMMN
Within the file system under the Cafienne IDE, cases, processes, tasks, etc. are stored as independent files.
For Cafienne Engine to run a case, these files must be collected and put into a single XML document conforming the [XML Schema Definition of CMMN1.1](https://www.omg.org/spec/CMMN/20151109/CMMN11CaseModel.xsd).

When pressing the View CMMN button, the IDE will compose such a file. It will not store it anywhere, but will only render it in the text area below it.

## Validate
The Validate button can be used to let the Cafienne Engine validate the composed document (this will do additional parsing that is also needed for execution).

## Deploy
The Deploy button does not do much. It will **NOT** deploy the model into the Cafienne Engine. Rather it will write a file to the directory that has been specified in the configuration of the Cafienne IDE.

By default this will write into `/repository_deploy`.

In the default configuration of [Getting Started](gettingStarted), the Cafienne Engine has been configured to read definitions from the same folder as where the IDE deploys definitions.

This scenario is pretty straightforward and a simple mechanism to help developers quickly deploy and test their case models.
