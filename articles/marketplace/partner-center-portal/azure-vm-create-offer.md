---
title: Tworzenie oferty maszyny wirtualnej platformy Azure — azure marketplace
description: Dowiedz się, jak utworzyć ofertę maszyn wirtualnych w komercyjnym rynku.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 161fd9276427db0d0d7d56da1bfc0bb8ccf52a52
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536662"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Tworzenie oferty maszyn wirtualnych platformy Azure

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami maszyn wirtualnych platformy Azure z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [programie Tworzenie maszyny wirtualnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) w portalu cloud partner, aby zarządzać ofertami.

W tym artykule opisano sposób tworzenia i publikowania oferty maszyny wirtualnej platformy Azure w [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com/) Dotyczy zarówno maszyn wirtualnych opartych na systemie Windows, jak i Linux, które zawierają system operacyjny, wirtualny dysk twardy (VHD) i maksymalnie 16 dysków z danymi.

## <a name="introduction"></a>Wprowadzenie

### <a name="publishing-benefits"></a>Korzyści związane z publikowaniem

Publikowanie w portalu Azure Marketplace ma następujące zalety:

- Promuj swoją firmę przy użyciu marki Microsoft
- Docieranie do ponad 100 milionów użytkowników usługi Office 365 i Dynamics 365 oraz ponad 200 000 organizacji za pośrednictwem portalu Azure Marketplace
- Uzyskaj wysokiej jakości potencjalnych klientów z tych rynków
- Promowanie usług przez zespoły firmy Microsoft i telesprzedaży

### <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli jeszcze tego nie zrobiono, zapoznaj się z [przewodnikiem publikowania oferty maszyny wirtualnej](https://aka.ms/Virtualmachineofferpublishingguide) i materiałem tej maszyny wirtualnej platformy Azure:

