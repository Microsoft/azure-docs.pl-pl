---
title: Logowanie w sieci Web za pomocą OpenID Connect Connect-Azure Active Directory B2C
description: Twórz aplikacje sieci Web przy użyciu protokołu uwierzytelniania OpenID Connect Connect w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: dbfeefc14059785ba82cbf245a60e5e72759db76
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840410"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Logowanie w sieci Web za pomocą OpenID Connect Connect in Azure Active Directory B2C

OpenID Connect Connect to protokół uwierzytelniania oparty na protokole OAuth 2,0, który może służyć do bezpiecznego podpisywania użytkowników w aplikacjach sieci Web. Korzystając z implementacji Azure Active Directory B2C (Azure AD B2C) OpenID Connect Connect, możesz w swoich aplikacjach sieci Web utworzyć konto, zalogować się i inne funkcje zarządzania tożsamościami, aby Azure Active Directory (Azure AD). W tym przewodniku pokazano, jak to zrobić w sposób niezależny od języka. Opisano w nim, jak wysyłać i odbierać komunikaty HTTP bez używania bibliotek typu open-source.

[OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) rozszerza protokół *autoryzacji* OAuth 2,0 do użycia jako protokół *uwierzytelniania* . Ten protokół uwierzytelniania umożliwia logowanie jednokrotne. Wprowadza koncepcję *tokenu identyfikatora*, dzięki czemu klient może weryfikować tożsamość użytkownika i uzyskać podstawowe informacje o profilu użytkownika.

Ponieważ rozszerza on protokół OAuth 2,0, umożliwia również aplikacjom bezpieczne pozyskiwanie *tokenów dostępu*. Za pomocą tokenów dostępu można uzyskać dostęp do zasobów zabezpieczonych przez [serwer autoryzacji](protocols-overview.md). Połączenie OpenID Connect jest zalecane, jeśli tworzysz aplikację sieci Web, która jest hostowana na serwerze i jest dostępna za pomocą przeglądarki. Aby uzyskać więcej informacji na temat tokenów, zobacz [Omówienie tokenów w Azure Active Directory B2C](tokens-overview.md)

Azure AD B2C rozszerza standardowy protokół połączenia OpenID Connect, aby wykonywać więcej niż proste uwierzytelnianie i autoryzację. Wprowadza [parametr przepływu użytkownika](user-flow-overview.md), który umożliwia korzystanie z programu OpenID Connect Connect w celu dodawania środowisk użytkownika do aplikacji, takich jak rejestrowanie, logowanie i Zarządzanie profilem.

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania

