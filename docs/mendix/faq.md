---
id: faq
title: DCM Frequently Asked Questions
sidebar_label: FAQ
---

## Supported databases

In order to use the DCM module, you need to have Postgres (preferred) or MS SQL Server. 

## Start Case cannot find my case

When you create a new case model and want to use it, you need to Deploy the model inside the DCM editor.
See [Deploy the model](designmodels#deploy-the-model) for all the details. 
Note that you do not need to restart after pressing the deploy button. 

## WaitFor issues

**You cannot use the WaitFor token in the same transaction**

When a WaitFor action is used, you use the WaitForToken returned by a previous java action. 
These java actions change the state of the Case Instance and need processing time to handle the transaction
in full. 

The microflow responsible for calling a java action that changes state **cannot** contain the WaitFor java action.
When you call both in the same microflow, they will execute in the same transaction. That results in a block inside 
the DCM module and the WaitFor will timeout. 

You have to use the WaitFor in a separate transaction. When you build user interface components, use a nano flow that calls 
the microflow with the state changing action and another nanoflow that will execute the WaitFor action.

The Testframework can be used in a way that it starts a separate transaction in a microflow. Take a look at the documentation on Unit Testing.
