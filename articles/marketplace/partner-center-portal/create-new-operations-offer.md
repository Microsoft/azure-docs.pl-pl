---
title: Tworzenie oferty usługi Dynamics 365 dla operacji w komercyjnej witrynie Marketplace
description: Jak utworzyć nową ofertę Dynamics 365 dla operacji w celu uzyskania listy lub sprzedaży w witrynie Azure Marketplace, AppSource lub za pośrednictwem programu Cloud Solution Provider (CSP) przy użyciu portalu Marketplace w witrynie Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 1c28b4edab1e1033bf9ac7df6b1249b549bb4fe1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131517"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Tworzenie oferty Dynamics 365 for Operations

W tym temacie wyjaśniono, jak utworzyć nową ofertę Dynamics 365 dla operacji. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) to usługa planowania zasobów przedsiębiorstwa (ERP), która obsługuje zaawansowane funkcje finansów, operacji, produkcji i łańcucha dostaw. Wszystkie oferty dla usługi Dynamics 365 dla operacji muszą przejść przez nasz proces certyfikacji.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

>[!NOTE]
> Po opublikowaniu oferty modyfikacje oferty zostaną zaktualizowane tylko w centrum partnerskim i sklepie online po ponownym przesłaniu oferty publikacji.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview** .
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Dynamics 365 dla operacji** .

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty** . To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Identyfikator oferty połączonej z IDENTYFIKATORem wydawcy musi być krótszy niż 40 znaków.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje. Na przykład jeśli identyfikator wydawcy to testpublisherid, a w tym miejscu wprowadzisz polecenie **test-Offer-1** , adres internetowy oferty będzie mieć wartość `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz** .

Wprowadź **alias oferty** . Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

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

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, dając im możliwość "Wypróbuj przed zakupem", co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych potencjalnych klientów. Aby dowiedzieć się więcej, Zacznij od [tego, co to jest dysk testowy](../what-is-test-drive.md).

Aby włączyć dysk testowy przez stały okres czasu, zaznacz pole wyboru **Włącz dysk testowy** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych wspierających Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy** .

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Klienci będą widzieć tę wersję na liście na stronie szczegółów oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne i postanowienia w polu Warunki **i** postanowienia. Możesz także podać adres URL, pod którym można znaleźć warunki i postanowienia. Klienci będą musieli zaakceptować te warunki, aby wypróbować ofertę.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Na tej stronie są wyświetlane Języki, w których zostanie wyświetlona oferta. Obecnie tylko w **języku angielskim (Stany Zjednoczone)** jest jedyną dostępną opcją.

Musisz zdefiniować szczegóły witryny Marketplace (nazwę oferty, opis, obrazy itp.) dla każdego języka/rynku. Wybierz nazwę języka/rynku, aby podać te informacje.

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu, warunki użytkowania itp.) nie jest wymagana w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Produkty
3. Kategorie
4. Branże
5. Adres pomocy technicznej (link)
6. Warunki użytkowania
7. Zasady ochrony prywatności
8. Nazwa oferty
9. Zrzuty ekranu/wideo
10. Opis

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

### <a name="support-urls"></a>Adresy URL pomocy technicznej

Ta sekcja zawiera linki pomagające klientom w dowiedzieć się więcej o ofercie.

#### <a name="help-link"></a>Link pomocy

Wprowadź adres URL, pod którym klienci mogą dowiedzieć się więcej o ofercie.

#### <a name="privacy-policy-url"></a>Adres URL zasad ochrony prywatności

Wprowadź adres URL zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

### <a name="contacts"></a>Kontakty

W tej sekcji Podaj nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego** . Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

W sekcji **skontaktuj się z pomocą techniczną** Podaj **adres URL pomocy technicznej** , pod którą partnerzy CSP mogą znaleźć obsługę oferty.

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Podaj co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak oficjalne dokumenty, broszury, listy kontrolne lub prezentacje. Te dokumenty muszą być w formacie PDF.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

W tej sekcji można podać logo i obrazy, które będą używane podczas wyświetlania oferty dla klienta. Wszystkie obrazy muszą mieć format PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** logo. Opcjonalnie można zastąpić ten inny obraz później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu pokazujące, jak działa Twoja oferta. Wymagany jest co najmniej jeden zrzut ekranu i można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać do czterech filmów wideo, które demonstrują Twoją ofertę. Te filmy wideo powinny być hostowane w usłudze YouTube i/lub Vimeo. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres URL i obraz miniatury filmu wideo (1280 x 720 pikseli)

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

[Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](../gtm-offer-listing-best-practices.md)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="availability"></a>Dostępność

Na tej stronie przedstawiono opcje dotyczące miejsca i sposobu udostępniania oferty.

### <a name="markets"></a>Wprowadza

Ta sekcja umożliwia określenie rynków, w których oferta powinna być dostępna. Aby to zrobić, wybierz pozycję **Edytuj rynki,** co spowoduje wyświetlenie okna podręcznego **wyboru na rynku** .

Domyślnie nie są zaznaczone żadne rynki. Wybierz co najmniej jeden rynek do opublikowania oferty. Kliknij przycisk  **Zaznacz wszystko** , aby udostępnić ofertę na każdym możliwym rynku, lub wybierz konkretne rynki, które chcesz dodać. Po zakończeniu wybierz pozycję **Zapisz** .

Wybrane tutaj ustawienia dotyczą tylko nowych nabyć; Jeśli ktoś ma już aplikację na określonym rynku, a później usuniesz ten rynek, osoby, które już posiadają ofertę na tym rynku, będą mogły nadal z nich korzystać, ale żaden nowy klient nie będzie mógł uzyskać swojej oferty.

> [!IMPORTANT]
> Odpowiedzialność za spełnienie wszelkich lokalnych wymagań prawnych, nawet jeśli te wymagania nie są wymienione w tym miejscu lub w centrum partnerskim.

Pamiętaj, że nawet w przypadku wybrania wszystkich rynków, lokalne prawa i ograniczenia lub inne czynniki mogą uniemożliwić wystawianie niektórych ofert w niektórych krajach i regionach.

### <a name="preview-audience"></a>Podgląd odbiorców

Przed opublikowaniem oferty na żywo w szerszej ofercie z witryny Marketplace musisz najpierw udostępnić ją w ograniczonej **grupie odbiorców w wersji zapoznawczej** . Wprowadź w tym miejscu **klucz ukrycia** (dowolny ciąg, używając tylko małych liter i/lub cyfr). Członkowie Twojej grupy zapoznawczej mogą używać tego klucza Ukryj jako tokenu, aby wyświetlić podgląd oferty w portalu Marketplace.

Gdy wszystko będzie gotowe do udostępnienia oferty i usunięcia ograniczenia wersji zapoznawczej, należy usunąć **klucz Ukryj** i opublikować ponownie.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Ta strona definiuje szczegóły techniczne używane do nawiązania połączenia z ofertą. To połączenie umożliwia nam zainicjowanie oferty dla klienta końcowego, jeśli zdecyduje się ją nabyć.

### <a name="solution-identifier"></a>Identyfikator rozwiązania

Podaj identyfikator rozwiązania (GUID) dla rozwiązania.

Aby znaleźć identyfikator rozwiązania:

1. W obszarze usługi Microsoft Dynamics — cykl życia (LCS) wybierz pozycję **zarządzanie rozwiązaniami** .
2. Wybierz rozwiązanie, a następnie wyszukaj **Identyfikator rozwiązania** w temacie **Omówienie pakietu** . Jeśli identyfikator jest pusty, wybierz pozycję **Edytuj** i ponownie Opublikuj pakiet, a następnie spróbuj ponownie.

### <a name="release-version"></a>Wersja wydania

Wybierz wersję systemu Dynamics 365 dla finansów i operacji, z którymi współpracuje to rozwiązanie.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive-technical-configuration"></a>Wersja testowa konfiguracji technicznej

Ta strona umożliwia skonfigurowanie pokazu ("Test Drive"), który umożliwia klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w temacie [co to jest dysk testowy](../what-is-test-drive.md).

Aby włączyć stację testową, zaznacz pole wyboru **Włącz dysk testowy** na karcie [Konfiguracja oferty](#test-drive) . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Po zakończeniu konfigurowania dysku testowego wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="supplemental-content"></a>Dodatkowa zawartość

Ta strona umożliwia podanie dodatkowych informacji o ofercie, które ułatwią nam zweryfikowanie oferty. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="validation-assets"></a>Zasoby weryfikacji

Przekaż [Raport analizy dostosowania (samochód)](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) w tej sekcji. Ten raport jest generowany przez analizowanie modeli dostosowywania i rozszerzeń na podstawie wstępnie zdefiniowanego zestawu reguł najlepszych rozwiązań.

Ten plik musi być w formacie xls lub xlsx. Jeśli masz więcej niż jeden raport, możesz przekazać plik. zip zawierający wszystkie raporty.

### <a name="does-solution-include-localizations"></a>Czy rozwiązanie obejmuje lokalizacje?

Wybierz opcję **tak** , jeśli rozwiązanie umożliwia korzystanie z lokalnych standardów i zasad (na przykład w przypadku uwzględnienia różnych reguł listy płac wymaganych przez różne kraje/regiony). W przeciwnym razie wybierz opcję **Nie** .

### <a name="does-solution-enable-translations"></a>Czy rozwiązanie włącza tłumaczenia?

Odpowiedź **tak** , jeśli tekst w rozwiązaniu można przetłumaczyć na inne języki. W przeciwnym razie wybierz opcję **Nie** .

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu.

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - **Nie uruchomiono** — sekcja nie została dotknięcia i powinna zostać ukończona.
    - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - **Ukończono** — sekcja jest kompletna, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji. Aby uzyskać więcej informacji o testowaniu instrukcji i zakończeniu pierwszego publikowania, zobacz [AppSource Dynamics 365 Finanse i operacje operacji](../dynamics-365-finance-operations-functional-validation.md).
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij** . Wyślemy Ci wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty, którą można przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę do publicznej publikacji oferty.

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)