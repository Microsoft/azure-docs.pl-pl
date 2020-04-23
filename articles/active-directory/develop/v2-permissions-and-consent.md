---
title: Zakresy, uprawnienia i zgody platformy tożsamości firmy Microsoft
description: Opis autoryzacji w punkcie końcowym platformy tożsamości firmy Microsoft, w tym zakresy, uprawnienia i zgody.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: 5495aa6fda189897985ed2f198f6e92c996f6fef
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868380"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft

Aplikacje integrowane z platformą tożsamości firmy Microsoft są zgodne z modelem autoryzacji, który daje użytkownikom i administratorom kontrolę nad tym, jak można uzyskać dostęp do danych. Implementacja modelu autoryzacji została zaktualizowana w punkcie końcowym platformy tożsamości firmy Microsoft i zmienia sposób, w jaki aplikacja musi wchodzić w interakcje z platformą tożsamości firmy Microsoft. W tym artykule opisano podstawowe pojęcia tego modelu autoryzacji, w tym zakresy, uprawnienia i zgody.

## <a name="scopes-and-permissions"></a>Zakresy i uprawnienia

Platforma tożsamości firmy Microsoft implementuje protokół autoryzacji [OAuth 2.0.](active-directory-v2-protocols.md) OAuth 2.0 to metoda, za pomocą której aplikacja innej firmy może uzyskiwać dostęp do zasobów hostowanych w sieci Web w imieniu użytkownika. Każdy zasób hostowany w sieci Web, który integruje się z platformą tożsamości firmy Microsoft, ma identyfikator zasobu lub identyfikator *URI identyfikatora aplikacji.* Na przykład niektóre zasoby hostowane w sieci Web firmy Microsoft obejmują:

* Wykres firmy Microsoft:`https://graph.microsoft.com`
* Interfejs API poczty usługi Office 365:`https://outlook.office.com`
* Azure Key Vault:`https://vault.azure.net`

> [!NOTE]
> Zdecydowanie zaleca się używanie programu Microsoft Graph zamiast interfejsu API poczty usługi Office 365 itp.

