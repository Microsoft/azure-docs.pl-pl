---
title: Zakresy, uprawnienia i zgody platformy tożsamości firmy Microsoft
description: Dowiedz się więcej na temat autoryzacji w punkcie końcowym platformy tożsamości firmy Microsoft, w tym zakresów, uprawnień i zgody.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperfq1, identityplatformtop40
ms.openlocfilehash: 79475414f6785474596beae208fefae81a673dea
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842686"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft

Aplikacje integrowane z platformą tożsamości firmy Microsoft są zgodne z modelem autoryzacji, który umożliwia użytkownikom i administratorom kontrolę nad sposobem uzyskiwania dostępu do danych. Implementacja modelu autoryzacji została zaktualizowana w punkcie końcowym platformy tożsamości firmy Microsoft i zmienia sposób, w jaki aplikacja musi współdziałać z platformą tożsamości firmy Microsoft. W tym artykule opisano podstawowe koncepcje tego modelu autoryzacji, w tym zakresy, uprawnienia i zgodę.

## <a name="scopes-and-permissions"></a>Zakresy i uprawnienia

Platforma tożsamości firmy Microsoft implementuje protokół autoryzacji [OAuth 2,0](active-directory-v2-protocols.md) . OAuth 2,0 to metoda, za pomocą której aplikacja innej firmy może uzyskać dostęp do zasobów hostowanych w sieci Web w imieniu użytkownika. Wszystkie zasoby hostowane w sieci Web, które integrują się z platformą tożsamości firmy Microsoft, mają identyfikator zasobu lub identyfikator *URI aplikacji*. Na przykład niektóre z zasobów internetowych firmy Microsoft obejmują:

* Microsoft Graph: `https://graph.microsoft.com`
* Interfejs API poczty Microsoft 365: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

> [!NOTE]
> Zdecydowanie zalecamy używanie Microsoft Graph zamiast interfejsu API poczty Microsoft 365, itp.

