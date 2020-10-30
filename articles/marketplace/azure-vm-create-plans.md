---
title: Tworzenie planów dla oferty maszyny wirtualnej w witrynie Azure Marketplace
description: Dowiedz się, jak utworzyć plany dla oferty maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: bc5e98484560fcc15e0ea3e289069c84687f158c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040584"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Jak utworzyć plany dla oferty maszyny wirtualnej

W ramach tej samej oferty w centrum partnerskim można udostępnić różne opcje planu. Oferta wymaga co najmniej jednego planu (dawniej nazywanego jednostką SKU), która może różnić się w zależności od zysków odbiorców, regionu platformy Azure, funkcji lub obrazów maszyn wirtualnych.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

Po utworzeniu planów wybierz kartę **Przegląd planu** , aby wyświetlić:

- Nazwy planów
- Modele licencji
- Odbiorcy (publiczna lub prywatna)
- Bieżący stan publikowania
- Dostępne akcje

Akcje, które są dostępne w okienku **Przegląd planu** , różnią się w zależności od bieżącego stanu planu.

- Jeśli plan jest stanem wersja robocza, wybierz pozycję **Usuń wersję roboczą** .
- Jeśli plan jest publikowany na żywo, wybierz pozycję **Zatrzymaj sprzedawanie planu** lub **zsynchronizuj odbiorców prywatnych** .

## <a name="create-a-new-plan"></a>Utwórz nowy plan

Wybierz pozycję **Utwórz nowy plan** u góry. Zostanie wyświetlone okno dialogowe **nowy plan** .

W polu **identyfikator planu** Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie sieci Web produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń i maksymalnie 50 znaków.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz** .

W polu **Nazwa planu** wprowadź nazwę dla tego planu. Klienci widzą tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę, która jasno wskazuje różnice między planami. Na przykład możesz wprowadzić **system Windows Server** z opcją *płatność zgodnie z rzeczywistym* użyciem, *BYOL* , *zaawansowanym* i planem *przedsiębiorstwa* .

Wybierz pozycję **Utwórz** .

## <a name="plan-setup"></a>Konfigurowanie planu

Ustaw konfigurację wysokiego poziomu dla typu planu, określ, czy ma ona replikować konfigurację techniczną z innego planu, i zidentyfikuj regiony platformy Azure, w których plan ma być dostępny. Wybrane tutaj ustawienia określają, które pola są wyświetlane w innych okienkach dla tego samego planu.

### <a name="reuse-a-technical-configuration"></a>Ponowne użycie konfiguracji technicznej

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie korzysta z konfiguracji technicznej z innego planu** . Ta opcja umożliwia wybranie jednego z innych planów tego samego typu dla tej oferty i umożliwia ponowne użycie konfiguracji technicznej.

> [!NOTE]
> W przypadku ponownego użycia konfiguracji technicznej z poziomu innego planu, cała karta **konfiguracja techniczna** znika z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, zostaną użyte również dla tego planu. Nie można zmienić tego ustawienia po opublikowaniu planu.

### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Plan musi być dostępny w co najmniej jednym regionie świadczenia usługi Azure.

Wybierz opcję **globalną platformy Azure** , aby udostępnić plan klientom we wszystkich regionach globalnych platformy Azure, które mają komercyjną integrację z portalu Marketplace. Aby uzyskać więcej informacji, zobacz [dostępność geograficzna i obsługa waluty](marketplace-geo-availability-currencies.md).

