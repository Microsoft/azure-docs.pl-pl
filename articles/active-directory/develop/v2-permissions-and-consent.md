---
title: Zakresy, uprawnienia i zgody platformy tożsamości & Microsoft
description: Dowiedz się więcej o autoryzacji w punkcie końcowym platformy tożsamości firmy Microsoft, w tym o zakresach, uprawnieniach i zgodach.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2a975a0aba06ecfd010fe328ef6c8cda75290f2b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515587"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Uprawnienia i zgoda na platformie tożsamości firmy Microsoft

Aplikacje zintegrowane z platformą tożsamości firmy Microsoft są zgodne z modelem autoryzacji, który zapewnia użytkownikom i administratorom kontrolę nad tym, jak można uzyskać dostęp do danych. Implementacja modelu autoryzacji została zaktualizowana na platformie tożsamości firmy Microsoft. Zmienia to sposób interakcji aplikacji z platformą tożsamości firmy Microsoft. Ten artykuł obejmuje podstawowe pojęcia dotyczące tego modelu autoryzacji, w tym zakresy, uprawnienia i zgody.

## <a name="scopes-and-permissions"></a>Zakresy i uprawnienia

Platforma tożsamości firmy Microsoft implementuje protokół [autoryzacji OAuth 2.0.](active-directory-v2-protocols.md) OAuth 2.0 to metoda, za pomocą której aplikacja innej firmy może uzyskać dostęp do zasobów hostowanych w Internecie w imieniu użytkownika. Każdy zasób hostowany w Internecie, który integruje się z platformą tożsamości firmy Microsoft, ma identyfikator zasobu lub *identyfikator URI identyfikatora aplikacji.* 

