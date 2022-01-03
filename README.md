# Hands-On-Labs - Basic Enterprise Integration with Logic Apps. JSON transformation with Parameters
This hands-on lab implements a part of the scenario based on the content described here - [Basic enterprise integration on Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)

![alt](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/_images/simple-enterprise-integration.png)

The scenario above was modified implementing a more complex JSON payload transformation inside of the Logic App instead of doing that in the API Management (APIM) - see green arrows. The complexity is increased by having some parameters needed by the JSON transformation: 

![](docs/media/2021-12-20-18-39-32.png)

## Scenario overview. Contoso Books, Ltd

Contoso Books, Ltd. is a small but growing company that offers digital purchases of books. They decided to attract new customers through their aggressive and creative advertising strategy and by providing an inventory that is comparable to what the larger online book stores offer. 

They created a new cloud based highly scalable e-commerce portal with the e-Commerce Order (ECO) API exposed to it, and need to integrate ECO API with the existing on-premise order processing system MyBookOrder (MBO) API. The integration is done by JSON transformation using some additional configuration parameters needed by MBO API. 

Since Contoso Books team is consisting of IT professionals and not software engineers, they decided to use Azure Logic Apps for the integration purposes.

Secrets need to be stored in Azure KeyVault

### Contoso Books sample payloads:

[ECO Payload](eco-payload.json)

[MBO Parameters](params.json)

[MBO Payload](mbo-payload.json)

