---
title: Zmiany wprowadzone do projektów WebAPI podczas nawiązywania połączenia z usługą Azure AD
description: Opisuje, co się dzieje z projektem WebAPI podczas nawiązywania połączenia z usługą Azure AD przy użyciu programu Visual Studio
author: ghogen
manager: jillfra
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: a070bee7a9fa836eeac7c739cf2757295533ad7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165366"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem WebAPI (usługa połączona Azure Active Directory Visual Studio)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

W tym artykule opisano dokładne zmiany wprowadzone do ASP.NET WebAPI, ASP.NET aplikacji Single-Page i ASP.NET projektów interfejsu API platformy Azure podczas dodawania [usługi połączonej Azure Active Directory przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md). Dotyczy również projektów usługi mobilnej platformy Azure ASP.NET w programie Visual Studio 2015.

Aby uzyskać informacje na temat pracy z połączoną usługą, zobacz [wprowadzenie](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Dodano odwołania

Dotyczy pliku projektu *. NET References i `packages.config` (odwołania NuGet).

| Typ | Odwołanie |
| --- | --- |
| Waga NuGet | Microsoft. Owin |
| Waga NuGet | Microsoft.Owin.Host.SystemWeb |
| Waga NuGet | Microsoft. Owin. Security |
| Waga NuGet | Microsoft. Owin. Security. ActiveDirectory |
| Waga NuGet | Microsoft. Owin. Security. JWT |
| Waga NuGet | Microsoft. Owin. Security. OAuth |
| Waga NuGet | Owin |
| Waga NuGet | System. IdentityModel. Tokens. JWT |

Dodatkowe odwołania w przypadku wybrania opcji **Odczytaj dane katalogu** :

| Typ | Odwołanie |
| --- | --- |
| Waga NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (tylko w programie Visual Studio 2015) |
| Waga NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| Waga NuGet | Microsoft. Data. EDM |
| Waga NuGet | Microsoft. Data. OData |
| Waga NuGet | Microsoft. Data. Services. Client |
| Waga NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms<br>(Tylko w programie Visual Studio 2015) |
| Waga NuGet | System. przestrzenny |

Następujące odwołania są usuwane (tylko projekty ASP.NET 4, jak w programie Visual Studio 2015):

| Typ | Odwołanie |
| --- | --- |
| Waga NuGet | Microsoft. AspNet. Identity. Core |
| Waga NuGet | Microsoft. AspNet. Identity. EntityFramework |
| Waga NuGet | Microsoft. AspNet. Identity. Owin |

## <a name="project-file-changes"></a>Zmiany w pliku projektu

- Ustaw właściwość `IISExpressSSLPort` na wartość DISTINCT.
- Ustaw właściwość `WebProject_DirectoryAccessLevelKey` na 0 lub 1, jeśli wybrano opcję **Odczytaj dane katalogu** .
- Ustaw właściwość `IISUrl` na `https://localhost:<port>/` gdzie pasuje do `<port>` `IISExpressSSLPort` wartości.

## <a name="webconfig-or-appconfig-changes"></a>Zmiany web.config lub app.config

- Dodano następujące wpisy konfiguracji:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Tylko program Visual Studio 2017: dodano również następujący wpis w sekcji `<appSettings>` "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Dodano `<dependentAssembly>` elementy w `<runtime><assemblyBinding>` węźle dla `System.IdentityModel.Tokens.Jwt` .

- W przypadku wybrania opcji **Odczytaj dane katalogu** dodano następujący wpis konfiguracyjny poniżej `<appSettings>` :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Zmiany i dodatki kodu

- Dodano `[Authorize]` atrybut do `Controllers/ValueController.cs` i wszystkich innych istniejących kontrolerów.

- Dodano klasę uruchamiania uwierzytelniania, `App_Start/Startup.Auth.cs` zawierającą logikę uruchamiania dla uwierzytelniania usługi Azure AD lub odpowiednio ją zmodyfikowano. W przypadku wybrania opcji **Czytaj dane katalogu** ten plik zawiera również kod umożliwiający otrzymanie kodu OAuth i wymienianie go z tokenem dostępu.

- (Tylko program Visual Studio 2015 z aplikacją ASP.NET 4) Usunięty `App_Start/IdentityConfig.cs` i dodany `Controllers/AccountController.cs` , `Models/IdentityModel.cs` , i `Providers/ApplicationAuthProvider.cs` .

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) lub `Service References/Azure AD/ConnectedService.json` (visual Studio 2015) zawierający informacje używane przez program Visual Studio do śledzenia dodawania połączonej usługi.

### <a name="file-backup-visual-studio-2015"></a>Kopia zapasowa plików (Visual Studio 2015)

Podczas dodawania połączonej usługi program Visual Studio 2015 tworzy kopie zapasowe zmienionych i usuniętych plików. Wszystkie odnośne pliki są zapisywane w folderze `Backup/AzureAD` . Program Visual Studio 2017 nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono aplikację usługi Azure AD w domenie wybranej podczas dodawania połączonej usługi.
- Aplikacja została zaktualizowana w celu uwzględnienia uprawnienia **Odczyt danych katalogu** , jeśli ta opcja została wybrana.

[Dowiedz się więcej o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](./authentication-vs-authorization.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)