Oto kilka przykładów zasobów hostowanych w Internecie firmy Microsoft:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 API poczty e-mail: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Dotyczy to również wszystkich zasobów innych firm, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Każdy z tych zasobów może również definiować zestaw uprawnień, których można użyć do dzielenia funkcjonalności tego zasobu na mniejsze fragmenty. Na przykład [Microsoft Graph](https://graph.microsoft.com) ma zdefiniowane uprawnienia do wykonywania następujących zadań, między innymi:

* Odczytywanie kalendarza użytkownika
* Zapis w kalendarzu użytkownika
* Wysyłanie wiadomości e-mail jako użytkownik

Ze względu na te typy definicji uprawnień zasób ma szczeglicową kontrolę nad swoimi danymi i tym, jak są udostępniane funkcje interfejsu API. Aplikacja innej firmy może zażądać tych uprawnień od użytkowników i administratorów, którzy muszą zatwierdzić żądanie, aby aplikacja może uzyskać dostęp do danych lub działać w imieniu użytkownika. 

Gdy funkcje zasobu są podzielone na małe zestawy uprawnień, można utworzyć aplikacje innych firm w celu żądania tylko uprawnień potrzebnych do wykonywania ich funkcji. Użytkownicy i administratorzy mogą wiedzieć, do jakich danych aplikacja może uzyskać dostęp. Mogą oni mieć większą pewność, że aplikacja nie zachowuje się ze złośliwą intencją. Deweloperzy powinni zawsze przestrzegać zasady najmniejszych uprawnień, prosząc tylko o uprawnienia, których potrzebują, aby aplikacje działały.

W przypadku protokołu OAuth 2.0 zestawy uprawnień tego typu są nazywane *zakresami*. Są one również często określane jako *uprawnienia*. Na platformie tożsamości firmy Microsoft uprawnienie jest reprezentowane jako wartość ciągu. Aplikacja żąda wymaganych uprawnień, określając uprawnienie w `scope` parametrze zapytania. Platforma tożsamości obsługuje kilka dobrze zdefiniowanych [zakresów](#openid-connect-scopes) OpenID Connect, a także uprawnienia oparte na zasobach (każde uprawnienie jest wskazywane przez dołączenie wartości uprawnień do identyfikatora zasobu lub identyfikatora URI aplikacji). Na przykład ciąg uprawnień służy do żądania uprawnień do odczytu kalendarzy użytkowników w `https://graph.microsoft.com/Calendars.Read` Microsoft Graph.

Aplikacja najczęściej żąda tych uprawnień, określając zakresy w żądaniach do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft. Jednak niektóre uprawnienia o wysokim poziomie uprawnień można przyznać tylko za pośrednictwem zgody administratora. Można je zażądać lub przyznać przy użyciu punktu [końcowego zgody administratora](#admin-restricted-permissions). Czytaj dalej, aby dowiedzieć się więcej.

## <a name="permission-types"></a>Typy uprawnień

Platforma tożsamości firmy Microsoft obsługuje dwa typy uprawnień: *uprawnienia delegowane* i *uprawnienia aplikacji.*

* **Uprawnienia delegowane** są używane przez aplikacje, które mają zalogowanego użytkownika. W przypadku tych aplikacji użytkownik lub administrator wyraża zgodę na uprawnienia wymagane przez aplikację. Aplikacja ma delegowane uprawnienia do działania jako zalogowany użytkownik, gdy wykonuje wywołania do zasobu docelowego. 

    Niektóre uprawnienia delegowane mogą być wyrażane przez osoby nieadministratorów. Jednak niektóre uprawnienia o wysokim poziomie uprawnień wymagają [zgody administratora.](#admin-restricted-permissions) Aby dowiedzieć się, które role administratora mogą wyrazić zgodę na uprawnienia delegowane, zobacz Uprawnienia ról administratorów [w usłudze Azure Active Directory (Azure AD).](../roles/permissions-reference.md)

* **Uprawnienia aplikacji** są używane przez aplikacje, które działają bez zalogowaowego użytkownika, na przykład aplikacje, które działają jako usługi w tle lub demony. Tylko [administrator może wyrazić zgodę na uprawnienia](#requesting-consent-for-an-entire-tenant) aplikacji.

_Efektywne uprawnienia_ to uprawnienia, które aplikacja ma podczas wykonywania żądań do zasobu docelowego. Ważne jest, aby zrozumieć różnicę między uprawnieniami delegowanymi i uprawnieniami aplikacji, które zostały przyznane aplikacji, a efektywnymi uprawnieniami udzielanych aplikacji podczas wykonywania wywołań do zasobu docelowego.

- W przypadku uprawnień  delegowanych efektywne uprawnienia aplikacji są najmniej uprzywilejowanymi częściami delegowanych uprawnień przyznanych aplikacji (za zgodą) i uprawnień aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. 

   W organizacjach uprawnienia zalogowaowego użytkownika mogą być określane przez zasady lub przez członkostwo w co najmniej jednej roli administratora. Aby dowiedzieć się, które role administratora mogą wyrazić zgodę na uprawnienia delegowane, zobacz [Uprawnienia ról administratorów w usłudze Azure AD.](../roles/permissions-reference.md)

   Załóżmy na przykład, że aplikacja ma przyznane _uprawnienie delegowane User.ReadWrite.All._ Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja może zaktualizować profil każdego użytkownika w organizacji. Jeśli jednak zalogowany użytkownik nie ma roli administratora, aplikacja może zaktualizować tylko profil zalogowaowego użytkownika. Nie może aktualizować profilów innych użytkowników w organizacji, ponieważ użytkownik, który ma uprawnienia do działania w imieniu użytkownika, nie ma tych uprawnień.

- W przypadku uprawnień aplikacji _efektywne uprawnienia_ aplikacji to pełny poziom uprawnień implikowany przez uprawnienie. Na przykład aplikacja z uprawnieniem _User.ReadWrite.All_ może zaktualizować profil każdego użytkownika w organizacji.

## <a name="openid-connect-scopes"></a>OpenID Connect zakresy

Implementacja platformy tożsamości firmy Microsoft OpenID Connect ma kilka dobrze zdefiniowanych zakresów, które są również hostowane w Microsoft Graph: `openid` , `email` , i `profile` `offline_access` . Zakresy `address` OpenID Connect i nie są `phone` obsługiwane.

Jeśli zażądasz OpenID Connect i tokenu, otrzymasz token do wywołania punktu [końcowego UserInfo.](userinfo.md)

### <a name="openid"></a>Openid

Jeśli aplikacja się za pomocą usługi [OpenID Connect](active-directory-v2-protocols.md), musi zażądać `openid` zakresu. Zakres `openid` jest wyświetlany na stronie zgody konta służbowego jako uprawnienie **Zaloguj** się. Na stronie konto Microsoft zgoda jest wyświetlana jako Wyświetlanie profilu i nawiązywanie połączenia z aplikacjami i usługami przy użyciu **konto Microsoft** aplikacji. 

Korzystając z tego uprawnienia, aplikacja może otrzymać unikatowy identyfikator użytkownika w postaci `sub` oświadczenia. Uprawnienie zapewnia również aplikacji dostęp do punktu końcowego UserInfo. Zakres może być używany w punkcie końcowym tokenu platformy tożsamości firmy `openid` Microsoft do uzyskania tokenów identyfikatorów. Aplikacja może używać tych tokenów do uwierzytelniania.

### <a name="email"></a>poczta e-mail

Zakres `email` może być używany z `openid` zakresem i innymi zakresami. Zapewnia aplikacji dostęp do podstawowego adresu e-mail użytkownika w postaci `email` oświadczenia. 

Oświadczenie `email` jest uwzględniane w tokenie tylko wtedy, gdy adres e-mail jest skojarzony z kontem użytkownika, co nie zawsze ma miejsce. Jeśli aplikacja używa zakresu, musi być w stanie obsłużyć przypadek, w którym w `email` `email` tokenie nie istnieje żadne oświadczenie.

### <a name="profile"></a>profil

Zakres `profile` może być używany z `openid` zakresem i dowolnym innym zakresem. Zapewnia aplikacji dostęp do dużej ilości informacji o użytkowniku. Informacje, do których ma dostęp, obejmują między innymi imię, nazwisko, preferowaną nazwę użytkownika i identyfikator obiektu. 

Aby uzyskać pełną listę `profile` oświadczeń dostępnych w `id_tokens` parametrze dla określonego użytkownika, zobacz [ `id_tokens` odwołanie](id-tokens.md).

### <a name="offline_access"></a>offline_access

Zakres [ `offline_access` zapewnia](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) aplikacji przez dłuższy czas dostęp do zasobów w imieniu użytkownika. Na stronie zgody ten zakres jest wyświetlany jako Zachowaj dostęp do danych, do których **udzielono mu uprawnień.** 

Gdy użytkownik zatwierdzi zakres, aplikacja może odbierać tokeny odświeżania z punktu końcowego `offline_access` tokenu platformy tożsamości firmy Microsoft. Tokeny odświeżania są długotrwałe. Aplikacja może uzyskać nowe tokeny dostępu, gdy starsze tokeny wygasną.

> [!NOTE]
> To uprawnienie jest obecnie wyświetlane na wszystkich stronach zgody, nawet w przypadku przepływów, które nie zapewniają tokenu odświeżania (takich jak [niejawny przepływ).](v2-oauth2-implicit-grant-flow.md) Ta konfiguracja dotyczy scenariuszy, w których klient może rozpocząć się w ramach niejawnego przepływu, a następnie przechodzi do przepływu kodu, w którym oczekiwano tokenu odświeżania.

Na platformie tożsamości firmy Microsoft (żądania do punktu końcowego w wersji 2.0) aplikacja musi jawnie zażądać zakresu w celu odbierania `offline_access` tokenów odświeżania. Dlatego po zrealizowaniu kodu autoryzacji w przepływie kodu autoryzacji [OAuth 2.0](active-directory-v2-protocols.md)otrzymasz tylko token dostępu z punktu `/token` końcowego. 

Token dostępu jest ważny przez krótki czas. Zazwyczaj wygasa on w ciągu jednej godziny. W tym momencie aplikacja musi przekierować użytkownika z powrotem do punktu końcowego, `/authorize` aby uzyskać nowy kod autoryzacji. Podczas tego przekierowania, w zależności od typu aplikacji, użytkownik może potrzebować ponownie wprowadzić swoje poświadczenia lub ponownie wyrazić zgodę na uprawnienia.

Aby uzyskać więcej informacji na temat sposobu uzyskania i używania tokenów odświeżania, zobacz informacje dotyczące protokołu [platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Żądanie zgody poszczególnych użytkowników

W [żądaniu OpenID Connect autoryzacji lub OAuth 2.0](active-directory-v2-protocols.md) aplikacja może zażądać wymaganych uprawnień przy użyciu `scope` parametru zapytania. Na przykład gdy użytkownik się do aplikacji, aplikacja wysyła żądanie, jak w poniższym przykładzie. (Podziały wierszy są dodawane w celu czytelności).

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

Parametr `scope` to rozdzielana spacjami lista delegowanych uprawnień, których żąda aplikacja. Każde uprawnienie jest wskazywane przez dołączenie wartości uprawnienia do identyfikatora zasobu (identyfikatora URI aplikacji). W przykładzie żądania aplikacja musi mieć uprawnienia do odczytywania kalendarza użytkownika i wysyłania wiadomości e-mail jako użytkownik.

Po wejściu poświadczeń przez użytkownika platforma tożsamości firmy Microsoft sprawdza, czy jest zgodny rekord *zgody użytkownika.* Jeśli użytkownik nie wyraził w przeszłości zgody na żadne z żądanych uprawnień, a administrator nie wyraził zgody na te uprawnienia w imieniu całej organizacji, platforma tożsamości firmy Microsoft prosi użytkownika o udzielenie żądanych uprawnień.

Obecnie uprawnienie ("Zachowaj dostęp do danych, do których udzielono mu dostępu") oraz uprawnienie ("Zaloguj się i odczytaj swój profil") są automatycznie uwzględniane podczas wstępnej zgody `offline_access` `user.read` na aplikację.  Te uprawnienia są zwykle wymagane do prawidłowego działania aplikacji. Uprawnienie `offline_access` zapewnia aplikacji dostęp do tokenów odświeżania, które mają kluczowe znaczenie dla aplikacji natywnych i aplikacji internetowych. Uprawnienie `user.read` zapewnia dostęp do `sub` oświadczenia. Dzięki temu klient lub aplikacja może poprawnie identyfikować użytkownika w czasie i uzyskać dostęp do informacji o użytkowniku.

![Przykładowy zrzut ekranu przedstawiający zgodę konta służbowego.](./media/v2-permissions-and-consent/work_account_consent.png)

Gdy użytkownik zatwierdzi żądanie uprawnień, zgoda jest rejestrowana. Użytkownik nie musi ponownie wyrazić zgody, gdy później zaloguje się do aplikacji.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żądanie zgody dla całej dzierżawy

Gdy organizacja kupuje licencję lub subskrypcję dla aplikacji, często chce aktywnie skonfigurować aplikację do użycia przez wszystkich członków organizacji. W ramach tego procesu administrator może udzielić zgody aplikacji na działanie w imieniu dowolnego użytkownika w dzierżawie. Jeśli administrator udziela zgody dla całej dzierżawy, użytkownicy organizacji nie widzą strony zgody dla aplikacji.

Aby zażądać zgody na uprawnienia delegowane dla wszystkich użytkowników w dzierżawie, aplikacja może użyć punktu końcowego zgody administratora.

Ponadto aplikacje muszą żądać uprawnień aplikacji za pomocą punktu końcowego zgody administratora.

## <a name="admin-restricted-permissions"></a>Uprawnienia ograniczone przez administratora

Niektóre uprawnienia o wysokim poziomie uprawnień w zasobach firmy Microsoft można ustawić na uprawnienia *ograniczone przez administratora.* Oto kilka przykładów tego rodzaju uprawnień:

* Odczytywanie pełnych profilów wszystkich użytkowników przy użyciu `User.Read.All`
* Zapisz dane w katalogu organizacji przy użyciu `Directory.ReadWrite.All`
* Odczytywanie wszystkich grup w katalogu organizacji przy użyciu `Groups.Read.All`

Mimo że użytkownik może udzielić aplikacji dostępu do tego rodzaju danych, użytkownicy organizacji nie mogą udzielić dostępu do tego samego zestawu poufnych danych firmy. Jeśli aplikacja zażąda dostępu do jednego z tych uprawnień od użytkownika organizacyjnego, zostanie wyświetlony komunikat o błędzie informujący, że nie ma autoryzacji do wyrażania zgody na uprawnienia aplikacji.

Jeśli aplikacja wymaga zakresów uprawnień ograniczonych przez administratora, administrator organizacji musi wyrazić zgodę na te zakresy w imieniu użytkowników organizacji. Aby uniknąć wyświetlania monitów dla użytkowników, którzy żądają zgody dla uprawnień, których nie mogą udzielić, aplikacja może użyć punktu końcowego zgody administratora. Punkt końcowy zgody administratora zostanie objęty następną sekcją.

Jeśli aplikacja zażąda uprawnień delegowanych o wysokim poziomie uprawnień, a administrator przyzna te uprawnienia za pośrednictwem punktu końcowego zgody administratora, zostanie udzielona zgoda dla wszystkich użytkowników w dzierżawie.

Jeśli aplikacja żąda uprawnień aplikacji, a administrator przyznaje te uprawnienia za pośrednictwem punktu końcowego zgody administratora, to przyznanie nie jest wykonywane w imieniu żadnego określonego użytkownika. Zamiast tego aplikacji klienckiej są przyznawane uprawnienia *bezpośrednio.* Te typy uprawnień są używane tylko przez usługi demona i inne nieinteraktywne aplikacje, które działają w tle.

## <a name="using-the-admin-consent-endpoint"></a>Korzystanie z punktu końcowego zgody administratora

Gdy udzielisz zgody administratora za pomocą punktu końcowego zgody administratora, wszystko będzie gotowe. Użytkownicy nie muszą podjąć żadnych dalszych działań. Po przyznaniu zgody administratora użytkownicy mogą uzyskać token dostępu za pośrednictwem typowego przepływu uwierzytelniania. Wynikowy token dostępu ma uprawnienia, na które wyrażana jest zgoda.

Gdy administrator globalny korzysta z Twojej aplikacji i jest przekierowyny do punktu końcowego autoryzacji, platforma tożsamości firmy Microsoft wykrywa rolę użytkownika. Zostanie pytanie, czy administrator globalny chce wyrazić zgodę w imieniu całej dzierżawy na żądane uprawnienia. Zamiast tego możesz użyć dedykowanego punktu końcowego zgody administratora, aby aktywnie poprosić administratora o udzielenie uprawnień w imieniu całej dzierżawy. Ten punkt końcowy jest również niezbędny do żądania uprawnień aplikacji. Nie można zażądać uprawnień aplikacji przy użyciu punktu końcowego autoryzacji.

W przypadku wykonywania tych kroków aplikacja może zażądać uprawnień dla wszystkich użytkowników w dzierżawie, w tym zakresów ograniczonych przez administratora. Ta operacja ma wysokie uprawnienia. Użyj operacji tylko w razie potrzeby dla scenariusza.

Aby wyświetlić przykładowy kod, który implementuje kroki, zobacz przykład [zakresów ograniczonych przez](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) administratora w usłudze GitHub.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji

W portalu rejestracji aplikacji aplikacje mogą wyświetlić listę wymaganych uprawnień, w tym uprawnienia delegowane i uprawnienia aplikacji. Ta konfiguracja umożliwia korzystanie z zakresu `/.default` i Azure Portal opcji U **udzielić zgody administratora.**  

Ogólnie rzecz biorąc, uprawnienia powinny być zdefiniowane statycznie dla danej aplikacji. Powinny one być narzutem uprawnień, których aplikacja będzie żądać dynamicznie lub przyrostowo.

> [!NOTE]
>Uprawnienia aplikacji można zażądać tylko za pomocą funkcji [`/.default`](#the-default-scope) . Jeśli więc aplikacja wymaga uprawnień aplikacji, upewnij się, że są one wyświetlane w portalu rejestracji aplikacji.

Aby skonfigurować listę statycznie żądanych uprawnień dla aplikacji:

1. Przejdź do aplikacji w <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">przewodniku Szybki Azure Portal — Rejestracje aplikacji</a> szybki start.
1. Wybierz aplikację lub [utwórz aplikację,](quickstart-register-app.md) jeśli jeszcze jej nie masz.
1. Na stronie Przegląd aplikacji w  **obszarze** Zarządzanie wybierz pozycję **Uprawnienia interfejsu API**  >  **Dodaj uprawnienie.**
1. Wybierz **Microsoft Graph** z listy dostępnych interfejsów API. Następnie dodaj uprawnienia wymagane przez aplikację.
1. Wybierz **pozycję Dodaj uprawnienia.**

### <a name="recommended-sign-the-user-in-to-your-app"></a>Zalecane: Logowanie użytkownika do aplikacji

Zazwyczaj podczas tworzenia aplikacji korzystającej z punktu końcowego zgody administratora aplikacja wymaga strony lub widoku, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może być:

* Część przepływu rejestracji aplikacji.
* Część ustawień aplikacji.
* Dedykowany przepływ "połącz". 

W wielu przypadkach warto, aby aplikacja wyświetlała ten widok "połącz" dopiero po zalogowaniu się użytkownika przy użyciu konta służbowego konto Microsoft lub konto Microsoft.

Po zalogowaniu użytkownika do aplikacji możesz zidentyfikować organizację, do której należy administrator, zanim poprosisz go o zatwierdzenie niezbędnych uprawnień. Chociaż ten krok nie jest ściśle konieczny, może pomóc w utworzeniu bardziej intuicyjnego interfejsu dla użytkowników w organizacji. 

Aby zalogować użytkownika, postępuj zgodnie z [samouczkami](active-directory-v2-protocols.md)protokołu platformy tożsamości firmy Microsoft .

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
| `tenant` | Wymagane | Dzierżawa katalogu, od której chcesz zażądać uprawnień. Można go podano w formacie identyfikatora GUID lub przyjaznej nazwy. Można też ogólnie odwoływać się do organizacji, jak po zobaczyliśmy w przykładzie. Nie używaj "typowych", ponieważ konta osobiste nie mogą wyrazić zgody administratora z wyjątkiem kontekstu dzierżawy. Aby zapewnić najlepszą zgodność z kontami osobistymi, które zarządzają dzierżawami, użyj identyfikatora dzierżawy, jeśli jest to możliwe. |
| `client_id` | Wymagane | Identyfikator aplikacji (klienta), który [Azure Portal — Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisane do aplikacji. |
| `redirect_uri` | Wymagane |Adres URI przekierowania, do którego aplikacja ma wysyłać odpowiedź. Musi on dokładnie odpowiadać jednem z adresów URL przekierowania zarejestrowanym w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Użyj stanu , aby zakodować informacje o stanie użytkownika w aplikacji przed zgłoszeniem żądania uwierzytelnienia, takie jak strona lub widok, na którym był on. |
|`scope`        | Wymagane        | Definiuje zestaw uprawnień żądanych przez aplikację. Zakresy mogą być statyczne (przy użyciu [`/.default`](#the-default-scope) ) lub dynamiczne.  Ten zestaw może obejmować zakresy OpenID Connect ( `openid` , `profile` , `email` ). Jeśli potrzebujesz uprawnień aplikacji, musisz użyć metody , aby zażądać statycznie `/.default` skonfigurowanej listy uprawnień.  |


W tym momencie usługa Azure AD wymaga od administratora dzierżawy zalogowania się w celu ukończenia żądania. Administrator zostanie poproszony o zatwierdzenie wszystkich uprawnień żądanych w `scope` parametrze .  Jeśli używasz wartości statycznej ( ), będzie ona działać jak punkt końcowy zgody administratora w wersji 1.0 i zażądać zgody dla wszystkich zakresów znalezionych w wymaganych uprawnieniach `/.default` dla aplikacji.

#### <a name="successful-response"></a>Pomyślna odpowiedź

Jeśli administrator zatwierdzi uprawnienia aplikacji, pomyślna odpowiedź będzie wyglądać następująco:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawa katalogu, która udzieliła aplikacji żądanych uprawnień, w formacie identyfikatora GUID. |
| `state` | Wartość uwzględniona w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed zgłoszeniem żądania uwierzytelniania, takich jak strona lub widok, na którym się znajduje. |
| `admin_consent` | Zostanie ustawiona wartość `True` . |

#### <a name="error-response"></a>Odpowiedź z błędem

Jeśli administrator nie zatwierdzi uprawnień aplikacji, odpowiedź z niepowodzeniem będzie wyglądać następująco:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują. Może również służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc deweloperowi w zidentyfikowaniu głównej przyczyny błędu. |

Po pomyślnym otrzymaniu odpowiedzi z punktu końcowego zgody administratora aplikacja uzyskała żądane uprawnienia. Następnie możesz zażądać tokenu dla zasobu, którego potrzebujesz.

## <a name="using-permissions"></a>Korzystanie z uprawnień

Gdy użytkownik wyraża zgodę na uprawnienia do aplikacji, aplikacja może uzyskać tokeny dostępu, które reprezentują uprawnienia aplikacji do uzyskiwania dostępu do zasobu w owej pojemności. Token dostępu może być używany tylko dla jednego zasobu. Jednak kodowane wewnątrz tokenu dostępu to każde uprawnienie przyznane aplikacji dla tego zasobu. Aby uzyskać token dostępu, aplikacja może wykonać żądanie do punktu końcowego tokenu platformy tożsamości firmy Microsoft w taki sposób:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Wynikowego tokenu dostępu można użyć w żądaniach HTTP do zasobu. W niezawodny sposób wskazuje zasobowi, że aplikacja ma odpowiednie uprawnienia do wykonania określonego zadania.

Aby uzyskać więcej informacji na temat protokołu OAuth 2.0 i sposobu uzyskiwania tokenów dostępu, zobacz informacje dotyczące protokołu punktu końcowego [platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Zakres /.default

Zakres umożliwia migrację aplikacji z punktu końcowego w wersji 1.0 do punktu końcowego `/.default` platformy tożsamości firmy Microsoft. Zakres jest wbudowany dla każdej aplikacji, która odwołuje się `/.default` do statycznej listy uprawnień skonfigurowanych w rejestracji aplikacji. 

Wartość `scope` jest `https://graph.microsoft.com/.default` funkcjonalnie taka sama jak w punkcie końcowym `resource=https://graph.microsoft.com` w wersji 1.0. Określając zakres w żądaniu, aplikacja żąda tokenu dostępu, który zawiera zakresy dla każdego uprawnienia usługi Microsoft Graph wybranego dla aplikacji w portalu `https://graph.microsoft.com/.default` rejestracji aplikacji. Zakres jest konstruowany przy użyciu URI zasobu i `/.default` . Jeśli więc zasób ma URI `https://contosoApp.com` , żądanym zakresem jest `https://contosoApp.com/.default` .  W przypadkach, w których należy dołączyć drugi ukośnik, aby poprawnie zażądać tokenu, zobacz sekcję na temat [ukośników na końcowej trasie.](#trailing-slash-and-default)

Zakres `/.default` może być używany w dowolnym przepływie protokołu OAuth 2.0. Jednak jest to konieczne w przepływie [On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) i [przepływie poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md). Jest ona również potrzebna w przypadku żądania uprawnień aplikacji za pomocą punktu końcowego zgody administratora w wersji 2.

Klienci nie mogą łączyć statycznej zgody `/.default` () i dynamicznej zgody w jednym żądaniu. Dlatego `scope=https://graph.microsoft.com/.default+mail.read` powoduje błąd, ponieważ łączy typy zakresów.

### <a name="default-and-consent"></a>/.default i zgoda

Zakres wyzwala również zachowanie punktu końcowego w wersji `/.default` 1.0. `prompt=consent` Żąda zgody dla wszystkich uprawnień zarejestrowanych przez aplikację, niezależnie od zasobu. Jeśli jest on uwzględniony w ramach żądania, zakres zwraca token zawierający zakresy `/.default` żądanego zasobu.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, gdy użytkownik wyraził już zgodę

Zakres `/.default` jest funkcjonalnie identyczny z zachowaniem `resource` -centric v1.0 punktu końcowego. Wykonuje również zachowanie zgody punktu końcowego w wersji 1.0. Oznacza to, że program wyzwala monit o wyrażenie zgody tylko wtedy, gdy użytkownik nie przyznał uprawnień między klientem a `/.default` zasobem. 

Jeśli taka zgoda istnieje, zwrócony token zawiera wszystkie zakresy przyznane użytkownikowi dla tego zasobu. Jeśli jednak nie udzielono uprawnień lub parametr został podany, zostanie wyświetlony monit o wyrażenie zgody dla wszystkich zakresów, które zarejestrowano `prompt=consent` w aplikacji klienckiej.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Przykład 1: użytkownik lub administrator dzierżawy przyznał uprawnienia

W tym przykładzie użytkownik lub administrator dzierżawy przyznał klientowi uprawnienia Microsoft Graph `mail.read` `user.read` i . 

Jeśli klient zażąda żądania , nie jest wyświetlany monit o wyrażenie zgody, niezależnie od zawartości zarejestrowanych uprawnień aplikacji klienckiej dla `scope=https://graph.microsoft.com/.default` Microsoft Graph. Zwrócony token zawiera zakresy `mail.read` i `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Przykład 2: użytkownik nie przyznał uprawnień między klientem a zasobem

W tym przykładzie użytkownik nie udzielił zgody między klientem a Microsoft Graph. Klient zarejestrował się na uprawnienia `user.read` i `contacts.read` . Zarejestrowała się również dla Azure Key Vault zakresu `https://vault.azure.net/user_impersonation` . 

Gdy klient zażąda tokenu dla usługi , użytkownik zobaczy stronę zgody dla zakresu, zakresu i Key Vault `scope=https://graph.microsoft.com/.default` `user.read` `contacts.read` `user_impersonation` zakresów. Zwrócony token zawiera tylko `user.read` zakresy `contacts.read` i . Można jej używać tylko w Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Przykład 3: Użytkownik wyraził zgodę, a klient żąda większej liczby zakresów

W tym przykładzie użytkownik wyraził już zgodę `mail.read` na to dla klienta. Klient został zarejestrowany dla `contacts.read` zakresu. 

Gdy klient zażąda tokenu przy użyciu usługi i zażąda zgody za pośrednictwem usługi , użytkownik zobaczy stronę zgody dla wszystkich (i tylko) uprawnień zarejestrowanych `scope=https://graph.microsoft.com/.default` `prompt=consent` przez aplikację. Zakres `contacts.read` znajduje się na stronie zgody, ale `mail.read` nie. Zwrócony token jest dla Microsoft Graph. Zawiera on `mail.read` elementy i `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Używanie zakresu /.default z klientem

W niektórych przypadkach klient może zażądać własnego `/.default` zakresu. W poniższym przykładzie pokazano ten scenariusz.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Ten przykład kodu tworzy stronę zgody dla wszystkich zarejestrowanych uprawnień, jeśli powyższe opisy zgody mają zastosowanie `/.default` do scenariusza. Następnie kod zwraca `id_token` wartość , a nie token dostępu.  

To zachowanie dotyczy niektórych starszych klientów, którzy są przenoszeni z biblioteki Azure AD Authentication Library (ADAL) do biblioteki Microsoft Authentication Library (MSAL). Ta konfiguracja *nie powinna być używana* przez nowych klientów, którzy są docelowi dla platformy tożsamości firmy Microsoft.

### <a name="client-credentials-grant-flow-and-default"></a>Poświadczenia klienta przyznają przepływ i /.default  

Innym zastosowaniem funkcji jest żądanie uprawnień aplikacji (lub ról ) w aplikacji nieinteraktywnej, na przykład aplikacji demona, która używa przepływu udzielania poświadczeń klienta do wywołania internetowego `/.default` interfejsu API.  [](v2-oauth2-client-creds-grant-flow.md)

Aby utworzyć uprawnienia aplikacji (role) dla internetowego interfejsu API, zobacz [Dodawanie ról aplikacji w aplikacji.](howto-add-app-roles-in-azure-ad-apps.md)

Żądania poświadczeń klienta w aplikacji klienckiej *muszą zawierać* `scope={resource}/.default` . Oto `{resource}` internetowy interfejs API, który aplikacja zamierza wywołać. Wystawianie żądania poświadczeń klienta przy użyciu uprawnień (ról) poszczególnych aplikacji nie *jest* obsługiwane. Wszystkie uprawnienia aplikacji (role), które zostały przyznane dla tego internetowego interfejsu API, są uwzględniane w zwróconym tokenie dostępu.

Aby udzielić dostępu do definiowania uprawnień aplikacji, w tym do udzielania zgody administratora dla aplikacji, zobacz Konfigurowanie aplikacji klienckiej w celu uzyskania dostępu [do internetowego interfejsu API.](quickstart-configure-app-access-web-apis.md)

### <a name="trailing-slash-and-default"></a>Ukośnik na końcowej trasie i /.default

Niektóre URI zasobów mają ukośnik na końcowej trasie, na przykład w `https://contoso.com/` przeciwieństwie do `https://contoso.com` . Ukośnik na końcowej stronie może powodować problemy z weryfikacją tokenu. Problemy występują głównie wtedy, gdy zażądano tokenu dla Azure Resource Manager ( `https://management.azure.com/` ). W takim przypadku ukośnik na końcowej stronie URI zasobu oznacza, że ukośnik musi być obecny, gdy zażądany jest token.  Dlatego gdy żądasz tokenu dla i używasz funkcji , musisz zażądać `https://management.azure.com/` `/.default` `https://management.azure.com//.default` (zwróć uwagę na podwójny ukośnik!). Ogólnie rzecz biorąc, jeśli sprawdzisz, czy token jest wystawiany, i jeśli token jest odrzucany przez interfejs API, który powinien go zaakceptować, rozważ dodanie drugiego ukośnika i spróbuj ponownie. 

## <a name="troubleshooting-permissions-and-consent"></a>Rozwiązywanie problemów z uprawnieniami i zgodami

Aby uzyskać instrukcje rozwiązywania problemów, zobacz [Nieoczekiwany błąd podczas wyrażania zgody na aplikację.](../manage-apps/application-sign-in-unexpected-user-consent-error.md)

## <a name="next-steps"></a>Następne kroki

* [Tokeny identyfikatorów na platformie tożsamości firmy Microsoft](id-tokens.md)
* [Tokeny dostępu na platformie tożsamości firmy Microsoft](access-tokens.md)
