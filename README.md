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


