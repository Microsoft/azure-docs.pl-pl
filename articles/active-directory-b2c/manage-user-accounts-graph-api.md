---
title: Zarządzanie użytkownikami za pomocą interfejsu API Microsoft Graph
titleSuffix: Azure AD B2C
description: Jak zarządzać użytkownikami w dzierżawie Azure AD B2C przez wywołanie interfejsu API Microsoft Graph i użycie tożsamości aplikacji w celu zautomatyzowania procesu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178878"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Zarządzanie kontami użytkowników Azure AD B2C przy użyciu Microsoft Graph

Microsoft Graph umożliwia zarządzanie kontami użytkowników w katalogu Azure AD B2C przez dostarczanie metod tworzenia, odczytywania, aktualizowania i usuwania w interfejsie API Microsoft Graph. Istnieje możliwość migrowania istniejącego magazynu użytkownika do dzierżawy Azure AD B2C i wykonywania innych operacji zarządzania kontami użytkowników przez wywołanie interfejsu API Microsoft Graph.

W poniższych sekcjach przedstawiono kluczowe aspekty Azure AD B2C zarządzania użytkownikami za pomocą interfejsu API Microsoft Graph. Microsoft Graph operacje interfejsu API, typy i właściwości przedstawione w tym miejscu są podzbiorem, który pojawia się w dokumentacji dotyczącej interfejsu API Microsoft Graph.

## <a name="register-a-management-application"></a>Rejestrowanie aplikacji zarządzania

Zanim dowolna aplikacja do zarządzania użytkownikami lub skrypt, który pisze, będzie mogła korzystać z zasobów w dzierżawie Azure AD B2C, potrzebna jest rejestracja aplikacji, która przyznaje uprawnienia.

Wykonaj kroki opisane w tym artykule jak, aby utworzyć rejestrację aplikacji, która może być używana przez aplikację zarządzania:

[Zarządzanie Azure AD B2C przy użyciu Microsoft Graph](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Operacje Microsoft Graph zarządzania użytkownikami

Następujące operacje zarządzania użytkownikami są dostępne w [interfejsie API Microsoft Graph](/graph/api/resources/user):

- [Pobierz listę użytkowników](/graph/api/user-list)
- [Tworzenie użytkownika](/graph/api/user-post-users)
- [Pobierz użytkownika](/graph/api/user-get)
- [Aktualizowanie użytkownika](/graph/api/user-update)
- [Usuwanie użytkownika](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Przykład kodu: jak programowo zarządzać kontami użytkowników

Ten przykładowy kod jest aplikacją konsolową platformy .NET Core, która korzysta z [zestawu SDK Microsoft Graph](/graph/sdks/sdks-overview) , aby współdziałać z interfejsem API Microsoft Graph. W jego kodzie pokazano, jak wywołać interfejs API, aby programowo zarządzać użytkownikami w dzierżawie Azure AD B2C.
Możesz [pobrać przykładowe archiwum](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [przejrzeć repozytorium](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) w witrynie GitHub lub sklonować repozytorium:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po uzyskaniu przykładowego kodu skonfiguruj go dla środowiska, a następnie Skompiluj projekt:

1. Otwórz projekt w programie [Visual Studio](https://visualstudio.microsoft.com) lub [Visual Studio Code](https://code.visualstudio.com).
1. Otwórz plik `src/appsettings.json`.
1. W `appSettings` sekcji Zamień na `your-b2c-tenant` nazwę dzierżawy oraz `Application (client) ID` `Client secret` wartości dla rejestracji aplikacji zarządzania (zobacz sekcję [Rejestrowanie aplikacji zarządzania](#register-a-management-application) w tym artykule).
1. Otwórz okno konsoli w ramach lokalnego klonu repozytorium, przejdź do `src` katalogu, a następnie Skompiluj projekt:
    ```console
    cd src
    dotnet build
    ```
1. Uruchom aplikację za pomocą `dotnet` polecenia:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Aplikacja wyświetla listę poleceń, które można wykonać. Na przykład Pobierz wszystkich użytkowników, uzyskaj jednego użytkownika, Usuń użytkownika, zaktualizuj hasło użytkownika i Importuj zbiorczo.

### <a name="code-discussion"></a>Dyskusja o kodzie

Przykładowy kod używa [zestawu SDK Microsoft Graph](/graph/sdks/sdks-overview), który jest przeznaczony do uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które mają dostęp Microsoft Graph.

Każde żądanie do interfejsu API Microsoft Graph wymaga tokenu dostępu do uwierzytelniania. Rozwiązanie korzysta z pakietu NuGet [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) , który udostępnia otokę opartą na scenariuszu uwierzytelniania biblioteki uwierzytelniania firmy Microsoft (MSAL) do użycia z zestawem SDK Microsoft Graph.

`RunAsync`Metoda w pliku _program.cs_ :

1. Odczytuje ustawienia aplikacji z _appsettings.jsw_ pliku
1. Inicjuje dostawcę uwierzytelniania przy użyciu funkcji przepływu [przydzielenia poświadczeń klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) . Za pomocą przepływu Przydziel poświadczenia klienta aplikacja może uzyskać token dostępu do wywołania interfejsu API Microsoft Graph.
1. Konfiguruje klienta usługi Microsoft Graph przy użyciu dostawcy uwierzytelniania:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

Zainicjowany *GraphServiceClient* jest następnie używany w _UserService.cs_ do wykonywania operacji zarządzania użytkownikami. Na przykład, aby uzyskać listę kont użytkowników w dzierżawie:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Wykonywanie wywołań interfejsu API za pomocą zestawów sdk Microsoft Graph](/graph/sdks/create-requests) zawiera informacje na temat odczytywania i zapisywania informacji z Microsoft Graph, używania `$select` do kontrolowania zwracanych właściwości, dostarczania niestandardowych parametrów zapytania i używania `$filter` `$orderBy` parametrów i.

## <a name="next-steps"></a>Następne kroki

Pełny indeks Microsoft Graph operacji interfejsu API obsługiwanych przez zasoby Azure AD B2C można znaleźć w temacie [Microsoft Graph operacje dostępne dla Azure AD B2C](microsoft-graph-operations.md).

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
