---
title: Tworzenie nowej oferty usługi Azure Apps w komercyjnym rynku
description: How to create a new Azure Apps offer for listing or selling in the Azure Marketplace, AppSource, or through the Cloud Solution Provider (CSP) program using the Commercial Marketplace portal on Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 6b7ca1f10744bf38137c57d94d796655946c31b3
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390342"
---
# <a name="create-an-azure-application-offer"></a>Tworzenie oferty aplikacji platformy Azure

Kroki publikowania oferty aplikacji platformy Azure w komercyjnym portalu marketplace przedstawiono tutaj.

## <a name="azure-application-offer-type"></a>Typ oferty aplikacji platformy Azure

W tym temacie opisano podstawy dotyczące ofert aplikacji platformy Azure.  Należy zapoznać się z tych pojęć przed rozpoczęciem procesu publikowania nowej oferty aplikacji platformy Azure w portalu Marketplace.

### <a name="publishing-overview"></a>Omówienie publikowania

Wideo [Szablony rozwiązań do tworzenia i aplikacje zarządzane dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) jest wprowadzeniem do typu oferty aplikacji platformy Azure:

* Jakie typy ofert są dostępne;
* Jakie zasoby techniczne są wymagane;
* Jak zasiewać szablon usługi Azure Resource Manager;
* Tworzenie i testowanie interfejsu użytkownika aplikacji;
* Jak opublikować ofertę aplikacji;
* Proces przeglądu aplikacji.

### <a name="types-of-azure-application-plans"></a>Typy planów aplikacji platformy Azure

Istnieją dwa rodzaje planów aplikacji platformy Azure, zarządzanych aplikacji i szablonów rozwiązań.

