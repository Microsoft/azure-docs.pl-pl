---
title: Migrowanie do MSAL.NET
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o różnicach między biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET) i biblioteką uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) oraz jak przeprowadzić migrację do usługi MSAL.NET.
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
ms.openlocfilehash: 64107c3f667dd7e59fcf6d191e83457029b3a277
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546350"
---
# <a name="migrating-applications-to-msalnet"></a>Migrowanie aplikacji do MSAL.NET

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET), jak i Biblioteka Azure AD Authentication Library for .NET (ADAL.NET) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD for Developers platform (v 1.0) do uwierzytelniania tożsamości usługi Azure AD (konta służbowe) przez żądanie tokenów przy użyciu biblioteki Azure AD Authentication Library (ADAL). Korzystanie z MSAL:

- można uwierzytelnić szerszy zbiór tożsamości firmy Microsoft (tożsamości usługi Azure AD i kont Microsoft, a także konta społecznościowe i lokalne za pośrednictwem Azure AD B2C), ponieważ korzysta on z platformy tożsamości firmy Microsoft.
- Użytkownicy będą korzystać z najlepszego środowiska logowania jednokrotnego.
- Aplikacja może włączyć przyrostową zgodę, a obsługa dostępu warunkowego jest łatwiejsza
- korzystasz z innowacji.

**MSAL.NET jest teraz zalecaną biblioteką uwierzytelniania do użycia z platformą tożsamości firmy Microsoft**. Żadne nowe funkcje nie zostaną zaimplementowane w systemie ADAL.NET. Wysiłki są skoncentrowane na ulepszaniu MSAL.

W tym artykule opisano różnice między biblioteką uwierzytelniania firmy Microsoft dla programu .NET (MSAL.NET) i biblioteką uwierzytelniania usługi Azure AD dla platformy .NET (ADAL.NET) i ułatwiają migrację do programu MSAL.

## <a name="differences-between-adal-and-msal-apps"></a>Różnice między aplikacjami ADAL i MSAL

W większości przypadków chcesz korzystać z MSAL.NET i platformy tożsamości firmy Microsoft, która jest najnowszą generacji bibliotek uwierzytelniania firmy Microsoft. Korzystając z MSAL.NET, uzyskujesz tokeny dla użytkowników logujących się do aplikacji za pomocą usługi Azure AD (konta służbowe), kont Microsoft (osobistych) lub Azure AD B2C.

Jeśli znasz już punkt końcowy usługi Azure AD dla deweloperów (i ADAL.NET), możesz chcieć zapoznać się z [informacjami na temat platformy tożsamości firmy Microsoft?](../azuread-dev/azure-ad-endpoint-comparison.md).

