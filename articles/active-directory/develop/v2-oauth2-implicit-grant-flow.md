---
title: OAuth 2.0 niejawny przepływ dotacji - Platforma tożsamości firmy Microsoft | Azure
description: Zabezpiecz aplikacje jednostronicowe przy użyciu niejawnego przepływu platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 89ae088b9cbb3bb3c593cfcbbfb4ce619baccfa8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868409"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Platforma tożsamości firmy Microsoft i niejawny przepływ dotacji

Za pomocą punktu końcowego platformy tożsamości firmy Microsoft można logować użytkowników do aplikacji jednostronicowych za pomocą kont osobistych i służbowych firmy Microsoft. Jednostronicowe i inne aplikacje JavaScript, które działają głównie w przeglądarce, stoją przed kilkoma interesującymi wyzwaniami, jeśli chodzi o uwierzytelnianie:

* Charakterystyka zabezpieczeń tych aplikacji znacznie różni się od tradycyjnych aplikacji sieci web opartych na serwerze.
* Wiele serwerów autoryzacji i dostawców tożsamości nie obsługuje żądań CORS.
* Przekierowania przeglądarki pełnej strony z dala od aplikacji stają się szczególnie inwazyjne dla środowiska użytkownika.

Dla tych aplikacji (Angular, Ember.js, React.js i tak dalej), platforma tożsamości firmy Microsoft obsługuje przepływ niejawnych dotacji OAuth 2.0. Niejawny przepływ jest opisany w [specyfikacji OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). Jego podstawową zaletą jest to, że umożliwia aplikacji, aby uzyskać tokeny z platformy tożsamości firmy Microsoft bez wykonywania wymiany poświadczeń serwera wewnętrznej bazy danych. Dzięki temu aplikacja do logowania użytkownika, obsługi sesji i uzyskać tokeny do innych interfejsów API sieci web w kodzie JavaScript klienta. Istnieje kilka ważnych kwestii bezpieczeństwa, które należy wziąć pod uwagę podczas korzystania z niejawnego przepływu w szczególności wokół [personifikacji](https://tools.ietf.org/html/rfc6749#section-10.3) [klienta](https://tools.ietf.org/html/rfc6749#section-10.3) i użytkownika.

W tym artykule opisano sposób programowania bezpośrednio względem protokołu w aplikacji.  Jeśli to możliwe, zaleca się użycie obsługiwanych bibliotek uwierzytelniania firmy Microsoft (MSAL) zamiast tego do [uzyskiwania tokenów i wywoływania zabezpieczonych interfejsów API sieci Web](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Zapoznaj się również z [przykładowymi aplikacjami korzystającymi z programu MSAL](sample-v2-code.md).

Jeśli jednak wolisz nie używać biblioteki w aplikacji jednostronicowej i samodzielnie wysyłać wiadomości z protokołów, wykonaj ogólne kroki opisane poniżej.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Odpowiednie scenariusze dla dotacji dorozumianej OAuth2

Specyfikacja OAuth2 oświadcza, że niejawna dotacja została opracowana w celu umożliwienia aplikacji agenta użytkownika — to znaczy aplikacji JavaScript wykonywanych w przeglądarce. Cechą definiującą takich aplikacji jest to, że kod JavaScript jest używany do uzyskiwania dostępu do zasobów serwera (zazwyczaj interfejsu API sieci web) i do odpowiedniego aktualizowania środowiska użytkownika aplikacji. Pomyśl o aplikacjach takich jak Gmail lub Outlook Web Access: po wybraniu wiadomości ze skrzynki odbiorczej tylko panel wizualizacji wiadomości zmienia się, aby wyświetlić nowe zaznaczenie, podczas gdy reszta strony pozostaje niezmieniona. Ta cecha jest w przeciwieństwie do tradycyjnych aplikacji sieci Web opartych na przekierowaniu, gdzie każda interakcja użytkownika powoduje pełny powrót strony i renderowanie całej strony nowej odpowiedzi serwera.

Aplikacje, które przyjmują podejście oparte na języku JavaScript do jego skrajności, są nazywane aplikacjami jednostronicowymi lub umowami SPA. Chodzi o to, że te aplikacje obsługują tylko początkową stronę HTML i powiązany javascript, a wszystkie późniejsze interakcje są napędzane przez wywołania internetowych interfejsów API wykonywane za pośrednictwem języka JavaScript. Jednak podejścia hybrydowe, gdzie aplikacja jest głównie postback-driven, ale wykonuje sporadyczne wywołania JS, nie są rzadkością — dyskusja na temat użycia przepływu niejawnego jest istotne dla tych, jak również.

Aplikacje oparte na przekierowaniach zazwyczaj zabezpieczają swoje żądania za pomocą plików cookie, jednak takie podejście nie działa równie dobrze w przypadku aplikacji JavaScript. Pliki cookie działają tylko na domenę, dla których zostały wygenerowane, podczas gdy połączenia JavaScript mogą być kierowane do innych domen. W rzeczywistości często tak będzie: pomyśl o aplikacjach wywołujących interfejs API programu Microsoft Graph, interfejs API pakietu Office, interfejs API platformy Azure — wszystkie przebywające poza domeną, z której jest obsługiwana aplikacja. Rosnącą tendencją dla aplikacji JavaScript jest brak zaplecza w ogóle, opierając się w 100% na interfejsach API internetowych innych firm, aby zaimplementować swoją funkcję biznesową.

Obecnie preferowaną metodą ochrony wywołań do internetowego interfejsu API jest użycie podejścia tokenu nośnika OAuth2, gdzie każdemu wywołaniu towarzyszy token dostępu OAuth2. Internetowy interfejs API sprawdza token dostępu przychodzącego i, jeśli znajdzie w nim niezbędne zakresy, udziela dostępu do żądanej operacji. Niejawny przepływ zapewnia wygodny mechanizm dla aplikacji JavaScript w celu uzyskania tokenów dostępu dla internetowego interfejsu API, oferując liczne korzyści w odniesieniu do plików cookie:

* Tokeny można niezawodnie uzyskać bez konieczności wykonywania połączeń między źródłami – obowiązkowa rejestracja identyfikatora URI przekierowania, do którego tokeny są gwarancjami zwrotu, że tokeny nie są przemieszczane
* Aplikacje JavaScript mogą uzyskać tyle tokenów dostępu, ile potrzebują, dla tylu interfejsów API sieci Web, na które są kierowane — bez ograniczeń dotyczących domen
* Funkcje HTML5, takie jak sesja lub pamięć lokalna, zapewniają pełną kontrolę nad buforowaniem tokenów i zarządzaniem okresem istnienia, podczas gdy zarządzanie plikami cookie jest nieprzezroczyste dla aplikacji
* Tokeny dostępu nie są podatne na ataki fałszerstwa żądań między witrynami (CSRF)

Niejawny przepływ dotacji nie wystawia tokenów odświeżania, głównie ze względów bezpieczeństwa. Token odświeżania nie jest tak wąsko objęty jak tokeny dostępu, co daje znacznie więcej mocy, co powoduje znacznie więcej obrażeń w przypadku wycieku. W przepływie niejawnym tokeny są dostarczane w adresie URL, w związku z tym ryzyko przechwycenia jest wyższa niż w grant kodu autoryzacji.

Jednak aplikacja JavaScript ma inny mechanizm do dyspozycji do odnawiania tokenów dostępu bez wielokrotnego monitowania użytkownika o poświadczenia. Aplikacja może używać ukrytego elementu iframe do wykonywania nowych żądań tokenu względem punktu końcowego autoryzacji usługi Azure AD: tak długo, jak przeglądarka nadal ma aktywną sesję (przeczytaj: ma plik cookie sesji) względem domeny usługi Azure AD, żądanie uwierzytelniania może pomyślnie wystąpić bez konieczności interakcji z użytkownikiem.

Ten model daje aplikacji JavaScript możliwość niezależnego odnawiania tokenów dostępu, a nawet nabywania nowych dla nowego interfejsu API (pod warunkiem, że użytkownik wcześniej wyraził na nie zgodę). Pozwala to uniknąć dodatkowego obciążenia nabycia, konserwacji i ochrony artefaktu o wysokiej wartości, takiego jak token odświeżania. Artefakt, który umożliwia ciche odnawianie, plik cookie sesji usługi Azure AD, jest zarządzany poza aplikacją. Inną zaletą tego podejścia jest użytkownik może wylogować się z usługi Azure AD, przy użyciu dowolnej aplikacji zalogowanych do usługi Azure AD, uruchomionych na dowolnej karcie przeglądarki. Powoduje to usunięcie pliku cookie sesji usługi Azure AD, a aplikacja JavaScript automatycznie utraci możliwość odnawiania tokenów dla wylogowanego użytkownika.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Czy dotacja niejawna jest odpowiednia dla mojej aplikacji?

Dotacja dorozumiana stanowi więcej ryzyka niż inne dotacje, a obszary, na które należy zwrócić uwagę, są dobrze udokumentowane (na przykład [Niewłaściwe użycie tokenu dostępu w celu personifikacji właściciela zasobu w przepływie niejawnym][OAuth2-Spec-Implicit-Misuse] i [oauth 2.0 Threat Model i zagadnienia dotyczące zabezpieczeń][OAuth2-Threat-Model-And-Security-Implications]). Jednak wyższy profil ryzyka jest w dużej mierze ze względu na fakt, że jest przeznaczony do włączenia aplikacji, które wykonują aktywny kod, obsługiwane przez zdalnego zasobu do przeglądarki. Jeśli planujesz architekturę SPA, nie masz składników wewnętrznej bazy danych lub zamierzasz wywołać internetowy interfejs API za pośrednictwem języka JavaScript, zaleca się użycie niejawnego przepływu do pozyskiwania tokenów.

Jeśli aplikacja jest klientem macierzystym, niejawny przepływ nie jest doskonałym rozwiązaniem. Brak pliku cookie sesji usługi Azure AD w kontekście klienta macierzystego pozbawia aplikacji ze środków utrzymania długotrwałej sesji. Oznacza to, że aplikacja będzie wielokrotnie monitować użytkownika podczas uzyskiwania tokenów dostępu dla nowych zasobów.

Jeśli tworzysz aplikację sieci Web, która zawiera wewnętrznej bazy danych i korzystanie z interfejsu API z jego kodu wewnętrznej bazy danych, niejawny przepływ również nie jest dobrym rozwiązaniem. Inne dotacje dają znacznie więcej mocy. Na przykład poświadczeń klienta OAuth2 udzielić zapewnia możliwość uzyskania tokenów, które odzwierciedlają uprawnienia przypisane do samej aplikacji, w przeciwieństwie do delegowania użytkowników. Oznacza to, że klient ma możliwość utrzymania programowego dostępu do zasobów, nawet jeśli użytkownik nie jest aktywnie zaangażowany w sesję i tak dalej. Nie tylko to, ale takie dotacje dają wyższe gwarancje bezpieczeństwa. Na przykład tokeny dostępu nigdy nie są przesyłane przez przeglądarkę użytkownika, nie ryzykują zapisania ich w historii przeglądarki i tak dalej. Aplikacja kliencka może również wykonywać silne uwierzytelnianie podczas żądania tokenu.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Diagram protokołu

Na poniższym diagramie przedstawiono, jak wygląda cały niejawny przepływ logowania, a poniższe sekcje opisują każdy krok bardziej szczegółowo.

![Diagram przedstawiający niejawny przepływ logowania](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Wyślij żądanie logowania

Aby początkowo zalogować użytkownika do aplikacji, można wysłać żądanie `id_token` uwierzytelniania [OpenID Connect](v2-protocols-oidc.md) i uzyskać z punktu końcowego platformy tożsamości firmy Microsoft.

> [!IMPORTANT]
> Aby pomyślnie zażądać tokenu identyfikatora i/lub tokenu dostępu, rejestracja aplikacji na stronie [Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) musi mieć włączony odpowiedni niejawny przepływ dotacji, wybierając **tokeny identyfikatorów** i.lub **tokeny dostępu** w sekcji **Niejawne przyznanie.** Jeśli nie jest włączona, `unsupported_response` zostanie zwrócony błąd: **podana wartość parametru wejściowego "response_type" nie jest dozwolona dla tego klienta. Oczekiwaną wartością jest "kod"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Aby przetestować logowanie przy <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> użyciu przepływu niejawnego, kliknij przycisk . Po zalogowaniu się przeglądarka powinna `https://localhost/myapp/` zostać `id_token` przekierowana do z paskiem adresu.
>

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` | wymagany |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` `organizations`, `consumers`, i identyfikatory dzierżawy. Aby uzyskać więcej informacji, zobacz [podstawy protokołu](active-directory-v2-protocols.md#endpoints). |
| `client_id` | wymagany | Identyfikator aplikacji (klienta), który [strona Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) jest przypisana do aplikacji. |
| `response_type` | wymagany |Musi `id_token` zawierać dla openid connect logowania. Może również obejmować response_type `token`. Za `token` pomocą w tym miejscu pozwoli aplikacji do odbierania tokenu dostępu natychmiast z punktu końcowego autoryzacji bez konieczności wysuwać drugie żądanie do punktu końcowego autoryzacji. Jeśli używasz `token` response_type, `scope` parametr musi zawierać zakres wskazujący, dla którego zasobu ma zostać wystawiony token (na przykład user.read na microsoft graph).  |
| `redirect_uri` | zalecane |Redirect_uri aplikacji, w której aplikacja może wysyłać i odbierać odpowiedzi na uwierzytelnianie. Musi dokładnie odpowiadać jednej z redirect_uris zarejestrowanej w portalu, z tą różnicą, że musi być zakodowany adres URL. |
| `scope` | wymagany |Oddzielona spacja lista [zakresów](v2-permissions-and-consent.md). W przypadku OpenID Connect (id_tokens) musi `openid`zawierać zakres, który przekłada się na uprawnienie "Zaloguj się" w interfejsie użytkownika zgody. Opcjonalnie można również dołączyć `email` zakresy i `profile` możliwości uzyskania dostępu do dodatkowych danych użytkownika. Można również uwzględnić inne zakresy w tym żądaniu zgody na różne zasoby, jeśli żądany jest token dostępu. |
| `response_mode` | optional |Określa metodę, która powinna służyć do wysyłania tokenu wynikowego z powrotem do aplikacji. Domyślnie kwerendy tylko token dostępu, ale fragment, jeśli żądanie zawiera id_token. |
| `state` | zalecane |Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości, którą chcesz. Losowo generowana unikatowa wartość jest zwykle używana do [zapobiegania atakom fałszerskości żądań między witrynami.](https://tools.ietf.org/html/rfc6749#section-10.12) Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |
| `nonce` | wymagany |Wartość uwzględniona w żądaniu, wygenerowana przez aplikację, która zostanie uwzględniona w wynikowym id_token jako oświadczenie. Aplikacja może następnie zweryfikować tę wartość, aby ograniczyć ataki powtarzania tokenów. Wartość jest zazwyczaj losowo, unikatowy ciąg, który może służyć do identyfikowania pochodzenia żądania. Wymagane tylko wtedy, gdy wymagane jest id_token. |
| `prompt` | optional |Wskazuje typ interakcji użytkownika, który jest wymagany. Jedynymi prawidłowymi wartościami w tej chwili są "login", "none", "select_account" i "consent". `prompt=login`wymusi użytkownikowi wprowadzenie poświadczeń w tym żądaniu, negując jednokrotne zalogowanie. `prompt=none`jest odwrotnie - zapewni, że użytkownik nie zostanie przedstawiony z żadnym interaktywnym monitem. Jeśli nie można ukończyć żądania po cichu za pomocą logowania jednokrotnego, punkt końcowy platformy tożsamości firmy Microsoft zwróci błąd. `prompt=select_account`wysyła użytkownika do selektora kont, w którym pojawią się wszystkie konta zapamiętane w sesji. `prompt=consent`wyzwoli okno dialogowe zgody OAuth po zalogowaniu się użytkownika, prosząc użytkownika o przyznanie uprawnień do aplikacji. |
| `login_hint`  |optional |Może służyć do wstępnego wypełnienia pola nazwy użytkownika/adresu e-mail strony logowania dla użytkownika, jeśli znasz jego nazwę użytkownika z wyprzedzeniem. Często aplikacje będą używać tego parametru podczas ponownego uwierzytelniania, po wyodrębnieniu nazwy użytkownika z poprzedniego logowania przy użyciu `preferred_username` oświadczenia.|
| `domain_hint` | optional |Jeśli zostanie uwzględniony, pominie proces odnajdywania oparty na wiadomości e-mail, który użytkownik przechodzi na stronie logowania, co prowadzi do nieco bardziej usprawnione środowisko użytkownika. Jest to powszechnie używane dla aplikacji typu Line of Business, które działają w jednej dzierżawie, gdzie będą dostarczać nazwę domeny w obrębie danej dzierżawy.  Spowoduje to przekazanie użytkownika do dostawcy federacji dla tej dzierżawy.  Należy pamiętać, że uniemożliwi to gościom zalogowanie się do tej aplikacji.  |

W tym momencie użytkownik zostanie poproszony o wprowadzenie swoich poświadczeń i zakończenie uwierzytelniania. Punkt końcowy platformy tożsamości firmy Microsoft zapewni również, że użytkownik wyraził `scope` zgodę na uprawnienia wskazane w parametrze zapytania. Jeśli użytkownik nie wyraził zgody na **żadne** z tych uprawnień, poprosi użytkownika o zgodę na wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [uprawnienia, zgody i aplikacje z wieloma dzierżawcami](v2-permissions-and-consent.md).

Gdy użytkownik uwierzytelnia się i udzieli zgody, punkt końcowy platformy tożsamości firmy `redirect_uri`Microsoft zwróci odpowiedź do `response_mode` aplikacji we wskazanym , przy użyciu metody określonej w parametrze.

#### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź `response_mode=fragment` `response_type=id_token+token` przy użyciu i wygląda następująco (z podziałami wierszy dla czytelności):

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parametr | Opis |
| --- | --- |
| `access_token` |W zestawie, jeśli `response_type` zawiera `token`. Token dostępu, o który prosiła aplikacja. Token dostępu nie powinny być dekodowane lub w inny sposób kontrolowane, powinny być traktowane jako nieprzezroczysty ciąg. |
| `token_type` |W zestawie, jeśli `response_type` zawiera `token`. Zawsze będzie `Bearer`. |
| `expires_in`|W zestawie, jeśli `response_type` zawiera `token`. Wskazuje liczbę sekund token jest prawidłowy, do celów buforowania. |
| `scope` |W zestawie, jeśli `response_type` zawiera `token`. Wskazuje zakres(y), dla którego access_token będzie prawidłowy. Może nie zawierać wszystkich żądanych zakresów, jeśli nie miały one zastosowania do użytkownika (w przypadku zakresów tylko usługi Azure AD są wymagane, gdy konto osobiste jest używane do logowania). |
| `id_token` | Podpisany token JSON Web Token (JWT). Aplikacja może dekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie powinna polegać na nich dla żadnych granic autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat [`id_token reference`](id-tokens.md)id_tokens, zobacz . <br> **Uwaga:** Tylko pod `openid` warunkiem, że zakres był wymagany. |
| `state` |Jeśli parametr stanu znajduje się w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do aplikacji, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` |Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Ciche uzyskiwanie tokenów dostępu w tle

Po podpisaniu użytkownika w aplikacji jednostronicowej można po cichu uzyskać tokeny dostępu do wywoływania internetowych interfejsów API zabezpieczonych przez platformę tożsamości firmy Microsoft, takich jak [Microsoft Graph](https://developer.microsoft.com/graph). Nawet jeśli już odebrano `token` token przy użyciu response_type, można użyć tej metody do uzyskania tokenów do dodatkowych zasobów bez konieczności przekierowywania użytkownika, aby zalogować się ponownie.

W normalnym przepływie OpenID Connect/OAuth można to zrobić, wysyłając `/token` żądanie do punktu końcowego platformy tożsamości firmy Microsoft. Jednak punkt końcowy platformy tożsamości firmy Microsoft nie obsługuje żądań CORS, więc wykonywanie wywołań AJAX, aby uzyskać i odświeżyć tokeny nie jest kwestią. Zamiast tego można użyć przepływu niejawnego w ukrytym iframe, aby uzyskać nowe tokeny dla innych interfejsów API sieci web:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Aby uzyskać szczegółowe informacje na temat parametrów zapytania w adresie URL, zobacz [wysyłanie żądania logowania](#send-the-sign-in-request).

> [!TIP]
> Spróbuj skopiować & wklejając poniższe żądanie do karty przeglądarki! (Nie zapomnij zastąpić `login_hint` wartości z poprawną wartością dla użytkownika)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Dzięki parametrowi `prompt=none` to żądanie zakończy się pomyślnie lub zakończy się niepowodzeniem natychmiast i powróci do aplikacji. Pomyślna odpowiedź zostanie wysłana do aplikacji `redirect_uri`na wskazane , `response_mode` przy użyciu metody określonej w parametrze.

#### <a name="successful-response"></a>Skuteczna reakcja

Pomyślna odpowiedź `response_mode=fragment` za pomocą wygląda następująco:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parametr | Opis |
| --- | --- |
| `access_token` |W zestawie, jeśli `response_type` zawiera `token`. Token dostępu, który aplikacja zażądała, w tym przypadku dla programu Microsoft Graph. Token dostępu nie powinny być dekodowane lub w inny sposób kontrolowane, powinny być traktowane jako nieprzezroczysty ciąg. |
| `token_type` | Zawsze będzie `Bearer`. |
| `expires_in` | Wskazuje liczbę sekund token jest prawidłowy, do celów buforowania. |
| `scope` | Wskazuje zakres(y), dla którego access_token będzie prawidłowy. Może nie zawierać wszystkich żądanych zakresów, jeśli nie miały one zastosowania do użytkownika (w przypadku zakresów tylko usługi Azure AD są wymagane, gdy konto osobiste jest używane do logowania). |
| `id_token` | Podpisany token JSON Web Token (JWT). W zestawie, jeśli `response_type` zawiera `id_token`. Aplikacja może dekodować segmenty tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie powinna polegać na nich dla żadnych granic autoryzacji lub zabezpieczeń. Aby uzyskać więcej informacji na temat id_tokens, zobacz [ `id_token` odwołanie](id-tokens.md). <br> **Uwaga:** Tylko pod `openid` warunkiem, że zakres był wymagany. |
| `state` |Jeśli parametr stanu znajduje się w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne. |

#### <a name="error-response"></a>Odpowiedź na błąd

Odpowiedzi na błędy mogą być `redirect_uri` również wysyłane do aplikacji, dzięki czemu aplikacja może obsługiwać je odpowiednio. W przypadku `prompt=none`oczekiwanego błędu będzie:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametr | Opis |
| --- | --- |
| `error` |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` |Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu uwierzytelniania. |

Jeśli ten błąd w żądaniu elementu iframe, użytkownik musi interaktywnie zalogować się ponownie, aby pobrać nowy token. Możesz obsługiwać tę sprawę w jakikolwiek sposób ma sens dla aplikacji.

## <a name="refreshing-tokens"></a>Odświeżanie tokenów

Niejawna dotacja nie zapewnia tokenów odświeżania. Zarówno `id_token`s, jak i `access_token`s wygasną po krótkim czasie, więc aplikacja musi być przygotowana do okresowego odświeżania tych tokenów. Aby odświeżyć każdy typ tokenu, można wykonać to samo `prompt=none` ukryte żądanie elementu iframe z góry przy użyciu parametru do kontrolowania zachowania platformy tożsamości. Jeśli chcesz otrzymać nowy `id_token`, należy `id_token` użyć `response_type` w `scope=openid`i , `nonce` jak również parametr.

## <a name="send-a-sign-out-request"></a>Wyślij żądanie wylogowywania się

OpenID Connect `end_session_endpoint` umożliwia aplikacji wysłanie żądania do punktu końcowego platformy tożsamości firmy Microsoft, aby zakończyć sesję użytkownika i wyczyścić pliki cookie ustawione przez punkt końcowy platformy tożsamości firmy Microsoft. Aby w pełni wylogować użytkownika z aplikacji sieci web, aplikacja powinna zakończyć własną sesję z użytkownikiem (zwykle przez wyczyszczenie pamięci podręcznej tokenu lub upuszczenie plików cookie), a następnie przekierować przeglądarkę do:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parametr |  | Opis |
| --- | --- | --- |
| `tenant` |wymagany |Wartość `{tenant}` w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to `common` `organizations`, `consumers`, i identyfikatory dzierżawy. Aby uzyskać więcej informacji, zobacz [podstawy protokołu](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | zalecane | Adres URL, do który użytkownik powinien zwrócić po zakończeniu wylogowania. Ta wartość musi być zgodna z jednym z identyfikatorów URI przekierowania zarejestrowanych dla aplikacji. Jeśli nie zostanie uwzględniony, użytkownik zostanie wyświetlony ogólny komunikat przez punkt końcowy platformy tożsamości firmy Microsoft. |

## <a name="next-steps"></a>Następne kroki

* Przejdź przez [przykłady MSAL JS,](sample-v2-code.md) aby rozpocząć kodowanie.
