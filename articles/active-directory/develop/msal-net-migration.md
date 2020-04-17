---
title: Migracja do MSAL.NET
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) a biblioteką uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) oraz o tym, jak przeprowadzić migrację do MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f389943d284c573312473f426048f8aadb79088e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533976"
---
# <a name="migrating-applications-to-msalnet"></a>Migrowanie aplikacji do MSAL.NET

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) jak i biblioteka uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów współpracowała z platformą Azure AD for developers (w wersji 1.0) w celu uwierzytelnienia tożsamości usługi Azure AD (kont służbowych i szkolnych), żądając tokenów przy użyciu biblioteki uwierzytelniania usługi Azure AD (ADAL). Korzystanie z usługi MSAL:

- można uwierzytelnić szerszy zestaw tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft oraz kont społecznościowych i lokalnych za pośrednictwem usługi Azure AD B2C), ponieważ używa punktu końcowego platformy tożsamości firmy Microsoft,
- użytkownicy otrzymają najlepsze środowisko rejestracji jednokrotnej.
- aplikacja może włączyć przyrostową zgodę, a obsługa dostępu warunkowego jest łatwiejsza
- korzyści z innowacji.

**MSAL.NET jest teraz zalecaną biblioteką śuchańską do użycia z platformą tożsamości firmy Microsoft.** W ADAL.NET nie zostaną wprowadzone żadne nowe funkcje. Wysiłki koncentrują się na poprawie MSAL.

W tym artykule opisano różnice między biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) a biblioteką uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) i opisano migrację do usługi MSAL.

## <a name="differences-between-adal-and-msal-apps"></a>Różnice między aplikacjami ADAL i MSAL

W większości przypadków chcesz użyć MSAL.NET i punktu końcowego platformy tożsamości firmy Microsoft, która jest najnowszą generacją bibliotek uwierzytelniania firmy Microsoft. Korzystając z MSAL.NET, można uzyskać tokeny dla użytkowników logujących się do aplikacji za pomocą usługi Azure AD (konta służbowe i szkolne), kont Microsoft (osobiste) (MSA) lub usługi Azure AD B2C.

