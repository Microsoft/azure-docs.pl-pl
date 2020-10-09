---
title: Logowanie jednostronicowe przy użyciu niejawnego przepływu
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dodać jednostronicowe Logowanie przy użyciu niejawnego przepływu OAuth 2,0 z Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fb1750996f40db6d76db30cd1c3bc07186660159
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201858"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Jednostronicowe Logowanie przy użyciu niejawnego przepływu OAuth 2,0 w Azure Active Directory B2C

Wiele nowoczesnych aplikacji ma fronton aplikacji jednostronicowej, który jest pisany głównie w języku JavaScript. Często aplikacja jest zapisywana przy użyciu struktury, takiej jak reagowanie, kątowe lub Vue.js. Aplikacje jednostronicowe i inne aplikacje JavaScript, które są uruchamiane głównie w przeglądarce, mają kilka dodatkowych wyzwań związanych z uwierzytelnianiem:

- Charakterystyki zabezpieczeń tych aplikacji różnią się od tradycyjnych aplikacji sieci Web opartych na serwerze.
- Wiele serwerów autoryzacji i dostawców tożsamości nie obsługuje żądań funkcji udostępniania zasobów między źródłami (CORS).
- Przeglądarka oparta na pełnej stronie przekieruje się do aplikacji.

W celu obsługi tych aplikacji Azure Active Directory B2C (Azure AD B2C) używa niejawnego przepływu OAuth 2,0. Niejawny zakres autoryzacji uwierzytelniania OAuth 2,0 został opisany w [sekcji 4,2 specyfikacji OAuth 2,0](https://tools.ietf.org/html/rfc6749). W niejawnym przepływie aplikacja otrzymuje tokeny bezpośrednio z Azure Active Directory (Azure AD) autoryzuje punkt końcowy, bez żadnej wymiany serwer-serwer. Wszystkie logiky uwierzytelniania i obsługa sesji są wykonywane całkowicie w kliencie JavaScript z przekierowaniem strony lub polem podręcznym.

Azure AD B2C rozszerza standardowy przepływ niejawny protokołu OAuth 2,0 na więcej niż proste uwierzytelnianie i autoryzację. Azure AD B2C wprowadza [parametr Policy](user-flow-overview.md). Za pomocą parametru Policy można użyć protokołu OAuth 2,0 do dodawania zasad do aplikacji, takich jak rejestrowanie, logowanie i przepływy użytkowników zarządzania profilami. W przykładach żądań HTTP w tym artykule **{dzierżawca}. onmicrosoft. com** jest używany jako przykład. Zamień `{tenant}` na nazwę dzierżawy, jeśli istnieje, a także utworzono przepływ użytkownika.

Niejawny przepływ logowania wygląda podobnie do poniższej ilustracji. Każdy krok został szczegółowo opisany w dalszej części artykułu.

![Diagram stylu toru przedstawiający niejawny przepływ OpenID Connect Connect](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Wysyłanie żądań uwierzytelniania

Gdy aplikacja sieci Web wymaga uwierzytelnienia użytkownika i uruchomienia przepływu użytkownika, może kierować użytkownika do `/authorize` punktu końcowego. Użytkownik wykonuje akcję w zależności od przepływu użytkownika.

W tym żądaniu klient wskazuje uprawnienia wymagane do uzyskania od użytkownika w `scope` parametrze i przepływ użytkownika do uruchomienia. Aby uzyskać informacje na temat sposobu działania żądania, spróbuj wkleić żądanie do przeglądarki i uruchomić ją. Zamień `{tenant}` na nazwę dzierżawy Azure AD B2C. Zamień na `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` Identyfikator aplikacji aplikacji, która została wcześniej zarejestrowana w dzierżawie. Zamień na `{policy}` nazwę zasad utworzonych w dzierżawie, na przykład `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
|dzierżaw| Tak | Nazwa dzierżawy Azure AD B2C|
|zasad| Tak| Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie Azure AD B2C. Na przykład: `b2c_1_sign_in` , `b2c_1_sign_up` , lub `b2c_1_edit_profile` . |
| client_id | Tak | Identyfikator aplikacji, który [Azure Portal](https://portal.azure.com/) przypisany do aplikacji. |
| response_type | Tak | Musi zawierać `id_token` do logowania za OpenID Connect Connect. Może również zawierać typ odpowiedzi `token` . Jeśli używasz programu `token` , aplikacja może natychmiast odebrać token dostępu od autoryzowanego punktu końcowego, bez wykonywania drugiego żądania do autoryzowanego punktu końcowego.  W przypadku użycia `token` typu odpowiedzi `scope` parametr musi zawierać zakres, który wskazuje zasób, dla którego ma zostać wystawiony token. |
| redirect_uri | Nie | Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| response_mode | Nie | Określa metodę, która ma zostać użyta do wysłania zwróconego tokenu z powrotem do aplikacji.  W przypadku niejawnych przepływów Użyj `fragment` . |
| scope | Tak | Rozdzielana spacjami lista zakresów. Pojedyncza wartość zakresu wskazuje na usługę Azure AD oba wymagane uprawnienia. `openid`Zakres wskazuje uprawnienia do logowania użytkownika i pobieranie danych o użytkowniku w postaci tokenów identyfikatorów. `offline_access`Zakres jest opcjonalny dla aplikacji sieci Web. Oznacza to, że aplikacja wymaga tokenu odświeżania na potrzeby długotrwałego dostępu do zasobów. |
| stan | Nie | Wartość zawarta w żądaniu, która również jest zwracana w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, która ma być używana. Zwykle jest używana losowo generowana wartość unikatowa, aby zapobiec atakom na fałszerstwo żądań między lokacjami. Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, np. na stronie, w której znajdowały się. |
| jednorazow | Tak | Wartość dołączona do żądania (wygenerowanego przez aplikację), która jest uwzględniona w tokenie zwracanego identyfikatora jako jako element Claim. Następnie aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Zazwyczaj wartość jest wartością losową i unikatowym ciągiem, który może służyć do identyfikowania pochodzenia żądania. |
| pytać | Nie | Typ interakcji z użytkownikiem, która jest wymagana. Obecnie jedyna prawidłowa wartość to `login` . Ten parametr wymusza, aby użytkownik wprowadził swoje poświadczenia dla tego żądania. Logowanie jednokrotne nie obowiązuje. |

W tym momencie użytkownik zostanie poproszony o ukończenie przepływu pracy zasad. Użytkownik może wprowadzić nazwę użytkownika i hasło, zalogować się przy użyciu tożsamości społecznościowej, utworzyć konto w katalogu lub dowolną inną liczbę kroków. Akcje użytkownika zależą od sposobu definiowania przepływu użytkownika.

Po zakończeniu przepływu użytkownika usługa Azure AD zwróci odpowiedź do aplikacji na wartość użytą przez użytkownika `redirect_uri` . Używa metody określonej w `response_mode` parametrze. Odpowiedź jest dokładnie taka sama dla każdego scenariusza akcji użytkownika, niezależnie od przepływu użytkownika, który został wykonany.

### <a name="successful-response"></a>Pomyślna odpowiedź
Pomyślna odpowiedź, która używa `response_mode=fragment` i wygląda następująco `response_type=id_token+token` , z podziałami wierszy na czytelność:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametr | Opis |
| --------- | ----------- |
| access_token | Token dostępu, którego żądała aplikacja. |
| token_type | Wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. |
| expires_in | Długość czasu ważności tokenu dostępu (w sekundach). |
| scope | Zakresy, dla których token jest ważny. Można również użyć zakresów do buforowania tokenów do późniejszego użycia. |
| id_token | Token identyfikatora, którego żądała aplikacja. Możesz użyć tokenu identyfikatora, aby zweryfikować tożsamość użytkownika i rozpocząć sesję z użytkownikiem. Aby uzyskać więcej informacji o tokenach identyfikatorów i ich zawartości, zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md). |
| stan | Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |

### <a name="error-response"></a>Odpowiedź na błąd
Odpowiedzi na błędy można także wysyłać do identyfikatora URI przekierowania, aby aplikacja mogła je odpowiednio obsłużyć:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --------- | ----------- |
| error | Kod używany do klasyfikowania typów błędów, które występują. |
| error_description | Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| stan | Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne.|

## <a name="validate-the-id-token"></a>Weryfikowanie tokenu identyfikatora

Odebranie tokenu identyfikatora jest za mało, aby można było uwierzytelnić użytkownika. Sprawdź poprawność podpisu tokenu identyfikatora i sprawdź oświadczenia w tokenie według wymagań aplikacji. Azure AD B2C używa [tokenów sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) i kryptografii klucza publicznego do podpisywania tokenów i sprawdzania, czy są one prawidłowe.

Wiele bibliotek typu open source jest dostępnych do sprawdzania poprawności JWTs, w zależności od języka, którego wolisz używać. Rozważ możliwość eksplorowania dostępnych bibliotek Open Source zamiast implementowania własnej logiki walidacji. Aby dowiedzieć się, jak prawidłowo korzystać z tych bibliotek, można użyć informacji zawartych w tym artykule.

Azure AD B2C ma punkt końcowy metadanych połączenia OpenID Connect. Aplikacja może używać punktu końcowego do pobierania informacji o Azure AD B2C w czasie wykonywania. Te informacje obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Istnieje dokument metadanych JSON dla każdego przepływu użytkownika w dzierżawie Azure AD B2C. Na przykład dokument metadanych dla przepływu b2c_1_sign_in użytkownika w dzierżawie fabrikamb2c.onmicrosoft.com znajduje się w:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Jedną z właściwości tego dokumentu konfiguracji jest `jwks_uri` . Wartością tego samego przepływu użytkownika będzie:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Aby określić, który przepływ użytkownika był używany do podpisywania tokenu identyfikatora (i miejsca pobierania metadanych z), dostępne są dwie opcje. Najpierw nazwa przepływu użytkownika jest uwzględniona w podanym w temacie `acr` `id_token` . Aby uzyskać informacje na temat sposobu analizowania oświadczeń z tokenu identyfikatora, zobacz informacje o [tokenach Azure AD B2C](tokens-overview.md). Innym rozwiązaniem jest zakodowanie przepływu użytkownika w wartości `state` parametru podczas wysyłania żądania. Następnie należy zdekodować `state` parametr, aby określić, który przepływ użytkownika był używany. Każda metoda jest prawidłowa.

Po pobraniu dokumentu metadanych z punktu końcowego metadanych OpenID Connect Connect można użyć kluczy publicznych RSA-256 (znajdujących się w tym punkcie końcowym), aby zweryfikować podpis tokenu identyfikatora. W tym punkcie końcowym może istnieć wiele kluczy, z których każdy jest identyfikowany przez `kid` . Nagłówek `id_token` zawiera również element `kid` Claim. Wskazuje, które z tych kluczy użyto do podpisania tokenu identyfikatora. Aby uzyskać więcej informacji, w tym informacje dotyczące [sprawdzania poprawności tokenów](tokens-overview.md), zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md).
<!--TODO: Improve the information on this-->

Po sprawdzeniu podpisu tokenu identyfikatora kilka oświadczeń wymaga weryfikacji. Na przykład:

* Sprawdź poprawność `nonce` roszczeń, aby zapobiec atakom metodą powtórzeń tokenu. Jej wartość powinna być określona w żądaniu logowania.
* Sprawdź poprawność `aud` roszczeń, aby upewnić się, że token identyfikatora został wystawiony dla aplikacji. Wartość powinna być IDENTYFIKATORem aplikacji w aplikacji.
* Zweryfikuj `iat` oświadczenia i, `exp` Aby upewnić się, że token identyfikatora nie wygasł.

Więcej informacji, które należy wykonać, są szczegółowo opisane w [specyfikacji OpenID Connect Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Możesz również chcieć sprawdzić poprawność dodatkowych oświadczeń, w zależności od danego scenariusza. Niektóre typowe walidacje obejmują:

* Upewnienie się, że użytkownik lub organizacja zarejestrowali się w aplikacji.
* Upewnienie się, że użytkownik ma odpowiednie uprawnienia i autoryzację.
* Upewnienie się, że wystąpiła pewna siła uwierzytelniania, na przykład przy użyciu usługi Azure Multi-Factor Authentication.

Aby uzyskać więcej informacji o oświadczeniach w tokenie identyfikatora, zobacz [Informacje o tokenach Azure AD B2C](tokens-overview.md).

Po sprawdzeniu poprawności tokenu identyfikatora można rozpocząć sesję z użytkownikiem. W aplikacji użyj oświadczeń w tokenie identyfikatora, aby uzyskać informacje o użytkowniku. Te informacje mogą służyć do wyświetlania, rekordów, autoryzacji i tak dalej.

## <a name="get-access-tokens"></a>Uzyskaj tokeny dostępu
Jeśli Jedyną czynnością, jaką musi wykonać aplikacja sieci Web, jest wykonywanie przepływów użytkownika, możesz pominąć kilka następnych sekcji. Informacje zawarte w poniższych sekcjach dotyczą tylko aplikacji sieci Web, które muszą wykonywać uwierzytelnione wywołania do internetowego interfejsu API, a które są chronione przez Azure AD B2C.

Teraz, gdy użytkownik został zarejestrowany w aplikacji jednostronicowej, można uzyskać tokeny dostępu do wywoływania interfejsów API sieci Web zabezpieczonych przez usługę Azure AD. Nawet jeśli token został już odebrany przy użyciu `token` typu odpowiedzi, można użyć tej metody do uzyskania tokenów dla dodatkowych zasobów bez przekierowania użytkownika, aby ponownie się zalogować.

W typowym przepływie aplikacji sieci Web należy wysłać żądanie do `/token` punktu końcowego. Jednak punkt końcowy nie obsługuje żądań CORS, dlatego nie jest możliwe wywołanie AJAX w celu uzyskania tokenu odświeżania. Zamiast tego można użyć niejawnego przepływu w ukrytym elemencie iframe HTML, aby uzyskać nowe tokeny dla innych interfejsów API sieci Web. Oto przykład z podziałami wierszy na potrzeby czytelności:

```http
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parametr | Wymagane? | Opis |
| --- | --- | --- |
|dzierżaw| Wymagane | Nazwa dzierżawy Azure AD B2C|
zasad| Wymagane| Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie Azure AD B2C. Na przykład: `b2c_1_sign_in` , `b2c_1_sign_up` , lub `b2c_1_edit_profile` . |
| client_id |Wymagane |Identyfikator aplikacji przypisany do aplikacji w [Azure Portal](https://portal.azure.com). |
| response_type |Wymagane |Musi zawierać `id_token` do logowania za OpenID Connect Connect.  Może również zawierać typ odpowiedzi `token` . Jeśli używasz `token` tego miejsca, aplikacja może natychmiast odebrać token dostępu od autoryzowanego punktu końcowego, bez wykonywania drugiego żądania do autoryzowanego punktu końcowego. W przypadku użycia `token` typu odpowiedzi `scope` parametr musi zawierać zakres, który wskazuje zasób, dla którego ma zostać wystawiony token. |
| redirect_uri |Zalecane |Identyfikator URI przekierowania aplikacji, w którym odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| scope |Wymagane |Rozdzielana spacjami lista zakresów.  W przypadku uzyskiwania tokenów Uwzględnij wszystkie zakresy wymagane dla zamierzonego zasobu. |
| response_mode |Zalecane |Określa metodę, która jest używana do wysyłania zwróconego tokenu z powrotem do aplikacji. Dla niejawnego przepływu Użyj `fragment` . Można określić dwa inne tryby, `query` `form_post` ale nie działają w niejawnym przepływie. |
| stan |Zalecane |Wartość zawarta w żądaniu, która jest zwracana w odpowiedzi tokenu.  Może to być ciąg dowolnej zawartości, która ma być używana.  Zwykle jest używana losowo generowana wartość unikatowa, aby zapobiec atakom na fałszerstwo żądań między lokacjami.  Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strona lub widok użytkownika. |
| jednorazow |Wymagane |Wartość uwzględniona w żądaniu wygenerowanym przez aplikację, która jest uwzględniona w tokenie zwracanego identyfikatora jako jako element Claim.  Następnie aplikacja może zweryfikować tę wartość, aby zmniejszyć ataki metodą powtórzeń tokenu. Zazwyczaj wartość jest losowo unikatowym ciągiem, który identyfikuje źródło żądania. |
| pytać |Wymagane |Aby odświeżyć i uzyskać tokeny w ukrytym elemencie iframe, użyj, `prompt=none` Aby upewnić się, że element IFRAME nie zostanie zablokowany na stronie logowania i natychmiast zwraca wartość. |
| login_hint |Wymagane |Aby odświeżyć i uzyskać tokeny w ukrytym elemencie iframe, należy dołączyć nazwę użytkownika w tej instrukcji w celu rozróżnienia między wieloma sesjami, które użytkownik może mieć w danym momencie. Można wyodrębnić nazwę użytkownika ze starszej rejestracji przy użyciu `preferred_username` roszczeń ( `profile` zakres jest wymagany w celu otrzymania `preferred_username` żądania). |
| domain_hint |Wymagane |Możliwe wartości to `consumers` i `organizations`.  Aby odświeżyć i uzyskać tokeny w ukrytym elemencie iframe, należy uwzględnić `domain_hint` wartość w żądaniu.  Wyodrębnij `tid` z tokenu ID wcześniejszego logowania, aby określić, która wartość ma być używana ( `profile` zakres jest wymagany w celu uzyskania `tid` żądania). Jeśli `tid` jest to wartość `9188040d-6c67-4c5b-b112-36a304b66dad` , użyj `domain_hint=consumers` .  W przeciwnym razie użyj `domain_hint=organizations` . |

Po ustawieniu `prompt=none` parametru to żądanie zakończy się pomyślnie lub natychmiast zakończy się niepowodzeniem, a następnie zwraca do aplikacji.  Pomyślna odpowiedź jest wysyłana do aplikacji na wskazanym identyfikatorze URI przekierowania przy użyciu metody określonej w `response_mode` parametrze.

### <a name="successful-response"></a>Pomyślna odpowiedź
Pomyślna odpowiedź przy użyciu `response_mode=fragment` wygląda podobnie do tego przykładu:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametr | Opis |
| --- | --- |
| access_token |Token żądany przez aplikację. |
| token_type |Typ tokenu zawsze będzie miał wartość Bearer. |
| stan |Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| scope |Zakresy, dla których token dostępu jest prawidłowy. |

### <a name="error-response"></a>Odpowiedź na błąd
Odpowiedzi na błędy można także wysyłać do identyfikatora URI przekierowania, aby aplikacja mogła je odpowiednio obsłużyć.  W przypadku `prompt=none` , oczekiwany błąd wygląda podobnie do tego przykładu:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują. Można również użyć ciągu do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

Jeśli ten błąd wystąpi w żądaniu iframe, użytkownik musi interaktywnie zalogować się ponownie, aby pobrać nowy token.

## <a name="refresh-tokens"></a>Odśwież tokeny
Tokeny identyfikatorów i tokeny dostępu wygasają po krótkim czasie. Aby okresowo odświeżać te tokeny, należy przygotować aplikację.  Aby odświeżyć każdy typ tokenu, należy wykonać to samo ukryte żądanie iframe używane we wcześniejszym przykładzie przy użyciu `prompt=none` parametru do kontrolowania kroków usługi Azure AD.  Aby otrzymać nową `id_token` wartość, należy użyć `response_type=id_token` `scope=openid` parametrów i i `nonce` parametru.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowania
Aby podpisać użytkownika poza aplikacją, należy przekierować użytkownika do usługi Azure AD w celu wylogowania. Jeśli nie przekierujesz użytkownika, może być możliwe ponowne uwierzytelnienie w aplikacji bez konieczności ponownego wprowadzania poświadczeń, ponieważ mają ważną sesję logowania jednokrotnego z usługą Azure AD.

Można po prostu przekierować użytkownika do programu `end_session_endpoint` , który znajduje się na liście w tym samym dokumencie metadanych OpenID Connect Connect opisanym w [ZWERYFIKUJ token ID](#validate-the-id-token). Na przykład:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametr | Wymagane | Opis |
| --------- | -------- | ----------- |
| dzierżaw | Tak | Nazwa dzierżawy Azure AD B2C |
| zasad | Tak | Przepływ użytkownika, którego chcesz użyć do podpisania użytkownika z poziomu aplikacji. |
| post_logout_redirect_uri | Nie | Adres URL, do którego użytkownik powinien zostać przekierowany po pomyślnym wylogowaniu. Jeśli ta wartość nie jest uwzględniona, Azure AD B2C pokazuje, że użytkownik jest komunikatem ogólnym. |
| stan | Nie | Jeśli `state` parametr zostanie uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy `state` wartości w żądaniu i odpowiedzi są identyczne. |


> [!NOTE]
> Nakazuje użytkownikowi `end_session_endpoint` wyczyszczenie stanu logowania jednokrotnego użytkownika przy użyciu Azure AD B2C. Nie jest on jednak podpisywany użytkownikowi z sesji dostawcy tożsamości społecznościowej użytkownika. Jeśli użytkownik wybierze tego samego dostawcę tożsamości podczas kolejnego logowania, użytkownik zostanie uwierzytelniony ponownie, bez wprowadzania poświadczeń. Jeśli użytkownik chce wylogować się z aplikacji Azure AD B2C, nie musi oznaczać, że chcą całkowicie wylogować się z konta w serwisie Facebook, na przykład. Jednak w przypadku kont lokalnych sesja użytkownika zostanie zakończona prawidłowo.
>

## <a name="next-steps"></a>Następne kroki

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Przykład kodu: Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla języka JavaScript

[Aplikacja jednostronicowa skompilowana za pomocą msal.js dla Azure AD B2C][github-msal-js-example] (GitHub)

Ten przykład w witrynie GitHub ma pomóc w rozpoczęciu Azure AD B2C w prostej aplikacji sieci Web skompilowanej za pomocą [msal.js][github-msal-js] i przy użyciu uwierzytelniania w stylu podręcznym.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
