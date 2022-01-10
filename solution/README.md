# Hands-On-Labs - Basic Enterprise Integration with Logic Apps. JSON transformation with Parameters

## DevOps with GitHub

The single-tenant model gives you the capability to separate the concerns between app and the underlying infrastructure. Logic app workflows typically have "application code" that you update more often than the underlying infrastructure. By separating these layers, you can focus more on building out your logic app's workflow and spend less on your effort to deploy the required resources across multiple environments.

![](../docs/media/deployment-pipelines-logic-apps.png)

### GitHub Secrets Setup
Before you can deploy your Logic App infrastructure and workflow code to Azure you need to provide credentials, that  GitHub Actions will use to deploy to your Azure environment.

- Create a Service Principle for your Azure subscription following this [guide](https://github.com/marketplace/actions/azure-login#configure-deployment-credentials)
The output of the ``` az ad sp create-for-rbac --name "{sp-name}" --sdk-auth... ``` command should look like this:
```
{
  "clientId": "<GUID>",
  "clientSecret": "<GUID>",
  "subscriptionId": "<GUID>",
  "tenantId": "<GUID>",
  (...)
}
```
- Save the output of the above command to [GitHub Secrets](https://github.com/marketplace/actions/azure-login#configure-deployment-credentials) and call the secret AZURE_CREDENTIALS
- Create a secret ``` AZURE_SUB ``` with the subscription ID of the subscription you want to deploy to. Subscription IDs can be found using the ``` az account list -o table ``` command.
- Create a secret ``` RG_LA ``` with the resource group name where you want to deploy your Logic App to. 


### Deploying Infrastructure using ARM
The ARM folder contains the ARM templates required to deploy all the required logic app resources.
``` la-template.json ``` file deploys 
- Logic App
- App Service Plan
- Storage Account

