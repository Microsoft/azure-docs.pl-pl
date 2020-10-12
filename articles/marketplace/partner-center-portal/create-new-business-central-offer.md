---
title: Tworzenie oferty Dynamics 365 Business Central — Microsoft Commercial Marketplace
description: Zapoznaj się z krokami i zagadnieniami dotyczącymi tworzenia nowej oferty Dynamics 365 Business Central w portalu komercyjnym firmy Microsoft w centrum partnerskim. Możesz wyświetlić lub sprzedać swoją ofertę w witrynie Azure Marketplace lub za pomocą programu Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: bc34d2044c3a91fe18e900b21d589dde855754d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774582"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Tworzenie oferty Dynamics 365 Business Central

W tym artykule wyjaśniono, jak utworzyć nową ofertę Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) to system planowania zasobów przedsiębiorstwa (ERP), który obsługuje szeroką gamę procesów firmy, w tym finansów, operacji, łańcucha dostaw, CRM i zarządzania projektami oraz handlu elektronicznego. Pakiety premium obsługują również klasyczny model wdrażania i produkcję. Wszystkie oferty dla programu Dynamics 365 Business Central muszą przejść przez nasz proces certyfikacji.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Dynamics 365 Business Central**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Identyfikator oferty połączonej z IDENTYFIKATORem wydawcy musi być krótszy niż 40 znaków.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje. Na przykład jeśli identyfikator wydawcy to testpublisherid, a w tym miejscu wprowadzisz polecenie **test-Offer-1** , adres internetowy oferty będzie mieć wartość `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Jak chcesz, aby potencjalni klienci mogli korzystać z tej oferty z licytacją?

Wybierz opcję, której chcesz użyć dla tej oferty.

#### <a name="get-it-now-free"></a>Pobierz teraz (bezpłatnie)

Wystaw swoją ofertę bezpłatnie klientom.

#### <a name="free-trial-listing"></a>Bezpłatna wersja próbna (lista)

Utwórz listę ofert dla klientów z linkiem do bezpłatnej wersji próbnej. Oferta z listą bezpłatnych wersji próbnych jest tworzona, zarządzana i konfigurowana przez usługę i nie ma subskrypcji zarządzanych przez firmę Microsoft.

> [!NOTE]
> Tokeny wysyłane przez aplikację za pomocą linku do wersji próbnej mogą być używane tylko w celu uzyskania informacji o użytkowniku za pomocą usługi Azure Active Directory (Azure AD) w celu zautomatyzowania tworzenia kont w aplikacji. Konta Microsoft nie są obsługiwane na potrzeby uwierzytelniania przy użyciu tego tokenu.

#### <a name="contact-me"></a>Skontaktuj się z nami

Zbierz informacje kontaktowe klienta, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i źródłem witryny Marketplace, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji o konfigurowaniu programu CRM, zobacz [Customer potencjalni klienci](#customer-leads).

### <a name="test-drive"></a>Wersja testowa

W tym momencie oferty Dynamics 365 Business Central nie obsługują wersji testowej. Aby usunąć dysk testowy z oferty, usuń zaznaczenie pola wyboru **Włącz dysk testowy** .

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych wspierających Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Klienci będą widzieć tę wersję na liście na stronie szczegółów oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne i postanowienia w polu Warunki **i** postanowienia. Możesz także podać adres URL, pod którym można znaleźć warunki i postanowienia. Klienci będą musieli zaakceptować te warunki, aby wypróbować ofertę.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Jest to miejsce, w którym można definiować szczegóły oferty, takie jak nazwa, opis i obrazy.

> [!NOTE]
> Możesz podać szczegółowe informacje o ofercie tylko w jednym języku. Nie jest wymagane, aby być w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także podać *adres URL linku pomocy* , aby zaoferować zawartość w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Produkty
3. Kategorie
4. Adres pomocy technicznej (link)
5. Warunki użytkowania
6. Zasady ochrony prywatności
7. Nazwa oferty
8. Podsumowanie
9. Opis
10. Zrzuty ekranu/wideo

### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

### <a name="short-description"></a>Krótki opis

Podaj krótki opis oferty, do 100 znaków. Ten opis może być używany w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Słowa kluczowe wyszukiwania

Opcjonalnie możesz wprowadzić do trzech słów kluczowych wyszukiwania, aby pomóc klientom w znalezieniu oferty w portalu Marketplace. Aby uzyskać najlepsze wyniki, spróbuj użyć tych słów kluczowych również w opisie.

### <a name="products-your-app-works-with"></a>Produkty, z którymi pracuje aplikacja

Jeśli chcesz, aby klienci wiedzieli, że aplikacja pracuje z określonymi produktami, w tym miejscu wprowadź maksymalnie trzy nazwy produktów.

### <a name="helpprivacy-urls"></a>Adresy URL pomocy/prywatności

Ta sekcja zawiera linki pomagające klientom w dowiedzieć się więcej o ofercie.

#### <a name="help-link"></a>Link pomocy

Wprowadź adres URL, pod którym klienci mogą dowiedzieć się więcej o ofercie. **Link pomocy** nie może być taki sam jak **adres URL pomocy technicznej** (wyjaśniony poniżej).

#### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres URL zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

### <a name="contact-information"></a>Informacje kontaktowe

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

W sekcji **skontaktuj się z pomocą techniczną** należy również podać **adres URL pomocy technicznej** , w której partnerzy CSP mogą znaleźć obsługę oferty. Adres URL pomocy technicznej nie może być taki sam jak **link do pomocy**.

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Podaj co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak oficjalne dokumenty, broszury, listy kontrolne lub prezentacje. Te dokumenty muszą mieć format PDF.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** logo. Opcjonalnie można zastąpić ten inny obraz później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje `https://upload.xboxlive.com` usługi używanej przez centrum partnerskie.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu pokazujące, jak działa Twoja oferta. Wymagane są co najmniej trzy zrzuty ekranu i można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli.

