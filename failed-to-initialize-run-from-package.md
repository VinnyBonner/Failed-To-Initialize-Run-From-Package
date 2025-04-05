# Troubleshoot error: "Failed to initialize run from package"

The _Azure Functions Runtime is unreachable_ error has many potential causes. The most common reason is that the function app no longer has access to its linked storage account. If you haven't already done so, please read [Troubleshoot: "Azure Functions Runtime is unreachable"](https://learn.microsoft.com/en-us/azure/azure-functions/functions-recover-storage-account). 

Another common reason for this error is failure to initialize a function app that [runs in Azure from the deployment package](https://learn.microsoft.com/en-us/azure/azure-functions/run-functions-from-deployment-package), which is a common deployment scenario.  

## The following are some of the reasons for a run from package deployment to fail: 

* You created your function app using ARM or Bicep templates that sets the `WEBSITE_RUN_FROM_PACKAGE` app setting. 
* An existing Function App was configured to with the WEBSITE_RUN_FROM_PACKAGE app setting:
  * Some deployment method will add the app setting "WEBSITE_RUN_FROM_PACKAGE" and if it failed to deploy the zip package successfully, you can run into this issue. 
  * A user has added the app setting by mistake. 

For more information on using the WEBSITE_RUN_FROM_PACKAGE, see [Run your functions from a package file in Azure](https://learn.microsoft.com/en-us/azure/azure-functions/run-functions-from-deployment-package#use-website_run_from_package--1)

## Investigation Steps:

To confirm if the issue is caused by failing to initialize run from package, we can check the following:

From the Azure Portal -> Function App -> Configuration blade, confirm if the app setting "WEBSITE_RUN_FROM_PACKAGE" is set with the value of 1.

![App Settings section showing WEBSITE_RUN_FROM_PACKAGE is set to 1](https://github.com/user-attachments/assets/e9b30e39-1008-4909-a01f-fc577b8a0050)

Once confirmed, we can check the Diagnose and Solve Problems blade and we will search for the "Functions that are not triggering" detector.

![Diagnose and Solve Problems blade](https://github.com/user-attachments/assets/313d7b96-3a91-4931-b34e-b10c542a3b91)

If the detector shows the Exception Message "Shutting down host due to presence of C:\home\site\wwwroot\FAILED TO INITIALIZE RUN FROM PACKAGE.txt. File content: Run From Package Initialization failed." the issue is likely caused by the WEBSITE_RUN_FROM_PACKAGE app setting being used without a successful deployment.

![Exception Message Shutting down host due to presence of C:\home\site\wwwroot\FAILED TO INITIALIZE RUN FROM PACKAGE.txt](https://github.com/user-attachments/assets/70e13d7a-ee6e-4da4-932a-fe979f78e738)

## Resolution:

In order to get the app in a normal running state and be able to deploy code, we need to remove the app settings "WEBSITE_RUN_FROM_PACKAGE" from the Portal -> Function App -> Configuration blade and click on the trash can icon to the right of the app setting.

![App Settings Section showing the Delete button next to app setting WEBSITE_RUN_FROM_PACKAGE](https://github.com/user-attachments/assets/56b1d036-834b-41a2-ac27-f74fe90be939)

Don't forget to click the "Save" button at the top afterwards to Save make the changes.

![App Settings section top bar showing Save button location](https://github.com/user-attachments/assets/4c2c503d-1a85-4bf9-b6ca-ddb368394e0c)

Once the App Setting "WEBSITE_RUN_FROM_PACKAGE" has been deleted, the function app will restart automatically due to the App Configuration change, and the function app will start up as expected. 
