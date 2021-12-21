# Hands-On-Labs - Basic Enterprise Integration with Logic Apps. JSON transformation with Parameters
This hands-on lab implements a part of the scenario based on the content described here - [Basic enterprise integration on Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)

![alt](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/enterprise-integration/_images/simple-enterprise-integration.png)

The scenario above was modified implementing a more complex JSON payload transformation inside of the Logic App instead of doing that in the API Management (APIM) - see green arrows. The complexity is increased by having some parameters needed by the JSON transformation: 

![](docs/media/2021-12-20-18-39-32.png)

## Scenario overview. Contoso Books, Ltd

Contoso Books, Ltd. is a small but growing company that offers digital purchases of books. They attract new customers through their aggressive and creative advertising strategy and by providing an inventory that is comparable to what the larger online book stores offer.

Contoso Books has recently completed an online book ordering deal with a number of local book selling companies, which promises to expand both their book catalog and customer base. The book ordering process from Contoso Books e-Commerce site needs to be extended for ordering books from the local stores. 

Since each local book store implements their own order HTTP-REST-API, Contoso Books e-Commerce REST-API needs to integrate with the new partners. The integration will transform JSON payload from APIM, add some configuration data for the individual local store parameters and call these services.

Since Contoso Books team is consisting of IT professionals and not software engineers, they decided to use Azure Logic Apps for the integration purposes.

