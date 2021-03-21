---
title: Zakresy platformy tożsamości firmy Microsoft, uprawnienia, & zgody
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
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2658c088304eba457b25bb3dc421b356ba70b57f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102482"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Uprawnienia i zgoda na platformie tożsamości firmy Microsoft

Aplikacje integrowane z platformą tożsamości firmy Microsoft są zgodne z modelem autoryzacji, który umożliwia użytkownikom i administratorom kontrolę nad sposobem uzyskiwania dostępu do danych. Implementacja modelu autoryzacji została zaktualizowana na platformie tożsamości firmy Microsoft. Zmienia sposób współpracy aplikacji z platformą tożsamości firmy Microsoft. W tym artykule opisano podstawowe koncepcje tego modelu autoryzacji, w tym zakresy, uprawnienia i zgodę.

## <a name="scopes-and-permissions"></a>Zakresy i uprawnienia

Platforma tożsamości firmy Microsoft implementuje protokół autoryzacji [OAuth 2,0](active-directory-v2-protocols.md) . OAuth 2,0 to metoda, za pomocą której aplikacja innej firmy może uzyskać dostęp do zasobów hostowanych w sieci Web w imieniu użytkownika. Wszystkie zasoby hostowane w sieci Web, które integrują się z platformą tożsamości firmy Microsoft, mają identyfikator zasobu lub identyfikator *URI aplikacji*. 

Poniżej przedstawiono kilka przykładów zasobów hostowanych przez firmę Microsoft w sieci Web:

