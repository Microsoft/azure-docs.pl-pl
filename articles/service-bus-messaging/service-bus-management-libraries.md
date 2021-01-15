---
title: Programowe tworzenie jednostek Azure Service Bus | Microsoft Docs
description: W tym artykule wyjaśniono, jak używać dynamicznie lub programowo aprowizacji Service Bus przestrzenie nazw i jednostek.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 97d89db17af9cde3afadee430b3d0c2a434e12c9
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210141"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Dynamicznie zastrzegaj Service Bus przestrzenie nazw i jednostek 
Azure Service Bus biblioteki zarządzania mogą dynamicznie inicjować Service Bus przestrzenie nazw i jednostek. Umożliwia to złożone wdrożenia i scenariusze obsługi komunikatów oraz umożliwia programowe Określanie, które jednostki mają być udostępniane. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Tworzenie, aktualizowanie, usuwanie przestrzeni nazw
* Tworzenie, aktualizowanie, usuwanie kolejki
* Tworzenie, aktualizowanie, usuwanie tematu
* Tworzenie, aktualizowanie, usuwanie subskrypcji

## <a name="azuremessagingservicebusadministration-recommended"></a>Azure. Messaging. ServiceBus. Administration (zalecane)
Możesz użyć klasy [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) w przestrzeni nazw [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration) , aby zarządzać przestrzeniami nazw, kolejkami, tematami i subskrypcjami. Oto przykładowy kod. Aby zapoznać się z kompletnym przykładem, zobacz [przykład CRUD](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Microsoft. Azure. ServiceBus. Management 
Można użyć klasy [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) w przestrzeni nazw [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management) do zarządzania przestrzeniami nazw, kolejkami, tematami i subskrypcjami. Oto przykładowy kod: 

> [!NOTE]
> Zalecamy używanie `ServiceBusAdministrationClient` klasy z `Azure.Messaging.ServiceBus.Administration` biblioteki, która jest NAJNOWSZYm zestawem SDK. Aby uzyskać szczegółowe informacje, zobacz [pierwszą sekcję](#azuremessagingservicebusadministration-recommended). 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Ta biblioteka jest częścią zestawu SDK płaszczyzny kontroli opartej na Azure Resource Manager. 

### <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć korzystanie z tej biblioteki, musisz się uwierzytelnić za pomocą usługi Azure Active Directory (Azure AD). Usługa Azure AD wymaga uwierzytelniania jako nazwy głównej usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacje na temat tworzenia nazwy głównej usługi, zobacz jeden z następujących artykułów:  

* [Użyj Azure Portal, aby utworzyć Active Directory aplikację i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

Te samouczki zapewniają `AppId` (identyfikator klienta), `TenantId` i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelniania przez biblioteki zarządzania. Musisz mieć co najmniej Azure Service Bus uprawnienia [**właściciela danych**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) lub [**współautora**](../role-based-access-control/built-in-roles.md#contributor) dla grupy zasobów, w której chcesz uruchomić.

### <a name="programming-pattern"></a>Wzorzec programowania

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

### <a name="complete-code-to-create-a-queue"></a>Ukończ kod, aby utworzyć kolejkę
Oto przykładowy kod służący do tworzenia kolejki Service Bus. Aby uzyskać pełny przykład, zobacz [przykład zarządzania .NET w witrynie GitHub](https://github.com/Azure-Samples/service-bus-dotnet-management/). 

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

## <a name="fluent-library"></a>Biblioteka Fluent
Przykład korzystania z biblioteki Fluent do zarządzania jednostkami Service Bus można znaleźć w [tym przykładzie](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus). 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące tematy dotyczące odwołań: 

- [Azure. Messaging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Microsoft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)