---
title: Zaawansowane użycie uwierzytelniania AuthN/AuthZ
description: Dowiedz się, jak dostosować funkcję uwierzytelniania i autoryzacji w App Service różnych scenariuszach oraz uzyskać oświadczenia użytkowników i różne tokeny.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9335bb62e494fab50f7beadf3d7bbc423d80cf14
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775731"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Zaawansowane użycie uwierzytelniania i autoryzacji w Azure App Service

W tym artykule pokazano, jak dostosować wbudowane uwierzytelnianie i autoryzację w [programie App Service](overview-authentication-authorization.md), a także jak zarządzać tożsamościami z aplikacji. 

Aby szybko rozpocząć pracę, zobacz jeden z następujących samouczków:

* [Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników w usłudze Azure App Service](tutorial-auth-aad.md)
* [Jak skonfigurować aplikację do korzystania z logowania platformy Tożsamości firmy Microsoft](configure-authentication-provider-aad.md)
* [Jak skonfigurować aplikację do używania logowania usługi Facebook](configure-authentication-provider-facebook.md)
* [Jak skonfigurować aplikację do używania logowania usługi Google](configure-authentication-provider-google.md)
* [Jak skonfigurować aplikację do używania logowania usługi Twitter](configure-authentication-provider-twitter.md)
* [Jak skonfigurować aplikację do logowania przy użyciu dostawcy OpenID Connect (wersja zapoznawcza)](configure-authentication-provider-openid-connect.md)
* [Jak skonfigurować aplikację do logowania się przy użyciu funkcji Zaloguj się przy użyciu konta Apple (wersja zapoznawcza)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Korzystanie z wielu dostawców logowania

Konfiguracja portalu nie oferuje kierunkowego sposobu prezentnia użytkownikom wielu dostawców logowania (takich jak Facebook i Twitter). Dodawanie funkcji do aplikacji nie jest jednak trudne. Kroki są opisane w następujący sposób:

Najpierw na stronie **Uwierzytelnianie/autoryzacja** w Azure Portal skonfiguruj każdego dostawcę tożsamości, który chcesz włączyć.

W **akcji do podjęcia, gdy żądanie nie jest uwierzytelniony,** wybierz opcję **Zezwalaj na żądania anonimowe (brak akcji)**.

Na stronie logowania, na pasku nawigacyjnym lub w dowolnej innej lokalizacji aplikacji dodaj link logowania do każdego z włączonych dostawców ( `/.auth/login/<provider>` ). Na przykład:

```html
<a href="/.auth/login/aad">Log in with the Microsoft Identity Platform</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Gdy użytkownik kliknie jeden z linków, zostanie otwarta odpowiedniej strony logowania, aby zalogować użytkownika.

Aby przekierować użytkownika po zalogowaniu się do niestandardowego adresu URL, użyj parametru ciągu zapytania (nie należy mylić z identyfikatorem URI przekierowania w `post_login_redirect_url` konfiguracji dostawcy tożsamości). Aby na przykład przejść do użytkownika po `/Home/Index` zalogowaniu, użyj następującego kodu HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Weryfikowanie tokenów od dostawców

W przypadku logowania skierowanego do klienta aplikacja loguje użytkownika do dostawcy ręcznie, a następnie przesyła token uwierzytelniania do usługi App Service w celu weryfikacji (zobacz [Przepływ uwierzytelniania).](overview-authentication-authorization.md#authentication-flow) Ta walidacja w rzeczywistości nie udziela dostępu do żądanych zasobów aplikacji, ale pomyślna walidacja daje token sesji, który umożliwia dostęp do zasobów aplikacji. 

Aby zweryfikować token dostawcy, App Service aplikację należy najpierw skonfigurować przy użyciu żądanego dostawcy. W czasie wykonywania po pobraniu tokenu uwierzytelniania od dostawcy opublikuj token na stronie `/.auth/login/<provider>` w celu weryfikacji. Na przykład: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Format tokenu różni się nieco w zależności od dostawcy. Szczegółowe informacje można znaleźć w poniższej tabeli:

| Wartość dostawcy | Wymagane w treści żądania | Komentarze |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Właściwość `expires_in` jest opcjonalna. <br/>Podczas żądania tokenu z usług live zawsze żądaj `wl.basic` zakresu. |
| `google` | `{"id_token":"<id_token>"}` | Właściwość `authorization_code` jest opcjonalna. Jeśli zostanie określony, może mu również opcjonalnie towarzyszyć `redirect_uri` właściwość . |
| `facebook`| `{"access_token":"<user_access_token>"}` | Użyj prawidłowego [tokenu dostępu użytkownika z](https://developers.facebook.com/docs/facebook-login/access-tokens) serwisu Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Jeśli token dostawcy zostanie zweryfikowany pomyślnie, interfejs API zwraca element z w treści `authenticationToken` odpowiedzi, która jest tokenem sesji. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Gdy masz token sesji, możesz uzyskać dostęp do chronionych zasobów aplikacji, dodając `X-ZUMO-AUTH` nagłówek do żądań HTTP. Na przykład: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Wyloguj się z sesji

Użytkownicy mogą zainicjować wylogowanie, wysyłając `GET` żądanie do punktu końcowego `/.auth/logout` aplikacji. Żądanie `GET` ma następujące działania:

- Czyszczy pliki cookie uwierzytelniania z bieżącej sesji.
- Usuwa tokeny bieżącego użytkownika z magazynu tokenów.
- W Azure Active Directory i Google usługa wykonuje wylogowanie po stronie serwera u dostawcy tożsamości.

Oto prosty link na stronie internetowej służący do wylogowywania:

```html
<a href="/.auth/logout">Sign out</a>
```

Domyślnie pomyślne wylogowanie przekierowuje klienta do adresu URL `/.auth/logout/done` . Możesz zmienić stronę przekierowania po wylogowaniu, dodając `post_logout_redirect_uri` parametr zapytania. Na przykład:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Zaleca się [zakodowanie](https://wikipedia.org/wiki/Percent-encoding) wartości `post_logout_redirect_uri` .

W przypadku korzystania z w pełni kwalifikowanych adresów URL adres URL musi być hostowany w tej samej domenie lub skonfigurowany jako dozwolony zewnętrzny adres URL przekierowania dla aplikacji. W poniższym przykładzie przekierowywanie do `https://myexternalurl.com` tego, który nie jest hostowany w tej samej domenie:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Uruchom następujące polecenie w Azure Cloud Shell [:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachowywanie fragmentów adresów URL

Po zalogowaniu się użytkowników do aplikacji zwykle chcą oni zostać przekierowani do tej samej sekcji tej samej strony, takiej jak `/wiki/Main_Page#SectionZ` . Jednak ponieważ fragmenty [adresów URL](https://wikipedia.org/wiki/Fragment_identifier) (na przykład ) nigdy nie są wysyłane do serwera, nie są one domyślnie zachowywane po zakończeniu logowania I przekierowaniu z powrotem do `#SectionZ` aplikacji. Następnie użytkownicy uzyskają nieoptymalna środowisko, gdy będą musieli ponownie przejść do żądanej kotwicy. To ograniczenie dotyczy wszystkich rozwiązań uwierzytelniania po stronie serwera.

W App Service uwierzytelniania można zachować fragmenty adresów URL w obrębie logowania OAuth. W tym celu ustaw ustawienie aplikacji o nazwie `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true` na . Możesz to zrobić w [Azure Portal](https://portal.azure.com)lub po prostu uruchomić następujące polecenie w Azure Cloud Shell [:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Uzyskiwanie dostępu do oświadczeń użytkowników

App Service przekazuje oświadczenia użytkownika do aplikacji przy użyciu specjalnych nagłówków. Żądania zewnętrzne nie mogą ustawiać tych nagłówków, więc są obecne tylko wtedy, gdy są ustawione przez App Service. Przykładowe nagłówki to:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod napisany w dowolnym języku lub w dowolnej platformie może uzyskać potrzebne informacje z tych nagłówków. W ASP.NET 4.6 obiekt **ClaimsPrincipal** jest automatycznie ustawiany z odpowiednimi wartościami. ASP.NET Core nie zapewnia jednak oprogramowania pośredniczącego uwierzytelniania, które integruje się z oświadczeniami App Service użytkownika. Aby obejść ten problem, [zobacz MaximeRouiller.Azure.AppService.EasyAuth.](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)

Jeśli [magazyn tokenów](overview-authentication-authorization.md#token-store) jest włączony dla aplikacji, możesz również uzyskać dodatkowe szczegóły dotyczące uwierzytelnionego użytkownika, `/.auth/me` wywołując . Zestawy SDK Mobile Apps zapewniają metody pomocnika do pracy z danymi. Aby uzyskać więcej informacji, zobacz How to use the Azure Mobile Apps Node.js SDK (Jak używać zestawu SDK usługi [Azure Mobile Apps Node.js)](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)i [Work with the .NET backend server SDK for Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)(Praca z zestawem SDK serwera zaplecza platformy .NET dla usługi Azure Mobile Apps).

## <a name="retrieve-tokens-in-app-code"></a>Pobieranie tokenów w kodzie aplikacji

Z kodu serwera tokeny specyficzne dla dostawcy są wstrzykiwane do nagłówka żądania, aby można było łatwo uzyskać do nich dostęp. W poniższej tabeli przedstawiono możliwe nazwy nagłówków tokenów:

| Dostawca | Nazwy nagłówków |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Z kodu klienta (takiego jak aplikacja mobilna lub język JavaScript w przeglądarce) wyślij żądanie HTTP na adres (należy włączyć `GET` `/.auth/me` magazyn[tokenów).](overview-authentication-authorization.md#token-store) Zwrócony JSON zawiera tokeny specyficzne dla dostawcy.

> [!NOTE]
> Tokeny dostępu są dostępne dla zasobów dostawcy, więc są obecne tylko w przypadku skonfigurowania dostawcy przy użyciu tajnego klienta. Aby dowiedzieć się, jak uzyskać tokeny odświeżania, zobacz Odświeżanie tokenów dostępu.

## <a name="refresh-identity-provider-tokens"></a>Odświeżanie tokenów dostawcy tożsamości

Po wygaśnięciu tokenu dostępu dostawcy (a nie [tokenu](#extend-session-token-expiration-grace-period)sesji ) należy ponownie uwierzytelnić użytkownika przed użyciem tego tokenu ponownie. Możesz uniknąć wygaśnięcia tokenu, wywołując `GET` punkt `/.auth/refresh` końcowy aplikacji. Po wywołaeniu App Service automatycznie odświeża tokeny dostępu w [magazynie tokenów](overview-authentication-authorization.md#token-store) dla uwierzytelnionego użytkownika. Kolejne żądania tokenów przez kod aplikacji otrzymają odświeżone tokeny. Jednak aby odświeżanie tokenu działało, magazyn tokenów musi zawierać [tokeny odświeżania](https://auth0.com/learn/refresh-tokens/) dla dostawcy. Sposób uzyskania tokenów odświeżania jest udokumentowany przez każdego dostawcę, ale następująca lista zawiera krótkie podsumowanie:

- **Google:** dołącz parametr `access_type=offline` ciągu zapytania do wywołania `/.auth/login/google` interfejsu API. Jeśli korzystasz z Mobile Apps SDK, możesz dodać parametr do jednego z przeciążeń `LogicAsync` (zobacz [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook:** nie zapewnia tokenów odświeżania. Długoterminowe tokeny wygasają w ciągu 60 dni (zobacz Wygasanie i rozszerzenie tokenów dostępu usługi [Facebook).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter:** tokeny dostępu nie wygasają (zobacz [Twitter OAuth FAQ (Twitter OAuth — często zadawane pytania).](https://developer.twitter.com/en/docs/authentication/faq)
- **Azure Active Directory:** W [https://resources.azure.com](https://resources.azure.com) programie wykonaj następujące czynności:
    1. W górnej części strony wybierz pozycję **Odczyt/Zapis.**
    2. W przeglądarce po lewej stronie przejdź do subskrypcji **>** **_\<subscription\_name_** > **resourceGroups** > **_** > providers \<resource\_group\_name> _ >  **Microsoft.Web**  >  **sites** > **_ \<app\_name> _** >   >  **config authsettings**. 
    3. Kliknij pozycję **Edytuj**.
    4. Zmodyfikuj następującą właściwość. Zastąp _\<app\_id>_ Azure Active Directory identyfikatorem aplikacji usługi, do której chcesz uzyskać dostęp.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kliknij **pozycję Umieść**. 

Po skonfigurowaniu dostawcy możesz znaleźć token odświeżania i czas wygaśnięcia [tokenu dostępu](#retrieve-tokens-in-app-code) w magazynie tokenów. 

Aby odświeżyć token dostępu w dowolnym momencie, po prostu `/.auth/refresh` wywołaj w dowolnym języku. Poniższy fragment kodu używa biblioteki jQuery do odświeżania tokenów dostępu z klienta JavaScript.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Jeśli użytkownik odwoła uprawnienia przyznane aplikacji, wywołanie usługi może się nie `/.auth/me` powieść z `403 Forbidden` odpowiedzią. Aby zdiagnozować błędy, sprawdź dzienniki aplikacji, aby uzyskać szczegółowe informacje.

## <a name="extend-session-token-expiration-grace-period"></a>Przedłużenie okresu prolongaty wygaśnięcia tokenu sesji

Uwierzytelniona sesja wygasa po 8 godzinach. Po wygaśnięciu sesji uwierzytelnionego domyślnie istnieje 72-godzinny okres prolongaty. W tym okresie prolongaty możesz odświeżyć token sesji przy użyciu App Service bez ponownego uwierzytelnienia użytkownika. Możesz po prostu wywołać wywołanie, gdy token sesji stanie się nieprawidłowy i nie musisz samodzielnie `/.auth/refresh` śledzić wygaśnięcia tokenu. Po upływie 72-godzinnego okresu prolongaty użytkownik musi zalogować się ponownie, aby uzyskać prawidłowy token sesji.

Jeśli 72 godziny to za mało czasu, możesz rozszerzyć to okno wygasania. Wydłużenie okresu ważności na długi czas może mieć znaczące konsekwencje dla bezpieczeństwa (na przykład w przypadku wycieku lub kradzieży tokenu uwierzytelniania). Dlatego należy pozostawić wartość domyślną 72 godziny lub ustawić okres rozszerzenia na najmniejszą wartość.

Aby rozszerzyć domyślne okno wygasania, uruchom następujące polecenie w Cloud Shell [.](../cloud-shell/overview.md)

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Okres prolongaty dotyczy tylko App Service sesji uwierzytelnianej, a nie tokenów od dostawców tożsamości. Nie ma okresu prolongaty dla wygasłych tokenów dostawcy. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Ograniczanie domeny kont logowania

Zarówno konto Microsoft, jak i Azure Active Directory umożliwiają logowanie się z wielu domen. Na przykład konto Microsoft umożliwia _outlook.com,_ _live.com_ i _hotmail.com_ kont. Usługa Azure AD zezwala na dowolną liczbę domen niestandardowych dla kont logowania. Możesz jednak przyspieszyć działania użytkowników bezpośrednio na własnej stronie logowania usługi Azure AD (takiej jak `contoso.com` ). Aby zasugerować nazwę domeny kont logowania, wykonaj następujące kroki.

W programie przejdź do subskrypcji [https://resources.azure.com](https://resources.azure.com) > **_\<subscription\_name_** > **resourceGroups** > ** **_> providers  \<resource\_group\_name> _ >  **Microsoft.Web**  >  **sites** > **_ \<app\_name> _** >   >  **config authsettings**. 

Kliknij **pozycję Edytuj**, zmodyfikuj następującą właściwość, a następnie kliknij pozycję **Umieść**. Pamiętaj, aby _\<domain\_name>_ zastąpić nazwę domeną, której potrzebujesz.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

To ustawienie dołącza parametr ciągu `domain_hint` zapytania do adresu URL przekierowania logowania. 

> [!IMPORTANT]
> Klient może usunąć parametr po otrzymaniu adresu URL przekierowania, a następnie `domain_hint` zalogować się przy użyciu innej domeny. Dlatego chociaż ta funkcja jest wygodna, nie jest funkcją zabezpieczeń.
>

## <a name="authorize-or-deny-users"></a>Autoryzowanie lub odrzucanie użytkowników

Chociaż App Service zajmuje się najprostszym przypadekem autoryzacji (tj. odrzucanie nieuwierzytanych żądań), aplikacja może wymagać bardziej szczegółowego zachowania autoryzacji, takiego jak ograniczenie dostępu tylko do określonej grupy użytkowników. W niektórych przypadkach należy napisać niestandardowy kod aplikacji w celu umożliwienia lub odmowy dostępu zalogowamu użytkownikowi. W innych przypadkach App Service lub dostawcy tożsamości mogą być w stanie pomóc bez konieczności zmiany kodu.

- [Poziom serwera](#server-level-windows-apps-only)
- [Poziom dostawcy tożsamości](#identity-provider-level)
- [Poziom aplikacji](#application-level)

### <a name="server-level-windows-apps-only"></a>Poziom serwera (tylko aplikacje systemu Windows)

Dla dowolnej aplikacji systemu Windows można zdefiniować zachowanie autoryzacji serwera internetowego usług IIS, edytując *Web.config* plików. Aplikacje systemu Linux nie korzystają z usług IIS i nie można ich skonfigurować za *pośrednictwemWeb.config*.

1. Przejdź do strony `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. W eksploratorze przeglądarki plików App Service przejdź do *witryny/wwwroot.* Jeśli plik *Web.config* nie istnieje, utwórz go, wybierając **+**  >  **pozycję Nowy plik.** 

1. Wybierz ołówek dla *Web.config,* aby go edytować. Dodaj następujący kod konfiguracji i kliknij przycisk **Zapisz.** Jeśli *Web.config* już istnieje, wystarczy dodać `<authorization>` element z jego wszystkim. Dodaj konta, na które chcesz zezwolić, w `<allow>` elemencie .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Poziom dostawcy tożsamości

Dostawca tożsamości może zapewnić pewną autoryzację typu "turn-key". Na przykład:

- Na [Azure App Service](configure-authentication-provider-aad.md)można zarządzać [dostępem na](../active-directory/manage-apps/what-is-access-management.md) poziomie przedsiębiorstwa bezpośrednio w usłudze Azure AD. Aby uzyskać instrukcje, [zobacz How to remove a user's access to an application](../active-directory/manage-apps/methods-for-removing-user-access.md)(Jak usunąć dostęp użytkownika do aplikacji).
- W [przypadku](configure-authentication-provider-google.md)projektów google interfejsu [](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) API Google należących do organizacji można skonfigurować tak, aby zezwalały na dostęp tylko użytkownikom w organizacji (zobacz stronę pomocy technicznej firmy Google Konfigurowanie protokołu [ **OAuth 2.0).**](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Poziom aplikacji

Jeśli którykolwiek z innych poziomów nie zapewnia potrzebnej autoryzacji lub jeśli platforma lub dostawca tożsamości nie jest obsługiwany, należy napisać kod niestandardowy w celu autoryzowania użytkowników na podstawie oświadczeń [użytkownika.](#access-user-claims)

## <a name="updating-the-configuration-version"></a>Aktualizowanie wersji konfiguracji

Istnieją dwie wersje interfejsu API zarządzania dla funkcji uwierzytelnianie/autoryzacja. Wersja V2 jest wymagana do obsługi uwierzytelniania w Azure Portal. Aplikacja korzystająca już z interfejsu API w wersji 1 może zostać uaktualniona do wersji V2 po wymusieniu kilku zmian. W szczególności konfigurację tajnych należy przenieść do ustawień aplikacji z kartą miejsca. Można to zrobić automatycznie w sekcji "Uwierzytelnianie" w portalu dla aplikacji.

> [!WARNING]
> Migracja do wersji 2 spowoduje wyłączenie zarządzania funkcją uwierzytelniania/autoryzacji usługi App Service dla aplikacji za pośrednictwem niektórych klientów, takich jak istniejące środowisko w programie Azure Portal, interfejsie wiersza polecenia platformy Azure i Azure PowerShell. Nie można tego cofnąć.

Interfejs API w wersji 2 nie obsługuje tworzenia ani edytowania konta Microsoft jako odrębnego dostawcy, co zostało zrobione w wersji 1. Zamiast tego wykorzystuje zbieżną platformę [tożsamości firmy Microsoft](../active-directory/develop/v2-overview.md) do logowania użytkowników przy użyciu zarówno usługi Azure AD, jak i osobistych kont Microsoft. Podczas przełączania do interfejsu API w wersji 2 konfiguracja Azure Active Directory 1 służy do konfigurowania dostawcy platformy tożsamości firmy Microsoft. Dostawca kont Microsoft w wersji 1 zostanie przeprowadzony w procesie migracji i będzie nadal działać w zwykły sposób, ale zaleca się przejście do nowszego modelu platformy Microsoft Identity Platform. Aby dowiedzieć się więcej, zobacz [Support for Microsoft Account provider registrations](#support-for-microsoft-account-provider-registrations) (Pomoc techniczna dla rejestracji dostawców kont Microsoft).

Proces zautomatyzowanej migracji przeniesie wpisy tajne dostawcy do ustawień aplikacji, a następnie przekonwertuje pozostałą część konfiguracji na nowy format. Aby użyć migracji automatycznej:

1. Przejdź do aplikacji w portalu i wybierz opcję menu **Uwierzytelnianie.**
1. Jeśli aplikacja jest skonfigurowana przy użyciu modelu w wersji 1, zostanie wyświetlony **przycisk Uaktualnij.**
1. Przejrzyj opis w wierszu potwierdzenia. Jeśli wszystko jest gotowe do przeprowadzenia migracji, kliknij przycisk **Uaktualnij** w wierszu polecenia.

### <a name="manually-managing-the-migration"></a>Ręczne zarządzanie migracją

Poniższe kroki umożliwią ręczną migrację aplikacji do interfejsu API w wersji 2, jeśli nie chcesz używać automatycznej wersji wymienionej powyżej.

#### <a name="moving-secrets-to-application-settings"></a>Przenoszenie wpisów tajnych do ustawień aplikacji

1. Pobierz istniejącą konfigurację przy użyciu interfejsu API w wersji 1:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   W wynikowy ładunek JSON zanotuj wartość tajnego używane dla każdego skonfigurowanego dostawcy:

   * Aad: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Konto Microsoft: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Wartości tajne są ważnymi poświadczeniami zabezpieczeń i należy je dokładnie obsługiwać. Nie należy udostępniać tych wartości ani utrwalać ich na komputerze lokalnym.

1. Utwórz ustawienia aplikacji typu slot-sticky dla każdej wartości tajnej. Możesz wybrać nazwę każdego ustawienia aplikacji. Ta wartość powinna odpowiadać wartości uzyskanej w poprzednim kroku lub odwoływać się do Key Vault [tajnego](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) utworzonego przy użyciu tej wartości.

   Aby utworzyć ustawienie, możesz użyć Azure Portal lub uruchomić odmianę następujących ustawień dla każdego dostawcy:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Ustawienia aplikacji dla tej konfiguracji powinny być oznaczone jako kartoklejne, co oznacza, że nie będą przechodzić między środowiskami podczas operacji [zamiany miejsca](./deploy-staging-slots.md). Jest to spowodowane tym, że sama konfiguracja uwierzytelniania jest powiązana ze środowiskiem. 

1. Utwórz nowy plik JSON o nazwie `authsettings.json` . Weź otrzymane wcześniej dane wyjściowe i usuń z niego każdą wartość w kluczu tajnym. Zapisz pozostałe dane wyjściowe w pliku, upewniąc się, że nie jest dołączony wpis tajny. W niektórych przypadkach konfiguracja może zawierać tablice zawierające puste ciągi. Upewnij się, `microsoftAccountOAuthScopes` że wartość nie jest taka, a jeśli tak, przełącz ją na wartość `null` .

1. Dodaj właściwość, do której wskazuje nazwę ustawienia aplikacji utworzoną `authsettings.json` wcześniej dla każdego dostawcy:
 
   * Aad: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Konto Microsoft: `microsoftAccountClientSecretSettingName`

   Przykładowy plik po tej operacji może wyglądać podobnie do poniższego, w tym przypadku skonfigurowanego tylko dla usługi AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Prześlij ten plik jako nową konfigurację uwierzytelniania/autoryzacji dla aplikacji:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Po tym gestu sprawdź, czy aplikacja nadal działa zgodnie z oczekiwaniami.

1. Usuń plik używany w poprzednich krokach.

Migrowanie aplikacji w celu przechowywania wpisów tajnych dostawcy tożsamości jako ustawień aplikacji.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Obsługa rejestracji dostawców kont Microsoft

Jeśli istniejąca konfiguracja zawiera dostawcę konta Microsoft i nie zawiera dostawcy Azure Active Directory, możesz przełączyć konfigurację do dostawcy Azure Active Directory, a następnie przeprowadzić migrację. W tym celu:

1. Przejdź do [**Rejestracje aplikacji**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Portal i znajdź rejestrację skojarzoną z dostawcą konta Microsoft. Może ona być pod nagłówkiem "Aplikacje z konta osobistego".
1. Przejdź do strony "Uwierzytelnianie" dla rejestracji. W obszarze "Redirect URIs" (Adresy UR PRZEKIEROWANIA) powinien zostać wyświetlony wpis kończący się na `/.auth/login/microsoftaccount/callback` . Skopiuj ten URI.
1. Dodaj nowy URI, który pasuje do skopiowanego właśnie skopiowanego, z wyjątkiem sytuacji, w których kończy się on na `/.auth/login/aad/callback` . Dzięki temu rejestracja będzie używana przez konfigurację uwierzytelniania App Service autoryzacji.
1. Przejdź do konfiguracji App Service uwierzytelniania/autoryzacji dla aplikacji.
1. Zbierz konfigurację dostawcy kont Microsoft.
1. Skonfiguruj dostawcę Azure Active Directory przy użyciu trybu zarządzania "Zaawansowane", używając identyfikatora klienta i wartości klucz tajny klienta zebranych w poprzednim kroku. W polu Adres URL wystawcy użyj parametru i zastąp parametr punktem końcowym uwierzytelniania dla środowiska chmury (np. " " dla globalnej platformy Azure), zastępując również parametr identyfikatorem katalogu `<authentication-endpoint>/<tenant-id>/v2.0` *\<authentication-endpoint>* [](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) https://login.microsoftonline.com *\<tenant-id>* **(dzierżawy).**
1. Po zapisaniu konfiguracji przetestuj przepływ logowania, przechodząc w przeglądarce do punktu końcowego w witrynie i ukończ `/.auth/login/aad` przepływ logowania.
1. W tym momencie konfiguracja została pomyślnie skopiowana, ale istniejąca konfiguracja dostawcy kont Microsoft pozostaje niezmieniona. Przed jej usunięciem upewnij się, że wszystkie części aplikacji odwołują się do dostawcy Azure Active Directory za pośrednictwem linków logowania itp. Sprawdź, czy wszystkie części aplikacji działają zgodnie z oczekiwaniami.
1. Po weryfikacji, czy wszystko działa w odniesieniu do dostawcy Azure Active Directory AAD, możesz usunąć konfigurację dostawcy kont Microsoft.

> [!WARNING]
> Dwie rejestracje można zmodyfikować, modyfikując obsługiwane [typy kont](../active-directory/develop/supported-accounts-validation.md) na potrzeby rejestracji aplikacji usługi AAD. Jednak wymuś to nowy monit o wyrażenie zgody dla użytkowników konta Microsoft, a oświadczenia tożsamości tych użytkowników mogą mieć różną strukturę, szczególnie zmieniając wartości, ponieważ jest używany nowy identyfikator `sub` aplikacji. Takie podejście nie jest zalecane, chyba że zostanie dokładnie zrozumiane. Zamiast tego należy poczekać na obsługę dwóch rejestracji na powierzchni interfejsu API w wersji 2.

#### <a name="switching-to-v2"></a>Przełączanie do wersji 2

Po wykonaniu powyższych kroków przejdź do aplikacji w Azure Portal. Wybierz sekcję "Uwierzytelnianie (wersja zapoznawcza)". 

Alternatywnie możesz wykonać żądanie PUT względem zasobu `config/authsettingsv2` w ramach zasobu lokacji. Schemat ładunku jest taki sam jak przechwycony w sekcji [Konfigurowanie przy użyciu](#config-file) pliku.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurowanie przy użyciu pliku (wersja zapoznawcza)

Ustawienia uwierzytelniania można opcjonalnie skonfigurować za pomocą pliku dostarczonego przez wdrożenie. Może to być wymagane przez niektóre funkcje wersji zapoznawczej usługi App Service uwierzytelnianie/autoryzacja.

> [!IMPORTANT]
> Należy pamiętać, że ładunek aplikacji, a w związku z tym ten plik, może przechodzić między środowiskami, tak jak w [przypadku miejsc](./deploy-staging-slots.md). Prawdopodobnie do każdego miejsca będzie przypięta inna rejestracja aplikacji. W takich przypadkach należy nadal używać standardowej metody konfiguracji zamiast pliku konfiguracji.

### <a name="enabling-file-based-configuration"></a>Włączanie konfiguracji opartej na plikach

> [!CAUTION]
> W wersji zapoznawczej włączenie konfiguracji opartej na plikach spowoduje wyłączenie zarządzania funkcją uwierzytelniania/autoryzacji usługi App Service dla aplikacji za pośrednictwem niektórych klientów, takich jak Azure Portal, interfejs wiersza polecenia platformy Azure i Azure PowerShell.

1. Utwórz nowy plik JSON dla konfiguracji w katalogu głównym projektu (wdrożony w katalogu D:\home\site\wwwroot w aplikacji internetowej/funkcji). Wprowadź żądaną konfigurację zgodnie z odwołaniem [do konfiguracji opartej na plikach](#configuration-file-reference). W przypadku modyfikowania istniejącej Azure Resource Manager konfiguracji należy przetłumaczyć właściwości przechwycone w kolekcji `authsettings` na plik konfiguracji.

2. Zmodyfikuj istniejącą konfigurację przechwyconą w [interfejsach API Azure Resource Manager](../azure-resource-manager/management/overview.md) w obszarze `Microsoft.Web/sites/<siteName>/config/authsettings` . Aby to zmodyfikować, można użyć szablonu [Azure Resource Manager](../azure-resource-manager/templates/overview.md) narzędzia, takiego [jak Azure Resource Explorer](https://resources.azure.com/). W kolekcji authsettings należy ustawić trzy właściwości (i mogą usuwać inne):

    1.  Ustaw `enabled` wartość "true"
    2.  Ustaw `isAuthFromFile` wartość "true"
    3.  Ustaw `authFilePath` na nazwę pliku (na przykład "auth.jssię")

> [!NOTE]
> Format pliku różni `authFilePath` się w zależności od platformy. W systemie Windows obsługiwane są ścieżki względne i bezwzględne. Zalecana jest względna. W przypadku systemu Linux obecnie obsługiwane są tylko ścieżki bezwzględne, więc wartość ustawienia powinna być "/home/site/wwwroot/auth.jsna" lub podobna.

Po dokonaniu tej aktualizacji konfiguracji zawartość pliku będzie służyć do definiowania zachowania usługi App Service/autoryzacji dla tej lokacji. Jeśli kiedykolwiek chcesz wrócić do Azure Resource Manager konfiguracji, możesz to zrobić, ustawiając z powrotem `isAuthFromFile` wartość "false".

### <a name="configuration-file-reference"></a>Informacje o pliku konfiguracji

Wszystkie wpisy tajne, do których będzie można odwoływać się z pliku konfiguracji, muszą być przechowywane jako [ustawienia aplikacji.](./configure-common.md#configure-app-settings) Ustawienia można nazwać w imieniu użytkownika. Upewnij się, że odwołania z pliku konfiguracji są używane w tych samych kluczach.

Następujące elementy wyczerpuje możliwe opcje konfiguracji w pliku:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Przypinanie aplikacji do określonej wersji środowiska uruchomieniowego uwierzytelniania

Po włączeniu uwierzytelniania/autoryzacji oprogramowanie pośredniczące platformy jest wstrzykiwane do potoku żądania HTTP, zgodnie z opisem w [przeglądzie funkcji](overview-authentication-authorization.md#how-it-works). To oprogramowanie pośredniczące platformy jest okresowo aktualizowane o nowe funkcje i ulepszenia w ramach rutynowych aktualizacji platformy. Domyślnie aplikacja internetowa lub funkcja będzie uruchamiana w najnowszej wersji tego oprogramowania pośredniczącego platformy. Te aktualizacje automatyczne są zawsze zgodne wstecz. Jednak w rzadkich przypadkach, gdy ta automatyczna aktualizacja wprowadza problem ze środowiskiem uruchomieniowym aplikacji internetowej lub aplikacji funkcji, można tymczasowo wycofać poprzednią wersję oprogramowania pośredniczącego. W tym artykule wyjaśniono, jak tymczasowo przypiąć aplikację do określonej wersji oprogramowania pośredniczącego uwierzytelniania.

### <a name="automatic-and-manual-version-updates"></a>Automatyczne i ręczne aktualizacje wersji 

Aplikację można przypiąć do określonej wersji oprogramowania pośredniczącego platformy, `runtimeVersion` ustawiając ustawienie dla aplikacji. Aplikacja zawsze działa w najnowszej wersji, chyba że zdecydujesz się jawnie przypiąć ją z powrotem do określonej wersji. Będzie obsługiwanych kilka wersji jednocześnie. Jeśli przypniesz do nieprawidłowej wersji, która nie jest już obsługiwana, aplikacja zamiast tego użyje najnowszej wersji. Aby zawsze uruchamiać najnowszą wersję, ustaw `runtimeVersion` wartość ~1. 

### <a name="view-and-update-the-current-runtime-version"></a>Wyświetlanie i aktualizowanie bieżącej wersji środowiska uruchomieniowego

Możesz zmienić wersję środowiska uruchomieniowego używaną przez aplikację. Nowa wersja środowiska uruchomieniowego powinna obowiązywać po ponownym uruchomieniu aplikacji. 

#### <a name="view-the-current-runtime-version"></a>Wyświetlanie bieżącej wersji środowiska uruchomieniowego

Bieżącą wersję oprogramowania pośredniczącego do uwierzytelniania platformy można wyświetlić przy użyciu interfejsu wiersza polecenia platformy Azure lub jednego z wbudowanych punktów końcowych HTTP wersji w aplikacji.

##### <a name="from-the-azure-cli"></a>Z interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure wyświetl bieżącą wersję oprogramowania pośredniczącego za pomocą [polecenia az webapp auth show.](/cli/azure/webapp/auth#az_webapp_auth_show)

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

W tym kodzie zastąp `<my_app_name>` nazwą swojej aplikacji. Zastąp `<my_resource_group>` również nazwą grupy zasobów dla aplikacji.

Pole zostanie wyświetlony `runtimeVersion` w danych wyjściowych interfejsu wiersza polecenia. Będzie on podobny do następujących przykładowych danych wyjściowych, które zostały obcinane w celu zapewnienia przejrzystości: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Z punktu końcowego wersji

Możesz również przejść do punktu końcowego /.auth/version w aplikacji, aby wyświetlić bieżącą wersję oprogramowania pośredniczącego, w których jest uruchomiona aplikacja. Będzie on podobny do następujących przykładowych danych wyjściowych:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Aktualizowanie bieżącej wersji środowiska uruchomieniowego

Za pomocą interfejsu wiersza polecenia platformy Azure możesz zaktualizować ustawienie w aplikacji za pomocą polecenia `runtimeVersion` [az webapp auth update.](/cli/azure/webapp/auth#az_webapp_auth_update)

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Zastąp `<my_app_name>` nazwą swojej aplikacji. Zastąp `<my_resource_group>` również nazwą grupy zasobów dla aplikacji. Zastąp również `<version>` prawidłową wersją środowiska uruchomieniowego 1.x lub `~1` najnowszą wersję. Informacje o wersji można znaleźć w różnych wersjach środowiska uruchomieniowego [tutaj] ( w celu określenia https://github.com/Azure/app-service-announcements) wersji, do których należy przypiąć kod.

To polecenie można uruchomić z [Azure Cloud Shell,](../cloud-shell/overview.md) wybierając polecenie **Wypróbuj w** poprzednim przykładzie kodu. Możesz również użyć interfejsu wiersza polecenia [platformy Azure lokalnie,](/cli/azure/install-azure-cli) aby wykonać to polecenie po wykonaniu [polecenia az login](/cli/azure/reference-index#az_login) w celu zalogowania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników](tutorial-auth-aad.md)
