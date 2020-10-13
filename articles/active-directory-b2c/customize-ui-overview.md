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
ms.date: 09/24/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4566e3d40a67a9cd9d61f78884df11536c0809bc
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949296"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w Azure Active Directory B2C

Oznakowanie i dostosowanie interfejsu użytkownika, który jest wyświetlany Azure Active Directory B2C (Azure AD B2C) dla klientów, zapewnia bezproblemowe środowisko użytkownika w aplikacji. Do tych środowisk należą: rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. W tym artykule przedstawiono metody dostosowywania interfejsu użytkownika dla przepływów użytkowników i zasad niestandardowych.

## <a name="ui-customization-in-different-scenarios"></a>Dostosowanie interfejsu użytkownika w różnych scenariuszach

Istnieje kilka sposobów na dostosowanie interfejsu użytkownika aplikacji, z których każdy jest odpowiedni dla różnych scenariuszy.

### <a name="user-flows"></a>Przepływy użytkowników

Jeśli używasz [przepływów użytkownika](user-flow-overview.md), możesz zmienić wygląd stron przepływu użytkownika przy użyciu wbudowanych *szablonów układów stron*lub przy użyciu własnego kodu HTML i CSS. Obie metody zostały omówione w dalszej części tego artykułu.

Użyj [Azure Portal](tutorial-customize-ui.md) , aby skonfigurować Dostosowywanie interfejsu użytkownika dla przepływów użytkowników.

