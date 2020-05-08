---
title: Biblioteki zarządzania Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak za pomocą bibliotek zarządzania Azure Service Bus dynamicznie zainicjować obsługę administracyjną Service Bus przestrzenie nazw i jednostek.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e1531d9b70860f498a3e38305f26eb862c9513f3
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901484"
---
# <a name="service-bus-management-libraries"></a>Biblioteki zarządzania usługi Service Bus

Azure Service Bus biblioteki zarządzania mogą dynamicznie inicjować Service Bus przestrzenie nazw i jednostek. Umożliwia to złożone wdrożenia i scenariusze obsługi komunikatów oraz umożliwia programowe Określanie, które jednostki mają być udostępniane. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Tworzenie, aktualizowanie, usuwanie przestrzeni nazw
* Tworzenie, aktualizowanie, usuwanie kolejki
* Tworzenie, aktualizowanie, usuwanie tematu
* Tworzenie, aktualizowanie, usuwanie subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć korzystanie z bibliotek zarządzania Service Bus, należy uwierzytelnić się za pomocą usługi Azure Active Directory (Azure AD). Usługa Azure AD wymaga uwierzytelniania jako nazwy głównej usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacje na temat tworzenia nazwy głównej usługi, zobacz jeden z następujących artykułów:  

* [Użyj Azure Portal, aby utworzyć Active Directory aplikację i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Te samouczki zapewniają `AppId` (identyfikator klienta), `TenantId`i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelniania przez biblioteki zarządzania. Musisz mieć co najmniej Azure Service Bus uprawnienia [**właściciela danych**](/azure/role-based-access-control/built-in-roles#azure-service-bus-data-owner) lub [**współautora**](/azure/role-based-access-control/built-in-roles#contributor) dla grupy zasobów, w której chcesz uruchomić.

## <a name="programming-pattern"></a>Wzorzec programowania

Wzorzec służący do manipulowania dowolnym zasobem Service Bus jest następujący:

1. Uzyskaj token z usługi Azure AD za pomocą biblioteki **Microsoft. IdentityModel. clients. ActiveDirectory** :
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Utwórz `ServiceBusManagementClient` obiekt:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Ustaw `CreateOrUpdate` parametry na określone wartości:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. Wykonaj wywołanie:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="complete-code-to-create-a-queue"></a>Ukończ kod, aby utworzyć kolejkę
Oto kompletny kod służący do tworzenia kolejki Service Bus: 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> Aby uzyskać pełny przykład, zobacz [przykład zarządzania .NET w witrynie GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

## <a name="next-steps"></a>Następne kroki
[Dokumentacja interfejsu API Microsoft. Azure. Management. ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
