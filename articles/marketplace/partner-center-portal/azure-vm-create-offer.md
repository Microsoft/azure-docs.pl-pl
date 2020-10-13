---
title: Tworzenie oferty maszyny wirtualnej platformy Azure w witrynie Azure Marketplace
description: Dowiedz się, jak opublikować ofertę maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 08/07/2020
ms.openlocfilehash: 46749015d5141053eaea3f2f251bbba7d9455481
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709020"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Tworzenie oferty maszyny wirtualnej platformy Azure w witrynie Azure Marketplace

W tym artykule opisano sposób tworzenia i publikowania oferty maszyny wirtualnej platformy Azure w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/). Dotyczy zarówno maszyn wirtualnych opartych na systemie Windows, jak i Linux, które zawierają system operacyjny, wirtualny dysk twardy (VHD) oraz maksymalnie 16 dysków danych. 

Przed rozpoczęciem [Utwórz komercyjne konto witryny Marketplace w centrum partnerskim](create-account.md). Upewnij się, że Twoje konto zostało zarejestrowane w komercyjnym programie Marketplace.

## <a name="introduction"></a>Wprowadzenie

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Zalety publikowania w witrynie Azure Marketplace

Po opublikowaniu ofert w witrynie Azure Marketplace można:

- Promuj swoją firmę, korzystając z pomocy marki firmy Microsoft.
- Dotrze do ponad 100 000 000 Microsoft 365 i użytkowników systemu Dynamics 365 i więcej niż 200 000 organizacje.
- Uzyskuj wysokiej jakości potencjalni klienci z tych rynków.
- Zyskaj swoje usługi przez zespoły ds. sprzedaży i telesprzedaży firmy Microsoft.

### <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli jeszcze tego nie zrobiono, zapoznaj się z [przewodnikiem publikowania oferty maszyn wirtualnych](../marketplace-virtual-machines.md) i tym materiałem maszyny wirtualnej platformy Azure:

- Przewodniki Szybki Start
  - [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/)
  - [Szablony szybkiego startu platformy Azure w witrynie GitHub](https://github.com/azure/azure-quickstart-templates)
- Samouczki
  - [Maszyny wirtualne z systemem Linux](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Maszyny wirtualne z systemem Windows](../../virtual-machines/windows/tutorial-manage-vm.md)
- Samples
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/powershell-samples.md)
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell dla maszyn wirtualnych z systemem Windows](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>Podstawy wiedzy technicznej

Proces projektowania, kompilowania i testowania ofert trwa od czasu i wymaga znajomości zarówno platformy Azure, jak i technologii, które są używane do tworzenia oferty.

Zespół inżynieryjny powinien dysponować podstawową wiedzą i praktyczną wiedzę o następujących technologiach firmy Microsoft:

- [Usługi platformy Azure](https://azure.microsoft.com/services/)
- [Projektowanie i Architektura aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
2. W okienku po lewej stronie wybierz pozycję **komercyjne Omówienie witryny Marketplace**  >  **Overview**.
3. Na stronie **Przegląd** wybierz pozycję **Nowa oferta**  >  **Azure Virtual Machine**.

    ![Zrzut ekranu przedstawiający opcje menu po lewej stronie i przycisk "Nowa oferta".](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Po opublikowaniu oferty wszelkie zmiany wprowadzone w centrum partnerskim są wyświetlane w witrynie Azure Marketplace dopiero po ponownym opublikowaniu oferty. Pamiętaj, aby zawsze ponownie opublikować ofertę po wprowadzeniu w niej zmian.

## <a name="new-offer"></a>Nowa oferta

Wprowadź **Identyfikator oferty**. To jest unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie sieci Web oferty usługi Azure Marketplace oraz w Azure PowerShell i interfejsu wiersza polecenia platformy Azure, jeśli ma zastosowanie.
- Użyj tylko małych liter i cyfr. Identyfikator może zawierać łączniki i podkreślenia, ale nie spacje i jest ograniczony do 50 znaków. Jeśli na przykład wprowadzisz polecenie **test-Offer-1**, adres sieci Web oferty będzie wynosić `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Nie można zmienić identyfikatora oferty po wybraniu pozycji **Utwórz**.

Wprowadź **alias oferty**. Alias oferty to nazwa, która jest używana na potrzeby oferty w centrum partnerskim.

- Ta nazwa nie jest używana w portalu Azure Marketplace. Różni się od nazwy oferty i innych wartości, które są widoczne dla klientów.

Wybierz pozycję **Utwórz** , aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="test-drive"></a>Wersja testowa

Wersja *testowa* to świetny sposób prezentowania oferty potencjalnym klientom. Daje im opcję "Wypróbuj przed zakupem", co może pomóc w zwiększeniu liczby konwersji i wygenerowania wysoce kwalifikowanych potencjalnych klientów. Aby uzyskać więcej informacji, zobacz [co to jest dysk testowy?](../what-is-test-drive.md)

Aby włączyć dysk testowy przez stały okres czasu, zaznacz pole wyboru **Włącz dysk testowy** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Dodatkowe zasoby dotyczące stacji testowych:

- [Najlepsze rozwiązania techniczne](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Najlepsze rozwiązania marketingowe](../what-is-test-drive.md)
- [Pobierz przegląd dysku testowego](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Plik PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

### <a name="customer-leads"></a>Potencjalni klienci

Po opublikowaniu oferty na komercyjnym rynku z Centrum partnerskiego połącz ją z systemem zarządzania relacjami z klientami (CRM). Pozwala to na otrzymywanie informacji kontaktowych klienta, gdy tylko ktoś wyrazi zainteresowanie lub skorzysta z produktu. Połączenie z programem CRM jest wymagane, jeśli chcesz włączyć dysk testowy (zobacz poprzednią sekcję). W przeciwnym razie połączenie z programem CRM jest opcjonalne.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) dla zaangażowania klienta
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Jeśli Twój system CRM nie jest tutaj wymieniony, użyj [usługi Azure Table Storage](commercial-marketplace-lead-management-instructions-azure-table.md) lub [punktu końcowego HTTPS](commercial-marketplace-lead-management-instructions-https.md) do przechowywania danych potencjalnych klientów. Następnie wyeksportuj dane do systemu CRM.

1. Połącz ofertę z miejscem docelowym potencjalnego klienta podczas publikowania w centrum partnerskim.
1. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest skonfigurowane prawidłowo. Po opublikowaniu w centrum partnerskim firma Microsoft weryfikuje połączenie i wysyła do Ciebie potencjalnego klienta. Podczas przeglądania oferty przed jej rozpoczęciem możesz również przetestować połączenie z liderem, próbując samodzielnie wdrożyć ofertę w środowisku wersji zapoznawczej.
1. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta zostało zaktualizowane, aby nie utracić żadnych potencjalnych klientów.

1. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Na stronie **Właściwości** można zdefiniować kategorie, które są używane do grupowania oferty w witrynie Azure Marketplace, wersji aplikacji oraz umów prawnych, które obsługują Twoją ofertę.

### <a name="category"></a>Kategoria

Wybierz kategorie i podkategorie, aby umieścić ofertę w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby opisać, w jaki sposób oferta obsługuje te kategorie w opisie oferty. Wybierz pozycję:

- Co najmniej jeden i maksymalnie dwie kategorie, w tym podstawowa i pomocnicza Kategoria (opcjonalnie).
- Do dwóch podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli żadna Podkategoria nie ma zastosowania do oferty, wybierz pozycję **nie dotyczy**.

Zapoznaj się z pełną listą kategorii i podkategorii w artykule [Oferta z najlepszymi rozwiązaniami](../gtm-offer-listing-best-practices.md). Oferty maszyn wirtualnych są zawsze wyświetlane w kategorii **obliczenia** w witrynie Azure Marketplace.

### <a name="legal"></a>Informacje prawne

Musisz podać swoim klientom warunki i postanowienia oferty. Dostępne są dwie opcje:

- **Korzystanie z własnych warunków i postanowień**

   Aby zapewnić własne niestandardowe warunki i postanowienia, wprowadź do 10 000 znaków tekstu w polu **warunki i** postanowienia. Jeśli potrzebujesz dłuższego opisu, wprowadź pojedynczy adres internetowy dla warunków i postanowień. Będzie ona wyświetlana klientom jako aktywne łącze.

   Aby wypróbować ofertę, klienci muszą zaakceptować te warunki.

- **Korzystanie z standardowej umowy dotyczącej portalu komercyjnego firmy Microsoft**

   Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt dla komercyjnego rynku. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą odczytywać i akceptować je tylko raz, a nie musisz tworzyć niestandardowych warunków i postanowień.

   Użyj standardowej umowy, zaznaczając pole wyboru **Użyj kontraktu standardowego dla komercyjnej witryny Microsoft Marketplace** , a następnie w oknie podręcznym wybierz pozycję **Akceptuj** (aby wyświetlić ją, może być konieczne przewinięcie w górę).

   ![Zrzut ekranu przedstawiający okienko prawne w centrum partnerskim z polem wyboru "Użyj standardowej umowy dla portalu komercyjnego firmy Microsoft".](media/use-standard-contract.png)

  > [!NOTE]
  > Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie można użyć własnych niestandardowych warunków i postanowień. Możesz zaoferować rozwiązanie w ramach standardowej umowy lub własnych warunków i postanowień.

  Aby uzyskać więcej informacji, zobacz temat [Standardowy kontrakt dla komercyjnego portalu Microsoft Marketplace](../standard-contract.md). Pobierz plik PDF z [kontraktem standardowym](https://go.microsoft.com/fwlink/?linkid=2041178) (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone).

  **Standardowe zmiany kontraktu**

  Standardowe zmiany kontraktu pozwalają wybrać standardowe warunki umowy dla uproszczenia i utworzyć warunki dla produktu lub firmy. Klienci muszą przejrzeć zmiany w kontrakcie tylko wtedy, gdy zostały już przejrzane i zaakceptowały umowę standardową firmy Microsoft. Istnieją dwa typy zmian:

  * **Uniwersalne zmiany**: te zmiany są powszechnie stosowane do standardowej umowy dla wszystkich klientów. Są one widoczne dla każdego klienta oferty w przepływie zakupu. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej oraz zmiany. Możesz wprowadzić pojedynczą zmianę uniwersalną dla każdej oferty. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w AppSource, w witrynie Azure Marketplace i/lub Azure Portal w ramach przepływu odnajdywania i zakupów.

  * **Zmiany niestandardowe**: te specjalne zmiany w standardowej umowie są przeznaczone dla określonych klientów za pomocą identyfikatorów dzierżaw platformy Azure. Możesz wybrać dzierżawcę, który ma być celem. Tylko klienci z dzierżawy są wyświetlani z niestandardowymi warunkami zmiany w przepływie zakupu oferty. Aby można było korzystać z oferty, klienci muszą zaakceptować warunki umowy standardowej oraz zmiany.

    1. Zacznij od wybrania pozycji **Dodaj niestandardowe warunki zmiany (maks. 10)**. Możesz dostarczyć do dziesięciu postanowień w ramach oferty. Wykonaj następujące czynności:

       a. Wprowadź własne warunki zmiany w polu **niestandardowe warunki poprawki** . Można wprowadzić nieograniczoną liczbę znaków. Tylko klienci z identyfikatorów dzierżaw określonych dla tych terminów niestandardowych będą widzieć je w przepływie zakupu oferty w Azure Portal.

       b. Potrzeb Podaj **identyfikatory dzierżawy**. Każda Poprawka niestandardowa może być przeznaczona do maksymalnie 20 identyfikatorów dzierżawy. W przypadku dodania niestandardowej zmiany należy podać co najmniej jeden identyfikator dzierżawy, który identyfikuje klienta na platformie Azure. Klient może go znaleźć na platformie Azure, wybierając pozycję **Azure Active Directory**  >  **Properties (właściwości**). Wartość identyfikatora katalogu to identyfikator dzierżawy (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Możesz również znaleźć identyfikator dzierżawy w organizacji, używając adresu sieci Web nazwy domeny w obszarze [co to jest mój Microsoft Azure i Microsoft 365 identyfikator dzierżawy?](https://www.whatismytenantid.com/).

       c. Obowiązkowe Podaj przyjazny **Opis** identyfikatora dzierżawy, który pomaga zidentyfikować klienta, którego celem jest zmiana.

        > [!NOTE]
        > Te dwa typy zmian są sparowane ze sobą. Klienci, którzy są odpowiedzialni za niestandardowe zmiany, otrzymają także uniwersalne zmiany w standardowej umowie w trakcie zakupu.

    1. Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="offer-listing"></a>Lista oferty

Na stronie z **listą ofert** zdefiniujesz szczegóły oferty, takie jak nazwa oferty, opis, linki i kontakty.

> [!NOTE]
> Twoja oferta zawiera informacje, takie jak opis, dokumenty, zrzuty ekranu i warunki użytkowania, nie musi być w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w systemie \<non-English language> ". Możesz także podać adres URL, aby połączyć się z witryną, która oferuje zawartość w języku innym niż ten, który jest używany w zawartości oferty.

### <a name="marketplace-details"></a>Szczegóły witryny Marketplace

#### <a name="name"></a>Nazwa

Nazwa wprowadzona w tym miejscu jest pokazywana klientom jako tytuł oferty. To pole jest wypełniane przy użyciu nazwy wprowadzonej w polu **alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić. Nazwa:

- Mogą być znakami towarowymi. Można dołączyć znaki towarowe i praw autorskich.
- Nie może zawierać więcej niż 50 znaków.
- Nie może zawierać znaków emoji.

#### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis oferty, który ma być wyświetlany w wynikach wyszukiwania w portalu Azure Marketplace. Może zawierać maksymalnie 100 znaków.

#### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty, która będzie wyświetlana w wynikach wyszukiwania w portalu Azure Marketplace. Może zawierać maksymalnie 256 znaków.

#### <a name="description"></a>Opis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Link zasad ochrony prywatności

Wprowadź adres sieci Web zasad zachowania poufności informacji organizacji. Upewnij się, że oferta jest zgodna z przepisami i przepisami dotyczącymi ochrony prywatności. Należy również opublikować prawidłowe zasady zachowania poufności informacji w witrynie sieci Web.

### <a name="useful-links"></a>Przydatne łącza

Podaj uzupełniające dokumenty online dotyczące Twojej oferty. Aby dodać łącze, wybierz opcję **Dodaj łącze**, a następnie wykonaj następujące pola:

- **Nazwa**: klienci będą widzieli nazwę na stronie szczegółów.
- **Link (adres URL)**: wprowadź link umożliwiający klientom Wyświetlanie dokumentu w trybie online.

### <a name="customer-support-links"></a>Linki obsługi klienta

Podaj witrynę sieci Web pomocy technicznej, w której klienci mogą skontaktować się z zespołem pomocy technicznej.

- Witryna internetowa pomocy technicznej platformy Azure
- Witryna sieci Web pomocy technicznej Azure Government

### <a name="partner-support-contact"></a>Kontakt z pomocą techniczną partnera

Podaj informacje kontaktowe dla partnerów firmy Microsoft, które mają być używane, gdy klienci otworzą bilet pomocy technicznej. Te informacje nie są wymienione w witrynie Azure Marketplace.

- Nazwa
- Poczta e-mail
- Telefon

### <a name="engineering-contact"></a>Kontakt inżynieryjny

Podaj informacje kontaktowe firmy Microsoft, które mają być używane w przypadku problemów z ofertą, w tym problemy z certyfikatem. Te informacje nie są wymienione w witrynie Azure Marketplace.

- Nazwa
- Poczta e-mail
- Telefon

### <a name="azure-marketplace-media"></a>Nośniki portalu Azure Marketplace

Podaj logo i obrazy, które mają być używane z ofertą. Wszystkie obrazy muszą mieć format PNG. Nierozmyte obrazy spowodują odrzucenie zgłoszenia.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Jeśli występuje problem z przekazywaniem plików, należy się upewnić, że sieć lokalna nie blokuje https://upload.xboxlive.com usługi używanej przez centrum partnerskie.

#### <a name="azure-marketplace-logos"></a>Logo portalu Azure Marketplace

Podaj plik PNG dla logo o **dużym** rozmiarze. Centrum partnerskie będzie używać tego do tworzenia **małych** i **średnich** logo. Opcjonalnie można zastąpić je innymi obrazami później.

- **Duże** (od 216 x 216 do 350 x 350 px, wymagane)
- **Średnia** (90 x 90 pikseli, opcjonalnie)
- **Mały** (48 x 48 pikseli, opcjonalnie)

Te logo są używane w różnych miejscach na liście:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj maksymalnie pięć zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy zrzut ekranu musi mieć 1280 &times; 720 pikseli w rozmiarze i formacie PNG. Każdy zrzut ekranu musi zawierać podpis.

#### <a name="videos"></a>Wideo

Dodaj do pięciu filmów wideo, które przedstawiają Twoją ofertę. Filmy wideo powinny być hostowane w zewnętrznej usłudze wideo. Wprowadź nazwę, adres sieci Web i obraz miniatury PNG wideo o godzinie 1280 &times; 720 pikseli.

Aby uzyskać dodatkowe zasoby dotyczące aukcji z witryny Marketplace, zobacz [najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](../gtm-offer-listing-best-practices.md).

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="preview"></a>Wersja zapoznawcza

Wybierz kartę **Podgląd** , a następnie wybierz ograniczoną **grupę odbiorców w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem jej na żywo w szerszej komercyjnej grupie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w okienku **podglądu** wybierz pozycję **Przejdź na żywo** , aby opublikować ofertę dla komercyjnych odbiorców w portalu Marketplace.

Odbiorca w wersji zapoznawczej jest identyfikowany przez identyfikatory GUID subskrypcji platformy Azure, a także opcjonalny opis dla każdej z nich. Żadne z tych pól nie może być widoczne dla klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie **subskrypcje** w Azure Portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure — pojedynczo (do 10 identyfikatorów) lub przekazując plik CSV (do 100 identyfikatorów). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlać podgląd oferty przed opublikowaniem jej na żywo. Jeśli oferta już istnieje, możesz zdefiniować odbiorców wersji zapoznawczej w celu przetestowania zmian lub aktualizacji oferty.

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od odbiorców prywatnych. Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo w witrynie Azure Marketplace. Odbiorcy wersji zapoznawczej mogą wyświetlać i sprawdzać wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po całkowitym opublikowaniu oferty w portalu Azure Marketplace. Prywatni odbiorcy (zdefiniowani w okienku **ceny i dostępność** planu) mają wyłączny dostęp do konkretnego planu.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej sekcji.

## <a name="plan-overview"></a>Przegląd planu

W ramach tej samej oferty w centrum partnerskim można udostępnić różne opcje planu. Oferta wymaga co najmniej jednego planu (dawniej nazywanego jednostką SKU), która może różnić się w zależności od zysków odbiorców, regionu platformy Azure, funkcji lub obrazów maszyn wirtualnych.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](../private-offers.md).

Po utworzeniu planów wybierz kartę **Przegląd planu** , aby wyświetlić:

- Nazwy planów
- Modele licencji
- Odbiorcy (publiczna lub prywatna)
- Bieżący stan publikowania
- Dostępne akcje

Akcje, które są dostępne w okienku **Przegląd planu** , różnią się w zależności od bieżącego stanu planu.

- Jeśli plan jest stanem wersja robocza, wybierz pozycję **Usuń wersję roboczą**.
- Jeśli plan jest publikowany na żywo, wybierz pozycję **Zatrzymaj sprzedawanie planu** lub **zsynchronizuj odbiorców prywatnych**.

### <a name="create-a-new-plan"></a>Utwórz nowy plan

Wybierz pozycję **Utwórz nowy plan** u góry. Zostanie wyświetlone okno dialogowe **nowy plan** .

W polu **identyfikator planu** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie sieci Web produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń i maksymalnie 50 znaków.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

W polu **Nazwa planu** wprowadź nazwę dla tego planu. Klienci widzą tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę, która jasno wskazuje różnice między planami. Na przykład możesz wprowadzić **system Windows Server** z opcją *płatność zgodnie z rzeczywistym*użyciem, *BYOL*, *zaawansowanym*i planem *przedsiębiorstwa* .

Wybierz przycisk **Utwórz**.

### <a name="plan-setup"></a>Konfigurowanie planu

Ustaw konfigurację wysokiego poziomu dla typu planu, określ, czy ma ona replikować konfigurację techniczną z innego planu, i zidentyfikuj regiony platformy Azure, w których plan ma być dostępny. Wybrane tutaj ustawienia określają, które pola są wyświetlane w innych okienkach dla tego samego planu.

#### <a name="reuse-a-technical-configuration"></a>Ponowne użycie konfiguracji technicznej

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie korzysta z konfiguracji technicznej z innego planu**. Ta opcja umożliwia wybranie jednego z innych planów tego samego typu dla tej oferty i umożliwia ponowne użycie konfiguracji technicznej.

> [!NOTE]
> W przypadku ponownego użycia konfiguracji technicznej z poziomu innego planu, cała karta **konfiguracja techniczna** znika z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, zostaną użyte również dla tego planu. Nie można zmienić tego ustawienia po opublikowaniu planu.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Plan musi być dostępny w co najmniej jednym regionie świadczenia usługi Azure.

Wybierz opcję **globalną platformy Azure** , aby udostępnić plan klientom we wszystkich regionach globalnych platformy Azure, które mają komercyjną integrację z portalu Marketplace. Aby uzyskać więcej informacji, zobacz [dostępność geograficzna i obsługa waluty](../marketplace-geo-availability-currencies.md).

Wybierz opcję **Azure Government** , aby udostępnić plan w regionie [Azure Government](../../azure-government/documentation-government-welcome.md) . Ten region zapewnia klientom kontrolowany dostęp do jednostek federalnych, stanowych, lokalnych i plemienne, a także dla partnerów uprawnionych do ich świadczenia. Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie izolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Przed opublikowaniem w celu [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)Przetestuj i sprawdź swój plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, zażądaj konta próbnego na stronie [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonym regionie świadczenia usługi Azure nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Ta opcja jest widoczna tylko w przypadku wybrania **Azure Government** jako regionu świadczenia usługi Azure w poprzedniej sekcji.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="plan-listing"></a>Zaplanuj listę

W tej sekcji można skonfigurować szczegóły listy dla planu. W tym okienku są wyświetlane określone informacje, które mogą różnić się od innych planów w ramach tej samej oferty.

#### <a name="plan-name"></a>Nazwa planu

To pole jest automatycznie wypełniane nazwą, która została nadana przez Ciebie podczas jego tworzenia. Ta nazwa jest wyświetlana w witrynie Azure Marketplace jako tytuł tego planu. Maksymalna liczba znaków to 100.

#### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu, a nie oferty. To podsumowanie jest ograniczone do 100 znaków.

#### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, i opisz różnice między planami w ramach oferty. Opisz tylko plan, a nie ofertę. Opis planu może zawierać maksymalnie 2 000 znaków.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="pricing-and-availability"></a>Cennik i dostępność

W tym okienku konfigurujesz:

- Rynki, w których ten plan jest dostępny. Każdy plan musi być dostępny na co najmniej jednym [rynku](../marketplace-geo-availability-currencies.md).
- Cena za godzinę.
- Określa, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (odbiorców prywatnych).

#### <a name="markets"></a>Wprowadza

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla każdej lokalizacji rynkowej, w której ten plan ma być dostępny do zakupu. (Użytkownicy na tych rynkach mogą nadal wdrażać oferty we wszystkich regionach świadczenia usługi Azure wybranych w sekcji ["Planowanie konfiguracji"](#plan-setup) ). Przycisk **podatek z podatkiem** przedstawia kraje/regiony, w których firma Microsoft przekazuje sprzedaż i korzysta z podatku w Twoim imieniu. Publikowanie w Chinach jest ograniczone do planów, które są *bezpłatne* lub mogą korzystać *z własnej licencji* (BYOL).

Jeśli już ustawisz ceny planu w walucie dolarów amerykańskich (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku jest obliczana zgodnie z bieżącymi stawkami za wymianę. Zawsze sprawdzaj cenę poszczególnych rynków przed opublikowaniem. Zapoznaj się z cennikiem, wybierając pozycję **Eksportuj ceny (xlsx)** po zapisaniu zmian.

Po usunięciu rynku klienci z tego rynku, którzy korzystają z aktywnych wdrożeń, nie będą mogli tworzyć nowych wdrożeń ani skalować istniejących wdrożeń. Istniejące wdrożenia nie są modyfikowane.

#### <a name="pricing"></a>Cennik

W polu **model licencji**wybierz **miesięczny plan rozliczeniowy** , aby skonfigurować Cennik dla tego planu, lub wybierz **Bring Your Own License** , aby umożliwić klientom korzystanie z tego planu wraz z istniejącą licencją.

W przypadku miesięcznego planu rozliczeniowego opartego na użyciu należy użyć jednej z następujących trzech opcji wprowadzania cen:

- **Na rdzeń**: Zapewnij ceny za rdzeń w USD. Firma Microsoft oblicza ceny według rozmiaru rdzenia i konwertuje ją na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Rozmiar na rdzeń**: podaj ceny według rozmiaru rdzenia w USD. Firma Microsoft oblicza ceny i konwertuje ją na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Na rynek i rozmiar rdzeń**: podaj ceny każdego rozmiaru rdzenia dla wszystkich rynków. Ceny można zaimportować z arkusza kalkulacyjnego.

> [!NOTE]
> Zapisz zmiany cen, aby włączyć eksport danych cen. Po opublikowaniu ceny na rynku planu nie można jej później zmienić. Aby upewnić się, że ceny są prawidłowe przed ich opublikowaniem, wyeksportuj arkusz cen i przejrzyj ceny na każdym rynku.

#### <a name="free-trial"></a>Bezpłatna wersja próbna

Możesz zaoferować klientom jeden miesiąc lub trzy miesiące lub sześć miesięcy *bezpłatnej wersji próbnej* .

#### <a name="visibility"></a>Widoczność

Każdy plan można zaprojektować jako widoczny dla wszystkich lub tylko do wybranych użytkowników. Przypisywanie członkostw w tych ograniczonych odbiorcach przy użyciu identyfikatorów subskrypcji platformy Azure.

**Publiczna**: Twój plan może być widoczny dla każdego.

**Prywatni odbiorcy**: Ustaw, aby Twój plan był widoczny tylko dla wybranych odbiorców. Po opublikowaniu jako planu prywatnego można zaktualizować odbiorców lub zmienić je na publiczną. Po dokonaniu publicznego planu nie musi on być publiczny. Nie można jej zmienić z powrotem do planu prywatnego.

> [!NOTE]
> Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej w okienku **podglądu** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo w witrynie Azure Marketplace. Mimo że wybór dla odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany prywatne i publiczne na potrzeby weryfikacji.

**Odbiorcy z ograniczeniami (identyfikatory subskrypcji platformy Azure)**: Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego przy użyciu identyfikatorów subskrypcji platformy Azure. Opcjonalnie Dołącz opis każdego przypisanego identyfikatora subskrypcji platformy Azure. Po zaimportowaniu arkusza kalkulacyjnego CSV Dodaj do maksymalnie 10 identyfikatorów subskrypcji ręcznie lub do 20 000 identyfikatorów. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a wszystkie litery muszą być pisane małymi literami.

>[!Note]
>Oferty prywatne nie są obsługiwane w przypadku subskrypcji platformy Azure ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP).

#### <a name="hide-a-plan"></a>Ukrywanie planu

Jeśli Twoja maszyna wirtualna ma być używana tylko pośrednio, gdy jest ona przywoływana przez inny szablon rozwiązania lub zarządzana aplikacja, zaznacz to pole wyboru, aby opublikować maszynę wirtualną, ale ukryć ją od klientów, którzy mogą bezpośrednio przeszukiwać lub przeglądać.

> [!NOTE]
> Ukryte plany nie obsługują linków podglądu.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

### <a name="technical-configuration"></a>Konfiguracja techniczna

Podaj obrazy i inne właściwości techniczne, które są skojarzone z tym planem. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](create-azure-container-technical-assets.md).

> [!NOTE]
> Karta **konfiguracja techniczna** nie jest wyświetlana, jeśli ten plan został skonfigurowany do ponownego użycia pakietów z innego planu na karcie **Konfiguracja planu** .

#### <a name="operating-system"></a>System operacyjny

W okienku **system operacyjny** wykonaj następujące czynności:

- W przypadku **rodziny systemów operacyjnych**wybierz system operacyjny **Windows** lub **Linux** .
- W polu **wydanie** lub **dostawca**wybierz dostawcę wydania systemu Windows lub Linux.
- W polu **przyjazna nazwa dla systemu**operacyjnego wprowadź przyjazną nazwę systemu operacyjnego. Ta nazwa jest widoczna dla klientów.

#### <a name="recommended-vm-sizes"></a>Zalecane rozmiary maszyn wirtualnych

Wybierz maksymalnie sześć zalecanych rozmiarów maszyn wirtualnych, które mają być wyświetlane w witrynie Azure Marketplace.

#### <a name="open-ports"></a>Otwieranie portów

Otwórz porty publiczne lub prywatne na wdrożonej maszynie wirtualnej.

#### <a name="storage-option-for-deployment"></a>Opcja magazynu dla wdrożenia

Dla **opcji wdrożenie dysku**wybierz typ wdrożenia dysku, którego klienci mogą używać dla maszyny wirtualnej. Firma Microsoft zaleca ograniczenie wdrożenia tylko do **wdrożenia dysku zarządzanego** .

#### <a name="properties"></a>Właściwości

Aby **zapewnić obsługę przyspieszonej sieci**, należy wybrać, czy maszyna wirtualna obsługuje [przyspieszone sieci](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Obrazy maszyn wirtualnych

Podaj wersję dysku i identyfikator URI sygnatury dostępu współdzielonego (SAS) dla obrazów maszyn wirtualnych. Dodaj do 16 dysków danych dla każdego obrazu maszyny wirtualnej. Podaj tylko jedną nową wersję obrazu na plan w określonym założeniu. Po opublikowaniu obrazu nie można go edytować, ale można go usunąć. Usunięcie wersji uniemożliwia nowym i istniejącym użytkownikom wdrażanie nowego wystąpienia usuniętej wersji.

- **Wersja dysku**: wersja dostarczanego obrazu.
- **Identyfikator URI sygnatury dostępu współdzielonego**: lokalizacja na koncie usługi Azure Storage, w której zapisano dysk VHD systemu operacyjnego. Aby dowiedzieć się, jak uzyskać identyfikator URI sygnatury [dostępu współdzielonego, zobacz temat Uzyskiwanie identyfikatora URI dla tego obrazu maszyny wirtualnej](get-sas-uri.md).
- Obrazy dysków danych to również identyfikatory URI sygnatury dostępu współdzielonego, które są przechowywane na kontach usługi Azure Storage.
- Dodaj tylko jeden obraz do każdego przesłania w planie.

Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Podczas wdrażania klienci nie mogą usunąć dysków, które są częścią obrazu, ale zawsze mogą dodawać dyski w trakcie wdrażania lub po nim.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem i wróć do **omówienia planu**.

## <a name="resell-through-csps"></a>Odsprzedaż za poorednictwem dostawców CSP

Rozszerz zasięg oferty, udostępniając ją partnerom w programie [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . Wszystkie plany przyłączenia do własnej licencji (BYOL) są automatycznie dodanych do programu. Możesz również zdecydować się na wybór planów BYOL.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="test-drive"></a>Wersja testowa

Skonfiguruj wersję demonstracyjną lub *testową*, która umożliwia klientom wypróbowanie oferty przez ustalony czas przed ich zakupieniem. Aby utworzyć środowisko demonstracyjne dla klientów, zobacz [oferty testowe](test-drive.md)na platformie Marketplace.

Aby włączyć stację testową, zaznacz pole wyboru **Włącz dysk testowy** w okienku **Konfiguracja oferty** . Aby usunąć dysk testowy z oferty, wyczyść to pole wyboru.

Dodatkowe zasoby dotyczące stacji testowych:

- [Najlepsze rozwiązania marketingowe](../what-is-test-drive.md)
- [Najlepsze rozwiązania techniczne](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Przegląd dysku testowego](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Plik PDF (Upewnij się, że blokowanie wyskakujących okienek jest wyłączone)

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po zakończeniu wszystkich wymaganych sekcji oferty możesz przesłać ją do przeglądu i publikacji.

W prawym górnym rogu wybierz pozycję **Przejrzyj i Opublikuj**.

W tym okienku można:

- Wyświetl stan ukończenia dla każdej sekcji oferty:

  - **Nie uruchomiono**: sekcja nie została uruchomiona i musi zostać zakończona.
  - **Niekompletne**: sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania dodatkowych informacji. Aby uzyskać wskazówki dotyczące aktualizowania niekompletnych informacji, zobacz wcześniejsze sekcje w tym artykule.
  - **Ukończono**: sekcja została ukończona i nie występują żadne błędy. Aby można było przesłać ofertę, wszystkie sekcje oferty muszą zostać ukończone.
- Podaj **uwagi dotyczące certyfikacji dla** zespołu certyfikacji, aby pomóc w zapewnieniu poprawnego testowania aplikacji. Dołącz instrukcje testowania i wszelkie dodatkowe uwagi, które mogą pomóc zespołowi zrozumieć swoją aplikację.

Aby przesłać ofertę do publikacji, wybierz pozycję **Przejrzyj i Opublikuj**.

Firma Microsoft wyśle wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty do przejrzenia i zatwierdzenia. Aby opublikować ofertę na publiczną (lub, jeśli jest to oferta prywatna, opublikuj ją w grupie odbiorców prywatnych), przejdź do Centrum partnerskiego, przejdź do strony **przeglądu** oferty, a następnie wybierz pozycję **Otwórz**. Gdy publikowanie jest w toku, w górnej części strony pojawi się stan oferty.

### <a name="errors-and-review-feedback"></a>Błędy i przejrzyj opinię

Krok **walidacji ręcznej** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych. Jeśli ten proces odzyskuje błędy oferty, otrzymasz raport z certyfikatem zawierającym szczegóły dotyczące problemów. Po prostu wprowadź wymagane poprawki i ponownie Opublikuj swoją ofertę.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków, zarówno ukończonych, jak i w toku, które są wymagane do opublikowania oferty oraz czas trwania każdego kroku.

Ta strona zawiera także linki pomagające w pracy z ofertą, w zależności od jej stanu:

- Jeśli oferta jest wersją roboczą: [Usuń wersję roboczą](update-existing-offer.md#delete-a-draft-offer)
- Jeśli oferta jest aktywna: [Zatrzymaj sprzedawanie oferty](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Jeśli oferta jest dostępna w wersji zapoznawczej: [Przejdź na żywo](publishing-status.md#publisher-approval)
- Jeśli nie ukończono wylogowania wydawcy: [Anuluj publikowanie](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>Przykłady dla witryny Marketplace

Oto przykład sposobu wyświetlania informacji o ofercie w witrynie Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Duże logo
2. Cena
3. Kategorie
4. Warunki i postanowienia
5. Adres zasad ochrony prywatności (link)
6. Nazwa oferty
7. Opis
8. Przydatne łącza
9. Zrzuty ekranu/wideo

<br>Oto przykład sposobu wyświetlania informacji o ofercie w wynikach wyszukiwania w portalu Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Małe logo
2. Nazwa oferty
3. Podsumowanie wyników wyszukiwania
4. Wersja próbna

<br>Oto przykład szczegółów planu witryny Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Nazwa i podsumowanie planu
2. Zalecane rozmiary maszyn wirtualnych
3. Cennik planu

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Azure Portal:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Nazwa
2. Opis
3. Przydatne łącza
4. Zrzuty ekranu/wideo

<br>Oto przykład sposobu wyświetlania informacji o ofercie w Azure Portal wynikach wyszukiwania:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Małe logo
2. Nazwa oferty
3. Podsumowanie wyników wyszukiwania

<br>Oto przykład Azure Portal szczegóły planu:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Ilustruje, jak ta oferta pojawia się w witrynie Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Opisy połączeń

1. Nazwa planu
2. Opis planu

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w komercyjnej witrynie Marketplace](update-existing-offer.md)
