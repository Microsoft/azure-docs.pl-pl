---
title: Planowanie oferty aplikacji platformy Azure dla komercyjnego portalu Marketplace
description: Dowiedz się, jak planować nową ofertę aplikacji platformy Azure w celu wystawiania lub sprzedawania w portalu Azure Marketplace albo za pośrednictwem programu Cloud Solution Provider (CSP) przy użyciu witryny komercyjne Portal Marketplace w centrum partnerskim firmy Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 45bc3aaf5217c626e2593b4eda861eaace2a8be2
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620413"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Planowanie oferty aplikacji platformy Azure dla komercyjnego portalu Marketplace

W tym artykule opisano różne opcje i wymagania dotyczące publikowania oferty aplikacji platformy Azure w portalu komercyjnym firmy Microsoft.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Projektowanie, kompilowanie i testowanie ofert aplikacji platformy Azure wymaga znajomości technicznej platformy Azure i technologii używanych do tworzenia oferty. Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

- Podstawowe informacje na temat [usług platformy Azure](https://azure.microsoft.com/services/).
- [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/).
- Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking#networking).
- Wiedza [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Praktyczna wiedza o formacie [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Dokumentacja techniczna i zasoby

Przejrzyj następujące zasoby w miarę planowania oferty aplikacji platformy Azure dla komercyjnej witryny Marketplace.

- [Informacje o szablonach Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- Przewodniki Szybki start:
    - [Szablony przewodników Szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/)
    - [Przewodnik po najlepszych rozwiązaniach dotyczących szablonów platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Publikowanie definicji aplikacji](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Wdrażanie aplikacji katalogu usług](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Samouczki:
    - [Tworzenie plików definicji](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Badan
    - [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Rozwiązania aplikacji zarządzanych](../azure-resource-manager/managed-applications/sample-projects.md)

[Tworzenie szablonów rozwiązań wideo i aplikacji zarządzanych dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) zapewnia kompleksowe wprowadzenie do typu oferty aplikacji platformy Azure:

- Jakie typy ofert są dostępne
- Jakie zasoby techniczne są wymagane
- Jak utworzyć szablon Azure Resource Manager
- Opracowywanie i testowanie interfejsu użytkownika aplikacji
- Jak opublikować ofertę aplikacji
- Proces przeglądu aplikacji

### <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz co najmniej jedno z następujących środowisk skryptów, aby ułatwić zarządzanie aplikacją platformy Azure:

- [Azure PowerShell](/powershell/azure/)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure)

Zalecamy dodanie następujących narzędzi do środowiska deweloperskiego:

- [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) z następującymi rozszerzeniami:
    - Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostępne narzędzia można sprawdzić na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/) . Jeśli używasz programu Visual Studio, zobacz [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Opcje ofert

Po opublikowaniu oferty opcje wyświetlania oferty są wyświetlane jako przycisk w lewym górnym rogu strony z listą ofert. Na przykład poniższy zrzut ekranu przedstawia stronę aukcji oferty w witrynie Azure Marketplace z przyciskiem _Pobierz teraz_ . Jeśli wybrano opcję zaoferowania dysku testowego, zostanie również wyświetlony przycisk _Testuj dysk_ .

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Ilustruje stronę listy w witrynie Azure Marketplace.":::

## <a name="test-drive"></a>Wersja testowa

Możesz włączyć test dla oferty aplikacji platformy Azure, która umożliwia klientom wypróbowanie oferty przed jej zakupem. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [co to jest dysk testowy?](what-is-test-drive.md) Aby uzyskać informacje o konfigurowaniu różnych rodzajów dysków testowych, zobacz temat [konfiguracja techniczna systemu testowego](test-drive-technical-configuration.md).

Możesz również przeczytać o [najlepszych rozwiązaniach dotyczących dysków testowych](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) i pobrać plik PDF z [omówieniem dysków testowych](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

> [!NOTE]
> Informacje, które użytkownik powinien zwrócić nawet w przypadku zaimplementowania skimmingBecause wszystkich aplikacji platformy Azure przy użyciu szablonu Azure Resource Manager jedynym typem dysku testowego dostępnym dla [aplikacji platformy Azure jest dysk testowy oparty na Azure Resource Manager](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Potencjalni klienci

Aby zbierać informacje o klientach, należy połączyć swoją ofertę z systemem zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i sklepem online, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Komercyjna witryna Marketplace obsługuje różne systemy CRM, a także opcję używania tabeli platformy Azure lub konfigurowania punktu końcowego HTTPS przy użyciu automatyzacji.

Połączenie programu CRM można dodać lub zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Kategorie i podkategorie

Możesz wybrać co najmniej jedną i maksymalnie dwie kategorie grupowania oferty w odpowiednie obszary wyszukiwania komercyjnego portalu Marketplace. Każdą kategorię podstawową i pomocniczą można wybrać do dwóch podkategorii. Aby uzyskać pełną listę kategorii i podkategorii, zobacz temat [najważniejsze wskazówki dotyczące oferty](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Umowy prawne

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt, którego możesz użyć dla ofert w portalu komercyjnym. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą tylko raz odczytywać i akceptować ją i nie muszą tworzyć niestandardowych warunków i postanowień.

Jeśli zdecydujesz się na korzystanie z kontraktu standardowego, możesz dodać postanowienia dotyczące uniwersalnej poprawki i maksymalnie 10 niestandardowych zmian do standardowej umowy. Możesz również użyć własnych warunków i postanowień zamiast standardowej umowy. Te szczegóły będą zarządzane na stronie **Właściwości** . Aby uzyskać szczegółowe informacje, zobacz [Standard Contract for Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie można użyć własnych niestandardowych warunków i postanowień. Jest to scenariusz "lub". Możesz zaoferować swoje rozwiązanie w ramach standardowej umowy lub własnych warunków i postanowień. Jeśli chcesz zmodyfikować warunki kontraktu standardowego, możesz to zrobić za pomocą standardowych poprawek kontraktu.

## <a name="offer-listing-details"></a>Szczegóły listy ofert

Gdy tworzysz nową ofertę aplikacji platformy Azure w centrum partnerskim, wprowadzisz tekst, obrazy, opcjonalne filmy wideo i inne szczegóły na stronie z listą ofert. Są to informacje, które użytkownicy zobaczą po znalezieniu oferty w portalu Azure Marketplace, jak pokazano w poniższym przykładzie.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Logo
2. Kategorie
3. Adres pomocy technicznej (link)
4. Warunki użytkowania
5. Adres zasad ochrony prywatności (link)
6. Nazwa oferty
7. Podsumowanie
8. Opis
9. Zrzuty ekranu/wideo

Poniższy zrzut ekranu przedstawia sposób wyświetlania informacji o ofercie w Azure Portal:

[![Ilustruje, w jaki sposób ta oferta pojawia się w Azure Portal.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Tytuł
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu

> [!NOTE]
> Zawartość oferty nie jest wymagana w języku angielskim, jeśli opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku [język inny niż angielski]".

Aby ułatwić szybkie tworzenie oferty, przygotuj niektóre z tych elementów przed czasem. Poniższe elementy są wymagane, o ile nie zaznaczono inaczej.

- **Nazwa**: Ta nazwa zostanie wyświetlona jako tytuł oferty w komercyjnej witrynie Marketplace. Nazwa może być znakiem towarowym. Nie może zawierać znaków emoji (chyba że są to symbole towarowe i praw autorskich) i muszą być ograniczone do 50 znaków.
- **Podsumowanie wyników wyszukiwania**: opisz przeznaczenie lub funkcję oferty jako pojedyncze zdanie, w postaci zwykłego tekstu bez podziałów wierszy, w 100 znaków lub mniej. To podsumowanie jest używane w komercyjnych wynikach wyszukiwania na liście w portalu Marketplace.
- **Krótki opis**: podaj do 256 znaków zwykłego tekstu. To podsumowanie zostanie wyświetlone na stronie szczegółów oferty.
- **Opis**: ten opis zostanie wyświetlony w temacie Omówienie aukcji portalu Azure Marketplace. Rozważ uwzględnienie propozycji wartości, najważniejszych korzyści, planowanego użytkownika, dowolnych z kategorii lub branżowych, możliwości zakupu w aplikacji, potrzeb klientów lub bólu, że oferta zawiera adresy, wszelkie wymagane ujawnienie oraz link, aby dowiedzieć się więcej.

    To pole tekstowe zawiera kontrolki edytora tekstu sformatowanego, których można użyć, aby dowiedzieć się więcej. Możesz również użyć tagów HTML do sformatowania opisu. W tym polu można wprowadzić do 3 000 znaków tekstu, w tym znaczniki HTML i spacje. Aby uzyskać dodatkowe wskazówki, zobacz artykuł [Napisz doskonały opis aplikacji](/windows/uwp/publish/write-a-great-app-description) i [tagi HTML obsługiwane w opisach oferty komercyjnej witryny Marketplace](supported-html-tags.md).

- **Wyszukaj słowa kluczowe** (opcjonalnie): Podaj do trzech słów kluczowych wyszukiwania, których klienci mogą używać w celu znalezienia oferty w sklepie online. Aby uzyskać najlepsze wyniki, należy również użyć tych słów kluczowych w opisie. Nie trzeba dołączać **nazwy** oferty i **opisu**. Ten tekst jest automatycznie uwzględniany w wyszukiwaniu.
- **Link zasad ochrony prywatności**: adres URL firmowych zasad zachowania poufności informacji. Należy podać prawidłowe zasady ochrony prywatności i są one odpowiedzialne za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności.
- **Przydatne linki** (opcjonalnie): możesz udostępnić linki do różnych zasobów użytkownikom oferty. Na przykład fora, często zadawane pytania i informacje o wersji.
- **Informacje kontaktowe**: należy wyznaczyć następujące kontakty z Twojej organizacji:
  - **Kontakt z pomocą techniczną**: Podaj nazwę, numer telefonu i adres E-mail partnerów firmy Microsoft do użycia, gdy klienci otworzą bilety. Należy również uwzględnić adres URL witryny sieci Web pomocy technicznej.
  - **Kontakt inżynieryjny**: Podaj nazwę, numer telefonu i adres E-mail firmy Microsoft do użycia bezpośrednio w przypadku wystąpienia problemów z ofertą. Te informacje kontaktowe nie są wymienione na rynku komercyjnym.
  - **Kontakt programu CSP** (opcjonalnie): Podaj nazwę, numer telefonu i adres e-mail, Jeśli zrezygnujesz z programu w programie Cloud Solution Provider (CSP), dzięki czemu ci partnerzy mogą skontaktować się z Tobą w celu uzyskania odpowiedzi na pytania. Możesz również dodać adres URL do materiałów marketingowych.
- **Multimedia — logo**: podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.
  - Duże (od 216 x 216 do 350 x 350 px, wymagane)
  - Średnia (90 x 90 pikseli, opcjonalnie)
  - Mały (48 x 48 pikseli, opcjonalnie)

  Te logo są używane w różnych miejscach w sklepach online:
  - Małe logo pojawia się w wynikach wyszukiwania w portalu Azure Marketplace.
  - Średnie logo pojawia się podczas tworzenia nowego zasobu w Microsoft Azure.
  - Duże logo pojawia się na stronie aukcji oferty w witrynie Azure Marketplace.

  Postępuj zgodnie z poniższymi wskazówkami dotyczącymi logo:

  - Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
  - Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe.
  - Jeśli używasz przezroczystego tła, upewnij się, że logo i tekst nie są białe, czarne ani niebieskie.
  - Wygląd i zachowanie logo powinny być płaskie i unikać gradientów w logo lub tło. Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki. Nierozmyte obrazy spowodują odrzucenie zgłoszenia.
  - Upewnij się, że logo nie jest rozciągnięte.

- **Zrzuty ekranu** (opcjonalnie): zalecamy dodanie zrzutów ekranu, które pokazują, jak działa Twoja oferta. Można dodać maksymalnie pięć zrzutów ekranu z następującymi wymaganiami, które pokazują, jak działa Twoja oferta:
  - 1280 x 720 pikseli
  - plik PNG
  - Musi zawierać podpis
- **Multimedia — wideo** (opcjonalnie): można dodać do pięciu filmów wideo z następującymi wymaganiami, które demonstrują Twoją ofertę:
  - Nazwa
  - Adres URL: musi być hostowany tylko w serwisie YouTube lub Vimeo.
  - Miniatura: plik 1280 x 720. png

> [!NOTE]
> Twoja oferta musi być zgodna z ogólnymi [komercyjnymi zasadami certyfikacji portalu Marketplace](/legal/marketplace/certification-policies#100-general.md) publikowanymi na komercyjnym rynku.

## <a name="preview-audience"></a>Podgląd odbiorców

Odbiorca w wersji zapoznawczej może uzyskać dostęp do oferty przed opublikowaniem jej w sklepach online w celu przetestowania kompleksowej funkcjonalności przed opublikowaniem jej na żywo.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od planu prywatnego. Plan prywatny jest dostępny tylko dla określonych odbiorców. Pozwala to na negocjowanie planu niestandardowego z konkretnymi klientami.

Możesz zdefiniować odbiorców wersji zapoznawczej przy użyciu identyfikatorów subskrypcji platformy Azure, a także opcjonalny opis dla każdego z nich. Żadne z tych pól nie może być widoczne dla klientów.

## <a name="technical-configuration"></a>Konfiguracja techniczna

W przypadku zarządzanych aplikacji, które emitują zdarzenia przy użyciu [interfejsów API usługi pomiaru Marketplace](partner-center-portal/marketplace-metering-service-apis.md), należy podać tożsamość, która będzie używana przez usługę podczas emitowania zdarzeń pomiaru.

Ta konfiguracja jest wymagana, jeśli chcesz użyć [zdarzenia użycia](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event)w usłudze Batch. Jeśli chcesz przesłać [zdarzenie użycia](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event), możesz również użyć [usługi metadanych wystąpienia](../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać [token okaziciela sieci Web JSON (JWT)](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

- **Azure Active Directory identyfikator dzierżawy** (wymagane): wewnątrz Azure Portal należy [utworzyć aplikację Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) , aby umożliwić nam zweryfikowanie połączenia między naszymi dwiema usługami za pomocą komunikacji uwierzytelnionej. Aby znaleźć [Identyfikator dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) aplikacji Azure Active Directory (Azure AD), w bloku [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Active Directory. W kolumnie **Nazwa wyświetlana** wybierz aplikację. Następnie wyszukaj **Właściwości**, a następnie dla **identyfikatora katalogu (dzierżawy)** (na przykład `50c464d3-4930-494c-963c-1e951d15360e` ).
- **Identyfikator aplikacji Azure Active Directory** (wymagane): wymagany jest również [Identyfikator aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) i klucz uwierzytelniania. Aby znaleźć identyfikator aplikacji, przejdź do bloku [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Active Directory. W kolumnie **Nazwa wyświetlana** wybierz aplikację, a następnie wyszukaj **Identyfikator aplikacji (** na przykład `50c464d3-4930-494c-963c-1e951d15360e` ). Aby znaleźć klucz uwierzytelniania, przejdź do pozycji **Ustawienia** i wybierz pozycję **klucze**. Należy podać opis i czas trwania, a następnie podać wartość liczbową.

> [!NOTE]
> Identyfikator aplikacji platformy Azure zostanie skojarzony z IDENTYFIKATORem wydawcy i będzie można go ponownie używać w ramach tego konta wydawcy.

## <a name="additional-sales-opportunities"></a>Dodatkowe możliwości sprzedaży

Możesz zrezygnować z obsługi kanałów marketingowych i sprzedaży obsługiwanych przez firmę Microsoft. Podczas tworzenia oferty w centrum partnerskim na końcu procesu zobaczysz dwie karty:

- Odsprzedaż **za pomocą dostawców CSP**: Użyj tej opcji, aby zezwolić partnerom dostawcy rozwiązań Microsoft Cloud (CSP) na odsprzedaż Twojego rozwiązania w ramach powiązanej oferty. Aby uzyskać więcej informacji, zobacz [program Cloud Solution Provider](./cloud-solution-providers.md) .
- **Sprzedawanie z firmą Microsoft**: Ta opcja umożliwia zespołom ds. sprzedaży firmy Microsoft rozpatrywanie odpowiednich rozwiązań dla adresów IP podczas oceny potrzeb klientów. Aby uzyskać szczegółowe informacje na temat przygotowywania oferty do oceny, zobacz [opcję współsprzedaży w portalu komercyjnym](commercial-marketplace-co-sell.md). Aby uzyskać szczegółowe informacje o wymaganiach dotyczących współsprzedawana adresów IP, zobacz [wymagania dotyczące stanu współsprzedażu](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status). Aby uzyskać więcej informacji na temat marketingu oferty za pomocą kanałów partnerów CSP firmy Microsoft, zobacz [dostawcy rozwiązań w chmurze](cloud-solution-providers.md).

Aby dowiedzieć się więcej, zobacz [rozwijanie działalności w chmurze za pomocą witryny Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Plany

Oferty aplikacji platformy Azure wymagają co najmniej jednego planu. Plan definiuje zakres i limity rozwiązania oraz powiązane ceny, jeśli ma zastosowanie. Możesz utworzyć wiele planów dla oferty, aby dać klientom różne opcje techniczne i cenowe.

Ogólne wskazówki dotyczące planów, w tym modeli cenowych i planów prywatnych, można znaleźć w temacie [plany i Cennik dla ofert komercyjnych w portalu Marketplace](plans-pricing.md). W poniższych sekcjach omówiono dodatkowe informacje specyficzne dla planów aplikacji platformy Azure.

### <a name="types-of-plans"></a>Typy planów

Istnieją dwa rodzaje planów aplikacji platformy Azure: _szablon rozwiązania_ i _aplikacja zarządzana_. Oba typy planów obsługują Automatyzowanie wdrażania i konfigurowania rozwiązania poza jedną maszyną wirtualną. Można zautomatyzować proces udostępniania wielu zasobów, w tym maszyn wirtualnych, sieci i zasobów magazynu, aby zapewnić złożone rozwiązania, takie jak rozwiązania IaaS. Oba typy planów mogą korzystać z wielu różnych rodzajów zasobów platformy Azure, w tym między innymi maszyn wirtualnych.

- Plany **szablonów rozwiązań** to jeden z głównych sposobów publikowania rozwiązania na rynku komercyjnym. Plany szablonów rozwiązań nie są transakcyjne w komercyjnej witrynie Marketplace, ale mogą służyć do wdrażania płatnych ofert maszyn wirtualnych, które są rozliczane za pomocą komercyjnej witryny Marketplace. Użyj typu planu szablonu rozwiązania, gdy klient będzie zarządzać rozwiązaniem, a transakcje są rozliczane za pomocą innego planu. Aby uzyskać więcej informacji na temat tworzenia szablonów rozwiązań, zobacz [co to jest Azure Resource Manager?](../azure-resource-manager/management/overview.md)
- Plany **aplikacji zarządzanych** umożliwiają łatwe tworzenie i dostarczanie w pełni zarządzanych aplikacji gotowe dla klientów. Mają one te same możliwości co plany szablonów rozwiązań, z niektórymi różnicami między kluczami:
    - Zasoby są wdrażane w grupie zasobów i są zarządzane przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. 
    - Jako wydawca należy określić koszt dla ciągłej pomocy technicznej rozwiązania, a transakcje są obsługiwane za pomocą komercyjnej witryny Marketplace.
 
    Użyj typu planu aplikacji zarządzanej, gdy użytkownik lub klient wymaga, aby rozwiązanie było zarządzane przez partnera, lub wdrożyć rozwiązanie oparte na subskrypcji. Aby uzyskać więcej informacji o zaletach i typach zarządzanych aplikacji, zobacz [Omówienie usługi Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Następne kroki

- Aby zaplanować szablon rozwiązania, zobacz [Planowanie szablonu rozwiązania dla oferty aplikacji platformy Azure](plan-azure-app-solution-template.md).
- Aby zaplanować aplikację zarządzaną platformy Azure, zobacz [planowanie aplikacji zarządzanej przez platformę Azure dla oferty aplikacji platformy Azure](plan-azure-app-managed-app.md).