## Setting up your development environment
We prefer to use Visual Studio Code and not Azure Portal for the development of the Logic Apps. You can run and debug your Logic Apps on your local development machine 
This became possible after the introduction of the Single-Tenant Logic Apps (Standard) resource type.
The Standard resource type allows you to run many Logic App workflows in one Logic App application. This runtime uses the Azure Functions extensibility model and is hosted as an extension on the Azure Functions runtime. [More information here](https://docs.microsoft.com/en-us/azure/logic-apps/single-tenant-overview-compare#logic-app-standard-resource)   

The Logic App (Standard) resource type contain popular managed connectors available as built-in operations. For example, you can use built-in operations for Azure Service Bus, Azure Event Hubs, SQL, and others. Meanwhile, the managed connector versions are still available and continue to work. Some of these connectors can also be used as emulators for your local development. This tutorial will mostly use the built-in connectors.

Another advantage of using Logic App (Standard) resource type is the ability to create stateless workflows which are more suitable for the real-time operations. This turorial will use stateful workflows for the debugging and testing purposes. Only with this option you will be able to see the execution history of you workflows.  

The complete guide for setting up a local development environment [can be found here](https://docs.microsoft.com/en-us/azure/logic-apps/create-single-tenant-workflows-visual-studio-code)


## Dealing with values in Logic Apps
While many Logic Apps samples deal with simple workflows, dealing with more complex data and Json objects requires a good understanding of dealing with values in your logic apps.

Let's explore Logic Apps [Built-In Actions](https://docs.microsoft.com/en-us/azure/connectors/built-in), [Variables](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-create-variables-store-values), [Outputs of Data Operations](https://docs.microsoft.com/en-us/azure/connectors/built-in#manage-or-manipulate-data), Functions and [Expressions](https://docs.microsoft.com/en-us/azure/logic-apps/workflow-definition-language-functions-reference) in a short warm-up excercise.

---
**NOTE**

My Windows machine runs .NET 6 and Azure Functions Core Tools V4, for that reason I use Windows Linux Subsystem (WSL) for the Logic App development where I installed [all necessary tools referred here](https://docs.microsoft.com/en-us/azure/logic-apps/create-single-tenant-workflows-visual-studio-code#tools)  

---

1. Create your Logic App

![](docs/media/2022-01-03-15-26-03.png)

Press Crtl-Shift-P to open VS Code Command Palette, search and select "Azure Logic Apps: Create new Project"

![](docs/media/2022-01-03-15-35-21.png)

Select the folder for your new Logic App:

![](docs/media/2022-01-03-15-32-52.png)

Select Stateful Workflow

![](docs/media/2022-01-03-15-33-53.png)

Provide a name for your workflow

![](docs/media/2022-01-03-15-37-21.png)

After a short while you will see your workflow json file in your VS Code workspace

![](docs/media/2022-01-03-15-39-01.png)

Open your workflow in designer

![](docs/media/2022-01-03-16-15-46.png)

Skip usage of Azure Connectors

![](docs/media/2022-01-03-16-16-37.png)

Search for "HTTP Request" and select the HTTP Request trigger

![](docs/media/2022-01-03-16-18-32.png)

![](docs/media/2022-01-03-16-19-01.png)

In this excercise we are not dealing with the HTTP payload. This trigger was just selected to start the workflow processing and explore the values of the variables and outputs.

---
**NOTE**

We recommend you to install the one of the HTTP/REST Client VS Code extensions. We are using the [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) extension to trigger the workflow.

--- 

Insert a new action into your workflow

![](docs/media/2022-01-03-16-35-20.png)

Search for "Intialize variable" action

![](docs/media/2022-01-03-16-36-42.png)

Create a new Integer variable, give it a name, provide the initial value  and rename the action

![](docs/media/2022-01-03-16-38-55.png)

Add a new step again. Search for the "Compose" action

![](docs/media/2022-01-03-16-41-11.png)


![](docs/media/2022-01-03-16-41-42.png)

Provide it with the value and rename the step

![](docs/media/2022-01-03-16-43-24.png)

Create a new Compose step and rename it 

![](docs/media/2022-01-03-16-44-53.png)

Save it 

![](docs/media/2022-01-03-16-47-06.png)

You will see the red error message an will need to provide the value

Click into "Inputs"

![](docs/media/2022-01-03-16-48-35.png)

select the Expression tab and type "add"

![](docs/media/2022-01-03-16-50-24.png)

open a round bracket and you will see the parameters for this function

![](docs/media/2022-01-03-16-51-33.png)

switch to the Dynamic Content 

![](docs/media/2022-01-03-16-56-28.png)

select intVariable as the first "add" parameter, add comma and select the Compose Variable outputs

![](docs/media/2022-01-03-16-58-22.png)

Now you added a function to the outcomes of your second Compose action

![](docs/media/2022-01-03-17-00-41.png)

If you click into your function, your code looks like this

````

add(variables('intVariable'),outputs('Compose_Variable')) 

````
add a new HTTP Response Action

![](docs/media/2022-01-03-17-10-24.png)

and add the Outputs of the Compose Sum action to the response body

![](docs/media/2022-01-03-17-13-17.png)

Save the workflow

Use Ctrl-Shift-P to open the VS Code Command Palette and start Azurite

![](docs/media/2022-01-03-17-16-19.png)

You will see the Azurite files created emulating some Azure services

![](docs/media/2022-01-03-17-17-20.png)

Run you Logic App with all the workflows (we have just one)

![](docs/media/2022-01-03-17-18-29.png)

![](docs/media/2022-01-03-17-19-04.png)

You will see your Logic App logs here

![](docs/media/2022-01-03-17-20-41.png)

Now we need to create an HTTP request and trigger the Logic App. But first of all we need to find the endpoint URL of the workflow.

Select the workflow.json file and select Overview

![](docs/media/2022-01-03-17-23-41.png)

Here you will find the callback URL of your workflow

![](docs/media/2022-01-03-17-24-51.png)

Create a new file test.http (here we will use the REST Client VS Code extension)

![](docs/media/2022-01-03-17-26-36.png)

create the following content in this file

````
GET  HTTP/1.1
Content-Type: application/json
````

copy and paste callback between "GET" and "HTTP/1.1"

![](docs/media/2022-01-03-17-31-31.png)

Now trigger the workflow from the file

![](docs/media/2022-01-03-17-32-47.png)

You will see the response of the HTTP request with the expected sum we calculated on the right hand side, and below in the Terminal the execution log of the Logic App Azure Functions Extension

![](docs/media/2022-01-03-17-35-46.png)

Let's inspect the Logic App Run History in the Overview window. You can also close the Response window from the HTTP Request.

Click Refresh to see the latest runs in the Overview window

![](docs/media/2022-01-03-17-39-12.png)

You will see the latest run here

![](docs/media/2022-01-03-17-39-57.png)

Open it here

![](docs/media/2022-01-03-17-40-44.png)

and you will see something like that 

![](docs/media/2022-01-03-17-41-21.png)

Now you can click into each Action to see the results of the individual action execution

![](docs/media/2022-01-03-17-42-28.png)

![](docs/media/2022-01-03-17-42-50.png)

![](docs/media/2022-01-03-17-43-22.png)

![](docs/media/2022-01-03-17-43-42.png)