Ta sama wartość dotyczy wszystkich zasobów innych firm, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Dowolny z tych zasobów może również definiować zestaw uprawnień, które mogą służyć do dzielenia funkcjonalności tego zasobu na mniejsze fragmenty. Na przykład [Microsoft Graph](https://graph.microsoft.com) ma zdefiniowane uprawnienia do wykonywania następujących zadań, między innymi:

* Odczytaj kalendarz użytkownika
* Zapisz w kalendarzu użytkownika
* Wyślij wiadomość jako użytkownika

Definiując te typy uprawnień, zasób ma szczegółową kontrolę nad swoimi danymi oraz sposób ujawniania funkcjonalności interfejsu API. Aplikacja innej firmy może zażądać tych uprawnień od użytkowników i administratorów, którzy muszą zatwierdzić żądanie, zanim aplikacja będzie mogła uzyskać dostęp do danych lub wykonać działania w imieniu użytkownika. Dzieląc funkcjonalność zasobu na mniejsze zestawy uprawnień, aplikacje innych firm można skompilować tak, aby żądały tylko określonych uprawnień, których potrzebują do wykonywania ich funkcji. Użytkownicy i Administratorzy mogą dokładnie znać dane, do których aplikacja ma dostęp, i mogą być bardziej przekonane, że nie zachowuje złośliwego celu. Deweloperzy powinni zawsze przestrzegać koncepcji najniższych uprawnień, zwracając tylko uprawnienia potrzebne do działania aplikacji.

W przypadku uwierzytelniania OAuth 2,0 te typy uprawnień są nazywane *zakresami*. Są one również często określane jako *uprawnienia*. Uprawnienie jest reprezentowane na platformie tożsamości firmy Microsoft jako wartość ciągu. Kontynuując Microsoft Graph przykład, wartość ciągu dla każdego uprawnienia to:

* Odczytaj kalendarz użytkownika przy użyciu `Calendars.Read`
* Zapisywanie w kalendarzu użytkownika przy użyciu `Calendars.ReadWrite`
* Wyślij wiadomość jako użytkownika używaną przez `Mail.Send`

Aplikacja najczęściej żąda tych uprawnień, określając zakresy w żądaniach do autoryzowanego punktu końcowego platformy tożsamości firmy Microsoft. Jednak pewne uprawnienia o wysokim poziomie uprawnień mogą być udzielane tylko przez zgodę administratora i wymagane/udzielone za pomocą [punktu końcowego zgody administratora](#admin-restricted-permissions). Przeczytaj, aby dowiedzieć się więcej.

## <a name="permission-types"></a>Typy uprawnień

Platforma tożsamości firmy Microsoft obsługuje dwa typy uprawnień: **delegowane uprawnienia** i **uprawnienia do aplikacji**.

* **Delegowane uprawnienia** są używane przez aplikacje, które mają obecny zalogowany użytkownik. W przypadku tych aplikacji użytkownik lub administrator wyraża zgodę na uprawnienia, które aplikacja żąda, a aplikacja jest delegowana do działania jako zalogowany użytkownik podczas wykonywania wywołań do zasobu docelowego. Niektóre uprawnienia delegowane mogą być przydzielone przez użytkowników niebędących administratorami, ale niektóre uprawnienia z wyższymi uprawnieniami wymagają [zgody administratora](#admin-restricted-permissions). Aby dowiedzieć się, które role administratorów mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Uprawnienia aplikacji** są używane przez aplikacje, które są uruchamiane bez zalogowanego użytkownika. na przykład aplikacje, które działają jako usługi lub demony w tle.  Uprawnienia aplikacji mogą być [wysyłane tylko przez administratora](#requesting-consent-for-an-entire-tenant).

_Czynne uprawnienia_ to uprawnienia, które aplikacja będzie miała podczas wykonywania żądań do zasobu docelowego. Ważne jest, aby zrozumieć różnicę między uprawnieniami delegowanymi i aplikacjami, do których przyznano aplikację, a jej obowiązującymi uprawnieniami podczas wykonywania wywołań do zasobu docelowego.

- W przypadku uprawnień delegowanych _czynne uprawnienia_ aplikacji są najniższymi przyznanymi uprawnieniami delegowanymi (za pośrednictwem zgody) i uprawnieniami aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. Uprawnienia zalogowanego użytkownika mogą być określone w organizacji na podstawie zasad lub członkostwa w co najmniej jednej roli administratora. Aby dowiedzieć się, które role administratorów mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Załóżmy na przykład, że aplikacja ma przyznane uprawnienie _User. ReadWrite. All_ delegowane. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja będzie mogła zaktualizować profil każdego użytkownika w organizacji. Jeśli jednak zalogowany użytkownik nie znajduje się w roli administratora, aplikacja będzie mogła aktualizować tylko profil zalogowanego użytkownika. Użytkownik nie będzie mógł zaktualizować profili innych użytkowników w organizacji, ponieważ użytkownik mający uprawnienia do działania w imieniu innych osób nie ma odpowiednich uprawnień.

- W przypadku uprawnień aplikacji _czynne uprawnienia_ aplikacji będą pełen poziom uprawnień IMPLIKOWANYCH przez uprawnienie. Na przykład aplikacja z uprawnieniem _User. ReadWrite. All_ aplikacji może aktualizować profil każdego użytkownika w organizacji.

## <a name="openid-connect-scopes"></a>Zakresy połączeń OpenID Connect

Implementacja platformy tożsamości firmy Microsoft w programie OpenID Connect Connect ma kilka dobrze zdefiniowanych zakresów, które są również hostowane w Microsoft Graph: `openid` , `email` , `profile` i `offline_access` . `address` `phone` Zakresy połączeń i OpenID Connect nie są obsługiwane.

Żądanie zakresów OIDC i tokenu spowoduje nadanie tokenowi wywołania [punktu końcowego UserInfo](userinfo.md).

### <a name="openid"></a>OpenID Connect

Jeśli aplikacja wykonuje Logowanie przy użyciu programu [OpenID Connect Connect](active-directory-v2-protocols.md), musi zażądać `openid` zakresu. `openid`Zakres jest wyświetlany na stronie wyrażanie zgody na konto służbowe jako uprawnienie "Logowanie do Ciebie" oraz na stronie osobistej zgody konto Microsoft jako "Wyświetlanie profilu i łączenie się z aplikacjami i usługami przy użyciu konto Microsoft". Dzięki temu uprawnieniu aplikacja może otrzymać unikatowy identyfikator użytkownika w postaci `sub` zgłoszenia. Zapewnia również dostęp aplikacji do punktu końcowego UserInfo. `openid`Zakresu można użyć w punkcie końcowym tokenów platformy tożsamości firmy Microsoft w celu uzyskania tokenów identyfikatorów, które mogą być używane przez aplikację do uwierzytelniania.

### <a name="email"></a>poczta e-mail

`email`Zakres może być używany z `openid` zakresem i wszelkimi innymi. Daje ona aplikacji dostęp do podstawowego adresu e-mail użytkownika w postaci `email` zgłoszenia. `email`Oświadczenia jest uwzględniany w tokenie tylko wtedy, gdy adres e-mail jest skojarzony z kontem użytkownika, co nie zawsze jest przypadkiem. Jeśli używa tego `email` zakresu, aplikacja powinna zostać przygotowana do obsługi przypadku, w którym `email` nie istnieje w tokenie.

### <a name="profile"></a>profil

`profile`Zakres może być używany z `openid` zakresem i wszelkimi innymi. Zapewnia ona aplikacji dostęp do znacznej ilości informacji o użytkowniku. Informacje, do których może uzyskać dostęp, obejmują, ale nie są ograniczone do, podanej nazwy, imienia, preferowanej nazwy użytkownika i identyfikatora obiektu. Aby uzyskać pełną listę oświadczeń profilu dostępnych w parametrze id_tokens określonego użytkownika, zobacz [ `id_tokens` odwołanie](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Zakres](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) umożliwia aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie zgoda ten zakres jest wyświetlany jako uprawnienie "Obsługuj dostęp do danych, do których masz dostęp. Gdy użytkownik zatwierdza `offline_access` zakres, aplikacja może odbierać tokeny odświeżania z punktu końcowego tokenu Microsoft Identity platform. Tokeny odświeżania są długotrwałe. Twoja aplikacja może uzyskać nowe tokeny dostępu, ponieważ wygasły.

> [!NOTE]
> To uprawnienie pojawia się na wszystkich ekranach wyrażania zgody dzisiaj, nawet w przypadku przepływów, które nie zapewniają tokenu odświeżania ( [przepływ niejawny](v2-oauth2-implicit-grant-flow.md)).  Dotyczy to scenariuszy, w których klient może rozpocząć pracę w niejawnym przepływie, a następnie przechodzić do przepływu kodu, w którym oczekiwany jest token odświeżania.

Na platformie tożsamości firmy Microsoft (żądania wysłane do punktu końcowego v 2.0) aplikacja musi jawnie zażądać `offline_access` zakresu, aby otrzymywać tokeny odświeżania. Oznacza to, że po zrealizowaniu kodu autoryzacji w [przepływie kodu autoryzacji OAuth 2,0](active-directory-v2-protocols.md)otrzymasz tylko token dostępu z `/token` punktu końcowego. Token dostępu jest ważny przez krótki czas. Token dostępu zazwyczaj wygasa w ciągu godziny. W tym momencie aplikacja musi przekierować użytkownika z powrotem do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji. W trakcie tego przekierowania, w zależności od typu aplikacji, użytkownik może potrzebować ponownie wprowadzić swoje poświadczenia lub wyrazić zgodę ponownie na uprawnienia.

Aby uzyskać więcej informacji na temat uzyskiwania i używania tokenów odświeżania, zobacz [informacje dotyczące protokołu Microsoft Identity platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Żądanie zgody poszczególnych użytkowników

W żądaniu autoryzacji [OpenID Connect Connect lub OAuth 2,0](active-directory-v2-protocols.md) aplikacja może zażądać wymaganych uprawnień przy użyciu `scope` parametru zapytania. Na przykład gdy użytkownik loguje się do aplikacji, aplikacja wysyła żądanie podobne do następującego przykładu (z podziałami wierszy dodanymi do czytelności):

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope`Parametr jest rozdzielaną spacją listą uprawnień delegowanych, które żąda aplikacja. Każde uprawnienie jest wskazywane przez dołączenie wartości uprawnienia do identyfikatora zasobu (identyfikator URI aplikacji). W przykładzie żądania aplikacja musi mieć uprawnienia do odczytu kalendarza użytkownika i wysyłania poczty jako użytkownik.

Po wprowadzeniu poświadczeń przez użytkownika punkt końcowy platformy tożsamości firmy Microsoft wyszukuje pasujący rekord *zgody użytkownika*. Jeśli użytkownik nie wyraził zgody na jakiekolwiek z żądanych uprawnień w przeszłości, ani nie ma dostępu administratora do tych uprawnień w imieniu całej organizacji, punkt końcowy platformy tożsamości firmy Microsoft prosi użytkownika o przyznanie żądanych uprawnień.

> [!NOTE]
>W tej chwili `offline_access` uprawnienia ("zapewnianie dostępu do danych, do których masz dostęp, do") i `user.read` ("Logowanie przy użyciu profilu") są automatycznie dołączane do początkowej zgody na aplikację.  Te uprawnienia są zwykle wymagane w celu uzyskania odpowiedniej funkcjonalności aplikacji — umożliwia aplikacjom `offline_access` dostęp do tokenów odświeżania, krytycznych dla aplikacji natywnych i sieci Web, a jednocześnie `user.read` zapewnia dostęp do tego `sub` żądania, dzięki czemu klient lub aplikacja może prawidłowo identyfikować użytkownika w czasie i uzyskać dostęp do informacji o użytkowniku podstawowe.

![Przykładowy zrzut ekranu pokazujący zgodę na konto służbowe](./media/v2-permissions-and-consent/work_account_consent.png)

Gdy użytkownik zatwierdzi żądanie uprawnienia, zostanie zarejestrowana zgoda i użytkownik nie musi ponownie wyrazić zgody na kolejne logowania do aplikacji.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żądanie zgody dla całej dzierżawy

Często gdy organizacja kupuje licencję lub subskrypcję aplikacji, organizacja chce aktywnie skonfigurować aplikację do użycia przez wszystkich członków organizacji. W ramach tego procesu administrator może udzielić zgody aplikacji na działanie w imieniu dowolnego użytkownika w dzierżawie. Jeśli administrator udzieli zgody dla całej dzierżawy, użytkownicy organizacji nie będą widzieć strony zgody dla aplikacji.

Aby zażądać zgody na delegowane uprawnienia dla wszystkich użytkowników w dzierżawie, aplikacja może korzystać z punktu końcowego zgody użytkownika.

Ponadto aplikacje muszą używać punktu końcowego zgody administratora, aby zażądać uprawnień aplikacji.

## <a name="admin-restricted-permissions"></a>Uprawnienia ograniczone przez administratora

Niektóre uprawnienia o wysokim poziomie uprawnień w ekosystemie firmy Microsoft można ustawić na wartość z *ograniczeniami administratora*. Przykłady tych rodzajów uprawnień obejmują następujące elementy:

* Odczytuj pełne profile wszystkich użytkowników za pomocą `User.Read.All`
* Zapisywanie danych w katalogu organizacji przy użyciu `Directory.ReadWrite.All`
* Odczytuj wszystkie grupy w katalogu organizacji przy użyciu `Groups.Read.All`

Mimo że użytkownik może udzielić aplikacji dostępu do tego rodzaju danych, użytkownicy w organizacji nie mogą udzielać dostępu do tego samego zestawu poufnych danych firmowych. Jeśli aplikacja zażąda dostępu do jednego z tych uprawnień od użytkownika w organizacji, zostanie wyświetlony komunikat o błędzie informujący, że nie są uprawnieni do wyrażania zgody na uprawnienia aplikacji.

Jeśli aplikacja wymaga dostępu do zakresów z ograniczeniami administratora dla organizacji, należy zażądać ich bezpośrednio od administratora firmy, a także za pomocą punktu końcowego zgody administratora, opisanego dalej.

Jeśli aplikacja żąda uprawnień delegowanych o wysokim poziomie uprawnień, a administrator przyznaje te uprawnienia za pośrednictwem punktu końcowego zgody na administratora, zostanie udzielony zgodę na wszystkich użytkowników w dzierżawie.

Jeśli aplikacja żąda uprawnień aplikacji, a administrator przyznaje te uprawnienia za pośrednictwem punktu końcowego zgody na administratora, to to uprawnienie nie jest wykonywane w imieniu określonego użytkownika. Zamiast tego do aplikacji klienckiej uzyskuje się *bezpośrednie*uprawnienia. Te typy uprawnień są używane tylko przez usługi demona i inne nieinteraktywne aplikacje działające w tle.

## <a name="using-the-admin-consent-endpoint"></a>Korzystanie z punktu końcowego zgody administratora

> [!NOTE]
> Należy pamiętać, że po udzieleniu zgody administratora przy użyciu punktu końcowego zgody administratora zakończono udzielanie zgody administratora, a użytkownicy nie muszą wykonywać żadnych dalszych dodatkowych akcji. Po udzieleniu zgody administratora użytkownicy mogą uzyskać token dostępu za pośrednictwem typowego przepływu uwierzytelniania, a uzyskany token dostępu będzie miał odpowiednie uprawnienia.

Gdy administrator firmy korzysta z aplikacji i jest kierowany do punktu końcowego autoryzacji, platforma tożsamości firmy Microsoft wykryje rolę użytkownika i poprosi o zgodę w imieniu całej dzierżawy dla żądanych uprawnień. Istnieje jednak również dedykowany punkt końcowy zgody administratora, którego można użyć, jeśli chcesz proaktywnie zażądać uprawnień administratora w imieniu całej dzierżawy. Korzystanie z tego punktu końcowego jest również niezbędne do żądania uprawnień aplikacji (których nie można żądać przy użyciu punktu końcowego autoryzacji).

W przypadku wykonania tych kroków aplikacja może zażądać uprawnień dla wszystkich użytkowników w dzierżawie, w tym zakresów z ograniczeniami administratora. Jest to operacja o wysokim poziomie uprawnień i należy ją wykonać tylko w razie potrzeby w danym scenariuszu.

Aby wyświetlić przykładowy kod, który implementuje kroki, zobacz [przykład zakresów z ograniczeniami administratora](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Zażądaj uprawnień w portalu rejestracji aplikacji

Aplikacje mogą zauważyć, które uprawnienia wymagają (zarówno delegowane, jak i aplikacje) w portalu rejestracji aplikacji.  Umożliwia to korzystanie z `/.default` zakresu i opcji "Udziel zgody administratora" Azure Portal.  Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest upewnienie się, że uprawnienia zdefiniowane statycznie dla danej aplikacji są nadzbiorem uprawnień, które będą dynamicznie/przyrostowo.

> [!NOTE]
>Uprawnienia aplikacji mogą być wymagane tylko za pomocą programu [`/.default`](#the-default-scope) — dlatego jeśli aplikacja wymaga uprawnień aplikacji, upewnij się, że są one wymienione w portalu rejestracji aplikacji.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Aby skonfigurować listę uprawnień w sposób statyczny dla aplikacji

1. Przejdź do swojej aplikacji w środowisku [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) lub [Utwórz aplikację](quickstart-register-app.md) , jeśli nie została jeszcze wybrana.
2. Znajdź sekcję **uprawnienia interfejsu API** i w obszarze uprawnienia interfejsu API kliknij pozycję Dodaj uprawnienie.
3. Wybierz pozycję **Microsoft Graph** z listy dostępnych interfejsów API, a następnie Dodaj uprawnienia wymagane przez aplikację.
3. **Zapisz** rejestrację aplikacji.

### <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Podpisz użytkownika w aplikacji

Zazwyczaj podczas kompilowania aplikacji korzystającej z punktu końcowego zgody administratora aplikacja musi mieć stronę lub widok, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może należeć do przepływu rejestracji aplikacji, części ustawień aplikacji lub być dedykowanym przepływem "Połącz". W wielu przypadkach ma sens, aby aplikacja pokazywała ten widok "Połącz" tylko po zalogowaniu się użytkownika przy użyciu konto Microsoft służbowego.

Po podpisaniu użytkownika do aplikacji można zidentyfikować organizację, do której należy administrator, przed zaproszeniem ich o zatwierdzenie wymaganych uprawnień. Chociaż nie jest to absolutnie konieczne, może pomóc w tworzeniu bardziej intuicyjnego środowiska dla użytkowników w organizacji. Aby podpisać użytkownika w programie, należy postępować zgodnie z naszymi [samouczkami dotyczącymi protokołów Microsoft Identity platform](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy wszystko będzie gotowe do zażądania uprawnień od administratora organizacji, możesz przekierować użytkownika do *punktu końcowego zgody administratora*platformy tożsamości firmy Microsoft.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parametr        | Warunek        | Opis                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Wymagane | Dzierżawa katalogu, z której chcesz zażądać uprawnień. Można podać w formacie identyfikatora GUID lub przyjaznej nazwy lub ogólnie przywoływane z organizacjami, jak pokazano w przykładzie. Nie używaj elementu "Common", ponieważ konta osobiste nie mogą zapewnić zgody administratora, z wyjątkiem sytuacji, w której znajduje się dzierżawa. Aby zapewnić najlepszą zgodność z kontami osobistymi, które zarządzają dzierżawcami, użyj identyfikatora dzierżawy, o ile jest to możliwe. |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `redirect_uri` | Wymagane |Identyfikator URI przekierowania, w którym odpowiedź ma być wysyłana przez aplikację do obsługi. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Użyj stanu, aby kodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takie jak strona lub widok. |
|`scope`        | Wymagane        | Definiuje zestaw uprawnień wymaganych przez aplikację. Może to być statyczne (za pomocą [`/.default`](#the-default-scope) ) lub zakresy dynamiczne.  Może to obejmować zakresy OIDC ( `openid` , `profile` , `email` ). Jeśli potrzebujesz uprawnień aplikacji, musisz użyć programu, `/.default` Aby zażądać statycznie skonfigurowanej listy uprawnień.  |


W tym momencie usługa Azure AD wymaga od administratora dzierżawy zalogowania się w celu ukończenia żądania. Administrator jest proszony o zatwierdzenie wszystkich uprawnień żądanych w `scope` parametrze.  Jeśli użyto wartości statycznej ( `/.default` ), będzie ona działać podobnie jak punkt końcowy zgody administratora w wersji 1.0 i poprosić o zgodę na wszystkie zakresy, które znajdują się w wymaganych uprawnieniach do aplikacji.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Jeśli administrator zatwierdzi uprawnienia do aplikacji, pomyślna odpowiedź wygląda następująco:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawa katalogu, która udzieliła aplikacji żądane uprawnienia w formacie identyfikatora GUID. |
| `state` | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Ten stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| `admin_consent` | Zostanie ustawiony na `True` . |

#### <a name="error-response"></a>Odpowiedź na błąd

Jeśli administrator nie zatwierdzi uprawnień dla aplikacji, odpowiedź zakończona niepowodzeniem będzie wyglądać następująco:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego zgody administratora aplikacja uzyskała żądane uprawnienia. Następnie możesz zażądać tokenu dla żądanego zasobu.

## <a name="using-permissions"></a>Korzystanie z uprawnień

Gdy użytkownik wyraża zgodę na uprawnienia do aplikacji, aplikacja może uzyskać tokeny dostępu, które reprezentują uprawnienia aplikacji, aby uzyskać dostęp do zasobu w pewnej pojemności. Tokenu dostępu można używać tylko w przypadku pojedynczego zasobu, ale zakodowany wewnątrz tokenu dostępu jest każde uprawnienie, które zostało udzielone aplikacji dla tego zasobu. Aby uzyskać token dostępu, aplikacja może wysłać żądanie do punktu końcowego tokenu platformy tożsamości firmy Microsoft w następujący sposób:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Możesz użyć otrzymanego tokenu dostępu w żądaniach HTTP do zasobu. Niezawodnie wskazuje zasobowi, że aplikacja ma odpowiednie uprawnienia do wykonania określonego zadania.

Aby uzyskać więcej informacji na temat protokołu OAuth 2,0 i sposobu uzyskiwania tokenów dostępu, zobacz [informacje dotyczące protokołu Microsoft Identity Endpoint platform](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Zakres/.default

Można użyć zakresu, `/.default` Aby ułatwić migrację aplikacji z punktu końcowego v 1.0 do punktu końcowego platformy tożsamości firmy Microsoft. Jest to wbudowany zakres dla każdej aplikacji, która odwołuje się do statycznej listy uprawnień skonfigurowanych na potrzeby rejestracji aplikacji. `scope`Wartość `https://graph.microsoft.com/.default` jest funkcjonalnie taka sama jak punkty końcowe v 1.0 `resource=https://graph.microsoft.com` — mianowicie, żąda tokenu z zakresami na Microsoft Graph, że aplikacja została zarejestrowana w Azure Portal.  Jest konstruowany przy użyciu identyfikatora URI zasobu + `/.default` (np. Jeśli identyfikator URI zasobu to `https://contosoApp.com` , żądany zakres będzie `https://contosoApp.com/.default` ).  Zapoznaj się z [sekcją na końcu ukośników](#trailing-slash-and-default) dla przypadków, w których należy uwzględnić drugi ukośnik, aby prawidłowo zażądać tokenu.

Zakres/.default może być używany w dowolnym przepływie protokołu OAuth 2,0, ale jest niezbędny w przebiegu przepływu i [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md), a także do żądania uprawnień aplikacji przy użyciu punktu końcowego zgody [administratora w wersji](v2-oauth2-on-behalf-of-flow.md) 2.

> [!NOTE]
> Klienci nie mogą łączyć statycznych ( `/.default` ) i dynamicznej zgody w pojedynczym żądaniu. W związku z tym, `scope=https://graph.microsoft.com/.default+mail.read` spowoduje to błąd ze względu na kombinację typów zakresów.

### <a name="default-and-consent"></a>/.default i wyrażanie zgody

`/.default`Zakres wyzwala również zachowanie punktu końcowego v 1.0 `prompt=consent` . Żądanie jest wyrażane zgodą na wszystkie uprawnienia zarejestrowane przez aplikację, niezależnie od zasobu. W przypadku uwzględnienia w ramach żądania `/.default` zakres zwraca token zawierający zakresy dla żądanego zasobu.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, gdy użytkownik już wyraził zgodę

Ponieważ `/.default` program jest funkcjonalnie identyczny z `resource` zachowaniem punktu końcowego skoncentrowanego na systemie v 1.0, zapewnia również zgodność z tym zachowaniem punktu końcowego v 1.0. Oznacza `/.default` to, że program wyzwala monit o zgodę, jeśli nie udzielono uprawnień między klientem i zasobem przez użytkownika. Jeśli istnieje taka zgoda, zostanie zwrócony token zawierający wszystkie zakresy przyznane przez użytkownika dla tego zasobu. Jednakże jeśli nie udzielono uprawnień lub `prompt=consent` parametr został podany, dla wszystkich zakresów zarejestrowanych przez aplikację kliencką zostanie wyświetlony monit o zgodę.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Przykład 1: użytkownik lub Administrator dzierżawy przyznał uprawnienia

W tym przykładzie użytkownik (lub Administrator dzierżawy) przyznał klientowi uprawnienia Microsoft Graph `mail.read` i `user.read` . Jeśli klient wysyła żądanie do programu `scope=https://graph.microsoft.com/.default` , nie będzie wyświetlany monit o zgodę, niezależnie od zawartości aplikacji klienckich zarejestrowanych uprawnień dla Microsoft Graph. Token zostałby zwrócony, zawierający zakresy `mail.read` i `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Przykład 2: użytkownik nie udzielił uprawnień między klientem i zasobem

W tym przykładzie nie istnieje zgoda dla użytkownika między klientem a Microsoft Graph. Klient został zarejestrowany na potrzeby `user.read` uprawnień i `contacts.read` , a także zakresu Azure Key Vault `https://vault.azure.net/user_impersonation` . Gdy klient żąda tokenu dla programu `scope=https://graph.microsoft.com/.default` , użytkownik zobaczy ekran wyrażania zgody dla `user.read` `contacts.read` zakresów, i Key Vault `user_impersonation` . Zwrócony token będzie zawierał tylko `user.read` zakresy i i `contacts.read` będzie można go używać tylko do Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Przykład 3: użytkownik wyraził zgodę, a klient żąda dodatkowych zakresów

W tym przykładzie użytkownik wyraził już zgodę na `mail.read` klienta. Klient został zarejestrowany dla `contacts.read` zakresu w jego rejestracji. Gdy klient wysyła żądanie dotyczące tokenu przy użyciu `scope=https://graph.microsoft.com/.default` i żąda zgody za pośrednictwem `prompt=consent` , zobaczy ekran zgody dla wszystkich (i tylko) uprawnień zarejestrowanych przez aplikację. `contacts.read` będzie obecny na ekranie wyrażania zgody, ale `mail.read` nie będzie. Zwrócony token będzie dla Microsoft Graph i będzie zawierać `mail.read` i `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Używanie zakresu/.default z klientem

Szczególny przypadek `/.default` zakresu istnieje, gdzie klient żąda własnego `/.default` zakresu. Poniższy przykład demonstruje ten scenariusz.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Spowoduje to wyświetlenie ekranu zgody na wszystkie zarejestrowane uprawnienia (jeśli dotyczy to na podstawie powyższych opisów wyrazów zgody i `/.default` ), a następnie zwraca id_token, a nie token dostępu.  Takie zachowanie istnieje w przypadku niektórych starszych klientów przechodzących z biblioteki ADAL do MSAL i **nie powinny** być używane przez nowych klientów przeznaczonych dla punktu końcowego platformy tożsamości firmy Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Przepływ i/.default przydzielenia poświadczeń klienta

Innym sposobem korzystania z programu `./default` jest żądanie uprawnień aplikacji (lub *ról*) w aplikacji nieinterakcyjnej, takiej jak aplikacja demona, która używa przepływu przydzielenia [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) do wywoływania internetowego interfejsu API.

Aby utworzyć uprawnienia aplikacji (role) dla internetowego interfejsu API, zobacz [jak: Dodawanie ról aplikacji w aplikacji](howto-add-app-roles-in-azure-ad-apps.md).

Żądania poświadczeń klienta w aplikacji klienckiej **muszą** zawierać `scope={resource}/.default` , gdzie `{resource}` jest interfejs API sieci Web, w którym aplikacja ma być wywoływana. Wystawianie żądania poświadczeń klienta przy użyciu poszczególnych uprawnień aplikacji (ról) **nie** jest obsługiwane. Wszystkie uprawnienia aplikacji (role) przyznane dla tego internetowego interfejsu API zostaną uwzględnione w zwróconym tokenie dostępu.

Aby udzielić dostępu do zdefiniowanych uprawnień aplikacji, w tym przyznawania zgody administratora aplikacji, zobacz [Szybki Start: Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowego interfejsu API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Końcowe ukośniki i/.default

Niektóre identyfikatory URI zasobów mają końcowy ukośnik ( `https://contoso.com/` w przeciwieństwie do `https://contoso.com` ), co może spowodować problemy z walidacją tokenu.  Może to wystąpić przede wszystkim podczas żądania tokenu dla usługi Azure Resource Management ( `https://management.azure.com/` ), który ma końcowy ukośnik w identyfikatorze URI zasobu i wymaga, aby był obecny w momencie żądania tokenu.  W takim przypadku podczas żądania tokenu dla `https://management.azure.com/` i używania należy `/.default` zażądać `https://management.azure.com//.default` — należy zwrócić uwagę na podwójny ukośnik!

Ogólnie — Jeśli sprawdzono, czy token jest wystawiony, a token jest odrzucany przez interfejs API, który powinien go zaakceptować, rozważ dodanie drugiego ukośnika i ponowienie próby. Dzieje się tak, ponieważ serwer logowania emituje token z odbiorcami pasującymi do identyfikatorów URI w `scope` parametrze z `/.default` usuniętym z końca.  Jeśli spowoduje to usunięcie końcowego ukośnika, serwer logowania nadal przetwarza żądanie i weryfikuje go względem identyfikatora URI zasobu, nawet jeśli nie są już zgodne — jest to niestandardowa i nie powinna być używana przez aplikację.

## <a name="troubleshooting-permissions-and-consent"></a>Rozwiązywanie problemów z uprawnieniami i zgodą

Jeśli użytkownik lub Twoja aplikacja widzi nieoczekiwane błędy w trakcie procesu wyrażania zgody, zapoznaj się z artykułem dotyczącym rozwiązywania problemów: [nieoczekiwany błąd podczas wyrażania zgody aplikacji](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Następne kroki

* [IDENTYFIKATORY tokenów | Platforma tożsamości firmy Microsoft](id-tokens.md)
* [Tokeny dostępu | Platforma tożsamości firmy Microsoft](access-tokens.md)
