---
title: Tworzenie oferty usługi Dynamics 365 dla klientów & usługi PowerApps w komercyjnej witrynie Microsoft Marketplace
description: Jak utworzyć nowy Dynamics 365 dla zaangażowania klienta & ofertę usługi PowerApps w celu uzyskania listy lub sprzedaży w witrynie Azure Marketplace, AppSource lub za pomocą programu Cloud Solution Provider (CSP) w centrum partnerskim.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 51df588b1ccd41bf05d8851f00399b61292b0736
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214109"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Tworzenie oferty Dynamics 365 for Customer Engagement & PowerApps

W tym temacie wyjaśniono, jak utworzyć nową usługę Dynamics 365 dla zaangażowania klienta & ofertę usługi PowerApps. Wszystkie aplikacje dla programu Dynamics 365 przeznaczone do zaangażowania klientów (PowerApps, Sales, Service, Project Service i Field Service) muszą przejść przez nasz proces certyfikacji i obsługiwać okres próbny. Proces certyfikacji sprawdza Twoje rozwiązanie pod kątem standardowych wymagań, zgodności i właściwych praktyk. Środowisko próbne umożliwia użytkownikom wdrożenie rozwiązania w środowisku usługi Dynamics 365 na żywo.

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , jeśli jeszcze tego nie zrobiono. Upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **Dynamics 365 do zaangażowania klienta & powerapps**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim są wyświetlane w obszarze witryny w sklepie po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie publikować po wprowadzeniu zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
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

Wystaw swoją ofertę bezpłatnie klientom, podając prawidłowy adres URL (począwszy od *protokołu HTTP* lub *https*), w którym użytkownicy mogą uzyskać dostęp do Twojej aplikacji.  Na przykład `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Bezpłatna wersja próbna (lista)

Utwórz listę ofert dla klientów z linkiem do bezpłatnej wersji próbnej, podając prawidłowy adres URL (zaczynający się od `http` lub `https` ), gdzie można pobrać wersję próbną.  Na przykład `https://contoso.com/trial/my-app`. Oferta z listą bezpłatnych wersji próbnych jest tworzona, zarządzana i konfigurowana przez usługę i nie ma subskrypcji zarządzanych przez firmę Microsoft.

> [!NOTE]
> Tokeny wysyłane przez aplikację za pomocą linku do wersji próbnej mogą być używane tylko w celu uzyskania informacji o użytkowniku za pomocą usługi Azure Active Directory (Azure AD) w celu zautomatyzowania tworzenia kont w aplikacji. Konta Microsoft nie są obsługiwane na potrzeby uwierzytelniania przy użyciu tego tokenu.

#### <a name="contact-me"></a>Skontaktuj się z nami