Jednak nadal musisz używać ADAL.NET, jeśli aplikacja musi zalogować się użytkowników przy użyciu wcześniejszych wersji [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services). Aby uzyskać więcej informacji, zobacz temat [Obsługa usług ADFS](https://aka.ms/msal-net-adfs-support).

Na poniższej ilustracji przedstawiono podsumowanie niektórych różnic między ADAL.NET i MSAL.NET ![ kod równoległy](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Pakiety i przestrzenie narzędzi NuGet

ADAL.NET jest używana z pakietu NuGet [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) . Przestrzeń nazw, która ma być używana, to `Microsoft.IdentityModel.Clients.ActiveDirectory` .

Aby użyć MSAL.NET, musisz dodać pakiet NuGet [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) i użyć `Microsoft.Identity.Client` przestrzeni nazw

### <a name="scopes-not-resources"></a>Zakresy nie są zasobami

ADAL.NET uzyskuje tokeny dla *zasobów*, ale MSAL.NET uzyskuje tokeny dla *zakresów*. Liczba zastąpień MSAL.NET AcquireToken wymaga parametru o nazwie Scopes ( `IEnumerable<string> scopes` ). Ten parametr jest prostą listą ciągów, które deklarują żądane uprawnienia i żądane zasoby. Dobrze znane zakresy są [zakresami Microsoft Graph](/graph/permissions-reference).

Możliwe jest również, że w MSAL.NET dostęp do zasobów w wersji 1.0. Zobacz szczegóły dotyczące [zakresów dla aplikacji w wersji 1.0](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Klasy podstawowe

- ADAL.NET używa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako reprezentacji połączenia z usługą tokenu zabezpieczającego (STS) lub serwerem autoryzacji za pośrednictwem urzędu. W przeciwieństwie do MSAL.NET jest przeznaczony dla [aplikacji klienckich](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Oferuje dwie osobne klasy: `PublicClientApplication` i `ConfidentialClientApplication`

- Uzyskiwanie tokenów: ADAL.NET i MSAL.NET mają takie same wywołania uwierzytelniania ( `AcquireTokenAsync` oraz `AcquireTokenSilentAsync` dla ADAL.NET, i `AcquireTokenInteractive` i `AcquireTokenSilent` w MSAL.NET), ale z innymi wymaganymi parametrami. Jedną z różnic jest fakt, że w MSAL.NET nie jest już konieczne przekazywanie `ClientID` aplikacji w każdym AcquireTokenXXym wywołaniu. Rzeczywiście, `ClientID` jest ustawiana tylko raz podczas kompilowania ( `IPublicClientApplication` lub `IConfidentialClientApplication` ).

### <a name="iaccount-not-iuser"></a>IAccount nie IUser

ADAL.NET manipulować użytkownikami. Jednak użytkownik jest agentem ludzkim lub oprogramowaniem, ale może być odpowiedzialny za co najmniej jedno konto w systemie tożsamości firmy Microsoft (kilka kont usługi Azure AD, Azure AD B2C, konta osobiste firmy Microsoft).

MSAL.NET 2. x teraz definiuje koncepcję konta (za pomocą interfejsu IAccount). Ta przerwana zmiana zapewnia właściwą semantykę: fakt, że ten sam użytkownik może mieć kilka kont w różnych katalogach usługi Azure AD. Ponadto MSAL.NET zapewnia lepsze informacje w scenariuszach gościa, ponieważ są udostępniane informacje o koncie głównym.

Aby uzyskać więcej informacji o różnicach między IUser i IAccount, zobacz [MSAL.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Wyjątki

#### <a name="interaction-required-exceptions"></a>Obsługa wyjątków wymaganych

MSAL.NET ma więcej jawnych wyjątków. Na przykład w przypadku niepowodzenia uwierzytelniania dyskretnego w bibliotece ADAL procedura ma przechwycić wyjątek i poszukać `user_interaction_required` kodu błędu:

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

Zobacz szczegóły w [zalecanym wzorcu, aby uzyskać token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) z ADAL.NET

Korzystając z MSAL.NET, możesz obchwycić `MsalUiRequiredException` zgodnie z opisem w [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Obsługa wyjątków wezwania na żądanie

W programie ADAL.NET wyjątki żądania roszczeń są obsługiwane w następujący sposób:

- `AdalClaimChallengeException` to wyjątek (pochodzący z `AdalServiceException` ) zgłoszony przez usługę w przypadku, gdy zasób wymaga większej liczby oświadczeń od użytkownika (na przykład uwierzytelnianie dwuskładnikowe). `Claims`Element członkowski zawiera fragment JSON z oświadczeniami, które są oczekiwane.
- Nadal w ADAL.NET, publiczna aplikacja kliencka, która otrzymuje ten wyjątek, musi wywołać `AcquireTokenInteractive` zastąpienie z parametrem oświadczenia. To przesłonięcie `AcquireTokenInteractive` nie nawet próbuje trafić pamięci podręcznej, ponieważ nie jest to konieczne. Przyczyną jest to, że token w pamięci podręcznej nie ma odpowiednich oświadczeń (w przeciwnym razie `AdalClaimChallengeException` nie zostało zgłoszone). W związku z tym nie ma potrzeby wyszukiwania w pamięci podręcznej. Należy pamiętać, że `ClaimChallengeException` może zostać odebrane w WebAPI OBO, a w przeciwnym razie `AcquireTokenInteractive` musi być wywoływana w publicznej aplikacji klienckiej wywołującej ten internetowy interfejs API.
- Aby uzyskać szczegółowe informacje, w tym przykłady obsługi [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

W programie MSAL.NET wyjątki żądania roszczeń są obsługiwane w następujący sposób:

- `Claims`Znajdują się one w `MsalServiceException` .
- Istnieje `.WithClaim(claims)` Metoda, która może być stosowana do `AcquireTokenInteractive` konstruktora.

### <a name="supported-grants"></a>Obsługiwane dotacje

Nie wszystkie dotacje są jeszcze obsługiwane w MSAL.NET i punkcie końcowym v 2.0. Poniżej znajduje się podsumowanie porównujące ADAL.NET i MSAL. Obsługiwane dotacje w sieci.

#### <a name="public-client-applications"></a>Publiczne aplikacje klienckie

Poniżej przedstawiono dotacje obsługiwane w ADAL.NET i MSAL.NET dla aplikacji klasycznych i mobilnych

Uprawnienie | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktywne | [Uwierzytelnianie interakcyjne](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktywny pozyskiwanie tokenów w usłudze MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Zintegrowane uwierzytelnianie systemu Windows | [Uwierzytelnianie zintegrowane w systemie Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication)
Nazwa użytkownika/hasło | [Uzyskiwanie tokenów przy użyciu nazwy użytkownika i hasła](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Uwierzytelnianie hasła użytkownika](msal-authentication-flows.md#usernamepassword)
Przepływ kodu urządzenia | [Profil urządzenia dla urządzeń bez przeglądarek sieci Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Poufne aplikacje klienckie

Poniżej przedstawiono dotacje obsługiwane w ADAL.NET i MSAL.NET dla aplikacji sieci Web, interfejsów API sieci Web i aplikacji demonów:

Typ aplikacji | Uprawnienie | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Aplikacja internetowa, internetowy interfejs API, Demon | Poświadczenia klienta | [Przepływy poświadczeń klienta w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Przepływy poświadczeń klienta w MSAL.NET](msal-authentication-flows.md#client-credentials)
Interfejs API sieci Web | W imieniu | [Wywołania usługi do obsługi w imieniu użytkownika z ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [W imieniu programu MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Aplikacja internetowa | Kod uwierzytelniania | [Uzyskiwanie tokenów z kodami autoryzacji w aplikacjach sieci Web za pomocą ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Uzyskiwanie tokenów z kodami autoryzacji w aplikacjach sieci Web za pomocą MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Trwałość pamięci podręcznej

ADAL.NET umożliwia zwiększenie `TokenCache` klasy w celu zaimplementowania odpowiedniej funkcjonalności trwałości na platformach bez bezpiecznego magazynu (.NET Framework i .NET Core) przy użyciu `BeforeAccess` `BeforeWrite` metod i. Aby uzyskać szczegółowe informacje, zobacz [Serializacja pamięci podręcznej tokenów w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET sprawia, że token buforuje klasę zapieczętowana, usuwając możliwość jej rozszerania. W związku z tym implementacja trwałości pamięci podręcznej tokenów musi mieć postać klasy pomocnika, która współdziała z pamięcią podręczną zapieczętowanych tokenów. Ta interakcja jest opisana w temacie [Serializacja pamięci podręcznej tokenów w MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signification wspólnego urzędu

W wersji 1.0, jeśli używasz `https://login.microsoftonline.com/common` urzędu, zezwolisz użytkownikom na logowanie się przy użyciu dowolnego konta usługi AAD (dla dowolnej organizacji). Zobacz [weryfikacja urzędu w ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Jeśli używasz `https://login.microsoftonline.com/common` urzędu w wersji 2.0, zezwolisz użytkownikom na logowanie się za pomocą dowolnej organizacji usługi AAD lub konta Microsoft Personal (MSA). W MSAL.NET, jeśli chcesz ograniczyć logowanie do dowolnego konta usługi AAD (takie samo zachowanie jak w przypadku usługi ADAL.NET), użyj `https://login.microsoftonline.com/organizations` . Aby uzyskać szczegółowe informacje, zobacz `authority` parametr w [publicznej aplikacji klienckiej](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokeny w wersji 1.0 i 2.0

Istnieją dwie wersje tokenów:
- tokeny v 1.0
- tokeny v 2.0

Punkt końcowy v 1.0 (używany przez ADAL) emituje tylko tokeny w wersji 1.0.

Jednak punkt końcowy v 2.0 (używany przez MSAL) emituje wersję tokenu, który akceptuje interfejs API sieci Web. Właściwość manifestu aplikacji internetowego interfejsu API pozwala deweloperom wybrać, która wersja tokenu jest akceptowana. Zapoznaj `accessTokenAcceptedVersion` się z dokumentacją dotyczącą [manifestu aplikacji](reference-app-manifest.md) .

Aby uzyskać więcej informacji na temat tokenów v 1.0 i v 2.0, zobacz [Azure Active Directory tokeny dostępu](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Zakresy dla internetowego interfejsu API akceptujące tokeny v 1.0

Uprawnienia OAuth2 są zakresami uprawnień, które aplikacja interfejsu API sieci Web w wersji 1.0 uwidacznia aplikacje klienckie. Te zakresy uprawnień mogą być udzielane aplikacjom klienckim podczas wyrażania zgody. Zapoznaj się z sekcją oauth2Permissions w [manifeście aplikacji Azure Active Directory](./reference-app-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do określonych uprawnień OAuth2 aplikacji v 1.0

Jeśli chcesz uzyskać tokeny dla aplikacji akceptującej tokeny w wersji 1.0 (na przykład interfejsu API Microsoft Graph, co jest https://graph.microsoft.com) konieczne, należy utworzyć `scopes` przez połączenie żądanego identyfikatora zasobu z żądanym uprawnieniem OAuth2 dla tego zasobu.

Aby na przykład uzyskać dostęp do nazwy użytkownika interfejsu API sieci Web w wersji 1.0, który ma identyfikator URI aplikacji `ResourceId` , należy użyć:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Jeśli chcesz odczytywać i zapisywać MSAL.NET Azure Active Directory przy użyciu interfejsu API Microsoft Graph ( https://graph.microsoft.com/) można utworzyć listę zakresów, jak w poniższym fragmencie kodu:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Ostrzeżenie: należy mieć jeden lub dwa ukośniki w zakresie odpowiadającym interfejsowi API sieci Web w wersji 1.0

Jeśli chcesz napisać zakres odpowiadający interfejsowi API Azure Resource Manager ( https://management.core.windows.net/) , zażądaj następującego zakresu (należy zauważyć dwa ukośniki).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Wynika to z faktu, że interfejs API Menedżer zasobów oczekuje ukośnika w swoim roście odbiorców ( `aud` ), a następnie jest ukośnik, aby oddzielić nazwę interfejsu API od zakresu.

Logika używana przez usługę Azure AD jest następująca:
- Dla punktu końcowego ADAL (v 1.0) z tokenem dostępu w wersji 1.0 (możliwe), AUD = zasób
- W przypadku usługi MSAL (punkt końcowy v 2.0) żądanie tokenu dostępu dla zasobu akceptującego tokeny v 2.0, AUD = Resource. AppId
- W przypadku usługi MSAL (punkt końcowy v 2.0) z prośbą o token dostępu dla zasobu akceptującego token dostępu w wersji 1.0 (w tym przypadku powyżej) usługa Azure AD analizuje żądanych odbiorców od żądanego zakresu, pobierając wszystko przed ostatnim ukośnikiem i używając go jako identyfikatora zasobu. W związku z tym jeśli https: \/ /Database.Windows.NET oczekuje odbiorców " https://database.windows.net/ ", musisz zażądać zakresu protokołu https: \/ /Database.Windows.NET//.default. Zobacz również artykuł problem #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): ukośnik końcowy adresu URL zasobu został pominięty, co spowodowało niepowodzenie uwierzytelniania SQL #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Zakresy żądania dostępu do wszystkich uprawnień aplikacji w wersji 1.0

Na przykład jeśli chcesz uzyskać token dla wszystkich zakresów statycznych aplikacji w wersji 1.0, należy użyć jednej z nich

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Zakresy do żądania w przypadku aplikacji przepływ poświadczeń klienta/demon

W przypadku przepływu poświadczeń klienta należy również przekazać zakres `/.default` . Ten zakres zawiera informacje o usłudze Azure AD: "wszystkie uprawnienia na poziomie aplikacji, które administrator wyraził zgodę na rejestrację aplikacji.

## <a name="adal-to-msal-migration"></a>Migracja biblioteki ADAL do MSAL

W ADAL.NET V2. X tokeny odświeżania były ujawniane, co umożliwia tworzenie rozwiązań dotyczących używania tych tokenów przez buforowanie ich i korzystanie z `AcquireTokenByRefreshToken` metod dostarczonych przez ADAL 2. x.
Niektóre z tych rozwiązań były używane w scenariuszach takich jak:
* Długotrwałe usługi, które wykonują akcje, w tym odświeżanie pulpitów nawigacyjnych w imieniu użytkowników, którzy nie są już połączeni z użytkownikami.
* Scenariusze dotyczące usługi Webfarm na potrzeby umożliwienia klientowi przełączenia RT z usługą sieci Web (buforowanie odbywa się po stronie klienta, zaszyfrowanego pliku cookie, a nie po stronie serwera)

MSAL.NET nie uwidacznia tokenów odświeżania ze względów bezpieczeństwa: MSAL obsługuje odświeżanie tokenów.

Na szczęście MSAL.NET ma teraz interfejs API, który umożliwia migrowanie poprzednich tokenów odświeżania (uzyskanych z biblioteki ADAL) do programu `IConfidentialClientApplication` :

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

Za pomocą tej metody można podać poprzednio używany token odświeżania wraz z dowolnymi żądanymi zakresami (zasobami). Token odświeżania zostanie nadany nowemu serwerowi i zapisany w pamięci podręcznej w aplikacji.

Ponieważ ta metoda jest przeznaczona dla scenariuszy, które nie są typowe, nie jest łatwo dostępna przy użyciu `IConfidentialClientApplication` bez uprzedniego rzutowania na `IByRefreshToken` .

Ten fragment kodu przedstawia kod migracji w poufnej aplikacji klienckiej. `GetCachedRefreshTokenForSignedInUser` Pobierz token odświeżania, który był przechowywany w niektórych magazynach przez poprzednią wersję aplikacji, która była używana do korzystania z biblioteki ADAL 2. x. `GetTokenCacheForSignedInUser` deserializacji pamięci podręcznej zalogowanego użytkownika (jako poufne aplikacje klienckie powinny mieć jedną pamięć podręczną na użytkownika).

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

Zostanie wyświetlony token dostępu i token identyfikatora zwrócony w AuthenticationResult, gdy nowy token odświeżania jest przechowywany w pamięci podręcznej.

Tej metody można również użyć w różnych scenariuszach integracji, w których dostępny jest token odświeżania.

## <a name="next-steps"></a>Następne kroki

Więcej informacji o zakresach w [zakresach, uprawnieniach i wyrażaniu zgody można znaleźć na platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
