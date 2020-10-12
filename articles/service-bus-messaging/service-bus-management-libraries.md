---
title: Biblioteki zarządzania Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak za pomocą bibliotek zarządzania Azure Service Bus dynamicznie zainicjować obsługę administracyjną Service Bus przestrzenie nazw i jednostek.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 915606bffc2037c8fcd1a7d33218143f40c78f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008050"
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

* [Użyj Azure Portal, aby utworzyć Active Directory aplikację i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

Te samouczki zapewniają `AppId` (identyfikator klienta), `TenantId` i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelniania przez biblioteki zarządzania. Musisz mieć co najmniej Azure Service Bus uprawnienia [**właściciela danych**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) lub [**współautora**](../role-based-access-control/built-in-roles.md#contributor) dla grupy zasobów, w której chcesz uruchomić.

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
3. Ustaw `CreateOrUpdate` Parametry na określone wartości:

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