#### <a name="videos"></a>Wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów wideo, które demonstrują Twoją ofertę. Te filmy wideo powinny być hostowane w usłudze YouTube i/lub Vimeo. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres URL i obraz miniatury filmu wideo (1280 x 720 pikseli).

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

[Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](../gtm-offer-listing-best-practices.md)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="availability"></a>Dostępność

Na tej stronie przedstawiono opcje, w których można uzyskać dostęp do oferty.

### <a name="markets"></a>Wprowadza

Ta sekcja umożliwia określenie rynków, w których oferta powinna być dostępna. Aby to zrobić, wybierz pozycję **Edytuj rynki**, co spowoduje wyświetlenie okna podręcznego **wyboru na rynku** .

Wybierz co najmniej jeden rynek w celu opublikowania oferty. Wybierz **pozycję Zaznacz wszystko** , aby udostępnić ofertę na każdym możliwym rynku, lub wybierz konkretne rynki, które chcesz dodać.

Wybrane tutaj ustawienia dotyczą tylko nowych nabyć; Jeśli ktoś ma już aplikację na określonym rynku, a później usuniesz ten rynek, osoby, które już posiadają ofertę na tym rynku, będą mogły nadal z nich korzystać, ale żaden nowy klient nie będzie mógł uzyskać swojej oferty.

> [!IMPORTANT]
> Odpowiedzialność za spełnienie wszelkich lokalnych wymagań prawnych, nawet jeśli te wymagania nie są wymienione w tym miejscu lub w centrum partnerskim.

Należy pamiętać, że nawet w przypadku wybrania wszystkich rynków, prawa lokalnego, ograniczenia lub inne czynniki mogą uniemożliwić wyświetlenie niektórych ofert w niektórych krajach i regionach.

### <a name="preview-audience"></a>Podgląd odbiorców

Przed opublikowaniem oferty na żywo w szerszej ofercie z witryny Marketplace musisz najpierw udostępnić ją w ograniczonej **grupie odbiorców w wersji zapoznawczej**. Wprowadź w tym miejscu **klucz ukrycia** (dowolny ciąg, używając tylko małych liter i/lub cyfr). Członkowie Twojej grupy zapoznawczej mogą używać tego klucza Ukryj jako tokenu, aby wyświetlić podgląd oferty w portalu Marketplace.

Gdy wszystko będzie gotowe do udostępnienia oferty i usunięcia ograniczenia wersji zapoznawczej, należy usunąć **klucz Ukryj** i opublikować ponownie.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Ta strona definiuje szczegóły techniczne używane do nawiązania połączenia z ofertą. To połączenie umożliwia nam zainicjowanie oferty dla klienta końcowego, jeśli zdecyduje się ją nabyć.

### <a name="package-type"></a>Typ pakietu

