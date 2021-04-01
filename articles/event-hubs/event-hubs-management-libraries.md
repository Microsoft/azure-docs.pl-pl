---
title: Biblioteki zarządzania — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje na temat biblioteki, która służy do zarządzania przestrzeniami nazw i jednostkami usługi Azure Event Hubs z poziomu platformy .NET.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 74392fbf0b2c0b81898410af8027a4f13fc52b67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89014000"
---
# <a name="event-hubs-management-libraries"></a>Biblioteki zarządzania usługi Event Hubs

Za pomocą bibliotek zarządzania usługi Azure Event Hubs można dynamicznie zainicjować obsługę administracyjną Event Hubs przestrzeni nazw i jednostek. Ten dynamiczny charakter umożliwia złożone wdrożenia i scenariusze obsługi komunikatów, dzięki czemu można programowo określić, jakie jednostki należy udostępnić. Te biblioteki są obecnie dostępne dla platformy .NET.

## <a name="supported-functionality"></a>Obsługiwane funkcje

* Tworzenie, aktualizowanie, usuwanie przestrzeni nazw
* Tworzenie, aktualizowanie, usuwanie Event Hubs
* Tworzenie, aktualizowanie, usuwanie grupy odbiorców

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć korzystanie z bibliotek zarządzania Event Hubs, należy uwierzytelnić się za pomocą usługi Azure Active Directory (AAD). Usługa AAD wymaga uwierzytelniania jako nazwy głównej usługi, która zapewnia dostęp do zasobów platformy Azure. Aby uzyskać informacje na temat tworzenia nazwy głównej usługi, zobacz jeden z następujących artykułów:  

* [Użyj Azure Portal, aby utworzyć Active Directory aplikację i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md)
* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](/cli/azure/create-an-azure-service-principal-azure-cli)

Te samouczki zapewniają `AppId` (identyfikator klienta), `TenantId` i `ClientSecret` (klucz uwierzytelniania), które są używane do uwierzytelniania przez biblioteki zarządzania. Użytkownik musi mieć uprawnienia **właściciela** do grupy zasobów, w której ma zostać uruchomione.

## <a name="programming-pattern"></a>Wzorzec programowania

Wzorzec służący do manipulowania dowolnym zasobem Event Hubs jest następujący:

1. Uzyskaj token z usługi AAD przy użyciu `Microsoft.IdentityModel.Clients.ActiveDirectory` biblioteki.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Utwórz `EventHubManagementClient` obiekt.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Ustaw `CreateOrUpdate` Parametry na określone wartości.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Wykonaj wywołanie.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Następne kroki
* [Przykład zarządzania .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Dokumentacja Microsoft. Azure. Management. EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 
