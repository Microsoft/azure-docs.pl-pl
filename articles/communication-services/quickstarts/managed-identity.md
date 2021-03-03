---
title: Korzystanie z tożsamości zarządzanych w usługach komunikacyjnych (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Zarządzane tożsamości umożliwiają autoryzowanie dostępu do usług Azure Communications Services z aplikacji uruchamianych na maszynach wirtualnych platformy Azure, aplikacjach funkcji i innych zasobach.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 7e8d9b56077819fc404d6c2bdc39f9f697224136
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692185"
---
# <a name="use-managed-identities-net"></a>Korzystanie z tożsamości zarządzanych (.NET)

Rozpocznij pracę z usługami Azure Communications Services przy użyciu tożsamości zarządzanych w programie .NET. Biblioteki administracyjne usług komunikacyjnych i klienta programu SMS obsługują uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym przewodniku szybki start pokazano, jak autoryzować dostęp do bibliotek klienckich administracji i programu SMS ze środowiska platformy Azure, które obsługuje zarządzane tożsamości. Opisano w nim również sposób testowania kodu w środowisku programistycznym.

## <a name="prerequisites"></a>Wymagania wstępne

 - Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free)
 - Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Konfigurowanie

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej lub w usłudze App Service

Zarządzane tożsamości powinny być włączone dla zasobów platformy Azure, które są autoryzowane. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Przypisywanie ról platformy Azure za pomocą Azure Portal

1. Przejdź do witryny Azure Portal.
1. Przejdź do zasobu usługi komunikacyjnej platformy Azure.
1. Przejdź do menu Access Control (IAM) — > i Dodaj > Dodaj przypisanie roli.
1. Wybierz rolę "Współautor" (jest to jedyna obsługiwana rola).
1. Wybierz pozycję "tożsamość zarządzana przypisana przez użytkownika" (lub "tożsamość zarządzana przez system"), a następnie wybierz żądaną tożsamość. Zapisz swój wybór.

![Rola tożsamości zarządzanej](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Przypisywanie ról platformy Azure za pomocą programu PowerShell

Aby przypisać role i uprawnienia przy użyciu programu PowerShell, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Dodaj zarządzaną tożsamość do rozwiązania usług komunikacyjnych

### <a name="install-the-client-library-packages"></a>Instalowanie pakietów biblioteki klienta

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Korzystanie z pakietów biblioteki klienta

Dodaj następujące `using` dyrektywy do kodu, aby użyć tożsamości platformy Azure i bibliotek klienckich usługi Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Poniższe przykłady używają [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). To poświadczenie jest odpowiednie dla środowisk produkcyjnych i programistycznych.

### <a name="create-an-identity-and-issue-a-token"></a>Tworzenie tożsamości i wystawianie tokenu

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tokenami Azure Active Directory, a następnie za pomocą klienta wydać token dla nowego użytkownika:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Wyślij wiadomość SMS z tokenami Azure Active Directory

Poniższy przykład kodu pokazuje, jak utworzyć obiekt klienta usługi z tokenami Azure Active Directory, a następnie wysłać wiadomość SMS przy użyciu klienta:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o uwierzytelnianiu](../concepts/authentication.md)

Możesz również chcieć:

- [Dowiedz się więcej o kontroli dostępu opartej na rolach na platformie Azure](../../../articles/role-based-access-control/index.yml)
- [Dowiedz się więcej o bibliotece tożsamości platformy Azure dla platformy .NET](/dotnet/api/overview/azure/identity-readme)
- [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)
- [Wysyłanie wiadomości SMS](../quickstarts/telephony-sms/send.md)
- [Dowiedz się więcej o programie SMS](../concepts/telephony-sms/concepts.md)