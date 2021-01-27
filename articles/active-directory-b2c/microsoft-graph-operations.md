---
title: Zarządzanie zasobami za pomocą Microsoft Graph
titleSuffix: Azure AD B2C
description: Jak zarządzać zasobami w dzierżawie Azure AD B2C przez wywołanie interfejsu API Microsoft Graph i użycie tożsamości aplikacji w celu zautomatyzowania procesu.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 96772020e70aeb32fa1a8ae18bf3818396887877
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805238"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Zarządzanie Azure AD B2C przy użyciu Microsoft Graph

Microsoft Graph umożliwia zarządzanie zasobami w katalogu Azure AD B2C. Następujące Microsoft Graph operacje interfejsu API są obsługiwane na potrzeby zarządzania zasobami Azure AD B2C, w tym użytkownikami, dostawcami tożsamości, przepływami użytkowników, zasadami niestandardowymi i kluczami zasad. Każdy link w poniższych sekcjach odwołuje się do odpowiedniej strony w ramach odwołania do interfejsu API Microsoft Graph dla tej operacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z programu MS interfejs API programu Graph i korzystać z zasobów w dzierżawie Azure AD B2C, potrzebna jest rejestracja aplikacji, która przyznaje uprawnienia. Wykonaj kroki opisane w artykule [zarządzanie Azure AD B2C w programie Microsoft Graph](microsoft-graph-get-started.md) , aby utworzyć rejestrację aplikacji, która może być używana przez aplikację zarządzania. 

## <a name="user-management"></a>Zarządzanie użytkownikami

- [Wyświetlanie użytkowników](/graph/api/user-list)
- [Tworzenie użytkownika odbiorcy](/graph/api/user-post-users)
- [Pobierz użytkownika](/graph/api/user-get)
- [Aktualizowanie użytkownika](/graph/api/user-update)
- [Usuwanie użytkownika](/graph/api/user-delete)

## <a name="user-phone-number-management"></a>Zarządzanie numerami telefonów użytkowników

- [Dodaj](/graph/api/authentication-post-phonemethods)
- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizowanie](/graph/api/b2cauthenticationmethodspolicy-update)
- [Usuwanie](/graph/api/phoneauthenticationmethod-delete)

