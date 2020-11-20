---
title: Zabezpieczanie interfejsu API usługi Azure API Management przy użyciu Azure Active Directory B2C
description: Dowiedz się, jak używać tokenów dostępu wystawionych przez Azure Active Directory B2C, aby zabezpieczyć punkt końcowy interfejsu API usługi Azure API Management.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953495"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Zabezpieczanie interfejsu API usługi Azure API Management przy użyciu Azure AD B2C

Dowiedz się, jak ograniczyć dostęp do interfejsu API usługi Azure API Management (APIM) do klientów uwierzytelnionych za pomocą Azure Active Directory B2C (Azure AD B2C). Wykonaj kroki opisane w tym artykule, aby utworzyć i przetestować zasady ruchu przychodzącego w programie APIM, które ograniczają dostęp tylko do tych żądań, które zawierają prawidłowy token dostępu wystawiony przez Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule potrzebne są następujące zasoby:

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Przepływy użytkowników utworzone](tutorial-create-user-flows.md) w dzierżawie
* [Opublikowany interfejs API](../api-management/import-and-publish.md) na platformie Azure API Management
* [Ogłoś](https://www.getpostman.com/) do testowania bezpiecznego dostępu (opcjonalnie)

## <a name="get-azure-ad-b2c-application-id"></a>Pobierz identyfikator aplikacji Azure AD B2C

W przypadku zabezpieczania interfejsu API w usłudze Azure API Management przy użyciu Azure AD B2C potrzebne są kilka wartości dla [zasad przychodzących](../api-management/api-management-howto-policies.md) tworzonych w APIM. Najpierw Zapisz identyfikator aplikacji utworzonej wcześniej w dzierżawie Azure AD B2C. Jeśli używasz aplikacji utworzonej w ramach wymagań wstępnych, użyj identyfikatora aplikacji dla *webbapp1*.

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz kartę **posiadane aplikacje** .
1. Zapisz wartość w kolumnie **Identyfikator aplikacji (klienta)** dla *webapp1* lub innej aplikacji, która została wcześniej utworzona.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **Zarządzaj** wybierz pozycję **aplikacje (starsza wersja)**.
1. Zapisz wartość w kolumnie **Identyfikator aplikacji** dla *webapp1* lub innej aplikacji, która została wcześniej utworzona.

* * *

## <a name="get-token-issuer-endpoint"></a>Pobierz punkt końcowy wystawcy tokenu

Następnie uzyskaj dobrze znany adres URL konfiguracji dla jednego z Azure AD B2C przepływów użytkownika. Wymagany jest również identyfikator URI punktu końcowego wystawcy tokenów, który ma być obsługiwany na platformie Azure API Management.

1. Przejdź do dzierżawy Azure AD B2C w [Azure Portal](https://portal.azure.com).
1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika**.
1. Wybierz istniejące zasady, na przykład *B2C_1_signupsignin1*, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. Zapisz adres URL w hiperłączu wyświetlanym w nagłówku przebiegu **użytkownika** w górnej części strony. Ten adres URL jest dobrze znanym punktem końcowym wykrywania OpenID Connect Connect dla przepływu użytkownika i jest używany w następnej sekcji podczas konfigurowania zasad ruchu przychodzącego w usłudze Azure API Management.

    ![Dobrze znane hiperłącze URI na stronie uruchamiania teraz Azure Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Wybierz hiperlink, aby przejść do strony OpenID Connect Connect dobrze znana konfiguracja.
1. Na stronie, która zostanie otwarta w przeglądarce, Zapisz `issuer` wartość, na przykład:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Ta wartość jest używana w następnej sekcji podczas konfigurowania interfejsu API w usłudze Azure API Management.

Teraz powinny zostać zarejestrowane dwa adresy URL do użycia w następnej sekcji: adres URL punktu końcowego znanej konfiguracji OpenID Connect Connect i identyfikator URI wystawcy. Przykład:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurowanie zasad ruchu przychodzącego na platformie Azure API Management

Teraz można przystąpić do dodawania zasad ruchu przychodzącego w usłudze Azure API Management, która sprawdza poprawność wywołań interfejsu API. Dodając zasady [sprawdzania poprawności tokenu JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) , które sprawdzają odbiorców i wystawcę w tokenie dostępu, można upewnić się, że akceptowane są tylko wywołania interfejsu API z prawidłowym tokenem.

1. Przejdź do wystąpienia usługi Azure API Management w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Interfejsy API**.
1. Wybierz interfejs API, który ma być zabezpieczony za pomocą Azure AD B2C.
1. Wybierz kartę **Projekt**.
1. W obszarze **Przetwarzanie przychodzące** wybierz pozycję **\</\>** Otwórz Edytor kodu zasad.
1. Umieść następujący `<validate-jwt>` tag wewnątrz `<inbound>` zasad.

    1. Zaktualizuj `url` wartość w `<openid-config>` elemencie za pomocą dobrze znanego adresu URL konfiguracji zasad.
    1. Zaktualizuj `<audience>` element za pomocą identyfikatora aplikacji utworzonej wcześniej w dzierżawie usługi B2C (na przykład *webapp1*).
    1. Zaktualizuj `<issuer>` element za pomocą zarejestrowanego wcześniej punktu końcowego wystawcy tokenu.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Weryfikowanie bezpiecznego dostępu do interfejsu API

Aby upewnić się, że tylko uwierzytelnione obiekty wywołujące mogą uzyskać dostęp do interfejsu API, możesz zweryfikować konfigurację usługi Azure API Management, wywołując interfejs API za pomocą programu [Poster](https://www.getpostman.com/).

Aby wywołać interfejs API, wymagany jest token dostępu wystawiony przez Azure AD B2C i klucz subskrypcji APIM.

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu

Najpierw musisz mieć token wystawiony przez Azure AD B2C do użycia w `Authorization` nagłówku w programie Poster. Możesz uzyskać jeden z nich, korzystając z funkcji **Uruchom teraz** przepływu użytkownika tworzenia konta/logowania, który powinien zostać utworzony jako jedno z wymagań wstępnych.

1. Przejdź do dzierżawy Azure AD B2C w [Azure Portal](https://portal.azure.com).
1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika**.
1. Wybierz istniejący przepływ użytkownika rejestracji/logowania, na przykład *B2C_1_signupsignin1*.
1. W przypadku **aplikacji** wybierz pozycję *webapp1*.
1. Dla **adresu URL odpowiedzi** wybierz opcję `https://jwt.ms` .
1. Wybierz pozycję **Uruchom przepływ użytkownika**.

    ![Uruchom stronę przepływu użytkownika w celu zarejestrowania się w przepływie użytkownika w Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Zakończ proces logowania. Należy przekierować do `https://jwt.ms` .
1. Zapisz zakodowaną wartość tokenu wyświetlaną w przeglądarce. Ta wartość tokenu jest używana dla nagłówka autoryzacji w programie Poster.

    ![Wartość zakodowanego tokenu wyświetlana w jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Pobierz klucz subskrypcji interfejsu API

Aplikacja kliencka (w tym przypadku) wywołująca opublikowany interfejs API musi zawierać prawidłowy klucz subskrypcji API Management w swoich żądaniach HTTP do interfejsu API. Aby uzyskać klucz subskrypcji do uwzględnienia w żądaniu HTTP Twojego wpisu:

1. Przejdź do wystąpienia usługi Azure API Management w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Subskrypcje**.
1. Wybierz wielokropek dla **produktu: nieograniczone**, a następnie wybierz pozycję **Pokaż/Ukryj klucze**.
1. Zapisz **klucz podstawowy** dla produktu. Ten klucz jest używany dla `Ocp-Apim-Subscription-Key` nagłówka w ŻĄDANIU http w programie Poster.

![Strona klucza subskrypcji z kluczami Pokaż/Ukryj wybrane w Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testowanie wywołania bezpiecznego interfejsu API

Po zarejestrowaniu tokenu dostępu i klucza subskrypcji APIM można już sprawdzić, czy bezpieczny dostęp do interfejsu API został prawidłowo skonfigurowany.

1. Utwórz nowe `GET` żądanie w programie [Poster](https://www.getpostman.com/). W polu adres URL żądania Określ punkt końcowy listy głośników interfejsu API, który został opublikowany jako jedno z wymagań wstępnych. Przykład:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Następnie Dodaj następujące nagłówki:

    | Klucz | Wartość |
    | --- | ----- |
    | `Authorization` | Zapisana wcześniej wartość tokenu zakodowana, poprzedzona znakiem `Bearer ` (z uwzględnieniem odstępu po "okaziciela") |
    | `Ocp-Apim-Subscription-Key` | APIM klucz subskrypcji został zarejestrowany wcześniej |

    Adres URL i **nagłówki** żądania **pobrania** powinny wyglądać podobnie do:

    ![Interfejs użytkownika programu Poster pokazujący adres URL i nagłówki żądania pobrania](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Wybierz przycisk **Wyślij** w programie Poster, aby wykonać żądanie. Jeśli wszystko zostało poprawnie skonfigurowane, należy przedstawić odpowiedź JSON z kolekcją prelegentów (pokazane tutaj obcięte):

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testowanie wywołania niezabezpieczonego interfejsu API

Po pomyślnym wykonaniu żądania Przetestuj przypadek niepowodzenia, aby upewnić się, że wywołania interfejsu API z *nieprawidłowym* tokenem zostaną odrzucone zgodnie z oczekiwaniami. Jednym ze sposobów przeprowadzenia testu jest dodanie lub zmiana kilku znaków w wartości tokenu, a następnie wykonanie tego samego `GET` żądania co poprzednio.

1. Dodaj kilka znaków do wartości tokenu, aby symulować nieprawidłowy token. Na przykład Dodaj wartość "Nieprawidłowa" do wartości tokenu:

    ![Sekcja Heads w interfejsie użytkownika programu Poster pokazująca nieprawidłowy dodany token do tokenu](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Wybierz przycisk **Wyślij** , aby wykonać żądanie. W przypadku nieprawidłowego tokenu oczekiwany wynik to `401` nieautoryzowany kod stanu:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Jeśli zobaczysz `401` kod stanu, sprawdzono, że tylko wywołujący mający prawidłowy token dostępu wystawiony przez Azure AD B2C mogą wykonywać pomyślne żądania do interfejsu API usługi Azure API Management.

## <a name="support-multiple-applications-and-issuers"></a>Obsługa wielu aplikacji i wystawców

Niektóre aplikacje zwykle współpracują z pojedynczym interfejsem API REST. Aby umożliwić interfejsowi API akceptowanie tokenów przeznaczonych dla wielu aplikacji, Dodaj ich identyfikatory aplikacji do `<audiences>` elementu w zasadach ruchu przychodzącego APIM.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Podobnie aby obsługiwać wiele wystawców tokenów, Dodaj ich identyfikatory URI punktu końcowego do `<issuers>` elementu w zasadach APIM przychodzących.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrowanie do b2clogin.com

Jeśli masz interfejs API APIM, który sprawdza poprawność tokenów wystawionych przez starszy `login.microsoftonline.com` punkt końcowy, należy przeprowadzić migrację interfejsu API i aplikacji, które go wywołują, aby używać tokenów wystawionych przez [b2clogin.com](b2clogin.md).

Ten ogólny proces można wykonać w celu przeprowadzenia migracji etapowej:

1. Dodaj obsługę zasad ruchu przychodzącego APIM dla tokenów wystawionych przez b2clogin.com i login.microsoftonline.com.
1. Zaktualizuj aplikacje pojedynczo, aby uzyskać tokeny z punktu końcowego b2clogin.com.
1. Gdy wszystkie aplikacje będą prawidłowo uzyskać tokeny z b2clogin.com, Usuń obsługę tokenów wystawionych przez login.microsoftonline.com z interfejsu API.

W poniższym przykładzie zasady ruchu przychodzącego APIM przedstawiają sposób akceptowania tokenów wystawionych przez b2clogin.com i login.microsoftonline.com. Ponadto obsługuje żądania interfejsu API z dwóch aplikacji.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje na temat zasad usługi Azure API Management, zobacz [indeks odwołań zasad APIM](../api-management/api-management-policies.md).

Informacje na temat migrowania interfejsów API sieci Web opartych na OWIN i ich aplikacji do b2clogin.com w celu [migrowania internetowego interfejsu API opartego na usłudze Owin do b2clogin.com](multiple-token-endpoints.md).