* **Szablon rozwiązania** jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Ten typ planu jest używany, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną (maszynę wirtualną).  Za pomocą szablonu rozwiązania można zautomatyzować dostarczanie więcej niż jednego zasobu, w tym maszyn wirtualnych, sieci i zasobów magazynu, aby zapewnić złożone rozwiązania IaaS.  Aby uzyskać więcej informacji na temat tworzenia szablonów rozwiązań, zobacz dokumentację [usługi Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

* **Aplikacja zarządzana** jest podobna do szablonów rozwiązań, z jedną kluczową różnicą. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Aplikacje zarządzane umożliwia łatwe tworzenie i dostarczanie klientom w pełni zarządzanych aplikacji "pod klucz".  Aby uzyskać więcej informacji na temat zalet i typów aplikacji zarządzanych, zobacz [omówienie aplikacji zarządzanych platformy Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Wszystkie aplikacje platformy Azure zawierają co najmniej `.zip` dwa pliki w folderze głównym archiwum:

* Plik szablonu Menedżera zasobów o nazwie [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Jest to szablon, który definiuje zasoby do wdrożenia w subskrypcji platformy Azure klienta.  Przykłady szablonów Usługi Resource Manager można znaleźć w [galerii Szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/) lub w odpowiednim usłudze GitHub: Repozytorium [szablonów szybkiego startu usługi Azure Resource Manager.](https://github.com/azure/azure-quickstart-templates)

* Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  W interfejsie użytkownika należy określić elementy, które umożliwiają klientom podanie wartości parametrów.

Wszystkie nowe oferty aplikacji platformy Azure muszą zawierać [identyfikator GUID atrybucji użycia klienta platformy Azure.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Zapoznaj się z następującą dokumentacją aplikacji platformy Azure, która zawiera przewodniki Szybki start, samouczki i przykłady.

* [Opis szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Przewodniki Szybki start:

    * [Szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/)
    * [Szablony przewodnika Szybki start platformy GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publikowanie definicji aplikacji](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Wdrażanie aplikacji katalogu usług](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Samouczki:

    * [Tworzenie plików definicji](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publish marketplace application (Publikowanie aplikacji w witrynie Marketplace)](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Próbki:

    * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Rozwiązania aplikacji zarządzanych](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Podstawy wiedzy technicznej

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty.

Zespół inżynierów powinien posiadać wiedzę na temat następujących technologii firmy Microsoft:

* Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/).
* Jak [projektować i projektować aplikacje platformy Azure.](https://azure.microsoft.com/solutions/architecture/)
* Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i sieci [azure.](https://azure.microsoft.com/services/?filter=networking#networking)
* Wiedza robocza na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Wiedza robocza [json](https://www.json.org/).

### <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz jedno lub oba z następujących środowisk skryptów, aby ułatwić zarządzanie aplikacją platformy Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Zalecamy dodanie następujących narzędzi do środowiska programistycznego:

* [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Kod programu Visual Studio](https://code.visualstudio.com/) z następującymi rozszerzeniami:
    * Rozszerzenie: [narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Rozszerzenie: [Upiększyć](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Rozszerzenie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Możesz przejrzeć dostępne narzędzia na stronie [Narzędzia deweloperskie platformy Azure.](https://azure.microsoft.com/tools/)  Również w przypadku korzystania z programu Visual Studio, [visual studio marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Tworzenie oferty aplikacji platformy Azure

Przed utworzeniem oferty aplikacji platformy Azure należy najpierw [utworzyć konto Centrum partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) i otworzyć [pulpit nawigacyjny komercyjnego portalu marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z wybraną kartą **Przegląd.**

>[!Note]
>Po opublikowaniu oferty zmiany w ofercie złożonej w Centrum partnerów zostaną zaktualizowane tylko w systemie i witrynach sklepowych po ponownym opublikowaniu.  Po dokonaniu zmian należy przesłać ofertę do publikacji.

### <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Wybierz przycisk **+ Nowa oferta,** a następnie wybierz element menu **aplikacji platformy Azure.** Zostanie wyświetlone okno dialogowe **Nowa oferta.**

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

* **Identyfikator oferty:** unikatowy identyfikator dla każdej oferty na twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL oferty portalu Marketplace i szablonach usługi Azure Resource Manager (jeśli dotyczy). <br> <br> Identyfikator oferty musi być małe litery alfanumeryczne znaki (w tym łączniki i podkreślenia, ale nie odstępy). Jest ograniczona do 50 znaków i nie można go zmienić po wybraniu opcji Utwórz. <br> <br> Na przykład, jeśli `test-offer-1` wpiszesz tutaj, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres URL oferty będzie . 

* **Alias oferty**: Nazwa używana do odwoływania się do oferty w Centrum partnerów. Ta nazwa nie będzie używana w portalu marketplace i różni się od nazwy oferty i innych wartości, które będą wyświetlane klientom. Tej wartości nie można zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu identyfikatora **oferty** i **aliasu oferty**wybierz pozycję **Utwórz**. Będziesz wtedy mógł pracować nad wszystkimi innymi częściami oferty.

## <a name="offer-setup"></a>Konfiguracja oferty

Strona **Ustawienia oferty** prosi o następujące informacje. Pamiętaj, aby wybrać **pozycję Zapisz** po zakończeniu tych pól.

### <a name="test-drive"></a>Jazda próbna

Jazda próbna to świetny sposób na zaprezentowanie oferty potencjalnym klientom, dając im możliwość "wypróbuj przed zakupem", co skutkuje zwiększoną konwersją i generowaniem wysoko wykwalifikowanych potencjalnych klientów. [Dowiedz się więcej o dyskach testowych.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Aby włączyć jazdę próbną, zaznacz pole **Włącz napęd testowy.** Następnie należy skonfigurować środowisko demonstracyjne w [konfiguracji technicznej dysku testowego](#types-of-azure-application-plans) skonfigurować, aby umożliwić klientom wypróbowanie oferty przez określony czas. 

>[!Note]
>Ponieważ wszystkie aplikacje platformy Azure są implementowane przy użyciu szablonu Usługi Azure Resource Manager, jedynym typem dysku testowego, który można skonfigurować dla aplikacji platformy Azure, jest [dysk testowy oparty na usłudze Azure Resource Manager.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)

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

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Umowa standardowa dla komercyjnego rynku firmy Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Oferta aukcji

Na stronie Oferta wyświetlana jest liczba języków, w których oferta będzie wyświetlana. Obecnie jedyną dostępną opcją jest **angielski (Stany Zjednoczone).**

Musisz zdefiniować szczegóły rynku (nazwa oferty, opis, obrazy itp.) dla każdego języka / rynku. Wybierz język/nazwę rynku, aby podać te informacje.

> [!NOTE]
> Zawartość aukcji oferty (taka jak opis, dokumenty, zrzuty ekranu, warunki użytkowania itp.) nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Dopuszczalne jest również podanie *użytecznego adresu URL linku,* aby oferować treści w języku innym niż ten używany w treści aukcji Oferty.

### <a name="name"></a>Nazwa

Nazwa, którą wpiszesz w tym miejscu, będzie wyświetlana klientom jako tytuł twojej oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym dla **aliasu Oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Nazwa ta może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich). Nazwa nie może zawierać więcej niż 50 znaków i nie może zawierać żadnych emotikonów.

### <a name="summary"></a>Podsumowanie

Podaj krótki opis oferty (do 100 znaków), która może być używana w wynikach wyszukiwania w marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis swojej oferty (do 256 znaków). Opis może być używany w wynikach wyszukiwania w portalu Marketplace.

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

### <a name="search-keywords"></a>Szukaj słów kluczowych

Opcjonalnie możesz wprowadzić maksymalnie trzy słowa kluczowe wyszukiwania, aby pomóc klientom znaleźć twoją ofertę w marketplace. Aby uzyskać najlepsze wyniki, spróbuj użyć tych słów kluczowych w opisie.

### <a name="support-urls"></a>Obsługa adresów URL

W tej sekcji znajdziesz łącza ułatwiające klientom zapoznanie się z ofertą.

#### <a name="privacy-policy-url"></a>Adres URL polityki prywatności

Wprowadź adres URL do zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i regulacjami dotyczącymi prywatności oraz za zapewnienie ważnej polityki prywatności.

#### <a name="useful-links"></a>Przydatne łącza

Podaj opcjonalne dodatkowe dokumenty online dotyczące rozwiązania.  Dodaj dodatkowe przydatne łącza, klikając **+ Dodaj łącze**.

### <a name="contacts"></a>Kontakty

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje nie są wyświetlane klientom, ale będą dostępne dla firmy Microsoft i mogą być dostarczane partnerom CSP.

W sekcji **Kontakt pomocy technicznej** należy również podać adres URL pomocy **technicznej,** w którym partnerzy CSP mogą znaleźć pomoc techniczną dla Twojej oferty.

### <a name="marketplace-images"></a>Obrazy z Marketplace

W tej sekcji możesz podać logo i obrazy, które będą używane podczas wyświetlania oferty klientowi. Wszystkie obrazy muszą być w formacie png.

#### <a name="store-logos"></a>Logo sklepu

Podaj logo swojej oferty w trzech rozmiarach: **Mały (48 x 48),** **Średni (90 X 90)** i **Duży (216 x 216)**.

#### <a name="hero"></a>Bohater

Obraz bohatera jest opcjonalny. Jeśli go podasz, musi mierzyć 815 x 290 pikseli.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu, które pokazują, jak działa Twoja oferta. Możesz dodać maksymalnie pięć zrzutów ekranu. Wszystkie zrzuty ekranu muszą mieć rozmiary 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów, które zademonstrują Twoją ofertę. Te filmy powinny być hostowane na YouTube i / lub Vimeo. Dla każdego z nich wprowadź nazwę filmu, jego adres URL i miniaturę filmu (1280 x 720 pikseli).

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

- [Najważniejsze wskazówki dotyczące ofert w marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Wersja zapoznawcza

Karta **Podgląd** umożliwia zdefiniowanie ograniczonej **grupy odbiorców w wersji zapoznawczej** w celu zweryfikowania oferty przed opublikowaniem oferty na żywo szerszej grupy odbiorców w marketplace.

> [!IMPORTANT]
> Musisz wybrać **Opcję Przejdź na żywo,** zanim twoja oferta zostanie opublikowana na żywo w publicznej publiczności marketplace po sprawdzeniu oferty w wersji zapoznawczej.

Grupa odbiorców w wersji zapoznawczej jest identyfikowana za pomocą identyfikatorów GUID subskrypcji platformy Azure w połączeniu z opcjonalnym opisem dla każdego z nich.  Żadne z tych pól nie są widoczne dla klientów.

Dodaj maksymalnie 10 identyfikatorów subskrypcji platformy Azure ręcznie lub do 100 w przypadku przekazywania pliku CSV.  Dodając te subskrypcje, definiujesz grupę odbiorców, która będzie mogła uzyskać dostęp w wersji zapoznawczej do oferty, zanim zostanie w pełni opublikowana.  Jeśli twoja oferta jest już dostępna, możesz zdefiniować grupę odbiorców w wersji zapoznawczej, aby przetestować wszelkie zmiany lub aktualizacje oferty.

>[!Note]
>Grupa odbiorców podglądu różni się od grupy prywatnej. Grupa odbiorców w wersji zapoznawczej ma dostęp do twojej oferty *przed* opublikowaniem na żywo na rynku. Możesz też utworzyć plan i udostępnić go tylko odbiorcom prywatnym (na karcie Dostępność planu).  Odbiorcy wersji zapoznawczej będą mogli zobaczyć i zweryfikować wszystkie plany, w tym plany, które będą dostępne tylko dla odbiorców prywatnych po pełnym opublikowaniu oferty na rynku.

## <a name="plan-overview"></a>Omówienie planu

Karta **Przegląd planu** umożliwia udostępnienie różnych opcji planu w ramach tej samej oferty. Te plany (nazywane jednostkami SKU w portalu cloud partnerów) mogą się różnić pod względem typu planu (szablon rozwiązania a aplikacja zarządzana), zarabiania lub odbiorców.  Skonfiguruj co najmniej jeden plan, aby wyświetlić ofertę w portalu Marketplace.

Po utworzeniu zobaczysz nazwy planu, identyfikatory, typ planu, dostępność (publiczne lub prywatne), bieżący stan publikowania i wszystkie dostępne akcje na tej karcie.

**Działania** dostępne w **przeglądzie planu** różnią się w zależności od aktualnego stanu planu i mogą obejmować:

* Jeśli stan planu to **Wersja robocza** — usuwanie wersji roboczej.
* Jeśli stan planu to **Live** – Stop sell plan lub Sync private audience.

### <a name="create-new-plan"></a>Tworzenie nowego planu

***Identyfikator planu*** — utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu.  Używaj tylko małych liter, znaków alfanumerycznych, kresek lub podkreśleń. Dla tego identyfikatora planu dozwolony jest maksymalnie 50 znaków. Ten identyfikator nie może być modyfikowany po wybraniu create.

***Nazwa planu*** — klienci zobaczą tę nazwę przy podejmowaniu decyzji, który plan wybrać w ramach oferty. Utwórz unikatową nazwę oferty dla każdego planu w tej ofercie. Nazwa planu służy do rozróżniania planów oprogramowania, które mogą być częścią tej samej oferty (np. Nazwa oferty: Windows Server; plany: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Konfiguracja planu

Karta **Ustawienia planu** umożliwia ustawienie konfiguracji wysokiego poziomu dla typu planu, czy ponownie wykorzystuje pakiety z innego planu i jakie chmury plan powinien być dostępny.  Odpowiedzi na tej karcie będą miały wpływ na pola wyświetlane na innych kartach dla tego samego planu.

#### <a name="plan-type"></a>Typ planu

Jak opisano w [typach planów aplikacji platformy Azure,](#types-of-azure-application-plans)wybierz, czy twój plan będzie zawierał szablon rozwiązania, czy aplikację zarządzaną.

#### <a name="this-plan-reuses-packages"></a>Ten plan ponownie wykorzystuje pakiety

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie wykorzystuje pakiety z innego planu**.  Po wybraniu tej opcji będzie można wybrać jeden z innych planów tego samego typu dla tej oferty, aby ponownie użyć pakietów. 

>[!Note]
>Po ponownym użyciu pakietów z innego planu, cała karta konfiguracji technicznej zniknie z tego planu.  Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje, które zostaną zainstalowane w przyszłości, będą również używane dla tego planu. <br> <br> Ponadto nie można zmienić tego ustawienia po opublikowaniu tego planu.

#### <a name="cloud-availability"></a>Dostępność chmury

Ten plan musi być dostępny w co najmniej jednej chmurze. 

Wybierz opcję **Platformy Public Azure,** aby twoje rozwiązanie można wdrożyć dla klientów we wszystkich publicznych regionach platformy Azure, które mają integrację z marketplace.  Dowiedz się więcej o [dostępności geograficznej](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Wybierz opcję **Azure Government Cloud,** aby twoje rozwiązanie było możliwe do wdrożenia w [usłudze Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)— chmurze dla instytucji rządowych z kontrolowanym dostępem dla klientów z usług federalnych, stanowych, lokalnych lub plemiennych stanów USA oraz partnerów uprawnionych do obsługi tych jednostek.  Użytkownik, jako wydawca, jest odpowiedzialny za wszelkie mechanizmy kontroli zgodności, środki zabezpieczeń i najlepsze rozwiązania służące tej społeczności w chmurze.  Platforma Azure Government używa fizycznie odizolowanych centrów danych i sieci (zlokalizowanych tylko w Stanach Zjednoczonych).  Przed opublikowaniem na [platformie Azure Government](https://aka.ms/azuregovpublish)firma Microsoft zaleca przetestowanie i sprawdzenie poprawności rozwiązania w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby zaaslić i przetestować rozwiązanie, poproś o konto próbne z tego [linku](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Po opublikowaniu planu jako dostępnego w określonej chmurze nie można usunąć tej chmury.

**Certyfikaty azure dla instytucji rządowych w chmurze**

Ta opcja jest widoczna tylko wtedy, gdy w obszarze **Dostępność chmury wybrano**usługę **Azure Government Cloud** .

Usługi azure dla instytucji rządowych obsługują dane podlegające określonym przepisom i wymaganiom rządowym, takim jak FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 i CJIS.  Aby zwiększyć świadomość certyfikatów dla tych programów, możesz podać maksymalnie 100 linków opisujących certyfikaty.  Linki te mogą być albo linki do aukcji w programie bezpośrednio, lub linki do opisów zgodności z nimi na własnych stronach internetowych.  Te łącza będą widoczne tylko dla klientów usługi Azure Government Cloud.

## <a name="plan-listing"></a>Lista planu

Na karcie **Lista planów** są wyświetlane informacje o aukcji specyficzne dla planu, które mogą się różnić między różnymi planami dla tej samej oferty.

### <a name="name"></a>Nazwa

Wstępnie wypełnione na podstawie nazwy przypisanej planu podczas jego tworzenia.  Ta nazwa pojawi się jako tytuł tego "planu oprogramowania" wyświetlanego na rynku.  Może zawierać maksymalnie 100 znaków.

### <a name="summary"></a>Podsumowanie

Podaj krótkie podsumowanie planu oprogramowania.  Może zawierać maksymalnie 100 znaków.

### <a name="description"></a>Opis

Ten opis jest okazją do wyjaśnienia, co sprawia, że ten plan oprogramowania jest wyjątkowy i jakie są różnice w stosunku do innych planów oprogramowania w ramach oferty. Może zawierać do 2000 znaków.

Wybierz **pozycję Zapisz** po wypełnieniu tych pól.

## <a name="availability"></a>Dostępność

Karta **Dostępność** jest widoczna tylko dla planów szablonów rozwiązania.  Plan można uwidocznić dla wszystkich, tylko dla określonych klientów (grupy odbiorców prywatnych) i czy plan ma być ukryty tylko przez inny szablon rozwiązania lub aplikacje zarządzane.

### <a name="plan-audience"></a>Zaplanuj grupę odbiorców

Możesz skonfigurować każdy plan tak, aby był widoczny dla wszystkich lub dla wybranych przez Ciebie odbiorców. Członkostwo w tej ograniczonej grupy odbiorców można przypisać przy użyciu identyfikatorów subskrypcji platformy Azure.

**Prywatność / To jest prywatny plan** (opcjonalne pole wyboru) - Zaznacz to pole wyboru, aby twój plan był prywatny i widoczny tylko dla wybranych odbiorców. Po opublikowaniu planu prywatnego możesz zaktualizować odbiorców lub udostępnić plan wszystkim. Gdy plan zostanie opublikowany jako widoczny dla wszystkich, musi pozostać widoczny dla wszystkich. (Planu nie można ponownie skonfigurować jako planu prywatnego).

**Ograniczona liczba odbiorców (identyfikatory subskrypcji platformy Azure)** — przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego. Program Access jest przypisywany przy użyciu identyfikatorów subskrypcji platformy Azure z opcją do uwzględnienia opisu każdego przypisanego identyfikatora subskrypcji platformy Azure. W przypadku importowania pliku arkusza kalkulacyjnego csv można dodać maksymalnie 10 identyfikatorów subskrypcji lub 20 000 identyfikatorów subskrypcji.  Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a litery muszą być małe litery.

>[!Note]
>Grupa odbiorców prywatnych (lub grupa odbiorców z ograniczeniami) różni się od grupy odbiorców w wersji zapoznawczej zdefiniowanej na karcie [**Podgląd.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Grupa odbiorców w wersji zapoznawczej ma dostęp do twojej oferty *przed* opublikowaniem oferty na żywo na rynku. Podczas gdy oznaczenie odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlać wszystkie plany (prywatne lub nie) do celów weryfikacji.

### <a name="hide-plan"></a>Ukryj plan

Jeśli szablon rozwiązania jest przeznaczony do wdrożenia tylko pośrednio, gdy odwołuje się do innego szablonu rozwiązania lub aplikacji zarządzanej, zaznacz to pole, aby opublikować szablon rozwiązania, ale ukryć go przed klientami wyszukującymi i przeglądającymi go bezpośrednio.

## <a name="pricing-and-availability"></a>Cennik i dostępność

Karta **Ceny i dostępność** jest widoczna tylko dla zarządzanych planów aplikacji.  Można skonfigurować rynki, na których ten plan będzie dostępny, cenę za miesiąc zarządzania rozwiązaniem oraz to, czy plan będzie widoczny dla wszystkich, czy tylko dla konkretnych klientów (prywatna grupa odbiorców).

### <a name="markets"></a>Rynków

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla dowolnej lokalizacji rynku, w której chcesz udostępnić ten plan. Pole wyszukiwania i przycisk wyboru krajów "Umorzenie podatków", w których firma Microsoft przekazuje podatek od sprzedaży i korzystać z niego w twoim imieniu, są dołączone do pomocy.

Jeśli masz już ustawione ceny planu w dolarach amerykańskich (USD) i dodasz inną lokalizację rynkową, cena nowego rynku zostanie obliczona zgodnie z aktualnymi kursami wymiany. Zawsze sprawdzaj cenę dla każdego rynku przed opublikowaniem. Ceny można przeglądać za pomocą linku "Ceny eksportowe (xlsx)" po zapisaniu zmian.

### <a name="pricing"></a>Cennik

Podaj cenę miesięczną dla tego planu.  Ta cena jest dodatkiem do dowolnej infrastruktury platformy Azure lub kosztów oprogramowania zgodnie z rzeczywistym użyciem poniesionych przez zasoby wdrożone przez to rozwiązanie.

Ceny ustalone w walucie lokalnej (USD = Dolar amerykański) są przeliczane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących kursów wymiany dostępnych podczas konfiguracji. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny cen i przeglądając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na poszczególnych rynkach, zmodyfikuj i zaimportuj arkusz kalkulacyjny cen. 

>[!Note]
>Aby włączyć eksport danych cenowych, należy najpierw zapisać zmiany cen.

Przejrzyj uważnie ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co może się zmienić po opublikowaniu planu.  

>[!Note]
>Po opublikowaniu ceny na rynku w planie nie można jej później zmienić.

### <a name="plan-audience"></a>Zaplanuj grupę odbiorców

Możesz skonfigurować każdy plan tak, aby był widoczny dla wszystkich lub dla wybranych przez Ciebie odbiorców. Członkostwo w tej ograniczonej grupy odbiorców można przypisać przy użyciu identyfikatorów subskrypcji platformy Azure.

**Prywatność / To jest prywatny plan** (opcjonalne pole wyboru) - Zaznacz to pole wyboru, aby twój plan był prywatny i widoczny tylko dla wybranych odbiorców. Po opublikowaniu planu prywatnego możesz zaktualizować odbiorców lub udostępnić plan wszystkim. Gdy plan zostanie opublikowany jako widoczny dla wszystkich, musi pozostać widoczny dla wszystkich. (Planu nie można ponownie skonfigurować jako planu prywatnego).

**Ograniczona liczba odbiorców (identyfikatory subskrypcji platformy Azure)** — przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego. Program Access jest przypisywany przy użyciu identyfikatorów subskrypcji platformy Azure z opcją do uwzględnienia opisu każdego przypisanego identyfikatora subskrypcji platformy Azure. W przypadku importowania pliku arkusza kalkulacyjnego csv można dodać maksymalnie 10 identyfikatorów subskrypcji lub 20 000 identyfikatorów subskrypcji.  Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a litery muszą być małe litery.

>[!Note]
>Grupa odbiorców prywatnych (lub grupa odbiorców z ograniczeniami) różni się od grupy odbiorców w wersji zapoznawczej zdefiniowanej na karcie [**Podgląd.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Grupa odbiorców w wersji zapoznawczej ma dostęp do twojej oferty *przed* opublikowaniem oferty na żywo na rynku. Podczas gdy oznaczenie odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlać wszystkie plany (prywatne lub nie) do celów weryfikacji.

## <a name="technical-configuration"></a>Konfiguracja techniczna 

Karta **konfiguracji technicznej** umożliwia przekazanie pakietu wdrażania, który umożliwi klientom wdrożenie planu.

>[!Note]
>Ta karta nie będzie widoczna, jeśli skonfigurowano ten plan do ponownego użycia pakietów z innego planu na karcie **Ustawienia planu.**

### <a name="package-details"></a>Szczegóły pakietu

Podasada **Szczegóły pakietu** umożliwia edycję wersji roboczej konfiguracji technicznej.

***Wersja*** — przypisz bieżącą wersję konfiguracji technicznej.  Przyrost tej wersji za każdym razem, gdy publikujesz zmianę na tej stronie. Wersja musi być `{integer}.{integer}.{integer}`w formacie .

***Plik*** pakietu`.zip`( ) - Ten pakiet zawiera wszystkie pliki szablonów potrzebne do tego `.zip` planu, a także wszelkie dodatkowe zasoby, spakowane jako plik.

Wszystkie pakiety planu aplikacji platformy Azure muszą zawierać `.zip` te dwa pliki w folderze głównym archiwum:

* Plik szablonu Menedżera zasobów o nazwie [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Ten szablon automatyzuje wdrażanie zasobów do subskrypcji platformy Azure klientów.  Przykłady szablonów Usługi Resource Manager można znaleźć w [galerii Szablonów szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/) lub w odpowiednim usłudze GitHub: Repozytorium [szablonów szybkiego startu usługi Azure Resource Manager.](https://github.com/azure/azure-quickstart-templates)

* Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Wszystkie nowe oferty aplikacji platformy Azure muszą również zawierać identyfikator GUID [atrybucji klienta partnera platformy Azure.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)

### <a name="previously-published-packages"></a>Wcześniej opublikowane pakiety 

Podasada **Wcześniej opublikowane pakiety** umożliwia wyświetlanie wszystkich opublikowanych wersji konfiguracji technicznej.

## <a name="technical-configuration-managed-application-plans-only"></a>Konfiguracja techniczna (tylko plany aplikacji zarządzanych)

Plany aplikacji zarządzanych mają dodatkową złożoność na karcie **Konfiguracja techniczna** poza opisanymi powyżej polami pliku **Wersja** i **Pakiet.** 

### <a name="enable-just-in-time-jit-access"></a>Włącz dostęp just-in-time (JIT)

Wybierz tę opcję, aby włączyć dostęp just-in-time (JIT) dla tego planu.  Dostęp JIT umożliwia żądanie podwyższonego dostępu do zasobów aplikacji zarządzanej w celu rozwiązywania problemów lub konserwacji. Zawsze masz dostęp tylko do odczytu do zasobów, ale przez określony okres możesz mieć większy dostęp.  Aby uzyskać więcej informacji, zobacz [Włączanie i żądanie dostępu just-in-time dla aplikacji zarządzanych platformy Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Aby wymagać, aby konsumenci aplikacji zarządzanej udzielili stałego dostępu do konta, pozostaw tę opcję niezaznaczoną.

>[!Note]
>Pamiętaj, aby `createUiDefinition.json` zaktualizować plik, aby obsługiwać tę funkcję.  

### <a name="deployment-mode"></a>Tryb wdrażania

Wybierz, czy podczas wdrażania tego planu należy skonfigurować tryb wdrażania **complete,** **czy przyrostowy:** 

* W **trybie kompletnym**ponowne wdrożenie aplikacji przez klienta spowoduje usunięcie zasobów w zarządzanej grupie zasobów, jeśli `mainTemplate.json`zasoby nie zostaną zdefiniowane w pliku . 
* W **trybie przyrostowym**ponowne wdrożenie aplikacji pozostawia istniejące zasoby bez zmian.

Aby dowiedzieć się więcej o trybach wdrażania, zobacz [Tryby wdrażania usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>Adres URL punktu końcowego powiadomień

Określ punkt końcowy elementu końcowego https webhook, aby otrzymywać powiadomienia o wszystkich operacjach CRUD w wystąpieniach aplikacji zarządzanych tej wersji planu.

### <a name="customize-allowed-customer-actions"></a>Dostosowywanie dozwolonych akcji klienta

Wybierz tę opcję, aby określić, które akcje, które`*/read`klienci mogą wykonywać na zarządzanych zasobach, oprócz akcji " ", które są domyślnie dostępne. 

Wymień dodatkowe akcje, które chcesz umożliwić klientowi wykonywanie tutaj, oddzielone średnikami.  Aby uzyskać więcej informacji, zobacz [Opis odmów przydziałów dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Aby uzyskać dostępne akcje, zobacz [Operacje dostawcy zasobów usługi Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Na przykład, aby umożliwić konsumentom ponowne `Microsoft.Compute/virtualMachines/restart/action` uruchomienie maszyn wirtualnych, dodaj do dozwolonych akcji.

### <a name="global-azure--azure-government-cloud"></a>Globalna platforma Azure / chmura azure dla instytucji rządowych

Wskazać, kto powinien mieć dostęp do zarządzania do tej zarządzanej aplikacji w każdej obsługiwanej chmurze.  Użytkownicy, grupy lub aplikacje, którym chcesz uzyskać uprawnienia do zarządzanej grupy zasobów, są identyfikowane przy użyciu tożsamości usługi Azure Active Directory (AAD).

***Identyfikator dzierżawy usługi Azure Active Directory*** — identyfikator dzierżawy usługi AAD (znany również jako identyfikator katalogu) zawierający tożsamości użytkowników, grup lub aplikacji, którym chcesz udzielić uprawnień.  Identyfikator dzierżawy usługi AAD można znaleźć w witrynie Azure portal w [aplikacji Properties for Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autoryzacje*** — dodaj identyfikator obiektu usługi Azure Active Directory użytkownika, grupy lub aplikacji, którym chcesz uzyskać uprawnienia do zarządzanej grupy zasobów. Zidentyfikuj użytkownika według ich identyfikatora głównego, który można znaleźć w [bloku użytkowników usługi Azure Active Directory w witrynie Azure portal.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)

Dla każdego podmiotu zabezpieczeń wybierz jedną z wbudowanych ról usługi Azure AD z listy (Właściciel lub Współautor). Wybrana rola opisze uprawnienia podmiotu zabezpieczeń do zasobów w subskrypcji klienta. Aby uzyskać więcej informacji, zobacz temat [Wbudowane role dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC), zobacz [Wprowadzenie do rbac w witrynie Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Chociaż można dodać maksymalnie 100 autoryzacji na chmurę, zazwyczaj łatwiej jest utworzyć grupę użytkowników usługi Active Directory i określić jej identyfikator w "Identyfikatorze głównym".  Umożliwi to dodanie większej liczby użytkowników do grupy zarządzania po wdrożeniu planu i zmniejszenie konieczności aktualizowania planu tylko po to, aby dodać więcej autoryzacji.

### <a name="policy-settings"></a>Ustawienia zasad

Zastosuj [zasady platformy Azure](https://docs.microsoft.com/azure/governance/policy/overview) do aplikacji zarządzanej, aby określić wymagania dotyczące zgodności wdrożonego rozwiązania.  Definicje zasad i format wartości parametrów podano w artykule [Przykłady dla usługi Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).  Można skonfigurować maksymalnie pięć zasad i tylko jedno wystąpienie każdej opcji Zasady.  Niektóre zasady wymagają dodatkowych parametrów.  Standardowa jednostka SKU jest wymagana dla zasad inspekcji.  Nazwa zasad jest ograniczona do 50 znaków.

## <a name="co-sell"></a>Wspólna sprzedaż

Podanie informacji na karcie Cosell jest całkowicie opcjonalne do publikowania oferty. Jest to wymagane do osiągnięcia co-sell Ready i IP Co-sell Ready status. Podane informacje będą używane przez zespoły sprzedaży firmy Microsoft, aby dowiedzieć się więcej o rozwiązaniu podczas oceny jego dopasowania do potrzeb klienta. Nie jest dostępny bezpośrednio dla klientów.

Aby uzyskać więcej informacji na temat uzupełniania tej karty, zobacz [Opcja współsprzedaj w Centrum partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Jazda próbna

Karta **Dysk testowy** umożliwia skonfigurowanie demonstracji (lub "jazdy testowej"), która umożliwi klientom wypróbowanie oferty przed jej zakupem. Dowiedz się więcej w artykule [Co to jest jazda testna?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Jeśli nie chcesz już dostarczać dysku testowego dla swojej oferty, wróć do strony **Ustawienia oferty** i wyjdź ze strony **Włącz jazdę próbną**.

### <a name="technical-configuration"></a>Konfiguracja techniczna

Aplikacje platformy Azure z natury używają typu dysku testowego usługi Azure Resource Manager.  Aby uzyskać więcej informacji, [zobacz Konfiguracja techniczna dla dysku testowego usługi Azure Resource Manager.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć jazdę testową w Twoim imieniu, utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure. (Nie jest wymagane w przypadku dysków testowych usługi Power BI).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla usługi Azure Resource Manager i aplikacje logiki): wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure w celu raportowania użycia zasobów i rozliczeń. Zaleca się, aby rozważyć [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) do użycia w przypadku jazdy testowych, jeśli nie masz jeszcze. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [witryny Azure portal](https://portal.azure.com/) i przechodząc do karty **Subskrypcje** menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (np.

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wprowadź identyfikator [dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **Właściwości, a następnie poszukaj numeru **identyfikatora katalogu** na liście (np. 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy organizacji przy użyciu [https://www.whatismytenantid.com](https://www.whatismytenantid.com)adresu URL nazwy domeny pod adresem: .

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla usługi Dynamic 365): wprowadź nazwę usługi Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [witryny Azure portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona pod nazwą konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagany): wprowadź identyfikator aplikacji usługi Azure Active Directory (AD). [application ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Aby znaleźć ten identyfikator, zaloguj się do [portalu Azure](https://portal.azure.com/), wybierz kartę Usługi Active Directory w menu po lewej stronie, wybierz **pozycję Rejestracje aplikacji**, a następnie poszukaj numeru **identyfikatora aplikacji** na liście (np. 50c464d3-4930-494c-963c-1e951d15360e).

- **Klucz tajny klienta aplikacji usługi Azure AD** (wymagany): wprowadź klucz tajny [klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD . Aby znaleźć tę wartość, zaloguj się do [witryny Azure portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **Rejestracje aplikacji**, a następnie wybierz aplikację dysku testowego. Następnie wybierz **pozycję Certyfikaty i wpisy tajne**, wybierz pozycję Nowy klucz tajny **klienta**, wprowadź opis, wybierz pozycję **Nigdy w** obszarze **Wygasa**, a następnie wybierz pozycję **Dodaj**. Pamiętaj, aby skopiować wartość. (Nie odchodź od strony przed skopiowaniem wartości, w przeciwnym razie nie będziesz mieć dostępu do tej wartości).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji!

### <a name="test-drive-listings-optional"></a>Lista dysków testowych (opcjonalnie)

Opcja **Aukcje na dysku testowym znajdująca** się na karcie **Dysk testowy** wyświetla języki (i rynki), w których jest dostępna jazda próbna, obecnie dostępna jest w języku angielskim (Stany Zjednoczone). Ponadto na tej stronie jest wyświetlany stan listy specyficznej dla języka oraz data/godzina dodania. Musisz zdefiniować szczegóły jazdy próbnej (opis, instrukcja obsługi, filmy itp.) dla każdego języka / rynku.

- **Opis** (wymagane): Opisz jazdę próbną, co zostanie zademonstrowane, cele, z jakim użytkownik może eksperymentować, funkcje do zbadania i wszelkie istotne informacje, które pomogą użytkownikowi ustalić, czy chcesz uzyskać ofertę. W tym polu można wprowadzić maksymalnie 3000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla usług Azure Resource Manager i logic dysków testowych): Wyjaśnij, co klient musi wiedzieć, aby uzyskać dostęp do tej dysku testowego i korzystać z niego. Zapoznaj się ze scenariuszem korzystania z oferty i dokładnie tym, co klient powinien wiedzieć, aby uzyskać dostęp do funkcji podczas jazdy próbnego. W tym polu można wprowadzić maksymalnie 10 000 znaków tekstu.

- **Instrukcja obsługi** (wymagana): szczegółowy przewodnik po doświadczeniu jazdy próbnej. Instrukcja obsługi powinna obejmować dokładnie to, co chcesz, aby klient uzyskał z powodu wystąpienia jazdy próbnej i służyć jako punkt odniesienia dla wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i być nazwany (maks. 255 znaków) po przesłaniu.

- **Filmy: dodaj filmy** (opcjonalnie): filmy można przesyłać do YouTube lub Vimeo i odwoływać się tutaj za pomocą linku i obrazu miniatur (533 x 324 piksele), aby klient mógł wyświetlić informacje, które pomogą mu lepiej zrozumieć jazdę próbną, w tym jak skutecznie korzystać z funkcji oferty i zrozumieć scenariusze, które podkreślają ich zalety.
  - **Nazwa** (wymagana)
  - **Adres URL (tylko YouTube lub Vimeo)** (wymagany)
  - **Miniatura (533 x 324 px):** Plik obrazu musi być w formacie PNG.

Wybierz **pozycję Zapisz** po wypełnieniu tych pól.

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po wypełnieniu wszystkich wymaganych sekcji oferty wybierz **pozycję publikuj** w prawym górnym rogu portalu. Zostaniesz przekierowany do strony **Recenzja i publikowanie.** 

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia każdej sekcji oferty.
    - *Nie rozpoczęto* - oznacza, że sekcja nie została dotknięta i musi zostać ukończona.
    - *Niekompletne* - oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga więcej informacji, które mają być dostarczone. Wróć do sekcji i zaktualizuj ją.
    - *Complete* - oznacza, że sekcja jest kompletna, wszystkie wymagane dane zostały dostarczone i nie ma żadnych błędów. Wszystkie sekcje oferty muszą być w pełnym stanie, zanim będzie można złożyć ofertę.
- Przekaż zespołowi certyfikacyjne instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie testowana, oprócz wszelkich dodatkowych notatek przydatnych do zrozumienia aplikacji.
- Prześlij ofertę do publikacji, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Wróć do Centrum partnerskiego i wybierz **opcję Przejdź na żywo,** aby oferta opublikowała ofertę publicznie (lub prywatną ofertę dla odbiorców prywatnych).

### <a name="errors-and-review-feedback"></a>Błędy i opinie

Krok **ręcznego sprawdzania poprawności** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych (zwłaszcza szablonu usługi Azure Resource Manager), problemy są zazwyczaj przedstawiane jako łącza żądania ściągnięcia (PR). Szczegółowe informacje na temat wyświetlania tych analiz danych i reagowania na nie można znaleźć w części [Obsługa opinii dotyczących recenzji.](./azure-apps-review-feedback.md)

Jeśli napotkano błędy w co najmniej jednym z kroków publikowania, należy je poprawić i ponownie opublikować ofertę.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
