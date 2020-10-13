---
title: Tworzenie oferty aplikacji platformy Azure — Microsoft Commercial Marketplace
description: Zapoznaj się z instrukcjami i zagadnieniami dotyczącymi tworzenia nowej oferty aplikacji platformy Azure w portalu komercyjnej witryny Marketplace w centrum partnerskim. Możesz wyświetlić lub sprzedać swoją ofertę aplikacji platformy Azure w portalu Azure Marketplace lub za pomocą programu Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: fb3a3ab5339186d8fa4e347d9d13e66940457f8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710723"
---
# <a name="create-an-azure-application-offer"></a>Tworzenie oferty aplikacji platformy Azure

W tym artykule opisano kroki i zagadnienia dotyczące tworzenia nowej oferty aplikacji platformy Azure w portalu komercyjnym. Przed utworzeniem nowej oferty aplikacji platformy Azure należy zapoznać się z tymi koncepcjami.

Przed opublikowaniem nowej oferty aplikacji platformy Azure [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md) i upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Projektowanie, kompilowanie i testowanie ofert aplikacji platformy Azure wymaga znajomości technicznej platformy Azure i technologii używanych do tworzenia oferty. Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

* Podstawowe informacje na temat [usług platformy Azure](https://azure.microsoft.com/services/).
* [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/).
* Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Wiedza [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Praktyczna wiedza o formacie [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Dokumentacja techniczna i zasoby

Zapoznaj się z poniższymi zasobami podczas przygotowywania oferty aplikacji platformy Azure dla komercyjnej witryny Marketplace.

* [Informacje o szablonach Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

* Przewodniki Szybki start:

    * [Szablony przewodników Szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/)
    * [Przewodnik po najlepszych rozwiązaniach dotyczących szablonów platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Publikowanie definicji aplikacji](../../managed-applications/publish-service-catalog-app.md)
    * [Wdrażanie aplikacji katalogu usług](../../managed-applications/deploy-service-catalog-quickstart.md)

* Samouczki:

    * [Tworzenie plików definicji](../../managed-applications/publish-service-catalog-app.md)
    * [Publikowanie aplikacji na platformie handlowej](../../managed-applications/publish-marketplace-app.md)

* Badan

    * [Interfejs wiersza polecenia platformy Azure](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [Rozwiązania aplikacji zarządzanych](../../managed-applications/sample-projects.md)

[Tworzenie szablonów rozwiązań wideo i aplikacji zarządzanych dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) zapewnia kompleksowe wprowadzenie do typu oferty aplikacji platformy Azure:

* Jakie typy ofert są dostępne
* Jakie zasoby techniczne są wymagane
* Jak utworzyć szablon Azure Resource Manager
* Opracowywanie i testowanie interfejsu użytkownika aplikacji
* Jak opublikować ofertę aplikacji
* Proces przeglądu aplikacji

### <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz co najmniej jedno z następujących środowisk skryptów, aby ułatwić zarządzanie aplikacją platformy Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Zalecamy dodanie następujących narzędzi do środowiska deweloperskiego:

* [Eksplorator usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/) z następującymi rozszerzeniami:
    * Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostępne narzędzia można sprawdzić na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/) . Ponadto, jeśli używasz programu Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Typy planów aplikacji platformy Azure

Istnieją dwa rodzaje planów aplikacji platformy Azure: szablony rozwiązań i zarządzane aplikacje.

* **Szablon rozwiązania** jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Użyj tego typu planu, jeśli rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza pojedynczą maszyną wirtualną. Szablon rozwiązania umożliwia Automatyzowanie udostępniania więcej niż jednego zasobu, w tym maszyn wirtualnych, sieci i zasobów magazynu, aby zapewnić złożone rozwiązania IaaS.  Aby uzyskać więcej informacji na temat tworzenia szablonów rozwiązań, zobacz [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

* **Aplikacja zarządzana** jest podobna do szablonów rozwiązań z jedną różnicą kluczową. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Korzystaj z zarządzanych aplikacji, aby łatwo tworzyć i dostarczać klientom w pełni zarządzane aplikacje gotowe.  Aby uzyskać więcej informacji o zaletach i typach zarządzanych aplikacji, zobacz [Omówienie usługi Azure Managed Applications](../../managed-applications/overview.md).

## <a name="technical-requirements"></a>Wymagania techniczne

Wszystkie aplikacje platformy Azure obejmują co najmniej dwa pliki w folderze głównym `.zip` archiwum:

* Plik szablonu Menedżer zasobów o nazwie [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md).  Ten szablon definiuje zasoby, które mają zostać wdrożone w ramach subskrypcji platformy Azure klienta. Przykłady szablonów Menedżer zasobów można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) lub w odpowiedniej repozytorium [szablonów szybkiego startu: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .

* Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition.jsna](../../managed-applications/create-uidefinition-overview.md).  W interfejsie użytkownika należy określić elementy, które umożliwiają klientom podanie wartości parametrów.

Wszystkie nowe oferty aplikacji platformy Azure muszą obejmować [Identyfikator GUID autorstwa klienta na platformie Azure](../azure-partner-customer-usage-attribution.md). 

Aby dowiedzieć się więcej o wymaganiach dotyczących publikowania dla każdego planu aplikacji, zobacz Wymagania dotyczące [publikowania szablonów rozwiązań](../marketplace-solution-templates.md) i [aplikacji zarządzanych](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

>[!NOTE]
>Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim nie będą wyświetlane w sklepach online do momentu ponownego opublikowania oferty. Pamiętaj, aby zawsze ponownie opublikować ofertę po wprowadzeniu zmian.

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).

1. W menu po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.

1. Na stronie Przegląd wybierz pozycję **+ Nowa oferta**  >  **aplikacji platformy Azure**.

    ![Ilustruje menu nawigacji po lewej stronie.](./media/new-offer-azure-app.png)

1. Na stronie **Nowa oferta** wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

     * Ten identyfikator jest widoczny dla klientów w adresie sieci Web dla ofert i szablonów Azure Resource Manager, jeśli ma zastosowanie.
     * Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

1. Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim.

     * Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.
     * Aliasu oferty nie można zmienić po wybraniu pozycji **Utwórz**.

1. Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

Na stronie **Konfiguracja oferty** można skonfigurować test i zarządzanie potencjalnymi klientami dla oferty.

### <a name="test-drive"></a>Wersja testowa

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, dając im możliwość "Wypróbuj przed zakupem", co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych potencjalnych klientów. [Dowiedz się więcej o testowaniu dysków](../what-is-test-drive.md).

Aby włączyć dysk testowy przez stały okres czasu, zaznacz pole wyboru **Włącz dysk testowy** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru. Skonfigurowanie środowiska testowego w sekcji [konfiguracja techniczna na dysku testowym](#test-drive-technical-configuration) w dalszej części tego tematu.

Aby uzyskać dodatkowe informacje, zobacz [testowanie oferty w komercyjnej witrynie Marketplace](test-drive.md). Możesz również przeczytać o [najlepszych rozwiązaniach dotyczących dysków testowych](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) i pobrać [plik PDF z omówieniem dysków testowych](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

>[!Note]
>Ze względu na to, że wszystkie aplikacje platformy Azure są implementowane przy użyciu szablonu Azure Resource Manager, jedynym typem dysku testowego dostępnym dla aplikacji platformy Azure jest [dysk testowy oparty na Azure Resource Manager](../azure-resource-manager-test-drive.md).

### <a name="customer-leads"></a>Potencjalni klienci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Na stronie **Właściwości** można zdefiniować kategorie używane do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych wspierających Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md).

### <a name="legal"></a>Informacje prawne

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Lista oferty

Na tej stronie można zarządzać kopią i obrazami oferty komercyjnej witryny Marketplace.

### <a name="marketplace-details"></a>Szczegóły witryny Marketplace

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu i warunki użytkowania) nie jest wymagana w języku angielskim, o ile opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

Oto przykład sposobu wyświetlania informacji o ofercie w witrynie Azure Marketplace (wszystkie wymienione ceny są przeznaczone wyłącznie do celów i nie są przeznaczone do odzwierciedlenia rzeczywistych kosztów):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

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

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Azure Portal:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Tytuł
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu

#### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

#### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty, do 100 znaków. Ten opis może być używany w wynikach wyszukiwania.

#### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty, do 256 znaków. Ten opis może być używany w wynikach wyszukiwania.

#### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Słowa kluczowe wyszukiwania

Opcjonalnie możesz wprowadzić do trzech słów kluczowych wyszukiwania, aby pomóc klientom w znalezieniu oferty w portalu Marketplace. Aby uzyskać najlepsze wyniki, należy również użyć tych słów kluczowych w opisie.

#### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres URL zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

### <a name="useful-links"></a>Przydatne łącza

Dodaj linki do opcjonalnych dodatkowych dokumentów online dotyczących rozwiązania, wybierając pozycję **+ Dodaj link**.

### <a name="contact-information"></a>Informacje kontaktowe

Podaj nazwę, adres e-mail i numer telefonu dla kontaktu **z pomocą techniczną**, **kontaktu inżynieryjnego**i **programu CSP**. Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP. Niektóre kontakty mogą wymagać dodatkowych informacji.

### <a name="marketplace-media"></a>Multimedia dla portalu Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Nierozmyte obrazy spowodują odrzucenie zgłoszenia.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="store-logos"></a>Logo sklepu

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Średnia** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy zrzut ekranu musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG. Każdy zrzut ekranu musi zawierać podpis.

#### <a name="videos"></a>Wideo

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Powinny one być hostowane w zewnętrznej usłudze wideo. Wprowadź nazwę, adres sieci Web i obraz miniatury PNG wideo o godzinie 1280 x 720 pikseli.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

- [Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](../gtm-offer-listing-best-practices.md)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="preview-audience"></a>Podgląd odbiorców

Na karcie Podgląd Wybierz ograniczony **odbiorcę w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo w szerszej grupie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w wersji zapoznawczej wybierz pozycję **Przejdź na żywo** , aby opublikować ofertę do komercyjnych odbiorców w portalu Marketplace.

Odbiorca w wersji zapoznawczej jest identyfikowany przez identyfikatory GUID subskrypcji platformy Azure, a także opcjonalny opis dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli Twoja oferta jest już aktywna, możesz nadal zdefiniować odbiorcę w wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty *przed* opublikowaniem jej na żywo na rynku. Mogą oni wyświetlać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po opublikowaniu oferty w portalu Marketplace. Prywatni odbiorcy (zdefiniowani na karcie **ceny i dostępność** ) mają wyłączny dostęp do konkretnego planu.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Wypełnij tę sekcję tylko wtedy, gdy oferta obejmuje zarządzaną aplikację, która będzie emitować zdarzenia pomiaru przy użyciu interfejsu API usługi pomiaru Marketplace. Wprowadź **Identyfikator dzierżawy Azure Active Directory** i **Identyfikator aplikacji Azure Active Directory** , która będzie używana przez usługę podczas emitowania zdarzeń pomiaru.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration-offer-level"></a>Konfiguracja techniczna (poziom oferty)

>[!Note]
>Szczegóły techniczne na poziomie oferty są opcjonalne.  Te szczegóły trzeba skonfigurować tylko w przypadku publikowania aplikacji zarządzanej przy użyciu zliczania taryfowego i posiadania usługi, która będzie uwierzytelniana za pomocą tokenu zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [strategie uwierzytelniania](./marketplace-metering-service-authentication.md) w różnych opcjach uwierzytelniania.

Konfiguracja techniczna definiuje szczegóły (identyfikator dzierżawy i identyfikator aplikacji) używane do identyfikacji usługi, która będzie emitować zdarzenia pomiaru dla aplikacji zarządzanej przy użyciu [interfejsów API usługi pomiaru Marketplace](./marketplace-metering-service-apis.md).  Wprowadź tożsamość, która będzie używana przez usługę podczas emitowania zdarzeń pomiarowych.

* **Identyfikator dzierżawy usługi Azure AD** (wymagane): wewnątrz Azure Portal należy [utworzyć aplikację Azure Active Directory (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) , aby umożliwić nam zweryfikowanie połączenia między naszymi dwiema usługami za pomocą komunikacji uwierzytelnionej. Aby znaleźć [Identyfikator dzierżawy](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)), przejdź do Azure Active Directory i wybierz pozycję **Właściwości**, a następnie wyszukaj numer **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).
* **Identyfikator aplikacji usługi Azure AD** (wymagane): wymagany jest również [Identyfikator aplikacji](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)i klucz uwierzytelniania. Aby uzyskać te wartości, przejdź do Azure Active Directory i wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Aby znaleźć klucz uwierzytelniania, przejdź do pozycji **Ustawienia** i wybierz pozycję **klucze**. Należy podać opis i czas trwania, a następnie podać wartość liczbową.

>[!Note]
>Identyfikator aplikacji platformy Azure zostanie skojarzony z IDENTYFIKATORem wydawcy i może być ponownie używany w ramach tego konta wydawcy.

>[!Note]
>Ta konfiguracja jest wymagana, jeśli chcesz użyć [zdarzenia użycia](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)w usłudze Batch.  Jeśli chcesz przesłać [zdarzenie użycia](marketplace-metering-service-apis.md#metered-billing-single-usage-event)), możesz również użyć [usługi metadanych wystąpienia](../../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać [token okaziciela sieci Web JSON (JWT)](pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

## <a name="plan-overview"></a>Przegląd planu

Na tej karcie można podać różne opcje planu w ramach tej samej oferty. Plany te (dawniej nazywane jednostkami SKU) mogą różnić się od typu planu (szablonu rozwiązania a aplikacji zarządzanej), zysków lub odbiorców. Skonfiguruj co najmniej jeden plan w celu wyświetlenia oferty w portalu Marketplace.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](../private-offers.md).

Po utworzeniu zobaczysz nazwy planu, identyfikatory, typ planu, dostępność (Public lub private), bieżący stan publikowania i wszystkie dostępne akcje na tej karcie.

**Akcje** dostępne w **przeglądzie planu** różnią się w zależności od bieżącego stanu planu i mogą obejmować:

* Jeśli plan ma stan **wersja robocza** — Usuń wersję roboczą.
* Jeśli plan ma stan **Live** — Zatrzymaj sprzedawanie lub zsynchronizuj prywatnych odbiorców.

### <a name="create-new-plan"></a>Utwórz nowy plan

***Identyfikator planu*** — Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu.  Używaj tylko małych liter, znaków alfanumerycznych, łączników lub podkreśleń. Dla tego identyfikatora planu można używać maksymalnie 50 znaków. Tego identyfikatora nie można zmienić po wybraniu pozycji Utwórz.

***Nazwa planu*** — klienci będą widzieli tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę oferty dla każdego planu w tej ofercie. Nazwa planu służy do rozróżniania planów oprogramowania, które mogą być częścią tej samej oferty (na przykład nazwa oferty: system Windows Server; plany: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Konfigurowanie planu

Na tej karcie można ustawić konfigurację wysokiego poziomu dla typu planu, bez względu na to, czy będzie ona używać pakietów z innego planu, a także jakie chmure ma być dostępny w programie. Odpowiedzi na tej karcie wpłyną na to, które pola są wyświetlane na innych kartach dla tego samego planu.

#### <a name="plan-type"></a>Typ planu
Wybierz typ planu dla oferty. Plan **szablonu rozwiązania** jest zarządzany całkowicie przez klienta. Plan **aplikacji zarządzanej** umożliwia wydawcom zarządzanie aplikacją w imieniu klienta. Aby uzyskać szczegółowe informacje, zobacz [typy planów aplikacji platformy Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Ponowne używanie konfiguracji technicznej

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie używa pakietów z innego planu**.  Po wybraniu tej opcji będziesz mieć możliwość wybrania jednego z innych planów tego samego typu dla tej oferty, aby ponownie użyć pakietów z programu.

>[!Note]
>Po ponownym użyciu pakietów z innego planu cała karta konfiguracja techniczna zostanie usunięta z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, zostaną użyte również dla tego planu.<br><br>Tego ustawienia nie można zmienić po opublikowaniu tego planu.

#### <a name="azure-regions-cloud"></a>Regiony platformy Azure (chmura)

Plan musi być dostępny w co najmniej jednym regionie świadczenia usługi Azure.

Wybierz opcję **globalną platformy Azure** , aby udostępnić plan klientom we wszystkich regionach globalnych platformy Azure, które mają komercyjną integrację z portalu Marketplace. Aby uzyskać szczegółowe informacje, zobacz [dostępność geograficzna i obsługa waluty](../marketplace-geo-availability-currencies.md).

Wybierz opcję **Azure Government** , aby udostępnić plan w regionie [Azure Government](../../azure-government/documentation-government-welcome.md) . Ten region zapewnia klientom kontrolowany dostęp do jednostek federalnych, stanowych, lokalnych i plemienne, a także partnerów uprawnionych do ich świadczenia. Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Przed opublikowaniem w celu [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)Przetestuj i zweryfikuj swój plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, zażądaj konta próbnego od [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Po opublikowaniu planu i udostępnieniu go w określonym regionie świadczenia usługi Azure nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Ta opcja jest widoczna tylko w przypadku wybrania **Azure Government**.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="plan-listing"></a>Zaplanuj listę

Jest to miejsce, w którym można skonfigurować szczegóły listy dla planu. Na tej karcie są wyświetlane określone informacje, które mogą się różnić w zależności od planów w ramach tej samej oferty.

#### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą, która została nadana przez Ciebie podczas jego tworzenia. Ta nazwa jest wyświetlana w portalu Marketplace jako tytuł tego planu i jest ograniczona do 100 znaków.

#### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu (nie oferty). To podsumowanie jest ograniczone do 100 znaków.

#### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, a także różnice między planami w ramach oferty. Nie opisz oferty, tylko plan. Opis planu może zawierać maksymalnie 2 000 znaków.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="availability-solution-template-plans-only"></a>Dostępność (tylko plany szablonów rozwiązań)

Można sprawić, aby plan był widoczny dla wszystkich, tylko dla określonych klientów (odbiorców prywatnych) i zdecydować, czy plan ma być ukryty do użytku przez inny szablon rozwiązania, czy tylko aplikacje zarządzane.

#### <a name="plan-visibility"></a>Widoczność planu

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich, lub tylko do określonych odbiorców. Członkostwo w tych ograniczonych odbiorcach można przypisywać przy użyciu identyfikatorów subskrypcji platformy Azure.

Wybierz opcję **to jest plan prywatny** , aby Twój plan był prywatny i widoczny tylko dla wybranych odbiorców z ograniczeniami. Po opublikowaniu jako planu prywatnego możesz zaktualizować odbiorców lub wybrać opcję udostępnienia planu wszystkim użytkownikom. Gdy plan zostanie opublikowany jako widoczny dla wszystkich użytkowników, musi pozostać widoczny dla wszystkich; nie można jej ponownie skonfigurować jako planu prywatnego.

W przypadku wybrania planu jako prywatnego wprowadź **Identyfikator subskrypcji platformy Azure** i jego opis. Każdy z nich to odbiorca, który będzie miał dostęp do tego planu prywatnego. Dostęp jest przypisywany przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji platformy Azure. Dodaj maksymalnie 10 identyfikatorów subskrypcji klientów osobno lub 20 000 przez zaimportowanie pliku CSV. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID i litery muszą być małymi literami.

>[!Note]
>Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie **Podgląd** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo w portalu Marketplace. Gdy wybór odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

#### <a name="hide-plan"></a>Ukryj plan

Jeśli szablon rozwiązania jest przeznaczony do wdrożenia tylko pośrednio, gdy istnieje odwołanie do innego szablonu rozwiązania lub aplikacji zarządzanej, zaznacz to pole wyboru, aby opublikować szablon rozwiązania, ale ukryć go od klientów wyszukiwania bezpośrednio i przeglądania.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Cennik i dostępność (tylko zarządzane plany aplikacji)

Służy do konfigurowania **rynków** , w których ten plan będzie dostępny w programie, **cen** za miesiąc zarządzania rozwiązaniem oraz **widoczności planu** , jeśli tylko określeni klienci powinni go zobaczyć (prywatni odbiorcy).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

#### <a name="markets"></a>Wprowadza

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla dowolnej lokalizacji rynkowej, w której chcesz udostępnić ten plan. Pole wyszukiwania i przycisk służący do wybierania "krajów/regionów", w których firma Microsoft dokonuje zakupu sprzedaży i używania podatków w Twoim imieniu, są dołączane do pomocy.

Jeśli już ustawisz ceny dla planu w Stany Zjednoczone dolarów (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku zostanie obliczona zgodnie z bieżącymi stawkami za wymianę. Zawsze sprawdzaj ceny poszczególnych rynków przed opublikowaniem. Cennik można przejrzeć przy użyciu linku "Eksport cen (xlsx)" po zapisaniu zmian.

#### <a name="pricing"></a>Cennik

Podaj cenę za miesiąc dla tego planu.  Ta cena jest uzupełnieniem usługi Azure Infrastructure lub kosztów związanych z płatność zgodnie z rzeczywistym użyciem w przypadku zasobów wdrożonych w ramach tego rozwiązania.

Oprócz ceny za miesiąc można także ustawić ceny za użycie jednostek niestandardowych przy użyciu [rozliczeń naliczanych](./azure-app-metered-billing.md).  Cena za miesiąc można ustawić na zero, a opłaty za korzystanie wyłącznie z naliczanych opłat.

Ceny ustawione w USD (USD = Stany Zjednoczone dolara) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany przy zapisywaniu. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny z cennikiem i sprawdzając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na indywidualnym rynku, zmodyfikuj i zaimportuj arkusz cen.

>[!Note]
>Najpierw musisz zapisać zmiany cen, aby włączyć eksportowanie danych cen.

Uważnie Przejrzyj ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co można zmienić po opublikowaniu planu.  

>[!Note]
>Po opublikowaniu ceny na rynku planu nie można jej później zmienić.

#### <a name="plan-visibility"></a>Widoczność planu

Każdy plan można skonfigurować tak, aby był widoczny dla wszystkich, lub tylko do określonych odbiorców. Członkostwo w tych ograniczonych odbiorcach można przypisywać przy użyciu identyfikatorów subskrypcji platformy Azure.

Wybierz opcję **to jest plan prywatny** , aby Twój plan był prywatny i widoczny tylko dla wybranych odbiorców z ograniczeniami. Po opublikowaniu jako planu prywatnego możesz zaktualizować odbiorców lub wybrać opcję udostępnienia planu wszystkim użytkownikom. Gdy plan zostanie opublikowany jako widoczny dla wszystkich użytkowników, musi pozostać widoczny dla wszystkich; nie można jej ponownie skonfigurować jako planu prywatnego.

>[!Note]
>Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie **Podgląd** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo w portalu Marketplace. Gdy wybór odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

W przypadku wybrania planu jako prywatnego wprowadź **Identyfikator subskrypcji platformy Azure** i jego opis. Każdy z nich to odbiorca, który będzie miał dostęp do tego planu prywatnego. Dostęp jest przypisywany przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji platformy Azure. Dodaj maksymalnie 10 identyfikatorów subskrypcji klientów osobno lub 20 000 przez zaimportowanie pliku CSV. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID i litery muszą być małymi literami.

>[!Note]
>Oferty prywatne nie są obsługiwane w przypadku subskrypcji platformy Azure ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP).

### <a name="technical-configuration"></a>Konfiguracja techniczna

Na tej karcie można przekazać pakiet wdrożeniowy, który umożliwi klientom wdrożenie planu.

>[!Note]
>Ta karta nie będzie widoczna, jeśli ten plan został skonfigurowany tak, aby ponownie używał pakietów z innego planu na karcie **Konfiguracja planu** .

#### <a name="package-details"></a>Szczegóły pakietu

Na tej karcie można edytować wersję roboczą konfiguracji technicznej.

**Wersja** — Przypisz bieżącą wersję konfiguracji technicznej.  Zwiększ tę wersję przy każdej publikacji zmiany na tej stronie. Wersja musi mieć format `{integer}.{integer}.{integer}` .

**Plik pakietu** (zip) — ten pakiet zawiera wszystkie pliki szablonów potrzebne dla tego planu, a także dodatkowe zasoby, spakowane jako `.zip` plik.

Wszystkie pakiety planu aplikacji platformy Azure muszą zawierać te dwa pliki w folderze głównym `.zip` archiwum:

* Plik szablonu Menedżer zasobów o nazwie [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md). Ten szablon automatyzuje wdrażanie zasobów w subskrypcji platformy Azure dla klientów.  Przykłady szablonów Menedżer zasobów można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) lub w odpowiedniej repozytorium [szablonów szybkiego startu: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .
* Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition.jsna](../../azure-resource-manager/managed-application-createuidefinition-overview.md).

Maksymalne obsługiwane rozmiary plików:

* Do 1 GB w łącznym rozmiarze skompresowanego `.zip` Archiwum
* Do 1 GB dla dowolnego nieskompresowanego pliku w `.zip` Archiwum  

Wszystkie nowe oferty aplikacji platformy Azure muszą również obejmować identyfikator GUID [przypisywania użycia klienta przez partnera platformy Azure](../azure-partner-customer-usage-attribution.md) .

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

Plany aplikacji zarządzanych wymagają dodatkowych informacji na tej karcie.

#### <a name="previously-published-packages"></a>Poprzednio opublikowane pakiety

Podkarta **poprzednio opublikowanych pakietów** umożliwia wyświetlenie wszystkich opublikowanych wersji konfiguracji technicznej.

#### <a name="enable-just-in-time-jit-access"></a>Włącz dostęp just-in-Time (JIT)

Wybierz tę opcję, aby włączyć dostęp just-in-Time (JIT) dla tego planu.  Dostęp JIT umożliwia żądanie dostępu z podwyższonym poziomem uprawnień do zasobów aplikacji zarządzanej w celu rozwiązywania problemów lub konserwacji. Zawsze masz dostęp tylko do odczytu do zasobów, ale przez określony okres możesz mieć większy dostęp.  Aby uzyskać więcej informacji, zobacz [Włączanie i żądanie dostępu just in Time do Azure Managed Applications](../../managed-applications/request-just-in-time-access.md).  Aby wymagać, aby klienci aplikacji zarządzanej mogli udzielić stałego dostępu do konta, pozostaw tę opcję niezaznaczone.

>[!Note]
>Pamiętaj, aby zaktualizować `createUiDefinition.json` plik w celu obsługi tej funkcji.  

#### <a name="deployment-mode"></a>Tryb wdrożenia

Wybierz, czy podczas wdrażania tego planu ma zostać skonfigurowany **pełny** , czy **przyrostowy tryb wdrażania** : 

* W **trybie kompletnym**ponowne wdrożenie aplikacji przez klienta spowoduje usunięcie zasobów w zarządzanej grupie zasobów, jeśli zasoby nie są zdefiniowane w `mainTemplate.json` . 
* W **trybie przyrostowym**ponowne wdrożenie aplikacji pozostawia istniejące zasoby bez zmian.

Aby dowiedzieć się więcej na temat trybów wdrażania, zobacz [Azure Resource Manager trybami wdrożenia](../../azure-resource-manager/deployment-modes.md).

#### <a name="notification-endpoint-url"></a>Adres URL punktu końcowego powiadomienia

Określ punkt końcowy elementu webhook HTTPS, aby otrzymywać powiadomienia o wszystkich operacjach CRUD na wystąpieniach aplikacji zarządzanej tej wersji planu.

#### <a name="customize-allowed-customer-actions"></a>Dostosuj dozwolone akcje klienta

Wybierz tę opcję, aby określić akcje, które klienci mogą wykonywać na zarządzanych zasobach oprócz akcji " `*/read` ", które są dostępne domyślnie.

Utwórz listę dodatkowych akcji, które chcesz umożliwić klientowi wykonywanie w tym miejscu, oddzielając je średnikami.  Aby uzyskać więcej informacji, zobacz [Omówienie przypisań Odmów dla zasobów platformy Azure](../../role-based-access-control/deny-assignments.md). Aby uzyskać dostępne akcje, zobacz [Azure Resource Manager operacje dostawcy zasobów](../../role-based-access-control/resource-provider-operations.md). Na przykład, aby zezwolić użytkownikom na ponowne uruchomienie maszyn wirtualnych, Dodaj `Microsoft.Compute/virtualMachines/restart/action` do dozwolonych akcji.

#### <a name="global-azure--azure-government-cloud"></a>Globalna Chmura platformy Azure/Azure Government

Wskaż, kto powinien mieć dostęp do aplikacji zarządzanej w każdej obsługiwanej chmurze. Użytkownicy, grupy lub aplikacje, którym chcesz nadać uprawnienia do zarządzanej grupy zasobów, są identyfikowane przy użyciu tożsamości Azure Active Directory (AD).

**Azure Active Directory identyfikator dzierżawy** — identyfikator dzierżawy usługi Azure AD (znany również jako identyfikator katalogu) zawierający tożsamości użytkowników, grup lub aplikacji, do których chcesz udzielić uprawnień. Identyfikator dzierżawy usługi Azure AD można znaleźć na Azure Portal w obszarze [właściwości Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autoryzacje** — Dodaj identyfikator obiektu Azure Active Directory użytkownika, grupy lub aplikacji, którym chcesz nadać uprawnienia do zarządzanej grupy zasobów. Zidentyfikuj użytkownika według identyfikatora podmiotu zabezpieczeń, który można znaleźć w [bloku Azure Active Directory użytkownicy na Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Dla każdego podmiotu zabezpieczeń wybierz jedną z wbudowanych ról usługi Azure AD z listy (właściciela lub współautora). Wybrana rola zostanie opisywana przez podmiot zabezpieczeń do zasobów w ramach subskrypcji klienta. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md). Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC), zobacz [Rozpoczynanie pracy z funkcją RBAC w Azure Portal](../../role-based-access-control/overview.md).

>[!Note]
>Mimo że użytkownik może dodać do 100 autoryzacji na chmurę, łatwiej jest utworzyć Active Directory grupę użytkowników i określić jej identyfikator w "Identyfikator podmiotu zabezpieczeń". Pozwoli to na dodanie większej liczby użytkowników do grupy zarządzania po wdrożeniu planu i zmniejszenie potrzeb aktualizacji planu tylko w celu dodania większej liczby autoryzacji.

#### <a name="policy-settings"></a>Ustawienia zasad

Zastosuj [Zasady platformy Azure](../../governance/policy/overview.md) do aplikacji zarządzanej, aby określić wymagania dotyczące zgodności dla wdrożonego rozwiązania. Definicje zasad i format wartości parametrów podano w artykule [Przykłady dla usługi Azure Policy](../../governance/policy/samples/index.md). Można skonfigurować maksymalnie pięć zasad i tylko jedno wystąpienie poszczególnych zasad. Niektóre zasady wymagają dodatkowych parametrów. Dla zasad inspekcji wymagana jest standardowa jednostka SKU. Nazwa zasad jest ograniczona do 50 znaków.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="co-sell-with-microsoft"></a>Wspólna sprzedaż z firmą Microsoft

Podanie informacji na karcie co do sprzedaży jest całkowicie opcjonalne w przypadku publikowania oferty. Jest to wymagane do osiągnięcia gotowej do rozsprzedażu i gotowego do zakupu stanu do współdziałania. Te informacje będą używane przez zespoły sprzedaży firmy Microsoft, aby dowiedzieć się więcej na temat rozwiązania podczas oceny jego dopasowania do potrzeb klientów. Nie jest on dostępny bezpośrednio dla klientów.

Aby uzyskać szczegółowe informacje na ten temat, zobacz [sprzedawanie opcji w centrum partnerskim](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Odsprzedaż za poorednictwem dostawców CSP

Rozszerz zasięg oferty, udostępniając ją partnerom w programie [dostawcy rozwiązań w chmurze](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Dzięki temu Odsprzedawcy mogą sprzedawać swoją ofertę klientom i tworzyć powiązane rozwiązania.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive"></a>Wersja testowa

Skonfiguruj demonstrację (Test Drive), która umożliwia klientom wypróbowanie oferty przed jej zakupem. Aby utworzyć środowisko demonstracyjne, które umożliwia klientom wypróbowanie oferty przez ustalony okres, zobacz temat [testowanie oferty w komercyjnej witrynie Marketplace](test-drive.md).

Aby włączyć stację testową, zaznacz pole wyboru **Włącz dysk testowy** na karcie [Konfiguracja oferty](#test-drive) . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

### <a name="test-drive-technical-configuration"></a>Wersja testowa konfiguracji technicznej

- **Identyfikator aplikacji usługi Azure AD** (wymagane): wprowadź [Identyfikator aplikacji](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby zezwolić na wdrożenie dysku testowego w Twoim imieniu, Utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure (niewymaganą dla Power BI dysków testowych).

* **Identyfikator subskrypcji platformy Azure** (wymagany dla Azure Resource Manager i aplikacji logiki) — wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure na potrzeby raportowania użycia zasobów i rozliczeń. Zalecamy [utworzenie oddzielnej subskrypcji platformy Azure](../../billing/billing-create-subscription.md) , która ma być używana na potrzeby dysków testowych, jeśli jeszcze jej nie masz. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [Azure Portal](https://portal.azure.com/) i przechodząc do karty **subskrypcje** w menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (na przykład "a83645ac-1234-5ab6-6789-1h234g764ghty").
* **Identyfikator dzierżawy usługi Azure AD** (wymagany) — wprowadź [Identyfikator dzierżawy](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **Właściwości**, a następnie wyszukaj numer **identyfikatora katalogu** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwy domeny w:  [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* **Nazwa dzierżawy usługi Azure AD** (wymagana dla dynamicznego 365) — wprowadź nazwę Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [Azure Portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona w polu Nazwa konta.
* **Identyfikator aplikacji usługi Azure AD** (wymagany) — wprowadź [Identyfikator aplikacji](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** na liście (na przykład 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory klucz tajny klienta aplikacji** (wymagane) — wprowadź [klucz tajny klienta](../../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)aplikacji usługi Azure AD. Aby znaleźć tę wartość, zaloguj się do [Azure Portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację testową. Następnie wybierz pozycję **Certyfikaty i wpisy tajne**, wybierz pozycję **Nowy wpis tajny klienta**, wprowadź opis, wybierz pozycję **nigdy nie** w obszarze **wygaśnięcie**, a następnie wybierz pozycję **Dodaj**. Pamiętaj o skopiowaniu wartości przed opuszczeniem tej strony.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="marketplace-listing-optional"></a>Lista Marketplace (opcjonalnie)

Opisz środowisko testowe.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Opis** (wymagany) — opisz swój dysk testowy, co zostanie udowodnione, czy chcesz, aby użytkownik mógł eksperymentować z programem, funkcje do eksplorowania i wszelkie istotne informacje, które ułatwią użytkownikowi określenie, czy uzyskać ofertę. W tym polu można wprowadzić do 3 000 znaków tekstu. 
* **Informacje o dostępie** (wymagane dla Azure Resource Manager i logicznych dysków testowych) — Wyjaśnij, co musi znać klient, aby uzyskać dostęp do tego dysku testowego i korzystać z niego. Zapoznaj się z scenariuszem dotyczącym korzystania z oferty i dokładnie tego, co klient powinien znać, aby uzyskać dostęp do funkcji na całym dysku testowym. W tym polu można wprowadzić do 10 000 znaków tekstu.
* **Podręcznik użytkownika** (wymagane) — szczegółowy przewodnik po środowisku testowym. Podręcznik użytkownika powinien obejmować dokładnie te informacje, które klient ma uzyskać, z dysku testowego i służy jako odwołanie do wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i mieć nazwę (maksymalnie 255 znaków) po przekazaniu.
* **Wideo: Dodaj filmy** wideo (opcjonalnie) — wideo można przekazać do usługi YouTube lub Vimeo i przywoływane w tym miejscu za pomocą linku i miniatury (533 x 324 pikseli), aby klient mógł wyświetlić szczegółowe informacje ułatwiające lepsze zrozumienie dysku testowego, w tym sposób pomyślnego użycia funkcji oferty i zrozumienie scenariuszy, które wyróżnią korzyści.
  * **Nazwa** (wymagana)
  * **Adres** (tylko w serwisie YouTube lub Vimeo; wymagane)
  * **Miniatura** (plik obrazu musi być w formacie PNG i 533 x 324 pikseli).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="publish"></a>Opublikuj

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Przejrzyj i Opublikuj** w prawym górnym rogu portalu.

Przejrzyj stan ukończenia dla każdej sekcji oferty.
    - *Nie uruchomiono* — oznacza, że sekcja nie została dotknięcia i należy ją ukończyć.
    - *Niekompletne* — oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - *Zakończone* — oznacza, że sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.

Jeśli ta oferta jest publikowana po raz pierwszy, możesz dostarczyć instrukcje dotyczące testowania do zespołu certyfikacji, aby upewnić się, że aplikacja zostanie prawidłowo przetestowana, a także dowolnych dodatkowych notatek przydatnych w zrozumieniu aplikacji.

Wybierz pozycję **Prześlij** , aby przesłać ofertę na potrzeby publikowania. Wyślemy Ci wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty, którą można przejrzeć i zatwierdzić.

Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę opublikowania oferty na publiczną (lub w przypadku prywatnej oferty dla odbiorców prywatnych).

### <a name="errors-and-review-feedback"></a>Błędy i przejrzyj opinię

Krok **ręcznego walidacji** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych (szczególnie Azure Resource Manager szablon), jednak problemy są zwykle przedstawiane jako linki żądania ściągnięcia. Informacje na temat sposobu wyświetlania tych żądań ściągnięcia i reagowania na nie można znaleźć w temacie [Obsługa informacji zwrotnych](./azure-apps-review-feedback.md).

Jeśli występują błędy w co najmniej jednym z kroków publikowania, popraw je przed ponownym opublikowaniem oferty.

## <a name="next-steps"></a>Następne kroki

* [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
