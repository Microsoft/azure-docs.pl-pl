---
title: Tworzenie nowej oferty Dynamics 365 Business Central w komercyjnym rynku firmy Microsoft
description: Jak utworzyć nową ofertę Dynamics 365 Business Central do wystawiania lub sprzedawania w portalu Azure Marketplace, Microsoft AppSource lub za pośrednictwem programu Dostawcy rozwiązań w chmurze (CSP) przy użyciu portalu komercyjnego portalu marketplace w Centrum partnerów firmy Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 10fa16767061506eccf212c182e738246dc9fe7b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867224"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Tworzenie nowej oferty Dynamics 365 Business Central

W tym artykule wyjaśniono, jak utworzyć nową ofertę Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) to system planowania zasobów przedsiębiorstwa (ERP), który obsługuje szeroki zakres procesów biznesowych, w tym finanse, operacje, łańcuch dostaw, CRM oraz zarządzanie projektami i handel elektroniczny. Pakiety premium obsługują również klasyczny model wdrażania i produkcję. Wszystkie oferty dynamics 365 Business Central muszą przejść przez nasz proces certyfikacji.

Aby rozpocząć tworzenie ofert Dynamics 365 Business Central, upewnij się, że najpierw [utwórz konto Centrum partnerów](./create-account.md) i otwórz [pulpit nawigacyjny portalu komercyjnego w portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z zaznaczoną stroną **Przegląd.**

![Pulpit nawigacyjny portalu Marketplace w Centrum partnerów](./media/new-offer-overview.png)

>[!Note]
> Po opublikowaniu oferty zmiany w ofercie złożonej w Centrum partnerskim zostaną zaktualizowane dopiero po ponownym opublikowaniu w systemie i na frontach sklepu. Po dokonaniu zmian należy przesłać ofertę do publikacji.

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Wybierz przycisk **+ Nowa oferta,** a następnie wybierz pozycję menu **Dynamics 365 Business Central.** Zostanie wyświetlone okno dialogowe **Nowa oferta.**

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

- **Identyfikator oferty:** Unikatowy identyfikator dla każdej oferty na twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL oferty portalu Marketplace i szablonach usługi Azure Resource Manager (jeśli dotyczy). Identyfikator oferty musi być małe litery alfanumeryczne znaki (w tym łączniki i podkreślenia, ale nie odstępy), ograniczone do 50 znaków i nie można zmienić po **wybraniu opcji Utwórz**.  Na przykład, jeśli wprowadzisz *test-offer-1* tutaj, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres URL oferty będzie .

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

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji!

## <a name="properties"></a>Właściwości

Strona **Właściwości** umożliwia zdefiniowanie kategorii i branż używanych do grupowania oferty w portalu Marketplace, wersji aplikacji i umów prawnych wspierających ofertę. Wybierz **pozycję Zapisz** po zakończeniu tej strony.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie trzy kategorie, które zostaną wykorzystane do umieszczenia oferty w odpowiednich obszarach wyszukiwania w rynku. Pamiętaj, aby sprawdzić, w jaki sposób Twoja oferta obsługuje te kategorie w opisie oferty. 

### <a name="industry"></a>Branża

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji oferty. Klienci zobaczą tę wersję wymienioną na stronie szczegółów oferty.

### <a name="terms-and-conditions"></a>Warunki i postanowienia

Podaj własne warunki prawne w polu **Warunki.** Możesz też podać adres URL, pod którym można znaleźć warunki. Klienci będą musieli zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

## <a name="offer-listing"></a>Oferta aukcji

Strona Oferta aukcji to miejsce, w którym definiujesz szczegóły oferty (takie jak imię i nazwisko, opis, obrazy itp.).

> [!NOTE]
> Możesz podać szczegóły oferty tylko w jednym języku. Nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Dopuszczalne jest również podanie *adresu URL łącza Pomocy,* aby oferować treści w języku innym niż ten używany w treści aukcji Oferty.

### <a name="name"></a>Nazwa

Nazwa, którą wpiszesz w tym miejscu, będzie wyświetlana klientom jako tytuł twojej oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu Oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Nazwa ta może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich). Nazwa nie może zawierać więcej niż 50 znaków i nie może zawierać żadnych emotikonów.

