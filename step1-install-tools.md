# Prepare your working environment
* Go througth [Prerequisites section](#https://docs.microsoft.com/en-us/azure/logic-apps/create-single-tenant-workflows-visual-studio-code#prerequisites) to set up your working environment.

* [Set up Visual Studio Code](#https://docs.microsoft.com/en-us/azure/logic-apps/create-single-tenant-workflows-visual-studio-code#set-up-visual-studio-code).

* [Connect to your Azure Account](#https://docs.microsoft.com/en-us/azure/logic-apps/create-single-tenant-workflows-visual-studio-code#connect-to-your-azure-account).

* You will need to install [Azurite emulator](#https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) for local Azure Storage development. After you install Azurite, create a local folder in your C drive called azurite and run the following command in VS Codeterminal to start Azurite

```
azurite -s -l c:\azurite -d c:\azurite\debug.log
```

* [VS Code REST Client](#https://marketplace.visualstudio.com/items?itemName=humao.rest-client) to test your Logic App locally. REST Client allows you to send HTTP request and view the response in Visual Studio Code directly.
 