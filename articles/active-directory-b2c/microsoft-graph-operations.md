---
title: Zarządzanie zasobami za pomocą Microsoft Graph
titleSuffix: Azure AD B2C
description: Jak zarządzać zasobami w dzierżawie Azure AD B2C, wywołując interfejs API Microsoft Graph i automatyzując proces przy użyciu tożsamości aplikacji.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf62330fd677dc978c8f25a81c6a1e5bfbb612ac
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717607"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Zarządzanie Azure AD B2C za pomocą Microsoft Graph

Microsoft Graph umożliwia zarządzanie zasobami w Azure AD B2C katalogu. Następujące operacje Microsoft Graph API są obsługiwane w celu zarządzania zasobami usługi Azure AD B2C, w tym użytkownikami, dostawcami tożsamości, przepływami użytkowników, zasadami niestandardowymi i kluczami zasad. Każdy link w poniższych sekcjach dotyczy odpowiedniej strony w Microsoft Graph interfejsu API dla tej operacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać interfejs Graph API ms i wchodzić w interakcje z zasobami w dzierżawie usługi Azure AD B2C, potrzebna jest rejestracja aplikacji, która przyznaje do tego uprawnienia. Wykonaj kroki opisane w artykule Zarządzanie Azure AD B2C za pomocą [Microsoft Graph,](microsoft-graph-get-started.md) aby utworzyć rejestrację aplikacji, z których może korzystać aplikacja do zarządzania. 

## <a name="user-management"></a>Zarządzanie użytkownikami