Gdy aplikacja sieci Web wymaga uwierzytelnienia użytkownika i uruchomienia przepływu użytkownika, może kierować użytkownika do `/authorize` punktu końcowego. Użytkownik wykonuje akcję w zależności od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia, które muszą uzyskać od użytkownika w `scope` parametrze i określa przepływ użytkownika do uruchomienia. Aby uzyskać informacje na temat sposobu działania żądania, spróbuj wkleić żądanie do przeglądarki i uruchomić ją. Zamień `{tenant}` na nazwę dzierżawy. Zamień na `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` Identyfikator aplikacji aplikacji, która została wcześniej zarejestrowana w dzierżawie. Na przykład zmień nazwę zasad ( `{policy}` ) na nazwę zasad, która znajduje się w dzierżawie `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| dzierżaw | Tak | Nazwa dzierżawy Azure AD B2C |
| zasad | Tak | Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie Azure AD B2C. Na przykład: `b2c_1_sign_in` , `b2c_1_sign_up` , lub `b2c_1_edit_profile` . |
| client_id | Tak | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| jednorazow | Tak | Wartość dołączona do żądania (wygenerowanego przez aplikację), która jest uwzględniona w tokenie zwracanego identyfikatora jako jako element Claim. Aplikacja może następnie zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Wartość jest zazwyczaj losowym unikatowym ciągiem, który może służyć do identyfikowania pochodzenia żądania. |
| response_type | Tak | Musi zawierać token identyfikatora dla OpenID Connect Connect. Jeśli aplikacja sieci Web wymaga również tokenów do wywoływania internetowego interfejsu API, można użyć programu `code+id_token` . |
| scope | Tak | Rozdzielana spacjami lista zakresów. `openid`Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci tokenów identyfikatorów. `offline_access`Zakres jest opcjonalny dla aplikacji sieci Web. Wskazuje, że aplikacja będzie potrzebować *tokenu odświeżania* na potrzeby rozszerzonego dostępu do zasobów. |
| pytać | Nie | Typ interakcji z użytkownikiem, która jest wymagana. Jedyną prawidłową wartością w tym momencie jest to `login` , co wymusza użytkownikowi wprowadzanie poświadczeń dla tego żądania. |
| redirect_uri | Nie | `redirect_uri`Parametr aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednemu z `redirect_uri` parametrów zarejestrowanych w Azure Portal, z tą różnicą, że musi on być zakodowany w adresie URL. |
| response_mode | Nie | Metoda, która jest używana do wysyłania podanego kodu autoryzacji z powrotem do aplikacji. Może to być albo `query` , `form_post` , lub `fragment` .  W `form_post` celu uzyskania najlepszych zabezpieczeń zaleca się używanie trybu odpowiedzi. |
| stan | Nie | Wartość zawarta w żądaniu, która jest również zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu zapobiegania atakom na fałszerstwo żądań między witrynami. Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, na przykład na stronie, w której znajdowały się. |

W tym momencie użytkownik zostanie poproszony o ukończenie przepływu pracy. Użytkownik może wprowadzić nazwę użytkownika i hasło, zalogować się przy użyciu tożsamości społecznościowej lub zarejestrować się w katalogu. Może istnieć jakakolwiek inna liczba kroków w zależności od sposobu definiowania przepływu użytkownika.

Gdy użytkownik ukończy przepływ użytkownika, odpowiedź jest zwracana do aplikacji w wskazanym `redirect_uri` parametrze przy użyciu metody określonej w `response_mode` parametrze. Odpowiedź jest taka sama dla każdego z powyższych przypadków, niezależnie od przepływu użytkownika.

Pomyślna odpowiedź przy użyciu `response_mode=fragment` powinna wyglądać następująco:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| id_token | Token identyfikatora, którego żądała aplikacja. Możesz użyć tokenu identyfikatora, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. |
| kod | Kod autoryzacji, którego żądał aplikacja, jeśli został użyty `response_type=code+id_token` . Aplikacja może używać kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. Kody autoryzacji zwykle wygasają po około 10 minutach. |
| stan | Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

Do parametru można także wysyłać odpowiedzi na błędy `redirect_uri` , aby aplikacja mogła je odpowiednio obsłużyć:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Określony komunikat o błędzie, który może pomóc identyfikować główną przyczynę błędu uwierzytelniania. |
| stan | Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

## <a name="validate-the-id-token"></a>Weryfikowanie tokenu identyfikatora

Tylko otrzymanie tokenu identyfikatora jest za mało do uwierzytelnienia użytkownika. Sprawdź poprawność podpisu tokenu identyfikatora i sprawdź oświadczenia w tokenie zgodnie z wymaganiami aplikacji. Azure AD B2C używa [tokenów sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe. Istnieje wiele bibliotek typu "open source", które są dostępne do sprawdzania poprawności JWTs, w zależności od języka preferencji. Zalecamy Eksplorowanie tych opcji zamiast implementowania własnej logiki walidacji.

Azure AD B2C ma punkt końcowy metadanych połączenia OpenID Connect, który umożliwia aplikacji pobieranie informacji o Azure AD B2C w czasie wykonywania. Te informacje obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Istnieje dokument metadanych JSON dla każdego przepływu użytkowników w dzierżawie usługi B2C. Na przykład dokument metadanych dla `b2c_1_sign_in` przepływu użytkownika w programie znajduje `fabrikamb2c.onmicrosoft.com` się w lokalizacji:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jedną z właściwości tego dokumentu konfiguracji jest `jwks_uri` , której wartość tego samego przepływu użytkownika byłaby:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Aby określić, który przepływ użytkownika był używany podczas podpisywania tokenu identyfikatora (i skąd uzyskać metadane), dostępne są dwie opcje. Najpierw nazwa przepływu użytkownika jest uwzględniana w podaniu `acr` w tokenie identyfikatora. Innym rozwiązaniem jest zakodowanie przepływu użytkownika w wartości `state` parametru podczas wysyłania żądania, a następnie dekodowanie go w celu określenia, który przepływ użytkownika został użyty. Każda metoda jest prawidłowa.

Po uzyskaniu dokumentu metadanych z punktu końcowego metadanych OpenID Connect Connect można użyć kluczy publicznych RSA 256 do weryfikacji podpisu tokenu identyfikatora. W tym punkcie końcowym mogą znajdować się wiele kluczy, z których każdy jest identyfikowany przez element `kid` Claims. Nagłówek tokenu identyfikatora zawiera również element `kid` Claim wskazujący, który z tych kluczy został użyty do podpisania tokenu identyfikatora.

Aby sprawdzić tokeny z Azure AD B2C, należy wygenerować klucz publiczny przy użyciu wykładnika (e) i modulo (n). Należy określić, jak to zrobić w odpowiednim języku programowania. Oficjalną dokumentację dotyczącą generowania klucza publicznego przy użyciu protokołu RSA można znaleźć tutaj: https://tools.ietf.org/html/rfc3447#section-3.1

Po sprawdzeniu poprawności sygnatury tokenu identyfikatora istnieje kilka oświadczeń, które należy zweryfikować. Przykład:

- Sprawdź poprawność `nonce` roszczeń, aby zapobiec atakom metodą powtórzeń tokenu. Jej wartość powinna być określona w żądaniu logowania.
- Sprawdź poprawność `aud` roszczeń, aby upewnić się, że token identyfikatora został wystawiony dla aplikacji. Jej wartość powinna być IDENTYFIKATORem aplikacji.
- Sprawdź poprawność `iat` i `exp` oświadczenia, aby upewnić się, że token identyfikatora nie wygasł.

Istnieje również kilka dodatkowych poprawń, które należy wykonać. Walidacje są szczegółowo opisane w [specyfikacji OpenID Connect Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń, w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

- Upewnienie się, że użytkownik/organizacja zarejestrowali się w aplikacji.
- Upewnienie się, że użytkownik ma odpowiednie uprawnienia/autoryzacja.
- Upewnienie się, że wystąpiło pewne silne uwierzytelnianie, takie jak usługa Azure AD Multi-Factor Authentication.

Po sprawdzeniu poprawności tokenu identyfikatora można rozpocząć sesję z użytkownikiem. Oświadczenia w tokenie identyfikatora mogą służyć do uzyskiwania informacji o użytkowniku w aplikacji. Program używa tych informacji, takich jak wyświetlanie, rekordy i autoryzacja.

## <a name="get-a-token"></a>Pobierz token

Jeśli potrzebujesz aplikacji sieci Web tylko do uruchamiania przepływów użytkowników, możesz pominąć kilka następnych sekcji. Te sekcje mają zastosowanie tylko do aplikacji sieci Web, które muszą wykonać uwierzytelnione wywołania do internetowego interfejsu API, a także są chronione przez Azure AD B2C.

Możesz zrealizować pobrany kod autoryzacji (za pomocą `response_type=code+id_token` ) dla tokenu do żądanego zasobu, wysyłając `POST` żądanie do `/token` punktu końcowego. W Azure AD B2C można [żądać tokenów dostępu dla innych interfejsów API](access-tokens.md#request-a-token) w zwykły sposób, określając ich zakresy w żądaniu.

Możesz również zażądać tokenu dostępu dla własnego internetowego interfejsu API zaplecza w ramach Konwencji przy użyciu identyfikatora klienta aplikacji jako żądanego zakresu (co spowoduje użycie tokenu dostępu z IDENTYFIKATORem klienta jako "odbiorcy"):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| dzierżaw | Tak | Nazwa dzierżawy Azure AD B2C |
| zasad | Tak | Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. Nie można użyć innego przepływu użytkownika w tym żądaniu. Dodaj ten parametr do ciągu zapytania, a nie do treści wpisu. |
| client_id | Tak | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| client_secret | Tak, w Web Apps | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W przypadku scenariuszy aplikacji natywnych (klienta publicznego) klucze tajne klienta nie mogą być bezpiecznie przechowywane, dlatego nie są używane w tym przepływie. W przypadku korzystania z klucza tajnego klienta należy okresowo zmieniać. |
| kod | Tak | Kod autoryzacji uzyskany na początku przepływu użytkownika. |
| grant_type | Tak | Typ dotacji, który musi być `authorization_code` przeznaczony dla przepływu kodu autoryzacji. |
| redirect_uri | Tak | `redirect_uri`Parametr aplikacji, w której został otrzymany kod autoryzacji. |
| scope | Nie | Rozdzielana spacjami lista zakresów. `openid`Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci parametrów id_token. Może służyć do uzyskiwania tokenów dla własnego interfejsu API zaplecza aplikacji, który jest reprezentowany przez ten sam identyfikator aplikacji co klient. `offline_access`Zakres wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby rozszerzonego dostępu do zasobów. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parametr | Opis |
| --------- | ----------- |
| not_before | Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type | Wartość typu tokenu. `Bearer` jest jedynym obsługiwanym typem. |
| access_token | Zażądano podpisanego tokenu JWT. |
| scope | Zakresy, dla których token jest prawidłowy. |
| expires_in | Długość czasu ważności tokenu dostępu (w sekundach). |
| refresh_token | Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. Zakres `offline_access` musi być używany w żądaniach autoryzacji i tokenu, aby można było odebrać token odświeżania. |

Odpowiedzi na błędy wyglądają następująco:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Komunikat, który może pomóc identyfikować główną przyczynę błędu uwierzytelniania. |

## <a name="use-the-token"></a>Użyj tokenu

Po pomyślnym pobraniu tokenu dostępu można użyć tokenu w żądaniach do interfejsów API sieci Web zaplecza, dołączając je do `Authorization` nagłówka:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Odświeżanie tokenu

Tokeny identyfikatora wygasają w krótkim czasie. Odśwież tokeny po wygaśnięciu, aby nadal mogły uzyskiwać dostęp do zasobów. Można odświeżyć token, przesyłając kolejne `POST` żądanie do `/token` punktu końcowego. Tym razem Podaj `refresh_token` parametr zamiast `code` parametru:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| dzierżaw | Tak | Nazwa dzierżawy Azure AD B2C |
| zasad | Tak | Przepływ użytkownika, który został użyty do uzyskania oryginalnego tokenu odświeżania. Nie można użyć innego przepływu użytkownika w tym żądaniu. Dodaj ten parametr do ciągu zapytania, a nie do treści wpisu. |
| client_id | Tak | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| client_secret | Tak, w Web Apps | Wpis tajny aplikacji, który został wygenerowany w [Azure Portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W przypadku scenariuszy aplikacji natywnych (klienta publicznego) klucze tajne klienta nie mogą być bezpiecznie przechowywane, dlatego nie są używane w tym wywołaniu. W przypadku korzystania z klucza tajnego klienta należy okresowo zmieniać. |
| grant_type | Tak | Typ dotacji, który musi być `refresh_token` dla tej części przepływu kodu autoryzacji. |
| refresh_token | Tak | Oryginalny token odświeżania, który został pobrany w drugiej części przepływu. `offline_access`Zakres musi być używany w żądaniach autoryzacji i tokenu, aby można było odebrać token odświeżania. |
| redirect_uri | Nie | `redirect_uri`Parametr aplikacji, w której został otrzymany kod autoryzacji. |
| scope | Nie | Rozdzielana spacjami lista zakresów. `openid`Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci tokenów identyfikatorów. Może służyć do wysyłania tokenów do własnego interfejsu API zaplecza aplikacji, który jest reprezentowany przez ten sam identyfikator aplikacji co klient. `offline_access`Zakres wskazuje, że aplikacja wymaga tokenu odświeżania na potrzeby rozszerzonego dostępu do zasobów. |

Pomyślna odpowiedź dotycząca tokenu wygląda następująco:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parametr | Opis |
| --------- | ----------- |
| not_before | Godzina, o której token jest uznawany za ważny, w czasie epoki. |
| token_type | Wartość typu tokenu. `Bearer` jest jedynym obsługiwanym typem. |
| access_token | Zażądano podpisanego tokenu JWT. |
| scope | Zakres, dla którego token jest prawidłowy. |
| expires_in | Długość czasu ważności tokenu dostępu (w sekundach). |
| refresh_token | Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. |

Odpowiedzi na błędy wyglądają następująco:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod, który może służyć do klasyfikowania typów błędów, które występują. |
| error_description | Komunikat, który może pomóc identyfikować główną przyczynę błędu uwierzytelniania. |

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania

Jeśli chcesz podpisać użytkownika poza aplikacją, nie wystarczy wyczyścić plików cookie aplikacji lub zakończyć sesję z użytkownikiem. Przekieruj użytkownika do Azure AD B2C, aby się wylogować. W przeciwnym razie użytkownik może być w stanie ponownie uwierzytelnić się w aplikacji bez konieczności ponownego wprowadzania poświadczeń. Aby uzyskać więcej informacji, zobacz [Azure AD B2C Session](session-overview.md).

Aby wylogować użytkownika, Przekieruj użytkownika do `end_session` punktu końcowego, który jest wymieniony w dokumencie OpenID Connect Connect Metadata opisanego wcześniej:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| dzierżaw | Tak | Nazwa dzierżawy Azure AD B2C |
| zasad | Tak | Przepływ użytkownika, którego chcesz użyć do podpisania użytkownika z poziomu aplikacji. |
| id_token_hint| Nie | Wcześniej wystawiony token ID do przekazania do punktu końcowego wylogowania jako wskazówkę dotyczącą bieżącej uwierzytelnionej sesji użytkownika końcowego z klientem. `id_token_hint`Zapewnia, że `post_logout_redirect_uri` jest to zarejestrowany adres URL odpowiedzi w ustawieniach aplikacji Azure AD B2C. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie przekierowania wylogowania](#secure-your-logout-redirect). |
| client_id | Znaleziono | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji.<br><br>\**Jest to wymagane w przypadku korzystania z `Application` konfiguracji logowania jednokrotnego z izolacją i _Wymagaj tokenu identyfikatora_ w żądaniu wylogowania `No` .* |
| post_logout_redirect_uri | Nie | Adres URL, do którego użytkownik powinien zostać przekierowany po pomyślnym wylogowaniu. Jeśli ta wartość nie jest uwzględniona, Azure AD B2C pokazuje, że użytkownik jest komunikatem ogólnym. O ile nie podano `id_token_hint` , nie należy rejestrować tego adresu URL jako adresu URL odpowiedzi w ustawieniach aplikacji Azure AD B2C. |
| stan | Nie | Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

### <a name="secure-your-logout-redirect"></a>Zabezpiecz przekierowanie wylogowania

Po wylogowaniu użytkownik zostanie przekierowany na identyfikator URI określony w `post_logout_redirect_uri` parametrze, bez względu na adresy URL odpowiedzi określone dla aplikacji. Jeśli jednak zostanie `id_token_hint` przekierowany prawidłowy, a **token Wymagaj tokenu w żądaniach wylogowania** jest włączony, Azure AD B2C sprawdza, czy wartość jest zgodna z `post_logout_redirect_uri` skonfigurowanymi identyfikatorami URI przekierowań aplikacji przed przekazaniem. Jeśli nie skonfigurowano zgodnego adresu URL odpowiedzi dla aplikacji, zostanie wyświetlony komunikat o błędzie i użytkownik nie zostanie przekierowany.

Aby ustawić wymagany token identyfikatora w żądaniach wylogowywania, zobacz [Konfigurowanie zachowania sesji w Azure Active Directory B2C](session-behavior-custom-policy.md#secure-your-logout-redirect)i [Konfigurowanie zachowania sesji przy użyciu zasad niestandardowych w programie Azure Active Directory B2C](session-behavior-custom-policy.md#secure-your-logout-redirect).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).