- Przewodniki szybki start
  - [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/)
  - [Szablony przewodnika Szybki start platformy GitHub](https://github.com/azure/azure-quickstart-templates)
- Samouczki
  - [Maszyny wirtualne z systemem Linux](https://aka.ms/LinuxVMtutorial)
  - [Maszyny wirtualne z systemem Windows](https://aka.ms/windowsvms)
- Samples
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux](https://aka.ms/linuxclisamples)
  - [Maszyny wirtualne programu Azure PowerShell dla systemów Linux](https://aka.ms/linuxpowershellsamples)
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows](https://aka.ms/windowsclisamples)
  - [Maszyny wirtualne programu Azure PowerShell dla systemów Windows](https://aka.ms/windowspowershellvmsamples)

### <a name="fundamentals-in-technical-knowledge"></a>Podstawy wiedzy technicznej

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty.

Zespół inżynierów powinien zrozumieć następujące technologie firmy Microsoft:

- Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/)
- Jak [projektować i projektować aplikacje platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i sieci platformy [Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Tworzenie oferty maszyn wirtualnych platformy Azure

Aby można było utworzyć ofertę maszyny wirtualnej platformy Azure, musisz mieć konto w portalu partnerów. Jeśli jeszcze go nie utworzyłeś, zobacz [Tworzenie konta w portalu partnerów](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Zaloguj się do [Centrum partnerów,](https://partner.microsoft.com/dashboard/home)a następnie z górnego menu wybierz pozycję **Pulpit nawigacyjny**.
2. Na pasku po lewej stronie nawigacyjnej wybierz pozycję **Rynek komercyjny**, a następnie **przegląd**.
3. Na stronie **Przegląd** wybierz pozycję **+ Nowa oferta**, a następnie **maszynę wirtualną platformy Azure**. Zostanie wyświetlone okno dialogowe **Nowa oferta.**

![Ilustruje stronę Przegląd w Centrum partnerów z wybranym przyciskiem Nowa oferta i ofertą maszyny wirtualnej platformy Azure.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator jest widoczny dla klientów w adresie internetowym dla oferty portalu Marketplace oraz w usłudze Azure PowerShell i interfejsie wiersza polecenia platformy Azure, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale bez spacji i jest ograniczona do 50 znaków. Na przykład, jeśli wprowadzisz **test-offer-1** tutaj, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres internetowy oferty będzie .
- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz**.

Wprowadź **alias oferty**. Jest to nazwa używana dla oferty w Centrum partnerów. Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości wyświetlanych klientom.

Wybierz **pozycję Utwórz,** aby wygenerować ofertę i kontynuować.

## <a name="offer-setup"></a>Konfiguracja oferty

### <a name="test-drive"></a>Jazda próbna

Skonfiguruj pokazową (jazdę próbną), która umożliwia klientom wypróbowanie oferty przed jej zakupem. Aby utworzyć środowisko demonstracyjne, które umożliwia klientom wypróbowanie oferty przez określony czas, zobacz [Test Drive oferty na rynku komercyjnym.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Aby włączyć jazdę próbną, zaznacz pole wyboru **Włącz jazdę próbną.** Aby usunąć jazdę próbną z oferty, wyczyść to pole wyboru.

Dodatkowe zasoby dysku próbne:

- [Najlepsze praktyki techniczne](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Najlepsze rozwiązania marketingowe](https://aka.ms/TestDriveMarketingBestPractices)
- [Omówienie dysków testowych](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (upewnij się, że bloker wyskakujących wyskakujących wyskakujących jest wyłączony).

### <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Publikując ofertę na rynku komercyjnym za pomocą Centrum partnerskiego, połącz ją z systemem CRM (Customer Relationship Management). Dzięki temu możesz otrzymywać informacje kontaktowe klienta, gdy tylko ktoś wyrazi zainteresowanie produktem lub z niego korzysta. Podłączenie do crm jest wymagane, jeśli włączysz **jazdę próbną** (patrz poprzednia sekcja), w przeciwnym razie jest to opcjonalne.

1. Wybierz miejsce docelowe potencjalnych klientów, do którego chcesz wysyłać potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy CRM:
    - [Dynamics 365](https://aka.ms/Dyn365LeadMgmt) dla zaangażowania klientów
    - [Marketo](https://aka.ms/LeadMgmtMarketo)
    - [SalesForce](https://aka.ms/LeadMgmtSalesforce)

    > [!NOTE]
    > Jeśli systemu CRM nie ma na liście powyżej, użyj [tabeli Azure](https://aka.ms/AzureTableLeadMgmt) lub [punktu końcowego https](https://aka.ms/LeadMgmtHTTPS) do przechowywania danych potencjalnych klientów. Następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z głównym miejscem docelowym podczas publikowania w Centrum partnerów.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest poprawnie skonfigurowane. Po opublikowaniu go w Centrum partnerów, firma Microsoft sprawdź poprawność połączenia i wysłać potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej wyświetleniem można również przetestować połączenie potencjalnego klienta, próbując samodzielnie wdrożyć ofertę w środowisku w wersji zapoznawczej.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta pozostaje zaktualizowane, aby nie utracić potencjalnych klientów.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

## <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii i branż używanych do grupowanie oferty w portalu Marketplace, wersja aplikacji i umowy prawne, które obsługują twoją ofertę.

### <a name="categories"></a>Kategorie

Wybierz co najmniej jedną i maksymalnie pięć kategorii. Te kategorie służą do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu marketplace. W opisie oferty wyjaśnij, w jaki sposób Twoja oferta obsługuje te kategorie. Oferty maszyn wirtualnych są wyświetlane w kategorii **Obliczeniowa** w portalu Azure Marketplace.

### <a name="legal"></a>Informacje prawne

Musisz podać warunki oferty. Dostępne są dwie opcje:

- Korzystaj z własnych warunków
- Korzystanie z kontraktu standardowego dla komercyjnego portalu Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Korzystaj z własnych warunków

Aby podać własne warunki niestandardowe, wprowadź do 10 000 znaków tekstu w polu **Warunki.** Jeśli potrzebujesz dłuższego opisu, wprowadź jeden adres internetowy, który wskazuje, gdzie można znaleźć warunki. Będzie wyświetlany klientom jako aktywne łącze.

Klienci muszą zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Korzystanie z kontraktu standardowego dla komercyjnego portalu Microsoft

Aby uprościć proces zaopatrzenia dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje umowę standardową dla rynku komercyjnego. Gdy oferujesz oprogramowanie w ramach umowy standardowej, klienci muszą je przeczytać i zaakceptować tylko raz i nie musisz tworzyć niestandardowych warunków.

Użyj umowy standardowej, zaznaczając pole wyboru **Użyj kontraktu standardowego dla komercyjnego portalu marketplace firmy Microsoft,** a następnie **zaakceptuj** w wyskakującym oknie (może być konieczne przewinięcie w górę, aby go wyświetlić).

![Ilustruje stronę Przegląd w Centrum partnerów z wybranym przyciskiem Nowa oferta i ofertą usług konsultingowych.](media/use-standard-contract.png)

> [!NOTE]
> Po opublikowaniu oferty przy użyciu umowy standardowej dla komercyjnego rynku nie możesz korzystać z własnych warunków niestandardowych. Zaoferuj swoje rozwiązanie w ramach umowy standardowej **lub** na własnych warunkach.

Aby dowiedzieć się więcej o umowie standardowej, zobacz Umowa standardowa dla [komercyjnego rynku](https://docs.microsoft.com/azure/marketplace/standard-contract)firmy Microsoft . Możesz pobrać [standardowy kontrakt](https://go.microsoft.com/fwlink/?linkid=2041178) w formacie PDF (upewnij się, że bloker wyskakujących wyskakujących wyskakujących wyskakujących jest wyłączony).

##### <a name="standard-contract-amendments"></a>Zmiany w umowie standardowej

Standardowe zmiany umowy pozwalają wybrać warunki umowy standardowej dla uproszczenia i stworzyć warunki dla twojego produktu lub firmy. Klienci muszą zapoznać się ze zmianami umowy tylko wtedy, gdy już przejrzeli i zaakceptowali umowę standardową firmy Microsoft. Dostępne są dwa rodzaje poprawek: uniwersalny i zwyczajowy.

**Uniwersalne zmiany** – są one powszechnie stosowane do umowy standardowej dla wszystkich klientów. Są one wyświetlane każdemu klientowi oferty w przepływie zakupu. Klienci muszą zaakceptować warunki Umowy Standardowej i zmiany, zanim będą mogli skorzystać z Twojej oferty. Możesz podać jedną uniwersalną poprawkę na ofertę. W tym polu można wprowadzić nieograniczoną liczbę znaków. Te warunki są wyświetlane klientom w usłudze AppSource, Azure Marketplace i/lub witrynie Azure portal podczas odnajdowania i zakupu.

**Zmiany niestandardowe** — są to specjalne zmiany umowy standardowej, które są przeznaczone dla określonych klientów za pośrednictwem identyfikatorów dzierżawy platformy Azure. Możesz wybrać dzierżawę, na którą chcesz kierować reklamy. Tylko klienci z najemcy zostaną przedstawieni z niestandardowymi warunkami zmian w przepływie zakupu oferty. Klienci muszą zaakceptować warunki Umowy Standardowej i zmiany, zanim będą mogli skorzystać z Twojej oferty.

Zacznij od wybrania opcji **Dodaj niestandardowe warunki zmiany (Maks. 10).** Możesz podać maksymalnie dziesięć niestandardowych warunków zmiany na ofertę.

- **Niestandardowe warunki poprawek** — wprowadź własne warunki poprawek w polu terminy zmiany niestandardowej. Można wprowadzić nieograniczoną liczbę znaków. Tylko klienci z identyfikatorów dzierżawy, które określisz dla tych warunków niestandardowych, zobaczą je w przepływie zakupów oferty w witrynie Azure portal.
- **Identyfikatory dzierżawy** (wymagane) — każda poprawka niestandardowa może być kierowana do maksymalnie 20 identyfikatorów dzierżawy. Jeśli dodasz poprawkę niestandardową, musisz podać co najmniej jeden identyfikator dzierżawy, który identyfikuje klienta na platformie Azure. klient może znaleźć dla Ciebie na platformie Azure w obszarze, a następnie Właściwości. Wartość identyfikatora katalogu jest identyfikatorem dzierżawy (na przykład 50c464d3-4930-494c-963c-1e951d15360e). Identyfikator dzierżawy klienta w organizacji można również znaleźć przy użyciu adresu sieci Web nazwy domeny pod adresem Co to jest mój identyfikator [dzierżawy platformy Microsoft Azure i usługi Office 365?](https://www.whatismytenantid.com/).
- **Opis** (opcjonalnie) — podaj przyjazny opis identyfikatora dzierżawy, który pomaga zidentyfikować klienta, na który kierujesz reklamy, za pomocą poprawki.

> [!NOTE]
> Te dwa rodzaje poprawek kumulują się jeden na drugim. Klienci objęci niestandardowymi zmianami otrzymają również uniwersalną zmianę umowy standardowej podczas zakupu.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

## <a name="offer-listing"></a>Oferta aukcji

Ta strona umożliwia zdefiniowanie szczegółów oferty, takich jak nazwa oferty, opis, łącza i kontakty.

> [!NOTE]
> Zawartość aukcji oferty (taka jak opis, dokumenty, zrzuty ekranu i warunki użytkowania) nie musi być w języku angielskim, o ile opis oferty zaczyna się od wyrażenia" Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]". Możesz również podać _adres internetowy Przydatne łącze,_ aby oferować treści w języku innym niż ten używany w treści aukcji Oferty.

### <a name="marketplace-details"></a>Szczegóły portalu Marketplace

#### <a name="name"></a>Nazwa

Nazwa, którą wpiszesz w tym miejscu, jest wyświetlana klientom jako tytuł twojej oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Może być znakiem towarowym (i możesz zawierać znaki towarowe i symbole praw autorskich)
- Nie może mieć więcej niż 50 znaków
- Nie można dołączać emotikonów.

#### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Krótki opis twojej oferty. Może to mieć do 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

#### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis swojej oferty. Może to mieć do 256 znaków i jest używana w wynikach wyszukiwania w marketplace.

#### <a name="description"></a>Opis

Podaj szczegółowy opis swojej oferty, do 3000 znaków. Jest to wyświetlane klientom w przeglądzie aukcji w rynku komercyjnym.

W opisie należy uwzględnić co najmniej jedną z następujących opcji:

- Wartość i najważniejsze korzyści twojej oferty
- Kategorie lub stowarzyszenia branżowe, lub oba
- Możliwości zakupu w aplikacji
- Wszelkie wymagane informacje

Oto kilka wskazówek dotyczących pisania opisu:

- Wyraźnie opisz propozycję wartości swojej oferty w pierwszych kilku zdaniach opisu. Dołącz następujące elementy:
  - Opis oferty.
  - Typ użytkownika, który korzysta z oferty.
  - Klient potrzebuje lub problemów, które dotyczy oferta.
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.
- Nie polegaj na funkcjach i funkcjach, aby sprzedawać swoją ofertę. Zamiast tego skup się na wartości, która zapewnia twoja oferta.
- Używaj słów branżowych lub opartych na korzyściach.

Aby opis oferty był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu. Edytor tekstu sformatowego umożliwia dodawanie liczb, punktorów, pogrubienia, kursywy i wcjęcia, aby opis był bardziej czytelny.

![Ilustruje stronę Przegląd w Centrum partnerów z wybranym przyciskiem Nowa oferta i ofertą usług konsultingowych.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Link do polityki prywatności

Wprowadź adres internetowy (URL) do zasad ochrony prywatności organizacji. Upewnij się, że Twoja oferta jest zgodna z przepisami i regulacjami dotyczącymi prywatności. Musisz również opublikować ważną politykę prywatności na swojej stronie internetowej.

### <a name="useful-links"></a>Przydatne łącza

Podaj dodatkowe dokumenty online dotyczące swojej oferty. Aby dodać łącze, wybierz **+ Dodaj łącze,** a następnie wypełnij następujące pola:

- **Nazwa** — klienci zobaczą nazwę na stronie szczegółów.
- **Link (URL)** — wprowadź link dla klientów, aby wyświetlić dokument online.

### <a name="customer-support-links"></a>Linki do obsługi klienta

Udostępnij witrynę pomocy technicznej, w której klienci mogą skontaktować się z twoim zespołem pomocy technicznej.

- Witryna pomocy technicznej platformy Azure Global
- Witryna pomocy technicznej platformy Azure dla instytucji rządowych

### <a name="partner-support-contact"></a>Kontakt z pomocą techniczną partnera

Podaj informacje kontaktowe partnerom firmy Microsoft, z których będą korzystać klienci, którzy otworzą bilet pomocy technicznej. Nie będzie to wyświetlane na rynku.

- Nazwa
- Adres e-mail
- Telefon

### <a name="engineering-contact"></a>Kontakt techniczny

Podaj informacje kontaktowe firmy Microsoft do użycia w przypadku wystąpienia problemów z ofertą, w tym problemów z certyfikacją. Nie będzie to wyświetlane na rynku.

- Nazwa
- Adres e-mail
- Telefon

### <a name="marketplace-media"></a>Media w marketplace

Podaj logo i obrazy do wykorzystania w swojej ofercie. Wszystkie obrazy muszą być w formacie PNG. Rozmazane obrazy spowodują odrzucenie zgłoszenia.

#### <a name="marketplace-logos"></a>Logo Marketplace

Podaj pliki PNG z logo oferty w następujących rozmiarach czterech pikseli:

- **Mały** (48 x 48)
- **Średni** (90 x 90)
- **Duży** (216 x 216)
- **Szeroki** (255 x 115)

Wszystkie cztery logo są wymagane i są używane w różnych miejscach na liście marketplace.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj do pięciu zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy zrzut ekranu musi mieć rozmiar 1280 x 720 pikseli i w formacie PNG. Należy również dodać podpis, aby opisać zrzut ekranu.

#### <a name="videos"></a>Filmy wideo

Dodaj do pięciu filmów, które pokazują twoją ofertę. Powinny one być hostowane w zewnętrznej usłudze wideo. Wprowadź nazwę, adres internetowy i miniaturę obrazu wideo w trybie PNG każdego filmu o wymiarach 1280 x 720 pikseli.

Aby uzyskać dodatkowe zasoby dotyczące aukcji w portalu marketplace, zobacz [Najważniejsze wskazówki dotyczące aukcji ofert w portalu Marketplace](https://aka.ms/LdMgmtOfferListingBestPractices).

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

## <a name="preview"></a>Wersja zapoznawcza

Na karcie Podgląd wybierz ograniczoną **grupę odbiorców w wersji zapoznawczej,** aby zweryfikować ofertę, zanim opublikujesz ją na żywo szerszym odbiorcom marketplace.

> [!IMPORTANT]
> Po sprawdzeniu oferty w wersji zapoznawczej wybierz **pozycję Przejdź na żywo,** aby opublikować ofertę w publicznej reklamie rynku komercyjnego.

Grupa odbiorców w wersji zapoznawczej jest identyfikowana przez identyfikatory identyfikatorów subskrypcji platformy Azure wraz z opcjonalnym opisem dla każdego z nich. Żadne z tych pól nie może być postrzegane przez klientów. Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w **witrynie** Azure portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure, indywidualnie (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, można zdefiniować, kto może wyświetlić podgląd oferty, zanim zostanie opublikowana na żywo. Jeśli twoja oferta jest już dostępna, możesz zdefiniować grupę odbiorców w wersji zapoznawczej, aby testowała zmiany oferty lub aktualizacje oferty.

> [!NOTE]
> Grupa odbiorców podglądu różni się od grupy prywatnej. Grupa odbiorców w wersji zapoznawczej może uzyskać dostęp do Twojej _oferty, zanim_ zostanie opublikowana na żywo na rynkuch. Mogą zobaczyć i zweryfikować wszystkie plany, w tym te, które będą dostępne tylko dla odbiorców prywatnych po pełnym opublikowaniu oferty na rynku. Prywatna publiczność (zdefiniowana na karcie **Ceny i dostępność** planu) ma wyłączny dostęp do określonego planu.

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Przegląd planu.

## <a name="plan-overview"></a>Omówienie planu

W Centrum partnerskim można podać różne opcje planu w ramach tej samej oferty. Plany te były wcześniej określane jako jednostki SKU. Oferta wymaga co najmniej jednego planu, który może się różnić pod względem odbiorców monetyzacji, regionów platformy Azure, funkcji lub obrazów maszyn wirtualnych.

Po utworzeniu planów na karcie **Przegląd planu** jest wyświetlany:

- Nazwy planów
- Modele licencji
- Publiczność (publiczna lub prywatna)
- Bieżący stan publikowania
- Dostępne akcje

Działania dostępne w przeglądzie planu różnią się w zależności od bieżącego stanu planu. Obejmują one następujące raporty:

- **Usuwanie wersji roboczej** — jeśli stan planu jest wersja robocza
- **Zatrzymaj plan sprzedaży** lub **Synchronizuj odbiorców prywatnych** — jeśli stan planu jest publikowany na żywo

### <a name="create-new-plan"></a>Tworzenie nowego planu

Wybierz **+ Utwórz nowy plan** u góry. Zostanie wyświetlone okno dialogowe **Nowy plan.**

W polu **Identyfikator planu** utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie internetowym produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń oraz maksymalnie 50 znaków.

> [!NOTE]
> Identyfikator planu nie można zmienić po wybraniu opcji **Utwórz**.

W polu **Nazwa planu** wprowadź nazwę tego planu. Klienci widzą tę nazwę przy podejmowaniu decyzji, który plan wybrać w ramach oferty. Utwórz unikatową nazwę, która wyraźnie wskazuje różnice każdego planu. Na przykład można użyć **systemu Windows Server** z planami Płatności zgodnie z rzeczywistym **użyciem**, **BYOL**, **Zaawansowane**i **Przedsiębiorstwo**.

Wybierz pozycję **Utwórz**.

### <a name="plan-setup"></a>Konfiguracja planu

Ustaw konfigurację wysokiego poziomu dla typu planu, czy ponownie używa konfiguracji technicznej z innego planu i w których regionach platformy Azure plan powinien być dostępny. Wybrane w tym miejscu określają, które pola są wyświetlane na innych kartach dla tego samego planu.

#### <a name="reuse-technical-configuration"></a>Ponowne wykorzystanie konfiguracji technicznej

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, można wybrać **ten plan ponownie używa konfiguracji technicznej z innego planu**. Ta opcja umożliwia wybranie jednego z innych planów tego samego typu dla tej oferty i ponowne użycie jej konfiguracji technicznej.

> [!NOTE]
> Po ponownym użyć konfiguracji technicznej z innego planu, cała karta **konfiguracji technicznej** znika z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje, które zostaną zainstalowane w przyszłości, będą również używane dla tego planu. Tego ustawienia nie można zmienić po opublikowaniu tego planu.

#### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Plan musi być udostępniony w co najmniej jednym regionie platformy Azure.

Wybierz opcję **Azure Global,** aby udostępnić swój plan klientom we wszystkich publicznych regionach platformy Azure, które mają integrację z komercyjnym rynkiem. Aby uzyskać szczegółowe informacje, zobacz [Dostępność geograficzna i obsługa walut](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Wybierz opcję **Azure Dla Instytucji Rządowych,** aby udostępnić swój plan w regionie [azure dla instytucji rządowych.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Region ten zapewnia kontrolowany dostęp dla klientów z amerykańskich podmiotów federalnych, stanowych, lokalnych lub plemiennych, a także partnerów uprawnionych do ich obsługi. Użytkownik, jako wydawca, jest odpowiedzialny za wszelkie mechanizmy kontroli zgodności, środki bezpieczeństwa i najlepsze rozwiązania. Platforma Azure Government używa fizycznie odizolowanych centrów danych i sieci (zlokalizowanych tylko w Stanach Zjednoczonych).

Przed opublikowaniem w [usłudze Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)należy przetestować i zweryfikować plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, poproś o konto próbne z [wersji próbnej platformy Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po opublikowaniu planu i udostępnienie go w określonym regionie platformy Azure nie można usunąć tego regionu.

#### <a name="azure-government-certifications"></a>Certyfikaty platformy Azure dla instytucji rządowych

Ta opcja jest widoczna tylko wtedy, gdy w obszarze **Regiony platformy Azure**wybierzesz **opcję Azure Government** .

Usługi azure dla instytucji rządowych obsługują dane podlegające określonym przepisom i wymaganiom rządowym. Na przykład FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 i CJIS. Aby zwiększyć świadomość certyfikatów dla tych programów, możesz podać do 100 linków, które je opisują. Mogą to być linki do twojej aukcji w programie bezpośrednio lub linki do opisów zgodności z nimi na własnych stronach internetowych. Te łącza widoczne tylko dla klientów platformy Azure dla instytucji rządowych.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

### <a name="plan-listing"></a>Lista planu

W tym miejscu można skonfigurować szczegóły aukcji planu. Na tej karcie są wyświetlane określone informacje, które mogą się różnić między planami w tej samej ofercie.

#### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą nadany plan podczas jego tworzenia. Ta nazwa pojawia się na rynku jako tytuł tego planu i jest ograniczona do 100 znaków.

#### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu (nie ofertę). To podsumowanie jest ograniczone do 100 znaków.

#### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest wyjątkowy, a także różnice między planami w ramach oferty. Nie opisuj oferty, tylko plan. Opis planu może zawierać maksymalnie 2000 znaków.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

### <a name="pricing-and-availability"></a>Cennik i dostępność

Na tej karcie skonfigurujesz następujące elementy:

- Rynki, które plan ten będzie dostępny w
- Cena za godzinę
- Czy plan ma być widoczny dla wszystkich, czy tylko dla konkretnych klientów (prywatna publiczność)

#### <a name="markets"></a>Rynków

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla każdej lokalizacji rynku, w której plan ten powinien być dostępny do zakupu (użytkownicy na tych rynkach nadal mogą wdrażać ofertę we wszystkich regionach platformy Azure wybranych w **[konfiguracji planu).](#plan-setup)** Przycisk **Umorzenie podatku** pokazuje kraje, w których firma Microsoft przekazuje podatek od sprzedaży i korzystać z niego w twoim imieniu. Publikowanie w Chinach jest ograniczone do planów, które są **bezpłatne** lub **przynieś własną licencję** (BYOL).

Jeśli masz już ustawione ceny planu w dolarach amerykańskich (USD) i dodasz inną lokalizację rynkową, cena nowego rynku zostanie obliczona zgodnie z aktualnymi kursami wymiany. Zawsze sprawdzaj cenę dla każdego rynku przed opublikowaniem. Przejrzyj ceny za pomocą linku **Ceny eksportowe (xlsx)** po zapisaniu zmian.

Po usunięciu rynku klienci z tego rynku przy użyciu aktywnych wdrożeń nie będą mogli tworzyć nowych wdrożeń ani skalować istniejących wdrożeń. Nie będzie to miało wpływu na istniejące wdrożenia.

#### <a name="pricing"></a>Cennik

**Model licencji** — wybierz **miesięczny plan rozliczany oparty na użyciu,** aby skonfigurować ceny dla tego planu lub **Przynieś własną licencję,** aby umożliwić klientom korzystanie z tego planu z istniejącą licencją.

W przypadku miesięcznego planu rozliczeniowego opartego na użyciu użyj jednej z następujących trzech opcji wprowadzania cen:

- **Za rdzeń** — podaj cenę za rdzeń w dolarach amerykańskich (USD). Obliczymy ceny według rozmiaru rdzenia i przeliczymy na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Rozmiar rdzenia** — podaj ceny za rozmiar rdzenia w USD. Ceny przeliczymy na waluty lokalne przy użyciu aktualnego kursu wymiany walut.
- **Na rynek i rozmiar rdzenia** — podaj ceny dla każdego rozmiaru rdzenia dla wszystkich rynków. Ceny można importować z arkusza kalkulacyjnego.

> [!NOTE]
> Zapisz zmiany cen, aby umożliwić eksport danych cenowych. Po opublikowaniu ceny na rynku w planie nie można jej później zmienić. Upewnij się, że te ceny są tuż przed opublikowaniem, eksportując arkusz kalkulacyjny cen i przeglądając cenę na każdym rynku.

#### <a name="free-trial"></a>Bezpłatna wersja próbna

Możesz zaoferować swoim klientom miesięczny lub trzymiesięczny bezpłatny okres próbny.

#### <a name="visibility"></a>Widoczność

Możesz zaprojektować każdy plan tak, aby był widoczny dla wszystkich lub tylko dla wstępnie wybranej grupy odbiorców. Przypisz członkostwo w tej ograniczonej grupy odbiorców przy użyciu identyfikatorów subskrypcji platformy Azure.

**Publiczny** - Twój plan może być postrzegany przez wszystkich.

**Prywatna publiczność** — udostępnij swój plan tylko wstępnie wybranej publiczności. Po opublikowaniu go jako planu prywatnego możesz zaktualizować odbiorców lub zmienić ją na publiczną. Po upublicznienie planu musi on pozostać publiczny; nie można go zmienić z powrotem na prywatny.

**Ograniczone grupy odbiorców (identyfikatory subskrypcji platformy Azure)** — przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego przy użyciu identyfikatorów subskrypcji platformy Azure. Opcjonalnie dołącz opis każdego przypisanego identyfikatora subskrypcji platformy Azure. Jeśli importowanie arkusza kalkulacyjnego CSV, należy dodać maksymalnie 10 identyfikatorów subskrypcji ręcznie lub 20 000. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a litery muszą być małe.

> [!NOTE]
> Grupa odbiorców prywatnych lub z ograniczeniami różni się od grupy odbiorców w wersji zapoznawczej zdefiniowanej na karcie **Podgląd.** Grupa odbiorców w wersji zapoznawczej może uzyskać dostęp do Twojej oferty _przed_ jej opublikowaniem na żywo w portalu marketplace. Wybór grupy odbiorców prywatnych dotyczy tylko określonego planu, ale odbiorcy wersji zapoznawczej mogą wyświetlać wszystkie plany (prywatne lub nie) do celów weryfikacji.

#### <a name="hide-plan"></a>Ukryj plan

Jeśli maszyna wirtualna ma być używana tylko pośrednio, gdy odwołuje się do innego szablonu rozwiązania lub aplikacji zarządzanej, zaznacz to pole, aby opublikować maszynę wirtualną, ale ukryj ją przed klientami wyszukującymi i przeglądającymi ją bezpośrednio.

> [!NOTE]
> Ukryte plany nie obsługują łączy w wersji zapoznawczej.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

### <a name="technical-configuration"></a>Konfiguracja techniczna

Podaj obrazy i inne właściwości techniczne skojarzone z tym planem. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie zasobu technicznego maszyny Wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Ta karta nie jest wyświetlana, jeśli ten plan został skonfigurowany do ponownego użycia pakietów z innego planu na karcie **Ustawienia planu.**

#### <a name="operating-system"></a>System operacyjny

- **Rodzina systemów operacyjnych** — wybierz z systemu operacyjnego **Windows** lub **Linux**
- **Zwolnij** lub **dostawca** — wybierz wersję systemu Windows lub dostawcę systemu Linux
- **Nazwa przyjazna systemowi operacyjnemu** — wybierz przyjazną nazwę systemu operacyjnego. Ta nazwa jest widoczna dla klientów

#### <a name="recommended-vm-sizes"></a>Zalecane rozmiary maszyn wirtualnych

Wybierz maksymalnie sześć zalecanych rozmiarów maszyn wirtualnych do wyświetlenia w portalu Azure Marketplace.

#### <a name="open-ports"></a>Otwieranie portów

Otwórz porty publiczne lub prywatne na wdrożonej maszynie wirtualnej.

#### <a name="storage-option-for-deployment"></a>Opcja magazynowania dla wdrożenia

**Opcja wdrażania dysku** — wybierz rodzaj wdrożenia dysku, którego użytkownicy mogą używać podczas korzystania z maszyny wirtualnej. Firma Microsoft zaleca ograniczenie wdrożenia tylko do wdrożenia dysku zarządzanego.

#### <a name="properties"></a>Właściwości

**Obsługa przyspieszonej sieci** — wybierz, czy maszyna wirtualna obsługuje [przyspieszoną sieć](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Obrazy maszyn wirtualnych

Podaj wersję dysku i identyfikator URI sygnatury dostępu Współdzielonego dla obrazów maszyn wirtualnych. Dodaj do 16 dysków danych dla każdego obrazu maszyny Wirtualnej. Podaj tylko jedną nową wersję obrazu na plan w danym zgłoszeniu. Po opublikowaniu obrazu nie można go edytować, ale można go usunąć. Usunięcie wersji uniemożliwi zarówno nowym, jak i istniejącym użytkownikom wdrożenie nowego wystąpienia usuniętej wersji.

- **Wersja płyty** jest wersją obrazu, który dostarczasz.
- **Identyfikator URI** sygnatury dostępu Współdzielonego to lokalizacja w usłudze Azure Storage, w której przechowywana jest dysk VHD systemu operacyjnego.
- Obrazy dysków danych są również identyfikatory URI SYGNAUSZ VHD przechowywane w ich magazynie platformy Azure.
- Dodaj tylko jeden obraz na zgłoszenie w planie.

Niezależnie od używanego systemu operacyjnego dodaj tylko minimalną liczbę dysków danych potrzebnych do rozwiązania. Klienci nie mogą usuwać dysków, które są częścią obrazu w czasie wdrażania, ale zawsze mogą dodawać dyski podczas lub po wdrożeniu.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem i wróć do **przeglądu planu**.

## <a name="resell-through-csps"></a>Odsprzedaj za pośrednictwem dostawców usług internetowych

Zwiększ zasięg swojej oferty, udostępniając ją partnerom w programie [Dostawców rozwiązań](https://azure.microsoft.com/offers/ms-azr-0145p/) w chmurze (CSP). Wszystkie plany Bring Your Own License (BYOL) są automatycznie włączane; możesz zdecydować się na swoje plany inne niż BYOL.

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

## <a name="test-drive"></a>Jazda próbna

Skonfiguruj pokazową (jazdę próbną), która umożliwia klientom wypróbowanie oferty przed jej zakupem. Aby utworzyć środowisko demonstracyjne, które umożliwia klientom wypróbowanie oferty przez określony czas, zobacz [Test Drive oferty na rynku komercyjnym.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Aby włączyć jazdę próbną, zaznacz pole wyboru Włącz dysk testowy na karcie [Ustawienia oferty.](#test-drive) Aby usunąć jazdę próbną z oferty, wyczyść to pole wyboru.

Dodatkowe zasoby dysku próbne:

- Najlepsze praktyki techniczne
- Najlepsze rozwiązania marketingowe
- Omówienie dysków testowych (PDF; upewnij się, że bloker wyskakujących wyskakujących wyskakujących jest wyłączony).

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po wypełnić wszystkie wymagane sekcje oferty, można przesłać go do przeglądu i opublikowania.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i opublikuj**.

Na tej stronie możesz:

- Zobacz stan ukończenia każdej sekcji oferty.
  - **Nie rozpoczęto** — sekcja nie została uruchomiona i musi zostać ukończona.
  - **Niekompletne** — sekcja zawiera błędy, które muszą zostać naprawione lub wymagają podania większej ilości informacji. Zapoznaj się z sekcjami we wcześniejszej części tego dokumentu, aby uzyskać wskazówki dotyczące aktualizowania tej sekcji.
  - **Ukończono** — sekcja jest kompletna i nie ma żadnych błędów. Przed złożeniem oferty należy wypełnić wszystkie sekcje oferty.
- **Uwagi dotyczące certyfikacji** — dostarczaj zespołowi certyfikacyjnemu instrukcje testowania, aby upewnić się, że aplikacja jest poprawnie przetestowana. Podaj wszelkie dodatkowe notatki pomocne w zrozumieniu aplikacji.

Aby przesłać ofertę publikacji, wybierz **pozycję Przejrzyj i opublikuj**.

Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia. Aby opublikować ofertę publiczną (lub prywatną, dla odbiorców prywatnych), przejdź do Centrum partnerów, znajdź stronę **Przegląd** oferty i wybierz **opcję Przejdź na żywo.** Stan oferty pojawi się w górnej części strony, gdy trwa publikowanie.

### <a name="errors-and-review-feedback"></a>Błędy i opinie

Krok **weryfikacji ręcznej** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych. Jeśli ten proces wykryje błędy w ofercie, otrzymasz raport certyfikacyjny, który wyszczegnie problemy. Wystarczy wprowadzić wymagane poprawki i ponownie opublikować ofertę.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania tej oferty (zarówno ukończonych, jak i w toku) oraz czasu, jaki powinien potrwać każdy krok.

Ta strona zawiera łącza do wykonywania operacji w tej ofercie na podstawie dokonanych wyborów. Przykład:

- Jeśli oferta jest wersja robocza — [usuwanie wersji roboczej oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Jeśli oferta jest na żywo - [Przestań sprzedawać ofertę](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Jeśli oferta jest w wersji zapoznawczej - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Jeśli nie zostało ukończone logowanie do wydawcy — [anulowanie publikowania](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Przykłady z Marketplace

Te przykłady pokazują, jak oferta pojawia się w portalu Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Szczegóły oferty w portalu Azure Marketplace

![Przykład ekranu szczegółów oferty usługi Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Wyniki wyszukiwania w portalu Azure Marketplace

![Przykład ekranu szczegółów wyszukiwania w portalu Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Szczegóły planu portalu Azure Marketplace

![Przykład ekranu szczegółów planu portalu azure marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Szczegóły oferty usługi Azure portal

![Przykład ekranu szczegółów oferty usługi azure portal](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Wyniki wyszukiwania w portalu Azure

![Przykład ekranu wyników wyszukiwania w portalu Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Szczegóły planu portalu platformy Azure

![Przykład ekranu szczegółów planu portalu platformy Azure](media/avm-create6.png)

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty na rynku komercyjnym](https://aka.ms/UpdateOfferCM)