Jeśli jesteś już zaznajomiony z punktu końcowego usługi Azure AD dla deweloperów (w wersji 1.0) (i ADAL.NET), możesz przeczytać Co się różni w punkcie końcowym platformy tożsamości firmy Microsoft (w wersji 2.0).If you are already familiar with the Azure AD for developers (v1.0) endpoint (and ADAL.NET), you might want to read [What's different about the Microsoft identity platform (v2.0) endpoint?](active-directory-v2-compare.md)

Jednak nadal należy używać ADAL.NET, jeśli aplikacja musi logować się do użytkowników we wcześniejszych wersjach [usług federacyjnych Active Directory (ADFS).](/windows-server/identity/active-directory-federation-services) Aby uzyskać więcej informacji, zobacz [Obsługa usługi ADFS](https://aka.ms/msal-net-adfs-support).

Na poniższej ilustracji podsumowano niektóre różnice między kodem ![ADAL.NET i MSAL.NET](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pakiety NuGet i przestrzenie nazw

ADAL.NET jest zużywany z pakietu [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet. obszar nazw, którego `Microsoft.IdentityModel.Clients.ActiveDirectory`należy użyć, to .

Aby korzystać z MSAL.NET należy dodać pakiet [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet `Microsoft.Identity.Client` i użyć obszaru nazw

### <a name="scopes-not-resources"></a>Zakresy nie zasoby

ADAL.NET nabywa tokeny dla *zasobów,* ale MSAL.NET nabywa tokeny dla *zakresów.* Wiele MSAL.NET zastępowania AcquireToken wymaga parametru o nazwie`IEnumerable<string> scopes`zakresy( ). Ten parametr jest prostą listą ciągów, które deklarują żądane uprawnienia i zasoby, które są wymagane. Dobrze znane zakresy [to zakresy programu Microsoft Graph.](/graph/permissions-reference)

Jest również możliwe w MSAL.NET, aby uzyskać dostęp do zasobów w wersji 1.0. Zobacz szczegóły w [zakresy dla aplikacji w wersji 1.0](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Podstawowe klasy

- ADAL.NET używa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako reprezentacji połączenia z usługą tokenu zabezpieczającego (STS) lub serwerem autoryzacji za pośrednictwem urzędu. Wręcz przeciwnie, MSAL.NET jest zaprojektowany wokół [aplikacji klienckich](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Zapewnia dwie oddzielne `PublicClientApplication` klasy: i`ConfidentialClientApplication`

- Pobieranie tokenów: ADAL.NET i MSAL.NET mają te same wywołania uwierzytelniania`AcquireTokenAsync` (i `AcquireTokenSilentAsync` `AcquireTokenInteractive` dla `AcquireTokenSilent` ADAL.NET i i w MSAL.NET), ale z różnymi wymaganymi parametrami. Jedną z różnic jest fakt, że w MSAL.NET nie trzeba `ClientID` już przechodzić w aplikacji w każdym wywołaniu AcquireTokenXX. Rzeczywiście, `ClientID` jest ustawiony tylko raz podczas`IPublicClientApplication` `IConfidentialClientApplication`budowy ( lub ).

### <a name="iaccount-not-iuser"></a>IAccount nie IUser

ADAL.NET manipulować użytkownikami. Jednak użytkownik jest człowiekiem lub agentem oprogramowania, ale może posiadać/posiadać/być odpowiedzialny za jedno lub więcej kont w systemie tożsamości firmy Microsoft (kilka kont usługi Azure AD, usługi Azure AD B2C, konta osobiste firmy Microsoft).

MSAL.NET 2.x definiuje teraz pojęcie Konto (za pośrednictwem interfejsu IAccount). Ta zmiana podziału zapewnia prawo semantyki: fakt, że ten sam użytkownik może mieć kilka kont w różnych katalogach usługi Azure AD. Również MSAL.NET zapewnia lepsze informacje w scenariuszach gościa, jak informacje o koncie domowym jest dostarczana.

Aby uzyskać więcej informacji na temat różnic między IUser i IAccount, zobacz [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Wyjątki

#### <a name="interaction-required-exceptions"></a>Interakcja wymagana wyjątki

MSAL.NET ma bardziej wyraźne wyjątki. Na przykład, gdy uwierzytelnianie nie ma dyskretne w ADAL procedura jest złapać wyjątek i poszukaj `user_interaction_required` kodu błędu:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Zobacz szczegóły w [Zalecany wzorzec, aby uzyskać token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) z ADAL.NET

Korzystając z MSAL.NET, można `MsalUiRequiredException` złapać zgodnie z [opisem w AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Obsługa wyjątków zaskarżenia roszczeń

W ADAL.NET wyjątki od zaskarżenia oświadczeń są obsługiwane w następujący sposób:

- `AdalClaimChallengeException`jest wyjątkiem (pochodzącym `AdalServiceException`z ) generowanym przez usługę w przypadku, gdy zasób wymaga więcej oświadczeń od użytkownika (na przykład uwierzytelnianie dwuskładnikowe). Element `Claims` członkowski zawiera niektóre fragment JSON z oświadczeń, które są oczekiwane.
- Nadal w ADAL.NET publiczna aplikacja kliencka odbierająca `AcquireTokenInteractive` ten wyjątek musi wywołać zastąpienie o parametr oświadczeń. To zastąpienie `AcquireTokenInteractive` nawet nie próbuje trafić w pamięć podręczną, ponieważ nie jest to konieczne. Powodem jest to, że token w pamięci podręcznej `AdalClaimChallengeException` nie ma odpowiednich oświadczeń (w przeciwnym razie nie zostałyby rzucone). W związku z tym nie ma potrzeby, aby spojrzeć na pamięci podręcznej. Należy zauważyć, że `ClaimChallengeException` można odbierać w webapi `AcquireTokenInteractive` robi OBO, podczas gdy musi być wywoływana w aplikacji klienta publicznego wywołującego ten internetowy interfejs API.
- szczegółowe informacje, w tym próbki, patrz Obsługa [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

W MSAL.NET wyjątki od zaskarżenia oświadczeń są obsługiwane w następujący sposób:

- Są `Claims` powierzchniowe w `MsalServiceException`pliku .
- Istnieje `.WithClaim(claims)` metoda, która może `AcquireTokenInteractive` mieć zastosowanie do konstruktora.

### <a name="supported-grants"></a>Dotacje wspierane

Nie wszystkie dotacje są jeszcze obsługiwane w MSAL.NET i w punkcie końcowym v2.0. Poniżej znajduje się podsumowanie porównujące ADAL.NET i MSAL. dotacje wspierane przez NET.

#### <a name="public-client-applications"></a>Publiczne aplikacje klienckie

Oto dotacje wspierane w ADAL.NET i MSAL.NET dla aplikacji komputerowych i mobilnych

Uprawnienie | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktywne | [Interaktywna eru](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktywne pozyskiwanie tokenów w MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Zintegrowane uwierzytelnianie systemu Windows | [Zintegrowane uwierzytelnianie w systemie Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nazwa użytkownika / hasło | [Pobieranie tokenów z nazwą użytkownika i hasłem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Uwierzytelnianie hasła nazwy użytkownika](msal-authentication-flows.md#usernamepassword)
Przepływ kodu urządzenia | [Profil urządzenia dla urządzeń bez przeglądarek internetowych](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Poufne aplikacje klienckie

Oto dotacje obsługiwane w ADAL.NET i MSAL.NET dla aplikacji sieci web, interfejsów API sieci Web i aplikacji demonów:

Typ aplikacji | Uprawnienie | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Aplikacja internetowa, internetowy interfejs API, demon | Poświadczenia klienta | [Przepływy poświadczeń klienta w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Przepływy poświadczeń klienta w MSAL.NET](msal-authentication-flows.md#client-credentials))
Interfejs API sieci Web | W imieniu | [Usługa obsługi połączeń w imieniu użytkownika za pomocą ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [W imieniu MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplikacja internetowa | Kod Emath | [Pobieranie tokenów z kodami autoryzacji w aplikacjach internetowych za pomocą ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Pobieranie tokenów z kodami autoryzacji w aplikacjach internetowych za pomocą MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Trwałość pamięci podręcznej

ADAL.NET umożliwia rozszerzenie `TokenCache` klasy w celu zaimplementowania żądanej funkcji trwałości na platformach bez bezpiecznego magazynu `BeforeAccess`(.NET Framework i .NET core) przy użyciu metody , i. `BeforeWrite` Aby uzyskać szczegółowe informacje, zobacz [Serializacja pamięci podręcznej tokenów w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET sprawia, że pamięć podręczna tokenu jest zapieczętowaną klasą, usuwając możliwość jej rozszerzenia. W związku z tym implementacja trwałości pamięci podręcznej tokenu musi mieć postać klasy pomocnika, która współdziała z zapieczętowaną pamięcią podręczną tokenu. Ta interakcja jest opisana w [serializacji pamięci podręcznej tokenów w MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Oznaczanie wspólnego organu

W wersji 1.0, `https://login.microsoftonline.com/common` jeśli używasz urzędu, możesz zezwolić użytkownikom na logowanie się przy użyciu dowolnego konta AAD (dla dowolnej organizacji). Zobacz [Sprawdzanie poprawności urzędu w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Jeśli korzystasz `https://login.microsoftonline.com/common` z urzędu w wersji 2.0, użytkownik zezwoli na logowanie się użytkowników za pomocą dowolnej organizacji AAD lub konta osobistego Microsoft (MSA). W MSAL.NET, jeśli chcesz ograniczyć logowanie do dowolnego konta AAD (takie samo zachowanie `https://login.microsoftonline.com/organizations`jak w przypadku ADAL.NET), musisz użyć . Aby uzyskać szczegółowe `authority` informacje, zobacz parametr w [publicznej aplikacji klienckiej](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>żetony v1.0 i v2.0

Istnieją dwie wersje tokenów:
- żetony w wersji 1.0
- żetony wersji 2.0

Punkt końcowy w wersji 1.0 (używany przez ADAL) emituje tylko tokeny w wersji 1.0.

Jednak punkt końcowy w wersji 2.0 (używany przez MSAL) emituje wersję tokenu, który akceptuje internetowy interfejs API. Właściwość manifestu aplikacji interfejsu API sieci web umożliwia deweloperom wybrać, która wersja tokenu jest akceptowana. Zobacz `accessTokenAcceptedVersion` w dokumentacji referencyjnej [manifestu aplikacji.](reference-app-manifest.md)

Aby uzyskać więcej informacji na temat tokenów w wersji 1.0 i wersji 2.0, zobacz [tokeny dostępu usługi Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy dla internetowego interfejsu API akceptującego tokeny w wersji 1.0

Uprawnienia OAuth2 są zakresami uprawnień, które aplikacja (zasób) sieci web w wersji 1.0 udostępnia aplikacjom klienckim. Te zakresy uprawnień mogą być przyznawane aplikacjom klienckim podczas udzielania zgody. Zobacz sekcję o oauth2Permissions w [manifeście aplikacji usługi Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do określonych uprawnień OAuth2 aplikacji w wersji 1.0

Jeśli chcesz uzyskać tokeny dla aplikacji akceptującej tokeny w wersji 1.0 https://graph.microsoft.com)(na przykład interfejsu `scopes` API programu Microsoft Graph, który jest , należy utworzyć przez łączenie żądanego identyfikatora zasobu z żądanym uprawnieniem OAuth2 dla tego zasobu.

Na przykład, aby uzyskać dostęp w imieniu użytkownika interfejsu API sieci web w `ResourceId`wersji 1.0, który jest identyfikatorem URI identyfikatora aplikacji, należy użyć:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Jeśli chcesz czytać i pisać za pomocą MSAL.NET usługi Azure Activehttps://graph.microsoft.com/)Directory przy użyciu interfejsu API programu Microsoft Graph ( , należy utworzyć listę zakresów, takich jak w poniższym urywek:

```csharp
ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Ostrzeżenie: Jeśli masz jeden lub dwa ukośniki w zakresie odpowiadającym internetowemu interfejsowi API w wersji 1.0

Jeśli chcesz napisać zakres odpowiadający interfejsowi APIhttps://management.core.windows.net/)usługi Azure Resource Manager ( , musisz zażądać następującego zakresu (zwróć uwagę na dwa ukośniki)

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Dzieje się tak, ponieważ interfejs API Menedżera zasobów`aud`oczekuje ukośnika w oświadczeniu grupy odbiorców ( ), a następnie istnieje ukośnik, aby oddzielić nazwę interfejsu API od zakresu.

Logika używana przez usługę Azure AD jest następująca:
- Dla punktu końcowego ADAL (v1.0) z tokenem dostępu w wersji 1.0 (jedyny możliwy), aud=resource
- W przypadku usługi MSAL (punktu końcowego w wersji 2.0) z prośbą o token dostępu dla zasobu akceptującego tokeny w wersji 2.0, aud=resource. Appid
- W przypadku usługi MSAL (punktu końcowego w wersji 2.0) z prośbą o token dostępu dla zasobu akceptującego token dostępu w wersji 1.0 (co ma miejsce powyżej), usługa Azure AD analizuje żądaną grupę odbiorców z żądanego zakresu, biorąc wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu. Dlatego jeśli https:\//database.windows.net oczekuje odbiorców "https://database.windows.net/", musisz poprosić o zakres https:\//database.windows.net//.default. Zobacz też problem #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Adres url zasobów końcowe ukośnika jest pomijany, co spowodowało niepowodzenie auth sql #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do wszystkich uprawnień aplikacji w wersji 1.0

Na przykład, jeśli chcesz uzyskać token dla wszystkich zakresów statycznych aplikacji w wersji 1.0, należy użyć

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Zakresy do żądania w przypadku przepływu poświadczeń klienta / aplikacji demona

W przypadku przepływu poświadczeń klienta zakres `/.default`do przekazania będzie również . Ten zakres informuje usługi Azure AD: "wszystkie uprawnienia na poziomie aplikacji, na które administrator wyraził zgodę w rejestracji aplikacji.

## <a name="adal-to-msal-migration"></a>Migracja ADAL do MSAL

W ADAL.NET v2. X, tokeny odświeżania zostały ujawnione, co pozwala na tworzenie rozwiązań dotyczących korzystania `AcquireTokenByRefreshToken` z tych tokenów przez ich buforowanie i przy użyciu metod dostarczonych przez ADAL 2.x.
Niektóre z tych rozwiązań zostały wykorzystane w scenariuszach, takich jak:
* Długotrwałe usługi, które wykonują akcje, w tym odświeżanie pulpitów nawigacyjnych w imieniu użytkowników, podczas gdy użytkownicy nie są już połączeni.
* WebFarm scenariusze umożliwiające klientowi przenieść RT do usługi sieci web (buforowanie odbywa się po stronie klienta, zaszyfrowany plik cookie, a nie po stronie serwera)

MSAL.NET nie udostępnia tokenów odświeżania ze względów bezpieczeństwa: MSAL obsługuje odświeżanie tokenów dla Ciebie.

Na szczęście MSAL.NET ma teraz interfejs API, który umożliwia migrację poprzednich tokenów odświeżania `IConfidentialClientApplication`(nabytych za pomocą usługi ADAL) do:

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

Za pomocą tej metody można podać poprzednio używany token odświeżania wraz z dowolnymi zakresami (zasobami), które chcesz. Token odświeżania zostanie wymieniony na nowy i buforowany w aplikacji.

Ponieważ ta metoda jest przeznaczona dla scenariuszy, które nie `IConfidentialClientApplication` są typowe, `IByRefreshToken`nie jest łatwo dostępna bez pierwszego rzutowania go do .

Ten fragment kodu zawiera kod migracji w poufnej aplikacji klienckiej. `GetCachedRefreshTokenForSignedInUser`pobrać token odświeżania, który był przechowywany w niektórych magazynach przez poprzednią wersję aplikacji, która używała do wykorzystania ADAL 2.x. `GetTokenCacheForSignedInUser`deserializuje pamięć podręczną dla zalogowanego użytkownika (jako poufne aplikacje klienckie powinny mieć jedną pamięć podręczną na użytkownika).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Zobaczysz token dostępu i token identyfikatora zwrócone w authenticationResult, gdy nowy token odświeżania jest przechowywany w pamięci podręcznej.

Tej metody można również użyć dla różnych scenariuszy integracji, w których masz token odświeżania dostępne.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat zakresów w [zakresach, uprawnieniach i zgody](v2-permissions-and-consent.md) można znaleźć w punkcie końcowym platformy tożsamości firmy Microsoft
