---
title: Zaawansowane użycie AuthN/autoryzacji
description: Dowiedz się, jak dostosować funkcję uwierzytelniania i autoryzacji w App Service dla różnych scenariuszy oraz uzyskać oświadczenia użytkowników i inne tokeny.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 0e07dc42a45a697b293e2ebc90bdd92aa924f071
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302037"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Zaawansowane użycie uwierzytelniania i autoryzacji w Azure App Service

W tym artykule opisano sposób dostosowywania wbudowanego [uwierzytelniania i autoryzacji w programie App Service](overview-authentication-authorization.md)oraz do zarządzania tożsamościami z poziomu aplikacji. 

Aby szybko rozpocząć pracę, zobacz jeden z następujących samouczków:

* [Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników w usłudze Azure App Service](tutorial-auth-aad.md)
* [Jak skonfigurować aplikację do używania logowania w usłudze Azure Active Directory](configure-authentication-provider-aad.md)
* [Jak skonfigurować aplikację do używania logowania usługi Facebook](configure-authentication-provider-facebook.md)
* [Jak skonfigurować aplikację do używania logowania usługi Google](configure-authentication-provider-google.md)
* [Jak skonfigurować aplikację do używania logowania za pomocą konta Microsoft](configure-authentication-provider-microsoft.md)
* [Jak skonfigurować aplikację do używania logowania usługi Twitter](configure-authentication-provider-twitter.md)
* [Jak skonfigurować aplikację do logowania przy użyciu dostawcy połączenia usługi OpenID Connect (wersja zapoznawcza)](configure-authentication-provider-openid-connect.md)

## <a name="use-multiple-sign-in-providers"></a>Korzystanie z wielu dostawców logowania

Konfiguracja portalu nie oferuje możliwości podkluczego sposobu prezentowania wielu dostawców logowania użytkownikom (na przykład Facebook i Twitter). Nie ma jednak trudności z dodaniem funkcjonalności do aplikacji. Kroki są opisane w następujący sposób:

Najpierw na stronie **uwierzytelnianie/autoryzacja** w Azure Portal Skonfiguruj każdy dostawca tożsamości, który ma zostać włączony.

W obszarze **Akcja do wykonania, gdy żądanie nie zostanie uwierzytelnione**, wybierz opcję **Zezwalaj na żądania anonimowe (bez akcji)**.

Na stronie logowania lub na pasku nawigacyjnym lub w dowolnej innej lokalizacji aplikacji Dodaj łącze logowania do każdego z włączonych dostawców ( `/.auth/login/<provider>` ). Przykład:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Gdy użytkownik kliknie jedno z linków, na odpowiedniej stronie logowania zostanie otwarta strona logowania użytkownika.

Aby przekierować użytkownika po zalogowaniu się do niestandardowego adresu URL, użyj `post_login_redirect_url` parametru ciągu zapytania (nie należy mylić z identyfikatorem URI przekierowania w konfiguracji dostawcy tożsamości). Na przykład, aby nawigować do użytkownika `/Home/Index` po zalogowaniu, użyj następującego kodu HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Weryfikowanie tokenów od dostawców

