---
title: Tworzenie nowej oferty Dynamics 365 for Customer Engagement & Usługi PowerApps w komercyjnym portalu Marketplace
description: How to create a new Dynamics 365 for Customer Engagement & PowerApps offer for listing or selling in the Azure Marketplace, AppSource, or through the Cloud Solution Provider (CSP) program using the Commercial Marketplace portal on Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 0ec62095f14b38d0c4ffebfabc14c4a0a27a70fb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869823"
---
# <a name="create-a-new-dynamics-365-for-customer-engagement--powerapps-offer"></a>Tworzenie nowej oferty Dynamics 365 for Customer Engagement & PowerApps

W tym temacie wyjaśniono, jak utworzyć nową ofertę Dynamics 365 for Customer Engagement & PowerApps. Wszystkie aplikacje dynamics 365 for Customer Engagement (PowerApps, Sales, Service, Project Service i Field Service) muszą przejść przez nasz proces certyfikacji i obsługiwać środowisko próbne. Proces certyfikacji sprawdza rozwiązanie pod kątem standardowych wymagań, zgodności i odpowiednich praktyk. Środowisko testowe umożliwia użytkownikom wdrożenie rozwiązania w środowisku dynamics 365 na żywo.

Aby rozpocząć tworzenie dynamics 365 for Customer Engagement & oferty usługi PowerApps, upewnij się, że najpierw [utwórz konto Centrum partnerów](./create-account.md) i otwórz [pulpit nawigacyjny portalu komercyjnego w portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z wybraną kartą **Przegląd.**

![Pulpit nawigacyjny portalu Marketplace w Centrum partnerów](./media/new-offer-overview.png)

>[!Note]
> Po opublikowaniu oferty zmiany w ofercie złożonej w Centrum partnerskim zostaną zaktualizowane dopiero po ponownym opublikowaniu w systemie i na frontach sklepu. Upewnij się, że po dokonaniu zmian należy przesłać ofertę do publikacji.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Wybierz przycisk **+ Nowa oferta,** a następnie wybierz element menu **Dynamics 365 for Customer Engagement & PowerApps.** Zostanie wyświetlone okno dialogowe **Nowa oferta.**

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

- **Identyfikator oferty:** Unikatowy identyfikator dla każdej oferty na twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL oferty portalu Marketplace i szablonach usługi Azure Resource Manager (jeśli dotyczy). Identyfikator oferty musi być małe litery alfanumeryczne znaki (w tym łączniki i podkreślenia, ale nie odstępy). Identyfikator jest ograniczony do 50 znaków i nie można go zmienić po wybraniu opcji **Utwórz**. Na przykład, jeśli wprowadzisz *test-offer-1* tutaj, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres URL oferty będzie .

- **Alias oferty**: Nazwa używana do odwoływania się do oferty w Centrum partnerów. Ta nazwa nie będzie używana w portalu marketplace i różni się od nazwy oferty i innych wartości, które będą wyświetlane klientom. Tej wartości nie można zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu identyfikatora **oferty** i **aliasu oferty**wybierz pozycję **Utwórz**. Będziesz wtedy mógł pracować nad wszystkimi częściami swojej oferty.

## <a name="offer-setup"></a>Konfiguracja oferty

Strona **Ustawienia oferty** prosi o następujące informacje. Pamiętaj, aby wybrać **pozycję Zapisz** po zakończeniu tych pól.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Jak chcesz, aby potencjalni klienci wchodzili w interakcje z tą ofertą aukcji?

Wybierz opcję, której chcesz użyć w tej ofercie.

#### <a name="get-it-now-free"></a>Pobierz go teraz (za darmo)

Wystaw swoją ofertę klientom za darmo, podając prawidłowy adres URL (zaczynający się od *http* lub *https),* w którym mogą uzyskać dostęp do Twojej aplikacji.  Na przykład: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Bezpłatna wersja próbna (aukcja)

Wystaw swoją ofertę klientom z linkiem do bezpłatnej wersji próbnej, podając prawidłowy adres URL (zaczynający się od *http* lub *https),* gdzie mogą uzyskać wersję próbną.  Na przykład: `https://contoso.com/trial/my-app`. Oferta bezpłatna wersje próbna są tworzone, zarządzane i konfigurowane przez usługę i nie mają subskrypcji zarządzanych przez firmę Microsoft.

> [!NOTE]
> Tokeny, które aplikacja otrzyma za pośrednictwem łącza próbnego, mogą być używane tylko do uzyskiwania informacji o użytkowniku za pośrednictwem usługi Azure Active Directory (Azure AD) w celu zautomatyzowania tworzenia konta w aplikacji. Konta Microsoft nie są obsługiwane do uwierzytelniania przy użyciu tego tokenu.

#### <a name="contact-me"></a>Skontaktuj się ze mną

Zbieraj informacje kontaktowe klientów, łącząc system CRM (Customer Relationship Management). Klient zostanie poproszony o pozwolenie na udostępnienie swoich informacji. Te dane klienta, wraz z nazwą oferty, identyfikatorem i źródłem witryny, w której znaleźli Twoją ofertę, zostaną wysłane do skonfigurowanym systemu CRM. Aby uzyskać więcej informacji na temat konfigurowania programu CRM, zobacz [Łączenie zarządzania potencjalnymi klientami](#connect-lead-management). 

### <a name="test-drive"></a>Jazda próbna

Jazda próbna to świetny sposób na zaprezentowanie oferty potencjalnym klientom, dając im możliwość "wypróbuj przed zakupem", co skutkuje zwiększoną konwersją i generowaniem wysoko wykwalifikowanych potencjalnych klientów. [Dowiedz się więcej o dyskach testowych.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Aby włączyć jazdę próbną, zaznacz pole **Włącz napęd testowy.** Następnie należy skonfigurować środowisko demonstracyjne w [konfiguracji technicznej dysku testowego](#test-drive-technical-configuration) skonfigurować, aby umożliwić klientom wypróbowanie oferty przez określony czas. 

#### <a name="type-of-test-drive"></a>Rodzaj jazdy testowej

Wybierz jedną z następujących opcji:

- **[Usługa Azure Resource Manager:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, które składają się na rozwiązanie. Produkty, które pasują do tego scenariusza, używają tylko zasobów platformy Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: Firma Microsoft obsługuje i utrzymuje usługę jazdy testowej (w tym inicjowanie obsługi administracyjnej i wdrażanie) dla systemu planowania zasobów przedsiębiorstwa Business Central (finanse, operacje, łańcuch dostaw, CRM itp.).  
- **[Dynamics 365 for Customer Engagement:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** Firma Microsoft obsługuje i utrzymuje usługę dysku testowego (w tym inicjowanie obsługi administracyjnej i wdrażanie) dla systemu Customer Engagement (sprzedaż, usługa, usługa projektu, obsługa w terenie itp.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Firma Microsoft obsługuje i utrzymuje usługę dysku testowego (w tym inicjowanie obsługi administracyjnej i wdrażanie) dla systemu planowania zasobów przedsiębiorstwa w sektorze finansowym i operacyjnym (finanse, operacje, produkcja, łańcuch dostaw itp.). 
- **[Aplikacja logiki:](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** szablon wdrażania obejmujący wszystkie architektury złożonych rozwiązań. Wszystkie produkty niestandardowe powinny używać tego typu dysku testowego.
- **[Power BI:](https://docs.microsoft.com/power-bi/service-template-apps-overview)** osadzone łącze do niestandardowego pulpitu nawigacyjnego. Produkty, które chcą zademonstrować interaktywną wizualizację usługi Power BI, powinny używać tego typu dysku testowego. Wszystko, co musisz przesłać tutaj, to osadzony adres URL usługi Power BI.

#### <a name="additional-test-drive-resources"></a>Dodatkowe zasoby dysku próbne

- [Najlepsze praktyki techniczne dotyczące jazdy próbkowej](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Sprawdzone praktyki marketingowe na dysku testowym](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Przegląd dysku testowego One Pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Łączenie zarządzania potencjalnymi klientami

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz [Omówienie zarządzania potencjalnymi klientami](./commercial-marketplace-get-customer-leads.md).

Pamiętaj, **aby zapisać** przed przejściem do następnej sekcji.

## <a name="properties"></a>Właściwości

Strona **Właściwości** umożliwia zdefiniowanie kategorii i branż używanych do grupowania oferty w portalu Marketplace, wersji aplikacji i umów prawnych wspierających ofertę. Wybierz **pozycję Zapisz** po zakończeniu tej strony.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie trzy kategorie. **Kategoria** zostanie wykorzystana do umieszczenia oferty w odpowiednich obszarach wyszukiwania w rynku. Pamiętaj, aby sprawdzić, w jaki sposób Twoja oferta obsługuje te kategorie w opisie oferty.

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Odpowiednie produkty Dynamics 365

Wybierz wszystkie produkty Dynamics 365, których dotyczy ta oferta.

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Klienci zobaczą tę wersję wymienioną na stronie szczegółów oferty. Jeśli numer wersji jest aktualizowany tylko z powodu zmian marketingowych/opisowych, zaznacz pole Zmień **tylko marketing.** Ta opcja umożliwia oferty pominąć certyfikacji i inicjowania obsługi administracyjnej etapów.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne w polu **Warunki.** Możesz też podać adres URL, pod którym można znaleźć warunki. Klienci będą musieli zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

## <a name="offer-listing"></a>Oferta aukcji

Na stronie Oferta wyświetlana jest liczba języków, w których oferta będzie wyświetlana. Obecnie jedyną dostępną opcją jest **angielski (Stany Zjednoczone).**

Musisz zdefiniować szczegóły rynku (nazwa oferty, opis, obrazy itp.) dla każdego języka / rynku. Wybierz język/nazwę rynku, aby podać te informacje.

> [!NOTE]
> Zawartość aukcji oferty (taka jak opis, dokumenty, zrzuty ekranu, warunki użytkowania itp.) nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Dopuszczalne jest również podanie *użytecznego adresu URL linku,* aby oferować treści w języku innym niż ten używany w treści aukcji Oferty.

### <a name="name"></a>Nazwa

Nazwa, którą wpiszesz w tym miejscu, będzie wyświetlana klientom jako tytuł twojej oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu Oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Nazwa ta może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich). Nazwa nie może zawierać więcej niż 50 znaków i nie może zawierać żadnych emotikonów.

### <a name="short-description"></a>Krótki opis

Podaj krótki opis swojej oferty (do 100 znaków). Ten opis może być używany w wynikach wyszukiwania w portalu Marketplace.











### <a name="description"></a>Opis

Podaj dłuższy opis swojej oferty (do 3000 znaków). Ten opis będzie wyświetlany klientom w przeglądzie aukcji w portalu marketplace. Dołącz propozycję wartości oferty, kluczowe korzyści, kategorie i/lub stowarzyszenia branżowe, możliwości zakupu w aplikacji i wszelkie wymagane informacje.

Kilka wskazówek dotyczących pisania opisu:  

- Wyraźnie opisz propozycję wartości swojej oferty w pierwszych kilku zdaniach opisu. Dołącz następujące informacje do swojej propozycji wartości:
  - Opis produktu
  - Typ użytkownika, który korzysta z produktu
  - Klient potrzebuje lub bólu, który produkt adresuje
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.  
- Nie polegaj na funkcjach i funkcjach sprzedaży produktu. Zamiast tego skup się na wartości, którą dostarczasz.  
- W miarę możliwości używaj słownictwa branżowego lub opartego na korzyściach.
- Rozważ użycie tagów HTML do sformatuj opisu i zwiększenie jego atrakcyjność.

Aby opis oferty był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu.

![Korzystanie z edytora tekstu sformatowego](./media/text-editor2.png)

Użyj następujących instrukcji, aby użyć edytora tekstu sformatowego:

- Aby zmienić format zawartości, wyróżnij tekst, który chcesz sformatować, i zaznacz styl tekstu, jak pokazano poniżej:

     ![Zmiana formatu tekstu za pomocą edytora tekstu sformatowania](./media/text-editor3.png)

- Aby dodać do tekstu listę punktowaną lub ponumerowaną, użyj poniższych opcji:

     ![Dodawanie list za pomocą edytora tekstu sformatowego](./media/text-editor4.png)

- Aby dodać lub usunąć wcięcie do tekstu, użyj poniższych opcji:

     ![Używanie edytora tekstu sformatowego do wcięcie](./media/text-editor5.png)







### <a name="search-keywords"></a>Szukaj słów kluczowych

Opcjonalnie możesz wprowadzić maksymalnie trzy słowa kluczowe wyszukiwania, aby pomóc klientom znaleźć twoją ofertę w marketplace. Aby uzyskać najlepsze wyniki, spróbuj użyć tych słów kluczowych w opisie.

### <a name="products-your-app-works-with"></a>Produkty, z w które współpracuje Twoja aplikacja

Jeśli chcesz poinformować klientów, że aplikacja działa z określonymi produktami, wprowadź w tym miejscu maksymalnie trzy nazwy produktów.

### <a name="support-urls"></a>Obsługa adresów URL

W tej sekcji znajdziesz łącza ułatwiające klientom zapoznanie się z ofertą.

#### <a name="help-link"></a>Link pomocy

Wpisz adres URL, pod którym klienci mogą dowiedzieć się więcej o Twojej ofercie.

#### <a name="privacy-policy-url"></a>Adres URL polityki prywatności

Wprowadź adres URL do zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i regulacjami dotyczącymi prywatności oraz za zapewnienie ważnej polityki prywatności.

### <a name="contacts"></a>Kontakty

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje nie są wyświetlane klientom, ale będą dostępne dla firmy Microsoft i mogą być dostarczane partnerom CSP.

W sekcji **Kontakt pomocy technicznej** należy również podać adres URL pomocy **technicznej,** w którym partnerzy CSP mogą znaleźć pomoc techniczną dla Twojej oferty.

### <a name="supporting-documents"></a>Dokumenty uzupełniające

W tym miejscu należy dostarczyć co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak białe dokumenty, broszury, listy kontrolne lub prezentacje. Dokumenty te muszą być w formacie .pdf.

### <a name="marketplace-images"></a>Obrazy z Marketplace

W tej sekcji możesz podać logo i obrazy, które będą używane podczas wyświetlania oferty klientowi. Wszystkie obrazy muszą być w formacie png.

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, https://upload.xboxlive.com że sieć lokalna nie blokuje usługi używanej przez Centrum partnerów.

#### <a name="store-logos"></a>Logo sklepu

Logo swojej oferty można podać w trzech rozmiarach: **Mały (48 x 48),** **Duży (216 x 216)** i **Szeroki (255 x 115).** Wymagane są małe i duże rozmiary.

#### <a name="hero"></a>Bohater

Obraz bohatera jest opcjonalny. Jeśli go podasz, musi mierzyć 815 x 290 pikseli.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu, które pokazują, jak działa Twoja oferta. Wymagany jest co najmniej jeden zrzut ekranu i można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć rozmiary 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie cztery filmy, które zademonstrują Twoją ofertę. Te filmy powinny być hostowane na YouTube i / lub Vimeo. Dla każdego z nich wprowadź nazwę filmu, jego adres URL i miniaturę filmu (1280 x 720 pikseli)

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

- [Najważniejsze wskazówki dotyczące ofert w marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Dostępność

Na stronie **Dostępność** znajdziesz opcje dotyczące tego, gdzie i jak udostępnić ofertę.

### <a name="markets"></a>Rynków

W tej sekcji można określić rynki, na których oferta powinna być dostępna. Aby to zrobić, wybierz **pozycję Edytuj rynki**, które wyświetli okno **podręczne Wybór rynku.**

Domyślnie nie wybrano żadnych rynków, ale musisz wybrać co najmniej jeden rynek, aby opublikować swoją ofertę. Kliknij **wybierz wszystkie,** aby udostępnić swoją ofertę na każdym możliwym rynku, lub wybierz konkretne rynki, które chcesz dodać. Po zakończeniu wybierz pozycję **Zapisz**.

Twoje wybory tutaj dotyczą tylko nowych nabytków; jeśli ktoś ma już Twoją aplikację na określonym rynku, a później usuniesz ten rynek, osoby, które już mają ofertę na tym rynku, mogą nadal z niej korzystać, ale żaden nowy klient na tym rynku nie będzie mógł uzyskać Twojej oferty.

> [!IMPORTANT]
> Twoim obowiązkiem jest spełnienie lokalnych wymogów prawnych, nawet jeśli te wymagania nie są wymienione tutaj lub w Centrum partnerów. Nawet jeśli wybierzesz wszystkie rynki, lokalne przepisy i ograniczenia lub inne czynniki mogą uniemożliwić niektóre oferty na liście w niektórych krajach i regionach.

### <a name="preview-audience"></a>Podgląd odbiorców

Zanim opublikujesz ofertę na żywo w szerszej ofercie rynkowej, musisz najpierw udostępnić ją ograniczonej **ofercie podglądu.** W tym miejscu należy wprowadzić **klawisz Ukryj** (dowolny ciąg przy użyciu tylko małych liter i/lub cyfr). Członkowie grupy odbiorców w wersji zapoznawczej mogą używać tego klucza ukrywania jako tokenu, aby wyświetlić podgląd oferty w portalu marketplace.

Następnie, gdy wszystko będzie gotowe udostępnić ofertę i usuniesz ograniczenie podglądu, musisz usunąć **klucz Ukryj** i opublikować go ponownie.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Strona **konfiguracji technicznej** definiuje szczegóły techniczne używane do łączenia się z ofertą. To połączenie umożliwia nam udostępnienie oferty klientowi końcowemu, jeśli zdecyduje się ją nabyć.

### <a name="base-license-model"></a>Podstawowy model licencji

Podstawowy model licencji określa sposób przypisywania aplikacji klientom w Centrum administracyjnym programu CRM. Wybierz **zasób** dla licencjonowania opartego na wystąpieniu lub **Użytkownik,** jeśli licencje są przypisane jeden na dzierżawę.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Wymaga dostępu wychodzącego S2S i crm secure store

Zaznacz to pole wyboru, aby włączyć konfigurację dostępu wychodzącego programu CRM Secure Store lub Server-to-Server (S2S). Ta funkcja wymaga specjalistycznego rozważenia ze strony zespołu Dynamics 365 podczas fazy certyfikacji. Firma Microsoft skontaktuje się z Użytkownikiem w celu wykonania dodatkowych kroków w celu obsługi tej funkcji.

### <a name="application-configuration-url"></a>Adres URL konfiguracji aplikacji

Podaj adres URL strony sieci Web konfiguracji, która umożliwia klientowi skonfigurowanie aplikacji.

### <a name="crm-package"></a>Pakiet CRM

W **adresie URL** pola lokalizacji pakietu wprowadź adres URL konta usługi Azure Storage zawierającego przekazany plik zip pakietu CRM. Ten adres URL powinien zawierać klucz tylko do odczytu sas, aby umożliwić firmie Microsoft pobranie pakietu w celu weryfikacji.

Zaznacz pole oznaczone W pliku pakietu znajduje się **więcej niż jeden pakiet CRM**, jeśli dotyczy. Jeśli tak, pamiętaj o dołączeniu wszystkich pakietów do pliku .zip.

### <a name="crm-package-availability"></a>Dostępność pakietów CRM

W tej sekcji wybierz **+ Dodaj region,** aby określić regiony geograficzne, w których pakiet CRM będzie dostępny dla klientów. Wdrażanie w następujących suwerennych regionach wymaga specjalnych uprawnień i walidacji podczas procesu certyfikacji: [Niemcy,](https://docs.microsoft.com/azure/germany/) [Us Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)i TIP.

Domyślnie **adres URL konfiguracji aplikacji,** który został wprowadzony powyżej, będzie używany dla każdego regionu. Jeśli wolisz, możesz wprowadzić oddzielny adres URL konfiguracji aplikacji dla jednego lub kilku określonych regionów. 

## <a name="test-drive-technical-configuration"></a>Konfiguracja techniczna jazdy testowej

Jeśli **wybrano opcję Włącz jazdę próbną** na stronie [Ustawienia oferty,](#offer-setup) musisz podać szczegóły w tym miejscu, aby umożliwić klientom korzystanie z jazdy próbnej oferty.

Strona **Dysku testowego** umożliwia skonfigurowanie demonstracji (lub "jazdy testowej"), która umożliwi klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w artykule [Co to jest jazda testna?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive). Jeśli nie chcesz już dostarczać dysku testowego dla swojej oferty, wróć do strony **[Ustawienia oferty](#offer-setup)** i wyjdź ze strony **Włącz jazdę próbną**.

Dostępne są następujące typy dysków testowych, z których każdy ma własne wymagania techniczne konfiguracji.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikacja logika](#technical-configuration-for-logic-app-test-drive)
- [Usługa Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (konfiguracja techniczna nie jest wymagana)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Konfiguracja techniczna dla dysku testowego usługi Azure Resource Manager

Szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, które składają się na rozwiązanie. Produkty, które pasują do tego scenariusza, używają tylko zasobów platformy Azure. Dowiedz się więcej o konfigurowaniu [dysku testowego usługi Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Regiony** (wymagane): Obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zazwyczaj należy udostępnić jazdę próbną w regionach, w których przewiduje się największą liczbę klientów, aby mogli wybrać najbliższy region dla najlepszej wydajności. Należy upewnić się, że subskrypcja może wdrożyć wszystkie zasoby potrzebne w każdym z regionów, które wybierasz.

- **Wystąpienia:** Wybierz typ (gorący lub zimny) i liczbę dostępnych wystąpień, które zostaną pomnożone przez liczbę regionów, w których oferta jest dostępna.

**Hot:** Ten typ wystąpienia jest wdrażany i oczekuje na dostęp dla wybranego regionu. Klienci mogą natychmiast uzyskać dostęp *do gorących* wystąpień dysku testowego, zamiast czekać na wdrożenie. Kompromisem jest to, że te wystąpienia są zawsze uruchomione w ramach subskrypcji platformy Azure, więc poniosą one większy koszt czasu pracy bez przestojów. Zdecydowanie zaleca się mieć co najmniej jedno *wystąpienie Hot,* ponieważ większość klientów nie chce czekać na pełne wdrożenia, co powoduje spadek użycia klienta, jeśli nie jest dostępne żadne *wystąpienie Hot.*

**Zimno:** Ten typ wystąpienia reprezentuje całkowitą liczbę wystąpień, które mogą być ewentualnie wdrożone na region. Zimne wystąpienia wymagają całego szablonu Menedżera zasobów dysku testowego do wdrożenia, gdy klient zażąda dysku testowego, *więc* zimne wystąpienia są znacznie wolniejsze do załadowania niż *wystąpienia hot.* Kompromisem jest to, że trzeba płacić tylko za czas trwania dysku testowego, *nie* zawsze jest uruchomiony w ramach subskrypcji platformy Azure, jak w przypadku *wystąpienia Hot.*

- **Szablon usługi Azure Resource Manager na dysku testowym:** przekaż plik zip zawierający szablon usługi Azure Resource Manager.  Dowiedz się więcej o tworzeniu szablonu usługi Azure Resource Manager w artykule Szybki start [Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu portalu Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas, przez który dysk testowy pozostanie aktywny w ciągu kilku godzin. Dysk testowy kończy się automatycznie po zakończeniu tego okresu. Czas trwania ten może być ustawiony tylko przez liczbę godzin (na przykład "2" godzin; "1.5" jest nieważne).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Konfiguracja techniczna dla jazdy testowej Dynamics 365

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego przez hosting i utrzymywanie inicjowania obsługi administracyjnej i wdrażania usługi przy użyciu tego typu dysku testowego. Konfiguracja dla tego typu hostowanego dysku testowego jest taka sama, niezależnie od tego, czy dysk testowy jest przeznaczony dla odbiorców Business Central, Customer Engagement lub Operations.

- **Maksymalna liczba równoczesnych dysków testowych** (wymagana): ustaw maksymalną liczbę klientów, którzy mogą korzystać z dysku testowego w tym samym czasie. Każdy równoczesnych użytkowników będzie używać licencji Dynamics 365, gdy dysk testowy jest aktywny, więc należy upewnić się, że masz wystarczająco dużo licencji dostępnych do obsługi maksymalnego limitu ustawionego. Zalecana wartość 3-5.

- **Czas trwania jazdy próbnej** (wymagany): Wprowadź czas, przez który testowa jazda będzie aktywna, definiując liczbę godzin. Po tych wielu godzinach sesja zostanie zakończona i nie będzie już zużywać jednej z licencji. Zalecamy wartość 2-24 godzin w zależności od złożoności oferty. Czas trwania ten może być ustawiony tylko przez liczbę godzin (na przykład "2" godzin; "1.5" jest nieważne). Użytkownik może zażądać nowej sesji, jeśli zabraknie im czasu i chcesz ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagany): adres URL, pod którym klient rozpocznie jazdę testowa. Zazwyczaj adres URL wystąpienia Dynamics 365 z zainstalowaną aplikacją `https://testdrive.crm.dynamics.com`z zainstalowanymi przykładowymi danymi (na przykład).

- **Adres URL interfejsu API sieci Web wystąpienia** (wymagane): pobierz adres URL interfejsu API sieci Web dla wystąpienia dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do **ustawień** \&gt; **Dostosowanie** \&gt; **Zasoby** \&dla deweloperów gt; **Interfejs API sieci Web wystąpienia (adres URL katalogu głównego usługi),** skopiuj adres URL znaleziony tutaj (na przykład `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Nazwa roli** (wymagana): Podaj nazwę roli zabezpieczeń zdefiniowaną na niestandardowej dysku testowym Dynamics 365. Ta nazwa roli zabezpieczeń zostanie przypisana do użytkownika podczas jazdy testowej (na przykład roli test-drive).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Konfiguracja techniczna dla aplikacji logiki jazdy testowej

Wszystkie produkty niestandardowe należy użyć tego typu szablonu wdrażania dysku testowego, który obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź [witrynę Operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [customer engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w usłudze GitHub.

- **Region** (wymagana lista rozwijana z jednym wyborem): obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma żadnych zasobów niestandardowych przechowywanych w określonym regionie, upewnij się, że region jest zaznaczony w tym miejscu. Najlepszym sposobem, aby to zrobić, jest pełne wdrożenie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa poprawnie przed dokonaniem tego wyboru.

- **Maksymalna liczba równoczesnych dysków testowych** (wymagana): ustaw maksymalną liczbę klientów, którzy mogą korzystać z dysku testowego w tym samym czasie. Te dyski testowe są już wdrożone, dzięki czemu klienci mogą natychmiast uzyskać do nich dostęp bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas, przez który dysk testowy pozostanie aktywny w ciągu kilku godzin. Po zakończeniu tego okresu dysk testowy kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagana): wprowadź nazwę grupy zasobów platformy [Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagane): Wprowadź nazwę aplikacji logiki, która przypisuje dysku testowego do użytkownika. Ta aplikacja logiki musi być zapisany w grupie zasobów platformy Azure powyżej.

- **Nazwa aplikacji logiki deprovision** (wymagane): Wprowadź nazwę aplikacji logiki, która usuwa aprowizji dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi być zapisany w grupie zasobów platformy Azure powyżej.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Konfiguracja techniczna nie jest wymagana dla dysków testowych usługi Power BI

Produkty, które chcą zademonstrować interaktywną wizualizację usługi Power BI, mogą używać wbudowanego łącza do udostępniania niestandardowego pulpitu nawigacyjnego jako dysku testowego, bez konieczności dalszej konfiguracji technicznej. Dowiedz się więcej o konfigurowaniu aplikacji szablonów[usługi Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć dysk testowy w Twoim imieniu, utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure (nie wymaganą dla dysków testowych usługi Power BI).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla usługi Azure Resource Manager i aplikacje logiki): wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure w celu raportowania użycia zasobów i rozliczeń. Zaleca się, aby rozważyć [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) do użycia w przypadku jazdy testowych, jeśli nie masz jeszcze. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [witryny Azure portal](https://portal.azure.com/) i przechodząc do karty **Subskrypcje** menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (na przykład "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wprowadź identyfikator [dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **polecenie Właściwości**, a następnie poszukaj numeru **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy organizacji przy użyciu [https://www.whatismytenantid.com](https://www.whatismytenantid.com)adresu URL nazwy domeny pod adresem: .

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla usługi Dynamic 365): wprowadź nazwę usługi Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [witryny Azure portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona pod nazwą konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagany): wprowadź identyfikator aplikacji usługi Azure Active Directory (AD). [application ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **pozycję Rejestracje aplikacji**, a następnie poszukaj numeru **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).

- **Klucz tajny klienta aplikacji usługi Azure AD** (wymagany): wprowadź klucz tajny [klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD . Aby znaleźć tę wartość, zaloguj się do [witryny Azure portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz aplikację dysku testowego. Następnie wybierz **pozycję Certyfikaty i wpisy tajne**, wybierz pozycję Nowy klucz tajny **klienta**, wprowadź opis, wybierz pozycję **Nigdy w** obszarze **Wygasa**, a następnie wybierz pozycję **Dodaj**. Pamiętaj, aby skopiować wartość. Nie odchodź od strony przed skopiowaniem wartości lub nie będziesz mieć dostępu do wartości).

### <a name="test-drive-marketplace-listings"></a>Listy z rynku dysku testowego

Opcja **Lista w Marketplace** znajdująca się na karcie Dysk **testowy** wyświetla języki, w których jest dostępna jazda próbna. Obecnie dostępna jest **w języku angielskim (Stany Zjednoczone).** Wybierz nazwę języka, aby wprowadzić informacje opisujące środowisko jazdy próbne.

- **Opis** (wymagane): Opisz jazdę próbną, co zostanie zademonstrowane, cele, z jakim użytkownik może eksperymentować, funkcje do zbadania i wszelkie istotne informacje, które pomogą użytkownikowi ustalić, czy chcesz uzyskać ofertę. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla usług Azure Resource Manager i logic dysków testowych): Wyjaśnij, co klient musi wiedzieć, aby uzyskać dostęp do tej dysku testowego i korzystać z niego. Zapoznaj się ze scenariuszem korzystania z oferty i dokładnie tym, co klient powinien wiedzieć, aby uzyskać dostęp do funkcji podczas jazdy próbnego. W tym polu można wprowadzić maksymalnie 10 000 znaków tekstu.

- **Instrukcja obsługi** (wymagana): szczegółowy przewodnik po doświadczeniu jazdy próbnej. Instrukcja obsługi powinna obejmować dokładnie to, co chcesz, aby klient uzyskał z powodu wystąpienia jazdy próbnej i służyć jako punkt odniesienia dla wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i być nazwany (maks. 255 znaków) po przesłaniu.

- **Filmy: dodaj filmy** (opcjonalnie): filmy można przesyłać do YouTube lub Vimeo i odwoływać się tutaj za pomocą linku i obrazu miniatur (533 x 324 piksele), aby klient mógł wyświetlić informacje, które pomogą mu lepiej zrozumieć jazdę próbną, w tym jak skutecznie korzystać z funkcji oferty i zrozumieć scenariusze, które podkreślają ich zalety.
  - **Nazwa** (wymagana)
  - **Adres URL (tylko YouTube lub Vimeo)** (wymagany)
  - **Miniatura (533 x 324px):** Plik obrazu musi być w formacie PNG.

## <a name="supplemental-content"></a>Treść uzupełniająca

Ta strona umożliwia dostarczenie dodatkowych informacji o ofercie, które pomogą nam zweryfikować twoją ofertę. Te informacje nie są wyświetlane klientom ani publikowane na rynku.

### <a name="key-usage-scenario"></a>Kluczowy scenariusz użycia

Konieczne jest przesłanie pliku .pdf zawierającego listę kluczowych scenariuszy użycia oferty wymienionych w dokumencie (format pdf). Wszystkie wymienione tutaj scenariusze mogą zostać zweryfikowane przez nasz zespół sprawdzania poprawności, zanim zatwierdzimy Twoją ofertę dla rynku.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po wypełnieniu wszystkich wymaganych sekcji oferty wybierz **pozycję publikuj** w prawym górnym rogu portalu. Zostaniesz przekierowany do strony **Recenzja i publikowanie.**

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia każdej sekcji oferty.
    - *Nie rozpoczęto* - oznacza, że sekcja nie została dotknięta i powinna zostać ukończona.
    - *Niekompletne* - oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga więcej informacji, które mają być dostarczone. Wróć do sekcji i zaktualizuj ją.
    - *Complete* - oznacza, że sekcja jest kompletna, wszystkie wymagane dane zostały dostarczone i nie ma żadnych błędów. Wszystkie sekcje oferty muszą być w pełnym stanie, zanim będzie można złożyć ofertę.
- W sekcji **Uwagi dotyczące certyfikacji** podaj zespołowi certyfikacyjnemu instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie testowana, a także wszelkie dodatkowe uwagi przydatne do zrozumienia aplikacji.
- Prześlij ofertę do publikacji, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Wróć do Centrum partnerów i wybierz **opcję Przejdź na żywo,** aby oferta była publikowana publicznie (lub jeśli jest to oferta prywatna, dla odbiorców prywatnych).

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