To samo dotyczy wszystkich zasobów innych firm, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Każdy z tych zasobów można również zdefiniować zestaw uprawnień, które mogą służyć do dzielenia funkcji tego zasobu na mniejsze fragmenty. Na przykład program [Microsoft Graph](https://graph.microsoft.com) zdefiniował uprawnienia do wykonywania następujących zadań, między innymi:

* Czytanie kalendarza użytkownika
* Pisanie do kalendarza użytkownika
* Wysyłanie poczty jako użytkownik

Definiując te typy uprawnień, zasób ma szczegółową kontrolę nad swoimi danymi i jak funkcja interfejsu API jest narażona. Aplikacja innej firmy może zażądać tych uprawnień od użytkowników i administratorów, którzy muszą zatwierdzić żądanie, zanim aplikacja będzie mogła uzyskać dostęp do danych lub działać w imieniu użytkownika. Przez fragmentowanie funkcji zasobu na mniejsze zestawy uprawnień, aplikacje innych firm można skonstować, aby zażądać tylko określonych uprawnień, które są potrzebne do wykonywania ich funkcji. Użytkownicy i administratorzy mogą dokładnie wiedzieć, do jakich danych aplikacja ma dostęp, i mogą mieć większą pewność, że nie zachowuje się ze złośliwym zamiarem. Deweloperzy powinni zawsze przestrzegać pojęcia najmniejszych uprawnień, prosząc o tylko uprawnienia, których potrzebują do działania ich aplikacji.

W OAuth 2.0 te typy uprawnień są nazywane *zakresami*. Są one również często określane jako *uprawnienia*. Uprawnienie jest reprezentowane na platformie tożsamości firmy Microsoft jako wartość ciągu. Kontynuując w przykładzie programu Microsoft Graph, wartość ciągu dla każdego uprawnienia jest:

* Czytanie kalendarza użytkownika za pomocą`Calendars.Read`
* Pisanie do kalendarza użytkownika przy użyciu`Calendars.ReadWrite`
* Wysyłanie poczty jako użytkownika za pomocą`Mail.Send`

Aplikacja najczęściej żąda tych uprawnień, określając zakresy w żądaniach do platformy tożsamości firmy Microsoft autoryzować punkt końcowy. Jednak niektóre uprawnienia o wysokich uprawnieniach mogą być udzielane tylko za zgodą administratora i żądane/przyznane przy użyciu [punktu końcowego zgody administratora](v2-permissions-and-consent.md#admin-restricted-permissions). Czytaj dalej, aby dowiedzieć się więcej.

## <a name="permission-types"></a>Typy uprawnień

Platforma tożsamości firmy Microsoft obsługuje dwa typy uprawnień: **uprawnienia delegowane** i **uprawnienia aplikacji**.

* **Uprawnienia delegowane** są używane przez aplikacje, które mają zalogowanego użytkownika obecnego. W przypadku tych aplikacji użytkownik lub administrator wyraża zgodę na uprawnienia, których żąda aplikacja, a aplikacja ma delegowane uprawnienia do działania jako zalogowany użytkownik podczas nawiązywać połączenia z zasobem docelowym. Niektóre uprawnienia delegowane mogą być wyrażane przez użytkowników nieadministratorów, ale niektóre uprawnienia o wyższych uprawnieniach wymagają [zgody administratora.](v2-permissions-and-consent.md#admin-restricted-permissions) Aby dowiedzieć się, które role administratora mogą wyrazić zgodę na uprawnienia delegowane, zobacz [Uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Uprawnienia aplikacji** są używane przez aplikacje, które działają bez zalogowanego użytkownika obecnego; na przykład aplikacje działające jako usługi w tle lub demony.  Uprawnienia aplikacji mogą być dozwolone tylko [przez administratora](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Skuteczne uprawnienia_ to uprawnienia, które aplikacja będzie miała podczas składania żądań do zasobu docelowego. Ważne jest, aby zrozumieć różnicę między uprawnieniami delegowanymi i aplikacji, które aplikacja jest przyznawana i jego skuteczne uprawnienia podczas nawiązywać połączenia do zasobu docelowego.

- W przypadku uprawnień delegowanych skuteczne uprawnienia aplikacji będą najmniej _uprzywilejowanym_ przecięciem uprawnień delegowanych, które aplikacja uzyskała (za zgodą) i uprawnieniami aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. Uprawnienia zalogowanego użytkownika mogą być określone w organizacji na podstawie zasad lub członkostwa w co najmniej jednej roli administratora. Aby dowiedzieć się, które role administratora mogą wyrazić zgodę na uprawnienia delegowane, zobacz [Uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Załóżmy na przykład, że aplikacja została przyznana _User.ReadWrite.All_ delegowane uprawnienia. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja będzie mogła zaktualizować profil każdego użytkownika w organizacji. Jeśli jednak zalogowany użytkownik nie pełni roli administratora, aplikacja będzie mogła zaktualizować tylko profil zalogowanego użytkownika. Użytkownik nie będzie mógł zaktualizować profili innych użytkowników w organizacji, ponieważ użytkownik mający uprawnienia do działania w imieniu innych osób nie ma odpowiednich uprawnień.

- W przypadku uprawnień aplikacji _skuteczne uprawnienia_ aplikacji będą pełnym poziomem uprawnień implikowanych przez uprawnienie. Na przykład aplikacja, która ma _user.ReadWrite.All_ uprawnienia aplikacji można zaktualizować profil każdego użytkownika w organizacji.

## <a name="openid-connect-scopes"></a>Zakresy OpenID Connect

Implementacja platformy tożsamości firmy Microsoft w usłudze OpenID Connect ma kilka dobrze `openid` `email`zdefiniowanych zakresów, które nie mają zastosowania do określonego zasobu: , , `profile`i `offline_access`. `address` Zakresy `phone` i OpenID Connect nie są obsługiwane.

### <a name="openid"></a>Openid

Jeśli aplikacja wykonuje logowanie przy użyciu [OpenID Connect,](active-directory-v2-protocols.md)musi zażądać `openid` zakresu. Zakres `openid` jest wyświetlany na stronie zgody konta służbowego jako uprawnienie "Zaloguj się", a na osobistej stronie zgody na konto Microsoft jako uprawnienie "Wyświetl swój profil i połącz się z aplikacjami i usługami przy użyciu konta Microsoft". Dzięki temu uprawnieniu aplikacja może otrzymać unikatowy identyfikator użytkownika `sub` w postaci oświadczenia. Daje również aplikacji dostęp do punktu końcowego UserInfo. Zakres `openid` może służyć w punkcie końcowym tokenu platformy tożsamości firmy Microsoft do uzyskiwania tokenów identyfikatorów, które mogą być używane przez aplikację do uwierzytelniania.

### <a name="email"></a>email

Zakres `email` może być używany `openid` z zakresu i innych. Daje aplikacji dostęp do podstawowego adresu e-mail użytkownika `email` w postaci oświadczenia. Oświadczenie `email` jest zawarte w tokenie tylko wtedy, gdy adres e-mail jest skojarzony z kontem użytkownika, co nie zawsze ma miejsce. Jeśli używa `email` zakresu, aplikacja powinna być przygotowana do obsługi `email` przypadku, w którym oświadczenie nie istnieje w tokenie.

### <a name="profile"></a>profil

Zakres `profile` może być używany `openid` z zakresu i innych. Daje aplikacji dostęp do znacznej ilości informacji o użytkowniku. Informacje, do których może uzyskać dostęp, obejmują między innymi imię, nazwisko, preferowaną nazwę użytkownika i identyfikator obiektu użytkownika, ale nie ograniczają się do nich. Aby uzyskać pełną listę oświadczeń profilu dostępnych w parametrze id_tokens dla określonego użytkownika, zobacz [ `id_tokens` odwołanie](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Zakres](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) daje aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie zgody ten zakres jest wyświetlany jako uprawnienie "Zachowaj dostęp do danych, do których udzielono mu dostępu". Gdy użytkownik zatwierdzi `offline_access` zakres, aplikacja może odbierać tokeny odświeżania z punktu końcowego tokenu platformy tożsamości firmy Microsoft. Tokeny odświeżania są długotrwałe. Aplikacja może uzyskać nowe tokeny dostępu w miarę wygasania starszych.

> [!NOTE]
> To uprawnienie pojawia się obecnie na wszystkich ekranach zgody, nawet w przypadku przepływów, które nie zapewniają tokenu odświeżania [(przepływu niejawnego).](v2-oauth2-implicit-grant-flow.md)  Ma to na celu pokrycie scenariuszy, w których klient może rozpocząć w ramach przepływu niejawnego, a następnie przejść do przepływu kodu, gdzie oczekuje się tokenu odświeżania.

Na platformie tożsamości firmy Microsoft (żądania do punktu końcowego w wersji `offline_access` 2.0) aplikacja musi jawnie zażądać zakresu, aby odbierać tokeny odświeżania. Oznacza to, że po zrealizowaniu kodu autoryzacji w [przepływie kodu autoryzacji OAuth 2.0](active-directory-v2-protocols.md)otrzymasz tylko token dostępu z punktu końcowego. `/token` Token dostępu jest prawidłowy przez krótki czas. Token dostępu zwykle wygasa w ciągu jednej godziny. W tym momencie aplikacja musi przekierować `/authorize` użytkownika z powrotem do punktu końcowego, aby uzyskać nowy kod autoryzacji. Podczas tego przekierowania, w zależności od typu aplikacji, użytkownik może być konieczne ponowne wprowadzenie poświadczeń lub ponownie wyrazić zgodę na uprawnienia.

Aby uzyskać więcej informacji na temat sposobu ujmowania i używania tokenów odświeżania, zobacz [odwołanie do protokołu platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Żądanie zgody poszczególnych użytkowników

W żądaniu autoryzacji [OpenID Connect lub OAuth 2.0](active-directory-v2-protocols.md) aplikacja może `scope` żądać wymaganych uprawnień przy użyciu parametru zapytania. Na przykład, gdy użytkownik loguje się do aplikacji, aplikacja wysyła żądanie, takie jak w poniższym przykładzie (z podziałami wierszy dodanymi dla czytelności):

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

Parametr `scope` jest oddzieloną spacją listą uprawnień delegowanych, o które żąda aplikacja. Każde uprawnienie jest wskazywane przez dołączenie wartości uprawnień do identyfikatora zasobu (identyfikatora URI identyfikatora aplikacji). W przykładzie żądania aplikacja potrzebuje uprawnień do odczytu kalendarza użytkownika i wysyłania poczty jako użytkownika.

Po wprowadzeniu przez użytkownika poświadczeń punkt końcowy platformy tożsamości firmy Microsoft sprawdza, czy nie ma pasującego rekordu *zgody użytkownika*. Jeśli użytkownik nie wyraził zgody na żadne z żądanych uprawnień w przeszłości, ani administrator nie wyraził zgody na te uprawnienia w imieniu całej organizacji, punkt końcowy platformy tożsamości firmy Microsoft prosi użytkownika o przyznanie żądanych uprawnień.

> [!NOTE]
>W tej chwili `offline_access` uprawnienia ("Zachowaj dostęp do danych, `user.read` do których użytkownik udzielił mu dostępu") i ("Zaloguj się i przeczytaj swój profil") są automatycznie uwzględniane w początkowej zgodzie na aplikację.  Te uprawnienia są zazwyczaj wymagane dla prawidłowej `offline_access` funkcjonalności aplikacji — daje aplikacji dostęp do tokenów `user.read` odświeżania, `sub` krytyczne dla aplikacji natywnych i sieci web, a jednocześnie daje dostęp do oświadczenia, dzięki czemu klient lub aplikacja, aby poprawnie zidentyfikować użytkownika w czasie i dostęp do podstawowych informacji o użytkowniku.

![Przykładowy zrzut ekranu przedstawiający zgodę konta służbowego](./media/v2-permissions-and-consent/work_account_consent.png)

Gdy użytkownik zatwierdzi żądanie uprawnień, zgoda jest rejestrowana, a użytkownik nie musi ponownie wyrażać zgody na kolejne logowania do aplikacji.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żądanie zgody dla całego dzierżawcy

Często, gdy organizacja kupuje licencję lub subskrypcję aplikacji, organizacja chce aktywnie skonfigurować aplikację do użytku przez wszystkich członków instytucji. W ramach tego procesu administrator może udzielić zgody na działanie aplikacji w imieniu dowolnego użytkownika w dzierżawie. Jeśli administrator udziela zgody dla całej dzierżawy, użytkownicy organizacji nie zobaczą strony zgody dla aplikacji.

Aby poprosić o zgodę na uprawnienia delegowane dla wszystkich użytkowników w dzierżawie, aplikacja może użyć punktu końcowego zgody administratora.

Ponadto aplikacje muszą używać punktu końcowego zgody administratora, aby zażądać uprawnień aplikacji.

## <a name="admin-restricted-permissions"></a>Uprawnienia z ograniczeniami administratora

Niektóre uprawnienia o wysokich uprawnieniach w ekosystemie firmy Microsoft można ustawić na ograniczone przez *administratora*. Przykłady tego rodzaju uprawnień są następujące:

* Przeczytaj pełne profile wszystkich użytkowników za pomocą`User.Read.All`
* Zapisywanie danych w katalogu organizacji przy użyciu`Directory.ReadWrite.All`
* Odczytywanie wszystkich grup w katalogu organizacji przy użyciu`Groups.Read.All`

Mimo że użytkownik konsument może udzielić aplikacji dostępu do tego rodzaju danych, użytkownicy organizacji są ograniczone do udzielania dostępu do tego samego zestawu poufnych danych firmy. Jeśli aplikacja żąda dostępu do jednego z tych uprawnień od użytkownika organizacji, użytkownik otrzymuje komunikat o błędzie informujący, że nie jest upoważniony do wyrażenia zgody na uprawnienia aplikacji.

Jeśli aplikacja wymaga dostępu do zakresów z ograniczeniami administratora dla organizacji, należy zażądać ich bezpośrednio od administratora firmy, również przy użyciu punktu końcowego zgody administratora, opisane dalej.

Jeśli aplikacja żąda uprawnień delegowanych o wysokim poziomie uprawnień, a administrator udziela tych uprawnień za pośrednictwem punktu końcowego zgody administratora, zgoda jest udzielana wszystkim użytkownikom w dzierżawie.

Jeśli aplikacja żąda uprawnień aplikacji, a administrator udziela tych uprawnień za pośrednictwem punktu końcowego zgody administratora, to przyznanie nie jest wykonywane w imieniu żadnego konkretnego użytkownika. Zamiast tego aplikacja kliencka otrzymuje uprawnienia *bezpośrednio*. Te typy uprawnień są używane tylko przez usługi demonów i inne aplikacje nieinterakcyjne, które działają w tle.

## <a name="using-the-admin-consent-endpoint"></a>Korzystanie z punktu końcowego zgody administratora

> [!NOTE]
> Należy pamiętać, że po udzieleniu zgody administratora przy użyciu punktu końcowego zgody administratora, użytkownik zakończył udzielanie zgody administratora, a użytkownicy nie muszą wykonywać żadnych dodatkowych czynności. Po udzieleniu zgody administratora użytkownicy mogą uzyskać token dostępu za pośrednictwem typowego przepływu eru, a wynikowy token dostępu będzie miał uprawnienia zgody.

Gdy administrator firmy korzysta z aplikacji i jest kierowany do punktu końcowego autoryzacji, platforma tożsamości firmy Microsoft wykryje rolę użytkownika i zapyta go, czy chciałby wyrazić zgodę w imieniu całej dzierżawy na żądane uprawnienia. Istnieje jednak również punkt końcowy zgody dedykowanej administratora, którego można użyć, jeśli chcesz proaktywnie zażądać, aby administrator udzielał uprawnień w imieniu całej dzierżawy. Korzystanie z tego punktu końcowego jest również niezbędne do żądania uprawnień aplikacji (które nie mogą być wymagane przy użyciu punktu końcowego autoryzacji).

Jeśli zastosujesz się do tych kroków, aplikacja może żądać uprawnień dla wszystkich użytkowników w dzierżawie, w tym zakresów z ograniczeniami administratora. Jest to operacja wysokiego uprawnienia i należy wykonać tylko wtedy, gdy jest to konieczne dla scenariusza.

Aby wyświetlić przykładowy kod, który implementuje kroki, zobacz [przykład zakresów z ograniczeniami administratora](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji

Aplikacje mogą zanotować, które uprawnienia są wymagane (zarówno delegowane, jak i aplikacje) w portalu rejestracji aplikacji.  Dzięki temu można `/.default` użyć zakresu i opcji "Udzielaj zgody administratora" w witrynie Azure portal.  Ogólnie rzecz biorąc jest najlepszym rozwiązaniem, aby upewnić się, że uprawnienia statycznie zdefiniowane dla danej aplikacji są nadzbiorem uprawnień, które będą żądać dynamicznie/przyrostowo.

> [!NOTE]
>Uprawnienia aplikacji można żądać tylko za [`/.default`](#the-default-scope) pomocą — więc jeśli aplikacja potrzebuje uprawnień aplikacji, upewnij się, że są one wymienione w portalu rejestracji aplikacji.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Aby skonfigurować listę statycznie żądanych uprawnień dla aplikacji

1. Przejdź do aplikacji w [witrynie Azure portal —](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko rejestracji aplikacji lub [utwórz aplikację,](quickstart-register-app.md) jeśli jeszcze tego nie zrobiłeś.
2. Znajdź sekcję **Uprawnienia interfejsu API,** a w ramach uprawnień interfejsu API kliknij pozycję Dodaj uprawnienie.
3. Wybierz pozycję **Microsoft Graph** z listy dostępnych interfejsów API, a następnie dodaj uprawnienia wymagane przez aplikację.
3. **Zapisz** rejestrację aplikacji.

### <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Zaloguj użytkownika do aplikacji

Zazwyczaj podczas tworzenia aplikacji, która używa punktu końcowego zgody administratora, aplikacja potrzebuje strony lub widoku, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może być częścią przepływu rejestracji aplikacji, częścią ustawień aplikacji lub może być dedykowanym przepływem "connect". W wielu przypadkach ma sens, aby aplikacja wyświetlała ten widok "połącz" tylko wtedy, gdy użytkownik zalogował się za pomocą służbowego konta Microsoft.

Po zalogowaniu użytkownika do aplikacji można zidentyfikować organizację, do której należy administrator, zanim poprosisz go o zatwierdzenie niezbędnych uprawnień. Chociaż nie jest to absolutnie konieczne, może pomóc w utworzeniu bardziej intuicyjnego środowiska dla użytkowników organizacji. Aby zalogować się do użytkownika, postępuj zgodnie z naszymi [samouczkami protokołu platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy będziesz gotowy do żądania uprawnień od administratora organizacji, możesz przekierować użytkownika do *punktu końcowego zgody administratora*platformy tożsamości firmy Microsoft .

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
| `tenant` | Wymagany | Dzierżawy katalogu, które chcesz zażądać uprawnień od. Może być podany w guid lub przyjazny format nazwy lub ogólnie odwołuje się do organizacji, jak widać w przykładzie. Nie należy używać "wspólne", ponieważ konta osobiste nie może udzielić zgody administratora, chyba że w kontekście dzierżawy. Aby zapewnić najlepszą zgodność z kontami osobistymi, które zarządzają dzierżawcami, użyj identyfikatora dzierżawy, jeśli to możliwe. |
| `client_id` | Wymagany | **Identyfikator aplikacji (klienta),** który usługa Azure portal — środowisko [rejestracji aplikacji przypisane](https://go.microsoft.com/fwlink/?linkid=2083908) do aplikacji. |
| `redirect_uri` | Wymagany |Identyfikator URI przekierowania, w którym ma zostać wysłana odpowiedź dla aplikacji do obsługi. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania, które zostały zarejestrowane w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Użyj stanu, aby zakodować informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takie jak strona lub widok, na których się znajdowały. |
|`scope`        | Wymagany        | Definiuje zestaw uprawnień wymaganych przez aplikację. Może to być statyczne [`/.default`](#the-default-scope)(przy użyciu) lub zakresy dynamiczne.  Może to obejmować zakresy OIDC (`openid`, , `profile` `email`). Jeśli potrzebujesz uprawnień aplikacji, `/.default` należy użyć do żądania statycznie skonfigurowanej listy uprawnień.  |


W tym momencie usługa Azure AD wymaga administratora dzierżawy, aby zalogować się, aby zakończyć żądanie. Administrator jest proszony o zatwierdzenie wszystkich uprawnień wymaganych `scope` w parametrze.  Jeśli użyto wartości statycznej`/.default`( ) będzie działać jak punkt końcowy zgody administratora w wersji 1.0 i zażądać zgody dla wszystkich zakresów znalezionych w wymaganych uprawnieniach dla aplikacji.

#### <a name="successful-response"></a>Skuteczna reakcja

Jeśli administrator zatwierdzi uprawnienia do aplikacji, pomyślna odpowiedź wygląda następująco:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawy katalogu, który przyznał aplikacji uprawnienia, o które żądano, w formacie GUID. |
| `state` | Wartość zawarta w żądaniu, która również zostanie zwrócona w odpowiedzi tokenu. Może to być ciąg dowolnej zawartości. Stan jest używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takich jak strona lub widok, na których się znajdowały. |
| `admin_consent` | Zostanie ustawiona `True`na . |

#### <a name="error-response"></a>Odpowiedź na błąd

Jeśli administrator nie zatwierdzi uprawnień do aplikacji, odpowiedź, która nie powiodło się, wygląda następująco:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie, który może pomóc deweloperowi zidentyfikować główną przyczynę błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego zgody administratora aplikacja uzyskała żądane uprawnienia. Następnie można zażądać tokenu dla zasobu, który chcesz.

## <a name="using-permissions"></a>Korzystanie z uprawnień

Po zgody użytkownika na uprawnienia dla aplikacji, aplikacja może uzyskać tokeny dostępu, które reprezentują uprawnienia aplikacji do dostępu do zasobu w pewnym pojemności. Token dostępu może służyć tylko dla pojedynczego zasobu, ale zakodowane wewnątrz tokenu dostępu jest każde uprawnienie, które aplikacja została przyznana dla tego zasobu. Aby uzyskać token dostępu, aplikacja może złożyć żądanie do punktu końcowego tokenu platformy tożsamości firmy Microsoft, w tym stylu:

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

Wynikowy token dostępu w żądaniach HTTP do zasobu jest używany. Niezawodnie wskazuje do zasobu, że aplikacja ma odpowiednie uprawnienia do wykonywania określonego zadania.

Aby uzyskać więcej informacji na temat protokołu OAuth 2.0 i sposobu uzyskiwania tokenów dostępu, zobacz [odwołanie do protokołu punktu końcowego platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Zakres /.default

Można użyć `/.default` zakresu, aby ułatwić migrację aplikacji z punktu końcowego w wersji 1.0 do punktu końcowego platformy tożsamości firmy Microsoft. Jest to wbudowany zakres dla każdej aplikacji, która odwołuje się do statycznej listy uprawnień skonfigurowanych w rejestracji aplikacji. Wartość `scope` `https://graph.microsoft.com/.default` jest funkcjonalnie taka sama jak punkty końcowe `resource=https://graph.microsoft.com` w wersji 1.0 — a mianowicie żąda tokenu z zakresami na programie Microsoft Graph, który aplikacja zarejestrowała w witrynie Azure portal.  Jest on konstruowany przy `/.default` użyciu identyfikatora URI + zasobu (np. jeśli identyfikator URI zasobu jest `https://contosoApp.com`, to zakres wymagany będzie `https://contosoApp.com/.default`).  Zobacz [sekcję na końcowe ukośnie](#trailing-slash-and-default) dla przypadków, w których należy dołączyć drugi ukośnik, aby poprawnie zażądać tokenu.

/.default zakres może służyć w dowolnym przepływie OAuth 2.0, ale jest to konieczne w [przepływie w imieniu](v2-oauth2-on-behalf-of-flow.md) i [przepływie poświadczeń klienta,](v2-oauth2-client-creds-grant-flow.md)a także podczas korzystania z punktu końcowego zgody administratora w wersji 2, aby zażądać uprawnień aplikacji.

> [!NOTE]
> Klienci nie mogą łączyć`/.default`statycznej ( ) i dynamicznej zgody w jednym żądaniu. W `scope=https://graph.microsoft.com/.default+mail.read` związku z tym spowoduje błąd ze względu na kombinację typów zakresu.

### <a name="default-and-consent"></a>/.default i zgoda

Zakres `/.default` wyzwala zachowanie punktu końcowego w wersji `prompt=consent` 1.0, jak również. Żąda zgody dla wszystkich uprawnień zarejestrowanych przez aplikację, niezależnie od zasobu. Jeśli jest uwzględniony jako część `/.default` żądania, zakres zwraca token, który zawiera zakresy żądanego zasobu.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, gdy użytkownik wyraził już zgodę

Ponieważ `/.default` jest funkcjonalnie `resource`identyczne z -centric v1.0 punktu końcowego zachowanie, przynosi ze sobą zachowanie zgody punktu końcowego w wersji 1.0, jak również. Mianowicie wyzwala `/.default` tylko monit o zgodę, jeśli użytkownik nie udzielił żadnych uprawnień między klientem a zasobem. Jeśli taka zgoda istnieje, token zostanie zwrócony zawierający wszystkie zakresy przyznane przez użytkownika dla tego zasobu. Jeśli jednak nie udzielono żadnych uprawnień lub `prompt=consent` parametr został dostarczony, zostanie wyświetlony monit o zgodę dla wszystkich zakresów zarejestrowanych przez aplikację kliencką.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Przykład 1: Użytkownik lub administrator dzierżawy udzielił uprawnień

W tym przykładzie użytkownik (lub administrator dzierżawy) przyznał klientowi `mail.read` uprawnienia `user.read`programu Microsoft Graph i . Jeśli klient złoży `scope=https://graph.microsoft.com/.default`żądanie , wówczas nie zostanie wyświetlony monit o zgodę niezależnie od zawartości aplikacji klienckich zarejestrowanych uprawnień dla programu Microsoft Graph. Token zostanie zwrócony zawierający `mail.read` zakresy i `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Przykład 2: Użytkownik nie udzielił uprawnień między klientem a zasobem

W tym przykładzie nie ma zgody dla użytkownika istnieje między klientem i Microsoft Graph. Klient zarejestrował się `user.read` dla `contacts.read` i uprawnień, a także `https://vault.azure.net/user_impersonation`zakresu usługi Azure Key Vault . Gdy klient zażąda tokenu `scope=https://graph.microsoft.com/.default`dla , użytkownik zobaczy `user.read` `contacts.read`ekran zgody dla `user_impersonation` , i zakresów usługi Key Vault. Token zwrócony będzie `user.read` miał `contacts.read` tylko i zakresy w nim i tylko być użyteczne dla programu Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Przykład 3: Użytkownik wyraził zgodę, a klient żąda dodatkowych zakresów

W tym przykładzie użytkownik wyraził `mail.read` już zgodę na klienta. Klient zarejestrował się `contacts.read` w zakresie rejestracji. Gdy klient składa żądanie tokenu `scope=https://graph.microsoft.com/.default` przy użyciu `prompt=consent`i żąda zgody za pośrednictwem, następnie użytkownik zobaczy ekran zgody dla wszystkich (i tylko) uprawnień zarejestrowanych przez aplikację. `contacts.read`będzie obecny na ekranie `mail.read` zgody, ale nie będzie. Zwrócony token będzie przeznaczony dla programu `mail.read` `contacts.read`Microsoft Graph i będzie zawierał i .

### <a name="using-the-default-scope-with-the-client"></a>Korzystanie z zakresu /.default z klientem

Szczególny przypadek zakresu `/.default` istnieje, gdy klient żąda `/.default` własnego zakresu. W poniższym przykładzie przedstawiono ten scenariusz.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Spowoduje to wyświetlenie ekranu zgody dla wszystkich zarejestrowanych uprawnień (jeśli `/.default`ma to zastosowanie na podstawie powyższych opisów zgody, a następnie zwraca id_token, a nie token dostępu.  To zachowanie istnieje dla niektórych starszych klientów przechodzących z usługi ADAL do MSAL i **nie powinny** być używane przez nowych klientów docelowych punktu końcowego platformy tożsamości firmy Microsoft.

### <a name="trailing-slash-and-default"></a>Ukośnik spływu końcowego i /.default

Niektóre identyfikatory URI zasobów mają`https://contoso.com/` ukośnik (w przeciwieństwie do), `https://contoso.com`co może powodować problemy z sprawdzaniem poprawności tokenu.  Może to wystąpić przede wszystkim podczas żądania tokenu dla usługi Azure Resource Management (`https://management.azure.com/`), który ma końcowe ukośnik na ich identyfikatorze URI zasobów i wymaga, aby być obecny, gdy token jest wymagany.  Tak więc, gdy żądasz tokenu `https://management.azure.com/` i za pomocą `/.default`, należy zażądać `https://management.azure.com//.default` - zanotuj podwójne ukośnik!

Ogólnie rzecz biorąc — jeśli masz zatwierdzone, że token jest wystawiany, a token jest odrzucany przez interfejs API, który powinien go zaakceptować, należy rozważyć dodanie drugiego ukośnika i ponowną próbę. Dzieje się tak, ponieważ serwer logowania emituje token z `scope` odbiorcami `/.default` pasującymi identyfikatory URI w parametrze — z usuniętym z końca.  Jeśli spowoduje to usunięcie końcowego ukośnika, serwer logowania nadal przetwarza żądanie i sprawdza poprawność go względem identyfikatora URI zasobu, nawet jeśli nie są już zgodne — jest to niestandardowe i nie powinno być zależne od aplikacji.

## <a name="troubleshooting-permissions-and-consent"></a>Rozwiązywanie problemów z uprawnieniami i zgodą

Jeśli ty lub użytkownicy aplikacji widzicie nieoczekiwane błędy podczas procesu zgody, zobacz ten artykuł, aby zapoznać się z instrukcjami rozwiązywania problemów: [Nieoczekiwany błąd podczas wykonywania zgody na aplikację](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
