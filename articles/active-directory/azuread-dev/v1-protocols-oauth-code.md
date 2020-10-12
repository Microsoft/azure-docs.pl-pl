---
title: Informacje o przepływie kodu autoryzacji OAuth 2,0 w usłudze Azure AD
description: W tym artykule opisano sposób korzystania z komunikatów HTTP w celu autoryzowania dostępu do aplikacji sieci Web i interfejsów API sieci Web w dzierżawie przy użyciu Azure Active Directory i uwierzytelniania OAuth 2,0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5f987ab15201e4c4dabf147ac468184881e9ed17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551646"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autoryzowanie dostępu do aplikacji internetowych usługi Azure Active Directory przy użyciu przepływu udzielania kodu OAuth 2.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Jeśli nie poinformujesz serwera o zasobach, które zamierzasz wywołać, serwer nie wyzwoli zasad dostępu warunkowego dla tego zasobu. Aby móc korzystać z wyzwalacza usługi MFA, należy uwzględnić zasób w adresie URL. 
>

Przy użyciu protokołu OAuth 2.0 usługa Azure Active Directory (Azure AD) umożliwia autoryzację dostępu do aplikacji internetowych i internetowych interfejsów API w dzierżawie usługi Azure AD. Ten przewodnik jest niezależny od języka i opisuje, jak wysyłać i odbierać komunikaty HTTP bez używania [bibliotek typu open-source](active-directory-authentication-libraries.md).

