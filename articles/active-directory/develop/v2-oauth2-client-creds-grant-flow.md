---
title: Przepływ poświadczeń klienta OAuth 2.0 na platformie tożsamości firmy Microsoft | Azure
description: Tworzenie aplikacji sieci web przy użyciu implementacji platformy tożsamości firmy Microsoft protokołu uwierzytelniania OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6021348160b338bc3a153764968f6c846a644cd1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535863"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Platforma tożsamości firmy Microsoft i przepływ poświadczeń klienta OAuth 2.0

Można użyć [oauth 2.0 poświadczeń klienta udzielić](https://tools.ietf.org/html/rfc6749#section-4.4) określone w RFC 6749, czasami nazywany *dwunożny OAuth*, aby uzyskać dostęp do zasobów hostowanych w sieci Web przy użyciu tożsamości aplikacji. Ten typ dotacji jest powszechnie używany do interakcji między serwerami, które muszą być uruchamiane w tle, bez natychmiastowej interakcji z użytkownikiem. Tego typu aplikacje są często określane jako *demony* lub *konta usług.*

W tym artykule opisano sposób programowania bezpośrednio względem protokołu w aplikacji. Jeśli to możliwe, zaleca się użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast tego do [uzyskiwania tokenów i wywoływania zabezpieczonych interfejsów API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z programu MSAL](sample-v2-code.md).

Poświadczenia klienta OAuth 2.0 udzielają informacji umożliwia usłudze sieci web (klientowi poufnemu) używanie własnych poświadczeń zamiast personifikacji użytkownika do uwierzytelniania podczas wywoływania innej usługi sieci web. W tym scenariuszu klient jest zazwyczaj usługą sieci web warstwy środkowej, usługą demona lub witryną sieci web. Aby uzyskać wyższy poziom pewności, platforma tożsamości firmy Microsoft umożliwia również usłudze wywołującej używanie certyfikatu (zamiast udostępnionego klucza tajnego) jako poświadczenia.

> [!NOTE]
> Punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje wszystkich scenariuszy i funkcji usługi Azure AD. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj o [ograniczeniach platformy tożsamości firmy Microsoft](active-directory-v2-limitations.md).

W bardziej typowym *trzynożnym OAuth*aplikacja kliencka otrzymuje uprawnienia dostępu do zasobu w imieniu określonego użytkownika. Uprawnienie jest delegowane od użytkownika do aplikacji, zwykle podczas procesu [zgody.](v2-permissions-and-consent.md) Jednak w poświadczeń klienta *(dwunożny OAuth)* przepływ, uprawnienia są przyznawane bezpośrednio do samej aplikacji. Gdy aplikacja przedstawia token do zasobu, zasób wymusza, że sama aplikacja ma autoryzację do wykonywania akcji, a nie użytkownika.

## <a name="protocol-diagram"></a>Diagram protokołu

Przepływ poświadczeń całego klienta wygląda podobnie do poniższego diagramu. Opisujemy każdy z kroków w dalszej części tego artykułu.

![Diagram przedstawiający przepływ poświadczeń klienta](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Uzyskaj bezpośrednią autoryzację

Aplikacja zazwyczaj otrzymuje bezpośrednią autoryzację dostępu do zasobu na jeden z dwóch sposobów:

* [Za pośrednictwem listy kontroli dostępu (ACL) w zasobie](#access-control-lists)
* [Za pomocą przypisania uprawnień aplikacji w usłudze Azure AD](#application-permissions)

Te dwie metody są najbardziej typowe w usłudze Azure AD i zaleca się je dla klientów i zasobów, które wykonują przepływ poświadczeń klienta. Zasób może również autoryzować swoich klientów w inny sposób. Każdy serwer zasobów można wybrać metodę, która ma największy sens dla jego aplikacji.

### <a name="access-control-lists"></a>Listy kontroli dostępu

Dostawca zasobów może wymusić sprawdzanie autoryzacji na podstawie listy identyfikatorów aplikacji (klienta), które zna i udziela określonego poziomu dostępu do. Gdy zasób odbiera token z punktu końcowego platformy tożsamości firmy Microsoft, można dekodować token i wyodrębnić identyfikator aplikacji klienta z `appid` i `iss` oświadczeń. Następnie porównuje aplikację z listą kontroli dostępu (ACL), która utrzymuje. Ziarnistość i metoda listy ACL mogą się znacznie różnić między zasobami.

Typowym przypadkiem użycia jest użycie listy ACL do uruchamiania testów dla aplikacji sieci web lub dla internetowego interfejsu API. Internetowy interfejs API może udzielić tylko podzbioru pełnych uprawnień określonej klientowi. Aby uruchomić kompleksowe testy w interfejsie API, należy utworzyć klienta testowego, który uzyskuje tokeny z punktu końcowego platformy tożsamości firmy Microsoft, a następnie wysyła je do interfejsu API. Interfejs API następnie sprawdza listę ACL pod kątem identyfikatora aplikacji klienta testowego w celu uzyskania pełnego dostępu do całej funkcjonalności interfejsu API. Jeśli używasz tego rodzaju listy ACL, należy sprawdzić poprawność `appid` nie tylko wartość `iss` wywołującego, ale także sprawdzić, czy wartość tokenu jest zaufany.

Ten typ autoryzacji jest typowy dla demonów i kont usług, które muszą uzyskać dostęp do danych należących do użytkowników indywidualnych, którzy mają osobiste konta Microsoft. W przypadku danych należących do organizacji zaleca się uzyskanie niezbędnej autoryzacji za pośrednictwem uprawnień aplikacji.

### <a name="application-permissions"></a>Uprawnienia aplikacji

Zamiast używać list ACL, można użyć interfejsów API, aby udostępnić zestaw **uprawnień aplikacji**. Uprawnienie aplikacji jest przyznawane aplikacji przez administratora organizacji i może służyć tylko do uzyskiwania dostępu do danych należących do tej organizacji i jej pracowników. Na przykład program Microsoft Graph udostępnia kilka uprawnień aplikacji, aby wykonać następujące czynności:

* Czytanie poczty we wszystkich skrzynkach pocztowych
* Odczytywanie i pisanie poczty we wszystkich skrzynkach pocztowych
* Wysyłanie poczty jako dowolnego użytkownika
* Odczytywanie danych katalogu

Aby uzyskać więcej informacji na temat uprawnień aplikacji, przejdź do programu [Microsoft Graph](https://developer.microsoft.com/graph).

Aby korzystać z uprawnień aplikacji w aplikacji, wykonaj kroki omówione w następnych sekcjach.


> [!NOTE]
> Podczas uwierzytelniania jako aplikacji, w przeciwieństwie do użytkownika, nie można użyć "delegowane uprawnienia" (zakresy, które są przyznawane przez użytkownika).  Należy użyć "uprawnienia aplikacji", znany również jako "role", które są przyznawane przez administratora dla aplikacji (lub za pośrednictwem pre-autoryzacji przez interfejs API sieci web).


#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji

1. Zarejestruj się i utwórz aplikację za pomocą nowego [środowiska rejestracji aplikacji (wersja zapoznawcza).](quickstart-register-app.md)
2. Przejdź do aplikacji w środowiska rejestracji aplikacji (wersja zapoznawcza). Przejdź do sekcji **Klucze & wpisy tajne** i dodaj **nowy klucz tajny klienta,** ponieważ do żądania tokenu potrzebny jest co najmniej jeden klucz tajny klienta.
3. Znajdź sekcję **uprawnień interfejsu API,** a następnie dodaj **uprawnienia aplikacji,** których wymaga aplikacja.
4. **Zapisz** rejestrację aplikacji.

#### <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Zaloguj użytkownika do aplikacji

Zazwyczaj podczas tworzenia aplikacji, która używa uprawnień aplikacji, aplikacja wymaga strony lub widoku, na którym administrator zatwierdza uprawnienia aplikacji. Ta strona może być częścią przepływu logowania aplikacji, częścią ustawień aplikacji lub może być dedykowanym przepływem "połącz". W wielu przypadkach ma sens, aby aplikacja wyświetlała ten widok "połącz" tylko wtedy, gdy użytkownik zalogował się za pomocą służbowego konta Microsoft.

Jeśli zalogujesz użytkownika do aplikacji, możesz zidentyfikować organizację, do której należy użytkownik, zanim poprosisz użytkownika o zatwierdzenie uprawnień aplikacji. Chociaż nie jest to absolutnie konieczne, może pomóc w stworzeniu bardziej intuicyjnego środowiska dla użytkowników. Aby zalogować się do użytkownika, postępuj zgodnie z naszymi [samouczkami protokołu platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy będziesz gotowy do żądania uprawnień od administratora organizacji, możesz przekierować użytkownika do *punktu końcowego zgody administratora*platformy tożsamości firmy Microsoft .

> [!TIP]
> Spróbuj wykonać to żądanie w Postman! (Użyj własnego identyfikatora aplikacji, aby uzyskać najlepsze wyniki — aplikacja samouczka nie zażąda przydatnych uprawnień). [Spróbuj uruchomić to żądanie w postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Dzierżawy katalogu, które chcesz zażądać uprawnień od. Może to być identyfikator GUID lub przyjazny format nazwy. Jeśli nie wiesz, do której dzierżawy należy użytkownik i chcesz pozwolić mu `common`zalogować się za pomocą dowolnej dzierżawy, użyj . |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji. |
| `redirect_uri` | Wymagany | Identyfikator URI przekierowania, w którym ma zostać wysłana odpowiedź dla aplikacji do obsługi. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania, które zostały zarejestrowane w portalu, z tą różnicą, że musi być zakodowany adres URL i może mieć dodatkowe segmenty ścieżki. |
| `state` | Zalecane | Wartość, która jest uwzględniona w żądaniu, który jest również zwracany w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, która ma być. Stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |

W tym momencie usługa Azure AD wymusza, że tylko administrator dzierżawy może zalogować się do ukończenia żądania. Administrator zostanie poproszony o zatwierdzenie wszystkich uprawnień aplikacji bezpośrednich, o które żądano dla aplikacji w portalu rejestracji aplikacji.

##### <a name="successful-response"></a>Skuteczna reakcja

Jeśli administrator zatwierdzi uprawnienia do aplikacji, pomyślna odpowiedź wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawy katalogu, który przyznał aplikacji uprawnienia, które zażądał, w formacie GUID. |
| `state` | Wartość, która jest uwzględniona w żądaniu, który również jest zwracany w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, która ma być. Stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |
| `admin_consent` | Ustaw wartość **True**. |

##### <a name="error-response"></a>Odpowiedź na błąd

Jeśli administrator nie zatwierdzi uprawnień do aplikacji, odpowiedź, która nie powiodło się, wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, który służy do klasyfikowania typów błędów i który służy do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego inicjowania obsługi administracyjnej aplikacji aplikacja uzyskała uprawnienia aplikacji bezpośrednich, o które się zwróciła. Teraz możesz poprosić o token dla żądanego zasobu.

## <a name="get-a-token"></a>Zdobądź token

Po uzyskaniu niezbędnej autoryzacji dla aplikacji, przejdź do pozyskiwania tokenów dostępu dla interfejsów API. Aby uzyskać token przy użyciu dotacji poświadczeń `/token` klienta, wyślij żądanie POST do punktu końcowego platformy tożsamości firmy Microsoft:

> [!TIP]
> Spróbuj wykonać to żądanie w Postman! (Użyj własnego identyfikatora aplikacji, aby uzyskać najlepsze wyniki — aplikacja samouczka nie zażąda przydatnych uprawnień). [Spróbuj uruchomić to żądanie w postman ![](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Pierwszy przypadek: żądanie tokenu dostępu z udostępnionym kluczem tajnym

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant!
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| `tenant` | Wymagany | Dzierżawy katalogu aplikacja planuje działać przeciwko, w formacie GUID lub nazwa domeny. |
| `client_id` | Wymagany | Identyfikator aplikacji przypisany do aplikacji. Informacje te można znaleźć w portalu, w którym zarejestrowano aplikację. |
| `scope` | Wymagany | Wartość przekazywana `scope` dla parametru w tym żądaniu powinna być identyfikatorem zasobu (identyfikatorem URI aplikacji) odpowiedniego zasobu, który jest umieszczany z sufiksem. `.default` W przykładzie programu Microsoft Graph wartość to `https://graph.microsoft.com/.default`. <br/>Ta wartość informuje punkt końcowy platformy tożsamości firmy Microsoft, że wszystkich uprawnień aplikacji bezpośrednich skonfigurowanych dla aplikacji, punkt końcowy powinien wydać token dla tych skojarzonych z zasobem, który ma być używany. Aby dowiedzieć `/.default` się więcej o zakresie, zobacz [dokumentację zgody](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Wymagany | Klucz tajny klienta wygenerowany dla aplikacji w portalu rejestracji aplikacji. Klucz tajny klienta musi być zakodowany w adresie URL przed wysłaniem. |
| `grant_type` | Wymagany | Musi być `client_credentials`ustawiona na . |

### <a name="second-case-access-token-request-with-a-certificate"></a>Drugi przypadek: żądanie tokenu dostępu z certyfikatem

```
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
| `tenant` | Wymagany | Dzierżawy katalogu aplikacja planuje działać przeciwko, w formacie GUID lub nazwa domeny. |
| `client_id` | Wymagany |Identyfikator aplikacji (klienta), który jest przypisany do aplikacji. |
| `scope` | Wymagany | Wartość przekazywana `scope` dla parametru w tym żądaniu powinna być identyfikatorem zasobu (identyfikatorem URI aplikacji) odpowiedniego zasobu, który jest umieszczany z sufiksem. `.default` W przykładzie programu Microsoft Graph wartość to `https://graph.microsoft.com/.default`. <br/>Ta wartość informuje punkt końcowy platformy tożsamości firmy Microsoft, że wszystkie uprawnienia aplikacji bezpośredniej skonfigurowane dla aplikacji, należy wydać token dla tych skojarzonych z zasobem, który chcesz użyć. Aby dowiedzieć `/.default` się więcej o zakresie, zobacz [dokumentację zgody](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Wymagany | Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`ustawiona na . |
| `client_assertion` | Wymagany | Twierdzenie (token internetowy JSON), które należy utworzyć i podpisać za pomocą certyfikatu zarejestrowanego jako poświadczenia dla aplikacji. Przeczytaj o [poświadczeniach certyfikatu,](active-directory-certificate-credentials.md) aby dowiedzieć się, jak zarejestrować certyfikat i format potwierdzenia.|
| `grant_type` | Wymagany | Musi być `client_credentials`ustawiona na . |

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez klucz udostępniony klucz tajny, z tą różnicą, że parametr client_secret jest zastępowany przez dwa parametry: client_assertion_type i client_assertion.

### <a name="successful-response"></a>Skuteczna reakcja

Odpowiedź oznaczająca powodzenie wygląda następująco:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Opis |
| --- | --- |
| `access_token` | Żądany token dostępu. Aplikacja może używać tego tokenu do uwierzytelniania do zabezpieczonego zasobu, na przykład do internetowego interfejsu API. |
| `token_type` | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje `bearer`platforma tożsamości firmy Microsoft, jest . |
| `expires_in` | Czas, przez który token dostępu jest prawidłowy (w sekundach). |

### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedź na błąd wygląda następująco:

```
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
| `error` | Ciąg kodu błędu, który służy do klasyfikowania typów błędów, które występują i reagować na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| `error_codes` | Lista kodów błędów specyficznych dla STS, które mogą pomóc w diagnostyce. |
| `timestamp` | Czas wystąpienia błędu. |
| `trace_id` | Unikatowy identyfikator żądania, aby pomóc w diagnostyce. |
| `correlation_id` | Unikatowy identyfikator żądania, aby pomóc w diagnostyce między składnikami. |

## <a name="use-a-token"></a>Używanie tokenu

Teraz, gdy zostały nabyte tokenu, użyj tokenu do żądania do zasobu. Po wygaśnięciu tokenu powtórz żądanie `/token` do punktu końcowego, aby uzyskać nowy token dostępu.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Przykłady kodu i inna dokumentacja

Przeczytaj [dokumentację omówienia poświadczeń klienta](https://aka.ms/msal-net-client-credentials) z biblioteki uwierzytelniania firmy Microsoft

| Przykład | Platforma |Opis |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | Konsola .NET Core 2.1 | Prosta aplikacja .NET Core, która wyświetla użytkownikom dzierżawy kwerendy microsoft graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. Przykład ilustruje również odmiany przy użyciu certyfikatów do uwierzytelniania. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Aplikacja sieci web, która synchronizuje dane z programu Microsoft Graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. |
