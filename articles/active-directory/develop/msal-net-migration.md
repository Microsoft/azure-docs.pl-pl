---
title: Migrowanie do MSAL.NET
titleSuffix: Microsoft identity platform
description: Poznaj różnice między biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) i biblioteką uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) oraz dowiedz się, jak przeprowadzić migrację do MSAL.NET.
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 0e7dc3540dc54e0563a5ea416510bddb9a41fb65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861701"
---
# <a name="migrating-applications-to-msalnet"></a>Migrowanie aplikacji do MSAL.NET

Zarówno biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET), jak i biblioteka uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD dla deweloperów (wersja 1.0) w celu uwierzytelniania tożsamości usługi Azure AD (kont służbowych) przez żądanie tokenów przy użyciu biblioteki Azure AD Authentication Library (ADAL). Korzystanie z msal:

- Możesz uwierzytelnić szerszy zestaw tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft oraz kont społecznościowych i lokalnych za pośrednictwem usługi Azure AD B2C), ponieważ korzysta ona z platformy tożsamości firmy Microsoft.
- Użytkownicy uzyskają najlepsze środowisko logowania pojedynczego.
- Aplikacja może włączyć przyrostowe wyrażanie zgody, a obsługa dostępu warunkowego jest łatwiejsza
- korzyści z innowacji.

**MSAL.NET lub Microsoft.Identity.Web** są teraz zalecanymi bibliotekami uwierzytelniania do użycia z platformą tożsamości firmy Microsoft. Na ADAL.NET nie zostaną zaimplementowane żadne nowe ADAL.NET. Te wysiłki koncentrują się na ulepszaniu msal.

W tym artykule opisano różnice między biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET) i biblioteką uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) oraz przedstawiono pomoc w migracji do biblioteki MSAL.

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>W przypadku migracji do witryny MSAL.NET lub do witryny Microsoft.Identity.Web

Przed zagłębianiem się w szczegóły MSAL.NET vs ADAL.NET warto sprawdzić, czy chcesz użyć MSAL.NET, czy abstrakcji wyższego poziomu, np. [Microsoft.Identity.Web](microsoft-identity-web.md)

Aby uzyskać szczegółowe informacje o drzewie decyzyjnym poniżej, przeczytaj temat Czy należy używać tylko MSAL.NET czy abstrakcji [wyższego poziomu?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Is-MSAL.NET-right-for-me%3F)

:::image type="content" source="media/msal-net-migration/decision-diagram.png" alt-text="Diagram blokowy wyjaśniający, jak wybrać, czy podczas migracji z programu MSAL.NET i Microsoft.Identity.Web ma być konieczne użycie obu tych ADAL.NET":::

## <a name="differences-between-adal-and-msal-apps"></a>Różnice między aplikacjami ADAL i MSAL

W większości przypadków chcesz użyć usługi MSAL.NET platformy tożsamości firmy Microsoft, która jest najnowszą generacją bibliotek uwierzytelniania firmy Microsoft. Korzystając MSAL.NET, uzyskujesz tokeny dla użytkowników logujący się do aplikacji przy użyciu konta usługi Azure AD (konta służbowego), kont Microsoft (osobistych) (MSA) lub kont Azure AD B2C.

Jeśli znasz już punkt końcowy usługi Azure AD dla deweloperów (w wersji 1.0) (i program ADAL.NET), warto przeczytać temat Co różni się od platformy tożsamości [firmy Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md).