Przepływ kodu autoryzacji OAuth 2,0 został opisany w [sekcji 4,1 specyfikacji oauth 2,0](https://tools.ietf.org/html/rfc6749#section-4.1). Służy do uwierzytelniania i autoryzacji w przypadku większości typów aplikacji, w tym aplikacji sieci Web i natywnie zainstalowanych aplikacji.

## <a name="register-your-application-with-your-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi AD
Najpierw Zarejestruj swoją aplikację za pomocą dzierżawy usługi Azure Active Directory (Azure AD). Spowoduje to nadanie aplikacji identyfikatora oraz umożliwi jej otrzymywanie tokenów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
   
1. Wybierz dzierżawę usługi Azure AD, wybierając swoje konto w prawym górnym rogu strony, a następnie wybierając pozycję **Przełącz katalog** nawigacji, a następnie wybierając odpowiednią dzierżawę. 
   - Pomiń ten krok, jeśli masz tylko jedną dzierżawę usługi Azure AD w ramach Twojego konta lub wybrano już odpowiednią dzierżawę usługi Azure AD.
   
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.
   
1. W menu **Azure Active Directory** po lewej stronie wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
   
1. Postępuj zgodnie z monitami i utwórz nową aplikację. Nie ma znaczenia, czy jest to aplikacja sieci Web lub aplikacja klienta publicznego (Mobile & Desktop) dla tego samouczka, ale jeśli chcesz znaleźć konkretne przykłady dla aplikacji sieci Web lub publicznych aplikacji klienckich, zapoznaj się z [przewodnikami szybki start](v1-overview.md).
   
   - **Nazwa** to nazwa aplikacji; opisuje aplikację innym użytkownikom.
   - W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
   - Podaj **Identyfikator URI przekierowania**. W przypadku aplikacji sieci Web jest to podstawowy adres URL aplikacji, w której użytkownicy mogą się logować.  Na przykład `http://localhost:12345`. W przypadku klienta publicznego (Mobile & Desktop) usługa Azure AD używa go do zwracania odpowiedzi tokenów. Wprowadź wartość specyficzną dla swojej aplikacji.  Na przykład `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po zakończeniu rejestracji usługa Azure AD przypisze aplikację unikatowy identyfikator klienta ( **Identyfikator aplikacji**). Ta wartość będzie potrzebna w następnych sekcjach, więc Skopiuj ją ze strony aplikacji.
   
1. Aby znaleźć aplikację w Azure Portal, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Wyświetl wszystkie aplikacje**.

## <a name="oauth-20-authorization-flow"></a>Przepływ autoryzacji OAuth 2,0

Na wysokim poziomie cały przepływ autoryzacji dla aplikacji wygląda następująco:

![Przepływ kodu uwierzytelniania OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Żądanie kodu autoryzacji

Przepływ kodu autoryzacji zaczyna się od klienta kierującego użytkownika do `/authorize` punktu końcowego. W tym żądaniu klient wskazuje uprawnienia wymagane do uzyskania od użytkownika. Punkt końcowy autoryzacji OAuth 2,0 dla dzierżawy można uzyskać, wybierając pozycję **Rejestracje aplikacji > punktów końcowych** w Azure Portal.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametr | Typ | Opis |
| --- | --- | --- |
| dzierżaw |wymagane |`{tenant}`Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawców, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` dla tokenów niezależnych od dzierżawy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji po jej zarejestrowaniu w usłudze Azure AD. Ten temat można znaleźć w witrynie Azure Portal. Kliknij przycisk **Azure Active Directory** na pasku bocznym usługi, kliknij pozycję **rejestracje aplikacji**, a następnie wybierz aplikację. |
| response_type |wymagane |Musi zawierać `code` do przepływu kodu autoryzacji. |
| redirect_uri |zalecane |Redirect_uri aplikacji, w której odpowiedzi uwierzytelniania mogą być wysyłane i odbierane przez aplikację. Musi dokładnie pasować do jednego z redirect_uris zarejestrowanego w portalu, z wyjątkiem tego, że musi on być zakodowany w adresie URL. W przypadku natywnych aplikacji mobilnych & należy użyć wartości domyślnej `https://login.microsoftonline.com/common/oauth2/nativeclient` . |
| response_mode |optional |Określa metodę, która ma zostać użyta do wysłania zwróconego tokenu z powrotem do aplikacji. Może być `query` , `fragment` lub `form_post` . `query` udostępnia kod jako parametr ciągu zapytania w identyfikatorze URI przekierowania. Jeśli żądasz tokenu identyfikatora przy użyciu niejawnego przepływu, nie możesz użyć `query` określonego w [specyfikacji OpenID Connect](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Jeśli żądasz tylko kodu, możesz użyć `query` , `fragment` , lub `form_post` . `form_post` wykonuje wpis zawierający kod dla identyfikatora URI przekierowania. Wartość domyślna to `query` dla przepływu kodu.  |
| stan |zalecane |Wartość zawarta w żądaniu, która jest również zwracana w odpowiedzi tokenu. Losowo wygenerowana unikatowa wartość jest zwykle używana w celu [zapobiegania atakom na fałszerstwo żądań między witrynami](https://tools.ietf.org/html/rfc6749#section-10.12). Ten stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia, takiego jak strona lub widok. |
| zasób | zalecane |Identyfikator URI aplikacji docelowego internetowego interfejsu API (zabezpieczony zasób). Aby znaleźć identyfikator URI aplikacji, w witrynie Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, Otwórz stronę **Ustawienia** aplikacji, a następnie kliknij przycisk **Właściwości**. Może to być również zasób zewnętrzny, taki jak `https://graph.microsoft.com` . Jest to wymagane w jednym z żądań autoryzacji lub tokenu. Aby zapewnić mniejszą liczbę wierszy uwierzytelniania, umieść ją w żądaniu autoryzacji, aby upewnić się, że od użytkownika otrzymano zgodę. |
| scope | **Ignoruj** | W przypadku aplikacji usługi Azure AD w wersji 1 zakresy muszą być konfigurowane statycznie w witrynie Azure Portal w obszarze **Ustawienia**aplikacji, **wymagane uprawnienia**. |
| pytać |optional |Wskaż typ interakcji z użytkownikiem, która jest wymagana.<p> Prawidłowe wartości: <p> *Logowanie*: użytkownik powinien mieć monit o ponowne uwierzytelnienie. <p> *select_account*: użytkownik jest monitowany o wybranie konta, przerwanie logowania jednokrotnego. Użytkownik może wybrać istniejące zalogowane konto, wprowadzić poświadczenia dla konta zapamiętanego lub użyć innego konta. <p> *zgoda*: przyznano zgodę użytkownika, ale należy ją zaktualizować. Użytkownik powinien zostać poproszony o zgodę. <p> *admin_consent*: Administrator powinien otrzymywać monit o zgodę w imieniu wszystkich użytkowników w organizacji. |
| login_hint |optional |Może służyć do wstępnego wypełniania pola Nazwa użytkownika/adres e-mail strony logowania dla użytkownika, jeśli znana jest jego nazwa użytkownika przed czasem. Często aplikacje używają tego parametru podczas ponownego uwierzytelniania, ponieważ już wyodrębnili nazwę użytkownika z poprzedniego logowania przy użyciu tego `preferred_username` żądania. |
| domain_hint |optional |Zawiera wskazówkę dotyczącą dzierżawy lub domeny, której użytkownik powinien używać do logowania. Wartość domain_hint jest zarejestrowaną domeną dla dzierżawcy. Jeśli dzierżawca jest federacyjny do katalogu lokalnego, w usłudze AAD przekierowania do określonego serwera federacyjnego dzierżawcy. |
| code_challenge_method | zalecane    | Metoda używana do kodowania `code_verifier` `code_challenge` parametru. Może być jedną z `plain` lub `S256` . Jeśli jest wykluczony, przyjmuje się, że jest to `code_challenge` zwykły tekst, jeśli `code_challenge` jest uwzględniony. Usługa Azure AAD v 1.0 obsługuje zarówno program `plain` , jak i `S256` . Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | zalecane    | Służy do zabezpieczania kodu autoryzacji za pośrednictwem klucza testowego dla wymiany kodu (PKCE) z klienta natywnego lub publicznego. Wymagane, jeśli `code_challenge_method` jest dołączony. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Jeśli użytkownik jest częścią organizacji, administrator organizacji może wyrazić zgodę na jego imienie lub zrezygnować z niego albo zezwolić użytkownikowi na wyrażanie zgody. Użytkownik otrzymuje opcję zgody tylko wtedy, gdy administrator zezwoli na to.
>
>

W tym momencie użytkownik zostanie poproszony o wprowadzenie poświadczeń i zgodę na uprawnienia wymagane przez aplikację w witrynie Azure Portal. Po uwierzytelnieniu i udzieleniu zgody użytkownik usługi Azure AD wyśle odpowiedź do aplikacji pod `redirect_uri` adresem w żądaniu przy użyciu kodu.

### <a name="successful-response"></a>Pomyślna odpowiedź
Pomyślna odpowiedź może wyglądać następująco:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Opis |
| --- | --- |
| admin_consent |Wartość jest równa true, jeśli administrator wyraził zgodę na monit o żądanie zgody. |
| kod |Kod autoryzacji żądany przez aplikację. Aplikacja może użyć kodu autoryzacji do żądania tokenu dostępu dla zasobu docelowego. |
| session_state |Unikatowa wartość, która identyfikuje bieżącą sesję użytkownika. Ta wartość jest identyfikatorem GUID, ale powinien być traktowany jako nieprzezroczysta wartość, która jest przenoszona bez badania. |
| stan |Jeśli w żądaniu zostanie uwzględniony parametr stanu, ta sama wartość powinna pojawić się w odpowiedzi. Dobrym sposobem, aby aplikacja mogła sprawdzić, czy wartości stanu w żądaniu i odpowiedzi są identyczne przed użyciem odpowiedzi. Pomaga to wykrywać [ataki między lokacjami (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) na klientach. |

### <a name="error-response"></a>Odpowiedź na błąd
Odpowiedzi na błędy mogą być również wysyłane do programu, aby `redirect_uri` aplikacja mogła je odpowiednio obsłużyć.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Opis |
| --- | --- |
| error |Wartość kodu błędu zdefiniowana w sekcji 5,2 [struktury autoryzacji OAuth 2,0](https://tools.ietf.org/html/rfc6749). W następnej tabeli opisano kody błędów zwracanych przez usługę Azure AD. |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie powinien być przyjazny dla użytkownika końcowego. |
| stan |Wartość stanu jest generowaną losowo wartością, która nie jest ponownie używana, która jest wysyłana w żądaniu i zwracana w odpowiedzi, aby zapobiec atakom na żądania między lokacjami (CSRF). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego autoryzacji
W poniższej tabeli opisano różne kody błędów, które mogą być zwracane w `error` parametrze odpowiedzi na błąd.

| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, taki jak brak wymaganego parametru. |Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny, który jest zazwyczaj przechwytywany podczas wstępnego testowania. |
| unauthorized_client |Aplikacja kliencka nie może zażądać kodu autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| access_denied |Niedozwolona zgoda właściciela zasobu |Aplikacja kliencka może powiadomić użytkownika o tym, że nie może wykonać tej czynności, chyba że użytkownik wyraził zgodę. |
| unsupported_response_type |Serwer autoryzacji nie obsługuje typu odpowiedzi w żądaniu. |Napraw i ponownie prześlij żądanie. Jest to błąd programistyczny, który jest zazwyczaj przechwytywany podczas wstępnego testowania. |
| server_error |Serwer napotkał nieoczekiwany błąd. |Ponów żądanie. Te błędy mogą wynikać z warunków tymczasowych. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona z powodu tymczasowego błędu. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. |Wskazuje to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Używanie kodu autoryzacji do żądania tokenu dostępu
Po pobraniu kodu autoryzacji i przyznaniu użytkownikowi uprawnienia do tego celu można zrealizować kod tokenu dostępu do żądanego zasobu, wysyłając żądanie POST do `/token` punktu końcowego:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parametr | Typ | Opis |
| --- | --- | --- |
| dzierżaw |wymagane |`{tenant}`Wartość w ścieżce żądania może służyć do kontrolowania, kto może zalogować się do aplikacji. Dozwolone wartości to identyfikatory dzierżawców, na przykład `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` lub `common` dla tokenów niezależnych od dzierżawy |
| client_id |wymagane |Identyfikator aplikacji przypisany do aplikacji po jej zarejestrowaniu w usłudze Azure AD. Można to znaleźć w Azure Portal. Identyfikator aplikacji jest wyświetlany w ustawieniach rejestracji aplikacji. |
| grant_type |wymagane |Musi być `authorization_code` dla przepływu kodu autoryzacji. |
| kod |wymagane |`authorization_code`Pobrano w poprzedniej sekcji |
| redirect_uri |wymagane | `redirect_uri`Zarejestrowane w aplikacji klienckiej. |
| client_secret |wymagane dla aplikacji sieci Web, niedozwolone dla klientów publicznych |Wpis tajny aplikacji utworzony w witrynie Azure Portal dla aplikacji w obszarze **klucze**. Nie może być używana w aplikacji natywnej (klient publiczny), ponieważ client_secrets nie może być niezawodnie przechowywana na urządzeniach. Jest to wymagane w przypadku aplikacji sieci Web i interfejsów API sieci Web (wszystkich klientów poufnych), które umożliwiają bezpieczne przechowywanie danych po `client_secret` stronie serwera. Client_secret powinna być zakodowana przy użyciu adresu URL przed wysłaniem. |
| zasób | zalecane |Identyfikator URI aplikacji docelowego internetowego interfejsu API (zabezpieczony zasób). Aby znaleźć identyfikator URI aplikacji, w witrynie Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, Otwórz stronę **Ustawienia** aplikacji, a następnie kliknij przycisk **Właściwości**. Może to być również zasób zewnętrzny, taki jak `https://graph.microsoft.com` . Jest to wymagane w jednym z żądań autoryzacji lub tokenu. Aby zapewnić mniejszą liczbę wierszy uwierzytelniania, umieść ją w żądaniu autoryzacji, aby upewnić się, że od użytkownika otrzymano zgodę. Jeśli zarówno w żądaniu autoryzacji, jak i w żądaniu tokenu, parametry zasobu muszą być zgodne. | 
| code_verifier | optional | Ten sam code_verifier, który został użyty w celu uzyskania authorization_code. Wymagane, jeśli w żądaniu udzielenia uprawnienia do kodu autoryzacji użyto PKCE. Aby uzyskać więcej informacji, zobacz [dokument RFC PKCE](https://tools.ietf.org/html/rfc7636)   |

Aby znaleźć identyfikator URI aplikacji, w witrynie Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, Otwórz stronę **Ustawienia** aplikacji, a następnie kliknij przycisk **Właściwości**.

### <a name="successful-response"></a>Pomyślna odpowiedź
Usługa Azure AD zwraca [token dostępu](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) po pomyślnej odpowiedzi. Aby zminimalizować wywołania sieci z aplikacji klienckiej i skojarzonych z nimi opóźnień, aplikacja kliencka powinna buforować tokeny dostępu dla okresu istnienia tokenu określonego w odpowiedzi OAuth 2,0. Aby określić okres istnienia tokenu, użyj `expires_in` `expires_on` wartości parametrów lub.

Jeśli zasób interfejsu API sieci Web zwróci `invalid_token` Kod błędu, może to oznaczać, że zasób ustalił, że token wygasł. Jeśli czas zegara klienta i zasobu jest różny (nazywany "przesunięciem czasowym"), zasób może uznać, że token wygasł przed wyczyszczeniem tokenu z pamięci podręcznej klienta. W takim przypadku należy wyczyścić token z pamięci podręcznej, nawet jeśli nadal jest on obliczany okres istnienia.

Pomyślna odpowiedź może wyglądać następująco:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parametr | Opis |
| --- | --- |
| access_token |Żądany token dostępu.  Jest to ciąg nieprzezroczysty — zależy od tego, co zasób oczekuje na otrzymanie, i nie jest przeznaczony do przeglądania przez klienta. Aplikacja może używać tego tokenu do uwierzytelniania w zabezpieczonym zasobie, takim jak internetowy interfejs API. |
| token_type |Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. Aby uzyskać więcej informacji na temat tokenów okaziciela, zobacz [Struktura autoryzacji OAuth 2.0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak długo token dostępu jest prawidłowy (w sekundach). |
| expires_on |Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu wygaśnięcia. Ta wartość służy do określenia okresu istnienia buforowanych tokenów. |
| zasób |Identyfikator URI aplikacji internetowego interfejsu API (zabezpieczony zasób). |
| scope |Uprawnienia personifikacji przyznane aplikacji klienckiej. Uprawnienie domyślne to `user_impersonation` . Właściciel zabezpieczonego zasobu może rejestrować dodatkowe wartości w usłudze Azure AD. |
| refresh_token |Token odświeżania OAuth 2,0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny dostępu po wygaśnięciu bieżącego tokenu dostępu. Tokeny odświeżania są długotrwałe i mogą być używane do przechowywania zasobów przez dłuższy czas. |
| id_token |Niepodpisany token sieci Web JSON (JWT) reprezentujący [token ID](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Aplikacja może base64Url dekodowanie segmentów tego tokenu, aby zażądać informacji o użytkowniku, który się zalogował. Aplikacja może buforować wartości i wyświetlać je, ale nie należy polegać na nich w przypadku jakichkolwiek ograniczeń autoryzacji lub zabezpieczeń. |

Aby uzyskać więcej informacji na temat tokenów sieci Web JSON, zobacz [specyfikację wersji roboczej JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Aby dowiedzieć się więcej na temat `id_tokens` , zobacz [przepływ połączenia OpenID Connect w wersji 1.0](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Odpowiedź na błąd
Błędy punktu końcowego wystawiania tokenów to kody błędów HTTP, ponieważ klient wywołuje punkt końcowy wystawiania tokenów bezpośrednio. Oprócz kodu stanu HTTP punkt końcowy wystawiania tokenów usługi Azure AD zwraca również dokument JSON z obiektami, które opisują błąd.

Przykładowa odpowiedź na błąd może wyglądać następująco:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |
| error_codes |Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| sygnatura czasowa |Godzina wystąpienia błędu. |
| trace_id |Unikatowy identyfikator żądania, które może pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator żądania, które może pomóc w diagnostyce między składnikami. |

#### <a name="http-status-codes"></a>Kody stanu HTTP
Poniższa tabela zawiera listę kodów stanu HTTP zwracanych przez punkt końcowy wystawiania tokenu. W niektórych przypadkach kod błędu jest wystarczający do opisania odpowiedzi, ale w przypadku wystąpienia błędów należy przeanalizować towarzyszący dokument JSON i zbadać jego kod błędu.

| Kod HTTP | Opis |
| --- | --- |
| 400 |Domyślny kod HTTP. Używane w większości przypadków i jest zazwyczaj spowodowane nieprawidłowo sformułowanym żądaniem. Napraw i ponownie prześlij żądanie. |
| 401 |Nie można przeprowadzić uwierzytelniania. Na przykład w żądaniu brakuje parametru client_secret. |
| 403 |Autoryzacja nie powiodła się. Na przykład użytkownik nie ma uprawnień dostępu do zasobu. |
| 500 |Wystąpił błąd wewnętrzny w usłudze. Ponów żądanie. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kody błędów dla błędów punktu końcowego tokenu
| Kod błędu | Opis | Akcja klienta |
| --- | --- | --- |
| invalid_request |Błąd protokołu, taki jak brak wymaganego parametru. |Napraw i ponownie prześlij żądanie |
| invalid_grant |Kod autoryzacji jest nieprawidłowy lub wygasł. |Wypróbuj nowe żądanie do `/authorize` punktu końcowego |
| unauthorized_client |Uwierzytelniony klient nie ma autoryzacji do korzystania z tego typu udzielania autoryzacji. |Zwykle dzieje się tak, gdy aplikacja kliencka nie jest zarejestrowana w usłudze Azure AD lub nie została dodana do dzierżawy usługi Azure AD użytkownika. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| invalid_client |Uwierzytelnianie klienta nie powiodło się. |Poświadczenia klienta są nieprawidłowe. Aby rozwiązać ten problem, administrator aplikacji aktualizuje poświadczenia. |
| unsupported_grant_type |Serwer autoryzacji nie obsługuje typu przydzielenia autoryzacji. |Zmień typ dotacji w żądaniu. Ten typ błędu powinien wystąpić tylko podczas opracowywania i być wykrywany podczas wstępnego testowania. |
| invalid_resource |Zasób docelowy jest nieprawidłowy, ponieważ nie istnieje, usługa Azure AD nie może go odnaleźć lub nie jest poprawnie skonfigurowana. |Wskazuje to, że zasób, jeśli istnieje, nie został skonfigurowany w dzierżawie. Aplikacja może monitować użytkownika z instrukcją dotyczącą instalowania aplikacji i dodawania jej do usługi Azure AD. |
| interaction_required |Żądanie wymaga interakcji z użytkownikiem. Na przykład wymagany jest dodatkowy krok uwierzytelniania. | Zamiast żądania nieinterakcyjnego spróbuj ponownie, używając interakcyjnego żądania autoryzacji dla tego samego zasobu. |
| temporarily_unavailable |Serwer jest tymczasowo zbyt zajęty, aby obsłużyć żądanie. |Ponów żądanie. Aplikacja kliencka może wyjaśnić użytkownikowi, że jego odpowiedź jest opóźniona ze względu na tymczasowy warunek. |

## <a name="use-the-access-token-to-access-the-resource"></a>Korzystanie z tokenu dostępu w celu uzyskania dostępu do zasobu
Teraz, po pomyślnym pobraniu `access_token` , można użyć tokenu w żądaniach do interfejsów API sieci Web, dołączając je do `Authorization` nagłówka. Specyfikacja [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) wyjaśnia, jak używać tokenów okaziciela w żądaniach HTTP w celu uzyskania dostępu do chronionych zasobów.

### <a name="sample-request"></a>Przykładowe żądanie
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Odpowiedź na błąd
Zabezpieczone zasoby, które implementują kody stanu HTTP 6750. Jeśli żądanie nie zawiera poświadczeń uwierzytelniania lub brakuje tokenu, odpowiedź zawiera `WWW-Authenticate` nagłówek. Gdy żądanie nie powiedzie się, serwer zasobów odpowiada za pomocą kodu stanu HTTP i kodu błędu.

Poniżej przedstawiono przykład niepomyślnej odpowiedzi, gdy żądanie klienta nie zawiera tokenu okaziciela:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametry błędu
| Parametr | Opis |
| --- | --- |
| authorization_uri |Identyfikator URI (fizyczny punkt końcowy) serwera autoryzacji. Ta wartość jest również używana jako klucz wyszukiwania, aby uzyskać więcej informacji na temat serwera z punktu końcowego odnajdywania. <p><p> Klient musi sprawdzić, czy serwer autoryzacji jest zaufany. Gdy zasób jest chroniony przez usługę Azure AD, wystarczy sprawdzić, czy adres URL zaczyna się od `https://login.microsoftonline.com` lub od innej nazwy hosta obsługiwanej przez usługę Azure AD. Zasób specyficzny dla dzierżawy powinien zawsze zwracać identyfikator URI autoryzacji specyficznej dla dzierżawy. |
| error |Wartość kodu błędu zdefiniowana w sekcji 5,2 [struktury autoryzacji OAuth 2,0](https://tools.ietf.org/html/rfc6749). |
| error_description |Bardziej szczegółowy opis błędu. Ten komunikat nie powinien być przyjazny dla użytkownika końcowego. |
| resource_id |Zwraca unikatowy identyfikator zasobu. Aplikacja kliencka może używać tego identyfikatora jako wartości `resource` parametru, gdy żąda tokenu dla zasobu. <p><p> Ważne jest, aby aplikacja kliencka mogła sprawdzić tę wartość. w przeciwnym razie złośliwa usługa może być w stanie wywołać atak **podniesienia** uprawnień <p><p> Zalecana strategia zapobiegania atakom polega na sprawdzeniu, czy jest to `resource_id` zgodne z podstawą dostępu do adresu URL internetowego interfejsu API. Na przykład, jeśli `https://service.contoso.com/data` jest uzyskiwany dostęp, `resource_id` może to być `https://service.contoso.com/` . Aplikacja kliencka musi odrzucić obiekt `resource_id` , który nie zaczyna się od podstawowego adresu URL, chyba że istnieje niezawodny alternatywny sposób na zweryfikowanie identyfikatora. |

#### <a name="bearer-scheme-error-codes"></a>Kody błędów schematów okaziciela
Specyfikacja RFC 6750 definiuje następujące błędy dla zasobów, które używają nagłówka WWW-Authenticate i schematu okaziciela w odpowiedzi.

| Kod stanu HTTP | Kod błędu | Opis | Akcja klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Żądanie nie jest poprawnie sformułowane. Na przykład może brakować parametru lub użyć tego samego parametru dwa razy. |Usuń błąd i spróbuj ponownie wykonać żądanie. Ten typ błędu powinien wystąpić tylko podczas tworzenia i zostanie wykryty podczas wstępnego testowania. |
| 401 |invalid_token |Brak tokenu dostępu, jest on nieprawidłowy lub został odwołany. Wartość parametru error_description zawiera dodatkowe szczegóły. |Zażądaj nowego tokenu od serwera autoryzacji. W przypadku niepowodzenia nowego tokenu wystąpił nieoczekiwany błąd. Wyślij do użytkownika komunikat o błędzie i ponów próbę po opóźnieniu losowym. |
| 403 |insufficient_scope |Token dostępu nie zawiera uprawnień personifikacji wymaganych do uzyskania dostępu do zasobu. |Wyślij nowe żądanie autoryzacji do punktu końcowego autoryzacji. Jeśli odpowiedź zawiera parametr Scope, użyj wartości Scope w żądaniu do zasobu. |
| 403 |insufficient_access |Podmiot tokenu nie ma uprawnień wymaganych do uzyskania dostępu do zasobu. |Monituj użytkownika o użycie innego konta lub zażądanie uprawnień do określonego zasobu. |

## <a name="refreshing-the-access-tokens"></a>Odświeżanie tokenów dostępu

Tokeny dostępu są krótkotrwałe i muszą być odświeżane po wygaśnięciu, aby nadal uzyskiwać dostęp do zasobów. Można odświeżyć `access_token` przez przesłanie kolejnego `POST` żądania do `/token` punktu końcowego, ale tym razem `refresh_token` zamiast `code` .  Tokeny odświeżania są prawidłowe dla wszystkich zasobów, do których klient wyraził zgodę na dostęp — w tym celu token odświeżania wystawiony na żądanie `resource=https://graph.microsoft.com` może służyć do żądania nowego tokenu dostępu dla `resource=https://contoso.com/api` . 

Tokeny odświeżania nie mają określonych okresów istnienia. Zwykle okresy istnienia tokenów odświeżania są stosunkowo długie. Jednak w niektórych przypadkach tokeny odświeżania wygasną, są odwoływane lub nie ma wystarczających uprawnień do żądanej akcji. Aplikacja musi oczekiwać i obsłużyć błędy zwrócone przez punkt końcowy wystawiania tokenów.

Po otrzymaniu odpowiedzi z błędem tokenu odświeżania Odrzuć bieżący token odświeżania i zażądaj nowego kodu autoryzacji lub tokenu dostępu. W szczególności w przypadku używania tokenu odświeżania w przepływie przydzielenia kodu autoryzacji, jeśli otrzymasz odpowiedź z `interaction_required` `invalid_grant` kodami błędów lub, Odrzuć token odświeżenia i zażądaj nowego kodu autoryzacji.

Przykładowe żądanie do punktu końcowego **specyficznego dla dzierżawy** (można również użyć **wspólnego** punktu końcowego), aby uzyskać nowy token dostępu przy użyciu tokenu odświeżania wygląda następująco:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Pomyślna odpowiedź
Pomyślna odpowiedź dotycząca tokenu będzie wyglądać następująco:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parametr | Opis |
| --- | --- |
| token_type |Typ tokenu. Jedyna obsługiwana wartość to **Bearer**. |
| expires_in |Pozostały okres istnienia tokenu w sekundach. Typowa wartość to 3600 (jedna godzina). |
| expires_on |Data i godzina wygaśnięcia tokenu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0: 0Z UTC do czasu wygaśnięcia. |
| zasób |Identyfikuje zabezpieczony zasób, do którego można uzyskać dostęp za pomocą tokenu dostępu. |
| scope |Uprawnienia personifikacji przyznane natywnej aplikacji klienckiej. Uprawnienie domyślne jest **user_impersonation**. Właściciel zasobu docelowego może zarejestrować alternatywne wartości w usłudze Azure AD. |
| access_token |Zażądano nowego tokenu dostępu. |
| refresh_token |Nowe refresh_token OAuth 2,0, których można użyć do żądania nowych tokenów dostępu, gdy ta odpowiedź wygaśnie. |

### <a name="error-response"></a>Odpowiedź na błąd
Przykładowa odpowiedź na błąd może wyglądać następująco:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typów błędów, które występują i mogą być używane do reagowania na błędy. |
| error_description |Konkretny komunikat o błędzie, który może ułatwić deweloperom zidentyfikowanie głównej przyczyny błędu uwierzytelniania. |
| error_codes |Lista kodów błędów specyficznych dla usługi STS, które mogą pomóc w diagnostyce. |
| sygnatura czasowa |Godzina wystąpienia błędu. |
| trace_id |Unikatowy identyfikator żądania, które może pomóc w diagnostyce. |
| correlation_id |Unikatowy identyfikator żądania, które może pomóc w diagnostyce między składnikami. |

Opis kodów błędów i zalecanej akcji klienta można znaleźć w temacie [kody błędów dla błędów punktu końcowego tokenu](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o punkcie końcowym usługi Azure AD v 1.0 i sposobach dodawania uwierzytelniania i autoryzacji do aplikacji sieci Web i interfejsów API sieci Web, zobacz [przykładowe aplikacje](sample-v1-code.md).