Wybierz opcję **Azure Government** , aby udostępnić plan w regionie [Azure Government](../azure-government/documentation-government-welcome.md) . Ten region zapewnia klientom kontrolowany dostęp do jednostek federalnych, stanowych, lokalnych i plemienne, a także dla partnerów uprawnionych do ich świadczenia. Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie izolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Przed opublikowaniem w celu [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)Przetestuj i sprawdź swój plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, zażądaj konta próbnego na stronie [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonym regionie świadczenia usługi Azure nie można usunąć tego regionu.

### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Ta opcja jest widoczna tylko w przypadku wybrania **Azure Government** jako regionu świadczenia usługi Azure w poprzedniej sekcji.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="plan-listing"></a>Zaplanuj listę

W tej sekcji można skonfigurować szczegóły listy dla planu. W tym okienku są wyświetlane określone informacje, które mogą różnić się od innych planów w ramach tej samej oferty.

### <a name="plan-name"></a>Nazwa planu

To pole jest automatycznie wypełniane nazwą, która została nadana przez Ciebie podczas jego tworzenia. Ta nazwa jest wyświetlana w witrynie Azure Marketplace jako tytuł tego planu. Maksymalna liczba znaków to 100.

### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu, a nie oferty. To podsumowanie jest ograniczone do 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, i opisz różnice między planami w ramach oferty. Opisz tylko plan, a nie ofertę. Opis planu może zawierać maksymalnie 2 000 znaków.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="pricing-and-availability"></a>Cennik i dostępność

W tym okienku konfigurujesz:

- Rynki, w których ten plan jest dostępny. Każdy plan musi być dostępny na co najmniej jednym [rynku](marketplace-geo-availability-currencies.md).
- Cena za godzinę.
- Określa, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (odbiorców prywatnych).

### <a name="markets"></a>Wprowadza

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla każdej lokalizacji rynkowej, w której ten plan ma być dostępny do zakupu. (Użytkownicy na tych rynkach mogą nadal wdrażać oferty we wszystkich regionach świadczenia usługi Azure wybranych w sekcji ["Planowanie konfiguracji"](#plan-setup) ). Przycisk **podatek z podatkiem** przedstawia kraje/regiony, w których firma Microsoft przekazuje sprzedaż i korzysta z podatku w Twoim imieniu. Publikowanie w Chinach jest ograniczone do planów, które są *bezpłatne* lub mogą korzystać *z własnej licencji* (BYOL).

Jeśli już ustawisz ceny planu w walucie dolarów amerykańskich (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku jest obliczana zgodnie z bieżącymi stawkami za wymianę. Zawsze sprawdzaj cenę poszczególnych rynków przed opublikowaniem. Zapoznaj się z cennikiem, wybierając pozycję **Eksportuj ceny (xlsx)** po zapisaniu zmian.

Po usunięciu rynku klienci z tego rynku, którzy korzystają z aktywnych wdrożeń, nie będą mogli tworzyć nowych wdrożeń ani skalować istniejących wdrożeń. Istniejące wdrożenia nie są modyfikowane.

### <a name="pricing"></a>Cennik

W polu **model licencji** wybierz **miesięczny plan rozliczeniowy** , aby skonfigurować Cennik dla tego planu, lub wybierz **Bring Your Own License** , aby umożliwić klientom korzystanie z tego planu wraz z istniejącą licencją.

W przypadku miesięcznego planu rozliczeniowego opartego na użyciu należy użyć jednej z następujących trzech opcji wprowadzania cen:

- **Na rdzeń** : Zapewnij ceny za rdzeń w USD. Firma Microsoft oblicza ceny według rozmiaru rdzenia i konwertuje ją na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Rozmiar na rdzeń** : podaj ceny według rozmiaru rdzenia w USD. Firma Microsoft oblicza ceny i konwertuje ją na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Na rynek i rozmiar rdzeń** : podaj ceny każdego rozmiaru rdzenia dla wszystkich rynków. Ceny można zaimportować z arkusza kalkulacyjnego.

> [!NOTE]
> Zapisz zmiany cen, aby włączyć eksport danych cen. Po opublikowaniu ceny na rynku planu nie można jej później zmienić. Aby upewnić się, że ceny są prawidłowe przed ich opublikowaniem, wyeksportuj arkusz cen i przejrzyj ceny na każdym rynku.

### <a name="free-trial"></a>Bezpłatna wersja próbna

Możesz zaoferować klientom jeden miesiąc lub trzy miesiące lub sześć miesięcy *bezpłatnej wersji próbnej* .

### <a name="visibility"></a>Widoczność

Każdy plan można zaprojektować jako widoczny dla wszystkich lub tylko do wybranych użytkowników. Przypisywanie członkostw w tych ograniczonych odbiorcach przy użyciu identyfikatorów subskrypcji platformy Azure.

**Publiczna** : Twój plan może być widoczny dla każdego.

**Prywatni odbiorcy** : Ustaw, aby Twój plan był widoczny tylko dla wybranych odbiorców. Po opublikowaniu jako planu prywatnego można zaktualizować odbiorców lub zmienić je na publiczną. Po dokonaniu publicznego planu nie musi on być publiczny. Nie można jej zmienić z powrotem do planu prywatnego.

> [!NOTE]
> Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej w okienku **podglądu** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty _przed_ opublikowaniem jej na żywo w witrynie Azure Marketplace. Mimo że wybór dla odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany prywatne i publiczne na potrzeby weryfikacji.

**Odbiorcy z ograniczeniami (identyfikatory subskrypcji platformy Azure)** : Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego przy użyciu identyfikatorów subskrypcji platformy Azure. Opcjonalnie Dołącz opis każdego przypisanego identyfikatora subskrypcji platformy Azure. Po zaimportowaniu arkusza kalkulacyjnego CSV Dodaj do maksymalnie 10 identyfikatorów subskrypcji ręcznie lub do 20 000 identyfikatorów. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a wszystkie litery muszą być pisane małymi literami.

>[!Note]
>Oferty prywatne nie są obsługiwane w przypadku subskrypcji platformy Azure ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP).

### <a name="hide-a-plan"></a>Ukrywanie planu

Jeśli Twoja maszyna wirtualna ma być używana tylko pośrednio, gdy jest ona przywoływana przez inny szablon rozwiązania lub zarządzana aplikacja, zaznacz to pole wyboru, aby opublikować maszynę wirtualną, ale ukryć ją od klientów, którzy mogą bezpośrednio przeszukiwać lub przeglądać.

> [!NOTE]
> Ukryte plany nie obsługują linków podglądu.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Podaj obrazy i inne właściwości techniczne, które są skojarzone z tym planem. Aby uzyskać więcej informacji, zobacz sekcję [Konfigurowanie oferty maszyny wirtualnej szczegóły listy](azure-vm-create-listing.md).

> [!NOTE]
> Karta **konfiguracja techniczna** nie jest wyświetlana, jeśli ten plan został skonfigurowany do ponownego użycia pakietów z innego planu na karcie **Konfiguracja planu** .

### <a name="operating-system"></a>System operacyjny

W okienku **system operacyjny** wykonaj następujące czynności:

- W przypadku **rodziny systemów operacyjnych** wybierz system operacyjny **Windows** lub **Linux** .
- W polu **wydanie** lub **dostawca** wybierz dostawcę wydania systemu Windows lub Linux.
- W polu **przyjazna nazwa dla systemu** operacyjnego wprowadź przyjazną nazwę systemu operacyjnego. Ta nazwa jest widoczna dla klientów.

### <a name="recommended-vm-sizes"></a>Zalecane rozmiary maszyn wirtualnych

Wybierz maksymalnie sześć zalecanych rozmiarów maszyn wirtualnych, które mają być wyświetlane w witrynie Azure Marketplace.

### <a name="open-ports"></a>Otwieranie portów

Otwórz porty publiczne lub prywatne na wdrożonej maszynie wirtualnej.

### <a name="storage-option-for-deployment"></a>Opcja magazynu dla wdrożenia

Dla **opcji wdrożenie dysku** wybierz typ wdrożenia dysku, którego klienci mogą używać dla maszyny wirtualnej. Firma Microsoft zaleca ograniczenie wdrożenia tylko do **wdrożenia dysku zarządzanego** .

### <a name="properties"></a>Właściwości

Aby **zapewnić obsługę przyspieszonej sieci** , należy wybrać, czy maszyna wirtualna obsługuje [przyspieszone sieci](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Generacje

Generowanie maszyny wirtualnej definiuje sprzęt wirtualny, którego używa. W zależności od potrzeb klientów można opublikować maszynę wirtualną generacji 1, maszynę wirtualną generacji 2 lub obie te funkcje.

1. Podczas tworzenia nowej oferty wybierz **Typ generacji** i wprowadź szczegóły żądanego obrazu:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details.png" alt-text="Widok listy rozwijanej generacja.":::

2. Aby dodać kolejną generację do planu, wybierz pozycję **Dodaj generację** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Widok listy rozwijanej generacja.":::

    Następnie wprowadź szczegóły generacji:

    :::image type="content" source="./media/create-vm/azure-vm-generations-details.png" alt-text="Widok listy rozwijanej generacja.":::

    Wybrany **Identyfikator generacji** będzie widoczny dla klientów w miejscach takich jak adresy URL produktów i szablony ARM (jeśli dotyczy). Używaj tylko małych liter, znaków alfanumerycznych, łączników lub podkreśleń; Po opublikowaniu nie można go zmodyfikować.

3. Aby zaktualizować istniejącą maszynę wirtualną, która ma już opublikowaną generację, po prostu Edytuj szczegóły na stronie **konfiguracji technicznej** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="Widok listy rozwijanej generacja.":::

Aby dowiedzieć się więcej o różnicach między możliwościami generacji 1 i 2, zobacz [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>Obrazy maszyn wirtualnych

Podaj wersję dysku i identyfikator URI sygnatury dostępu współdzielonego (SAS) dla obrazów maszyn wirtualnych. Dodaj do 16 dysków danych dla każdego obrazu maszyny wirtualnej. Podaj tylko jedną nową wersję obrazu na plan w określonym założeniu. Po opublikowaniu obrazu nie można go edytować, ale można go usunąć. Usunięcie wersji uniemożliwia nowym i istniejącym użytkownikom wdrażanie nowego wystąpienia usuniętej wersji.

- **Wersja dysku** : wersja dostarczanego obrazu.
- **Identyfikator URI sygnatury dostępu współdzielonego** : lokalizacja na koncie usługi Azure Storage, w której zapisano dysk VHD systemu operacyjnego. Aby dowiedzieć się, jak uzyskać identyfikator URI sygnatury [dostępu współdzielonego, zobacz temat Uzyskiwanie identyfikatora URI dla tego obrazu maszyny wirtualnej](azure-vm-get-sas-uri.md).
- Obrazy dysków danych to również identyfikatory URI sygnatury dostępu współdzielonego, które są przechowywane na kontach usługi Azure Storage.
- Dodaj tylko jeden obraz do każdego przesłania w planie.

Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Podczas wdrażania klienci nie mogą usunąć dysków, które są częścią obrazu, ale zawsze mogą dodawać dyski w trakcie wdrażania lub po nim.

Wybierz pozycję **Zapisz wersję roboczą** przed kontynuowaniem i wróć do **omówienia planu** .

## <a name="next-steps"></a>Następne kroki

- [Dodawanie odbiorców w wersji zapoznawczej](azure-vm-create-preview.md)