Wybierz opcję, która ma zastosowanie do oferty:

* **Dodaj** — aplikacja dodatku rozszerza środowisko i istniejące funkcje programu Dynamics 365 Business Central. Aby uzyskać szczegółowe informacje, zobacz [aplikacje dodatków](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Connect** — aplikacja Connect może być używana w scenariuszu, w którym musi zostać ustanowione połączenie punkt-punkt między programem Dynamics 365 Business Central i rozwiązaniem lub usługą innej firmy. Aby uzyskać szczegółowe informacje, zobacz [łączenie aplikacji](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Przekazywanie plików

Jeśli zaznaczono opcję **Dodaj na** wyższym miejscu, należy przekazać plik pakietu oferty wraz z plikami pakietu dla każdego rozszerzenia, na którym ma zależności.

#### <a name="extensions-package-file"></a>Plik pakietu rozszerzeń

Przekaż plik pakietu rozszerzenia (App) dla oferty.

#### <a name="library-package-file"></a>Plik pakietu biblioteki

Wymagane, jeśli oferta musi być zainstalowana wraz z innym rozszerzeniem, które nie zostanie opublikowane w portalu Marketplace. Jeśli tak, Przekaż swój plik. app tutaj.

>[!NOTE]
>Plik pakietu zależności nie jest już używany. Zamiast tego Przekaż plik pakietu biblioteki.

### <a name="url-to-app-installation"></a>Adres URL instalacji aplikacji

Jeśli wybrano opcję **Połącz** powyżej, podaj tutaj adres instalacji aplikacji. W przypadku usług połączonych, które nie wymagają instalacji, podaj adres strony docelowej lub stronie rejestracji usługi.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive-technical-configuration"></a>Wersja testowa konfiguracji technicznej

Ta strona umożliwia skonfigurowanie pokazu ("Test Drive"), który umożliwia klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w temacie [co to jest dysk testowy](../what-is-test-drive.md).

Aby włączyć stację testową, zaznacz pole wyboru **Włącz dysk testowy** na karcie [Konfiguracja oferty](#test-drive) . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Po zakończeniu konfigurowania dysku testowego wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="supplemental-content"></a>Dodatkowa zawartość

Ta strona umożliwia podanie dodatkowych informacji o ofercie, które ułatwią nam zweryfikowanie oferty. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="target-release"></a>Wersja docelowa

Wskaż, która wersja programu Microsoft Dynamics Business Central to rozwiązanie docelowe: **Current**, **Next General**lub **Next pomocniczy**. Te informacje umożliwiają nam odpowiednie testowanie Twojego rozwiązania.

### <a name="supported-editions"></a>Obsługiwane wersje

Jeśli oferta wymaga wersji Premium systemu Microsoft Dynamics 365 Business Central, wybierz opcję tylko **Premium** . W przeciwnym razie wybierz zarówno **podstawowe** , jak i **Premium**.

### <a name="key-usage-scenario"></a>Scenariusz użycia klucza

Musisz przekazać plik PDF, który zawiera listę scenariuszy użycia klucza oferty wymienionych w dokumencie (format PDF). Wszystkie scenariusze wymienione w tym miejscu mogą być weryfikowane przez nasz zespół ds. weryfikacji przed zatwierdzeniem oferty dla portalu Marketplace.

### <a name="app-tests-automation"></a>Automatyzacja testów aplikacji

Jeśli Twoja oferta jest aplikacją dodatku, musisz przekazać plik **automatyzacji testów aplikacji** (. app). Ten plik nie ma zastosowania do łączenia aplikacji.

### <a name="test-accounts"></a>Konta testowe

Jeśli konto testowe jest konieczne, aby nasz zespół certyfikacji mógł prawidłowo przejrzeć Twoją ofertę, Przekaż plik PDF, doc lub docx do informacji o **kontach testowych** .

## <a name="publish"></a>Opublikuj

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Publikuj** w prawym górnym rogu portalu. Nastąpi przekierowanie do strony **Recenzja i publikowanie** . 

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - *Nie uruchomiono* — oznacza, że sekcja nie została dotknięcia i należy ją ukończyć.
    - *Niekompletne* — oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - *Gotowe* — oznacza, że sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail, gdy zostanie udostępniona wersja zapoznawcza oferty, którą możesz przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę do publicznej publikacji oferty.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
