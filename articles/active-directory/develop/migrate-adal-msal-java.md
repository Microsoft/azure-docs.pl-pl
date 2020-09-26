---
title: Przewodnik migracji biblioteki ADAL do MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji Java biblioteki Azure Active Directory Authentication Library (ADAL) do biblioteki uwierzytelniania firmy Microsoft (MSAL).
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
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 1cc94519525d5f87a14769b0109e425171265c6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258323"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Przewodnik migracji biblioteki ADAL do MSAL dla języka Java

W tym artykule przedstawiono zmiany, które należy wykonać w celu przeprowadzenia migracji aplikacji, która Azure Active Directory używa biblioteki MSAL Authentication Library (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft.

Zarówno Biblioteka uwierzytelniania firmy Microsoft dla języka Java (MSAL4J), jak i Biblioteka Azure AD Authentication Library for Java (ADAL4J) są używane do uwierzytelniania jednostek usługi Azure AD i żądania tokenów z usługi Azure AD. Do tej pory większość deweloperów pracowała z usługą Azure AD for Developers platform (v 1.0) do uwierzytelniania tożsamości usługi Azure AD (konta służbowe) przez żądanie tokenów przy użyciu biblioteki Azure AD Authentication Library (ADAL).

MSAL oferuje następujące korzyści:

- Ponieważ używa ona nowszego punktu końcowego platformy tożsamości firmy Microsoft, można uwierzytelnić szerszy zestaw tożsamości firmy Microsoft, takich jak tożsamości usługi Azure AD, konta Microsoft i konta społecznościowe i lokalne za pośrednictwem usługi Azure AD Business to Consumer (B2C).
- Użytkownicy będą korzystać z najlepszego środowiska logowania jednokrotnego.
- Aplikacja może włączyć przyrostową zgodę i ułatwić dostęp warunkowy.

MSAL for Java jest biblioteką uwierzytelniania zalecaną do użycia z platformą tożsamości firmy Microsoft. Żadne nowe funkcje nie zostaną zaimplementowane w systemie ADAL4J. Wszystkie wysiłki w przód są skoncentrowane na ulepszaniu MSAL.

## <a name="differences"></a>Różnice

Jeśli pracujesz z punktem końcowym usługi Azure AD dla deweloperów (i ADAL4J), warto zapoznać się z [informacjami o tym, co się dzieje w punkcie końcowym platformy Identity platform (v 2.0)?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="scopes-not-resources"></a>Zakresy nie są zasobami

ADAL4J uzyskuje tokeny dla zasobów, a MSAL for Java uzyskuje tokeny dla zakresów. Liczba MSAL dla klas języka Java wymaga parametru Scopes. Ten parametr jest listą ciągów, które deklarują żądane uprawnienia i żądane zasoby. Zobacz [zakresy Microsoft Graph](/graph/permissions-reference) , aby zobaczyć przykładowe zakresy.

Można dodać `/.default` sufiks zakresu do zasobu, aby ułatwić migrację aplikacji z punktu końcowego v 1.0 (ADAL) do punktu końcowego platformy tożsamości firmy Microsoft (MSAL). Na przykład dla wartości zasobu wartość `https://graph.microsoft.com` równoważna wartość zakresu to `https://graph.microsoft.com/.default` .  Jeśli zasób nie jest w formie adresu URL, ale identyfikator zasobu formularza `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , można nadal używać wartości zakresu jako `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Aby uzyskać więcej informacji na temat różnych typów zakresów, zapoznaj się z [uprawnieniami i wyrażaniem zgody na platformie tożsamości firmy Microsoft](./v2-permissions-and-consent.md) oraz [zakresami dla internetowego interfejsu API akceptujących artykuły tokenów w wersji 1.0](./msal-v1-app-scopes.md) .

## <a name="core-classes"></a>Klasy podstawowe

W ADAL4J `AuthenticationContext` Klasa reprezentuje połączenie z usługą tokenu zabezpieczającego (STS) lub serwerem autoryzacji za pomocą urzędu. MSAL for Java jest jednak przeznaczony dla aplikacji klienckich. Oferuje dwie osobne klasy: `PublicClientApplication` i `ConfidentialClientApplication` do reprezentowania aplikacji klienckich.  Drugie, `ConfidentialClientApplication` reprezentuje aplikację, która została zaprojektowana w celu bezpiecznego utrzymania hasła, takiego jak identyfikator aplikacji dla aplikacji demona.

W poniższej tabeli przedstawiono, jak funkcje ADAL4J są mapowane na nowe MSAL dla funkcji języka Java:

| ADAL4J, Metoda| MSAL4J, Metoda|
|------|-------|
|acquireToken (zasób ciągu, obiekt ClientCredential, AuthenticationCallback wywołanie zwrotne) | acquireToken(ClientCredentialParameters)|
|acquireToken (zasób ciągu, potwierdzenie ClientAssertion, AuthenticationCallback wywołania zwrotne)|acquireToken(ClientCredentialParameters)|
|acquireToken (zasób ciągu, poświadczenia AsymmetricKeyCredential, wywołanie zwrotne AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken (ciąg zasobu, ciąg clientId, nazwa użytkownika ciągu, hasło ciągu, wywołanie zwrotne AuthenticationCallback)| acquireToken(UsernamePasswordParameters)|
|acquireToken (ciąg Resource, String clientId, String username, String Password = null, AuthenticationCallback callback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (ciąg zasobu, UserAssertion userAssertion, obiekt ClientCredential Credential, AuthenticationCallback wywołania zwrotnego)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () i acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount zamiast IUser

ADAL4J manipulować użytkownikami. Chociaż użytkownik reprezentuje jednego agenta człowieka lub oprogramowania, może mieć co najmniej jedno konto w systemie tożsamości firmy Microsoft. Na przykład użytkownik może mieć kilka kont usługi Azure AD, Azure AD B2C lub Microsoft Personal.

MSAL for Java definiuje koncepcję konta za pośrednictwem `IAccount` interfejsu. Jest to istotna zmiana z ADAL4J, ale jest to dobre, ponieważ przechwytuje fakt, że ten sam użytkownik może mieć kilka kont, a nawet w różnych katalogach usługi Azure AD. Program MSAL for Java oferuje lepsze informacje w scenariuszach gościa, ponieważ są udostępniane informacje o koncie głównym.

## <a name="cache-persistence"></a>Trwałość pamięci podręcznej

ADAL4J nie obsługiwał pamięci podręcznej tokenów.
MSAL for Java dodaje [pamięć podręczną tokenów](msal-acquire-cache-tokens.md) w celu uproszczenia zarządzania okresami istnienia tokenu przez automatyczne odświeżenie wygasłych tokenów, gdy jest to możliwe, i uniemożliwianie niepotrzebnych wyświetleń przez użytkownika poświadczeń, gdy

## <a name="common-authority"></a>Wspólny urząd

W wersji 1.0, jeśli używasz `https://login.microsoftonline.com/common` urzędu, użytkownicy mogą logować się przy użyciu dowolnego konta usługi Azure Active Directory (AAD) (dla dowolnej organizacji).

Jeśli używasz `https://login.microsoftonline.com/common` urzędu w wersji 2.0, użytkownicy mogą logować się przy użyciu dowolnej organizacji usługi AAD, a nawet konta osobistego firmy Microsoft (MSA). W programie MSAL for Java, jeśli chcesz ograniczyć logowanie do dowolnego konta usługi AAD, musisz użyć `https://login.microsoftonline.com/organizations` urzędu (to samo zachowanie jest takie samo jak w przypadku programu ADAL4J). Aby określić urząd, należy ustawić `authority` parametr w metodzie [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) podczas tworzenia `PublicClientApplication` klasy.

## <a name="v10-and-v20-tokens"></a>tokeny w wersji 1.0 i 2.0

Punkt końcowy v 1.0 (używany przez ADAL) emituje tylko tokeny w wersji 1.0.

Punkt końcowy v 2.0 (używany przez MSAL) może emitować tokeny v 1.0 i v 2.0. Właściwość manifestu aplikacji internetowego interfejsu API pozwala deweloperom wybrać, która wersja tokenu jest akceptowana. Zapoznaj `accessTokenAcceptedVersion` się z dokumentacją dotyczącą [manifestu aplikacji](./reference-app-manifest.md) .

Aby uzyskać więcej informacji na temat tokenów v 1.0 i v 2.0, zobacz [Azure Active Directory tokeny dostępu](./access-tokens.md).

## <a name="adal-to-msal-migration"></a>Migracja biblioteki ADAL do MSAL

W programie ADAL4J tokeny odświeżania były ujawniane — które umożliwiają deweloperom ich buforowanie. Następnie będą one używane `AcquireTokenByRefreshToken()` do włączania rozwiązań, takich jak implementacja długotrwałych usług, które odświeżają pulpity nawigacyjne w imieniu użytkownika, gdy użytkownik nie jest już połączony.

MSAL for Java nie ujawnia tokenów odświeżania ze względów bezpieczeństwa. Zamiast tego MSAL obsługuje odświeżanie tokenów.

Program MSAL for Java ma interfejs API, który umożliwia migrowanie tokenów odświeżania uzyskanych z ADAL4j do ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Za pomocą tej metody można podać poprzednio używany token odświeżania wraz z dowolnymi żądanymi zakresami (zasobami). Token odświeżania zostanie nadany nowemu serwerowi w pamięci podręcznej do użycia przez aplikację.

Poniższy fragment kodu przedstawia kod migracji w poufnej aplikacji klienckiej:

```java
String rt = GetCachedRefreshTokenForSignedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`Zwraca token dostępu i token identyfikatora, podczas gdy nowy token odświeżania jest przechowywany w pamięci podręcznej.
Aplikacja będzie również zawierać IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Aby użyć tokenów, które znajdują się teraz w pamięci podręcznej, wywołaj:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