* Microsoft Graph: `https://graph.microsoft.com`
* Interfejs API poczty Microsoft 365: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Ta sama wartość dotyczy wszystkich zasobów innych firm, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Dowolny z tych zasobów może również definiować zestaw uprawnień, które mogą służyć do dzielenia funkcjonalności tego zasobu na mniejsze fragmenty. Na przykład [Microsoft Graph](https://graph.microsoft.com) ma zdefiniowane uprawnienia do wykonywania następujących zadań, między innymi:

* Odczytaj kalendarz użytkownika
* Zapisz w kalendarzu użytkownika
* Wyślij wiadomość jako użytkownika

Ze względu na te typy definicji uprawnień zasób ma szczegółową kontrolę nad swoimi danymi oraz sposób ujawniania funkcjonalności interfejsu API. Aplikacja innej firmy może zażądać tych uprawnień od użytkowników i administratorów, którzy muszą zatwierdzić żądanie, zanim aplikacja będzie mogła uzyskać dostęp do danych lub wykonać działania w imieniu użytkownika. 

Gdy funkcjonalność zasobu jest podzielony na małe zestawy uprawnień, aplikacje innych firm mogą być kompilowane, aby żądać tylko uprawnień niezbędnych do wykonania ich funkcji. Użytkownicy i Administratorzy mogą dowiedzieć się, jakie dane mogą uzyskać dostęp do aplikacji. Dzięki temu aplikacja nie zachowuje złośliwego celu. Deweloperzy powinni zawsze przestrzegać zasad najniższych uprawnień, zwracając się z prośbą o tylko uprawnienia potrzebne do działania aplikacji.

W przypadku protokołu OAuth 2,0 te typy zestawów uprawnień są nazywane *zakresami*. Są one również często określane jako *uprawnienia*. Na platformie tożsamości firmy Microsoft uprawnienie jest reprezentowane jako wartość ciągu. W Microsoft Graph przykładzie jest to wartość ciągu dla każdego uprawnienia:

* Odczytaj kalendarz użytkownika przy użyciu `Calendars.Read`
* Zapisywanie w kalendarzu użytkownika przy użyciu `Calendars.ReadWrite`
* Wyślij wiadomość jako użytkownika używaną przez `Mail.Send`

Aplikacja najczęściej żąda tych uprawnień, określając zakresy w żądaniach do autoryzowanego punktu końcowego platformy tożsamości firmy Microsoft. Jednak niektóre uprawnienia o wysokim poziomie uprawnień mogą być udzielane tylko za poorednictwem zgody administratora. Można je zażądać lub uzyskać za pomocą [punktu końcowego zgody administratora](#admin-restricted-permissions). Aby dowiedzieć się więcej, zapoznaj się z artykułem.

## <a name="permission-types"></a>Typy uprawnień

Platforma tożsamości firmy Microsoft obsługuje dwa typy uprawnień: *delegowane uprawnienia* i *uprawnienia do aplikacji*.

* **Delegowane uprawnienia** są używane przez aplikacje, które mają obecny zalogowany użytkownik. W przypadku tych aplikacji użytkownik lub administrator wyraża zgodę na uprawnienia, które aplikacja żąda. Aplikacja jest delegowane uprawnienia do działania jako zalogowany użytkownik podczas wykonywania wywołań do zasobu docelowego. 

    Niektóre uprawnienia delegowane mogą być wysyłane przez innych administratorów. Jednak pewne uprawnienia o wysokim poziomie uprawnień wymagają [zgody administratora](#admin-restricted-permissions). Aby dowiedzieć się, które role administratorów mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administrator w Azure Active Directory (Azure AD)](../roles/permissions-reference.md).

* **Uprawnienia aplikacji** są używane przez aplikacje, które są uruchamiane bez zalogowanego użytkownika, na przykład aplikacje, które działają jako usługi lub demony w tle. Tylko [administrator może wyrazić zgodę na](#requesting-consent-for-an-entire-tenant) uprawnienia aplikacji.

_Czynne uprawnienia_ są uprawnieniami, które aplikacja ma w momencie, gdy wysyła żądania do zasobu docelowego. Ważne jest, aby zrozumieć różnicę między delegowanymi uprawnieniami i uprawnieniami aplikacji, które są udzielane przez aplikację, oraz obowiązującymi uprawnieniami, które są udzielane podczas wykonywania wywołań do zasobu docelowego.

- W przypadku uprawnień delegowanych _czynne uprawnienia_ aplikacji są częścią wspólną uprawnień delegowanych przypisanych do aplikacji (według zgody) oraz uprawnieniami aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. 

   W ramach organizacji uprawnienia zalogowanego użytkownika mogą być określane przez zasady lub przez członkostwo w co najmniej jednej roli administratora. Aby dowiedzieć się, które role administratorów mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../roles/permissions-reference.md).

   Załóżmy na przykład, że aplikacja ma przyznane uprawnienie _User. ReadWrite. All_ delegowane. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja może zaktualizować profil każdego użytkownika w organizacji. Jeśli jednak zalogowany użytkownik nie ma roli administratora, aplikacja może zaktualizować tylko profil zalogowanego użytkownika. Nie można zaktualizować profilów innych użytkowników w organizacji, ponieważ użytkownik, do którego ma uprawnienia do działania w imieniu użytkownika, nie ma tych uprawnień.

- W przypadku uprawnień aplikacji _czynne uprawnienia_ aplikacji są pełen poziom uprawnień IMPLIKOWANYCH przez uprawnienia. Na przykład aplikacja z uprawnieniem _User. ReadWrite. All_ aplikacji może aktualizować profil każdego użytkownika w organizacji.

## <a name="openid-connect-scopes"></a>Zakresy połączeń OpenID Connect

Implementacja platformy tożsamości firmy Microsoft w programie OpenID Connect Connect zawiera kilka dobrze zdefiniowanych zakresów, które są również hostowane w Microsoft Graph: `openid` , `email` , `profile` i `offline_access` . `address` `phone` Zakresy połączeń i OpenID Connect nie są obsługiwane.

Jeśli zażądasz zakresów OpenID Connect Connect i tokenu, otrzymasz token umożliwiający wywołanie [punktu końcowego UserInfo](userinfo.md).

### <a name="openid"></a>OpenID Connect

Jeśli aplikacja loguje się przy użyciu programu [OpenID Connect Connect](active-directory-v2-protocols.md), musi zażądać `openid` zakresu. `openid`Zakres jest wyświetlany na stronie wyrażanie zgody na konto służbowe jako uprawnienia do **logowania** . Na stronie osobista zgoda konto Microsoft zostanie wyświetlona pozycja **Wyświetl swój profil i Połącz się z aplikacjami i usługami przy użyciu uprawnień konto Microsoft** . 

Korzystając z tego uprawnienia, aplikacja może odbierać unikatowy identyfikator dla użytkownika w postaci `sub` żądania. Uprawnienie daje również dostęp do aplikacji do punktu końcowego UserInfo. `openid`Zakres może być używany w punkcie końcowym tokenu platformy tożsamości firmy Microsoft w celu uzyskania tokenów identyfikatorów. Aplikacja może używać tych tokenów do uwierzytelniania.

### <a name="email"></a>poczta e-mail

`email`Zakres może być używany z `openid` zakresem i innymi zakresami. Daje ona aplikacji dostęp do podstawowego adresu e-mail użytkownika w postaci `email` zgłoszenia. 

`email`Oświadczenia jest uwzględniany w tokenie tylko wtedy, gdy adres e-mail jest skojarzony z kontem użytkownika, co nie zawsze jest przypadkiem. Jeśli aplikacja używa `email` zakresu, aplikacja musi być w stanie obsłużyć przypadek, w którym w `email` tokenie nie istnieje żadne zastrzeżenie.

### <a name="profile"></a>profil

`profile`Zakres może być używany z `openid` zakresem i dowolnym innym zakresem. Zapewnia aplikacji dostęp do dużej ilości informacji o użytkowniku. Informacje, do których może uzyskać dostęp, obejmują, ale nie są ograniczone do, podanej nazwy, imienia, preferowanej nazwy użytkownika i identyfikatora obiektu. 

Aby uzyskać pełną listę `profile` oświadczeń dostępnych w `id_tokens` parametrze dla określonego użytkownika, zobacz [ `id_tokens` odwołanie](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Zakres](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) umożliwia aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie wyrażanie zgody ten zakres jest wyświetlany jako **dostęp do danych, do których masz dostęp** . 

Gdy użytkownik zatwierdza `offline_access` zakres, aplikacja może odbierać tokeny odświeżania z punktu końcowego tokenu Microsoft Identity platform. Tokeny odświeżania są długotrwałe. Twoja aplikacja może uzyskać nowe tokeny dostępu, ponieważ wygasły.

> [!NOTE]
> To uprawnienie jest obecnie wyświetlane na wszystkich stronach zgody, nawet w przypadku przepływów, które nie zapewniają tokenu odświeżania (takiego jak [przepływ niejawny](v2-oauth2-implicit-grant-flow.md)). Ta konfiguracja rozwiązuje scenariusze, w których klient może rozpocząć pracę w niejawnym przepływie, a następnie przejść do przepływu kodu, w którym oczekiwany jest token odświeżania.

Na platformie tożsamości firmy Microsoft (żądania wysłane do punktu końcowego v 2.0) aplikacja musi jawnie zażądać `offline_access` zakresu, aby otrzymywać tokeny odświeżania. Dlatego po zrealizowaniu kodu autoryzacji w [przepływie kodu autoryzacji OAuth 2,0](active-directory-v2-protocols.md)otrzymasz tylko token dostępu z `/token` punktu końcowego. 

Token dostępu jest ważny przez krótki czas. Zwykle wygasa w ciągu godziny. W tym momencie aplikacja musi przekierować użytkownika z powrotem do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji. W trakcie tego przekierowania, w zależności od typu aplikacji, użytkownik może potrzebować ponownie wprowadzić swoje poświadczenia lub wyrazić zgodę ponownie na uprawnienia.

Aby uzyskać więcej informacji na temat uzyskiwania i używania tokenów odświeżania, zobacz [informacje dotyczące protokołu Microsoft Identity platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Żądanie zgody poszczególnych użytkowników

W żądaniu autoryzacji [OpenID Connect Connect lub OAuth 2,0](active-directory-v2-protocols.md) aplikacja może zażądać wymaganych uprawnień przy użyciu `scope` parametru zapytania. Na przykład gdy użytkownik loguje się do aplikacji, aplikacja wysyła żądanie podobne do poniższego przykładu. (Podziały wierszy są dodawane do czytelności).

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

Po wprowadzeniu poświadczeń przez użytkownika platforma tożsamości firmy Microsoft wyszukuje pasujący rekord *zgody użytkownika*. Jeśli użytkownik nie wyraził zgody na jakiekolwiek z żądanych uprawnień w przeszłości, a administrator nie wyraził zgody na te uprawnienia w imieniu całej organizacji, platforma tożsamości firmy Microsoft prosi użytkownika o przyznanie wymaganych uprawnień.

W tej chwili `offline_access` uprawnienie ("zapewnianie dostępu do danych, do których masz dostęp, do") uprawnienia i `user.read` ("Logowanie użytkownika i odczytanie Twojego profilu") jest automatycznie dołączane do początkowej zgody na aplikację.  Te uprawnienia są zwykle wymagane w celu uzyskania odpowiedniej funkcjonalności aplikacji. `offline_access`Uprawnienie umożliwia aplikacji dostęp do odświeżania tokenów, które mają kluczowe znaczenie dla natywnych aplikacji i aplikacji sieci Web. `user.read`Uprawnienie daje dostęp do tego `sub` żądania. Pozwala klientowi lub aplikacji poprawnie identyfikować użytkownika w czasie i uzyskać dostęp do informacji o użytkowniku podstawowe.

![Przykładowy zrzut ekranu, który pokazuje zgodę na konto służbowe.](./media/v2-permissions-and-consent/work_account_consent.png)

Gdy użytkownik zatwierdzi żądanie uprawnienia, zostanie zarejestrowana zgoda. Użytkownik nie musi ponownie wyrazić zgody, gdy później zaloguje się do aplikacji.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żądanie zgody dla całej dzierżawy

Gdy organizacja kupuje licencję lub subskrypcję aplikacji, organizacja często chce aktywnie skonfigurować aplikację do użycia przez wszystkich członków organizacji. W ramach tego procesu administrator może udzielić zgody aplikacji na działanie w imieniu dowolnego użytkownika w dzierżawie. Jeśli administrator udzieli zgody dla całej dzierżawy, użytkownicy organizacji nie widzą strony zgody dla aplikacji.

Aby zażądać zgody na delegowane uprawnienia dla wszystkich użytkowników w dzierżawie, aplikacja może korzystać z punktu końcowego zgody użytkownika.

Ponadto aplikacje muszą używać punktu końcowego zgody administratora, aby zażądać uprawnień aplikacji.

## <a name="admin-restricted-permissions"></a>Uprawnienia ograniczone przez administratora

Niektóre uprawnienia o wysokim poziomie uprawnień w zasobach firmy Microsoft można ustawić na wartość z *ograniczeniami administratora*. Oto kilka przykładów tego rodzaju uprawnień:

* Odczytuj pełne profile wszystkich użytkowników za pomocą `User.Read.All`
* Zapisywanie danych w katalogu organizacji przy użyciu `Directory.ReadWrite.All`
* Odczytuj wszystkie grupy w katalogu organizacji przy użyciu `Groups.Read.All`

Mimo że użytkownik może udzielić aplikacji dostępu do tego rodzaju danych, użytkownicy w organizacji nie mogą udzielić dostępu do tego samego zestawu poufnych danych firmowych. Jeśli aplikacja zażąda dostępu do jednego z tych uprawnień od użytkownika w organizacji, zostanie wyświetlony komunikat o błędzie informujący, że nie są uprawnieni do wyrażania zgody na uprawnienia aplikacji.

Jeśli aplikacja wymaga zakresów dla uprawnień z ograniczeniami administratora, administrator organizacji musi wyrazić zgodę na te zakresy w imieniu użytkowników w organizacji. Aby uniknąć wyświetlania użytkownikom, którzy zażądali zgody na uprawnienia, których nie mogą udzielić, aplikacja może korzystać z punktu końcowego zgody administratora. Punkt końcowy zgody administratora został uwzględniony w następnej sekcji.

Jeśli aplikacja żąda uprawnień delegowanych o wysokim poziomie uprawnień i administrator przyznaje te uprawnienia za pomocą punktu końcowego zgody użytkownika, zgoda jest przyznawana wszystkim użytkownikom w dzierżawie.

Jeśli aplikacja żąda uprawnień aplikacji, a administrator przyznaje te uprawnienia za pomocą punktu końcowego zgody na administratora, to przyznanie nie jest wykonywane w imieniu określonego użytkownika. Zamiast tego do aplikacji klienckiej uzyskuje się *bezpośrednie* uprawnienia. Te typy uprawnień są używane tylko przez usługi demona i inne nieinteraktywne aplikacje działające w tle.

## <a name="using-the-admin-consent-endpoint"></a>Korzystanie z punktu końcowego zgody administratora

Po użyciu punktu końcowego zgody administratora, aby udzielić zgody administratora, wszystko zostało zakończone. Użytkownicy nie muszą podejmować żadnych dalszych działań. Po udzieleniu zgody administratora użytkownicy mogą uzyskać token dostępu za pomocą typowego przepływu uwierzytelniania. Uzyskany token dostępu ma odpowiednie uprawnienia.

Gdy administrator globalny używa aplikacji i jest kierowany do punktu końcowego autoryzacji, platforma tożsamości firmy Microsoft wykrywa rolę użytkownika. Pyta, czy Administrator globalny chce wyrazić zgodę w imieniu całej dzierżawy dla żądanych uprawnień. Zamiast tego możesz użyć dedykowanego punktu końcowego zgody administratora, aby proaktywnie zażądać uprawnień administratora w imieniu całej dzierżawy. Ten punkt końcowy jest również wymagany do żądania uprawnień aplikacji. Nie można żądać uprawnień aplikacji za pomocą autoryzowanego punktu końcowego.

W przypadku wykonania tych kroków aplikacja może zażądać uprawnień dla wszystkich użytkowników w dzierżawie, w tym zakresów z ograniczeniami administratora. Ta operacja ma duże uprawnienia. Użyj tej operacji tylko w razie potrzeby w danym scenariuszu.

Aby wyświetlić przykładowy kod, który implementuje kroki, zobacz [przykład zakresów z ograniczeniami administratora](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) w witrynie GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Zażądaj uprawnień w portalu rejestracji aplikacji

W portalu rejestracji aplikacji aplikacje mogą wyświetlić listę wymaganych uprawnień, w tym uprawnienia delegowane i uprawnienia aplikacji. Ta konfiguracja umożliwia korzystanie z `/.default` zakresu i opcji **zgody administratora udzielanej** przez Azure Portal.  

Ogólnie rzecz biorąc, uprawnienia powinny być zdefiniowane statycznie dla danej aplikacji. Powinny one być nadzbiorem uprawnień, które aplikacja będzie żądać dynamicznie lub przyrostowo.

> [!NOTE]
>Uprawnienia aplikacji można żądać tylko przy użyciu programu [`/.default`](#the-default-scope) . Jeśli więc Twoja aplikacja wymaga uprawnień aplikacji, upewnij się, że są one wymienione w portalu rejestracji aplikacji.

Aby skonfigurować listę uprawnień statycznych żądanych dla aplikacji:

1. Przejdź do swojej aplikacji w środowisku szybkiego startu <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal rejestracje aplikacji</a> .
1. Wybierz aplikację lub [Utwórz aplikację](quickstart-register-app.md) , jeśli jeszcze tego nie zrobiono.
1. Na stronie **Przegląd** aplikacji w obszarze **Zarządzanie** wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**.
1. Wybierz pozycję **Microsoft Graph** z listy dostępnych interfejsów API. Następnie Dodaj uprawnienia wymagane przez aplikację.
1. Wybierz pozycję **Dodaj uprawnienia**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Zalecane: Podpisz użytkownika w aplikacji

Zazwyczaj podczas kompilowania aplikacji korzystającej z punktu końcowego zgody administratora aplikacja musi mieć stronę lub widok, w którym administrator może zatwierdzić uprawnienia aplikacji. Na tej stronie można:

* Część przepływu rejestracji aplikacji.
* Część ustawień aplikacji.
* Dedykowany przepływ "Połącz". 

W wielu przypadkach warto wyświetlić ten widok "Połącz" tylko po zalogowaniu się użytkownika przy użyciu konto Microsoft służbowej lub konto Microsoft szkolnej.

Po podpisaniu użytkownika w aplikacji można określić organizację, do której należy administrator, przed zaproszeniem ich o zatwierdzenie wymaganych uprawnień. Chociaż ten krok nie jest ściśle konieczny, może pomóc w tworzeniu bardziej intuicyjnego środowiska dla użytkowników w organizacji. 

Aby podpisać użytkownika w programie, postępuj zgodnie z [samouczkami dotyczącymi protokołu Microsoft Identity platform](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy wszystko będzie gotowe do zażądania uprawnień od administratora organizacji, możesz przekierować użytkownika do punktu końcowego zgody administratora platformy tożsamości firmy Microsoft.

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
| `tenant` | Wymagane | Dzierżawa katalogu, z której chcesz zażądać uprawnień. Można go podać w formacie identyfikatora GUID lub przyjaznej nazwy. Lub można je ogólnie przywoływać do organizacji, jak pokazano w przykładzie. Nie używaj "wspólnych", ponieważ konta osobiste nie mogą zapewnić zgody administratora, z wyjątkiem sytuacji, w której znajduje się dzierżawa. Aby zapewnić najlepszą zgodność z kontami osobistymi, które zarządzają dzierżawcami, użyj identyfikatora dzierżawy, jeśli jest to możliwe. |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), który [Azure Portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `redirect_uri` | Wymagane |Identyfikator URI przekierowania, w którym odpowiedź ma być wysyłana przez aplikację do obsługi. Musi dokładnie pasować do jednego z identyfikatorów URI przekierowania zarejestrowanych w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Użyj stanu, aby kodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takie jak strona lub widok. |
|`scope`        | Wymagane        | Definiuje zestaw uprawnień wymaganych przez aplikację. Zakresy mogą być statyczne (za pomocą [`/.default`](#the-default-scope) ) lub dynamiczne.  Ten zestaw może zawierać zakresy połączeń OpenID Connect ( `openid` , `profile` , `email` ). Jeśli potrzebujesz uprawnień aplikacji, musisz użyć programu, `/.default` Aby zażądać statycznie skonfigurowanej listy uprawnień.  |


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
| `error` | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują. Może również służyć do reagowania na błędy. |
| `error_description` | Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego zgody administratora aplikacja uzyskała żądane uprawnienia. Następnie możesz zażądać tokenu dla żądanego zasobu.

## <a name="using-permissions"></a>Korzystanie z uprawnień

Gdy użytkownik wyraża zgodę na uprawnienia do aplikacji, może uzyskać tokeny dostępu reprezentujące uprawnienia aplikacji umożliwiające dostęp do zasobu w pewnej pojemności. Tokenu dostępu można używać tylko dla pojedynczego zasobu. Jednak kodowanie wewnątrz tokenu dostępu jest każde uprawnienie, które zostało przyznane dla tego zasobu. Aby uzyskać token dostępu, aplikacja może wysłać żądanie do punktu końcowego tokenu platformy tożsamości firmy Microsoft w następujący sposób:

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

Można użyć zakresu, `/.default` Aby ułatwić migrację aplikacji z punktu końcowego v 1.0 do punktu końcowego platformy tożsamości firmy Microsoft. `/.default`Zakres jest wbudowany dla każdej aplikacji, która odwołuje się do statycznej listy uprawnień skonfigurowanych na potrzeby rejestracji aplikacji. 

`scope`Wartość `https://graph.microsoft.com/.default` jest funkcjonalnie taka sama jak `resource=https://graph.microsoft.com` w punkcie końcowym v 1.0. Przez określenie `https://graph.microsoft.com/.default` zakresu w jego żądaniu aplikacja żąda tokenu dostępu zawierającego zakresy dla każdego uprawnienia Microsoft Graph wybranego dla aplikacji w portalu rejestracji aplikacji. Zakres jest konstruowany przy użyciu identyfikatora URI zasobu i `/.default` . Dlatego jeśli identyfikator URI zasobu to `https://contosoApp.com` , żądany zakres to `https://contosoApp.com/.default` .  W przypadkach, w których należy uwzględnić drugi ukośnik, aby prawidłowo zażądać tokenu, zobacz [sekcję dotyczącą końcowych ukośników](#trailing-slash-and-default).

`/.default`Zakres może być używany w dowolnym przepływie protokołu OAuth 2,0. Jest to jednak wymagane w przepływie [na rzecz](v2-oauth2-on-behalf-of-flow.md) przepływu i [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md). Jest ona również potrzebna, gdy do żądania uprawnień aplikacji jest używany punkt końcowy zgody administratora w wersji 2.

Klienci nie mogą łączyć statycznej `/.default` zgody i dynamicznej zgody w pojedynczym żądaniu. `scope=https://graph.microsoft.com/.default+mail.read`Powoduje to błąd, ponieważ łączy typy zakresów.

### <a name="default-and-consent"></a>/.default i wyrażanie zgody

`/.default`Zakres wyzwala również zachowanie punktu końcowego v 1.0 `prompt=consent` . Żądanie jest wyrażane zgodą na wszystkie uprawnienia zarejestrowane przez aplikację, niezależnie od zasobu. Jeśli zostanie uwzględniony jako część żądania, `/.default` zakres zwraca token, który zawiera zakresy dla żądanego zasobu.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, gdy użytkownik już wyraził zgodę

`/.default`Zakres jest funkcjonalnie identyczny z zachowaniem `resource` punktu końcowego skoncentrowanego na wersji v 1.0. Ma również zachowanie zgody na punkt końcowy v 1.0. Oznacza to, że program `/.default` wyzwala monit o zgodę tylko wtedy, gdy użytkownik nie ma uprawnień między klientem i zasobem. 

Jeśli istnieje taka zgoda, zwracany token zawiera wszystkie zakresy, które użytkownik udzielił dla tego zasobu. Jeśli jednak żadne uprawnienie nie zostało przyznane lub jeśli `prompt=consent` parametr został dostarczony, dla wszystkich zakresów, które zarejestrowano w aplikacji klienckiej, jest wyświetlany monit o zgodę.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Przykład 1: użytkownik lub Administrator dzierżawy przyznał uprawnienia

W tym przykładzie użytkownik lub Administrator dzierżawy udzielił `mail.read` `user.read` uprawnień i Microsoft Graph dla klienta. 

W przypadku żądania klienta `scope=https://graph.microsoft.com/.default` nie jest wyświetlany monit o zgodę, niezależnie od zawartości zarejestrowanych uprawnień aplikacji klienckiej dla Microsoft Graph. Zwrócony token zawiera zakresy `mail.read` i `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Przykład 2: użytkownik nie udzielił uprawnień między klientem i zasobem

W tym przykładzie użytkownik nie udzielił zgody między klientem a Microsoft Graph. Klient został zarejestrowany na potrzeby uprawnień `user.read` i `contacts.read` . Zarejestrowano również dla zakresu Azure Key Vault `https://vault.azure.net/user_impersonation` . 

Gdy klient żąda tokenu dla programu `scope=https://graph.microsoft.com/.default` , użytkownik widzi stronę zgody dla `user.read` zakresu, `contacts.read` zakresu i `user_impersonation` zakresów Key Vault. Zwrócony token zawiera tylko `user.read` `contacts.read` zakresy i. Można jej używać tylko w odniesieniu do Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Przykład 3: użytkownik wyraził zgodę, a klient żąda więcej zakresów

W tym przykładzie użytkownik wyraził już zgodę na `mail.read` klienta. Klient został zarejestrowany dla `contacts.read` zakresu. 

Gdy klient żąda tokenu przy użyciu `scope=https://graph.microsoft.com/.default` i żąda zgody przez `prompt=consent` , użytkownik zobaczy stronę zgody dla wszystkich (i tylko) uprawnień zarejestrowanych przez aplikację. `contacts.read`Zakres znajduje się na stronie zgody, ale `mail.read` nie jest. Zwrócony token jest przeznaczony dla Microsoft Graph. Zawiera `mail.read` i `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Używanie zakresu/.default z klientem

W niektórych przypadkach klient może zażądać własnego `/.default` zakresu. Poniższy przykład demonstruje ten scenariusz.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ten przykład kodu tworzy stronę zgody na wszystkie zarejestrowane uprawnienia, jeśli poprzednie opisy wyrazu zgodę i `/.default` dotyczą tego scenariusza. Następnie kod zwraca `id_token` a, a nie token dostępu.  

Takie zachowanie umożliwia uwzględnienie niektórych starszych klientów przenoszonych z biblioteki uwierzytelniania usługi Azure AD (ADAL) do biblioteki uwierzytelniania firmy Microsoft (MSAL). Ta konfiguracja nie *powinna* być używana przez nowych klientów przeznaczonych dla platformy tożsamości firmy Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Przepływ i/.default przydzielenia poświadczeń klienta  

Innym zastosowaniem `/.default` jest zażądanie uprawnień aplikacji (lub *ról*) w aplikacji nieinteraktywnej, takiej jak aplikacja demona, która używa przepływu przydzielenia [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) do wywoływania internetowego interfejsu API.

Aby utworzyć uprawnienia aplikacji (role) dla internetowego interfejsu API, zobacz [Dodawanie ról aplikacji w aplikacji](howto-add-app-roles-in-azure-ad-apps.md).

Żądania poświadczeń klienta w aplikacji klienckiej *muszą* zawierać `scope={resource}/.default` . W tym miejscu `{resource}` jest internetowy interfejs API, którego aplikacja zadzwoni. Wystawianie żądania poświadczeń klienta przy użyciu poszczególnych uprawnień aplikacji (ról) *nie* jest obsługiwane. Wszystkie uprawnienia aplikacji (role) przyznane dla tego internetowego interfejsu API są zawarte w zwracanym tokenie dostępu.

Aby udzielić dostępu do zdefiniowanych uprawnień aplikacji, w tym przyznawania zgody administratora aplikacji, zobacz [Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu do internetowego interfejsu API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Końcowe ukośniki i/.default

Niektóre identyfikatory URI zasobów mają kończący się ukośnikiem, na przykład `https://contoso.com/` w przeciwieństwie do `https://contoso.com` . Końcowy ukośnik może spowodować problemy z walidacją tokenu. Problemy występują przede wszystkim wtedy, gdy wymagany jest token dla Azure Resource Manager ( `https://management.azure.com/` ). W tym przypadku końcowy ukośnik na identyfikatorze URI zasobu oznacza, że ukośnik musi być obecny w momencie żądania tokenu.  Dlatego po zażądaniu tokenu dla `https://management.azure.com/` i użycia należy `/.default` zażądać `https://management.azure.com//.default` (Zauważ, że podwójny ukośnik!). Ogólnie rzecz biorąc, jeśli sprawdzasz, czy token jest wystawiony, a jeśli token jest odrzucany przez interfejs API, który powinien go zaakceptować, rozważ dodanie drugiego ukośnika i spróbuj ponownie. 

## <a name="troubleshooting-permissions-and-consent"></a>Rozwiązywanie problemów z uprawnieniami i zgodą

Aby uzyskać informacje na temat rozwiązywania problemów, zobacz [nieoczekiwany błąd podczas wyrażania zgody na aplikację](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Następne kroki

* [IDENTYFIKATORY tokenów na platformie tożsamości firmy Microsoft](id-tokens.md)
* [Tokeny dostępu na platformie tożsamości firmy Microsoft](access-tokens.md)