> [!TIP]
> Jeśli chcesz zmodyfikować tylko logo transparentu, obraz tła i kolor tła stron przepływu użytkownika, możesz wypróbować funkcję [znakowania firmowego](#company-branding) opisaną w dalszej części tego artykułu.

### <a name="custom-policies"></a>Zasady niestandardowe

Jeśli używasz [zasad niestandardowych](custom-policy-overview.md) w celu zapewnienia rejestracji lub logowania, resetowania haseł lub edytowania profilu w aplikacji, użyj [plików zasad, aby dostosować interfejs użytkownika](custom-policy-ui-customization.md).

Jeśli musisz dostarczyć zawartość dynamiczną na podstawie decyzji klienta, użyj zasad niestandardowych, które mogą [dynamicznie zmieniać zawartość strony](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) w zależności od parametru, który jest wysyłany w ciągu zapytania. Na przykład można zmienić obraz tła na stronie rejestracji lub logowania na Azure AD B2C na podstawie parametru przekazanego z aplikacji sieci Web lub mobilnej.

### <a name="javascript"></a>JavaScript

Kod JavaScript po stronie klienta można włączyć zarówno w przypadku [przepływów użytkownika](user-flow-javascript-overview.md) , jak i [zasad niestandardowych](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Dostosowanie interfejsu użytkownika tylko do logowania

Jeśli udostępniasz tylko logowanie, wraz z towarzyszącą stroną resetowania hasła i weryfikacyjnymi wiadomościami e-mail, Użyj tych samych kroków dostosowania, które są używane dla [strony logowania usługi Azure AD](../active-directory/fundamentals/customize-branding.md).

Jeśli klienci próbują edytować swój profil przed zalogowaniem się, nastąpi przekierowanie do strony, która została dostosowana, przy użyciu tych samych kroków, które są używane do dostosowywania strony logowania usługi Azure AD.

## <a name="page-layout-templates"></a>Szablony układów stron

Przepływy użytkownika udostępniają kilka wbudowanych szablonów, z których można skorzystać, aby umożliwić użytkownikom korzystanie ze stron o profesjonalnym wyglądzie. Te szablony układów mogą również działać jako punkt wyjścia dla własnych dostosowań.

W obszarze **Dostosuj** w menu po lewej stronie wybierz pozycję **układy stron** , a następnie wybierz pozycję **szablon**.

![Lista rozwijana wybierania szablonu na stronie przepływu użytkownika Azure Portal](media/customize-ui-overview/template-selection.png)

Następnie wybierz szablon z listy. Poniżej przedstawiono przykłady stron logowania dla każdego szablonu:

| Ocean niebieski | Szare | Klasyczny |
|:-:|:-:|:-:|
|![Przykład niebieskiego szablonu oceanu renderowany podczas rejestracji na stronie logowania](media/customize-ui-overview/template-ocean-blue.png)|![Przykład szarego szablonu renderowanego na stronie logowania](media/customize-ui-overview/template-slate-gray.png)|![Przykładowy szablon klasyczny renderowany podczas rejestracji na stronie logowania](media/customize-ui-overview/template-classic.png)|

Po wybraniu szablonu wybrany układ zostanie zastosowany do wszystkich stron w przepływie użytkownika, a identyfikator URI dla każdej strony jest widoczny w polu **niestandardowy identyfikator URI strony** .

## <a name="custom-html-and-css"></a>Niestandardowe HTML i CSS

Jeśli chcesz zaprojektować własny układ zasad przy użyciu dostosowanego kodu HTML i CSS, możesz to zrobić, przełączając przełącznik "Użyj niestandardowej zawartości strony" dla każdej nazwy układu w ramach zasad. Postępuj zgodnie z poniższymi instrukcjami dotyczącymi konfiguracji układu niestandardowego:

Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu metody zwanej [współużytkowaniem zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/).

W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie użytkownika lub w zasadach niestandardowych. Każda Strona w środowisku użytkownika ładuje swoją zawartość z adresu URL określonego dla tej strony. Po załadowaniu zawartości z adresu URL zostanie on scalony z fragmentem kodu HTML wstawionym przez Azure AD B2C, a następnie zostanie wyświetlona strona klienta.

Zapoznaj się z poniższymi wskazówkami przed użyciem własnych plików HTML i CSS, aby dostosować interfejs użytkownika:

- Azure AD B2C **Scala** zawartość HTML na stronach. Nie Kopiuj ani nie próbuj zmienić domyślnej zawartości dostarczanej przez Azure AD B2C. Najlepszym rozwiązaniem jest skompilowanie zawartości HTML od podstaw i użycie domyślnej zawartości jako odwołania.
- **Kod JavaScript** można uwzględnić w zawartości niestandardowej zarówno dla [przepływów użytkownika](user-flow-javascript-overview.md) , jak i [zasad niestandardowych](javascript-samples.md).
- Obsługiwane **wersje przeglądarek** :
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa programu Internet Explorer 9 i 8
  - Google Chrome 42,0 i nowsze
  - Mozilla Firefox 38,0 i nowsze
  - Safari dla systemów iOS i macOS, wersja 12 i nowsze
- Nie dołączaj **tagów formularza** w kodzie HTML. Tagi formularzy zakłócają operacje POST generowane przez kod HTML wprowadzony przez Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Gdzie mogę przechowywać zawartość interfejsu użytkownika?

Korzystając z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, można hostować zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS, który obsługuje mechanizm CORS. Na przykład [usługa Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md), serwery sieci Web, sieci CDN, AWS S3 lub systemy udostępniania plików.

Ważnym punktem jest hostowanie zawartości w publicznie dostępnym punkcie końcowym HTTPS z [włączonym](https://enable-cors.org/server.html)mechanizmem CORS. Musisz użyć bezwzględnego adresu URL, gdy określisz go w zawartości.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat tworzenia zawartości HTML, przekazywania zawartości do usługi Azure Blob Storage i konfigurowania mechanizmu CORS, zobacz sekcję niestandardowe wskazówki dotyczące [zawartości strony](custom-policy-ui-customization.md#custom-page-content-walkthrough) w artykule dotyczącym dostosowywania interfejsu użytkownika.

## <a name="get-started-with-custom-html-and-css"></a>Wprowadzenie do niestandardowego kodu HTML i CSS

Zacznij korzystać z własnego kodu HTML i CSS na stronach środowiska użytkownika, postępując zgodnie z tymi wskazówkami.

- Utwórz poprawnie sformułowaną zawartość HTML z pustym `<div id="api"></div>` elementem znajdującym się w miejscu `<body>` . Ten element oznacza miejsce wstawienia zawartości Azure AD B2C. W poniższym przykładzie przedstawiono minimalną stronę:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Użyj CSS do stylu elementów interfejsu użytkownika, które Azure AD B2C wstawia do strony. Poniższy przykład pokazuje prosty plik CSS, który zawiera również ustawienia dla elementów z wprowadzonym kodem HTML:

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

- Hostowanie zawartości w punkcie końcowym HTTPS (z dozwolonym mechanizmem CORS). Podczas konfigurowania mechanizmu CORS należy włączyć obie metody żądania GET i OPTIONS.
- Utwórz lub Edytuj przepływ użytkownika lub zasady niestandardowe, aby użyć utworzonej zawartości.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmenty kodu HTML z Azure AD B2C

W poniższej tabeli wymieniono fragmenty kodu HTML, które Azure AD B2C scalane do `<div id="api"></div>` elementu znajdującego się w zawartości.

| Wstawiona Strona | Opis HTML |
| ------------- | ------------------- |
| Wybór dostawcy tożsamości | Zawiera listę przycisków dostawców tożsamości, z których może skorzystać klient podczas rejestracji lub logowania. Te przyciski obejmują dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalne (na podstawie adresu e-mail lub nazwy użytkownika). |
| Rejestracja konta lokalnego | Zawiera formularz rejestracji konta lokalnego na podstawie adresu e-mail lub nazwy użytkownika. Formularz może zawierać różne kontrolki wejściowe, takie jak pole wprowadzania tekstu, pole wprowadzania hasła, przycisk radiowy, pola rozwijane z pojedynczym wybieraniem i pola wyboru z zaznaczeniem wielu. |
| Konto społecznościowe — Rejestracja | Może pojawić się podczas rejestracji przy użyciu istniejącego konta od dostawcy tożsamości społecznościowej, takiego jak Facebook lub Google. Jest on używany, gdy dodatkowe informacje muszą być zbierane z klienta przy użyciu formularza tworzenia konta. |
| Ujednolicone rejestrowanie lub logowanie | Obsługuje zarówno rejestrowanie, jak i logowanie klientów, którzy mogą korzystać z dostawców tożsamości społecznościowych, takich jak Facebook, Google lub konta lokalnego. |
| Uwierzytelnianie wieloskładnikowe | Klienci mogą weryfikować numery telefonów (przy użyciu tekstu lub głosu) podczas rejestracji lub logowania. |
| Error | Zawiera informacje o błędach dla klienta. |

## <a name="company-branding"></a>Znakowanie firmowe

Możesz dostosować strony przepływu użytkownika za pomocą logo transparentu, obrazu tła i koloru tła, używając Azure Active Directory [znakowania firmowego](../active-directory/fundamentals/customize-branding.md).

Aby dostosować strony przepływu użytkownika, należy najpierw skonfigurować znakowanie firmowe w Azure Active Directory, a następnie włączyć je w układach stron przepływów użytkownika w Azure AD B2C.

### <a name="configure-company-branding"></a>Konfigurowanie oznaczenia marką firmy

Zacznij od ustawienia logo transparentu, obrazu tła i koloru tła w obszarze **znakowania firmowego**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **Zarządzaj**wybierz opcję **znakowanie firmowe**.
1. Wykonaj kroki opisane w sekcji [Dodawanie znakowania do Azure Active Directory stronie logowania w organizacji](../active-directory/fundamentals/customize-branding.md).

Podczas konfigurowania znakowania firmowego w Azure AD B2C należy pamiętać o następujących kwestiach:

* Znakowanie firmowe w Azure AD B2C jest obecnie ograniczone do **obrazu tła**, **logo transparentu**i dostosowania **koloru tła** . Inne właściwości w okienku znakowania firmowego, na przykład **Ustawienia zaawansowane**, *nie są obsługiwane*.
* Na stronach przepływu użytkownika kolor tła jest wyświetlany przed załadowaniem obrazu tła. Zalecamy wybranie koloru tła, który ściśle dopasowuje kolory w obrazie tła w celu wygładzenia środowiska ładowania.

### <a name="enable-branding-in-user-flow-pages"></a>Włącz znakowanie na stronach przepływu użytkownika

Po skonfigurowaniu znakowania firmowego włącz je w przepływach użytkownika.

1. W menu po lewej stronie Azure Portal wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady**wybierz pozycję **przepływy użytkownika (zasady)**.
1. Wybierz przepływ użytkownika, dla którego chcesz włączyć znakowanie firmowe. Znakowanie firmowe **nie jest obsługiwane** w przypadku standardowych typów przepływów *użytkowników* *logowania* standardowego i standardowego.
1. W obszarze **Dostosowywanie**wybierz pozycję **układy stron**, a następnie wybierz układ, który chcesz oznaczyć marką. Na przykład wybierz pozycję **ujednolicone rejestrowanie lub strona logowania**.
1. W polu **wersja układu strony (wersja zapoznawcza)** wybierz pozycję wersja **1.2.0** lub nowsza.
1. Wybierz pozycję **Zapisz**.

Jeśli chcesz oznaczyć wszystkie strony w przepływie użytkownika, ustaw wersję układu strony dla każdego układu strony w przepływie użytkownika.

![Wybór układu strony w Azure AD B2C Azure Portal](media/customize-ui-overview/portal-02-page-layout-select.png)

Ten przykład adnotacji przedstawia niestandardowe logo transparentu i obraz tła na stronie *rejestracja i logowanie w* przepływie użytkownika, który korzysta z niebieskiego szablonu oceanu:

![Markowe strony rejestracji/logowania obsługiwane przez Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Używanie zasobów znakowania firmowego w niestandardowym kodzie HTML

Aby korzystać z firmowych zasobów znakowania w niestandardowym kodzie HTML, Dodaj następujące znaczniki poza `<div id="api">` tagiem:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Źródło obrazu jest zastępowane obrazem tła i logo transparentu. Zgodnie z opisem w sekcji wprowadzenie [do NIESTANDARDOWEGO HTML i CSS](#get-started-with-custom-html-and-css) , użyj klas CSS do stylu i położenia zasobów na stronie.

## <a name="localize-content"></a>Lokalizowanie zawartości

Zawartość HTML można zlokalizować, włączając [Dostosowywanie języka](user-flow-language-customization.md) w dzierżawie Azure AD B2C. Włączenie tej funkcji umożliwia Azure AD B2C przekazanie parametru OpenID Connect Connect `ui_locales` do punktu końcowego. Twój serwer zawartości może użyć tego parametru, aby udostępnić strony HTML specyficzne dla języka.

Zawartość można ściągnąć z różnych miejsc w oparciu o używane ustawienia regionalne. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości wieloznacznej, nastąpi wywołanie odpowiedniej metody `{Culture:RFC5646}` .

Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Możesz załadować stronę w języku francuskim, pobierając zawartość z:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Przykłady

Kilka przykładowych plików szablonów można znaleźć w repozytorium [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) w witrynie GitHub.

Przykładowe pliki HTML i CSS w szablonach znajdują się w katalogu [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Następne kroki

- Jeśli używasz **przepływów użytkowników**, możesz rozpocząć Dostosowywanie interfejsu użytkownika przy użyciu samouczka:

    [Dostosuj interfejs użytkownika aplikacji w Azure Active Directory B2C](tutorial-customize-ui.md).
- Jeśli używasz **zasad niestandardowych**, możesz rozpocząć Dostosowywanie interfejsu użytkownika przy użyciu artykułu:

    [Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C](custom-policy-ui-customization.md).
