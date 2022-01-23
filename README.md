# Hands-On-Labs - Basic Enterprise Integration with Logic Apps. JSON transformation with Parameters
This hands-on lab implements a part of the scenario based on the content described here - [Basic enterprise integration on Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)

![alt](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/_images/simple-enterprise-integration.png)

The scenario above was modified implementing a more complex JSON payload transformation inside of the Logic App instead of doing that in the API Management (APIM) - see green arrows. The complexity is increased by having some parameters needed by the JSON transformation: 

![](docs/media/2021-12-20-18-39-32.png)

## Scenario overview. Contoso Books, Ltd

Contoso Books, Ltd. is a small but growing company that offers digital purchases of books. They decided to attract new customers through their aggressive and creative advertising strategy and by providing an inventory that is comparable to what the larger online book stores offer. 

They created a new cloud based highly scalable e-commerce portal with the e-Commerce Order (ECO) API exposed to it, and need to integrate ECO API with the existing on-premise order processing system MyBookOrder (MBO) API. The integration is done by JSON transformation using some additional configuration parameters needed by MBO API. 

Since Contoso Books team is consisting of IT professionals and not software engineers, they decided to use Azure Logic Apps for the integration purposes.

Contoso Books IT professionals would like to implement CI/CD for both Instrastructure (and treat it as code - IaC) and also for the workflows they develop.  

### Contoso Books sample payloads:

[ECO Payload](eco-payload.json)

[MBO Parameters](params.json)

[MBO Payload](mbo-payload.json)

## Setting up your development environment
---
**NOTE**
No Azure Subscription will be needed for this tutorial.

--- 
In this tutorial we will use Visual Studio Code and not the Azure Portal for the development of the Logic Apps. You can run and debug your Logic Apps on your local development machine. 
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

Create your Logic App

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

