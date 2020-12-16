---
title: Omówienie tokenów — Azure Active Directory B2C
description: Dowiedz się więcej na temat tokenów używanych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516351"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Omówienie tokenów w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania poszczególnych [przepływów uwierzytelniania](application-types.md). W tym dokumencie opisano format, charakterystykę zabezpieczeń i zawartość każdego typu tokenu.

## <a name="token-types"></a>Typy tokenów

Azure AD B2C obsługuje [protokoły połączeń OAuth 2,0 i OpenID Connect](protocols-overview.md), które umożliwiają uwierzytelnianie i bezpieczny dostęp do zasobów. Wszystkie tokeny używane w Azure AD B2C są [tokenami sieci Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) , które zawierają potwierdzenia informacji na temat okaziciela i tematu tokenu.

Następujące tokeny są używane w komunikacji z Azure AD B2C:

- *Token ID* — JWT zawierający oświadczenia, których można użyć do identyfikacji użytkowników w aplikacji. Ten token jest bezpiecznie wysyłany w żądaniach HTTP w celu komunikacji między dwoma składnikami tej samej aplikacji lub usługi. Możesz użyć oświadczeń w tokenie identyfikatora w miarę jak pasujesz. Są one często używane do wyświetlania informacji o kontach lub podejmowania decyzji dotyczących kontroli dostępu w aplikacji. Tokeny identyfikatorów są podpisane, ale nie są szyfrowane. Gdy aplikacja lub interfejs API otrzymuje token identyfikatora, musi sprawdzić poprawność podpisu, aby potwierdzić, że token jest autentyczny. Aplikacja lub interfejs API musi także sprawdzić poprawność kilku oświadczeń w tokenie, aby udowodnić, że jest on prawidłowy. W zależności od wymagań scenariusza oświadczenia zweryfikowane przez aplikację mogą się różnić, ale aplikacja musi wykonywać pewne typowe walidacje oświadczeń w każdym scenariuszu.
- *Token dostępu* — JWT zawierający oświadczenia, których można użyć do zidentyfikowania przyznanych uprawnień do interfejsów API. Tokeny dostępu są podpisane, ale nie są szyfrowane. Tokeny dostępu są używane w celu zapewnienia dostępu do interfejsów API i serwerów zasobów.  Gdy interfejs API otrzymuje token dostępu, musi sprawdzić poprawność podpisu, aby potwierdzić, że token jest autentyczny. Interfejs API musi również sprawdzać poprawność kilku oświadczeń w tokenie, aby udowodnić, że jest on prawidłowy. W zależności od wymagań scenariusza oświadczenia zweryfikowane przez aplikację mogą się różnić, ale aplikacja musi wykonywać pewne typowe walidacje oświadczeń w każdym scenariuszu.
- *Token odświeżania —* tokeny odświeżania są używane do uzyskiwania nowych tokenów identyfikatorów i tokenów dostępu w przepływie protokołu OAuth 2,0. Umożliwiają one aplikacji długoterminową dostęp do zasobów w imieniu użytkowników, bez konieczności interakcji z użytkownikami. Tokeny odświeżania są nieprzezroczyste dla aplikacji. Są one wydawane przez Azure AD B2C i mogą być sprawdzane i interpretowane tylko przez Azure AD B2C. Są one długotrwałe, ale aplikacja nie powinna być zapisywana w oczekiwany sposób, że token odświeżania będzie ostatni przez określony czas. Tokeny odświeżania można w dowolnym momencie unieważnić z wielu powodów. Jedynym sposobem, w jaki aplikacja ma wiedzieć, czy token odświeżania jest prawidłowy, jest próba jego zrealizowania przez utworzenie żądania tokenu do Azure AD B2C. W przypadku zrealizowania tokenu odświeżania dla nowego tokenu otrzymujesz nowy token odświeżania w odpowiedzi tokenu. Zapisz nowy token odświeżania. Zastępuje token odświeżania, który był wcześniej używany w żądaniu. Ta akcja umożliwia zagwarantowanie, że tokeny odświeżania pozostaną prawidłowe przez cały czas. Należy zauważyć, że aplikacje jednostronicowe korzystające z przepływu kodu autoryzacji z PKCE zawsze mają okres istnienia tokenu odświeżania równy 24 godziny. [Dowiedz się więcej o implikacjach dotyczących zabezpieczeń tokenów odświeżania w przeglądarce](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Punkty końcowe

[Zarejestrowana aplikacja](tutorial-register-applications.md) otrzymuje tokeny i komunikuje się z Azure AD B2C, wysyłając żądania do tych punktów końcowych:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Tokeny zabezpieczające, od których aplikacja otrzymuje Azure AD B2C mogą pochodzić z `/authorize` `/token` punktów końcowych lub. Gdy tokeny identyfikatorów są uzyskiwane z `/authorize` punktu końcowego, jest ono realizowane przy użyciu [niejawnego przepływu](implicit-flow-single-page-application.md), który jest często używany w przypadku użytkowników logujących się do aplikacji sieci Web opartych na języku JavaScript. Gdy tokeny identyfikatorów są uzyskiwane z `/token` punktu końcowego, odbywa się przy użyciu [przepływu kodu autoryzacji](openid-connect.md#get-a-token), który utrzymuje token ukryty w przeglądarce.

## <a name="claims"></a>Oświadczenia

Gdy używasz Azure AD B2C, masz precyzyjną kontrolę nad zawartością tokenów. [Przepływy użytkowników](user-flow-overview.md) i [zasady niestandardowe](custom-policy-overview.md) można skonfigurować do wysyłania określonych zestawów danych użytkownika w oświadczeniach wymaganych przez aplikację. Te oświadczenia mogą zawierać standardowe właściwości, takie jak **DisplayName** i **EmailAddress**. Aplikacje mogą używać tych oświadczeń do bezpiecznego uwierzytelniania użytkowników i żądań.

Oświadczenia w tokenach identyfikatorów nie są zwracane w żadnej określonej kolejności. Nowe oświadczenia można wprowadzać w tokenach identyfikatorów w dowolnym momencie. Twoja aplikacja nie powinna działać w miarę wprowadzania nowych oświadczeń. W oświadczeniach można również dołączać [niestandardowe atrybuty użytkownika](user-flow-custom-attributes.md) .

W poniższej tabeli wymieniono oświadczenia, których można oczekiwać w tokenach identyfikatorów i tokenach dostępu wystawionych przez Azure AD B2C.

| Nazwa | Claim | Przykładowa wartość | Opis |
| ---- | ----- | ------------- | ----------- |
| Grupy odbiorców | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identyfikuje zamierzony odbiorcę tokenu. W przypadku Azure AD B2C odbiorca jest IDENTYFIKATORem aplikacji. Aplikacja powinna sprawdzić poprawność tej wartości i odrzucić token, jeśli nie jest zgodny. Odbiorcy są równoznaczne z zasobami. |
| Wystawca | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identyfikuje usługę tokenu zabezpieczającego (STS), która konstruuje i zwraca token. Identyfikuje także katalog, w którym użytkownik został uwierzytelniony. Twoja aplikacja powinna sprawdzać poprawność roszczeń wystawcy, aby upewnić się, że token pochodzi z odpowiedniego punktu końcowego. |
| Wystawiony o | `iat` | `1438535543` | Godzina wystawienia tokenu reprezentowana w czasie epoki. |
| Czas wygaśnięcia | `exp` | `1438539443` | Godzina, o której token stał się nieprawidłowy, reprezentowane w czasie epoki. Twoja aplikacja powinna używać tego żądania, aby zweryfikować ważność okresu istnienia tokenu. |
| Nie przed | `nbf` | `1438535543` | Godzina, o której token stał się ważny, reprezentowane w czasie epoki. Ta godzina jest zwykle taka sama jak godzina wystawienia tokenu. Twoja aplikacja powinna używać tego żądania, aby zweryfikować ważność okresu istnienia tokenu. |
| Wersja | `ver` | `1.0` | Wersja tokenu identyfikatora, zgodnie z definicją Azure AD B2C. |
| Skrót kodu | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Skrót kodu zawarty w tokenie identyfikatora tylko wtedy, gdy token jest wystawiany wraz z kodem autoryzacji OAuth 2,0. Skrót kodu może służyć do weryfikowania autentyczności kodu autoryzacji. Aby uzyskać więcej informacji o tym, jak wykonać to sprawdzanie poprawności, zobacz [specyfikację OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Skrót tokenu dostępu | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Skrót tokenu dostępu zawarty w tokenie identyfikatora tylko wtedy, gdy token jest wystawiany wraz z tokenem dostępu OAuth 2,0. Skrót tokenu dostępu może służyć do weryfikowania autentyczności tokenu dostępu. Aby uzyskać więcej informacji o tym, jak wykonać to sprawdzanie poprawności, zobacz [Specyfikacja OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Jednorazow | `nonce` | `12345` | Identyfikator jednorazowy jest strategią używaną do ograniczania ataków metodą powtórzeń tokenu. Aplikacja może określić identyfikator jednorazowy w żądaniu autoryzacji za pomocą `nonce` parametru zapytania. Wartość podaną w żądaniu jest emitowana w niezmienionej postaci tylko w ramach żądania `nonce` tokenu identyfikatora. To zastrzeżenie umożliwia aplikacji zweryfikowanie wartości względem wartości określonej w żądaniu. Twoja aplikacja powinna przeprowadzić tę weryfikację podczas procesu weryfikacji tokenu identyfikatora. |
| Temat | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Podmiot zabezpieczeń, dla którego token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do bezpiecznego sprawdzania autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu. Domyślnie, zgłoszenie podmiotu jest wypełniane IDENTYFIKATORem obiektu użytkownika w katalogu. |
| Odwołanie do klasy kontekstu uwierzytelniania | `acr` | Nie dotyczy | Używany tylko ze starszymi zasadami. |
| Zasady dotyczące struktury zaufania | `tfp` | `b2c_1_signupsignin1` | Nazwa zasad, która została użyta w celu uzyskania tokenu identyfikatora. |
| Czas uwierzytelniania | `auth_time` | `1438535543` | Godzina, o której ostatnio wprowadzono poświadczenia, reprezentowane w czasie epoki. Nie ma żadnej dyskryminacji między tym uwierzytelnianiem, czyli sesją logowania jednokrotnego (SSO) lub innym typem logowania. `auth_time`Jest to godzina ostatniego zainicjowania przez aplikację (lub użytkownika) próby uwierzytelnienia w odniesieniu do Azure AD B2C. Metoda używana do uwierzytelniania nie jest rozróżniana. |
| Zakres | `scp` | `Read`| Uprawnienia przyznane do zasobu dla tokenu dostępu. Wiele przyznanych uprawnień jest rozdzielonych spacją. |
| Uprawniony podmiot | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **Identyfikator aplikacji** klienckiej, która zainicjowała żądanie. |

## <a name="configuration"></a>Konfigurowanie

Następujące właściwości są używane do [zarządzania okresami istnienia tokenów zabezpieczających](configure-tokens.md) emitowanych przez Azure AD B2C:

- **Okresy istnienia tokenu identyfikatora & dostępu (minuty)** — okres istnienia tokenu okaziciela OAuth 2,0 używany do uzyskiwania dostępu do chronionego zasobu. Wartość domyślna to 60 min. Minimalna (włącznie) to 5 minut. Wartość maksymalna (włącznie) to 1440 minut.

- **Okres istnienia tokenu odświeżania (w dniach)** — maksymalny przedział czasu, w którym można użyć tokenu odświeżania w celu uzyskania nowego tokenu dostępu lub identyfikatora. Okres obejmuje również Uzyskiwanie nowego tokenu odświeżania, jeśli Twoja aplikacja uzyskała `offline_access` zakres. Wartość domyślna to 14 dni. Minimalny (włącznie) to jeden dzień. Wartość maksymalna (włącznie) to 90 dni.

- Okres istnienia przenoszonego **okna tokenu odświeżania (w dniach)** — po upływie tego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego tokenu odświeżania uzyskanego przez aplikację. Można ją podać tylko wtedy, gdy przełącznik jest ustawiony na wartość **bounded**. Musi być większa lub równa wartości **okresu istnienia tokenu odświeżania (w dniach)** . Jeśli przełącznik jest ustawiony na **niepowiązane**, nie można podać określonej wartości. Wartość domyślna to 90 dni. Minimalny (włącznie) to jeden dzień. Wartość maksymalna (włącznie) to 365 dni.

Następujące przypadki użycia są włączone przy użyciu tych właściwości:

- Zezwalaj użytkownikowi na zalogowanie się do aplikacji mobilnej w nieskończoność, o ile użytkownik jest stale aktywny w aplikacji. **Okres istnienia okna przewijania tokenu odświeżania** można ustawić na **niepowiązane** w przepływie użytkownika logowania.
- Zapoznaj się z wymaganiami dotyczącymi bezpieczeństwa i zgodności w branży, ustawiając odpowiednie okresy istnienia tokenu dostępu.

Te ustawienia nie są dostępne na potrzeby przepływów użytkownika resetowania hasła.

## <a name="compatibility"></a>Zgodność

Do [zarządzania zgodnością tokenów](configure-tokens.md)służą następujące właściwości:

- **Wystawcy (ISS)** — ta właściwość identyfikuje dzierżawę Azure AD B2C, która wystawił token. Wartość domyślna to `https://<domain>/{B2C tenant GUID}/v2.0/`. Wartość `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` obejmuje identyfikatory zarówno dla dzierżawy Azure AD B2C, jak i przepływu użytkownika, który został użyty w żądaniu tokenu. Jeśli aplikacja lub biblioteka musi Azure AD B2C być zgodna ze [specyfikacją OpenID Connect Connect Discovery 1,0](https://openid.net/specs/openid-connect-discovery-1_0.html), Użyj tej wartości.

- **Zastrzeżenie podmiotu (Sub)** — ta właściwość identyfikuje jednostkę, dla której token potwierdza informacje. Wartość domyślna to **objectid**, który wypełnia zakres `sub` w tokenie identyfikatorem obiektu użytkownika. Wartość nie jest **obsługiwana** tylko pod kątem zgodności z poprzednimi wersjami. Zaleca się przełączenie do **identyfikatora objectid** , gdy tylko będzie możliwe.

- **Żądanie reprezentujące identyfikator zasad** — ta właściwość identyfikuje typ typu, w którym zostanie wypełniona Nazwa zasad używana w żądaniu tokenu. Wartość domyślna to `tfp`. Wartość `acr` jest zapewniana tylko w celu zapewnienia zgodności z poprzednimi wersjami.

## <a name="pass-through"></a>Przekazywanie

Po rozpoczęciu podróży użytkownika Azure AD B2C otrzymuje token dostępu od dostawcy tożsamości. Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. W przepływie użytkownika można włączyć funkcję [przekazywania tokenu](idp-pass-through-user-flow.md) do aplikacji, które są rejestrowane w Azure AD B2C. Aby skorzystać z przekazywania tokenu jako żądania, aplikacja musi używać [zalecanego przepływu użytkownika](user-flow-versions.md) .

Azure AD B2C obecnie obsługuje tylko przekazywanie tokenu dostępu dla dostawców tożsamości OAuth 2,0, w tym Facebook i Google. W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

## <a name="validation"></a>Walidacja

Aby sprawdzić poprawność tokenu, aplikacja powinna sprawdzić zarówno podpis, jak i oświadczenia tokenu. Wiele bibliotek typu open source jest dostępnych do sprawdzania poprawności JWTs, w zależności od preferowanego języka. Zalecane jest zapoznanie się z tymi opcjami zamiast implementowania własnej logiki walidacji.

### <a name="validate-signature"></a>Weryfikuj sygnaturę

Token JWT zawiera trzy segmenty, *nagłówek*, *treść* i *podpis*. Segment podpisu może służyć do weryfikowania autentyczności tokenu, aby mógł być zaufany przez aplikację. Tokeny Azure AD B2C są podpisane przy użyciu standardowych algorytmów szyfrowania asymetrycznego, takich jak RSA 256.

Nagłówek tokenu zawiera informacje na temat metody klucza i szyfrowania użytej do podpisania tokenu:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Wartość **alg** jest algorytmem, który został użyty do podpisania tokenu. Wartość **żądania jest** kluczem publicznym, który został użyty do podpisania tokenu. W dowolnym momencie Azure AD B2C może podpisać token przy użyciu dowolnego zestawu par kluczy publiczny-prywatny. Azure AD B2C okresowo obracać możliwy zestaw kluczy. Aplikacja powinna być zapisywana, aby automatycznie obsługiwać te zmiany kluczy. Rozsądna częstotliwość sprawdzania dostępności aktualizacji kluczy publicznych używanych przez Azure AD B2C wynosi co 24 godziny. Aby obsłużyć nieoczekiwane zmiany w kluczach, aplikacja powinna zostać zapisywana w celu ponownego pobrania kluczy publicznych, Jeśli odbierze nieoczekiwaną wartość dla **dzieci** .

Azure AD B2C ma punkt końcowy metadanych połączenia OpenID Connect. Za pomocą tego punktu końcowego aplikacje mogą zażądać informacji o Azure AD B2C w czasie wykonywania. Te informacje obejmują punkty końcowe, zawartość tokenu i klucze podpisywania tokenu. Dzierżawca Azure AD B2C zawiera dokument metadanych JSON dla każdej zasady. Dokument metadanych jest obiektem JSON, który zawiera kilka przydatnych informacji. Metadane zawierają **jwks_uri**, które dają lokalizację zestawu kluczy publicznych, które są używane do podpisywania tokenów. Ta lokalizacja jest tutaj dostępna, ale najlepiej jest pobrać lokalizację dynamicznie przy użyciu dokumentu metadanych i analizy **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Dokument JSON znajdujący się w tym adresie URL zawiera wszystkie informacje o kluczu publicznym używanym w danym momencie. Twoja aplikacja może użyć `kid` w nagłówku JWT żądania, aby wybrać klucz publiczny w dokumencie JSON, który jest używany do podpisywania określonego tokenu. Następnie może przeprowadzić walidację sygnatur przy użyciu poprawnego klucza publicznego i wskazanego algorytmu.

Dokument metadanych `B2C_1_signupsignin1` zasad w dzierżawie znajduje się `contoso.onmicrosoft.com` w lokalizacji:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Aby określić, które zasady zostały użyte do podpisania tokena (i gdzie należy przeznaczyć żądanie metadanych), dostępne są dwie opcje. Najpierw Nazwa zasad jest uwzględniona w `tfp` (domyślne) lub `acr` w ramach żądania (zgodnie z konfiguracją) w tokenie. Można analizować oświadczenia z treści JWT przez Base-64 dekodowanie treści i deserializacji ciągu JSON, który jest wynikiem. `tfp`Lub `acr` to nazwa zasad, które zostały użyte do wystawienia tokenu. Druga opcja polega na zakodowaniu zasad w wartości `state` parametru podczas wysyłania żądania, a następnie zdekodowaniu go w celu określenia, które zasady zostały użyte. Każda metoda jest prawidłowa.

Opis sposobu wykonywania walidacji podpisu znajduje się poza zakresem tego dokumentu. Dostępnych jest wiele bibliotek typu "open source", które ułatwiają sprawdzanie poprawności tokenu.

### <a name="validate-claims"></a>Weryfikuj oświadczenia

Gdy aplikacje lub interfejs API otrzymają Token identyfikatora, powinien również wykonać kilka operacji sprawdzenia względem oświadczeń w tokenie ID. Należy sprawdzić następujące oświadczenia:

- **odbiorcy** — weryfikuje, czy token identyfikatora został przewidziany dla aplikacji.
- **nie wcześniej** i **czas wygaśnięcia** — WERYFIKUJE, czy token identyfikatora nie wygasł.
- **wystawca** — weryfikuje, czy token został wystawiony dla aplikacji przez Azure AD B2C.
- Identyfikator **jednorazowy** — strategia rozwiązywania problemów z atakiem metodą powtórzeń tokenu.

Aby zapoznać się z pełną listą poprawności, którą aplikacja powinna wykonać, zapoznaj się ze [specyfikacją OpenID Connect Connect](https://openid.net).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [używania tokenów dostępu](access-tokens.md).

