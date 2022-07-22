---
title: Azure Policy Regulatory Compliance controls for Azure App Service
description: Lists Azure Policy Regulatory Compliance controls available for Azure App Service. These built-in policy definitions provide common approaches to managing the compliance of your Azure resources.
ms.date: 07/20/2022
ms.topic: sample
ms.service: app-service
ms.custom: subject-policy-compliancecontrols
---
# Azure Policy Regulatory Compliance controls for Azure App Service

[Regulatory Compliance in Azure Policy](../governance/policy/concepts/regulatory-compliance.md)
provides Microsoft created and managed initiative definitions, known as _built-ins_, for the
**compliance domains** and **security controls** related to different compliance standards. This
page lists the **compliance domains** and **security controls** for Azure App Service. You can
assign the built-ins for a **security control** individually to help make your Azure resources
compliant with the specific standard.

[!INCLUDE [Azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [Azure-policy-compliancecontrols-appservice](../../includes/policy/standards/byrp/microsoft.web.md)]

## Release notes

### July 2022

- Deprecation of the following policies:
  - **Ensure API app has 'Client Certificates (Incoming client certificates)' set to 'On'**
  - **Ensure that 'Python version' is the latest, if used as a part of the API app**
  - **CORS should not allow every resource to access your API App**
  - **Managed identity should be used in your API App**
  - **Remote debugging should be turned off for API Apps**
  - **Ensure that 'PHP version' is the latest, if used as a part of the API app**
  - **API apps should use an Azure file share for its content directory**
  - **FTPS only should be required in your API App**
  - **Ensure that 'Java version' is the latest, if used as a part of the API app**
  - **Ensure that 'HTTP Version' is the latest, if used to run the API app**
  - **Latest TLS version should be used in your API App**
  - **Authentication should be enabled on your API app**
- **Function apps should have 'Client Certificates (Incoming client certificates)' enabled**
  - Update scope of policy to include slots
  - Update scope of policy to exclude Logic apps
- **Ensure WEB app has 'Client Certificates (Incoming client certificates)' set to 'On'**
  - Rename of policy to "App Service apps should have 'Client Certificates (Incoming client certificates)' enabled"
  - Update scope of policy to include slots
  - Update scope of policy to include all app types except Function apps
- **Ensure that 'Python version' is the latest, if used as a part of the Web app**
  - Rename of policy to "App Service apps that use Python should use the latest 'Python version'"
  - Update scope of policy to include all app types except Function apps
- **Ensure that 'Python version' is the latest, if used as a part of the Function app**
  - Rename of policy to "Function apps that use Python should use the latest 'Python version'"
  - Update scope of policy to exclude Logic apps
- **CORS should not allow every resource to access your Web Applications**
  - Rename of policy to "App Service apps should not have CORS configured to allow every resource to access your apps"
  - Update scope of policy to include all app types except Function apps
- **CORS should not allow every resource to access your Function Apps**
  - Rename of policy to "Function apps should not have CORS configured to allow every resource to access your apps"
  - Update scope of policy to exclude Logic apps
- **Managed identity should be used in your Function App**
  - Rename of policy to "Function apps should use managed identity"
  - Update scope of policy to exclude Logic apps
- **Managed identity should be used in your Web App**
  - Rename of policy to "App Service apps should use managed identity"
  - Update scope of policy to include all app types except Function apps
- **Remote debugging should be turned off for Function Apps**
  - Rename of policy to "Function apps should have remote debugging turned off"
  - Update scope of policy to exclude Logic apps
- **Remote debugging should be turned off for Web Applications**
  - Rename of policy to "App Service apps should have remote debugging turned off"
  - Update scope of policy to include all app types except Function apps
- **Ensure that 'PHP version' is the latest, if used as a part of the WEB app**
  - Rename of policy to "App Service apps that use PHP should use the latest 'PHP version'"
  - Update scope of policy to include all app types except Function apps
- **App Service slots should have local authentication methods disabled for SCM site deployment**
  - Rename of policy to "App Service app slots should have local authentication methods disabled for SCM site deployments"
- **App Service should have local authentication methods disabled for SCM site deployments**
  - Rename of policy to "App Service apps should have local authentication methods disabled for SCM site deployments"
- **App Service slots should have local authentication methods disabled for FTP deployments**
  - Rename of policy to "App Service app slots should have local authentication methods disabled for FTP deployments"
- **App Service should have local authentication methods disabled for FTP deployments**
  - Rename of policy to "App Service apps should have local authentication methods disabled for FTP deployments"
- **Function apps should use an Azure file share for its content directory**
  - Update scope of policy to include slots
  - Update scope of policy to exclude Logic apps
- **Web apps should use an Azure file share for its content directory**
  - Rename of policy to "App Service apps should use an Azure file share for its content directory"
  - Update scope of policy to include slots
  - Update scope of policy to include all app types except Function apps
- **FTPS only should be required in your Function App**
  - Rename of policy to "Function apps should require FTPS only"
  - Update scope of policy to exclude Logic apps
- **FTPS should be required in your Web App**
  - Rename of policy to "App Service apps should require FTPS only"
  - Update scope of policy to include all app types except Function apps
- **Ensure that 'Java version' is the latest, if used as a part of the Function app**
  - Rename of policy to "Function apps that use Java should use the latest 'Java version'"
  - Update scope of policy to exclude Logic apps
- **Ensure that 'Java version' is the latest, if used as a part of the Web app**
  - Rename of policy to "App Service apps that use Java should use the latest 'Java version"
  - Update scope of policy to include all app types except Function apps
- **App Service should use private link**
  - Rename of policy to "App Service apps should use private link"
- **Configure App Services to use private DNS zones**
  - Rename of policy to "Configure App Service apps to use private DNS zones"
- **App Service Apps should be injected into a virtual network**
  - Rename of policy to "App Service apps should be injected into a virtual network"
  - Update scope of policy to include slots
- **Ensure that 'HTTP Version' is the latest, if used to run the Web app**
  - Rename of policy to "App Service apps should use latest 'HTTP Version'"
  - Update scope of policy to include all app types except Function apps
- **Ensure that 'HTTP Version' is the latest, if used to run the Function app**
  - Rename of policy to "Function apps should use latest 'HTTP Version'"
  - Update scope of policy to exclude Logic apps
- **Latest TLS version should be used in your Web App**
  - Rename of policy to "App Service apps should use the latest TLS version"
  - Update scope of policy to include all app types except Function apps
- **Latest TLS version should be used in your Function App**
  - Rename of policy to "Function apps should use the latest TLS version"
  - Update scope of policy to exclude Logic apps
- **App Service Environment should disable TLS 1.0 and 1.1**
  - Rename of policy to "App Service Environment should have TLS 1.0 and 1.1 disabled"
- **Resource logs in App Services should be enabled**
  - Rename of policy to "App Service apps should have resource logs enabled"
- **Authentication should be enabled on your web app**
  - Rename of policy to "App Service apps should have authentication enabled"
- **Authentication should be enabled on your Function app**
  - Rename of policy to "Function apps should have authentication enabled"
  - Update scope of policy to exclude Logic apps
- **App Service Environment should enable internal encryption**
  - Rename of policy to "App Service Environment should have internal encryption enabled"
- **Function apps should only be accessible over HTTPS**
  - Update scope of policy to exclude Logic apps
- **App Service should use a virtual network service endpoint**
  - Rename of policy to "App Service apps should use a virtual network service endpoint"
  - Update scope of policy to include all app types except Function apps

### June 2022

- Deprecation of policy **API App should only be accessible over HTTPS**
- **Web Application should only be accessible over HTTPS**
  - Rename of policy to "App Service apps should only be accessible over HTTPS"
  - Update scope of policy to include all app types except Function apps
  - Update scope of policy to include slots
- **Function apps should only be accessible over HTTPS**
  - Update scope of policy to include slots
- **App Service apps should use a SKU that supports private link**
  - Update logic of policy to include checks on App Service plan tier or name so that the policy supports Terraform deployments
  - Update list of supported SKUs of policy to include the Basic and Standard tiers

## Next steps

- Learn more about [Azure Policy Regulatory Compliance](../governance/policy/concepts/regulatory-compliance.md).
- See the built-ins on the [Azure Policy GitHub repo](https://github.com/Azure/azure-policy).