### <a name="short-description"></a>Krótki opis

Podaj krótki opis oferty (do 100 znaków), która może być używana w wynikach wyszukiwania w marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis swojej oferty (do 3000 znaków). Ten opis będzie wyświetlany klientom w przeglądzie aukcji w portalu marketplace. Dołącz propozycję wartości oferty, kluczowe korzyści, kategorie i/lub stowarzyszenia branżowe, możliwości zakupu w aplikacji i wszelkie wymagane informacje. 

Kilka wskazówek dotyczących pisania opisu:  

- Wyraźnie opisz propozycję wartości swojej oferty w pierwszych kilku zdaniach opisu. Dołącz do swojej propozycji wartości następujące elementy:
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

### <a name="helpprivacy-urls"></a>Adresy URL pomocy/prywatności

W tej sekcji znajdziesz łącza ułatwiające klientom zapoznanie się z ofertą.

#### <a name="help-link"></a>Link pomocy

Wpisz adres URL, pod którym klienci mogą dowiedzieć się więcej o Twojej ofercie. **Link Pomocy** nie może być taki sam jak adres URL **pomocy technicznej** (wyjaśniono poniżej).

#### <a name="privacy-policy-link"></a>Link do polityki prywatności

Wprowadź adres URL do zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i regulacjami dotyczącymi prywatności oraz za zapewnienie ważnej polityki prywatności.

### <a name="contact-information"></a>Informacje kontaktowe

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje nie są wyświetlane klientom, ale będą dostępne dla firmy Microsoft i mogą być dostarczane partnerom CSP.

W sekcji **Kontakt pomocy technicznej** należy również podać adres URL pomocy **technicznej,** w którym partnerzy CSP mogą znaleźć pomoc techniczną dla Twojej oferty. Adres URL pomocy technicznej nie może być taki sam jak **link Pomocy**.

### <a name="supporting-documents"></a>Dokumenty uzupełniające

Podaj tutaj co najmniej jeden (i maksymalnie trzy) powiązane dokumenty marketingowe, takie jak białe księgi, broszury, listy kontrolne lub prezentacje. Dokumenty te muszą być w formacie .pdf.

### <a name="marketplace-images"></a>Obrazy z Marketplace

W tej sekcji możesz podać logo i obrazy, które będą używane podczas wyświetlania oferty klientowi. Wszystkie obrazy muszą być w formacie png. Prześlij logo oferty w dwóch rozmiarach: **Mały (48 x 48)** i **Duży (216 x 216)**.

>[!Note]
>Jeśli masz problem z przekazywaniem plików, upewnij się, https://upload.xboxlive.com że sieć lokalna nie blokuje usługi używanej przez Centrum partnerów.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu, które pokazują, jak działa Twoja oferta. Wymagane są co najmniej trzy zrzuty ekranu i można dodać maksymalnie pięć. Wszystkie zrzuty ekranu muszą mieć rozmiary 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów, które zademonstrują Twoją ofertę. Te filmy powinny być hostowane na YouTube i / lub Vimeo. Dla każdego z nich wprowadź nazwę filmu, jego adres URL i miniaturę filmu (1280 x 720 pikseli)

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

