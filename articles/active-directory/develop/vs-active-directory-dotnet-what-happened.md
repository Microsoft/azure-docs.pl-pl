---
title: Zmiany wprowadzone do projektu MVC po nawiązaniu połączenia z usługą Azure AD
description: Opisuje, co się dzieje z projektem MVC po nawiązaniu połączenia z usługą Azure AD przy użyciu usług połączonych programu Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: ab72512b2e5d915dbd4b8a3d7d8446a5932eef9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165451"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co się stało z moim projektem MVC (usługa połączona Azure Active Directory Visual Studio)?

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

W tym artykule zidentyfikowano dokładne zmiany w projekcie ASP.NET MVC podczas dodawania [połączonej usługi Azure Active Directory przy użyciu programu Visual Studio](vs-active-directory-add-connected-service.md).

Aby uzyskać informacje na temat pracy z połączoną usługą, zobacz [wprowadzenie](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Dodano odwołania

Ma wpływ na plik projektu (*. NET References) i `packages.config` (odwołania NuGet).

| Type | Tematy pomocy |
| --- | --- |
| Waga NuGet | Microsoft. IdentityModel. Protocol. Extensions |
| Waga NuGet | Microsoft. Owin |
| Waga NuGet | Microsoft.Owin.Host.SystemWeb |
| Waga NuGet | Microsoft. Owin. Security |
| Waga NuGet | Microsoft.Owin.Security.Cookies |
| Waga NuGet | Microsoft.Owin.Security.OpenIdConnect |
| Waga NuGet | Owin |
| .NET        | System. IdentityModel |
| Waga NuGet | System. IdentityModel. Tokens. JWT |
| .NET        | System.Runtime.Serialization |

Dodatkowe odwołania w przypadku wybrania opcji **Odczytaj dane katalogu** :

| Type | Tematy pomocy |
| --- | --- |
| Waga NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (tylko w programie Visual Studio 2015) |
| Waga NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| Waga NuGet | Microsoft. Data. EDM |
| Waga NuGet | Microsoft. Data. OData |
| Waga NuGet | Microsoft. Data. Services. Client |
| Waga NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (tylko Visual Studio 2015) |
| Waga NuGet | System. przestrzenny |

Następujące odwołania są usuwane (tylko projekty ASP.NET 4, jak w programie Visual Studio 2015):

| Type | Tematy pomocy |
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
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Dodano `<dependentAssembly>` elementy w `<runtime><assemblyBinding>` węźle dla `System.IdentityModel.Tokens.Jwt` i `Microsoft.IdentityModel.Protocol.Extensions` .

Dodatkowe zmiany w przypadku wybrania opcji **Odczytaj dane katalogu** :

- Dodano następujący wpis konfiguracji poniżej `<appSettings>` :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Dodano następujące elementy w obszarze `<configuration>` ; wartości dla pliku Project-MDF-File i Project-Catalog-ID będą się różnić:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Dodano `<dependentAssembly>` elementy w `<runtime><assemblyBinding>` węźle dla `Microsoft.Data.Services.Client` , `Microsoft.Data.Edm` , i `Microsoft.Data.OData` .

## <a name="code-changes-and-additions"></a>Zmiany i dodatki kodu

- Dodano `[Authorize]` atrybut do `Controllers/HomeController.cs` i wszystkich innych istniejących kontrolerów.

- Dodano klasę uruchamiania uwierzytelniania, `App_Start/Startup.Auth.cs` zawierającą logikę uruchamiania uwierzytelniania usługi Azure AD. W przypadku wybrania opcji **Czytaj dane katalogu** ten plik zawiera również kod umożliwiający otrzymanie kodu OAuth i wymienianie go z tokenem dostępu.

- Dodano klasę kontrolera, `Controllers/AccountController.cs` zawierającą `SignIn` i `SignOut` metody.

- Dodano widok częściowy, `Views/Shared/_LoginPartial.cshtml` zawierający link akcji dla `SignIn` i `SignOut` .

- Dodano widok częściowy, `Views/Account/SignoutCallback.cshtml` zawierający kod HTML dla interfejsu użytkownika wylogowania.

- Zaktualizowano `Startup.Configuration` metodę w celu uwzględnienia wywołania, `ConfigureAuth(app)` Jeśli Klasa już istnieje; w przeciwnym razie dodaliśmy `Startup` klasę, która zawiera wywołania metody.

- Dodano `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) lub `Service References/Azure AD/ConnectedService.json` (visual Studio 2015) zawierający informacje używane przez program Visual Studio do śledzenia dodawania połączonej usługi.

- Jeśli wybrano opcję **Odczytaj dane katalogu** , dodano `Models/ADALTokenCache.cs` i `Models/ApplicationDbContext.cs` do obsługi buforowania tokenów. Dodano również dodatkowy kontroler i widok, aby zilustrować dostęp do informacji o profilu użytkownika przy użyciu interfejsów API usługi Azure Graph: `Controllers/UserProfileController.cs` , `Views/UserProfile/Index.cshtml` i `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Kopia zapasowa plików (Visual Studio 2015)

Podczas dodawania połączonej usługi program Visual Studio 2015 tworzy kopie zapasowe zmienionych i usuniętych plików. Wszystkie odnośne pliki są zapisywane w folderze `Backup/AzureAD` . Program Visual Studio 2017 i nowsze nie tworzy kopii zapasowych.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Zmiany na platformie Azure

- Utworzono aplikację usługi Azure AD w domenie wybranej podczas dodawania połączonej usługi.
- Aplikacja została zaktualizowana w celu uwzględnienia uprawnienia **Odczyt danych katalogu** , jeśli ta opcja została wybrana.

[Dowiedz się więcej o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](./authentication-vs-authorization.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)