Zbierz informacje kontaktowe klienta, łącząc system zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i źródłem witryny Marketplace, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Aby uzyskać więcej informacji o konfigurowaniu programu CRM, zobacz [Customer potencjalni klienci](#customer-leads).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="test-drive"></a>Wersja testowa

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, dając im możliwość "Wypróbuj przed zakupem", co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych potencjalnych klientów. [Dowiedz się więcej o testowaniu dysków](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Aby włączyć dysk testowy przez stały okres czasu, zaznacz pole wyboru **Włącz dysk testowy** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru. Skonfigurowanie środowiska testowego w sekcji [konfiguracja techniczna na dysku testowym](#test-drive-technical-configuration) w dalszej części tego tematu.

Aby uzyskać dodatkowe informacje, zobacz [testowanie oferty w komercyjnej witrynie Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Typ dysku testowego

Wybierz jedną z następujących opcji:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** — szablon wdrożenia zawierający wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** — hosty firmy Microsoft i utrzymują usługę testową (w tym aprowizacji i wdrażania) dla systemu planowania zasobów przedsiębiorstwa w przedsiębiorstwie (Finanse, operacje, łańcuch dostaw, CRM itp.).  
- **[Dynamics 365 dla zaangażowania klienta](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** — hosty firmy Microsoft i utrzymują usługę testową (w tym obsługę i wdrażanie) dla systemu zaangażowania klientów (sprzedaż, usługa, usługa projektu, usługa pola itp.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** — hosty firmy Microsoft i utrzymują usługę testową (w tym aprowizacji i wdrażania) dla systemu planowania zasobów w ramach finansów i operacji (finansów, operacji, produkcji, łańcucha dostaw itp.). 
- **[Aplikacja logiki](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** — szablon wdrożenia obejmujący wszystkie złożone architektury rozwiązań. Wszystkie produkty niestandardowe powinny używać tego typu dysku testowego.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** — osadzony link do pulpitu nawigacyjnego skompilowanego niestandardowo. Produkty, które chcą zaprezentować interaktywną wizualizację Power BI, powinny używać tego typu dysku testowego. Wszystko, co musisz przekazać, to osadzony adres URL Power BI.

#### <a name="additional-test-drive-resources"></a>Dodatkowe zasoby dotyczące dysku testowego

- [Najlepsze rozwiązania techniczne](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Najlepsze rozwiązania marketingowe](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Przegląd dysków testowych](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych wspierających Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie trzy kategorie. Są one używane do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty.

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

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj logo swojej oferty na trzy rozmiary pikseli:
- **Mały** (wymagany; 48 x 48)
- **Duże** (wymagane; 216 x 216)
- **Szeroki** (opcjonalnie; 255 x 115)

#### <a name="hero"></a>Hero

Obraz Hero jest opcjonalny. Jeśli postanowisz jeden, musi on mierzyć 815 x 290 pikseli.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu pokazujące, jak działa Twoja oferta. Wymagany jest co najmniej jeden zrzut ekranu i można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać do czterech filmów wideo, które demonstrują Twoją ofertę. Te filmy wideo powinny być hostowane w usłudze YouTube i/lub Vimeo. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres URL i obraz miniatury filmu wideo (1280 x 720 pikseli)

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

[Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="availability"></a>Dostępność

Ta strona umożliwia określenie miejsca i sposobu udostępniania oferty.

### <a name="markets"></a>Wprowadza

Określ rynki, w których oferta powinna być dostępna. Aby to zrobić, wybierz pozycję **Edytuj rynki**, co spowoduje wyświetlenie okna podręcznego **wyboru na rynku** .

Domyślnie nie wybrano żadnych rynków, ale musisz wybrać co najmniej jeden rynek, aby opublikować swoją ofertę. Wybierz **pozycję Zaznacz wszystko** , aby udostępnić ofertę na każdym możliwym rynku, lub wybierz konkretne rynki, które chcesz dodać.

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

W tej sekcji Wybierz pozycję **+ Dodaj region** , aby określić regiony geograficzne, w których pakiet CRM będzie dostępny dla klientów. Wdrożenie do następujących suwerennych regionów wymaga specjalnych uprawnień i weryfikacji w trakcie procesu certyfikacji: [Niemcy](https://docs.microsoft.com/azure/germany/), [chmura rządowa USA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)i Porada.

Domyślnie **adres URL konfiguracji aplikacji** wprowadzony powyżej zostanie użyty dla każdego regionu. Jeśli wolisz, możesz wprowadzić oddzielny adres URL konfiguracji aplikacji dla jednego lub większej liczby regionów. 

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive-technical-configuration"></a>Testuj konfigurację techniczną

Ta strona umożliwia skonfigurowanie pokazu ("Test Drive"), który umożliwia klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w artykule [co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Aby włączyć stację testową, zaznacz pole wyboru Włącz dysk testowy na karcie [Konfiguracja oferty](#test-drive) . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Dowiedz się więcej w artykule [co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Dostępne są następujące typy dysków testowych, z których każda ma własne wymagania dotyczące konfiguracji technicznej:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikacja logiki](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (konfiguracja techniczna nie jest wymagana)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Konfiguracja techniczna dla Azure Resource Managergo dysku testowego

Szablon wdrożenia zawierający wszystkie zasoby platformy Azure, które składają się na Twoje rozwiązanie. Produkty, które pasują do tego scenariusza, korzystają tylko z zasobów platformy Azure. Dowiedz się więcej o konfigurowaniu [Azure Resource Manager dysku testowego](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiony** (wymagane) — obecnie istnieją 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zwykle warto udostępnić dysk testowy w regionach, w których przewidujesz największą liczbę klientów, dzięki czemu można wybrać najbliższy region dla najlepszej wydajności. Musisz się upewnić, że Twoja subskrypcja może wdrażać wszystkie wymagane przez siebie regiony.
- **Wystąpienia** — wybierz typ (gorąca lub zimna) oraz liczbę dostępnych wystąpień, które będą mnożone przez liczbę regionów, w których oferta jest dostępna.

    **Gorąca** — ten typ wystąpienia jest wdrożony i oczekuje na dostęp w wybranym regionie. Klienci mogą natychmiast uzyskać dostęp do *gorącego* wystąpienia dysku testowego, a nie muszą czekać na wdrożenie. Jest to, że te wystąpienia są zawsze uruchamiane w ramach subskrypcji platformy Azure, dzięki czemu będą one miały większy koszt przestoju. Zdecydowanie zaleca się, aby miało co najmniej jedno *aktywne* wystąpienie, ponieważ większość *klientów nie chce* czekać na pełne wdrożenia, co powoduje odrzucanie w przypadku użycia klienta w przypadku braku dostępnego wystąpienia.

    **Zimne** — ten typ wystąpienia reprezentuje łączną liczbę wystąpień, które mogą być wdrożone w poszczególnych regionach. Zimne wystąpienia wymagają, aby cały dysk testowy Menedżer zasobów szablon do wdrożenia, gdy klient zażąda dysku testowego, więc *zimne* wystąpienia są znacznie wolniejsze, aby można było ładować je od *aktywnych* wystąpień. Wadą jest to, że musisz tylko uregulować czas trwania testu, ale *nie* zawsze działa w ramach subskrypcji platformy Azure, tak jak w przypadku wystąpienia *aktywnego* .

- **Test Azure Resource Manager szablonu** — Przekaż plik zip zawierający szablon Azure Resource Manager. Dowiedz się więcej o tworzeniu szablonu Azure Resource Manager w artykule Szybki Start [Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Czas trwania dysku testowego** (wymagane) — wprowadź liczbę godzin aktywności dysku testowego. TestdDrive kończy się automatycznie po zakończeniu tego okresu. Ten czas trwania może być ustawiony tylko w ciągu całych godzin (na przykład "2" godziny jest prawidłowy; "1,5" nie jest).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Konfiguracja techniczna dla systemu Dynamics 365 Test Drive

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego, udostępniając i utrzymując obsługę administracyjną i wdrożenie usługi przy użyciu tego typu dysku testowego. Konfiguracja dla tego typu hostowanego dysku testowego jest taka sama, niezależnie od tego, czy dysk testowy jest przeznaczony dla odbiorców w biznesie, w ramach zaangażowania z klientami, czy z działu operacji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane) — Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Każdy użytkownik współbieżny będzie korzystał z licencji Dynamics 365, gdy test jest aktywny, więc należy upewnić się, że masz wystarczającą liczbę licencji do obsługi maksymalnego ustawionego limitu. Zalecana wartość to 3-5.

- **Czas trwania dysku testowego** (wymagane) — wprowadź liczbę godzin aktywności dysku testowego. Po upływie tego czasu sesja zostanie zakończona i nie będzie już korzystać z jednej z Twoich licencji. Zalecamy użycie wartości 2-24 godzin w zależności od złożoności oferty. Ten czas trwania może być ustawiony tylko w ciągu całych godzin (na przykład "2" godziny jest prawidłowy; "1,5" nie jest). Użytkownik może zażądać nowej sesji, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagany) — adres URL, pod którym klient będzie rozpoczynać swój dysk testowy. Zwykle jest to adres URL wystąpienia usługi Dynamics 365 z uruchomioną aplikacją z zainstalowanymi przykładowymi danymi (na przykład `https://testdrive.crm.dynamics.com` ).

- **Adres URL internetowego interfejsu API wystąpienia** (wymagany) — Pobierz adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do dostosowywania **ustawień**  >  **Customization**  >  wystąpienia**zasobów deweloperskich**  >  **Web API (główny adres URL usługi)**, skopiuj adres URL znaleziony tutaj (na przykład `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Nazwa roli** (wymagana) — podaj nazwę roli zabezpieczeń, która została zdefiniowana w niestandardowym dysku testowym Dynamics 365. Ta nazwa roli zabezpieczeń zostanie przypisana do użytkownika podczas jego dysku testowego (na przykład roli dysk testowy).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Konfiguracja techniczna dla dysku testowego aplikacji logiki

Wszystkie produkty niestandardowe powinny korzystać z tego typu szablonu wdrożenia dysku testowego, który obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź stronę [operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [zaangażowanie klientów](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w serwisie GitHub.

- **Region** (wymagana, lista rozwijana z pojedynczym wyborem) — obecnie dostępne są 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma jakieś zasoby niestandardowe przechowywane w określonym regionie, upewnij się, że w tym miejscu wybrano region. Najlepszym sposobem jest w pełni wdrażanie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa ona prawidłowo przed wybraniem tej opcji.

- **Maksymalna liczba współbieżnych dysków testowych** (wymagane) — Ustaw maksymalną liczbę klientów, którzy mogą jednocześnie używać danego dysku testowego. Te stacje testowe są już wdrożone, umożliwiając klientom natychmiastowe uzyskiwanie dostępu do nich bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane) — Wprowadź czas aktywności dysku testowego w ciągu kilku godzin. Po upływie tego czasu test kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagana) — wprowadź nazwę [grupy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) , w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagana) — wprowadź nazwę aplikacji logiki, która przypisuje dysk testowy do użytkownika. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

- Anulowanie aprowizacji **nazwy aplikacji logiki** (wymagane) — wprowadź nazwę aplikacji logiki, która powoduje anulowanie obsługi dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi zostać zapisana w powyższej grupie zasobów platformy Azure.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Konfiguracja techniczna dla Power BI dysków testowych nie jest wymagana

Produkty, które chcą zaprezentować interaktywną wizualizację Power BI, mogą użyć osadzonego linku, aby udostępnić pulpit nawigacyjny, który jest wbudowanym niestandardowym jako dysk testowy, nie jest wymagana żadna dodatkowa konfiguracja techniczna. Dowiedz się więcej o konfigurowaniu aplikacji[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) Template.

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby umożliwić firmie Microsoft wdrożenie dysku testowego w Twoim imieniu, Utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure (niewymaganą dla Power BI dysków testowych).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla Azure Resource Manager i aplikacji logiki) — wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure na potrzeby raportowania użycia zasobów i rozliczeń. Zalecamy [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , która ma być używana na potrzeby dysków testowych, jeśli jeszcze jej nie masz. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [Azure Portal](https://portal.azure.com/) i przechodząc do karty **subskrypcje** w menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (na przykład "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagany) — wprowadź [Identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **Właściwości**, a następnie wyszukaj numer **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwy domeny w lokalizacji — [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla dynamicznego 365) — wprowadź nazwę Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [Azure Portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona w polu Nazwa konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagany) — wprowadź [Identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).

- **Wpis tajny klienta aplikacji usługi Azure AD** (wymagane) — wprowadź [klucz tajny klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD. Aby znaleźć tę wartość, zaloguj się do [Azure Portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację testową. Następnie wybierz pozycję **Certyfikaty i wpisy tajne**, wybierz pozycję **Nowy wpis tajny klienta**, wprowadź opis, wybierz pozycję **nigdy nie** w obszarze **wygaśnięcie**, a następnie wybierz pozycję **Dodaj**. Należy pamiętać o skopiowaniu wartości. Nie opuszczaj strony przed przystąpieniem do kopiowania wartości lub nie będziesz mieć dostępu do wartości.)

### <a name="test-drive-marketplace-listings"></a>Testowanie aukcji z portalu Marketplace

Opcja **wystaw w witrynie Marketplace** znajdująca się na karcie **dysk testowy** zawiera Języki, w których dostępny jest dysk testowy. Obecnie tylko w **języku angielskim (Stany Zjednoczone)** jest jedyną dostępną lokalizacją. Wybierz nazwę języka, aby wprowadzić informacje opisujące środowisko testowe.

- **Opis** (wymagany) — opisz swój dysk testowy, co zostanie udowodnione, czy chcesz, aby użytkownik mógł eksperymentować z programem, funkcje do eksplorowania i wszelkie istotne informacje, które ułatwią użytkownikowi określenie, czy uzyskać ofertę. W tym polu można wprowadzić do 3 000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla Azure Resource Manager i logicznych dysków testowych) — Wyjaśnij, co musi znać klient, aby uzyskać dostęp do tego dysku testowego i korzystać z niego. Zapoznaj się z scenariuszem dotyczącym korzystania z oferty i dokładnie tego, co klient powinien znać, aby uzyskać dostęp do funkcji na całym dysku testowym. W tym polu można wprowadzić do 10 000 znaków tekstu.

- **Podręcznik użytkownika** (wymagane) — szczegółowy przewodnik po środowisku testowym. Podręcznik użytkownika powinien obejmować dokładnie te informacje, które klient ma uzyskać, z dysku testowego i służy jako odwołanie do wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i mieć nazwę (maksymalnie 255 znaków) po przekazaniu.

- **Wideo** (opcjonalnie) — przekazywanie filmów wideo do zewnętrznej witryny hostingu odwołuje się do linku i obrazu miniatury (533 x 324 pikseli) w tym miejscu. Dzięki temu Klient może wyświetlić szczegółowe informacje ułatwiające im lepsze zrozumienie dysku testowego, w tym jak pomyślnie korzystać z funkcji oferty i zrozumieć scenariusze, które wyróżnią swoje korzyści.
  - **Nazwa** (wymagana)
  - **Adres** (wymagany; Tylko w serwisie YouTube lub Vimeo)
  - Obraz **miniatury** (plik obrazu musi być w formacie PNG i 533 x 324 pikseli)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="supplemental-content"></a>Dodatkowa zawartość

Ta strona umożliwia podanie dodatkowych informacji o ofercie, które ułatwią nam zweryfikowanie oferty. Te informacje nie są widoczne dla klientów ani opublikowane w portalu Marketplace.

### <a name="key-usage-scenario"></a>Scenariusz użycia klucza

Przekaż plik PDF, który zawiera listę scenariuszy użycia klucza oferty. Wszystkie scenariusze mogą zostać zweryfikowane przez nasz zespół ds. weryfikacji przed zatwierdzeniem oferty dla portalu Marketplace.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu.

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - **Nie uruchomiono** — sekcja nie została dotknięcia i należy ją ukończyć.
    - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub aby można było dostarczyć więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - **Ukończono** — sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- W sekcji **uwagi dotyczące certyfikacji** Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także dodatkowe uwagi przydatne do poznania aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty, którą można przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę do opublikowania w publicznej wersji (lub w przypadku prywatnej oferty do odbiorców prywatnych).

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