- [Najważniejsze wskazówki dotyczące ofert w marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Dostępność

Na stronie **Dostępność** znajdziesz opcje dotyczące tego, gdzie i jak udostępnić ofertę.

### <a name="markets"></a>Rynków

W tej sekcji można określić rynki, na których oferta powinna być dostępna. Aby to zrobić, wybierz **pozycję Edytuj rynki**, które wyświetli okno **podręczne Wybór rynku.**

Domyślnie nie wybrano żadnych rynków, ale musisz wybrać co najmniej jeden rynek, aby opublikować swoją ofertę. Kliknij **wybierz wszystkie,** aby udostępnić swoją ofertę na każdym możliwym rynku, lub wybierz konkretne rynki, które chcesz dodać. Po zakończeniu wybierz pozycję **Zapisz**.

Twoje wybory tutaj dotyczą tylko nowych nabytków; jeśli ktoś ma już Twoją aplikację na określonym rynku, a później usuniesz ten rynek, osoby, które już mają ofertę na tym rynku, mogą nadal z niej korzystać, ale żaden nowy klient na tym rynku nie będzie mógł uzyskać Twojej oferty.

> [!IMPORTANT]
> Twoim obowiązkiem jest spełnienie lokalnych wymogów prawnych, nawet jeśli te wymagania nie są wymienione tutaj lub w Centrum partnerów.

Pamiętaj, że nawet jeśli wybierzesz wszystkie rynki, lokalne przepisy, ograniczenia lub inne czynniki mogą uniemożliwić wyświetlenia niektórych ofert w niektórych krajach i regionach.

### <a name="preview-audience"></a>Podgląd odbiorców

Zanim opublikujesz ofertę na żywo w szerszej ofercie rynkowej, musisz najpierw udostępnić ją ograniczonej **ofercie podglądu.** W tym miejscu należy wprowadzić **klawisz Ukryj** (dowolny ciąg przy użyciu tylko małych liter i/lub cyfr). Członkowie grupy odbiorców w wersji zapoznawczej mogą używać tego klucza ukrywania jako tokenu, aby wyświetlić podgląd oferty w portalu marketplace.

Następnie, gdy wszystko będzie gotowe udostępnić ofertę i usuniesz ograniczenie podglądu, musisz usunąć **klucz Ukryj** i opublikować go ponownie.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Strona **konfiguracji technicznej** definiuje szczegóły techniczne używane do łączenia się z ofertą. To połączenie umożliwia nam udostępnienie oferty klientowi końcowemu, jeśli zdecyduje się ją nabyć.

### <a name="package-type"></a>Typ pakietu

Wybierz opcję, która ma zastosowanie do oferty:

- **Dodaj:** Aplikacja dodatkowa rozszerza środowisko i istniejące funkcje Dynamics 365 Business Central. Aby uzyskać więcej informacji, zobacz [Aplikacje dodatkowe](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Połącz**: Aplikacji Connect można używać w scenariuszu, w którym musi zostać ustanowione połączenie typu punkt-punkt między Dynamics 365 Business Central a rozwiązaniem lub usługą innej firmy. Aby uzyskać więcej informacji, zobacz [Połącz](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Przekazywanie plików

Jeśli wybierzesz **Dodaj powyżej,** gdzie przekażesz plik pakietu oferty wraz z plikami pakietów dla każdego rozszerzenia, na którym ma zależności.

#### <a name="extensions-package-file"></a>Plik pakietu rozszerzeń

Przekaż plik pakietu rozszerzenia (.app) dla swojej oferty.

#### <a name="library-package-file"></a>Plik pakietu biblioteki

Wymagane, jeśli oferta musi być zainstalowana wraz z innym rozszerzeniem, które nie zostanie opublikowane na rynku. Jeśli tak, przekaż swój plik .app tutaj.

#### <a name="dependency-package-file"></a>Plik pakietu zależności

Wymagane, jeśli oferta musi być zainstalowana wraz z innym rozszerzeniem, które zostało już opublikowane na rynku. Jeśli tak, `.app` prześlij jego lub `.zip` plik tutaj.

### <a name="url-to-app-installation"></a>Adres URL do instalacji aplikacji

Jeśli wybrano **opcję Połącz** powyżej, podaj tutaj adres URL instalacji aplikacji. W przypadku usług połączonych, które nie wymagają instalacji, podaj adres URL strony docelowej usługi lub strony rejestracji.

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

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas, przez który dysk testowy pozostanie aktywny w ciągu kilku godzin. Dysk testowy kończy się automatycznie po zakończeniu tego okresu. Ten czas trwania może być ustawiony tylko przez liczbę godzin (na przykład "2" godzin, "1.5" jest nieprawidłowy).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Konfiguracja techniczna dla jazdy testowej Dynamics 365

Firma Microsoft może usunąć złożoność konfigurowania dysku testowego przez hosting i utrzymywanie inicjowania obsługi administracyjnej i wdrażania usługi przy użyciu tego typu dysku testowego. Konfiguracja dla tego typu hostowanego dysku testowego jest taka sama, niezależnie od tego, czy dysk testowy jest przeznaczony dla odbiorców Business Central, Customer Engagement lub Operations.

- **Maksymalna liczba równoczesnych dysków testowych** (wymagana): ustaw maksymalną liczbę klientów, którzy mogą korzystać z dysku testowego w tym samym czasie. Każdy równoczesnych użytkowników będzie używać licencji Dynamics 365, gdy dysk testowy jest aktywny, więc należy upewnić się, że masz wystarczająco dużo licencji dostępnych do obsługi maksymalnego limitu ustawionego. Zalecana wartość 3-5.

- **Czas trwania jazdy próbnej** (wymagany): Wprowadź czas, przez który testowa jazda będzie aktywna, definiując liczbę godzin. Po tych wielu godzinach sesja zostanie zakończona i nie będzie już zużywać jednej z licencji. Zalecamy wartość 2-24 godzin w zależności od złożoności oferty. Ten czas trwania może być ustawiony tylko przez liczbę godzin (na przykład "2" godzin, "1.5" jest nieprawidłowy).  Użytkownik może zażądać nowej sesji, jeśli zabraknie im czasu i chcesz ponownie uzyskać dostęp do dysku testowego.

- **Adres URL wystąpienia** (wymagany): adres URL, pod którym klient rozpocznie jazdę testowa. Zazwyczaj adres URL wystąpienia Dynamics 365 z zainstalowaną aplikacją `https://testdrive.crm.dynamics.com`z zainstalowanymi przykładowymi danymi (na przykład).

- **Adres URL interfejsu API sieci Web wystąpienia** (wymagane): pobierz adres URL interfejsu API sieci Web dla wystąpienia dynamics 365, logując się do konta usługi Microsoft 365 i przechodząc do **ustawień** \&gt; **Dostosowanie** \&gt; **Zasoby** \&dla deweloperów gt; **Interfejs API sieci Web wystąpienia (adres URL katalogu głównego usługi),** skopiuj adres URL znaleziony tutaj (na przykład `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Nazwa roli** (wymagana): Podaj nazwę roli zabezpieczeń zdefiniowaną na niestandardowej dysku testowym Dynamics 365, która zostanie przypisana do użytkownika podczas jazdy testowej (na przykład roli test-drive).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Konfiguracja techniczna dla aplikacji logiki jazdy testowej

Wszystkie produkty niestandardowe należy użyć tego typu szablonu wdrażania dysku testowego, który obejmuje wiele złożonych architektur rozwiązań. Aby uzyskać więcej informacji na temat konfigurowania dysków testowych aplikacji logiki, odwiedź [witrynę Operacje](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) i [customer engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) w usłudze GitHub.

- **Region** (wymagana lista rozwijana z jednym wyborem): obecnie istnieje 26 regionów obsługiwanych przez platformę Azure, w których można udostępnić dysk testowy. Zasoby aplikacji logiki zostaną wdrożone w wybranym regionie. Jeśli aplikacja logiki ma żadnych zasobów niestandardowych przechowywanych w określonym regionie, upewnij się, że region jest zaznaczony w tym miejscu. Najlepszym sposobem jest pełne wdrożenie aplikacji logiki lokalnie w ramach subskrypcji platformy Azure w portalu i sprawdzenie, czy działa poprawnie przed dokonaniem tego wyboru.

- **Maksymalna liczba równoczesnych dysków testowych** (wymagana): ustaw maksymalną liczbę klientów, którzy mogą korzystać z dysku testowego w tym samym czasie. Te dyski testowe są już wdrożone, dzięki czemu klienci mogą natychmiast uzyskać do nich dostęp bez oczekiwania na wdrożenie.

- **Czas trwania dysku testowego** (wymagane): Wprowadź czas, przez który dysk testowy pozostanie aktywny w ciągu kilku godzin. Po zakończeniu tego okresu dysk testowy kończy się automatycznie.

- **Nazwa grupy zasobów platformy Azure** (wymagana): wprowadź nazwę grupy zasobów platformy [Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) w której jest zapisywany dysk testowy aplikacji logiki.

- **Nazwa aplikacji logiki platformy Azure** (wymagane): Wprowadź nazwę aplikacji logiki, która przypisuje dysku testowego do użytkownika. Ta aplikacja logiki musi być zapisany w grupie zasobów platformy Azure powyżej.

- **Nazwa aplikacji logiki deprovision** (wymagane): Wprowadź nazwę aplikacji logiki, która usuwa aprowizji dysku testowego po zakończeniu klienta. Ta aplikacja logiki musi być zapisany w grupie zasobów platformy Azure powyżej.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Konfiguracja techniczna nie jest wymagana dla dysków testowych usługi Power BI

Produkty, które chcą zademonstrować interaktywną wizualizację usługi Power BI, mogą używać wbudowanego łącza do udostępniania niestandardowego pulpitu nawigacyjnego jako dysku testowego, bez konieczności dalszej konfiguracji technicznej. Dowiedz się więcej o konfigurowaniu aplikacji szablonów[usługi Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć jazdę testową w Twoim imieniu, utwórz i podaj oddzielną i unikatową subskrypcję platformy Azure. (Nie jest wymagane w przypadku dysków testowych usługi Power BI).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla usługi Azure Resource Manager i aplikacje logiki): wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure w celu raportowania użycia zasobów i rozliczeń. Zaleca się, aby rozważyć [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) do użycia w przypadku jazdy testowych, jeśli nie masz jeszcze. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [witryny Azure portal](https://portal.azure.com/) i przechodząc do karty **Subskrypcje** menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (na przykład "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wprowadź identyfikator [dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **Właściwości, a następnie poszukaj numeru **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy organizacji przy użyciu [https://www.whatismytenantid.com](https://www.whatismytenantid.com)adresu URL nazwy domeny pod adresem: .

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla usługi Dynamic 365): wprowadź nazwę usługi Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [witryny Azure portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona pod nazwą konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagany): wprowadź identyfikator aplikacji usługi Azure Active Directory (AD). [application ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **pozycję Rejestracje aplikacji**, a następnie poszukaj numeru **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).

- **Klucz tajny klienta aplikacji usługi Azure AD** (wymagany): wprowadź klucz tajny [klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD . Aby znaleźć tę wartość, zaloguj się do [witryny Azure portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz aplikację dysku testowego. Następnie wybierz **pozycję Certyfikaty i wpisy tajne**, wybierz pozycję Nowy klucz tajny **klienta**, wprowadź opis, wybierz pozycję **Nigdy w** obszarze **Wygasa**, a następnie wybierz pozycję **Dodaj**. Pamiętaj, aby skopiować wartość. (Nie odejdź od strony, zanim to zrobisz, w przeciwnym razie nie będziesz mieć dostępu do wartości).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji!

### <a name="test-drive-marketplace-listings"></a>Listy z rynku dysku testowego

Opcja **aukcji w Marketplace** znajdująca się na karcie Dysk **testowy** to miejsce, w którym definiujesz szczegóły dotyczące jazdy próbnej.

> [!NOTE]
> Szczegóły aukcji na dysku testowym można podać tylko w jednym języku. Nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Dopuszczalne jest również podanie *adresu URL łącza Pomocy,* aby oferować zawartość w języku innym niż ten używany w treści z dysku testowego.


- **Opis** (wymagane): Opisz jazdę próbną, co zostanie zademonstrowane, cele, z jakim użytkownik może eksperymentować, funkcje do zbadania i wszelkie istotne informacje, które pomogą użytkownikowi ustalić, czy chcesz uzyskać ofertę. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla usług Azure Resource Manager i logic dysków testowych): Wyjaśnij, co klient musi wiedzieć, aby uzyskać dostęp do tej dysku testowego i korzystać z niego. Zapoznaj się ze scenariuszem korzystania z oferty i dokładnie tym, co klient powinien wiedzieć, aby uzyskać dostęp do funkcji podczas jazdy próbnego. W tym polu można wprowadzić maksymalnie 10 000 znaków tekstu.

- **Instrukcja obsługi** (wymagana): szczegółowy przewodnik po doświadczeniu jazdy próbnej. Instrukcja obsługi powinna obejmować dokładnie to, co chcesz, aby klient uzyskał z powodu wystąpienia jazdy próbnej i służyć jako punkt odniesienia dla wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i być nazwany (maks. 255 znaków) po przesłaniu.

- **Filmy: dodaj filmy** (opcjonalnie): filmy można przesyłać do YouTube lub Vimeo i odwoływać się tutaj za pomocą linku i obrazu miniatur (533 x 324 piksele), aby klient mógł wyświetlić informacje, które pomogą mu lepiej zrozumieć jazdę próbną, w tym jak skutecznie korzystać z funkcji oferty i zrozumieć scenariusze, które podkreślają ich zalety.
  - **Nazwa** (wymagana)
  - **Adres URL (tylko YouTube lub Vimeo)** (wymagany)
  - **Miniatura (533 x 324 px):** Plik obrazu musi być w formacie PNG.

## <a name="supplemental-content"></a>Treść uzupełniająca

Ta strona umożliwia dostarczenie dodatkowych informacji o ofercie, które pomogą nam zweryfikować twoją ofertę. Te informacje nie są wyświetlane klientom ani publikowane na rynku.

### <a name="target-release"></a>Wydanie docelowe

Wskaż, która wersja programu Microsoft Dynamics Business Central jest docelowa rozwiązania: **Bieżąca,** **Następna główna**lub **Następna pomocnicza**. Te informacje pozwalają nam odpowiednio przetestować rozwiązanie.

### <a name="supported-editions"></a>Obsługiwane wersje

Jeśli twoja oferta wymaga wersji Premium systemu Microsoft Dynamics 365 Business Central, wybierz tylko **wersję Premium.** W przeciwnym razie wybierz opcję **Essentials** i **Premium**.

### <a name="key-usage-scenario"></a>Kluczowy scenariusz użycia

Należy przesłać `.pdf` plik zawierający listę kluczowych scenariuszy użycia oferty wymienionych w dokumencie (format pdf). Wszystkie wymienione tutaj scenariusze mogą zostać zweryfikowane przez nasz zespół sprawdzania poprawności, zanim zatwierdzimy Twoją ofertę dla rynku.

### <a name="app-tests-automation"></a>Automatyzacja testów aplikacji

Opcjonalnie można przesłać plik **automatyzacji testów aplikacji** tutaj (.app).

### <a name="test-accounts"></a>Konta testowe

Jeśli konto testowe jest potrzebne, aby nasz zespół certyfikujący prawidłowo zapoznał się z ofertą, prześlij plik .pdf, .doc lub .docx z informacjami o **kontach testowych.**

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po wypełnieniu wszystkich wymaganych sekcji oferty wybierz **pozycję publikuj** w prawym górnym rogu portalu. Zostaniesz przekierowany do strony **Recenzja i publikowanie.** 

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia każdej sekcji oferty.
    - *Nie rozpoczęto* - oznacza, że sekcja nie została dotknięta i musi zostać ukończona.
    - *Niekompletne* - oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga więcej informacji, które mają być dostarczone. Wróć do sekcji i zaktualizuj ją.
    - *Complete* - oznacza, że sekcja jest kompletna, wszystkie wymagane dane zostały dostarczone i nie ma żadnych błędów. Wszystkie sekcje oferty muszą być w pełnym stanie, zanim będzie można złożyć ofertę.
- W sekcji **Uwagi dotyczące certyfikacji** podaj zespołowi certyfikacyjnemu instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie testowana, a także wszelkie dodatkowe uwagi przydatne do zrozumienia aplikacji.
- Prześlij ofertę do publikacji, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail, gdy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Wróć do Centrum partnerskiego i wybierz **opcję Przejdź na żywo,** aby oferta opublikowała ofertę publicznie (lub prywatną ofertę dla odbiorców prywatnych).

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