Aby uzyskać więcej informacji na temat zarządzania numerem telefonu logowania użytkownika, zobacz [metody uwierzytelniania B2C](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Dostawcy tożsamości (przepływ użytkownika)

Zarządzaj dostawcami tożsamości dostępnymi dla przepływów użytkowników w dzierżawie Azure AD B2C.

- [Wyświetlanie listy dostawców tożsamości zarejestrowanych w dzierżawie Azure AD B2C](/graph/api/identityprovider-list)
- [Tworzenie dostawcy tożsamości](/graph/api/identityprovider-post-identityproviders)
- [Uzyskiwanie dostawcy tożsamości](/graph/api/identityprovider-get)
- [Aktualizowanie dostawcy tożsamości](/graph/api/identityprovider-update)
- [Usuwanie dostawcy tożsamości](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Przepływ użytkownika

Skonfiguruj wstępnie skompilowane zasady rejestracji, logowania, połączonego rejestrowania i logowania, resetowania haseł i aktualizacji profilu.

- [Wyświetlanie listy przepływów użytkownika](/graph/api/identitycontainer-list-b2cuserflows)
- [Tworzenie przepływu użytkownika](/graph/api/identitycontainer-post-b2cuserflows)
- [Pobierz przepływ użytkownika](/graph/api/b2cidentityuserflow-get)
- [Usuwanie przepływu użytkownika](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>Zasady niestandardowe

Poniższe operacje umożliwiają zarządzanie Azure AD B2C zasadami struktury zaufania, znanymi jako [zasady niestandardowe](custom-policy-overview.md).

- [Wyświetl listę wszystkich zasad struktury zaufania skonfigurowanych w dzierżawie](/graph/api/trustframework-list-trustframeworkpolicies)
- [Utwórz zasady dotyczące struktury zaufania](/graph/api/trustframework-post-trustframeworkpolicy)
- [Odczytaj właściwości istniejących zasad struktury zaufania](/graph/api/trustframeworkpolicy-get)
- [Aktualizowanie lub tworzenie zasad struktury zaufania.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Usuwanie istniejących zasad struktury zaufania](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klucze zasad

Struktura środowiska tożsamości przechowuje wpisy tajne, do których odwołuje się zasada niestandardowa, aby ustanowić relację zaufania między składnikami. Te klucze tajne mogą być symetrycznymi lub asymetryczne klucze/wartości. W Azure Portal te jednostki są wyświetlane jako **klucze zasad**.

Zasób najwyższego poziomu dla kluczy zasad w interfejsie API Microsoft Graph jest [zaufanym zestawem kluczy](/graph/api/resources/trustframeworkkeyset). Każdy **zestaw kluczy** zawiera co najmniej jeden **klucz**. Aby utworzyć klucz, najpierw utwórz pusty zestaw kluczy, a następnie Wygeneruj klucz w zestawie kluczy. Można utworzyć Ręczny klucz tajny, przekazać certyfikat lub klucz PKCS12. Klucz może być wygenerowanym wpisem tajnym, ciągiem (na przykład wpisem tajnym aplikacji Facebook) lub przekazywanym certyfikatem. Jeśli zestaw kluczy ma wiele kluczy, tylko jeden z kluczy jest aktywny.

### <a name="trust-framework-policy-keyset"></a>Zestaw kluczy zasad zaufania platformy

- [Wyświetl listę zestawów ustawień struktury zaufania](/graph/api/trustframework-list-keysets)
- [Tworzenie zestawów narzędzi struktury zaufania](/graph/api/trustframework-post-keysets)
- [Pobierz zestaw kluczy](/graph/api/trustframeworkkeyset-get)
- [Aktualizowanie zestawów danych struktury zaufania](/graph/api/trustframeworkkeyset-update)
- [Usuwanie zestawów danych struktury zaufania](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klucz zasad zaufania platformy

- [Pobierz obecnie aktywny klucz w zestawie kluczy](/graph/api/trustframeworkkeyset-getactivekey)
- [Generowanie klucza w zestawie kluczy](/graph/api/trustframeworkkeyset-generatekey)
- [Przekazywanie klucza tajnego opartego na ciągu](/graph/api/trustframeworkkeyset-uploadsecret)
- [Przekaż certyfikat X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Przekaż certyfikat formatu PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikacje

- [Wyświetlanie listy aplikacji](/graph/api/application-list)
- [Tworzenie aplikacji](/graph/api/resources/application)
- [Aktualizuj aplikację](/graph/api/application-update)
- [Utwórz obiekt serviceprincipal](/graph/api/resources/serviceprincipal)
- [Utwórz przyznanie oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Usuń aplikację](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Właściwości rozszerzenia aplikacji

- [Wyświetl właściwości rozszerzenia](/graph/api/application-list-extensionproperty)

Azure AD B2C udostępnia katalog, który może zawierać 100 atrybutów niestandardowych na użytkownika. W przypadku przepływów użytkowników te właściwości rozszerzenia są [zarządzane przy użyciu Azure Portal](user-flow-custom-attributes.md). W przypadku zasad niestandardowych Azure AD B2C tworzy dla Ciebie właściwość, podczas gdy zasady zapisują wartość do właściwości rozszerzenia.

## <a name="audit-logs"></a>Dzienniki inspekcji

- [Wyświetl listę dzienników inspekcji](/graph/api/directoryaudit-list)

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do dzienników inspekcji Azure AD B2C, zobacz [dostęp do Azure AD B2C inspekcji dzienników](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Przykład kodu: jak programowo zarządzać kontami użytkowników

Ten przykładowy kod jest aplikacją konsolową platformy .NET Core, która korzysta z [zestawu SDK Microsoft Graph](/graph/sdks/sdks-overview) , aby współdziałać z interfejsem API Microsoft Graph. W jego kodzie pokazano, jak wywołać interfejs API, aby programowo zarządzać użytkownikami w dzierżawie Azure AD B2C.
Możesz [pobrać przykładowe archiwum](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [przejrzeć repozytorium](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) w witrynie GitHub lub sklonować repozytorium:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po uzyskaniu przykładowego kodu skonfiguruj go dla środowiska, a następnie Skompiluj projekt:

1. Otwórz projekt w programie [Visual Studio](https://visualstudio.microsoft.com) lub [Visual Studio Code](https://code.visualstudio.com).
1. Otwórz plik `src/appsettings.json`.
1. W `appSettings` sekcji Zamień na `your-b2c-tenant` nazwę dzierżawy oraz `Application (client) ID` `Client secret` wartości dla rejestracji aplikacji zarządzania. Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji Microsoft Graph](microsoft-graph-get-started.md).
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

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
