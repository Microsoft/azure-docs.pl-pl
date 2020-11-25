---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 4cb4cdef227c786b12790903037fdc02649592e3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95990888"
---
## <a name="use-custom-page-content"></a>Użyj niestandardowej zawartości strony

Za pomocą funkcji dostosowywania interfejsu użytkownika na stronie można dostosować wygląd i działanie wszelkich zasad niestandardowych. Można także zapewnić spójność wizerunku marki i wrażeń wizualnych między aplikacją i usługą Azure AD B2C.

### <a name="how-it-works"></a>Jak to działa

Azure AD B2C uruchamia kod w przeglądarce klienta przy użyciu [udostępniania zasobów między źródłami (CORS)](https://www.w3.org/TR/cors/). W czasie wykonywania zawartość jest ładowana z adresu URL określonego w przepływie użytkownika lub w zasadach niestandardowych. Każda Strona w środowisku użytkownika ładuje swoją zawartość z adresu URL określonego dla tej strony. Po załadowaniu zawartości z adresu URL zostanie on scalony z fragmentem kodu HTML wstawionym przez Azure AD B2C, a następnie zostanie wyświetlona strona klienta.

![Margines zawartości strony niestandardowej](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>Zawartość niestandardowej strony HTML

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>Dostosowywanie domyślnych stron Azure AD B2C

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

Korzystając z własnych plików HTML i CSS, aby dostosować interfejs użytkownika, hostować zawartość interfejsu użytkownika w dowolnym publicznie dostępnym punkcie końcowym HTTPS, który obsługuje mechanizm CORS. Na przykład [usługa Azure Blob Storage](../articles/storage/blobs/storage-blobs-introduction.md), [Azure App Services](../articles/app-service/index.yml), serwery sieci Web, sieci CDN, AWS S3 lub systemy udostępniania plików.

## <a name="guidelines-for-using-custom-page-content"></a>Wskazówki dotyczące korzystania z niestandardowej zawartości strony

- Użyj bezwzględnego adresu URL, jeśli w pliku HTML są uwzględniane zasoby zewnętrzne, takie jak pliki multimedialne, CSS i JavaScript.
- Korzystając z [wersji układu strony](../articles/active-directory-b2c/page-layout.md) 1.2.0 i nowszych, można dodać `data-preload="true"` ATRYBUT do tagów HTML w celu kontrolowania kolejności ładowania dla CSS i JavaScript. W programie `data-preload=true` Strona jest zbudowana przed wyświetleniem użytkownika. Ten atrybut pomaga zapobiec "migotaniu" na stronie przez wstępne załadowanie pliku CSS bez wyświetlania kodu HTML, który nie ma stylu. Poniższy fragment kodu HTML pokazuje użycie `data-preload` znacznika.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Zalecamy rozpoczęcie od domyślnej zawartości strony i jej skompilowanie na jej podstawie.
- Możesz dołączyć kod JavaScript do zawartości niestandardowej zarówno dla [przepływów użytkownika](../articles/active-directory-b2c/user-flow-javascript-overview.md) , jak i [zasad niestandardowych](../articles/active-directory-b2c/javascript-samples.md).
- Obsługiwane wersje przeglądarek:
  - Internet Explorer 11, 10 i Microsoft Edge
  - Ograniczona obsługa programu Internet Explorer 9 i 8
  - Google Chrome 42,0 i nowsze
  - Mozilla Firefox 38,0 i nowsze
  - Safari dla systemów iOS i macOS, wersja 12 i nowsze
- Ze względu na ograniczenia zabezpieczeń Azure AD B2C nie `frame` obsługuje `iframe` elementów, ani `form` HTML.

## <a name="custom-page-content-walkthrough"></a>Przewodnik po zawartości strony niestandardowej

Poniżej przedstawiono omówienie procesu:

1. Przygotuj lokalizację do hostowania niestandardowej zawartości strony (dostępny publicznie punkt końcowy HTTPS z obsługą mechanizmu CORS).
1. Pobierz i Dostosuj domyślny plik zawartości strony, na przykład `unified.html` .
1. Opublikuj zawartość strony niestandardowej w publicznie dostępnym punkcie końcowym HTTPS.
1. Ustaw współużytkowanie zasobów między źródłami (CORS) dla aplikacji sieci Web.
1. Wskaż zasady dla niestandardowego identyfikatora URI zawartości zasad.

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

1. Wklej skopiowany fragment w edytorze tekstów, a następnie Zapisz plik jako *customize-ui.html*.

> [!NOTE]
> Elementy formularza HTML zostaną usunięte ze względu na ograniczenia zabezpieczeń, jeśli używasz login.microsoftonline.com. Jeśli chcesz użyć elementów formularza HTML w niestandardowej zawartości HTML, [użyj b2clogin.com](../articles/active-directory-b2c/b2clogin.md).

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