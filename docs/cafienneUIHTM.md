---
id: cafienneUIHTM
title: Human Task Model and Start Case Schema
sidebar_label: How to use task UI rendering
---
In Cafienne UI, there is a method to declare a user interface with a JSON structure that will render a UI for a business user who is executing the Case Plan. You have to declare this JSON structure in the `Human Task Model` in the [Cafienne Modeler](modelPlanItems.md). On top of that, you can declare a User Interface that helps you starting a case instance; for this, use the `Start Case Editor`.

The Human Task Model is an implementation of a Human Task, the Start Case Editor provides you in CafiennUI –in the Modeler next to the button Start Case– a User Interface that allows a business user to enter data to the Case File Item that is modeled as input parameter of the Case Plan.

## Human Task Model
In the Cafienne Modeler (on http://localhost:2081), if you are modeling a Case Model, you can create an *Human Task Model*. You can click in the navigator on the label for Human Task Model; you will see a little panel in which you can create a Human Task Model by entering a `Name` and `Description` and next click on `Create` or `Create+Open`.

In the Human Task Model Editor, you must first enter the Case File Item that are input and output parameters of the task. Next, you can add a JSON structure, like the one that is described in the [previous page](CafienneUI.md).

In the Start Case Editor, you can only enter the JSON structure. You add the Case Input Parameters using the input parameters of the Case.



