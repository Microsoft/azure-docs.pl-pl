---
title: Dostosowywanie interfejsu użytkownika za pomocą szablonów HTML
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować interfejs użytkownika za pomocą szablonów HTML dla aplikacji, które używają Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f9f6dc1abd08c5e53f3d44a8f6ec1b3e20786ed
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717445"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika za pomocą szablonów HTML w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Znakowanie i dostosowywanie interfejsu użytkownika, który Azure Active Directory B2C (Azure AD B2C) klientom, pomaga zapewnić bezproblemowe środowisko użytkownika w aplikacji. Te doświadczenia obejmują rejestrację, logowanie, edytowanie profilu i resetowanie haseł. W tym artykule oprowadzono metody dostosowywania interfejsu użytkownika. 

> [!TIP]
> Jeśli chcesz zmodyfikować tylko baner logo, obraz tła i kolor tła stron przepływu użytkownika, możesz wypróbować funkcję znakowania [firmowego.](customize-ui.md)

## <a name="custom-html-and-css-overview"></a>Omówienie niestandardowego kodu HTML i CSS

Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu współużytku zasobów między źródłami [(CORS).](https://www.w3.org/TR/cors/) W czasie wykonywania zawartość jest ładowana z adresu URL, który został określony w przepływie użytkownika lub w zasadach niestandardowych. Każda strona w interfejsie użytkownika ładuje swoją zawartość z adresu URL, który został określony dla tej strony. Po załadowaniu zawartości z adresu URL zostanie ona scalona z fragmentem KODU HTML wstawionego przez Azure AD B2C, a następnie strona zostanie wyświetlona klientowi.

![Niestandardowy margines zawartości strony](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Niestandardowa zawartość strony HTML

Utwórz stronę HTML z własnym znakowaniem, aby obsługiwać niestandardową zawartość strony. Ta strona może być stroną statyczną lub dynamiczną, taką jak `*.html` .NET, Node.js lub PHP.

Niestandardowa zawartość strony może zawierać dowolne elementy HTML, w tym CSS i JavaScript, ale nie może zawierać niezabezpieczonych elementów, takich jak elementy iframe. Jedynym wymaganym elementem jest element div z ustawionym na wartość , takim jak ten `id` `api` na stronie `<div id="api"></div>` HTML.

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

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Dostosowywanie domyślnych stron Azure AD B2C danych

Zamiast tworzyć niestandardową zawartość strony od podstaw, można Azure AD B2C domyślną zawartość strony.

W poniższej tabeli wymieniono domyślną zawartość strony dostarczaną przez Azure AD B2C. Pobierz pliki i użyj ich jako punktu wyjścia do tworzenia własnych stron niestandardowych.

| Strona domyślna | Opis | Identyfikator definicji zawartości<br/>(tylko zasady niestandardowe) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Strona błędu**. Ta strona jest wyświetlana po napotkaniu wyjątku lub błędu. | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Strona z samodzielnie potwierdzeniami**. Użyj tego pliku jako niestandardowej zawartości strony dla strony rejestracji konta społecznościowego, strony rejestracji konta lokalnego, strony logowania do konta lokalnego, resetowania hasła i nie tylko. Formularz może zawierać różne kontrolki wejściowe, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z jednym zaznaczeniem i pola wyboru wielokrotnego. | *api.localaccountsignin*, *api.localaccountsignup,* *api.localaccountpasswordreset*, *api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Strona uwierzytelniania wieloskładnikowego**. Na tej stronie użytkownicy mogą weryfikować numery telefonów (przy użyciu tekstu lub głosu) podczas rejestracji lub logowania. | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Strona aktualizacji profilu**. Ta strona zawiera formularz, do który użytkownicy mogą uzyskać dostęp w celu zaktualizowania swojego profilu. Ta strona jest podobna do strony rejestracji konta społecznościowego, z wyjątkiem pól wprowadzania hasła. | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Ujednolicona strona rejestracji lub logowania.** Ta strona obsługuje proces rejestracji i logowania użytkownika. Użytkownicy mogą używać dostawców tożsamości przedsiębiorstwa, dostawców tożsamości społecznościowych, takich jak Facebook, Google+, lub kont lokalnych. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hostowanie zawartości strony

W przypadku korzystania z własnych plików HTML i CSS w celu dostosowania interfejsu użytkownika należy hostować zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS, który obsługuje protokół CORS. Na przykład [usługi Azure Blob Storage,](../storage/blobs/storage-blobs-introduction.md)Azure [App Services,](../app-service/index.yml)serwery internetowe, sieci CDN, systemy AWS S3 lub systemy udostępniania plików.

## <a name="guidelines-for-using-custom-page-content"></a>Wskazówki dotyczące korzystania z niestandardowej zawartości strony

- Użyj bezwzględnego adresu URL w przypadku dołączania do pliku HTML zasobów zewnętrznych, takich jak pliki multimedialne, CSS i JavaScript.
- Korzystając [z układu](page-layout.md) strony w wersji 1.2.0 lub nowszej, możesz dodać atrybut w tagach HTML, aby kontrolować kolejność ładowania dla `data-preload="true"` arkuszy CSS i Języka JavaScript. Za `data-preload="true"` pomocą instrukcji strona jest konstruowana przed ich pokazywanie użytkownikowi. Ten atrybut zapobiega "migotaniu" strony dzięki wstępnemu ładowaniu pliku CSS bez pokazywania użytkownikowi kodu HTML bez stylu. Poniższy fragment kodu HTML przedstawia użycie `data-preload` tagu .
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Zalecamy, aby rozpocząć od domyślnej zawartości strony i utworzyć ją na jej podstawie.
- Do zawartości niestandardowej można dołączyć język [JavaScript.](javascript-and-page-layout.md)
- Obsługiwane wersje przeglądarek to:
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa Internet Explorer 9 i 8
  - Google Chrome 42.0 i więcej
  - Mozilla Firefox 38.0 i więcej
  - Safari dla systemów iOS i macOS, wersja 12 lub nowsza
- Ze względu na ograniczenia zabezpieczeń Azure AD B2C nie obsługują `frame` elementów `iframe` , ani `form` HTML.

## <a name="localize-content"></a>Lokalizacja zawartości

Zawartość HTML można zlokalizowane przez włączenie [dostosowywania](language-customization.md) języka w dzierżawie Azure AD B2C dzierżawie. Włączenie tej funkcji Azure AD B2C przekazywanie OpenID Connect do punktu `ui_locales` końcowego. Serwer zawartości może użyć tego parametru, aby udostępnić strony HTML specyficzne dla języka.

Zawartość można ściągać z różnych miejsc w zależności od użytych danych regionalnych. W punkcie końcowym z obsługą cors należy skonfigurować strukturę folderów do hostów zawartości dla określonych języków. W przypadku użycia wartości z symbolami wieloznacznymi należy wywołać `{Culture:RFC5646}` właściwą.

Na przykład niestandardowy adres URI strony może wyglądać tak:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Możesz załadować stronę w języku francuskim, ściągając zawartość z:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Przewodnik po zawartości strony niestandardowej

Oto omówienie tego procesu:

1. Przygotuj lokalizację do hostów niestandardowej zawartości strony (publicznie dostępnego punktu końcowego HTTPS z obsługą cors).
1. Pobierz i dostosuj domyślny plik zawartości strony, na przykład `unified.html` .
1. Publikowanie niestandardowej zawartości strony jako publicznie dostępnego punktu końcowego HTTPS.
1. Ustaw udostępnianie zasobów między źródłami (CORS) dla aplikacji internetowej.
1. Wskaż zasadom niestandardowy adres URI zawartości zasad.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1. Tworzenie zawartości HTML

Utwórz niestandardową zawartość strony z nazwą marki produktu w tytule.

1. Skopiuj poniższy fragment kodu HTML. Jest to dobrze uformowany kod HTML5 z pustym elementem o *\<div id="api"\>\</div\>* nazwie umieszczonym w *\<body\>* tagach. Ten element wskazuje, Azure AD B2C zawartość ma zostać wstawiona.

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

1. Wklej skopiowany fragment kodu w edytorze tekstów
1. Użyj arkuszy CSS, aby styliować elementy interfejsu użytkownika Azure AD B2C wstawień na stronie. W poniższym przykładzie pokazano prosty plik CSS, który zawiera również ustawienia dla elementów HTML wprowadzanych podczas rejestracji:

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
> Elementy formularza HTML zostaną usunięte z powodu ograniczeń zabezpieczeń, jeśli używasz login.microsoftonline.com. Jeśli chcesz używać elementów formularza HTML w niestandardowej zawartości HTML, użyj [b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Tworzenie konta usługi Azure Blob Storage

W tym artykule używamy usługi Azure Blob Storage do hostowanie naszej zawartości. Możesz hostować zawartość na serwerze internetowym, ale musisz włączyć na nim funkcję [CORS.](https://enable-cors.org/server.html)

Aby hostować zawartość HTML w magazynie obiektów blob, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu **Centrum** wybierz pozycję **Nowe**  >  **konto**  >  **magazynu magazynu.**
1. Wybierz **subskrypcję** dla konta magazynu.
1. Utwórz **grupę zasobów** lub wybierz istniejącą.
1. Wprowadź **unikatową nazwę** konta magazynu.
1. Wybierz **lokalizację geograficzną** dla konta magazynu.
1. **Model wdrażania** może pozostać **Resource Manager**.
1. **Wydajność** może pozostać w **standardowych .**
1. Zmień **typ konta na** Blob **Storage.**
1. **Replikacja** może pozostać **w ra-GRS.**
1. **Warstwa dostępu może** pozostać **gorąca.**
1. Wybierz **pozycję Przeglądanie + tworzenie,** aby utworzyć konto magazynu.
    Po zakończeniu wdrażania zostanie automatycznie otwarta **strona Konto** magazynu.

#### <a name="21-create-a-container"></a>2.1. Tworzenie kontenera

Aby utworzyć kontener publiczny w magazynie obiektów blob, wykonaj następujące kroki:

1. W **Blob service** menu po lewej stronie wybierz pozycję **Obiekty blob**.
1. Wybierz **pozycję +Kontener.**
1. W **nazwie** wprowadź *wartość root*. Nazwa może być nazwą, która zostanie przez użytkownika,  na przykład *contoso*, ale dla uproszczenia użyjemy katalogu głównego w tym przykładzie.
1. W **przypadku poziomu dostępu publicznego** wybierz pozycję **Blob**, a następnie **przycisk OK.**
1. Wybierz **pozycję root,** aby otworzyć nowy kontener.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Przekazywanie plików zawartości strony niestandardowej

1. Wybierz pozycję **Przekaż**.
1. Wybierz ikonę folderu obok **opcji Wybierz plik**.
1. Przejdź do strony **customize-ui.html**, która została utworzona wcześniej w sekcji Dostosowywanie interfejsu użytkownika strony.
1. Jeśli chcesz przekazać plik do podfolderu, rozwiń opcje **Zaawansowane** i wprowadź nazwę folderu w **folderze Przekaż do**.
1. Wybierz pozycję **Przekaż**.
1. Wybierz przekazany **customize-ui.html** obiektu blob.
1. Po prawej stronie pola **tekstowego adresu URL** wybierz ikonę Kopiuj do **schowka,** aby skopiować adres URL do schowka.
1. W przeglądarce internetowej przejdź do skopiowanego adresu URL, aby sprawdzić, czy przekazany obiekt blob jest dostępny. Jeśli jest on niedostępny, na przykład jeśli wystąpi błąd, upewnij się, że typ dostępu do kontenera `ResourceNotFound` jest ustawiony na obiekt **blob**.

### <a name="3-configure-cors"></a>3. Konfigurowanie cors

Skonfiguruj usługę Blob Storage na celu współużytkowanie zasobów między źródłami, wykonując następujące kroki:

1. W menu wybierz pozycję **CORS.**
1. W **przypadku dozwolonych źródeł** wprowadź wartość `https://your-tenant-name.b2clogin.com` . Zastąp `your-tenant-name` nazwą swojej dzierżawy Azure AD B2C dzierżawy. Na przykład `https://fabrikam.b2clogin.com`. Podczas wprowadzania nazwy dzierżawy używaj tylko małych liter.
1. W **przypadku opcji Dozwolone metody** wybierz zarówno pozycję , jak i `GET` `OPTIONS` .
1. W **obszarze Dozwolone nagłówki** wprowadź gwiazdkę (*).
1. W **obszarze Widoczne nagłówki** wprowadź gwiazdkę (*).
1. W **wartość Max age**(Maksymalny wiek) wprowadź wartość 200.
1. Wybierz pozycję **Zapisz**.

#### <a name="31-test-cors"></a>3.1 Testowanie cors

Sprawdź, czy wszystko jest gotowe, wykonując następujące czynności:

1. Powtórz krok konfigurowania corsu. W **przypadku dozwolonych źródeł wprowadź** wartość `https://www.test-cors.org`
1. Przejdź do [www.test-cors.org](https://www.test-cors.org/) 
1. W polu **Zdalny adres URL** wklej adres URL pliku HTML. Na przykład `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Wybierz **pozycję Wyślij żądanie.**
    Wynikiem powinno być `XHR status: 200` . 
    Jeśli wystąpi błąd, upewnij się, że ustawienia cors są poprawne. Może być również konieczne wyczyszczenie pamięci podręcznej przeglądarki lub otwarcie sesji przeglądania w trybie prywatnym przez naciśnięcie klawiszy Ctrl+Shift+P.

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Aktualizowanie przepływu użytkownika

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz **pozycję Przepływy** użytkownika, a następnie wybierz *B2C_1_signupsignin1* przepływu użytkownika.
1. Wybierz **pozycję Układy stron,** a następnie w obszarze **Unified sign-up or sign-in page**(Ujednolicona strona rejestracji lub logowania) kliknij przycisk **Yes** (Tak) w obszarze Use custom page **content (Użyj zawartości strony niestandardowej).**
1. W **niestandardowym uri** strony wprowadź URI dlacustom-ui.htm *l* pliku, który został zarejestrowany wcześniej.
1. W górnej części strony wybierz pozycję **Zapisz**.

### <a name="5-test-the-user-flow"></a>5. Testowanie przepływu użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **Przepływy użytkownika** i wybierz przepływ *B2C_1_signupsignin1* użytkownika.
1. W górnej części strony kliknij pozycję **Uruchom przepływ użytkownika.**
1. Kliknij przycisk **Run user flow (Uruchom przepływ** użytkownika).

Powinna zostać wyświetlony strona podobna do poniższego przykładu z elementami wyśrodkowanymi na podstawie utworzonego pliku CSS:

![Przeglądarka internetowa z wyświetloną stroną rejestracji lub logowania z niestandardowymi elementami interfejsu użytkownika](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. Modyfikowanie pliku rozszerzeń

Aby skonfigurować dostosowywanie interfejsu użytkownika, skopiuj **element ContentDefinition** i jego elementy podrzędne z pliku podstawowego do pliku extensions.

1. Otwórz plik podstawowy zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Ten plik podstawowy jest jednym z plików zasad zawartych w pakiecie startowym zasad niestandardowych, który należy uzyskać w wymaganiach wstępnych Rozpoczynanie pracy [z zasadami niestandardowymi.](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
1. Wyszukaj i skopiuj całą zawartość **elementu ContentDefinitions.**
1. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions.xml*. Wyszukaj element **BuildingBlocks.** Jeśli element nie istnieje, dodaj go.
1. Wklej całą zawartość elementu **ContentDefinitions** skopiowaną jako element podrzędny **elementu BuildingBlocks.**
1. Wyszukaj element **ContentDefinition,** który zawiera `Id="api.signuporsignin"` skopiowany kod XML.
1. Zmień wartość **loaduri** na adres URL pliku HTML przekazanego do magazynu. Na przykład `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Zasady niestandardowe powinny wyglądać podobnie do poniższego fragmentu kodu:

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

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Przekazywanie i testowanie zaktualizowanych zasad niestandardowych

#### <a name="51-upload-the-custom-policy"></a>5.1 Przekazywanie zasad niestandardowych

1. Upewnij się, że używasz katalogu zawierającego dzierżawę usługi Azure AD B2C, wybierając filtr Katalog **i** subskrypcja w górnym menu i wybierając katalog zawierający dzierżawę.
1. Wyszukaj i wybierz **pozycję Azure AD B2C**.
1. W **obszarze Zasady** wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Przekaż zasady niestandardowe.**
1. Przekaż wcześniej zmieniony plik rozszerzeń.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Testowanie zasad niestandardowych przy użyciu opcji **Uruchom teraz**

1. Wybierz przekazane zasady, a następnie wybierz pozycję **Uruchom teraz.**
1. Rejestracja powinna być możliwe przy użyciu adresu e-mail.

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurowanie dynamicznego niestandardowego interfejsu URI zawartości strony

Używając Azure AD B2C zasad niestandardowych, możesz wysłać parametr w ścieżce adresu URL lub ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Parametrem może być dowolny [program rozpoznawania](claim-resolver-overview.md)oświadczenia, taki jak identyfikator aplikacji, identyfikator języka lub niestandardowy parametr ciągu zapytania, taki jak `campaignId` .

### <a name="sending-query-string-parameters"></a>Wysyłanie parametrów ciągu zapytania

Aby wysłać parametry ciągu zapytania, w zasadach [jednostki zależnej](relyingparty.md)dodaj `ContentDefinitionParameters` element , jak pokazano poniżej.

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

W definicji zawartości zmień wartość na `LoadUri` `https://<app_name>.azurewebsites.net/home/unified` . Zasady niestandardowe powinny `ContentDefinition` wyglądać podobnie do poniższego fragmentu kodu:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Po Azure AD B2C strony powoduje wywołanie punktu końcowego serwera internetowego:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dynamiczna zawartość strony URI

Zawartość można ściągać z różnych miejsc na podstawie użytych parametrów. W punkcie końcowym z obsługą cors skonfiguruj strukturę folderów do hostów zawartości. Na przykład możesz uporządkować zawartość w następującej strukturze. Folder *główny/folder na język/pliki HTML.* Na przykład niestandardowy adres URI strony może wyglądać tak:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C wysyła dwuliterowy kod ISO dla języka , dla `fr` języka francuskiego:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>Przykładowe szablony

Przykładowe szablony dostosowywania interfejsu użytkownika można znaleźć tutaj:

```bash
git clone https://github.com/azure-ad-b2c/html-templates
```

Ten projekt zawiera następujące szablony:
- [Ocean Blue](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/AzureBlue)
- [Slate Gray](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/MSA)
- [Klasyczny](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/classic)
- [Zasoby szablonu](https://github.com/azure-ad-b2c/html-templates/tree/main/templates/src)

Aby użyć przykładu:

1. Sklonuj repo na komputerze lokalnym. Wybierz folder szablonu `/AzureBlue` `/MSA` , lub `/classic` .
1. Przekaż wszystkie pliki w folderze szablonu i `/src` folderze do usługi Blob Storage zgodnie z opisem w poprzednich sekcjach.
1. Następnie otwórz każdy `\*.html` plik w folderze szablonu. Następnie zastąp wszystkie wystąpienia adresów `https://login.microsoftonline.com` URL adresem URL przekazanym w kroku 2. Na przykład:
    
    Od:
    ```html
    https://login.microsoftonline.com/templates/src/fonts/segoeui.WOFF
    ```

    Do:
    ```html
    https://your-storage-account.blob.core.windows.net/your-container/templates/src/fonts/segoeui.WOFF
    ```
    
1. Zapisz pliki `\*.html` i przekaż je do magazynu obiektów blob.
1. Teraz zmodyfikuj zasady, wskaż plik HTML, jak wspomniano wcześniej.
1. Jeśli brakuje czcionek, obrazów lub arkuszy CSS, sprawdź odwołania w zasadach rozszerzeń i `\*.html` plikach.

## <a name="use-company-branding-assets-in-custom-html"></a>Używanie zasobów brandingu firmy w niestandardowym kodzie HTML

Aby używać [zasobów brandowania](customize-ui.md#configure-company-branding) firmy w niestandardowym kodzie HTML, dodaj następujące tagi poza `<div id="api">` tagiem. Źródło obrazu jest zastępowane obrazem tła i logo baneru.

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [włączyć kod JavaScript po stronie klienta.](javascript-and-page-layout.md)
