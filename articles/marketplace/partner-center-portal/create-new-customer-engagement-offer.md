---
title: Tworzenie oferty usługi Dynamics 365 dla klientów & usługi PowerApps w komercyjnej witrynie Microsoft Marketplace
description: Jak utworzyć nowy Dynamics 365 dla zaangażowania klienta & ofertę usługi PowerApps w celu uzyskania listy lub sprzedaży w witrynie Azure Marketplace, AppSource lub za pomocą programu Cloud Solution Provider (CSP) w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 106ad6c1669506b5a8086afc1a74d967478e556c
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597291"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Tworzenie oferty Dynamics 365 for Customer Engagement & PowerApps

W tym temacie wyjaśniono, jak utworzyć nową usługę Dynamics 365 dla zaangażowania klienta & ofertę usługi PowerApps. Wszystkie aplikacje dla programu Dynamics 365 przeznaczone do zaangażowania klientów (PowerApps, Sales, Service, Project Service i Field Service) muszą przejść przez nasz proces certyfikacji i obsługiwać okres próbny. Proces certyfikacji sprawdza Twoje rozwiązanie pod kątem standardowych wymagań, zgodności i właściwych praktyk. Środowisko próbne umożliwia użytkownikom wdrożenie rozwiązania w środowisku usługi Dynamics 365 na żywo.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Dynamics 365 do zaangażowania klienta & powerapps**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim będą widoczne w sklepach online dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Identyfikator oferty połączonej z IDENTYFIKATORem wydawcy musi być krótszy niż 40 znaków.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje. Na przykład jeśli identyfikator wydawcy to testpublisherid, a ty wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
- Tego nie można zmienić po wybraniu opcji **Utwórz**.

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

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

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
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Odpowiednie produkty Dynamics 365

Wybierz wszystkie produkty Dynamics 365, których dotyczy ta oferta.

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Klienci będą widzieć tę wersję na liście na stronie szczegółów oferty. Jeśli aktualizujesz tylko numer wersji z powodu zmian marketingowych i opisowych, zaznacz pole wyboru **Marketing Only** . Ta opcja umożliwia pominięcie przez ofertę oferty certyfikacji i aprowizacji.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

W tym miejscu podaj własne warunki prawne i postanowienia. Możesz również podać adres, pod którym można znaleźć warunki i postanowienia. Klienci będą musieli zaakceptować te warunki, aby wypróbować ofertę.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Na tej stronie są wyświetlane Języki, w których zostanie wyświetlona oferta. Obecnie tylko w **języku angielskim (Stany Zjednoczone)** jest jedyną dostępną opcją.

