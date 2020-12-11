---
title: Dostosowywanie interfejsu użytkownika
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować interfejs użytkownika dla aplikacji korzystających z Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869cf5a47831844b04e0461a95fb7d16aa4d1569
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111494"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Oznakowanie i dostosowanie interfejsu użytkownika, który jest wyświetlany Azure Active Directory B2C (Azure AD B2C) dla klientów, zapewnia bezproblemowe środowisko użytkownika w aplikacji. Do tych środowisk należą: rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. W tym artykule przedstawiono metody dostosowywania interfejsu użytkownika. 

> [!TIP]
> Jeśli chcesz zmodyfikować tylko logo transparentu, obraz tła i kolor tła stron przepływu użytkownika, możesz wypróbować funkcję [znakowania firmowego](company-branding.md) .


## <a name="custom-html-and-css-overview"></a>Niestandardowy kod HTML i CSS — Omówienie


Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie użytkownika lub w zasadach niestandardowych. Każda Strona w środowisku użytkownika ładuje swoją zawartość z adresu URL określonego dla tej strony. Po załadowaniu zawartości z adresu URL zostanie on scalony z fragmentem kodu HTML wstawionym przez Azure AD B2C, a następnie zostanie wyświetlona strona klienta.

![Margines zawartości strony niestandardowej](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Zawartość niestandardowej strony HTML

Utwórz stronę HTML z własną marką, aby zapewnić swoją niestandardową zawartość strony. Ta strona może być stroną statyczną `*.html` lub stroną dynamiczną, taką jak .NET, Node.js lub php.

Zawartość strony niestandardowej może zawierać dowolne elementy HTML, w tym CSS i JavaScript, ale nie może zawierać niezabezpieczonych elementów, takich jak iframes. Jedynym wymaganym elementem jest element DIV z `id` ustawionym na `api` , taki jak ten, na `<div id="api"></div>` stronie HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Dostosowywanie domyślnych stron Azure AD B2C

Zamiast tworzyć zawartość strony niestandardowej od podstaw, można dostosować domyślną zawartość strony usługi Azure AD B2C's.

W poniższej tabeli wymieniono domyślną zawartość strony dostarczoną przez Azure AD B2C. Pobierz pliki i użyj ich jako punktu wyjścia do tworzenia własnych stron niestandardowych.

| Strona domyślna | Opis | Identyfikator definicji zawartości<br/>(tylko zasady niestandardowe) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub błędu. | *Interfejs API. błąd* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Strona z własnym potwierdzeniem**. Użyj tego pliku jako niestandardowej zawartości strony dla strony rejestracji konta społecznościowego, strony rejestracji konta lokalnego, strony logowania do konta lokalnego, resetowania hasła i nie tylko. Formularz może zawierać różne kontrolki danych wejściowych, na przykład: pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z wieloma zaznaczeniami. | *API. localaccountsignin*, *API. localaccountsignup*, *API. localaccountpasswordreset*, *API. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą weryfikować numery telefonów (za pomocą tekstu lub głosu) podczas rejestracji lub logowania. | *API. PhoneFactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu**. Ta strona zawiera formularz, do którego użytkownicy mogą uzyskać dostęp w celu zaktualizowania swojego profilu. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. | *API. selfasserted. profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona Strona rejestracji lub logowania**. Ta strona obsługuje proces tworzenia konta i logowania użytkownika. Użytkownicy mogą korzystać z dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google + lub konta lokalnego. | *API. signuporsignin* |

## <a name="hosting-the-page-content"></a>Hosting zawartości strony

Korzystając z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, hostować zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS, który obsługuje mechanizm CORS. Na przykład [usługa Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), [Azure App Services](../app-service/index.yml), serwery sieci Web, sieci CDN, AWS S3 lub systemy udostępniania plików.

## <a name="guidelines-for-using-custom-page-content"></a>Wskazówki dotyczące korzystania z niestandardowej zawartości strony

- Użyj bezwzględnego adresu URL, jeśli w pliku HTML są uwzględniane zasoby zewnętrzne, takie jak pliki multimedialne, CSS i JavaScript.
- Korzystając z [wersji układu strony](page-layout.md) 1.2.0 i nowszych, można dodać `data-preload="true"` ATRYBUT do tagów HTML w celu kontrolowania kolejności ładowania dla CSS i JavaScript. W programie `data-preload=true` Strona jest zbudowana przed wyświetleniem użytkownika. Ten atrybut pomaga zapobiec "migotaniu" na stronie przez wstępne załadowanie pliku CSS bez wyświetlania kodu HTML, który nie ma stylu. Poniższy fragment kodu HTML pokazuje użycie `data-preload` znacznika.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Zalecamy rozpoczęcie od domyślnej zawartości strony i jej skompilowanie na jej podstawie.
- Możesz [dołączyć kod JavaScript](javascript-and-page-layout.md) do zawartości niestandardowej.
- Obsługiwane wersje przeglądarek:
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa programu Internet Explorer 9 i 8
  - Google Chrome 42,0 i nowsze
  - Mozilla Firefox 38,0 i nowsze
  - Safari dla systemów iOS i macOS, wersja 12 i nowsze
- Ze względu na ograniczenia zabezpieczeń Azure AD B2C nie `frame` obsługuje `iframe` elementów, ani `form` HTML.

## <a name="localize-content"></a>Lokalizowanie zawartości

Zawartość HTML można zlokalizować, włączając [Dostosowywanie języka](language-customization.md) w dzierżawie Azure AD B2C. Włączenie tej funkcji umożliwia Azure AD B2C przekazanie parametru OpenID Connect Connect `ui_locales` do punktu końcowego. Twój serwer zawartości może użyć tego parametru, aby udostępnić strony HTML specyficzne dla języka.

Zawartość można ściągnąć z różnych miejsc w oparciu o używane ustawienia regionalne. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości wieloznacznej, nastąpi wywołanie odpowiedniej metody `{Culture:RFC5646}` .

Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Możesz załadować stronę w języku francuskim, pobierając zawartość z:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Przewodnik po zawartości strony niestandardowej

Poniżej przedstawiono omówienie procesu:

1. Przygotuj lokalizację do hostowania niestandardowej zawartości strony (dostępny publicznie punkt końcowy HTTPS z obsługą mechanizmu CORS).
1. Pobierz i Dostosuj domyślny plik zawartości strony, na przykład `unified.html` .
1. Opublikuj zawartość strony niestandardowej w publicznie dostępnym punkcie końcowym HTTPS.
1. Ustaw współużytkowanie zasobów między źródłami (CORS) dla aplikacji sieci Web.
1. Wskaż zasady dla niestandardowego identyfikatora URI zawartości zasad.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


### <a name="1-create-your-html-content"></a>1. Utwórz zawartość HTML

Utwórz niestandardową zawartość strony z nazwą marki produktu w tytule.

1. Skopiuj poniższy fragment kodu HTML. Jest to dobrze sformułowany plik HTML5 z pustym elementem o nazwie *\<div id="api"\>\</div\>* znajdującym się w *\<body\>* tagach. Ten element wskazuje, gdzie Azure AD B2C zawartość ma zostać wstawiona.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Wklej skopiowany fragment w edytorze tekstu
1. Użyj CSS do stylu elementów interfejsu użytkownika, które Azure AD B2C wstawia do strony. Poniższy przykład pokazuje prosty plik CSS, który zawiera również ustawienia dla elementów z wprowadzonym kodem HTML:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Zapisz plik jako *customize-ui.html*.

> [!NOTE]
> Elementy formularza HTML zostaną usunięte ze względu na ograniczenia zabezpieczeń, jeśli używasz login.microsoftonline.com. Jeśli chcesz użyć elementów formularza HTML w niestandardowej zawartości HTML, [użyj b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Tworzenie konta usługi Azure Blob Storage

W tym artykule korzystamy z usługi Azure Blob Storage do obsługi naszej zawartości. Możesz wybrać hostowanie zawartości na serwerze sieci Web, ale należy [włączyć funkcję CORS na serwerze sieci Web](https://enable-cors.org/server.html).

Aby hostować zawartość HTML w usłudze BLOB Storage, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu **centrum** wybierz kolejno pozycje **Nowy**  >  **Magazyn**  >  **konto magazynu**.
1. Wybierz **subskrypcję** dla konta magazynu.
1. Utwórz **grupę zasobów** lub wybierz istniejącą.
1. Wprowadź unikatową **nazwę** konta magazynu.
1. Wybierz **lokalizację geograficzną** dla swojego konta magazynu.
1. **Model wdrażania** może pozostawać **Menedżer zasobów**.
1. **Wydajność** może być **standardowa**.
1. Zmień **rodzaj konta** na **Magazyn obiektów BLOB**.
1. **Replikacja** może pozostawać **RA-GRS**.
1. **Warstwa dostępu** może pozostać **gorąca**.
1. Wybierz pozycję **Przegląd + Utwórz** , aby utworzyć konto magazynu.
    Po zakończeniu wdrażania zostanie automatycznie otwarta strona **konto magazynu** .

#### <a name="21-create-a-container"></a>2,1 Tworzenie kontenera

Aby utworzyć kontener publiczny w usłudze BLOB Storage, wykonaj następujące czynności:

1. W obszarze **BLOB Service** w menu po lewej stronie wybierz pozycję **obiekty blob**.
1. Wybierz pozycję **+ kontener**.
1. W obszarze **Nazwa wprowadź nazwę** *root*. Nazwa może być nazwą wybraną, na przykład *contoso*, ale w tym przykładzie używamy *elementu głównego* w celu uproszczenia.
1. W obszarze **poziom dostępu publicznego** wybierz pozycję **obiekt BLOB**, a następnie kliknij **przycisk OK**.
1. Wybierz pozycję **root** , aby otworzyć nowy kontener.

#### <a name="22-upload-your-custom-page-content-files"></a>2,2 przekazywanie niestandardowych plików zawartości strony

1. Wybierz pozycję **Przekaż**.
1. Wybierz ikonę folderu obok pozycji **Wybierz plik**.
1. Przejdź do i wybierz **customize-ui.html**, który został utworzony wcześniej w sekcji Dostosowywanie interfejsu użytkownika strony.
1. Jeśli chcesz przekazać do podfolderu, rozwiń pozycję **Zaawansowane** i wprowadź nazwę folderu w polu **Przekaż do folderu**.
1. Wybierz pozycję **Przekaż**.
1. Wybierz przekazany obiekt BLOB **customize-ui.html** .
1. Z prawej strony pola tekstowego **adres URL** wybierz ikonę **Kopiuj do schowka** , aby skopiować adres URL do Schowka.
1. W przeglądarce internetowej przejdź do adresu URL skopiowanego w celu zweryfikowania dostępności przekazanego obiektu BLOB. Jeśli jest niedostępny, na przykład jeśli wystąpi `ResourceNotFound` błąd, upewnij się, że typ dostępu do kontenera jest ustawiony na **obiekt BLOB**.

### <a name="3-configure-cors"></a>3. Konfigurowanie mechanizmu CORS

Skonfiguruj magazyn obiektów BLOB dla udostępniania zasobów między źródłami, wykonując następujące czynności:

1. Z menu wybierz pozycję **CORS**.
1. Dla **dozwolonych źródeł** wprowadź `https://your-tenant-name.b2clogin.com` . Zamień `your-tenant-name` na nazwę dzierżawy Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com`. W przypadku wprowadzania nazwy dzierżawy używaj wszystkich małych liter.
1. W przypadku **dozwolonych metod** zaznacz opcję oba `GET` i `OPTIONS` .
1. Dla **dozwolonych nagłówków** Wprowadź gwiazdkę (*).
1. W przypadku **widocznych nagłówków** Wprowadź gwiazdkę (*).
1. W obszarze **Maksymalny wiek** wprowadź 200.
1. Wybierz pozycję **Zapisz**.

#### <a name="31-test-cors"></a>3,1 testów CORS

Sprawdź, czy wszystko jest gotowe, wykonując następujące czynności:

1. Powtórz krok konfigurowania mechanizmu CORS. Dla **dozwolonych źródeł** wpisz `https://www.test-cors.org`
1. Przejdź do [www.test-CORS.org](https://www.test-cors.org/) 
1. W polu **zdalny adres URL** Wklej adres URL pliku HTML. Na przykład `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Wybierz pozycję **Wyślij żądanie**.
    Powinien być wynikiem `XHR status: 200` . 
    Jeśli wystąpi błąd, upewnij się, że ustawienia mechanizmu CORS są poprawne. Może być również konieczne wyczyszczenie pamięci podręcznej przeglądarki lub otwarcie sesji przeglądania w trybie prywatnym przez naciśnięcie klawiszy Ctrl + Shift + P.


::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Zaktualizuj przepływ użytkownika

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**, a następnie wybierz pozycję *B2C_1_signupsignin1* przepływ użytkownika.
1. Wybierz pozycję **układy stron**, a następnie w obszarze **ujednolicone rejestrowanie lub logowanie** kliknij pozycję **tak** , aby **użyć niestandardowej zawartości strony**.
1. W polu **Identyfikator URI strony niestandardowej** wprowadź identyfikator URI zarejestrowanego wcześniej pliku *custom-ui.html* .
1. W górnej części strony wybierz pozycję **Zapisz**.

### <a name="5-test-the-user-flow"></a>5. Testowanie przepływu użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika** i wybierz *B2C_1_signupsignin1* przepływ użytkownika.
1. W górnej części strony kliknij pozycję **Uruchom przepływ użytkownika**.
1. Kliknij przycisk **Uruchom przepływ użytkownika** .

Powinna zostać wyświetlona strona podobna do poniższego przykładu z elementami wyśrodkowanymi na podstawie utworzonego pliku CSS:

![Przeglądarka sieci Web przedstawiająca rejestrowanie lub logowanie na stronie z niestandardowymi elementami interfejsu użytkownika](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Zmodyfikuj plik rozszerzeń.

Aby skonfigurować dostosowanie interfejsu użytkownika, skopiuj **ContentDefinition** i jego elementy podrzędne z pliku podstawowego do pliku rozszerzeń.

1. Otwórz podstawowy plik zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Ten plik podstawowy jest jednym z plików zasad zawartych w pakiecie startowym zasad niestandardowych, który powinien zostać uzyskany w wymaganiu wstępnym, [Rozpocznij od zasad niestandardowych](./custom-policy-get-started.md).
1. Wyszukaj i Skopiuj całą zawartość elementu **ContentDefinitions** .
1. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions.xml*. Wyszukaj element **BuildingBlocks** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **ContentDefinitions** , który został skopiowany jako element podrzędny elementu **BuildingBlocks** .
1. Wyszukaj element **ContentDefinition** , który zawiera `Id="api.signuporsignin"` kod XML, który został skopiowany.
1. Zmień wartość **LoadUri** na adres URL pliku HTML, który został przekazany do magazynu. Na przykład `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Zasady niestandardowe powinny wyglądać podobnie do następującego fragmentu kodu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Zapisz plik rozszerzeń.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. przekazanie i przetestowanie zaktualizowanych zasad niestandardowych

#### <a name="51-upload-the-custom-policy"></a>5,1 przekazywanie zasad niestandardowych

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wyszukaj i wybierz **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. Przekaż wcześniej zmieniony plik rozszerzeń.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testowanie zasad niestandardowych za pomocą polecenia **Uruchom teraz**

1. Wybierz przekazane zasady, a następnie wybierz pozycję **Uruchom teraz**.
1. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail.

## <a name="configure-dynamic-custom-page-content-uri"></a>Skonfiguruj dynamiczny Identyfikator URI zawartości strony niestandardowej

Za pomocą Azure AD B2C zasad niestandardowych można wysłać parametr w ścieżce URL lub ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Parametr może być dowolnymi [mechanizmami rozwiązywania konfliktów](claim-resolver-overview.md), takimi jak identyfikator aplikacji, identyfikator języka lub niestandardowy parametr ciągu zapytania, taki jak `campaignId` .

### <a name="sending-query-string-parameters"></a>Wysyłanie parametrów ciągu zapytania

Aby wysłać parametry ciągu zapytania, w [zasadach jednostki uzależnionej](relyingparty.md)Dodaj `ContentDefinitionParameters` element, jak pokazano poniżej.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

W definicji zawartości Zmień wartość `LoadUri` na `https://<app_name>.azurewebsites.net/home/unified` . Zasady niestandardowe `ContentDefinition` powinny wyglądać podobnie do następującego fragmentu kodu:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Gdy Azure AD B2C ładuje stronę, wywołuje punkt końcowy serwera sieci Web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Identyfikator URI zawartości strony dynamicznej

Zawartość można ściągnąć z różnych miejsc na podstawie używanych parametrów. W punkcie końcowym z obsługą mechanizmu CORS Skonfiguruj strukturę folderów, aby hostować zawartość. Można na przykład zorganizować zawartość w następującej strukturze. *Folder główny/folder na język/pliki HTML*. Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C wysyła dwuliterowy kod ISO dla języka w `fr` języku francuskim:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Przykładowe szablony

Przykładowe szablony do dostosowywania interfejsu użytkownika można znaleźć tutaj:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Ten projekt zawiera następujące szablony:
- [Ocean niebieski](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Szare](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Aby użyć przykładu:

1. Sklonuj repozytorium na komputerze lokalnym. Wybierz folder szablonu `/ocean_blue` lub `/slate_gray` .
1. Przekaż wszystkie pliki z folderu Template i `/assets` folder do magazynu obiektów BLOB zgodnie z opisem w poprzednich sekcjach.
1. Następnie otwórz każdy `\*.html` plik w katalogu głównym `/ocean_blue` lub `/slate_gray` Zastąp wszystkie wystąpienia względnych adresów URL adresami URL plików CSS, obrazów i czcionek przekazanych w kroku 2. Na przykład:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Działanie
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Zapisz `\*.html` pliki i przekaż je do magazynu obiektów BLOB.
1. Teraz zmodyfikuj zasady, wskazując na plik HTML, jak wspomniano wcześniej.
1. Jeśli widzisz brakujące czcionki, obrazy lub CSS, Sprawdź odwołania w zasadach rozszerzeń i \* plikach. html.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak włączyć [kod JavaScript po stronie klienta](javascript-and-page-layout.md).



