---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "70803010"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Uzyskaj token Azure Resource Manager
Azure Active Directory musi uwierzytelnić wszystkie zadania wykonywane na zasobach przy użyciu Azure Resource Manager. W poniższym przykładzie jest stosowane uwierzytelnianie hasła dla innych metod zobacz [uwierzytelnianie Azure Resource Manager żądania][lnk-authenticate-arm].

1. Dodaj następujący kod do metody **Main** w programie program. cs, aby pobrać token z usługi Azure AD przy użyciu identyfikatora aplikacji i hasła.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Utwórz obiekt **ResourceManagementClient** , który używa tokenu, dodając następujący kod na końcu metody **Main** :
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Utwórz lub uzyskaj odwołanie do grupy zasobów, której używasz:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx