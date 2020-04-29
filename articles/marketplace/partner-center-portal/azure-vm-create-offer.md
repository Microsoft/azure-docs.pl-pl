---
title: Tworzenie oferty maszyny wirtualnej platformy Azure — Azure Marketplace
description: Dowiedz się, jak utworzyć ofertę maszyny wirtualnej w portalu komercyjnym.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869058"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Tworzenie oferty maszyny wirtualnej platformy Azure

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami maszyn wirtualnych platformy Azure z usługi portal Cloud Partner do Centrum partnerskiego. Dopóki Twoje oferty nie zostaną zmigrowane, nadal postępuj zgodnie z instrukcjami zawartymi w temacie [Tworzenie oferty maszyny wirtualnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) w Portal Cloud partner, aby zarządzać ofertami.

W tym artykule opisano sposób tworzenia i publikowania oferty maszyny wirtualnej platformy Azure w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/). Dotyczy zarówno maszyn wirtualnych opartych na systemie Windows, jak i Linux, które zawierają system operacyjny, wirtualny dysk twardy (VHD) oraz maksymalnie 16 dysków danych.

## <a name="introduction"></a>Wprowadzenie

### <a name="publishing-benefits"></a>Korzyści z publikowania

Publikowanie w witrynie Azure Marketplace ma następujące zalety:

- Promuj swoją firmę przy użyciu marki Microsoft
- Docieraj do ponad 100 000 000 klientów z 365 pakietem Dynamics 200 000 365 i z platformą Azure
- Zyskaj wysokiej jakości potencjalni klienci z tych rynków
- Zyskaj swoje usługi przez zespoły pól i telesprzedaży firmy Microsoft

### <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli jeszcze tego nie zrobiono, zapoznaj się z [przewodnikiem publikowania oferty maszyn wirtualnych](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) i tym materiałem maszyny wirtualnej platformy Azure:

- Przewodniki Szybki Start
  - [Szablony przewodników Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/)
  - [Szablony szybkiego startu platformy Azure w witrynie GitHub](https://github.com/azure/azure-quickstart-templates)
- Samouczki
  - [Maszyny wirtualne z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Maszyny wirtualne z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Samples
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell dla maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell dla maszyn wirtualnych z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Podstawy wiedzy technicznej

Projektowanie, kompilowanie i testowanie tych zasobów zabiera czas i wymaga znajomości wiedzy technicznej platformy Azure oraz technologii używanych do tworzenia oferty.

Zespół inżynieryjny powinien zrozumieć następujące technologie firmy Microsoft:

- Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/)
- [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Tworzenie oferty maszyny wirtualnej platformy Azure

Aby można było utworzyć ofertę maszyny wirtualnej platformy Azure, musisz mieć komercyjne konto w witrynie Marketplace w centrum partnerskim. Jeśli jeszcze tego nie zrobiono, zobacz [Tworzenie komercyjnego konta witryny Marketplace w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home), a następnie z górnego menu wybierz pozycję **pulpit nawigacyjny**.
2. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **komercyjna witryna Marketplace**, a następnie kliknij pozycję **Przegląd**.
3. Na stronie **Przegląd** wybierz pozycję **+ Nowa oferta**, a następnie pozycję **maszyna wirtualna platformy Azure**. Zostanie wyświetlone okno dialogowe **Nowa oferta** .

    ![Ilustruje stronę przegląd w centrum partnerskim z wybraną pozycją nowy przycisk oferta i oferta Azure Virtual Machine.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web oferty witryny Marketplace oraz w Azure PowerShell i interfejsu wiersza polecenia platformy Azure, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczone do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1** tutaj, adres internetowy oferty będzie miał `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`wartość.
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w centrum partnerskim. Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości widocznych dla klientów.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="test-drive"></a>Wersja testowa

Skonfiguruj demonstrację (Test Drive), która umożliwia klientom wypróbowanie oferty przed jej zakupem. Aby utworzyć środowisko demonstracyjne, które umożliwia klientom wypróbowanie oferty przez ustalony okres, zobacz temat [testowanie oferty w komercyjnej witrynie Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Aby włączyć dysk testowy, zaznacz pole wyboru **Włącz dysk testowy** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Dodatkowe zasoby dotyczące stacji testowych:

- [Najlepsze rozwiązania techniczne](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Najlepsze rozwiązania marketingowe](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Przegląd dysków testowych](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

### <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Po opublikowaniu oferty na komercyjnym rynku w centrum partnerskim połącz ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu. Połączenie z programem CRM jest wymagane, jeśli zostanie włączone **testowanie dysku** (patrz wcześniejsza sekcja), w przeciwnym razie jest to opcjonalne.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli Twój system CRM nie jest wymieniony powyżej, użyj [tabeli platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów. Następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu go w centrum partnerskim sprawdzimy połączenie i wyślesz potencjalnego klienta. Podczas wyświetlania podglądu oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując samodzielnie wdrożyć ofertę w środowisku wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, więc nie utracisz żadnych potencjalnych klientów.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych, które obsługują Twoją ofertę.

### <a name="categories"></a>Kategorie

Wybierz co najmniej jedną i maksymalnie pięć kategorii. Te kategorie służą do złożenia oferty w odpowiednich obszarach wyszukiwania w portalu Marketplace. W opisie oferty Wyjaśnij, w jaki sposób oferta obsługuje te kategorie. Oferty maszyn wirtualnych są wyświetlane w kategorii **obliczenia** w portalu Azure Marketplace.

### <a name="legal"></a>Informacje prawne

Musisz podać warunki i postanowienia oferty. Dostępne są dwie opcje:

- Korzystanie z własnych warunków i postanowień
- Korzystanie z standardowej umowy dotyczącej portalu komercyjnego firmy Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Korzystanie z własnych warunków i postanowień

Aby zapewnić własne niestandardowe warunki i postanowienia, wprowadź do 10 000 znaków tekstu w polu **warunki i** postanowienia. Jeśli potrzebujesz dłuższego opisu, wprowadź pojedynczy adres internetowy wskazujący, gdzie można znaleźć warunki i postanowienia. Będzie ona wyświetlana klientom jako aktywne łącze.

Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Korzystanie z standardowej umowy dotyczącej portalu komercyjnego firmy Microsoft

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt dla komercyjnego rynku. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą tylko raz odczytywać i akceptować je, a nie musisz tworzyć niestandardowych warunków i postanowień.

Użyj standardowej umowy, zaznaczając pole wyboru **Użyj standardowej umowy dla komercyjnej witryny Microsoft Marketplace** , a następnie **Zaakceptuj** ją w oknie podręcznym (może być konieczne przewinięcie w górę, aby zobaczyć).

![Ilustruje stronę przegląd w centrum partnerskim z wybraną pozycją Nowa oferta i oferta usługi konsultingowej.](media/use-standard-contract.png)

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie możesz użyć własnych niestandardowych warunków i postanowień. Oferuj swoje rozwiązanie w ramach standardowej umowy **lub** w ramach własnych warunków i postanowień.

Aby dowiedzieć się więcej na temat standardowej umowy, zobacz temat standardowy kontrakt dla [komercyjnego portalu Microsoft Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). Możesz pobrać [kontrakt standardowy](https://go.microsoft.com/fwlink/?linkid=2041178) jako plik PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

##### <a name="standard-contract-amendments"></a>Standardowe zmiany kontraktu

Standardowe zmiany kontraktu pozwalają wybrać standardowe warunki umowy dla uproszczenia i utworzyć warunki dla produktu lub firmy. Klienci muszą jedynie przejrzeć zmiany w umowie, jeśli zostały już przejrzane i zaakceptowały umowę standardową firmy Microsoft. Dostępne są dwa rodzaje zmian: uniwersalne i niestandardowe.

**Uniwersalne zmiany** — są one stosowane uniwersalnie do standardowej umowy dla wszystkich klientów. Są one widoczne dla każdego klienta oferty w przepływie zakupu. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki. Możesz wprowadzić pojedynczą zmianę uniwersalną dla każdej oferty. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w AppSource, w witrynie Azure Marketplace i/lub Azure Portal podczas odnajdywania i przepływu zakupów.

**Zmiany niestandardowe** — są to specjalne zmiany w standardowej umowie, które są przeznaczone dla określonych klientów za pomocą identyfikatorów dzierżaw platformy Azure. Możesz wybrać dzierżawcę, który ma być celem. Tylko klienci z dzierżawy będą widzieć niestandardowe warunki zmiany w przepływie zakupu oferty. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej i poprawki.

Zacznij od wybrania pozycji **Dodaj niestandardowe warunki zmiany (maks. 10)**. Możesz dostarczyć do dziesięciu postanowień w ramach oferty.

- **Niestandardowe warunki zmiany** — wprowadź własne warunki zmiany w polu niestandardowe warunki zmiany. Można wprowadzić nieograniczoną liczbę znaków. Tylko klienci z identyfikatorów dzierżawy określonych dla tych terminów niestandardowych będą widzieć je w przepływie zakupu oferty w Azure Portal.
- **Identyfikatory dzierżawców** (wymagane) — Każda Poprawka niestandardowa może być przeznaczona do maksymalnie 20 identyfikatorów dzierżawy. W przypadku dodania niestandardowej zmiany należy podać co najmniej jeden identyfikator dzierżawy, który identyfikuje klienta na platformie Azure. Klient może znaleźć Cię na platformie Azure w obszarze, a następnie właściwości. Wartość identyfikatora katalogu to identyfikator dzierżawy (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również znaleźć identyfikator dzierżawy w organizacji, używając adresu sieci Web nazwy domeny w obszarze [co to jest mój Microsoft Azure i identyfikator dzierżawy pakietu Office 365?](https://www.whatismytenantid.com/).
- **Opis** (opcjonalnie) — podaj przyjazny opis identyfikatora dzierżawy, który pomaga zidentyfikować klienta, którego celem jest zmiana.

> [!NOTE]
> Te dwa typy zmian stosują się między sobą. Klienci z niestandardowymi zmianami otrzymają także uniwersalną zmianę do standardowej umowy podczas zakupu.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Na tej stronie można zdefiniować szczegóły oferty, takie jak nazwa oferty, opis, linki i kontakty.

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu i warunki użytkowania) nie jest wymagana w języku angielskim, o ile opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Możesz również podać _przydatny adres internetowy linku_ , aby zaoferować zawartość w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

### <a name="marketplace-details"></a>Szczegóły witryny Marketplace

#### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu jest pokazywana klientom jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i można zawierać symbole towarowe i praw autorskich)
- Nie może być dłuższa niż 50 znaków
- Nie może zawierać znaków emoji.

#### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Krótki opis oferty. Może to być maksymalnie 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

#### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty. Może to być maksymalnie 256 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

#### <a name="description"></a>Opis

Podaj szczegółowy opis oferty, do 3 000 znaków. Ten komunikat jest wyświetlany klientom w przeglądzie komercyjnej aukcji Marketplace.

W opisie Uwzględnij co najmniej jeden z następujących elementów:

- Wartość i kluczowe korzyści wynikające z oferty
- Skojarzenia kategorii lub branżowych
- Szanse zakupu w aplikacji
- Wszelkie wymagane ujawnienie

Oto kilka porad dotyczących pisania opisu:

- Jasno opisz wartość oferty w pierwszych kilku zdaniach opisu. Uwzględnij następujące elementy:
  - Opis oferty.
  - Typ użytkownika, który korzysta z oferty.
  - Wymagania klientów lub problemy z ofertą.
- Należy pamiętać, że pierwsze niektóre zdania mogą być wyświetlane w wynikach wyszukiwania.
- Nie należy polegać na funkcjach i funkcjach, które umożliwiają sprzedawanie oferty. Zamiast tego należy skoncentrować się na wartości, którą oferuje oferta.
- Używaj słów specyficznych dla branży lub korzyści.

Aby zwiększyć atrakcyjność opisu oferty, użyj edytora tekstu sformatowanego do formatowania opisu. Edytor tekstu sformatowanego pozwala dodawać numery, punktory, pogrubienie, kursywę i wcięcia, aby zwiększyć czytelność opisu.

![Ilustruje stronę przegląd w centrum partnerskim z wybraną pozycją Nowa oferta i oferta usługi konsultingowej.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres internetowy (URL) do zasad zachowania poufności informacji organizacji. Upewnij się, że oferta jest zgodna z przepisami i przepisami dotyczącymi ochrony prywatności. Należy również opublikować prawidłowe zasady zachowania poufności informacji w witrynie sieci Web.

### <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące Twojej oferty. Aby dodać łącze, wybierz pozycję **+ Dodaj łącze** , a następnie wykonaj następujące pola:

- **Nazwa** — klienci będą widzieli nazwę na stronie szczegółów.
- **Link (adres URL)** — wprowadź link dla klientów, aby wyświetlić dokument w trybie online.

### <a name="customer-support-links"></a>Linki obsługi klienta

Podaj witrynę sieci Web pomocy technicznej, w której klienci mogą skontaktować się z zespołem pomocy technicznej.

- Witryna internetowa pomocy technicznej platformy Azure
- Witryna sieci Web pomocy technicznej Azure Government

### <a name="partner-support-contact"></a>Kontakt z pomocą techniczną partnera

Podaj informacje kontaktowe dla partnerów firmy Microsoft, które mają być używane, gdy klienci otworzą bilet pomocy technicznej. Ta wartość nie zostanie wyświetlona na liście w portalu Marketplace.

- Nazwa
- Poczta e-mail
- Telefon

### <a name="engineering-contact"></a>Kontakt inżynieryjny

Podaj informacje kontaktowe firmy Microsoft, które mają być używane w przypadku problemów z ofertą, w tym problemy z certyfikatem. Ta wartość nie zostanie wyświetlona na liście w portalu Marketplace.

- Nazwa
- Poczta e-mail
- Telefon

### <a name="marketplace-media"></a>Multimedia dla portalu Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Nierozmyte obrazy spowodują odrzucenie zgłoszenia.

>[!Note]
>Jeśli występuje problem z przekazywaniem plików, upewnij się, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="marketplace-logos"></a>Logo witryny Marketplace

Udostępniaj pliki PNG logo oferty w następujących czterech rozmiarach pikseli:

- **Mały** (48 x 48)
- **Średni** (90 x 90)
- **Duże** (216 x 216)
- **Szeroki** (255 x 115)

Wszystkie cztery logo są wymagane i są używane w różnych miejscach na liście w portalu Marketplace.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy zrzut ekranu musi mieć 1280 x 720 pikseli w rozmiarze i formacie PNG. Należy również dodać podpis, aby opisać zrzut ekranu.

#### <a name="videos"></a>Filmy wideo

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Powinny one być hostowane w zewnętrznej usłudze wideo. Wprowadź nazwę pliku wideo, adres sieci Web i obraz miniatury PNG wideo o rozmiarze 1280 x 720 pikseli.

Aby uzyskać dodatkowe zasoby dotyczące aukcji z witryny Marketplace, zobacz [najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="preview"></a>Wersja zapoznawcza

Na karcie Podgląd Wybierz ograniczony **odbiorcę w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo w szerszej grupie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w wersji zapoznawczej wybierz pozycję **Przejdź na żywo** , aby opublikować ofertę do komercyjnych odbiorców w portalu Marketplace.

Odbiorca w wersji zapoznawczej jest identyfikowany przez identyfikatory GUID subskrypcji platformy Azure, a także opcjonalny opis dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli Twoja oferta jest już aktywna, możesz nadal zdefiniować odbiorcę w wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo na rynku. Mogą oni wyświetlać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po opublikowaniu oferty w portalu Marketplace. Prywatni odbiorcy (zdefiniowani na karcie **ceny i dostępność** ) mają wyłączny dostęp do konkretnego planu.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji, Zaplanuj przegląd.

## <a name="plan-overview"></a>Przegląd planu

W ramach tej samej oferty w centrum partnerskim można udostępnić różne opcje planu. Te plany były wcześniej określane jako jednostki SKU. Oferta wymaga co najmniej jednego planu, który może różnić się pod względem odbiorców zysków, regionów platformy Azure, funkcji lub obrazów maszyn wirtualnych.

Po utworzeniu planów na karcie **Przegląd planu** jest wyświetlana wartość:

- Nazwy planów
- Modele licencji
- Odbiorcy (publiczna lub prywatna)
- Bieżący stan publikowania
- Dostępne akcje

Akcje dostępne w przeglądzie planu różnią się w zależności od bieżącego stanu planu. Obejmują one następujące raporty:

- **Usuń wersję roboczą** — Jeśli plan jest stanem wersja robocza
- **Przestań sprzedawać plan** lub **zsynchronizuj odbiorców prywatnych** — Jeśli stan planu jest opublikowany na żywo

### <a name="create-new-plan"></a>Utwórz nowy plan

Wybierz pozycję **+ Utwórz nowy plan** u góry. Zostanie wyświetlone okno dialogowe **nowy plan** .

W polu **identyfikator planu** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie sieci Web produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń i maksymalnie 50 znaków.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

W polu **Nazwa planu** wprowadź nazwę dla tego planu. Klienci widzą tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę, która jasno wskazuje różnice w każdym planie. Na przykład możesz użyć **systemu Windows Server** z planami **płatność zgodnie z rzeczywistym**użyciem, **BYOL**, **Advanced**i **Enterprise**.

Wybierz przycisk **Utwórz**.

### <a name="plan-setup"></a>Konfigurowanie planu

Ustaw konfigurację wysokiego poziomu dla typu planu, niezależnie od tego, czy jest ona ponownie wykorzystywana przez konfigurację techniczną z innego planu, a w jakich regionach platformy Azure ma być dostępny plan. Wybrane tutaj ustawienia określają, które pola są wyświetlane na innych kartach dla tego samego planu.

#### <a name="reuse-technical-configuration"></a>Ponownie Użyj konfiguracji technicznej

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie korzysta z konfiguracji technicznej z innego planu**. Ta opcja umożliwia wybranie jednego z innych planów tego samego typu dla tej oferty i ponowne użycie konfiguracji technicznej.

> [!NOTE]
> W przypadku ponownego użycia konfiguracji technicznej z poziomu innego planu, cała karta **konfiguracja techniczna** znika z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, zostaną użyte również dla tego planu. Nie można zmienić tego ustawienia po opublikowaniu tego planu.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Plan musi być dostępny w co najmniej jednym regionie świadczenia usługi Azure.

Wybierz opcję **globalną platformy Azure** , aby udostępnić plan klientom we wszystkich publicznych regionach świadczenia usługi Azure z komercyjną integracją z portalu Marketplace. Aby uzyskać szczegółowe informacje, zobacz [dostępność geograficzna i obsługa waluty](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Wybierz opcję **Azure Government** , aby udostępnić plan w regionie [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Ten region zapewnia klientom kontrolowany dostęp do jednostek federalnych, stanowych, lokalnych i plemienne, a także partnerów uprawnionych do ich świadczenia. Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Przed opublikowaniem w celu [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)Przetestuj i zweryfikuj swój plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, zażądaj konta próbnego od [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonym regionie świadczenia usługi Azure nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Ta opcja jest widoczna tylko w przypadku wybrania **Azure Government** w obszarze **regionów świadczenia usługi Azure**.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="plan-listing"></a>Zaplanuj listę

Jest to miejsce, w którym można skonfigurować szczegółowe informacje dotyczące planu. Na tej karcie są wyświetlane określone informacje, które mogą się różnić w zależności od planów w ramach tej samej oferty.

#### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą, która została nadana przez Ciebie podczas jego tworzenia. Ta nazwa jest wyświetlana w portalu Marketplace jako tytuł tego planu i jest ograniczona do 100 znaków.

#### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu (nie oferty). To podsumowanie jest ograniczone do 100 znaków.

#### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, a także różnice między planami w ramach oferty. Nie opisz oferty, tylko plan. Opis planu może zawierać maksymalnie 2 000 znaków.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="pricing-and-availability"></a>Cennik i dostępność

Na tej karcie skonfigurujesz następujące elementy:

- Rynki, w których ten plan będzie dostępny
- Cena za godzinę
- Określa, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (odbiorców prywatnych).

#### <a name="markets"></a>Wprowadza

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla każdej lokalizacji rynkowej, w której plan ma być dostępny do zakupu (Użytkownicy na tych rynkach mogą nadal wdrażać oferty we wszystkich regionach platformy Azure wybranych w obszarze **[Konfiguracja planu](#plan-setup)**). Przycisk **podatek z podatkiem** przedstawia kraje, w których firma Microsoft przekazuje sprzedaż i korzysta z podatku w Twoim imieniu. Publikowanie w Chinach jest ograniczone do planów, które są **bezpłatne** lub **Bring Your Own License** (BYOL).

Jeśli już ustawisz ceny dla planu w Stany Zjednoczone dolarów (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku zostanie obliczona zgodnie z bieżącymi stawkami za wymianę. Zawsze sprawdzaj ceny poszczególnych rynków przed opublikowaniem. Po zapisaniu zmian zapoznaj się z cenami przy użyciu linku **ceny eksport (xlsx)** .

Po usunięciu rynku klienci z tego rynku korzystający z aktywnych wdrożeń nie będą mogli tworzyć nowych wdrożeń ani skalować istniejących wdrożeń. Nie wpłynie to na istniejące wdrożenia.

#### <a name="pricing"></a>Cennik

**Model licencji** — wybierz **miesięczny plan rozliczeniowy oparty na użyciu** , aby skonfigurować Cennik dla tego planu lub **Bring Your Own License** , aby umożliwić klientom korzystanie z tego planu w ramach istniejącej licencji.

W przypadku miesięcznego planu rozliczeniowego opartego na użyciu należy użyć jednej z następujących trzech opcji wprowadzania cen:

- **Na rdzeń** — Podaj cenę za rdzeń w Stany Zjednoczone dolarów (USD). Obliczamy ceny według rozmiaru rdzenia i Konwertujemy na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Na rozmiar podstawowy** — podaj ceny na rozmiar podstawowy w USD. Ceny zostaną przekonwertowane na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Na rynek i rozmiar podstawowy** — zapewniają ceny za każdy rozmiar podstawowy dla wszystkich rynków. Możesz zaimportować ceny z arkusza kalkulacyjnego.

> [!NOTE]
> Zapisz zmiany cen, aby włączyć eksport danych cen. Po opublikowaniu ceny na rynku planu nie można jej później zmienić. Upewnij się, że te ceny są odpowiednie przed opublikowaniem, eksportując arkusz cen i sprawdzając cenę na każdym rynku.

#### <a name="free-trial"></a>Bezpłatna wersja próbna

Możesz zaoferować klientom jedną lub trzy miesiące bezpłatnej wersji próbnej.

#### <a name="visibility"></a>Widoczność

Każdy plan można zaprojektować jako widoczny dla wszystkich użytkowników lub tylko do wybranych użytkowników. Przypisz członkostwo w tych ograniczonych odbiorcach przy użyciu identyfikatorów subskrypcji platformy Azure.

**Publiczna** — Twój plan może być widoczny dla wszystkich użytkowników.

**Prywatni odbiorcy** — ustaw, aby Twój plan był widoczny tylko dla wybranych odbiorców. Po opublikowaniu jako planu prywatnego można zaktualizować odbiorców lub zmienić je na publiczną. Po dokonaniu publicznego planu musi on pozostać publiczny; nie można zmienić jej z powrotem na prywatną.

**Odbiorcy z ograniczeniami (identyfikatory subskrypcji platformy Azure)** — Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego przy użyciu identyfikatorów subskrypcji platformy Azure. Opcjonalnie Dołącz opis każdego przypisanego identyfikatora subskrypcji platformy Azure. Dodaj maksymalnie 10 identyfikatorów subskrypcji ręcznie lub 20 000 w przypadku importowania arkusza kalkulacyjnego CSV. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID i litery muszą być małymi literami.

> [!NOTE]
> Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie **Podgląd** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo w portalu Marketplace. Gdy wybór odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

#### <a name="hide-plan"></a>Ukryj plan

Jeśli Twoja maszyna wirtualna ma być używana tylko pośrednio, gdy jest przywoływana za pośrednictwem innego szablonu rozwiązania lub aplikacji zarządzanej, zaznacz to pole, aby opublikować swoją maszynę wirtualną, ale ukryć ją od klientów wyszukiwania bezpośrednio i przeglądania.

> [!NOTE]
> Ukryte plany nie obsługują linków podglądu.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="technical-configuration"></a>Konfiguracja techniczna

Podaj obrazy i inne właściwości techniczne skojarzone z tym planem. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Ta karta nie jest wyświetlana, jeśli ten plan został skonfigurowany tak, aby ponownie używał pakietów z innego planu na karcie **Konfiguracja planu** .

#### <a name="operating-system"></a>System operacyjny

- **Rodzina systemów operacyjnych** — wybierz z systemu operacyjnego **Windows** lub **Linux**
- **Wydanie** lub **dostawca** — wybieranie dostawcy systemu Windows w wersji lub Linux
- **Przyjazna nazwa** systemu operacyjnego — wybierz przyjazną nazwę system operacyjny. Ta nazwa jest widoczna dla klientów

#### <a name="recommended-vm-sizes"></a>Zalecane rozmiary maszyn wirtualnych

Wybierz maksymalnie sześć zalecanych rozmiarów maszyn wirtualnych do wyświetlenia w witrynie Azure Marketplace.

#### <a name="open-ports"></a>Otwieranie portów

Otwórz porty publiczne lub prywatne na wdrożonej maszynie wirtualnej.

#### <a name="storage-option-for-deployment"></a>Opcja magazynu dla wdrożenia

**Opcja wdrażania dysku** — wybierz rodzaj wdrożenia dysku, którego użytkownicy mogą używać podczas korzystania z maszyny wirtualnej. Firma Microsoft zaleca ograniczenie wdrożenia tylko do wdrożenia dysku zarządzanego.

#### <a name="properties"></a>Właściwości

**Obsługa przyspieszonej sieci** — wybierz, czy maszyna wirtualna obsługuje [przyspieszone sieci](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Obrazy maszyn wirtualnych

Podaj wersję dysku i identyfikator URI sygnatury dostępu współdzielonego dla obrazów maszyn wirtualnych. Dodaj do 16 dysków danych dla każdego obrazu maszyny wirtualnej. Dla danego planu należy podać tylko jedną nową wersję obrazu. Po opublikowaniu obrazu nie można go edytować, ale można go usunąć. Usunięcie wersji uniemożliwi nowym i istniejącym użytkownikom wdrażanie nowego wystąpienia usuniętej wersji.

- **Wersja dysku** to wersja obrazu, który jest udostępniany.
- **Identyfikator URI sygnatury dostępu współdzielonego** to lokalizacja w usłudze Azure Storage, w której zapisano wirtualny dysk twardy systemu operacyjnego.
- Obrazy dysków danych to również identyfikatory URI sygnatury dostępu współdzielonego dysku VHD przechowywane w usłudze Azure Storage.
- Dodaj tylko jeden obraz do każdego przesłania w planie.

Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Klienci nie mogą usunąć dysków, które są częścią obrazu w czasie wdrażania, ale mogą zawsze dodawać dyski w trakcie wdrażania lub po nim.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem i wróć do **omówienia planu**.

## <a name="resell-through-csps"></a>Odsprzedaż za poorednictwem dostawców CSP

Rozszerz zasięg oferty, udostępniając ją partnerom w programie [dostawcy rozwiązań w chmurze](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Wszystkie plany przenoszenia własnych licencji (BYOL) są automatycznie wybrały; Możesz wybrać opcję rezygnacji z planów BYOL.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive"></a>Wersja testowa

Skonfiguruj demonstrację (Test Drive), która umożliwia klientom wypróbowanie oferty przed jej zakupem. Aby utworzyć środowisko demonstracyjne, które umożliwia klientom wypróbowanie oferty przez ustalony okres, zobacz temat [testowanie oferty w komercyjnej witrynie Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Aby włączyć stację testową, zaznacz pole wyboru Włącz dysk testowy na karcie [Konfiguracja oferty](#test-drive) . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Dodatkowe zasoby dotyczące stacji testowych:

- Najlepsze rozwiązania techniczne
- Najlepsze rozwiązania marketingowe
- Informacje o testowaniu dysków (PDF; upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="review-and-publish"></a>Przejrzyj i Opublikuj

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać ją do przeglądu i publikacji.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i Opublikuj**.

Na tej stronie można:

- Zobacz stan ukończenia dla każdej sekcji oferty.
  - **Nie uruchomiono** — sekcja nie została uruchomiona i musi zostać zakończona.
  - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania dodatkowych informacji. Zapoznaj się z pozostałymi sekcjami w tym dokumencie, aby uzyskać wskazówki dotyczące aktualizowania tej sekcji.
  - **Ukończono** — sekcja jest kompletna i nie ma błędów. Aby można było przesłać ofertę, wszystkie sekcje oferty muszą zostać ukończone.
- **Uwagi dotyczące certyfikacji** — Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana. Podaj wszelkie dodatkowe uwagi pomocne przy zrozumieniu swojej aplikacji.

Aby przesłać ofertę do publikacji, wybierz pozycję **Przejrzyj i Opublikuj**.

Wyślemy Ci wiadomość e-mail z prośbą o poinformowanie o udostępnieniu wersji zapoznawczej oferty do przejrzenia i zatwierdzenia. Aby opublikować ofertę publicznie (lub w przypadku prywatnej oferty, w przypadku odbiorców prywatnych), przejdź do Centrum partnerskiego, Znajdź stronę **omówienia** oferty i wybierz pozycję **Przejdź na żywo**. Gdy publikowanie jest w toku, w górnej części strony zostanie wyświetlony stan oferty.

### <a name="errors-and-review-feedback"></a>Błędy i przejrzyj opinię

Krok **walidacji ręcznej** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych. Jeśli ten proces odzyskuje błędy oferty, zostanie wyświetlony Raport z certyfikatem zawierającym szczegóły dotyczące problemów. Po prostu wprowadź wymagane poprawki i ponownie Opublikuj swoją ofertę.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania oferty (zarówno w toku, jak i w trakcie) oraz czas trwania każdego kroku.

Ta strona zawiera linki do wykonywania operacji na tej ofercie w zależności od dokonanego wyboru. Przykład:

- Jeśli oferta jest ofertą typu wersja robocza, [Usuń wersję roboczą](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Jeśli oferta jest aktywna [, Zatrzymaj sprzedawanie oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Jeśli oferta jest dostępna w wersji zapoznawczej — [Przejdź na żywo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Jeśli nie ukończono wylogowania wydawcy — [Anulowanie publikowania](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Przykłady dla witryny Marketplace

W poniższych przykładach pokazano, jak oferta pojawia się w witrynie Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Szczegóły oferty witryny Azure Marketplace

![Przykład ekranu szczegółów oferty w witrynie Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Wyniki wyszukiwania w portalu Azure Marketplace

![Przykład ekranu szczegółów wyszukiwania w witrynie Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Szczegóły planu witryny Azure Marketplace

![Przykład ekranu szczegółów planu witryny Azure Marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Szczegóły oferty Azure Portal

![Przykład ekranu Azure Portal szczegóły oferty](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Wyniki wyszukiwania Azure Portal

![Przykład ekranu wyników wyszukiwania Azure Portal](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Szczegóły planu Azure Portal

![Przykład ekranu szczegółów planu Azure Portal](media/avm-create6.png)

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
