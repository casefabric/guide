---
id: parameter-mapping
title: Tasks and Parameters - Mapping
sidebar_label: Tasks and Parameters
---

This section gives an overview of using Tasks and Parameters.
It explains how to pass data into Tasks and how to take data out of Tasks after they have completed.

The Task Mappings editor can be opened by clicking one of the curly arrows under the Task.

![Image](assets/ide/mapping.png)

## Tasks and the Case File

Data in a case resides in the Case File.
Tasks can take their input from the Case File. The CMMN specification is a bit cumbersome in this area, but in essence it gives a lot of flexibility to handle data.

In the picture we see the `Greeting` input parameter taken from the Case File and mapped directly into the task parameter `Greeting`.
This is the most straightforward and probably also the most applied pattern.

![Image](assets/ide/map-greeting.png)

The details of the CMMN specification distinguish between 3 important elements.

- Case File Item
- Task Parameter
- Implementation Parameter - in the picture labeled as `Model Input Parameter`

The `Model Input Parameter` is not defined in the case model itself, but in the underlying task.
Furthermore, inside the picture we do not see the label `Task Parameter`, but only a label `Case File Item`.

The editor is hiding some of the complexity of the CMMN specification.

In the above scenario, we just want the Greeting to be passed to the Human Task. The Case File of HelloWorld contains an item named Greeting.
And the SendResponse human task works only on input parameters named Greeting.

![Image](assets/ide/map-greeting-detail.png)

> Note: in order to have a `Model Input Parameter` show up in the mapping editor, it first must be defined in the underlying task implementation.
>
> The list of input parameters in the mapping editor is read only and populated from the input parameters of the implementation.

### Binding steps
Although the editor tries to hide some of the complexity of the specification by choosing smart defaults, it is important to understand the underlying mapping and binding aspects.

#### Input binding
1. Binding Refinement - from Case File to Task Input Parameter
2. Transformation - from Task Input Parameter to Model Input Parameter
#### Output binding
3. Transformation - from Model Output Parameter to Task Output Parameter
4. Binding Refinement - from Task Output Parameter to Case File

Between steps 2 and 3 the underlying task execution is done. The implementation is responsible for it's own mapping.

The transformations enable binding the same task input parameter to be mapped to multiple different input parameters.
And for output parameters of the underlying model, the same is applicable. One implementation output parameter can be mapped into multiple different Task Output Parameters and through that update multiple Case File Items.


## Transformations

During mapping of a Task Parameter to the Model Input Parameter, a transformation can be applied.

This can be used for example to pass only specific properties of a Case File Item into the underlying task implementation.
It can also be used to entirely override the given Case File Item, e.g. to pass the task id or case id or a timestamp "now" into the implementation parameter.

### Expression language

CaseFabric supports multiple expression languages. You can read more about it in the Expression chapter.
The CMMN specification enables you to choose a different language at the level of each individual expression.
In the parameter mapping editor you can choose to deviate from the default expression language of the case by pressing the small `L` button at the left of the transformation. If the choosen language differs from the default language, the `L` button gets a small blue underlining.

![Image](assets/ide/transformation-language.png)


## Binding Refinement (Operations)
CaseFabric provides a small set of operations to enable various patterns of binding the Case File Item to a Task Input Parameter.
Also for binding Task Output Parameters back to the Case File Item a number of operations is supported.

> A critical element in choosing the right operation is the multiplicity of the Case File Item.
> 
> If the multiplicity is `0..*` or `1..*` or simply `*`, the CaseFabric engine treats the Case File Item as an array.

Details are described in the below screenshots.

### Binding Refinement on Task Input Parameters 
![Image](assets/ide/operations-input.png)

### Binding Refinement on Task Output Parameters 
![Image](assets/ide/operations-output.png)

