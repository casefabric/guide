---
id: design-case
title: Modelling Cases
sidebar_label: Designing
---

This section gives a brief overview of how the IDE supports modeling CMMN Case models.

> This page assumes you have a [**basic understanding of CMMN**](../cmmn/overview)

![Image](assets/designer/casemodel.png)

## ShapeBox
To the left of the canvas is the ShapeBox. This contains all types of CMMN elements that you can put to the drawing.
Simply drag and drop an element from the ShapeBox into the drawing. The editor will give you some feedback as to whether the location where you want to drop the element is compliant with CMMN.

You can also drag and drop items from the Repository Browser (on your left in the IDE) and from the Case File Editor (on your right in the IDE).
This enables you to quickly reuse an existing task or process from the repository. When you drop an item from the Repository Browser, then a case will become a CaseTask inside the canvas, a process will become a ProcessTask and a task will become a HumanTask.

## Halo and Resizer
When you hover over an element in the canvas, and near its border, the Halo and Resizer for that element become visible. Through the Halo you can access various element properties.

## Undo/redo
At the top of the ShapeBox you can find buttons that enable basic undo/redo functionality.