Jednak nadal musisz używać usługi ADAL.NET, jeśli aplikacja musi zalogować użytkowników przy użyciu wcześniejszych wersji [usługi Active Directory Federation Services (ADFS).](/windows-server/identity/active-directory-federation-services) Aby uzyskać więcej informacji, zobacz [Obsługa usług AD FS.](https://aka.ms/msal-net-adfs-support)

Na poniższej ilustracji przedstawiono podsumowanie niektórych różnic między ADAL.NET i MSAL.NET dla kodu ![ side-by-side aplikacji klienta publicznego](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pakiety NuGet i przestrzenie nazw

ADAL.NET z pakietu [NuGet Microsoft.IdentityModel.Clients.ActiveDirectory.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) Przestrzeń nazw do użycia to `Microsoft.IdentityModel.Clients.ActiveDirectory` .

Aby użyć MSAL.NET należy dodać pakiet NuGet [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) i użyć przestrzeni `Microsoft.Identity.Client` nazw

### <a name="scopes-not-resources"></a>Zakresy nie zasobów

ADAL.NET uzyskuje tokeny dla *zasobów,* ale MSAL.NET uzyskuje tokeny dla *zakresów*. Wiele przesło MSAL.NET AcquireToken wymaga parametru o nazwie scopes( `IEnumerable<string> scopes` ). Ten parametr to prosta lista ciągów, które deklarują żądane uprawnienia i zasoby. Dobrze znane zakresy to [Microsoft Graph zakresu.](/graph/permissions-reference)

Dostęp do zasobów w MSAL.NET w wersji 1.0 jest również możliwy w programie . Szczegółowe informacje można [znaleźć w tece Zakresy dla aplikacji w wersji 1.0.](#scopes-for-a-web-api-accepting-v10-tokens)

### <a name="core-classes"></a>Klasy podstawowe

- ADAL.NET używa [metody AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako reprezentacji połączenia z usługą tokenu zabezpieczającego (STS) lub serwerem autoryzacji za pośrednictwem urzędu. Z drugiej strony, MSAL.NET projektowane wokół [aplikacji klienckich.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) Udostępnia dwie oddzielne klasy: `PublicClientApplication` i `ConfidentialClientApplication`

- Uzyskiwanie tokenów: ADAL.NET i MSAL.NET mają takie same wywołania uwierzytelniania ( i dla ADAL.NET i i w MSAL.NET), ale z innymi wymaganymi `AcquireTokenAsync` `AcquireTokenSilentAsync` `AcquireTokenInteractive` `AcquireTokenSilent` parametrami. Jedną z różnic jest fakt, że w MSAL.NET nie trzeba już przekazywać kodu aplikacji w każdym `ClientID` wywołaniu AcquireTokenXX. W rzeczywistości obiekt `ClientID` jest ustawiany tylko raz podczas tworzenia obiektu ( `IPublicClientApplication` lub `IConfidentialClientApplication` ).

### <a name="iaccount-not-iuser"></a>IAccount nie IUser

ADAL.NET manipulować użytkownikami. Jednak użytkownik jest człowiekiem lub agentem oprogramowania, ale może posiadać/być właścicielem/być odpowiedzialny za co najmniej jedno konto w systemie tożsamości firmy Microsoft (kilka kont usługi Azure AD, Azure AD B2C, konta osobiste Microsoft).

MSAL.NET 2.x definiuje teraz koncepcję konta (za pośrednictwem interfejsu IAccount). Ta przełomowa zmiana zapewnia właściwą semantykę: fakt, że ten sam użytkownik może mieć kilka kont w różnych katalogach usługi Azure AD. Ponadto MSAL.NET zapewnia lepsze informacje w scenariuszach gościa, ponieważ podano informacje o koncie macierzysłym.

Aby uzyskać więcej informacji o różnicach między użytkownikami i kontami IAccount, [zobacz MSAL.NET 2.x.](https://aka.ms/msal-net-2-released)

### <a name="exceptions"></a>Wyjątki

#### <a name="interaction-required-exceptions"></a>Wymagane wyjątki interakcji

MSAL.NET ma bardziej jawne wyjątki. Na przykład w przypadku niepowodzenia uwierzytelniania dyskretnego w adal procedura jest przechwyć wyjątek i poszukaj `user_interaction_required` kodu błędu:

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

Zobacz szczegóły w [zalecanym wzorcu, aby uzyskać token za](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) pomocą ADAL.NET

Używając MSAL.NET, przechwytuje się zgodnie `MsalUiRequiredException` z opisem w [tece AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Obsługa wyjątków wyzwania oświadczenia

W ADAL.NET żądania wyjątków są obsługiwane w następujący sposób:

- `AdalClaimChallengeException` to wyjątek (pochodzący od klasy ) zgłaszany przez usługę w przypadku, gdy zasób wymaga większej liczby oświadczeń od użytkownika (na przykład `AdalServiceException` uwierzytelniania dwuskładnikowego). Członek `Claims` zawiera fragment JSON z oczekiwanymi oświadczeniami.
- Nadal w ADAL.NET aplikacja klienca publiczna odbierający ten wyjątek musi wywołać `AcquireTokenInteractive` przesłonięcie mające parametr oświadczeń. To przesłonięcie `AcquireTokenInteractive` nie próbuje nawet trafić do pamięci podręcznej, ponieważ nie jest to konieczne. Przyczyną jest to, że token w pamięci podręcznej nie ma odpowiednich oświadczeń (w przeciwnym razie `AdalClaimChallengeException` nie zostałby zgłoszony). W związku z tym nie ma potrzeby przyjrzenia się pamięci podręcznej. Należy pamiętać, że obiekt może zostać odebrany w interfejsie WebAPI wykonującym obiekt OBO, natomiast obiekt musi być wywoływany w publicznej aplikacji klienckiej wywołującej `ClaimChallengeException` `AcquireTokenInteractive` ten internetowy interfejs API.
- Aby uzyskać szczegółowe informacje, w tym przykłady, zobacz Handling [AdalClaimChageException (Obsługa wyjątku AdalClaimChageException)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

W MSAL.NET żądania wyjątki dotyczące wyzwania są obsługiwane w następujący sposób:

- Są `Claims` one dostępne w . `MsalServiceException`
- Istnieje `.WithClaim(claims)` metoda, która może być stosowana do `AcquireTokenInteractive` konstruktora.

### <a name="supported-grants"></a>Obsługiwane granty

Nie wszystkie granty są jeszcze obsługiwane w MSAL.NET i punkcie końcowym w wersji 2.0. Poniżej przedstawiono podsumowanie porównujące ADAL.NET i MSAL. Obsługiwane granty dla sieci NET.

#### <a name="public-client-applications"></a>Publiczne aplikacje klienckie

Oto granty obsługiwane w usługach ADAL.NET i MSAL.NET dla aplikacji klasycznych i mobilnych

Uprawnienie | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktywne | [Uwierzytelnianie interakcyjne](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktywne uzyskiwanie tokenów w MSAL.NET](scenario-desktop-acquire-token.md?tabs=dotnet#acquire-a-token-interactively)
Zintegrowane uwierzytelnianie systemu Windows | [Zintegrowane uwierzytelnianie w systemie Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Zintegrowane uwierzytelnianie systemu Windows](scenario-desktop-acquire-token.md?tabs=dotnet#integrated-windows-authentication)
Nazwa użytkownika/hasło | [Uzyskiwanie tokenów przy użyciu nazwy użytkownika i hasła](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Uwierzytelnianie za pomocą hasła nazwy użytkownika](scenario-desktop-acquire-token.md?tabs=dotnet#username-and-password)
Przepływ kodu urządzenia | [Profil urządzenia dla urządzeń bez przeglądarek internetowych](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Przepływ kodu urządzenia](scenario-desktop-acquire-token.md?tabs=dotnet#command-line-tool-without-a-web-browser)

#### <a name="confidential-client-applications"></a>Poufne aplikacje klienckie

Oto granty obsługiwane w usługach ADAL.NET, MSAL.NET i Microsoft.Identity.Web dla aplikacji internetowych, internetowych interfejsów API i aplikacji demona:

Typ aplikacji | Uprawnienie | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Aplikacja internetowa, internetowy interfejs API, demon | Poświadczenia klienta | [Przepływy poświadczeń klienta w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Przepływy poświadczeń klienta w MSAL.NET](scenario-daemon-acquire-token.md?tabs=dotnet#acquiretokenforclient-api)
Interfejs API sieci Web | W imieniu | [Wywołania usługi do usługi w imieniu użytkownika za pomocą ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [W imieniu użytkownika w MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)
Aplikacja internetowa | Kod uwierzytelniania | [Uzyskiwanie tokenów za pomocą kodów autoryzacji w aplikacjach internetowych przy użyciu ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Uzyskiwanie tokenów za pomocą kodów autoryzacji w aplikacjach internetowych za pomocą MSAL.NET](scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

### <a name="cache-persistence"></a>Trwałość pamięci podręcznej

ADAL.NET umożliwia rozszerzenie klasy w celu zaimplementowania żądanej funkcji trwałości na platformach bez bezpiecznego magazynu (.NET Framework i .NET Core) przy użyciu metod `TokenCache` `BeforeAccess` i `BeforeWrite` . Aby uzyskać szczegółowe informacje, zobacz [Serializacja pamięci podręcznej tokenów w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET sprawia, że pamięć podręczna tokenu jest zapieczętowana klasą, co usuwa możliwość jej rozszerzania. W związku z tym implementacja trwałości pamięci podręcznej tokenu musi mieć postać klasy pomocnika, która wchodzi w interakcję z zapieczętowanym pamięcią podręczną tokenów. Ta interakcja jest opisana w [te tematach Serializacja pamięci podręcznej tokenów w MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Oznaczanie wspólnego urzędu

Jeśli korzystasz z urzędu w wersji 1.0, zezwolisz użytkownikom na logowanie się przy użyciu dowolnego konta `https://login.microsoftonline.com/common` WAD (dla dowolnej organizacji). Zobacz [Walidacja urzędu w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Jeśli używasz urzędu w wersji 2.0, zezwolisz użytkownikom na logowanie się przy użyciu dowolnej organizacji usługi AAD lub konta `https://login.microsoftonline.com/common` osobistego Microsoft (MSA). W MSAL.NET, jeśli chcesz ograniczyć logowanie do dowolnego konta WAD (takie samo zachowanie jak w przypadku ADAL.NET), `https://login.microsoftonline.com/organizations` użyj . Aby uzyskać szczegółowe informacje, zobacz `authority` parametr w publicznej aplikacji [klienckiej](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>Tokeny w wersji 1.0 i 2.0

Istnieją dwie wersje tokenów:
- Tokeny w wersji 1.0
- Tokeny w wersji 2.0

Punkt końcowy w wersji 1.0 (używany przez ADAL) emituje tylko tokeny w wersji 1.0.

Jednak punkt końcowy w wersji 2.0 (używany przez usługę MSAL) emituje wersję tokenu akceptowaną przez internetowy interfejs API. Właściwość manifestu aplikacji internetowego interfejsu API umożliwia deweloperom wybranie, która wersja tokenu jest akceptowana. Zobacz `accessTokenAcceptedVersion` dokumentację [referencyjną manifestu](reference-app-manifest.md) aplikacji.

Aby uzyskać więcej informacji na temat tokenów w wersji 1.0 i 2.0, [zobacz Azure Active Directory tokeny dostępu](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy dla internetowego interfejsu API akceptujące tokeny w wersji 1.0

Uprawnienia OAuth2 to zakresy uprawnień, które aplikacja internetowego interfejsu API (zasobu) w wersji 1.0 uwidacznia aplikacjom klienckim. Te zakresy uprawnień mogą być udzielane aplikacjom klienckim podczas wyrażania zgody. Zobacz sekcję oauth2Permissions w [manifeście Azure Active Directory aplikacji](./reference-app-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do określonych uprawnień OAuth2 aplikacji w wersji 1.0

Jeśli chcesz uzyskać tokeny dla aplikacji akceptującej tokeny w wersji 1.0 (na przykład interfejs API usługi Microsoft Graph, czyli , należy utworzyć, łączyjąc żądany identyfikator zasobu z żądanym uprawnieniem OAuth2 dla tego https://graph.microsoft.com) `scopes` zasobu.

Aby na przykład uzyskać dostęp w imieniu użytkownika do internetowego interfejsu API w wersji 1.0, którego identyfikator URI identyfikatora aplikacji to , należy `ResourceId` użyć:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Jeśli chcesz odczytywać i zapisywać za pomocą MSAL.NET Azure Active Directory przy użyciu interfejsu API Microsoft Graph (), utwórz listę zakresów, jak w poniższym https://graph.microsoft.com/) fragmencie kodu:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Ostrzeżenie: jeśli w zakresie odpowiadającym internetowy interfejs API w wersji 1.0 powinien być jeden lub dwa ukośniki

Jeśli chcesz napisać zakres odpowiadający interfejsowi API Azure Resource Manager ( , zażądaj następującego zakresu (zwróć uwagę https://management.core.windows.net/) na dwa ukośniki).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Wynika to z tego, że Resource Manager API oczekuje ukośnika w oświadczeniach odbiorców ( ), a następnie istnieje ukośnik, który oddziela nazwę interfejsu `aud` API od zakresu.

Logika używana przez usługę Azure AD jest następująca:
- W przypadku punktu końcowego ADAL (wersja 1.0) z tokenem dostępu w wersji 1.0 (jedyny możliwy), aud=resource
- W przypadku usługi MSAL (punkt końcowy w wersji 2.0) z pytaniem o token dostępu dla zasobu akceptującego tokeny w wersji 2.0, aud=resource. Appid
- W przypadku usługi MSAL (punkt końcowy w wersji 2.0) z prośbą o token dostępu dla zasobu akceptującego token dostępu w wersji 1.0 (tak jak w powyższym przypadku) usługa Azure AD analizuje żądanych odbiorców z żądanego zakresu, biorąc wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu. W związku z tym jeśli https: /database.windows.net oczekuje odbiorców " ", należy zażądać zakresu \/ https://database.windows.net/ https: \/ /database.windows.net//.default. Zobacz również problem nr[747:](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)Ukośnik końcowego adresu URL zasobu został pominięty, co spowodowało błąd uwierzytelniania SQL #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do wszystkich uprawnień aplikacji w wersji 1.0

Jeśli na przykład chcesz uzyskać token dla wszystkich zakresów statycznych aplikacji w wersji 1.0, należy użyć

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Zakresy do żądania w przypadku przepływu poświadczeń klienta/aplikacji demona

W przypadku przepływu poświadczeń klienta zakresem do przekazania będzie również `/.default` . Ten zakres informuje usługę Azure AD: "wszystkie uprawnienia na poziomie aplikacji, na które administrator wyraził zgodę podczas rejestracji aplikacji.

## <a name="adal-to-msal-migration"></a>Migracja z ADAL do MSAL

W ADAL.NET wersji 2. X, tokeny odświeżania zostały ujawnione, co umożliwia opracowywanie rozwiązań na temat używania tych tokenów przez ich buforowanie i używanie metod dostarczanych przez `AcquireTokenByRefreshToken` ADAL 2.x.
Niektóre z tych rozwiązań były używane w scenariuszach, takich jak:
* Długotrwałe usługi, które robią akcje, w tym odświeżanie pulpitów nawigacyjnych w imieniu użytkowników, podczas gdy użytkownicy nie są już połączeni.
* Scenariusze webFarm umożliwiające klientowi doprowadzenie wersji RT do usługi sieci Web (buforowanie odbywa się po stronie klienta, zaszyfrowany plik cookie, a nie po stronie serwera)

MSAL.NET nie uwidacznia tokenów odświeżania ze względów bezpieczeństwa: usługa MSAL obsługuje odświeżanie tokenów za Ciebie.

Na szczęście MSAL.NET interfejs API, który umożliwia migrowanie poprzednich tokenów odświeżania (pozyskanych z ADAL) do `IConfidentialClientApplication` :

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

Za pomocą tej metody możesz podać wcześniej używany token odświeżania wraz z dowolnymi zakresami (zasobami), których potrzebujesz. Token odświeżania zostanie wymieniany na nowy i buforowany w aplikacji.

Ponieważ ta metoda jest przeznaczona dla scenariuszy, które nie są typowe, nie jest łatwo dostępna dla metody bez `IConfidentialClientApplication` uprzedniego rzutowania jej do `IByRefreshToken` metody .

Ten fragment kodu przedstawia kod migracji w poufnej aplikacji klienckiej. `GetCachedRefreshTokenForSignedInUser` pobiera token odświeżania, który był przechowywany w magazynie przez poprzednią wersję aplikacji, która korzysta z ADAL 2.x. `GetTokenCacheForSignedInUser` deerializuje pamięć podręczną zalogowanego użytkownika (poufne aplikacje klienckie powinny mieć jedną pamięć podręczną na użytkownika).

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

Token dostępu i token identyfikatora zostaną zwrócone w danych AuthenticationResult, gdy nowy token odświeżania będzie przechowywany w pamięci podręcznej.

Tej metody można również użyć w różnych scenariuszach integracji, w których jest dostępny token odświeżania.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat zakresów można znaleźć w [tematach Zakresy, uprawnienia i zgoda na platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