There is one important difference between the variables and the action [output values](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-workflow-definition-language#outputs) - you can change the variable value after you initialized it with the [Set Variable action as described here](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-create-variables-store-values), but you cannot do it with the output values of the compose action. We often use Compose action to debug a number of variables and output values. 

Please also be aware of the [implicit data type conversions](https://docs.microsoft.com/en-us/azure/logic-apps/workflow-definition-language-functions-reference#implicit-data-type-conversions)  

Additionally, as you improve your Logic App skills, you might decide to work with the [Expression Editor and the workflow.json code, or directly editing the workflow.json file](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-workflow-definition-language#expressions) 

## Dealing with Objects and Json data in Logic Apps

Let's create a new workflow from the VS Code Command Palette

![](docs/media/2022-01-04-10-28-47.png)

Select Stateful Workflow

![](docs/media/2022-01-04-10-29-30.png)

Give it a name

![](docs/media/2022-01-04-10-30-36.png)

you will see a new workflow folder in your Logic App project

![](docs/media/2022-01-04-10-31-37.png)

Open it in the designer and create a simple HTTP Request-Response workflow similar to one we created above and save it

![](docs/media/2022-01-04-10-34-24.png)

Select your HTTP Request Trigger and click on "use sample payload to generate schema". Your Logic App will create a schema and will be able to parse the HTTP payload into values you can use for the further actions.

![](docs/media/2022-01-04-10-38-14.png)

add one ECO Order payload as a sample to generate schema 

````
    {
        "orderId": "66e62767-c1cd-46b9-8c9e-f8900a790910",
        "orderDate": "2021-03-19T07:22Z",
        "deliveryAddress": {
            "firstName": "Bob",
            "lastName": "Mustermann",
            "city": "Budapest",
            "country": "Hungary",
            "postalCode": "1073",
            "streetAddress": "Liszt Ferenc tér 5"
        },
        "orderItems": [
            {
                "ISBN": "9783060311354",
                "title": "Fahrenheit 451",
                "author": "Ray Bradbury",
                "category": "sci-fi",
                "price": 11.99,
                "quantity": 1,
                "bookStore": "StoreA",
                "bookStoreBranch": "Budapest"
            },
            {
                "ISBN": "9780571258093",
                "title": "Never let me go",
                "author": "Kazuro Ishiguro",
                "category": "sci-fi",
                "price": 12.99,
                "quantity": 1,
                "bookStore": "StoreB",
                "bookStoreBranch": "Budapest"
            },
            {
                "ISBN": "9780316409131",
                "title": "Upheaval: Turning Points for Nations in Crisis",
                "author": "Jarred Diamond",
                "category": "history",
                "price": 11.99,
                "quantity": 1,
                "bookStore": "StoreA",
                "bookStoreBranch": "Eger"
            }
        ]
    }
````

![](docs/media/2022-01-04-10-40-19.png)

and press Done button. You will also be notified that our HTTP requests will need to provide 
````
Content-Type: application/json
````

Select the Response Action and for the add the Request Body to the response content

![](docs/media/2022-01-04-10-45-14.png)

![](docs/media/2022-01-04-10-45-34.png)

Save your workflow and create another test.http file in your new workflow body. This time we use the HTTP POST method and add the ECO single order payload to it 

![](docs/media/2022-01-04-10-50-20.png)

Open your workflows overview and grab the callback URL and insert it between POST and HTTP/1.1 tokens in the test.http file

![](docs/media/2022-01-04-10-54-42.png)

Click on "send request" and see the output of your workflow. You might also decide to explore the run history in the workflow overview.

![](docs/media/2022-01-04-10-58-00.png)

Now we can start exploring the work with Object and Json data.

Add a new Compose action between the Request trigger and the Response action

Click in Inputs in your new Compose action. Because we provided a sample payload to our trigger you are able to see the parsed tokens from our HTTP payload. 

![](docs/media/2022-01-04-11-51-23.png)

There is nothing wrong if the parsed HTTP Trigger body satisfies your requerements and you can use it for your Logic App. But what happens if you want to deal with the json variables and action outputs - how do you navigate through the Json object attributes, nested attributes and arrays? What if you are not using HTTP Trigger in your Logic App but receive a Json object you need to deal with?

Let's create a new variable of type Json and assign its value to the value of the HTTP Trigger Body.

You can remove your Compose action if you already saved the workflow and initialize a new variable

![](docs/media/2022-01-04-12-07-49.png)

Change the response content to this new variable

![](docs/media/2022-01-04-12-08-42.png)

save it and run your http test - it should return the same result, but now from the variable value

![](docs/media/2022-01-04-12-11-21.png)

Now we can dissect our Json object derived from the HTTP Trigger body.

---
**NOTE**
You can also create a Json object from the scratch using the Compose action as shown here 

![](https://docs.microsoft.com/en-us/azure/logic-apps/media/logic-apps-perform-data-operations/configure-compose-action.png) and use it as an output from the Compose action.

---

Add a new Compose action after the Initialize ECOVariable action, switch to the expression editor and type "variables(" it will be autocompleted into "variables()"

![](docs/media/2022-01-04-12-27-13.png)

type

````
variables('ECOVariable').deliveryAddress)
````
---
**NOTE**
although the expression above will work here, in some cases we might refer to none existing Json attributes and that will cause an exception in your Logic App. For that reason we recommend to use the following notation using the ? operator which will substitute null or not existing values into empty strings

````
variables('ECOVariable')?.deliveryAddress)
````

---

Replace the Response content into output of your Compose action

![](docs/media/2022-01-04-12-52-22.png)

Run your test again and validate the result

![](docs/media/2022-01-04-12-54-16.png)

You can also decide and rather [parse your Json object](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-perform-data-operations#parse-json-action) and use user-friendly tokens for the Json properties

![](docs/media/2022-01-04-14-57-11.png)

add the output of the Select Delivery Address action

![](docs/media/2022-01-04-14-59-44.png)
 
Refer to the run history to see the result

![](docs/media/2022-01-11-15-56-42.png)

## Dealing with the Logic App Parameters
In Azure Logic Apps, you can abstract values that might change in your workflows across your development, test, and production environments by defining parameters. When you use parameters instead, you can focus more on designing your workflows, and insert your environment-specific variables later.

This article introduces how parameters work in single-tenant Azure Logic Apps and how to edit, reference, and manage environment variables using the single-tenant parameters experience.

[Create parameters to use in single-tenant Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/parameterize-workflow-app?tabs=azure-portal)

Let's create a new parameter of type object for our Logic App

![](docs/media/2022-01-12-16-10-55.png) 

Paste the Json content of your desired parameters from [MBO Parameters](params.json)

![](docs/media/2022-01-12-16-14-23.png)

close the window an open the parameters configuration again

![](docs/media/2022-01-12-16-15-56.png)

After you save your workflow you will discover a new parameter file at the top level of your Logic App. Be aware that all workflows inside of your single-tenant Logic Apps will share the same parameters. If you wish, you can add additional parameters of different types to your Logic App.

![](docs/media/2022-01-12-16-19-14.png)

As you can see from the content of the files, it is slighthly different from the original content defining the type and the value of the parameter

![](docs/media/2022-01-12-16-27-19.png)

Let's read StoreA parameter values in the Compose action

![](docs/media/2022-01-12-16-38-44.png)

and inspect the value in the run history after triggering the URL in the "test.http" file

![](docs/media/2022-01-12-16-39-42.png)

now let's select just a single parameter in a new compose action again and validate the value in the run history after triggering the URL in the "test.http" file

![](docs/media/2022-01-12-16-44-58.png)

and the result in the run history is 

![](docs/media/2022-01-12-16-45-44.png)

---
**NOTE**
Since we have an array of shop specific parameters, we use the "[]" operator to access one particular shop data. Once we have this array item, we can access its property by the "." operator
Be aware, that this is still best practice to use "?" operator to avoid exception in your workflow. Below are corrected sample expressions

````
parameters('MBOParams')?['StoreA']
````

````
parameters('MBOParams')?['StoreA']?.serviceEndpoint
````

---
## Dealing with secrets

### Move paramater values into the "local.settings.json" file

You probably noticed that some workflow parameter values contain sensitive information which might be stored in your source code repository together with "parameters.json" file.

````
        "serviceEndpoint": "https://order-engine.storeb.com",
        "OAuthTenantName": "storeb.com",
        "OAuthAppId": "61612ba2-5672-4aa2-bfec-18886177871e",
        "OAuthAppSecret": "coreAPIB-BigSecret",

````

Working on workflows locally, you can move these values into the "local.settings.json" file. This file is usually excluded from the source code check-ins because it is included into the [".gitignore"](.gitignore) file. Additionally you will need to refer to these values in the "parameters.json" file

Let's add these parameters to the "local.settings.json"

Now it will look like that 
````
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "WORKFLOWS_SUBSCRIPTION_ID": "",
    "WORKFLOWS_TENANT_ID": "",
    "WORKFLOWS_RESOURCE_GROUP_NAME": "",
    "WORKFLOWS_LOCATION_NAME": "",
    "WORKFLOWS_MANAGEMENT_BASE_URI": "https://management.azure.com/",
    "StoreA.serviceEndpoint": "https://order-engine.storea.com",
    "StoreA.OAuthTenantName": "storea.com",
    "StoreA.OAuthAppId": "2de6a36b-89f0-4ef3-a8ce-191402ed2a1a",
    "StoreA.OAuthAppSecret": "coreAPIA-BigSecret",
    "StoreB.serviceEndpoint": "https://order-engine.storeb.com",
    "StoreB.OAuthTenantName": "storeb.com",
    "StoreB.OAuthAppId": "61612ba2-5672-4aa2-bfec-18886177871e",
    "StoreB.OAuthAppSecret": "coreAPIB-BigSecret"
  }
}

````
All other values above our custom values were created automatically by Visual Studio Code.

### Add values references to "parameters.json" file

Let's add references to these values into our "parameters.json" file

````
{
  "MBOParams": {
    "type": "Object",
    "value": {
      "StoreA": {
        "serviceEndpoint": "@appsetting('StoreA.serviceEndpoint')",
        "OAuthTenantName": "@appsetting('StoreA.OAuthTenantName')",
        "OAuthAppId": "@appsetting('StoreA.OAuthAppId')",
        "OAuthAppSecret": "@appsetting('StoreA.OAuthAppSecret')",
        "retryAttempts": 3,
        "retryInterval": 5,
        "logLevel": "info"
      },
      "StoreB": {
        "serviceEndpoint": "@appsetting('StoreB.serviceEndpoint')",
        "OAuthTenantName": "@appsetting('StoreB.OAuthTenantName')",
        "OAuthAppId": "@appsetting('StoreB.OAuthAppId')",
        "OAuthAppSecret": "@appsetting('StoreB.OAuthAppSecret')",
        "retryAttempts": 5,
        "retryInterval": 2,
        "logLevel": "error"
      }
    }
  }
}

````
Repeat your previous test and ensure you receive the same result

![](docs/media/2022-01-12-16-39-42.png)

We are using the ["appsetting"](https://docs.microsoft.com/en-us/azure/logic-apps/parameterize-workflow-app?tabs=azure-portal#visual-studio-code) expression type to refer to the app settings in the local.setting.json during the local development, and to the Logic Apps "App Settings" for these values after the [Azure deployment](https://docs.microsoft.com/EN-US/azure/logic-apps/edit-app-settings-host-settings?tabs=azure-portal#manage-app-settings---localsettingsjson) 

### Optional - advanced secret protection with App Settings and KeyVault

As [this document](https://docs.microsoft.com/en-us/azure/app-service/overview-security#application-secrets) states : "App Settings and connection strings are stored encrypted in Azure, and they're decrypted only before being injected into your app's process memory when the app starts. The encryption keys are rotated regularly. 
Alternatively, you can integrate your App Service app with Azure Key Vault for advanced secrets management. By accessing the Key Vault with a managed identity, your App Service app can securely access the secrets you need."

[This document](https://docs.microsoft.com/en-us/azure/app-service/app-service-key-vault-references) shows in great details how to refer to the KeyVault secrets from the App Settings. Your KeyVault needs to trust to the identity your Logic App having sufficient policies to access the secrets.

Let me demo how to establish this trust and how to refer to the secrets from the app settings. For that I will deploy the demo logic app into my Azure Subscription.

---
**NOTE**
If you want to go through the steps below, you will need to have an Azure Subsription with the KeyVault and single-tenant Logic App resources.
You can deploy your Logic App to Azure directly from the Visual Studio Code command palette.

---

Here the resources for the newly created Logic App (Application Insights creating was skipped, but you will definitely need it for the production deployments)




## Scenario Solution proposal

As you might have discovered, the solution needs to implement an iteration over each ECO order, select ECO order items for each book store and create an MBO job for each book store contaiing only the selected ECO order items, and adding all necessary parameters values.

picture below is the expected result omitting some details
![](docs/media/draft_result.jpg) 

We will not go into the details of the solution implementation [here](solution/logic), but there are definitely other alternatives for the json array transformation. Feel free to explore the solution and the workflow actions used there. 
1. Clone this repository
2. change the current directory to 
json-transformation-logicapp/solution/logic
3. start VS Code with "code ."
4. start Azurite from the VS Code command palette
5. start the Logic App
6. select ECO2MBO folder and the workflow.json file there and call from the context menu "overview" and copy the workflow endpoint URL from there 
7. create a test.http file as it was done above and provide the [ECO payload](eco-payload.json) and the endpoint URL for the POST request 
8. run the http POST request and see the result
9. go to the "overview" context menu of the workflow, refresh the run history, select the latest run and navigate through the actions results to better understand the workflow logic
10. switch to the context "Open in Designer" from the workflow.json and have a look at the workflow design. 

![](docs/media/jsontransformworkflow.jpg) 

Since we gave each action a custom name and the symbols are looking similar, go to the "About" section of the actions to see the type of action. Here is an example:
![](docs/media/2022-01-15-11-28-47.png)    


---
**NOTE**
The real solution does not return the result as an HTTP response, but rather sends it to the MBO service

---


## CI/CD

Two GitHub actions were created for CI/CD :

Infrastructure as a code - to create all [necessary Azure resources](.github/workflows/IaC_deploy.yml)

and the GitHub action for the workflow [code deployment](.github/workflows/logicapp_deploy.yml) 

More information on CI/CD is [here](solution/README.md) 