- [Wyświetlanie użytkowników](/graph/api/user-list)
- [Tworzenie użytkownika](/graph/api/user-post-users)
- [Uzyskiwanie użytkownika](/graph/api/user-get)
- [Aktualizowanie użytkownika](/graph/api/user-update)
- [Usuwanie użytkownika](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Zarządzanie numerami telefonów użytkowników (beta)

Numer telefonu, który może być używany przez użytkownika do logowania się przy użyciu wiadomości [SMS](identity-provider-local.md#phone-sign-in-preview)lub połączeń głosowych albo [uwierzytelniania wieloskładnikowego.](multi-factor-authentication.md) Aby uzyskać więcej informacji, zobacz [Interfejs API metod uwierzytelniania usługi Azure AD.](/graph/api/resources/phoneauthenticationmethod)

- [Dodaj](/graph/api/authentication-post-phonemethods)
- [Lista](/graph/api/authentication-list-phonemethods)
- [Get](/graph/api/phoneauthenticationmethod-get)
- [Aktualizowanie](/graph/api/phoneauthenticationmethod-update)
- [Usuwanie](/graph/api/phoneauthenticationmethod-delete)

Pamiętaj, że [operacja listy](/graph/api/authentication-list-phonemethods) zwraca tylko włączone numery telefonów. Z operacjami na liście należy włączyć następujący numer telefonu. 

![Włączanie logowania za pomocą telefonu](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> W bieżącej wersji beta ten interfejs API działa tylko wtedy, gdy numer telefonu jest przechowywany ze spacją między kodem kraju a numerem telefonu. Usługa Azure AD B2C obecnie domyślnie nie dodaje tego miejsca.

## <a name="self-service-password-reset-email-address-beta"></a>Adres e-mail samoobsługowego resetowania hasła (beta)

Adres e-mail, który może być używany przez konto logowania nazwy [użytkownika](identity-provider-local.md#username-sign-in) do resetowania hasła. Aby uzyskać więcej informacji, zobacz [Interfejs API metod uwierzytelniania usługi Azure AD.](/graph/api/resources/emailauthenticationmethod)

- [Dodaj](/graph/api/emailauthenticationmethod-post)
- [Lista](/graph/api/emailauthenticationmethod-list)
- [Get](/graph/api/emailauthenticationmethod-get)
- [Aktualizowanie](/graph/api/emailauthenticationmethod-update)
- [Usuwanie](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Dostawcy tożsamości

Zarządzanie [dostawcami tożsamości](add-identity-provider.md) dostępnymi dla przepływów użytkownika w dzierżawie Azure AD B2C dzierżawy.

- [Lista dostawców tożsamości zarejestrowanych w dzierżawie Azure AD B2C dzierżawy](/graph/api/identityprovider-list)
- [Tworzenie dostawcy tożsamości](/graph/api/identityprovider-post-identityproviders)
- [Uzyskiwanie dostawcy tożsamości](/graph/api/identityprovider-get)
- [Aktualizowanie dostawcy tożsamości](/graph/api/identityprovider-update)
- [Usuwanie dostawcy tożsamości](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Przepływ użytkownika

Konfigurowanie wstępnie skonfigurowaliśmy zasad rejestracji, logowania, połączonej rejestracji i logowania, resetowania hasła i aktualizacji profilu.

- [Lista przepływów użytkownika](/graph/api/identitycontainer-list-b2cuserflows)
- [Tworzenie przepływu użytkownika](/graph/api/identitycontainer-post-b2cuserflows)
- [Uzyskiwanie przepływu użytkownika](/graph/api/b2cidentityuserflow-get)
- [Usuwanie przepływu użytkownika](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Metody uwierzytelniania przepływu użytkownika (beta)

Wybierz mechanizm umożliwiania użytkownikom rejestrowania się za pośrednictwem kont lokalnych. Konta lokalne to konta, w przypadku których usługa Azure AD obsługuje potwierdzenia tożsamości. Aby uzyskać więcej informacji, zobacz [typ zasobu b2cAuthenticationMethodsPolicy](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [Aktualizowanie](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Zasady niestandardowe

Następujące operacje umożliwiają zarządzanie zasadami struktury zaufania Azure AD B2C, nazywanymi zasadami [niestandardowymi.](custom-policy-overview.md)

- [Lista wszystkich zasad struktury zaufania skonfigurowanych w dzierżawie](/graph/api/trustframework-list-trustframeworkpolicies)
- [Tworzenie zasad struktury zaufania](/graph/api/trustframework-post-trustframeworkpolicy)
- [Odczytywanie właściwości istniejących zasad struktury zaufania](/graph/api/trustframeworkpolicy-get)
- [Aktualizowanie lub tworzenie zasad struktury zaufania.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Usuwanie istniejących zasad struktury zaufania](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Klucze zasad

W Identity Experience Framework wpisy tajne, do których odwołuje się zasada niestandardowa, można ustanowić zaufanie między składnikami. Te wpisy tajne mogą być kluczami/wartościami symetrycznymi lub asymetrycznymi. W Azure Portal te jednostki są wyświetlane jako **klucze zasad**.

Zasób najwyższego poziomu dla kluczy zasad w interfejsie API Microsoft Graph to zaufany [zestaw kluczy struktury](/graph/api/resources/trustframeworkkeyset). Każdy **zestaw kluczy** zawiera co najmniej jeden **klucz**. Aby utworzyć klucz, najpierw utwórz pusty zestaw kluczy, a następnie wygeneruj klucz w tym zsetze kluczy. Możesz utworzyć ręczny klucz tajny, przekazać certyfikat lub klucz PKCS12. Klucz może być wygenerowanym wpisem tajnym, ciągiem (takim jak wpis tajny aplikacji serwisu Facebook) lub certyfikatem, który przekażemy. Jeśli zestaw kluczy ma wiele kluczy, aktywny jest tylko jeden z nich.

### <a name="trust-framework-policy-keyset"></a>Zestaw kluczy zasad struktury zaufania

- [Lista ets kluczy struktury zaufania](/graph/api/trustframework-list-keysets)
- [Tworzenie tworzyć grupy kluczy struktury zaufania](/graph/api/trustframework-post-keysets)
- [Uzyskiwanie zestawu kluczy](/graph/api/trustframeworkkeyset-get)
- [Aktualizowanie ets kluczy struktury zaufania](/graph/api/trustframeworkkeyset-update)
- [Usuwanie ets kluczy struktury zaufania](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Klucz zasad struktury zaufania

- [Pobierz obecnie aktywny klucz w z zestawu kluczy](/graph/api/trustframeworkkeyset-getactivekey)
- [Generowanie klucza w zestawu kluczy](/graph/api/trustframeworkkeyset-generatekey)
- [Przekazywanie ciągu opartego na kluczu tajnym](/graph/api/trustframeworkkeyset-uploadsecret)
- [Przekazywanie certyfikatu X.509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Przekazywanie certyfikatu formatu PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Aplikacje

- [Wyświetlanie listy aplikacji](/graph/api/application-list)
- [Tworzenie aplikacji](/graph/api/resources/application)
- [Aktualizowanie aplikacji](/graph/api/application-update)
- [Tworzenie konta servicePrincipal](/graph/api/resources/serviceprincipal)
- [Tworzenie przyznawania oauth2Permission](/graph/api/resources/oauth2permissiongrant)
- [Usuwanie aplikacji](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Właściwości rozszerzenia aplikacji

- [Lista właściwości rozszerzenia](/graph/api/application-list-extensionproperty)

Azure AD B2C katalog, w którym można przechowywać 100 atrybutów niestandardowych na użytkownika. W przypadku przepływów użytkownika te właściwości rozszerzenia są [zarządzane przy użyciu Azure Portal](user-flow-custom-attributes.md). W przypadku zasad Azure AD B2C tworzy właściwość za Ciebie, gdy zasady po raz pierwszy zapisną wartość do właściwości rozszerzenia.

## <a name="audit-logs"></a>Dzienniki inspekcji

- [Lista dzienników inspekcji](/graph/api/directoryaudit-list)

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do Azure AD B2C inspekcji, zobacz [Accessing Azure AD B2C audit logs](view-audit-logs.md)(Uzyskiwanie dostępu Azure AD B2C inspekcji).

## <a name="conditional-access"></a>Dostęp warunkowy

- [Lista wszystkich zasad dostępu warunkowego](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Odczytywanie właściwości i relacji zasad dostępu warunkowego](/graph/api/conditionalaccesspolicy-get)
- [Tworzenie nowych zasad dostępu warunkowego](/graph/api/resources/application)
- [Aktualizowanie zasad dostępu warunkowego](/graph/api/conditionalaccesspolicy-update)
- [Usuwanie zasad dostępu warunkowego](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Przykład kodu: Jak programowo zarządzać kontami użytkowników

Ten przykładowy kod to aplikacja konsolowa platformy .NET Core, która używa [zestawu SDK](/graph/sdks/sdks-overview) Microsoft Graph do interakcji z Microsoft Graph API. Jego kod pokazuje, jak wywołać interfejs API w celu programowego zarządzania użytkownikami w Azure AD B2C dzierżawy.
Możesz pobrać [przykładowe archiwum](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip), przejrzeć [repozytorium](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) w witrynie GitHub lub sklonować repozytorium:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Po pobraniu przykładowego kodu skonfiguruj go dla swojego środowiska, a następnie skonstruuj projekt:

1. Otwórz projekt w [Visual Studio](https://visualstudio.microsoft.com) lub [Visual Studio Code](https://code.visualstudio.com).
1. Otwórz plik `src/appsettings.json`.
1. W sekcji zastąp wartość nazwą dzierżawy, a wartościami dla `appSettings` `your-b2c-tenant` rejestracji aplikacji do `Application (client) ID` `Client secret` zarządzania. Aby uzyskać więcej informacji, [zobacz Register a Microsoft Graph Application (Rejestrowanie Microsoft Graph aplikacji).](microsoft-graph-get-started.md)
1. Otwórz okno konsoli w lokalnym klonie repo, przełącz się do katalogu, a `src` następnie skompilować projekt:

    ```console
    cd src
    dotnet build
    ```
    
1. Uruchom aplikację za pomocą `dotnet` polecenia :

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Aplikacja wyświetli listę poleceń, które można wykonać. Na przykład pobierz wszystkich użytkowników, uzyskaj jednego użytkownika, usuń użytkownika, zaktualizuj hasło użytkownika i importu zbiorczego.

### <a name="code-discussion"></a>Omówienie kodu

Przykładowy kod korzysta z [Microsoft Graph SDK](/graph/sdks/sdks-overview), który został zaprojektowany w celu uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które mają dostęp do Microsoft Graph.

Każde żądanie do interfejsu API Microsoft Graph wymaga tokenu dostępu do uwierzytelniania. Rozwiązanie korzysta z pakietu NuGet [Microsoft.Graph.Auth,](https://www.nuget.org/packages/Microsoft.Graph.Auth/) który zapewnia opartą na scenariuszu uwierzytelnianie otokę biblioteki Microsoft Authentication Library (MSAL) do użycia z zestawem MICROSOFT GRAPH SDK.

Metoda `RunAsync` w pliku _Program.cs:_

1. Odczytuje ustawienia aplikacji z _appsettings.jspliku_
1. Inicjuje dostawcę uwierzytelniania przy użyciu przepływu udzielania poświadczeń klienta [OAuth 2.0.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) W przypadku przepływu udzielania poświadczeń klienta aplikacja może uzyskać token dostępu w celu wywołania interfejsu API Microsoft Graph API.
1. Konfiguruje klienta Microsoft Graph usługi przy użyciu dostawcy uwierzytelniania:

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

Zainicjowany interfejs *GraphServiceClient* jest następnie używany w _userService.cs_ do wykonywania operacji zarządzania użytkownikami. Na przykład uzyskanie listy kont użytkowników w dzierżawie:

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

Wykonywanie [wywołań](/graph/sdks/create-requests) interfejsu API przy użyciu zestawów SDK usługi Microsoft Graph zawiera informacje na temat sposobu odczytywania i zapisu informacji z usługi Microsoft Graph, używania funkcji do kontrolowania zwracanych właściwości, zapewnienia niestandardowych parametrów zapytania oraz używania parametrów zapytania i `$select` `$filter` `$orderBy` .

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
