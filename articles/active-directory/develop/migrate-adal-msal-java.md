---
title: Przewodnik migracji ADAL do MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji Java z biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) do biblioteki uwierzytelniania Microsoft (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7ba845e79074313f0ccf2c066ba016bd72d46efe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534571"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Przewodnik po migracji ADAL do MSAL dla języka Java

W tym artykule przedstawiono zmiany, które należy wprowadzić w celu migracji aplikacji korzystającej z biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft (MSAL).

Biblioteka uwierzytelniania firmy Microsoft dla języka Java (MSAL4J) i biblioteka uwierzytelniania usługi Azure AD dla języka Java (ADAL4J) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z platformą Azure AD for developers (w wersji 1.0) w celu uwierzytelnienia tożsamości usługi Azure AD (kont służbowych i szkolnych), żądając tokenów przy użyciu biblioteki uwierzytelniania usługi Azure AD (ADAL).

MSAL oferuje następujące korzyści:

- Ponieważ używa nowszego punktu końcowego platformy tożsamości firmy Microsoft, można uwierzytelnić szerszy zestaw tożsamości firmy Microsoft, takich jak tożsamości usługi Azure AD, konta Microsoft i kont społecznościowych i lokalnych za pośrednictwem usługi Azure AD Business do konsumenta (B2C).
- Użytkownicy otrzymają najlepsze środowisko rejestracji jednokrotnej.
- Aplikacja może włączyć przyrostową zgodę, a obsługa dostępu warunkowego jest łatwiejsza.

MSAL dla Języka Java to biblioteka urywek, z której zalecamy korzystanie z platformy tożsamości firmy Microsoft. Żadne nowe funkcje nie zostaną zaimplementowane na ADAL4J. Wszystkie wysiłki w przyszłości koncentrują się na poprawie MSAL.

## <a name="differences"></a>Różnice

Jeśli pracujesz z usługą Azure AD dla deweloperów (w wersji 1.0) punkt końcowy (i ADAL4J), można przeczytać Co różni się od punktu końcowego platformy tożsamości firmy Microsoft (w wersji 2.0).If you have been working with the Azure AD for developers (v1.0) endpoint (and ADAL4J), you have want to read [What's different about the Microsoft identity platform (v2.0) endpoint?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Zakresy nie zasoby

ADAL4J nabywa tokeny dla zasobów, podczas gdy msal dla języka Java uzyskuje tokeny dla zakresów. Liczba klas MSAL dla języka Java wymaga parametru zakresów. Ten parametr jest listą ciągów, które deklarują żądane uprawnienia i zasoby, które są wymagane. Zobacz [zakresy programu Microsoft Graph,](https://docs.microsoft.com/graph/permissions-reference) aby zobaczyć przykładowe zakresy.

## <a name="core-classes"></a>Podstawowe klasy

W ADAL4J `AuthenticationContext` klasa reprezentuje połączenie z usługą tokenu zabezpieczającego (STS) lub serwerem autoryzacji za pośrednictwem urzędu. Jednak MSAL dla języka Java jest przeznaczony wokół aplikacji klienckich. Udostępnia dwie oddzielne `PublicClientApplication` klasy: i `ConfidentialClientApplication` do reprezentowania aplikacji klienckich.  Ten ostatni `ConfidentialClientApplication`, reprezentuje aplikację, która jest przeznaczona do bezpiecznego zachowania klucza tajnego, takiego jak identyfikator aplikacji dla aplikacji demona.

W poniższej tabeli pokazano, jak funkcje ADAL4J są mapowane do nowych funkcji MSAL dla języka Java:

| Metoda ADAL4J| Metoda MSAL4J|
|------|-------|
|acquireToken(Zasób ciągu, Poświadczenie z funkcją klienta, wywołanie zwrotne AuthenticationCallback) | acquireToken(ClientCredentialParameters)|
|acquireToken(Zasób ciągu, Twierdzenie klientaAssertion, Wywołanie zwrotne AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(Zasób ciąg, Asymetryczne poświadczenia klawiszowe, wywołanie zwrotne AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(Składnik zasobu, Identyfikator klienta ciągu, nazwa użytkownika ciągu, hasło ciągu, wywołanie zwrotne AuthenticationCallback)| acquireToken(Nazwa UżytkownikaPasswordParameters)|
|acquireToken(Składnik zasobu, Identyfikator klienta ciągu, nazwa użytkownika ciągu, hasło ciągu=wartość null, wywołanie zwrotne AuthenticationCallback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(Zasób ciągu, UserAssertion userAssertion, Poświadczenie z funkcją klienta, wywołanie zwrotne AuthenticationCallback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() i acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently (SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount zamiast IUser

ADAL4J manipulował użytkownikami. Mimo że użytkownik reprezentuje jednego agenta ludzkiego lub oprogramowania, może mieć jedno lub więcej kont w systemie tożsamości firmy Microsoft. Na przykład użytkownik może mieć kilka kont osobistych usługi Azure AD, Azure AD B2C lub Microsoft.

MSAL dla języka Java definiuje pojęcie `IAccount` Konta za pośrednictwem interfejsu. Jest to przełomowa zmiana z ADAL4J, ale jest dobra, ponieważ przechwytuje fakt, że ten sam użytkownik może mieć kilka kont, a może nawet w różnych katalogach usługi Azure AD. Usługa MSAL dla języka Java zapewnia lepsze informacje w scenariuszach gościa, ponieważ informacje o koncie domowym są dostarczane.

## <a name="cache-persistence"></a>Trwałość pamięci podręcznej

ADAL4J nie ma obsługi pamięci podręcznej tokenów.
MSAL dla języka Java dodaje [pamięć podręczną tokenu,](msal-acquire-cache-tokens.md) aby uprościć zarządzanie okresami istnienia tokenu, automatycznie odświeżając wygasłe tokeny, gdy jest to możliwe, i zapobiegając niepotrzebnym monitom dla użytkownika o podanie poświadczeń, gdy jest to możliwe.

## <a name="common-authority"></a>Wspólny organ

W wersji 1.0, `https://login.microsoftonline.com/common` jeśli używasz urzędu, użytkownicy mogą logować się przy użyciu dowolnego konta usługi Azure Active Directory (AAD) (dla dowolnej organizacji).

Jeśli używasz `https://login.microsoftonline.com/common` urzędu w wersji 2.0, użytkownicy mogą logować się za pomocą dowolnej organizacji AAD, a nawet konta osobistego Microsoft (MSA). W msal dla języka Java, jeśli chcesz ograniczyć logowanie do `https://login.microsoftonline.com/organizations` dowolnego konta AAD, musisz użyć urzędu (co jest takie samo zachowanie jak w przypadku ADAL4J). Aby określić urząd, `authority` należy ustawić parametr w [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) metody podczas tworzenia `PublicClientApplication` klasy.

## <a name="v10-and-v20-tokens"></a>żetony v1.0 i v2.0

Punkt końcowy w wersji 1.0 (używany przez ADAL) emituje tylko tokeny w wersji 1.0.

Punkt końcowy w wersji 2.0 (używany przez MSAL) może emitować tokeny w wersji 1.0 i v2.0. Właściwość manifestu aplikacji interfejsu API sieci web umożliwia deweloperom wybrać, która wersja tokenu jest akceptowana. Zobacz `accessTokenAcceptedVersion` w dokumentacji referencyjnej [manifestu aplikacji.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

Aby uzyskać więcej informacji na temat tokenów w wersji 1.0 i wersji 2.0, zobacz [tokeny dostępu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>Migracja ADAL do MSAL

W ADAL4J tokeny odświeżania zostały ujawnione — co pozwoliło deweloperom na ich buforowanie. Następnie będą `AcquireTokenByRefreshToken()` one używane do włączania rozwiązań, takich jak implementowanie długotrwałych usług, które odświeżają pulpity nawigacyjne w imieniu użytkownika, gdy użytkownik nie jest już połączony.

MSAL dla języka Java nie udostępnia tokenów odświeżania ze względów bezpieczeństwa. Zamiast tego MSAL obsługuje odświeżanie tokenów dla Ciebie.

MSAL dla języka Java ma interfejs API, który umożliwia migrację tokenów odświeżania nabytych za pomocą ADAL4j do ClientApplication: [acquireToken(RefreshTokenParameters).](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-) Za pomocą tej metody można podać poprzednio używany token odświeżania wraz z dowolnymi zakresami (zasobami), które chcesz. Token odświeżania zostanie wymieniony na nowy i buforowany do użycia przez aplikację.

Poniższy fragment kodu pokazuje kod migracji w poufnej aplikacji klienckiej:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Zwraca `IAuthenticationResult` token dostępu i token identyfikatora, podczas gdy nowy token odświeżania jest przechowywany w pamięci podręcznej.
Aplikacja będzie również zawierać konto IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Aby użyć tokenów, które są teraz w pamięci podręcznej, wywołaj:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