W tym miejscu Zdefiniuj szczegóły witryny Marketplace dla każdego języka/rynku, takie jak nazwa, opis i obrazy oferty. Wybierz nazwę języka/rynku, aby podać te informacje.

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu i warunki użytkowania) nie jest wymagana w języku angielskim, o ile opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w Microsoft AppSource (wszelkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Ilustruje, w jaki sposób ta oferta pojawia się w Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Produkty
3. Kategorie
4. Adres pomocy technicznej (link)
5. Adres Warunki użytkowania (link)
6. Nazwa oferty
7. Opis
8. Zrzuty ekranu/wideo

### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

### <a name="short-description"></a>Krótki opis

Podaj krótki opis oferty, do 100 znaków. Ten opis może być używany w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Słowa kluczowe wyszukiwania

Opcjonalnie możesz wprowadzić do trzech słów kluczowych wyszukiwania, aby pomóc klientom w znalezieniu oferty w portalu Marketplace. Aby uzyskać najlepsze wyniki, należy również użyć tych słów kluczowych w opisie.

### <a name="products-your-app-works-with"></a>Produkty, z którymi pracuje aplikacja

Jeśli chcesz, aby klienci wiedzieli, że aplikacja pracuje z określonymi produktami, w tym miejscu wprowadź maksymalnie trzy nazwy produktów.

### <a name="support-urls"></a>Adresy URL pomocy technicznej

Ta sekcja zawiera linki pomagające klientom w dowiedzieć się więcej o ofercie.

#### <a name="help-link"></a>Link pomocy

Wprowadź adres, na który klienci mogą dowiedzieć się więcej o ofercie.

#### <a name="privacy-policy-url"></a>Adres URL zasad ochrony prywatności

Wprowadź adres do zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

### <a name="contacts"></a>Kontakty

Podaj nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

W sekcji **skontaktuj się z pomocą techniczną** należy również podać **adres URL pomocy technicznej** , w której partnerzy CSP mogą znaleźć obsługę oferty.

### <a name="supporting-documents"></a>Dokumenty pomocnicze

Podaj co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak oficjalne dokumenty, broszury, listy kontrolne lub prezentacje, w formacie PDF.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

Podaj logo i obrazy oferty. Wszystkie obrazy muszą mieć format PNG.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
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

Ta strona umożliwia określenie miejsca i sposobu udostępniania oferty.

### <a name="markets"></a>Wprowadza

Określ rynki, w których oferta powinna być dostępna. Aby to zrobić, wybierz pozycję **Edytuj rynki**, co spowoduje wyświetlenie okna podręcznego **wyboru na rynku** .

Domyślnie nie wybrano żadnych rynków, ale musisz wybrać co najmniej jeden rynek, aby opublikować swoją ofertę. Wybierz  **pozycję Zaznacz wszystko** , aby udostępnić ofertę na każdym możliwym rynku, lub wybierz konkretne rynki, które chcesz dodać.

Wybrane tutaj ustawienia dotyczą tylko nowych nabyć; Jeśli ktoś ma już aplikację na określonym rynku, a później usuniesz ten rynek, osoby, które już posiadają ofertę na rynku, będą mogły nadal z nich korzystać, ale żaden nowy klient nie będzie mógł uzyskać swojej oferty.

> [!IMPORTANT]
> Odpowiedzialność za spełnienie wszelkich lokalnych wymagań prawnych, nawet jeśli te wymagania nie są wymienione w tym miejscu lub w centrum partnerskim. Nawet w przypadku wybrania wszystkich rynków, lokalne prawa i ograniczenia lub inne czynniki mogą uniemożliwić wystawienie niektórych ofert w niektórych krajach i regionach.

### <a name="preview-audience"></a>Podgląd odbiorców

Przed opublikowaniem oferty na żywo w szerszej ofercie z witryny Marketplace musisz najpierw udostępnić ją w ograniczonej **grupie odbiorców w wersji zapoznawczej**. Wprowadź w tym miejscu **klucz ukrycia** (dowolny ciąg, używając tylko małych liter i/lub cyfr). Członkowie Twojej grupy zapoznawczej mogą używać tego klucza Ukryj jako tokenu, aby wyświetlić podgląd oferty w portalu Marketplace.

Gdy wszystko będzie gotowe do udostępnienia oferty i usunięcia ograniczenia wersji zapoznawczej, należy usunąć **klucz Ukryj** i opublikować ponownie.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Ta strona definiuje szczegóły techniczne używane do nawiązania połączenia z ofertą. To połączenie umożliwia nam zainicjowanie oferty dla klienta końcowego, jeśli zdecyduje się ją nabyć.

### <a name="base-license-model"></a>Podstawowy model licencji

Podstawowy model licencji określa, w jaki sposób klienci są przypisani do aplikacji w centrum administracyjnym programu CRM. Wybierz pozycję **zasób** dla licencjonowania lub **użytkownika** opartego na wystąpieniu, jeśli licencje są przypisane do jednej dzierżawy.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Wymaga dostępu do poczty wychodzącej S2S i CRM bezpiecznego magazynu

Zaznacz to pole wyboru, aby włączyć konfigurację dostępu wychodzącego z serwera CRM Secure lub serwer-serwer (S2S). Ta funkcja wymaga wyspecjalizowanej uwagi z zespołu Dynamics 365 w trakcie fazy certyfikacji. Firma Microsoft skontaktuje się z Tobą w celu wykonania dodatkowych czynności w celu obsługi tej funkcji.

### <a name="application-configuration-url"></a>Adres URL konfiguracji aplikacji

Podaj adres URL strony sieci Web konfiguracji, która umożliwia klientowi skonfigurowanie aplikacji.

### <a name="crm-package"></a>Pakiet CRM

W polu **adres URL pola lokalizacja pakietu** wprowadź adres URL konta usługi Azure Blob Storage, które zawiera przekazany plik. zip pakietu CRM. W adresie URL Uwzględnij klucz SAS tylko do odczytu, dzięki czemu firma Microsoft może pobrać pakiet do weryfikacji.

> [!IMPORTANT]
> Aby uniknąć bloku publikowania, upewnij się, że data wygaśnięcia w adresie URL magazynu obiektów BLOB nie wygasła. Możesz skorygować datę, uzyskując dostęp do zasad. Zalecane jest, aby **czas wygaśnięcia** był co najmniej jeden miesiąc w przyszłości.

Zaznacz pole o nazwie **zawiera więcej niż jeden pakiet CRM w pliku pakietu**, jeśli ma zastosowanie. Jeśli tak, pamiętaj o uwzględnieniu wszystkich pakietów w pliku zip.

Aby uzyskać szczegółowe informacje na temat sposobu kompilowania pakietu i aktualizowania jego struktury, zobacz [krok 3. Tworzenie pakietu AppSource dla aplikacji](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Dostępność pakietu CRM

W tej sekcji Wybierz pozycję **+ Dodaj region** , aby określić regiony geograficzne, w których pakiet CRM będzie dostępny dla klientów. Wdrożenie do następujących suwerennych regionów wymaga specjalnych uprawnień i weryfikacji w trakcie procesu certyfikacji: [Niemcy](../../germany/index.yml), [chmura rządowa USA](../../azure-government/documentation-government-welcome.md)i Porada.

Domyślnie **adres URL konfiguracji aplikacji** wprowadzony powyżej zostanie użyty dla każdego regionu. Jeśli wolisz, możesz wprowadzić oddzielny adres URL konfiguracji aplikacji dla jednego lub większej liczby regionów. 

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive-technical-configuration"></a>Wersja testowa konfiguracji technicznej

Ta strona umożliwia skonfigurowanie pokazu ("Test Drive"), który umożliwia klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w temacie [co to jest dysk testowy](../what-is-test-drive.md).

Aby włączyć stację testową, zaznacz pole wyboru **Włącz dysk testowy** na karcie [Konfiguracja oferty](#test-drive) . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Po zakończeniu konfigurowania dysku testowego wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="supplemental-content"></a>Dodatkowa zawartość

Ta strona umożliwia podanie dodatkowych informacji o ofercie, które ułatwią nam zweryfikowanie oferty. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="key-usage-scenario"></a>Scenariusz użycia klucza

Przekaż plik PDF, który zawiera listę scenariuszy użycia klucza oferty. Wszystkie scenariusze mogą zostać zweryfikowane przez nasz zespół ds. weryfikacji przed zatwierdzeniem oferty dla portalu Marketplace.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="publish"></a>Opublikuj

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu.

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - **Nie uruchomiono** — sekcja nie została dotknięcia i należy ją ukończyć.
    - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub aby można było dostarczyć więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - **Ukończono** — sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty, którą można przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać publiczną ofertę publikacji.

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
