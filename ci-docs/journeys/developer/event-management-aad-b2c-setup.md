---
title: Set up event management to work with Microsoft Entra ID
description: Provides information about how you can set up event management web application to work with Microsoft Entra ID.
ms.date: 02/21/2019
ms.topic: how-to
author: alfergus
ms.author: alfergus
search.audienceType: 
  - developer
---

# Setting up event management to work with Microsoft Entra ID

[!INCLUDE [consolidated-sku-rtm-only](.././includes/consolidated-sku-rtm-only.md)]

[!INCLUDE [azure-ad-to-microsoft-entra-id](../includes/azure-ad-to-microsoft-entra-id.md)]

## Overview

The event portal is capable of integrating the **Microsoft Entra ID**. To integrate it, you need to implement a couple of steps. 

1. If you don't have one already, create a Microsoft Entra ID tenant.
1. Add a web application to your Microsoft Entra ID tenant.
1. Register the application with your Dynamics 365 Customer Insights - Journeys instance.
1. Configure event management application to work with your Microsoft Entra ID tenant

## Creating a Microsoft Entra ID tenant and adding a web application to the tenant

The quick start tutorial explains how to create a new Microsoft Entra ID tenant here: [Create a Microsoft Entra ID tenant](/azure/active-directory-b2c/tutorial-create-tenant)

After you have successfully created a **Microsoft Entra ID** tenant, follow the tutorial to add a web application to the newly created tenant:
[Register a web application with Microsoft Entra ID](/azure/active-directory-b2c/tutorial-register-applications)

### Grant API access to the registered web application

1. Select the web application that you have registered before.
2. Specify `events` as **App ID URI**. You can apply this setting in the **Properties** tab.
3. Navigate to API access.
4. Select **Add**.
5. Select your API and select all scopes.
6. Select **Ok**.

### Create a user flow policy

You need to create a user flow policy if you don't already have one.

To do so, follow the steps below:

1. Navigate to your Azure portal and open your **Microsoft Entra ID**
2. Select **User flows (policies)** in the **Policies** section.
3. Select **new user flow**.
4. Select **Sign up** and **Sign in** user flow type.
5. Specify a name. 
6. Select an identity provider.
7. Select at least the following user attributes and claims:
  * Email Address (required, **collect attribute**)
  * Given Name (**collect attribute** and **return claim**)
  * Surname (**collect attribute** and **return claim**)
8. Select **Create**.

> [!NOTE]
> To use a name-based contact matching strategy, you must configure the sign-up policy to include the `Given Name` and `Surname` attributes and also selecting them in the `Application claim` section. More information: [How to configure and set it up in Microsoft Entra ID](/azure/active-directory-b2c/)

## Naming

To reduce the clutter while navigating Azure portal we will use the following naming for the portal screens:

* B2C Tenant: **Tenant**
* B2C Tenant > Microsoft Entra ID Settings: **Settings**
* B2C Tenant > Microsoft Entra ID Settings > Application > Select your application: **Application**
* Application > Application ID textbox: **Application ID**
* Tenant > Properties > Name textbox: **Tenant name** (for example, `contoso.onmicrosoft.com`)
* First part of the tenant name: **Tenant ID** (for example, `contoso`)
* Settings > User Flows (policies): **Policy** (for example, `B2C_1_default-sign-up`)
* Application > Published scopes > Full scope value textbox: **Scope** (for example, `https://contosoeventmanagementtest.onmicrosoft.com/events/registration`)

## Registering the application with Customer Insights - Journeys instance

Follow those steps to register the application with Customer Insights - Journeys.

1. Open your Customer Insights - Journeys instance
1. Navigate to **Dynamics 365 > Customer Insights - Journeys > Settings > Web applications** and select your **Web application** record. If you haven't created a **Web application** record yet, follow the steps to [register your web application](/dynamics365/customer-engagement/marketing/developer/self-hosted#register-your-web-application).
1. Insert your **Application ID** in the **Microsoft Entra ID Client ID** field.
1. Insert your metadata endpoint in the **Microsoft Entra ID Metadata Endpoint** field.
  According to the defined naming assembly, the Microsoft Entra ID metadata endpoint looks in this pattern `https://{tenant id}.b2clogin.com/{tenant id}.onmicrosoft.com/v2.0/.well-known/openid-configuration?p={policy}`
  The Microsoft Entra ID metadata endpoint looks in this pattern `https://contosoeventmanagementtest.b2clogin.com/contosoeventmanagementtest.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_default-sign-up`

1. Save the changes.


    > [!NOTE]
    > It can take up to 10 minutes until the changes become active.

## Configuring event management application

Open the `environment.ts` configuration file located in the **\src\environments** folder and enter all required values in the `aadB2cConfig` variable.

Use following mapping while entering the values:

* authorityHost: {tenant ID}.b2clogin.com (for example, `contosoeventmanagementtest.b2clogin.com`)
* tenant: Tenant name (for example, `contosoeventmanagementtest.onmicrosoft.com`)
* clientID: Application ID 
* signUpSignInPolicy: Policy (for example, `B2C_1_default-sign-up`)
* b2cScopes: ['Scope'] (for example, `https://contosoeventmanagementtest.onmicrosoft.com/events/registration`)
* redirectUri: Application > Reply URL > Pick your application uri (for example, for localhost `https://localhost:4200`)

An example of the full aadB2cConfig object would be:

```JS
aadB2CConfig: {
    authorityHost: 'contoso.b2clogin.com',
    tenant: 'contoso.onmicrosoft.com',
    clientID: '00001111-aaaa-2222-bbbb-3333cccc4444',
    signUpSignInPolicy: 'B2C_1_default-sign-up',
    b2cScopes: ['https://contoso.onmicrosoft.com/events/registration'],
    redirectUri: 'https://localhost:4200'
}
```


[!INCLUDE [footer-include](.././includes/footer-banner.md)]