W przypadku logowania po stronie klienta program automatycznie loguje użytkownika do dostawcy, a następnie przesyła token uwierzytelniania do App Service na potrzeby weryfikacji (zobacz [przepływ uwierzytelniania](overview-authentication-authorization.md#authentication-flow)). Takie sprawdzenie poprawności nie pozwala na uzyskanie dostępu do żądanych zasobów aplikacji, ale pomyślne sprawdzenie poprawności spowoduje użycie tokenu sesji umożliwiającego dostęp do zasobów aplikacji. 

Aby sprawdzić poprawność tokenu dostawcy, aplikacja App Service należy najpierw skonfigurować przy użyciu żądanego dostawcy. W czasie wykonywania, po pobraniu tokenu uwierzytelniania od dostawcy, Opublikuj token w celu `/.auth/login/<provider>` sprawdzenia poprawności. Przykład: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Format tokenu jest nieco różny w zależności od dostawcy. Szczegółowe informacje znajdują się w poniższej tabeli:

| Wartość dostawcy | Wymagane w treści żądania | Komentarze |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in`Właściwość jest opcjonalna. <br/>Żądając tokenu od usług Live, zawsze Żądaj `wl.basic` zakresu. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code`Właściwość jest opcjonalna. Po określeniu można również opcjonalnie dołączyć do `redirect_uri` właściwości. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Użyj prawidłowego [tokenu dostępu użytkownika](https://developers.facebook.com/docs/facebook-login/access-tokens) z serwisu Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

W przypadku pomyślnego zweryfikowania tokenu dostawcy interfejs API zwraca wartość z `authenticationToken` w treści odpowiedzi, która jest tokenem sesji. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Gdy masz ten token sesji, możesz uzyskać dostęp do chronionych zasobów aplikacji, dodając `X-ZUMO-AUTH` nagłówek do żądań HTTP. Przykład: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Wyloguj się z sesji

Użytkownicy mogą inicjować wylogowywanie, wysyłając `GET` żądanie do `/.auth/logout` punktu końcowego aplikacji. `GET`Żądanie wykonuje następujące czynności:

- Czyści pliki cookie uwierzytelniania z bieżącej sesji.
- Usuwa z magazynu tokenów tokeny bieżącego użytkownika.
- W przypadku Azure Active Directory i Google program wykonuje wylogowywanie po stronie serwera dla dostawcy tożsamości.

Oto prosty link na stronie internetowej służący do wylogowywania:

```html
<a href="/.auth/logout">Sign out</a>
```

Domyślnie pomyślne wylogowanie przekierowuje klienta do adresu URL `/.auth/logout/done` . Można zmienić stronę przekierowywanie po wylogowaniu, dodając `post_logout_redirect_uri` parametr zapytania. Przykład:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Zaleca się [zakodowanie](https://wikipedia.org/wiki/Percent-encoding) wartości `post_logout_redirect_uri` .

W przypadku korzystania z w pełni kwalifikowanych adresów URL musi być hostowana w tej samej domenie lub skonfigurowana jako dozwolony zewnętrzny adres URL przekierowania dla aplikacji. W poniższym przykładzie, aby przekierować do `https://myexternalurl.com` tego elementu nie są hostowane w tej samej domenie:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Uruchom następujące polecenie w [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachowaj fragmenty adresów URL

Gdy użytkownicy logują się do aplikacji, zazwyczaj chcą być przekierowani do tej samej sekcji na tej samej stronie, na przykład `/wiki/Main_Page#SectionZ` . Jednak ze względu na to, że [fragmenty adresów URL](https://wikipedia.org/wiki/Fragment_identifier) (na przykład `#SectionZ` ) nigdy nie są wysyłane do serwera, nie są domyślnie zachowywane po zakończeniu logowania OAuth i przekierowania z powrotem do aplikacji. Następnie użytkownicy uzyskują nieoptymalny komfort, gdy chcą ponownie przejść do żądanego zakotwiczenia. To ograniczenie dotyczy wszystkich rozwiązań uwierzytelniania po stronie serwera.

W ramach uwierzytelniania App Service można zachować fragmenty adresów URL w ramach logowania za pomocą protokołu OAuth. W tym celu należy ustawić ustawienie aplikacji o nazwie `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` do `true` . Można to zrobić w [Azure Portal](https://portal.azure.com)lub po prostu uruchomić następujące polecenie w [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Uzyskiwanie dostępu do oświadczeń użytkowników

App Service przekazuje oświadczenia użytkownika do aplikacji przy użyciu specjalnych nagłówków. Żądania zewnętrzne nie mogą ustawiać tych nagłówków, dlatego są obecne tylko wtedy, gdy są ustawione przez App Service. Przykładowe nagłówki obejmują:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kod, który jest pisany w dowolnym języku lub platformie, może uzyskać informacje potrzebne z tych nagłówków. W przypadku aplikacji ASP.NET 4,6, **ClaimsPrincipal** jest automatycznie ustawiany z odpowiednimi wartościami. ASP.NET Core jednak nie oferuje oprogramowania pośredniczącego uwierzytelniania, które integruje się z App Service oświadczenia użytkownika. Aby obejść ten temat, zobacz [MaximeRouiller. Azure. appService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Jeśli [Magazyn tokenów](overview-authentication-authorization.md#token-store) jest włączony dla aplikacji, możesz również uzyskać dodatkowe informacje na temat uwierzytelnionego użytkownika, wywołując polecenie `/.auth/me` . Zestawy SDK serwera Mobile Apps zapewniają metody pomocnika do pracy z tymi danymi. Aby uzyskać więcej informacji, zobacz [jak używać zestawu SDK platformy azure Mobile Apps Node.js](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)i [współdziałać z zestawem SDK serwera zaplecza platformy .NET dla platformy Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Pobieranie tokenów w kodzie aplikacji

W kodzie serwera tokeny specyficzne dla dostawcy są wstawiane do nagłówka żądania, dzięki czemu można łatwo uzyskać do nich dostęp. W poniższej tabeli przedstawiono możliwe nazwy nagłówków tokenów:

| Dostawca | Nazwy nagłówków |
|-|-|
| Usługa Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Konto Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Na podstawie kodu klienta (takiego jak aplikacja mobilna lub JavaScript w przeglądarce) Wyślij `GET` żądanie HTTP do `/.auth/me` ([Magazyn tokenów](overview-authentication-authorization.md#token-store) musi być włączony). Zwrócony kod JSON ma tokeny specyficzne dla dostawcy.

> [!NOTE]
> Tokeny dostępu są przeznaczone do uzyskiwania dostępu do zasobów dostawcy, dlatego są dostępne tylko w przypadku skonfigurowania dostawcy przy użyciu klucza tajnego klienta. Aby dowiedzieć się, jak uzyskać tokeny odświeżania, zobacz Odświeżanie tokenów dostępu.

## <a name="refresh-identity-provider-tokens"></a>Odśwież tokeny dostawcy tożsamości

Gdy token dostępu dostawcy (nie [token sesji](#extend-session-token-expiration-grace-period)) wygaśnie, należy ponownie uwierzytelnić użytkownika przed ponownym użyciem tego tokenu. Możesz uniknąć wygaśnięcia tokenu, wykonując `GET` wywołanie do `/.auth/refresh` punktu końcowego aplikacji. Gdy jest wywoływana, App Service automatycznie odświeża tokeny dostępu w [magazynie tokenów](overview-authentication-authorization.md#token-store) dla uwierzytelnionego użytkownika. Kolejne żądania dotyczące tokenów przez kod aplikacji otrzymują odświeżone tokeny. Jednak aby odświeżanie tokenów działało, magazyn tokenów musi zawierać [tokeny odświeżania](https://auth0.com/learn/refresh-tokens/) dla dostawcy. Sposób uzyskania tokenów odświeżania jest udokumentowany przez każdego dostawcę, ale Poniższa lista zawiera krótkie podsumowanie:

- **Google**: Dołącz `access_type=offline` parametr ciągu zapytania do `/.auth/login/google` wywołania interfejsu API. Jeśli używasz zestawu SDK Mobile Apps, możesz dodać parametr do jednego z `LogicAsync` przeciążeń (zobacz [tokeny usługi Google Refresh](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nie udostępnia tokenów odświeżania. Tokeny długotrwałe wygasają w ciągu 60 dni (zobacz temat [wygaśnięcie i rozszerzanie tokenów dostępu w serwisie Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: tokeny dostępu nie wygasną (zobacz [często zadawane pytania dotyczące usługi Twitter OAuth](https://developer.twitter.com/en/docs/authentication/faq)).
- **Konto Microsoft**: podczas [konfigurowania ustawień uwierzytelniania konta Microsoft](configure-authentication-provider-microsoft.md)wybierz `wl.offline_access` zakres.
- **Azure Active Directory**: w [https://resources.azure.com](https://resources.azure.com) , wykonaj następujące czynności:
    1. W górnej części strony wybierz pozycję **Odczyt/zapis**.
    2. W lewej przeglądarce przejdź do **subskrypcji** > * *_\<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **dostawcy**  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Kliknij pozycję **Edytuj**.
    4. Zmodyfikuj następującą właściwość. Zamień na _\<app\_id>_ Identyfikator aplikacji Azure Active Directory usługi, do której chcesz uzyskać dostęp.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kliknij przycisk **Put**. 

Po skonfigurowaniu dostawcy można [znaleźć token odświeżania i czas wygaśnięcia tokenu dostępu](#retrieve-tokens-in-app-code) w magazynie tokenów. 

Aby odświeżyć token dostępu w dowolnym momencie, po prostu wywołaj `/.auth/refresh` w dowolnym języku. Poniższy fragment kodu używa platformy jQuery do odświeżania tokenów dostępu z klienta JavaScript.

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

Jeśli użytkownik odwołuje uprawnienia udzielone aplikacji, wywołanie programu `/.auth/me` może zakończyć się niepowodzeniem z `403 Forbidden` odpowiedzią. Aby zdiagnozować błędy, Sprawdź dzienniki aplikacji, aby uzyskać szczegółowe informacje.

## <a name="extend-session-token-expiration-grace-period"></a>Okres karencji wygaśnięcia tokenu sesji

Sesja uwierzytelniona wygasa po 8 godzinach. Po wygaśnięciu sesji uwierzytelnionej domyślnie obowiązuje okres prolongaty 72 godzin. W ramach tego okresu prolongaty można odświeżyć token sesji z App Service bez ponownego uwierzytelniania użytkownika. Możesz tylko wywołać `/.auth/refresh` , gdy token sesji stanie się nieprawidłowy, i nie musisz samodzielnie śledzić wygaśnięcia tokenu. Po upływie 72-godzinnego okresu prolongaty użytkownik musi zalogować się ponownie, aby uzyskać prawidłowy token sesji.

Jeśli przez 72 godzin nie jest wystarczająco dużo czasu, możesz to zrobić. Przedłużenie czasu wygaśnięcia przez długi czas może mieć znaczący wpływ na zabezpieczenia (na przykład w przypadku przecieku lub kradzieży tokenu uwierzytelniania). W związku z tym należy pozostawić ją w domyślnych 72 godzinach lub ustawić okres przedłużenia na najmniejszą wartość.

Aby zwiększyć domyślne okno wygaśnięcia, uruchom następujące polecenie w [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Okres prolongaty dotyczy tylko sesji uwierzytelnionej App Service, a nie tokenów od dostawców tożsamości. Brak okresu prolongaty dla wygasłych tokenów dostawcy. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Ogranicz domenę kont logowania

Zarówno konto Microsoft, jak i Azure Active Directory umożliwiają logowanie się z wielu domen. Na przykład konto Microsoft umożliwia korzystanie z kont _Outlook.com_, _Live.com_ i _hotmail.com_ . Usługa Azure AD umożliwia dowolna liczba domen niestandardowych dla kont logowania. Można jednak przyspieszyć użytkowników bezpośrednio do własnej, oznakowanej strony logowania usługi Azure AD (np `contoso.com` .). Aby zasugerować nazwę domeny kont logowania, wykonaj następujące kroki.

W programie [https://resources.azure.com](https://resources.azure.com) Przejdź do **subskrypcji** > * *_\<subscription\_name_** > **resourceGroups** > * *_* \<resource\_group\_name> _* > **dostawcy**  >  **Microsoft. Web**  >  **sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 

Kliknij przycisk **Edytuj**, zmodyfikuj następującą właściwość, a następnie kliknij przycisk **Put**. Pamiętaj, aby zamienić na _\<domain\_name>_ wybraną domenę.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

To ustawienie dołącza `domain_hint` parametr ciągu zapytania do adresu URL przekierowania logowania. 

> [!IMPORTANT]
> Możliwe jest, aby klient usunął `domain_hint` parametr po odebraniu adresu URL przekierowania, a następnie zalogować się w innej domenie. Dlatego chociaż ta funkcja jest wygodna, nie jest funkcją zabezpieczeń.
>

## <a name="authorize-or-deny-users"></a>Autoryzuj lub Odmów użytkownikom

Chociaż App Service zajmuje się najprostszym przypadkiem autoryzacji (tj. odrzuca nieuwierzytelnione żądania), aplikacja może wymagać bardziej szczegółowych zachowań autoryzacji, takich jak ograniczanie dostępu tylko do określonej grupy użytkowników. W niektórych przypadkach należy napisać niestandardowy kod aplikacji, aby zezwolić na dostęp do zalogowanego użytkownika lub go zablokować. W innych przypadkach App Service lub dostawca tożsamości może pomóc bez konieczności wprowadzania zmian w kodzie.

- [Poziom serwera](#server-level-windows-apps-only)
- [Poziom dostawcy tożsamości](#identity-provider-level)
- [Poziom aplikacji](#application-level)

### <a name="server-level-windows-apps-only"></a>Poziom serwera (tylko aplikacje systemu Windows)

W przypadku dowolnej aplikacji systemu Windows można zdefiniować zachowanie autoryzacji serwera sieci Web usług IIS, edytując plik *Web.config* . Aplikacje systemu Linux nie używają usług IIS i nie można ich skonfigurować za pomocą *Web.config*.

1. Przejdź do strony `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. W Eksploratorze przeglądarki plików App Service przejdź do *lokalizacji site/wwwroot*. Jeśli *Web.config* nie istnieje, utwórz ją, wybierając pozycję **+**  >  **nowy plik**. 

1. Wybierz ołówek do *Web.config* , aby go edytować. Dodaj następujący kod konfiguracji i kliknij przycisk **Zapisz**. Jeśli *Web.config* już istnieje, po prostu Dodaj `<authorization>` element ze wszystkimi elementami. Dodaj konta, które mają być dozwolone w `<allow>` elemencie.

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

Dostawca tożsamości może zapewnić pewną autoryzację klucza. Przykład:

- [Azure App Service](configure-authentication-provider-aad.md)można [zarządzać dostępem na poziomie przedsiębiorstwa](../active-directory/manage-apps/what-is-access-management.md) bezpośrednio w usłudze Azure AD. Aby uzyskać instrukcje, zobacz [Jak usunąć dostęp użytkownika do aplikacji](../active-directory/manage-apps/methods-for-removing-user-access.md).
- W przypadku usługi [Google](configure-authentication-provider-google.md)interfejsy API, które należą do [organizacji](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) , można skonfigurować tak, aby zezwalały na dostęp tylko użytkownikom w organizacji (zobacz sekcję [Konfigurowanie pomocy technicznej **OAuth 2,0**](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Poziom aplikacji

Jeśli jeden z pozostałych poziomów nie zapewnia autoryzacji lub dostawca tożsamości nie jest obsługiwany, należy napisać kod niestandardowy w celu autoryzowania użytkowników na podstawie [oświadczeń użytkowników](#access-user-claims).

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurowanie przy użyciu pliku (wersja zapoznawcza)

Ustawienia uwierzytelniania można opcjonalnie skonfigurować za pośrednictwem pliku dostarczonego przez wdrożenie. Może to być wymagane przez niektóre możliwości w wersji zapoznawczej App Service uwierzytelniania/autoryzacji.

> [!IMPORTANT]
> Należy pamiętać, że ładunek aplikacji i w związku z tym ten plik może przechodzić między środowiskami, tak jak w przypadku [miejsc](./deploy-staging-slots.md). Prawdopodobnie chcesz, aby inna Rejestracja aplikacji została przypięta do każdego miejsca, a w takich przypadkach należy nadal używać standardowej metody konfiguracji zamiast korzystać z pliku konfiguracji.

### <a name="enabling-file-based-configuration"></a>Włączanie konfiguracji opartej na plikach

> [!CAUTION]
> W trakcie okresu zapoznawczego włączenie konfiguracji opartej na plikach spowoduje wyłączenie zarządzania funkcją uwierzytelniania App Service/autoryzacją dla aplikacji za pośrednictwem niektórych klientów, takich jak Azure Portal, interfejs wiersza polecenia platformy Azure i Azure PowerShell.

1. Utwórz nowy plik JSON dla konfiguracji w katalogu głównym projektu (wdrożony do D:\home\site\wwwroot w aplikacji sieci Web/funkcji). Wypełnij żądaną konfigurację zgodnie z dokumentacją [konfiguracyjną opartą na plikach](#configuration-file-reference). W przypadku modyfikowania istniejącej konfiguracji Azure Resource Manager upewnij się, że właściwości przechwycone w kolekcji zostały przetłumaczone `authsettings` na plik konfiguracji.

2. Zmodyfikuj istniejącą konfigurację, która jest przechwytywana w [Azure Resource Manager](../azure-resource-manager/management/overview.md) interfejsów API w ramach programu `Microsoft.Web/sites/<siteName>/config/authsettings` . Aby to zmodyfikować, można użyć [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) lub narzędzia, takiego jak [Azure Resource Explorer](https://resources.azure.com/). W kolekcji authsettings należy ustawić trzy właściwości (i może usunąć inne):

    1.  Ustaw `enabled` na wartość "true"
    2.  Ustaw `isAuthFromFile` na wartość "true"
    3.  Ustaw `authFilePath` na nazwę pliku (na przykład "auth.json")

> [!NOTE]
> Format `authFilePath` różnic między platformami. W systemie Windows obsługiwane są zarówno ścieżki względne, jak i bezwzględne. Metoda relatywna jest zalecana. W przypadku systemu Linux obecnie obsługiwane są tylko ścieżki bezwzględne, więc wartość ustawienia powinna być równa "/Home/site/wwwroot/auth.json" lub podobny.

Po dokonaniu tej aktualizacji konfiguracji zawartość pliku zostanie użyta do zdefiniowania zachowania App Service uwierzytelniania/autoryzacji dla tej lokacji. Jeśli kiedykolwiek chcesz wrócić do konfiguracji Azure Resource Manager, możesz to zrobić, ustawiając `isAuthFromFile` z powrotem wartość "false".

### <a name="configuration-file-reference"></a>Dokumentacja pliku konfiguracji

Wszelkie wpisy tajne, do których odwołuje się plik konfiguracji, muszą być przechowywane jako [Ustawienia aplikacji](./configure-common.md#configure-app-settings). Można nazwać dowolne ustawienia. Upewnij się, że odwołania z pliku konfiguracji używają tych samych kluczy.

W pliku są dostępne następujące opcje konfiguracji:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "requireAuthentication": <true|false>,
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
        "allowedExternalRedirectUri": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityProviderDerived",
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
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "secretSettingName": "<name of app setting containing client secret>"
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
                    "scope": [
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

Po włączeniu uwierzytelniania/autoryzacji oprogramowanie pośredniczące platformy jest wstrzykiwane do potoku żądania HTTP zgodnie z opisem w temacie [Omówienie funkcji](overview-authentication-authorization.md#how-it-works). Ta platforma oprogramowania pośredniczącego jest okresowo aktualizowana o nowe funkcje i ulepszenia w ramach rutynowych aktualizacji platformy. Domyślnie aplikacja sieci Web lub funkcja będzie uruchamiana w najnowszej wersji tego oprogramowania. Te aktualizacje automatyczne są zawsze zgodne z poprzednimi wersjami. Jednak w rzadkich przypadkach, gdy ta aktualizacja automatyczna wprowadza problem w czasie wykonywania dla aplikacji sieci Web lub funkcji, można tymczasowo przywrócić poprzednią wersję oprogramowania pośredniczącego. W tym artykule opisano sposób tymczasowego przypinania aplikacji do określonej wersji oprogramowania pośredniczącego uwierzytelniania.

### <a name="automatic-and-manual-version-updates"></a>Aktualizacje automatyczne i ręczne 

Możesz przypiąć aplikację do określonej wersji oprogramowania pośredniczącego platformy przez ustawienie `runtimeVersion` Ustawienia dla aplikacji. Aplikacja zawsze jest uruchamiana w najnowszej wersji, o ile nie zostanie wybrana jawne Przypinanie jej z powrotem do określonej wersji. W danym momencie będzie obsługiwanych kilka wersji. Jeśli przypinasz do nieprawidłowej wersji, która nie jest już obsługiwana, aplikacja będzie używać najnowszej wersji. Aby zawsze uruchamiać najnowszą wersję, ustaw wartość `runtimeVersion` ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Wyświetl i zaktualizuj bieżącą wersję środowiska uruchomieniowego

Możesz zmienić wersję środowiska uruchomieniowego używaną przez aplikację. Nowa wersja środowiska uruchomieniowego powinna obowiązywać po ponownym uruchomieniu aplikacji. 

#### <a name="view-the-current-runtime-version"></a>Wyświetl bieżącą wersję środowiska uruchomieniowego

Bieżącą wersję oprogramowania pośredniczącego uwierzytelniania platformy można wyświetlić przy użyciu interfejsu wiersza polecenia platformy Azure lub za pośrednictwem jednej z built0 wersji punktów końcowych HTTP w aplikacji.

##### <a name="from-the-azure-cli"></a>W interfejsie wiersza polecenia platformy Azure

Korzystając z interfejsu wiersza polecenia platformy Azure, Wyświetl bieżącą wersję oprogramowania pośredniczącego przy użyciu [AZ webapp auth show](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-show) .

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

W tym kodzie Zamień na `<my_app_name>` nazwę aplikacji. Zastąp również `<my_resource_group>` nazwą grupy zasobów aplikacji.

Zobaczysz `runtimeVersion` pole w danych wyjściowych interfejsu wiersza polecenia. Będzie podobne do następujących przykładowych danych wyjściowych, które zostały obcięte do przejrzystości: 
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

Możesz również kliknąć pozycję punkt końcowy/.auth/Version w aplikacji, aby wyświetlić bieżącą wersję oprogramowania pośredniczącego, na którym działa aplikacja. Będzie wyglądać podobnie do następujących przykładowych danych wyjściowych:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Aktualizuj bieżącą wersję środowiska uruchomieniowego

Korzystając z interfejsu wiersza polecenia platformy Azure, można zaktualizować `runtimeVersion` ustawienie w aplikacji za pomocą [AZ webapp auth Update](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-update) .

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Zamień `<my_app_name>` na nazwę aplikacji. Zastąp również `<my_resource_group>` nazwą grupy zasobów aplikacji. Zastąp również `<version>` prawidłową wersją środowiska uruchomieniowego 1. x lub `~1` najnowszą wersję. Informacje o wersji można znaleźć w różnych wersjach środowiska uruchomieniowego [tutaj] ( https://github.com/Azure/app-service-announcements) Aby określić wersję do przypięcia do programu.

Możesz uruchomić to polecenie z [Azure Cloud Shell](../cloud-shell/overview.md) , wybierając pozycję **Wypróbuj** w poprzednim przykładzie kodu. Możesz również użyć [interfejsu wiersza polecenia platformy Azure lokalnie](/cli/azure/install-azure-cli) , aby wykonać to polecenie po wykonaniu polecenia [AZ login](/cli/azure/reference-index#az-login) , aby się zalogować.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: kompleksowe uwierzytelnianie i autoryzacja użytkowników](tutorial-auth-aad.md)
