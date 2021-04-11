---
title: Przepływ poświadczeń klienta OAuth 2,0 na platformie tożsamości firmy Microsoft | Azure
description: Twórz aplikacje sieci Web przy użyciu implementacji platformy tożsamości firmy Microsoft w ramach protokołu uwierzytelniania OAuth 2,0.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 4/1/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8b3e27f266551eb7425d4801d14261a5e428e4c2
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168114"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity platform i przepływ poświadczeń klienta OAuth 2,0

W celu uzyskania dostępu do zasobów hostowanych przez sieć Web przy użyciu tożsamości aplikacji można użyć przyznanych [poświadczeń klienta oauth 2,0](https://tools.ietf.org/html/rfc6749#section-4.4) *, które* określono w dokumencie RFC 6749. Ten typ uprawnień jest często używany do interakcji między serwerami, które muszą działać w tle bez natychmiastowej interakcji z użytkownikiem. Te typy aplikacji są często określane jako *demony* lub *konta usług*.

W tym artykule opisano, jak programować bezpośrednio w odniesieniu do protokołu w aplikacji. Jeśli to możliwe, zalecamy korzystanie z obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast [uzyskiwać tokeny i wywoływać zabezpieczone interfejsy API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z MSAL](sample-v2-code.md).

Przepływ uprawnień uwierzytelniania OAuth 2,0 zezwala usłudze sieci Web (poufnego klienta) na używanie własnych poświadczeń, a nie personifikowanie użytkownika w celu uwierzytelniania podczas wywoływania innej usługi sieci Web. W przypadku wyższego poziomu gwarancji platforma tożsamości firmy Microsoft umożliwia usłudze wywołującej używanie certyfikatu (zamiast wspólnego klucza tajnego) jako poświadczenia.  Ponieważ używane są poświadczenia własne aplikacji, te poświadczenia muszą być przechowywane w bezpieczny — _nigdy nie_ należy publikować tego poświadczenia w kodzie źródłowym, osadzać go na stronach sieci Web ani używać go w powszechnie rozproszonej aplikacji natywnej. 

W przepływie poświadczeń klienta uprawnienia są udzielane bezpośrednio do aplikacji przez administratora. Gdy aplikacja przedstawia token dla zasobu, wymusza, że sama aplikacja ma autoryzację do wykonania akcji, ponieważ nie ma żadnego użytkownika w uwierzytelnianiu.  W tym artykule opisano kroki wymagane do [autoryzowania aplikacji do wywoływania interfejsu API](#application-permissions), a także [metody uzyskiwania tokenów niezbędnych do wywołania tego interfejsu API](#get-a-token).

## <a name="protocol-diagram"></a>Diagram protokołu

Cały przepływ poświadczeń klienta wygląda podobnie do poniższego diagramu. Opisujemy każdy z kroków opisanych w dalszej części tego artykułu.

![Diagram przedstawiający przepływ poświadczeń klienta](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Uzyskiwanie autoryzacji bezpośredniej

Aplikacja zwykle otrzymuje bezpośrednią autoryzację w celu uzyskania dostępu do zasobu na jeden z dwóch sposobów:

* [Za pomocą listy kontroli dostępu (ACL) w zasobie](#access-control-lists)
* [Przypisywanie uprawnień aplikacji w usłudze Azure AD](#application-permissions)

Te dwie metody są najczęściej używane w usłudze Azure AD, a firma Microsoft zaleca ich klientom i zasobom, które wykonują przepływ poświadczeń klienta. Zasób może również zdecydować się na autoryzowanie swoich klientów w inny sposób. Każdy serwer zasobów może wybrać metodę, która jest najbardziej zrozumiała dla swojej aplikacji.

### <a name="access-control-lists"></a>Listy kontroli dostępu

Dostawca zasobów może wymusić kontrolę autoryzacji na podstawie listy identyfikatorów aplikacji (klienta), których zna, i przyznaje określony poziom dostępu do. Gdy zasób otrzymuje token z platformy tożsamości firmy Microsoft, może zdekodować token i wyodrębnić identyfikator aplikacji klienta z `appid` `iss` oświadczeń i. Następnie porównuje aplikację z listą kontroli dostępu (ACL), którą obsługuje. Stopień szczegółowości i Metoda listy ACL mogą się znacznie różnić w zależności od zasobów.

Typowym przypadkiem użycia jest użycie listy ACL do uruchamiania testów dla aplikacji sieci Web lub interfejsu API sieci Web. Internetowy interfejs API może udzielić tylko podzestawu pełnych uprawnień do określonego klienta. Aby przeprowadzić kompleksowe testy na interfejsie API, należy utworzyć klienta testowego, który uzyskuje tokeny z platformy tożsamości firmy Microsoft, a następnie wysyła je do interfejsu API. Następnie interfejs API sprawdza listę kontroli dostępu dla identyfikatora aplikacji klienta testowego, aby uzyskać pełny dostęp do całej funkcjonalności interfejsu API. Jeśli używasz tego rodzaju listy ACL, pamiętaj, aby sprawdzić, czy nie tylko wartość elementu wywołującego, `appid` ale również sprawdzić, czy `iss` wartość tokenu jest zaufana.

Ten typ autoryzacji jest typowy dla demonów i kont usług, które muszą uzyskiwać dostęp do danych należących do użytkowników indywidualnych, którzy mają osobiste konta Microsoft. W przypadku danych należących do organizacji zalecamy uzyskanie niezbędnej autoryzacji za pomocą uprawnień aplikacji.

#### <a name="controlling-tokens-without-the-roles-claim"></a>Kontrolowanie tokenów bez `roles` roszczeń

Aby można było włączyć ten wzorzec autoryzacji na podstawie listy ACL, usługa Azure AD nie wymaga, aby aplikacje były autoryzowane do uzyskiwania tokenów dla innej aplikacji. W ten sposób tokeny tylko dla aplikacji mogą być wystawiane bez `roles` żądania. Aplikacje uwidaczniające interfejsy API muszą implementować testy uprawnień w celu akceptowania tokenów.

Jeśli chcesz uniemożliwić aplikacjom uzyskiwanie tokenów dostępu z dostępem do aplikacji bez ról, upewnij się, [że dla aplikacji są włączone wymagania dotyczące przypisywania użytkowników](../manage-apps/assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Spowoduje to zablokowanie użytkowników i aplikacji bez przypisywania ról w celu uzyskania tokenu dla tej aplikacji. 

### <a name="application-permissions"></a>Uprawnienia aplikacji

Zamiast używać list ACL, można użyć interfejsów API, aby uwidocznić zestaw **uprawnień aplikacji**. Uprawnienie aplikacji jest przyznawane aplikacji przez administratora organizacji i może być używane tylko w celu uzyskania dostępu do danych należących do tej organizacji i jej pracowników. Na przykład Microsoft Graph uwidacznia kilka uprawnień aplikacji, aby wykonać następujące czynności:

* Odczytaj pocztę we wszystkich skrzynkach pocztowych
* Odczytuj i zapisuj wiadomości e-mail we wszystkich skrzynkach pocztowych
* Wyślij wiadomość e-mail jako dowolny użytkownik
* Odczyt danych katalogu

Aby korzystać z uprawnień aplikacji przy użyciu własnego interfejsu API (w przeciwieństwie do Microsoft Graph), należy najpierw [uwidocznić interfejs API](quickstart-configure-app-expose-web-apis.md) przez definiowanie zakresów w rejestracji aplikacji interfejsu API w Azure Portal. Następnie [Skonfiguruj dostęp do interfejsu API](quickstart-configure-app-access-web-apis.md) , wybierając te uprawnienia w rejestracji aplikacji w aplikacji klienckiej. Jeśli nie zostały ujawnione żadne zakresy w rejestracji aplikacji interfejsu API, nie będzie można określić uprawnień aplikacji dla tego interfejsu API w ramach rejestracji aplikacji w aplikacji klienckiej w Azure Portal.

W przypadku uwierzytelniania jako aplikacji (w przeciwieństwie do użytkownika) nie można używać *delegowanych uprawnień* — zakresów przyznanych przez użytkownika. Musisz użyć uprawnień aplikacji, znanych również jako role, które są udzielane przez administratora aplikacji lub przez internetowy interfejs API.

Aby uzyskać więcej informacji o uprawnieniach aplikacji, zobacz [uprawnienia i wyrażanie zgody](v2-permissions-and-consent.md#permission-types).

#### <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Podpisz użytkownika w aplikacji

Zazwyczaj podczas kompilowania aplikacji korzystającej z uprawnień aplikacji aplikacja wymaga strony lub widoku, w którym administrator zatwierdza uprawnienia aplikacji. Ta strona może należeć do przepływu logowania aplikacji, części ustawień aplikacji lub być dedykowanym przepływem "Połącz". W wielu przypadkach ma sens, aby aplikacja pokazywała ten widok "Połącz" tylko po zalogowaniu się użytkownika przy użyciu konto Microsoft służbowego.

Jeśli użytkownik jest zalogowany w aplikacji, można określić organizację, do której należy użytkownik, przed poproszeniem użytkownika o zatwierdzenie uprawnień aplikacji. Chociaż nie jest to absolutnie konieczne, może pomóc w tworzeniu bardziej intuicyjnego środowiska dla użytkowników. Aby podpisać użytkownika w programie, postępuj zgodnie z [samouczkami dotyczącymi protokołu Microsoft Identity platform](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy wszystko będzie gotowe do zażądania uprawnień od administratora organizacji, możesz przekierować użytkownika do *punktu końcowego zgody administratora* platformy tożsamości firmy Microsoft.

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster! (Użyj własnego identyfikatora aplikacji, aby uzyskać najlepsze wyniki — aplikacja samouczka nie będzie żądać przydatnych uprawnień). [ ![ Spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

Porada pakietu Pro: spróbuj wkleić poniższe żądanie w przeglądarce.

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Dzierżawa katalogu, z której chcesz zażądać uprawnień. Może to być w formacie identyfikatora GUID lub przyjaznej nazwy. Jeśli nie wiesz, do której dzierżawy należy użytkownik i chcesz zezwolić im na logowanie się przy użyciu dowolnej dzierżawy, użyj `common` . |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `redirect_uri` | Wymagane | Identyfikator URI przekierowania, w którym odpowiedź ma być wysyłana przez aplikację do obsługi. Musi on dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany w adresie URL i może zawierać dodatkowe segmenty ścieżki. |
| `state` | Zalecane | Wartość, która jest zawarta w żądaniu, która jest również zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Ten stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |

W tym momencie usługa Azure AD wymusza, że tylko administrator dzierżawy może zalogować się w celu ukończenia żądania. Administrator zostanie poproszony o zatwierdzenie wszystkich uprawnień aplikacji bezpośrednich żądanych dla aplikacji w portalu rejestracji aplikacji.

##### <a name="successful-response"></a>Pomyślna odpowiedź

Jeśli administrator zatwierdzi uprawnienia do aplikacji, pomyślna odpowiedź wygląda następująco:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawa katalogu, która udzieliła aplikacji żądane uprawnienia, w formacie identyfikatora GUID. |
| `state` | Wartość, która jest zawarta w żądaniu, która również jest zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Ten stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| `admin_consent` | Wartość **true**. |

##### <a name="error-response"></a>Odpowiedź na błąd

Jeśli administrator nie zatwierdzi uprawnień dla aplikacji, odpowiedź zakończona niepowodzeniem będzie wyglądać następująco:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów i którego można użyć do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego aprowizacji aplikacji Aplikacja uzyskała bezpośrednio żądane uprawnienia aplikacji. Teraz możesz zażądać tokenu dla żądanego zasobu.

## <a name="get-a-token"></a>Pobierz token

Po uzyskaniu niezbędnej autoryzacji aplikacji Kontynuuj uzyskiwanie tokenów dostępu dla interfejsów API. Aby uzyskać token przy użyciu przydzielenia poświadczeń klienta, Wyślij żądanie POST do `/token` platformy tożsamości firmy Microsoft:

> [!TIP]
> Spróbuj wykonać to żądanie w programie Poster! (Użyj własnego identyfikatora aplikacji, aby uzyskać najlepsze wyniki — aplikacja samouczka nie będzie żądać przydatnych uprawnień). [ ![ Spróbuj uruchomić to żądanie w programie Poster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu przy użyciu wspólnego klucza tajnego

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```Bash
# Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Dzierżawca katalogu, w którym aplikacja planuje działać, w formacie GUID lub nazwa domeny. |
| `client_id` | Wymagane | Identyfikator aplikacji przypisany do aplikacji. Te informacje można znaleźć w portalu, w którym zarejestrowano aplikację. |
| `scope` | Wymagane | Wartość przekazaną dla `scope` parametru w tym żądaniu powinna być identyfikatorem zasobu (identyfikatorem URI aplikacji), który ma zostać dołączony do `.default` sufiksu. Na przykład Microsoft Graph wartość jest `https://graph.microsoft.com/.default` . <br/>Ta wartość informuje platformę Microsoft Identity o wszystkich uprawnieniach bezpośrednich aplikacji skonfigurowanych dla aplikacji, dlatego punkt końcowy powinien wydać token dla skojarzonych z zasobem, który ma być używany. Aby dowiedzieć się więcej o `/.default` zakresie, zobacz [dokumentację dotyczącą zgody](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Wymagane | Wpis tajny klienta wygenerowany dla aplikacji w portalu rejestracji aplikacji. Wpis tajny klienta musi być zakodowany przy użyciu adresu URL przed wysłaniem. |
| `grant_type` | Wymagane | Musi być ustawiony na `client_credentials` . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagane | Dzierżawca katalogu, w którym aplikacja planuje działać, w formacie GUID lub nazwa domeny. |
| `client_id` | Wymagane |Identyfikator aplikacji (klienta) przypisany do aplikacji. |
| `scope` | Wymagane | Wartość przekazaną dla `scope` parametru w tym żądaniu powinna być identyfikatorem zasobu (identyfikatorem URI aplikacji), który ma zostać dołączony do `.default` sufiksu. Na przykład Microsoft Graph wartość jest `https://graph.microsoft.com/.default` . <br/>Ta wartość informuje platformę tożsamości firmy Microsoft o wszystkich bezpośrednich uprawnieniach aplikacji skonfigurowanych dla danej aplikacji. powinna wydać token dla skojarzonych z zasobem, który ma być używany. Aby dowiedzieć się więcej o `/.default` zakresie, zobacz [dokumentację dotyczącą zgody](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Wymagane | Wartość musi być ustawiona na `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` . |
| `client_assertion` | Wymagane | Potwierdzenie (token sieci Web JSON), które należy utworzyć i podpisać przy użyciu certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Przeczytaj informacje o [poświadczeniach certyfikatów](active-directory-certificate-credentials.md) , aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia.|
| `grant_type` | Wymagane | Musi być ustawiony na `client_credentials` . |

Zwróć uwagę, że parametry są prawie takie same, jak w przypadku żądania przez wspólny klucz tajny, z tą różnicą, że parametr client_secret jest zastępowany przez dwa parametry: client_assertion_type i client_assertion.

### <a name="successful-response"></a>Pomyślna odpowiedź

Odpowiedź oznaczająca powodzenie wygląda następująco:

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Opis |
| --- | --- |
| `access_token` | Żądany token dostępu. Aplikacja może używać tego tokenu do uwierzytelniania w zabezpieczonym zasobie, na przykład do internetowego interfejsu API. |
| `token_type` | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje platforma tożsamości firmy Microsoft, jest `bearer` . |
| `expires_in` | Czas ważności tokenu dostępu (w sekundach). |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedź na błąd wygląda następująco:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które występują, oraz do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| `error_codes` | Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| `timestamp` | Czas wystąpienia błędu. |
| `trace_id` | Unikatowy identyfikator żądania, które pomoże przeprowadzić diagnostykę. |
| `correlation_id` | Unikatowy identyfikator dla żądania pomagającego przeprowadzić diagnostykę w składnikach. |

## <a name="use-a-token"></a>Użyj tokenu

Po uzyskaniu tokenu Użyj tokenu, aby wykonać żądania do zasobu. Po wygaśnięciu tokenu Powtórz żądanie do `/token` punktu końcowego, aby uzyskać nowy token dostępu.

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```bash
# Pro tip: Try the following command! (Replace the token with your own.)

curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Przykłady kodu i inne dokumenty

Przeczytaj [dokumentację dotyczącą przeglądu poświadczeń klienta](https://aka.ms/msal-net-client-credentials) w bibliotece uwierzytelniania firmy Microsoft

| Przykład | Platforma |Opis |
|--------|----------|------------|
|[Active-Directory-dotnetcore-demon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Konsola programu .NET Core 2,1 | Prosta aplikacja platformy .NET Core, która wyświetla użytkowników dzierżawcy wykonujących zapytania dotyczące Microsoft Graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. Przykład ilustruje również zmiany przy użyciu certyfikatów do uwierzytelniania. |
|[Active-Directory-dotnet-demon — wersja 2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Aplikacja sieci Web, która synchronizuje dane z Microsoft Graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. |
