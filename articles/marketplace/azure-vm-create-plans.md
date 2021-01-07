---
title: Tworzenie planów dla oferty maszyny wirtualnej w witrynie Azure Marketplace
description: Dowiedz się, jak utworzyć plany dla oferty maszyny wirtualnej w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: e5fb425afdd4b212a0b28ce91418eb0ee9e3632f
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964437"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Jak utworzyć plany dla oferty maszyny wirtualnej

Na stronie **Przegląd planu** (wybierz z menu po lewej stronie w centrum partnerskim) możesz udostępnić różne opcje planu w ramach tej samej oferty. Oferta wymaga co najmniej jednego planu (dawniej nazywanego jednostką SKU), która może różnić się w zależności od zysków odbiorców, regionu platformy Azure, funkcji lub obrazów maszyn wirtualnych.

Możesz utworzyć do 100 planów dla każdej oferty: maksymalnie 45 z nich mogą być prywatne. Dowiedz się więcej o planach prywatnych w [ofertach prywatnych w komercyjnym portalu Microsoft Marketplace](private-offers.md).

Po utworzeniu planów wybierz kartę **Przegląd planu** , aby wyświetlić:

- Nazwy planów
- Modele licencji
- Odbiorcy (publiczna lub prywatna)
- Bieżący stan publikowania
- Dostępne akcje

Akcje dostępne w okienku **Przegląd planu** różnią się w zależności od bieżącego stanu planu.

- Jeśli plan jest stanem wersja robocza, wybierz pozycję **Usuń wersję roboczą**.
- Jeśli plan jest publikowany na żywo, wybierz pozycję **Zatrzymaj sprzedawanie planu** lub **zsynchronizuj odbiorców prywatnych**.

## <a name="create-a-new-plan"></a>Utwórz nowy plan

Wybierz pozycję **+ Utwórz nowy plan** u góry.

W oknie dialogowym **nowy plan** wprowadź unikatowy **identyfikator planu** dla każdego planu w ramach tej oferty. Ten identyfikator będzie widoczny dla klientów w adresie sieci Web produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń i maksymalnie 50 znaków.

> [!NOTE]
> Nie można zmienić identyfikatora planu po wybraniu opcji **Utwórz**.

Wprowadź **nazwę planu**. Klienci widzą tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę, która jasno wskazuje różnice między planami. Na przykład możesz wprowadzić **system Windows Server** z opcją *płatność zgodnie z rzeczywistym* użyciem, *BYOL*, *zaawansowanym* i planem *przedsiębiorstwa* .

Wybierz przycisk **Utwórz**. Spowoduje to otwarcie strony **Konfiguracja planu** .

## <a name="plan-setup"></a>Konfigurowanie planu

Ustaw konfigurację wysokiego poziomu dla typu planu, określ, czy ma ona replikować konfigurację techniczną z innego planu, i zidentyfikuj regiony platformy Azure, w których plan ma być dostępny. Wybrane tutaj ustawienia określają, które pola są wyświetlane w innych okienkach dla tego samego planu.

### <a name="reuse-technical-configuration"></a>Ponownie Użyj konfiguracji technicznej

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie korzysta z konfiguracji technicznej z innego planu**. Ta opcja umożliwia wybranie jednego z innych planów tego samego typu dla tej oferty i umożliwia ponowne użycie konfiguracji technicznej.

> [!NOTE]
> W przypadku ponownego użycia konfiguracji technicznej z poziomu innego planu, cała karta **konfiguracja techniczna** znika z tego planu. Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, zostaną użyte również dla tego planu. Nie można zmienić tego ustawienia po opublikowaniu planu.

### <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Plan musi być dostępny w co najmniej jednym regionie świadczenia usługi Azure.

Wybierz pozycję **Azure Global** , aby udostępnić swój plan klientom we wszystkich regionach globalnych platformy Azure, które mają komercyjną integrację z portalu Marketplace. Aby uzyskać więcej informacji, zobacz [dostępność geograficzna i obsługa waluty](marketplace-geo-availability-currencies.md).

Wybierz **Azure Government** , aby udostępnić plan w regionie [Azure Government](../azure-government/documentation-government-welcome.md) . Ten region zapewnia klientom kontrolowany dostęp do jednostek federalnych, stanowych, lokalnych i plemienne, a także dla partnerów uprawnionych do ich świadczenia. Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania. Azure Government używa fizycznie izolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).

Przed opublikowaniem w celu [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)Przetestuj i sprawdź swój plan w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby skonfigurować i przetestować plan, zażądaj konta próbnego na stronie [Microsoft Azure Government wersji próbnej](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonym regionie świadczenia usługi Azure nie można usunąć tego regionu.

### <a name="azure-government-certifications"></a>Certyfikaty Azure Government

Ta opcja jest widoczna tylko w przypadku wybrania **Azure Government** jako regionu świadczenia usługi Azure w poprzedniej sekcji.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom i wymogom rządowym. Na przykład FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS. Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków, które je opisują. Mogą to być albo linki do swojej aukcji bezpośrednio w programie, jak i linki do opisów zgodności z ich własnymi witrynami sieci Web. Te linki są widoczne tylko dla Azure Government klientów.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty w menu planu po lewej stronie, **Zaplanuj listę**.

## <a name="plan-listing"></a>Zaplanuj listę

Skonfiguruj szczegóły listy dla planu. W tym okienku są wyświetlane określone informacje, które mogą różnić się od innych planów w ramach tej samej oferty.

### <a name="plan-name"></a>Nazwa planu

To pole jest automatycznie wypełniane nazwą, która została nadana przez Ciebie podczas jego tworzenia. Ta nazwa jest wyświetlana w witrynie Azure Marketplace jako tytuł tego planu. Maksymalna liczba znaków to 100.

### <a name="plan-summary"></a>Podsumowanie planu

Podaj krótkie podsumowanie planu, a nie oferty. To podsumowanie jest ograniczone do 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest unikatowy, i opisz różnice między planami w ramach oferty. Opisz tylko plan, a nie ofertę. Opis planu może zawierać maksymalnie 2 000 znaków.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty w menu planu po lewej stronie, **Cennik i dostępność**.

## <a name="pricing-and-availability"></a>Cennik i dostępność

W tym okienku konfigurujesz:

- Rynki, w których ten plan jest dostępny. Każdy plan musi być dostępny na co najmniej jednym [rynku](marketplace-geo-availability-currencies.md).
- Cena za godzinę.
- Określa, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (odbiorców prywatnych).

### <a name="markets"></a>Wprowadza

Każdy plan musi być dostępny na co najmniej jednym rynku. Większość rynków jest domyślnie zaznaczona. Aby edytować listę, wybierz opcję **Edytuj rynki** i zaznacz lub wyczyść pola wyboru dla każdej lokalizacji rynkowej, w której ten plan ma (lub nie powinien) być dostępny do zakupu. Użytkownicy na wybranych rynkach nadal mogą wdrożyć ofertę we wszystkich regionach świadczenia usługi Azure wybranych w sekcji ["Planowanie konfiguracji"](#plan-setup) .

Wybierz opcję **Wybierz tylko podatek firmy Microsoft** , który zostanie przekazany do wyboru tylko w krajach/regionach, w których firma Microsoft przyniesie sprzedaż i użyje podatku w Twoim imieniu. Publikowanie w Chinach jest ograniczone do planów, które są *bezpłatne* lub mogą korzystać *z własnej licencji* (BYOL).

Jeśli już ustawisz ceny planu w walucie dolarów amerykańskich (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku jest obliczana zgodnie z bieżącymi stawkami za wymianę. Zawsze sprawdzaj cenę poszczególnych rynków przed opublikowaniem. Zapoznaj się z cennikiem, wybierając pozycję **Eksportuj ceny (xlsx)** po zapisaniu zmian.

Po usunięciu rynku klienci z tego rynku, którzy korzystają z aktywnych wdrożeń, nie będą mogli tworzyć nowych wdrożeń ani skalować istniejących wdrożeń. Istniejące wdrożenia nie są modyfikowane.

Wybierz pozycję **Zapisz** , aby kontynuować.

### <a name="pricing"></a>Cennik

W przypadku **modelu licencji** wybierz **miesięczny plan rozliczeniowy oparty na użyciu** , aby skonfigurować Cennik dla tego planu lub **Bring Your Own License** , aby umożliwić klientom korzystanie z tego planu w ramach istniejącej licencji.

W przypadku miesięcznego planu rozliczeniowego opartego na użyciu należy skorzystać z jednej z następujących trzech opcji wpisów cen:

- **Na rdzeń** — zapewnianie cen za rdzeń w USD. Firma Microsoft oblicza ceny według rozmiaru rdzenia i konwertuje ją na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Na rozmiar podstawowy** — zapewnianie cen według rozmiaru rdzenia w USD. Firma Microsoft oblicza ceny i konwertuje ją na waluty lokalne przy użyciu bieżącego kursu wymiany.
- **Na rynek i rozmiar podstawowy** — zapewniają ceny każdego rozmiaru rdzenia dla wszystkich rynków. Ceny można zaimportować z arkusza kalkulacyjnego.

Wprowadź **cenę za rdzeń**, a następnie wybierz pozycję **Cena za rozmiar podstawowy** , aby wyświetlić tabelę obliczeń cen/godzin.

> [!NOTE]
> Zapisz zmiany cen, aby włączyć eksport danych cen. Po opublikowaniu ceny na rynku planu nie można jej później zmienić. Aby upewnić się, że ceny są prawidłowe przed ich opublikowaniem, wyeksportuj arkusz cen i przejrzyj ceny na każdym rynku.

### <a name="free-trial"></a>Bezpłatna wersja próbna

Możesz zaoferować swoim klientom jedną z trzech lub sześć-miesięcznych **bezpłatnych wersji próbnych** .

### <a name="plan-visibility"></a>Widoczność planu

Każdy plan można zaprojektować jako widoczny dla wszystkich lub tylko do wybranych użytkowników. Przypisywanie członkostw w tych ograniczonych odbiorcach przy użyciu identyfikatorów subskrypcji platformy Azure.

**Publiczna**: Twój plan może być widoczny dla każdego.

**Prywatne**: Ustaw swój plan jako widoczny tylko dla wybranych odbiorców. Po opublikowaniu jako planu prywatnego można zaktualizować odbiorców lub zmienić je na publiczną. Po dokonaniu publicznego planu nie musi on być publiczny. Nie można jej zmienić z powrotem do planu prywatnego.

Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego przy użyciu **identyfikatora subskrypcji platformy Azure**. Opcjonalnie Dołącz **Opis** każdego przypisanego identyfikatora subskrypcji platformy Azure. W przypadku importowania arkusza kalkulacyjnego CSV ręcznie Dodaj do 10 identyfikatorów subskrypcji lub maksymalnie 20 000. Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a wszystkie litery muszą być małymi literami.

> [!NOTE]
> Prywatna lub ograniczona Grupa odbiorców różni się od odbiorców w wersji zapoznawczej zdefiniowanej w okienku **podglądu** . Odbiorca wersji zapoznawczej może uzyskać dostęp do oferty *przed* opublikowaniem jej na żywo w witrynie Azure Marketplace. Mimo że wybór dla odbiorców prywatnych dotyczy tylko określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany prywatne i publiczne na potrzeby weryfikacji.

Oferty prywatne nie są obsługiwane w przypadku subskrypcji platformy Azure ustanowionych przez odsprzedawcę programu dostawcy rozwiązań w chmurze (CSP).

### <a name="hide-plan"></a>Ukryj plan

Jeśli Twoja maszyna wirtualna ma być używana tylko pośrednio, gdy jest ona przywoływana przez inny szablon rozwiązania lub zarządzana aplikacja, zaznacz to pole wyboru, aby opublikować maszynę wirtualną, ale ukryć ją od klientów, którzy mogą bezpośrednio przeszukiwać lub przeglądać.

Ukryte plany nie obsługują linków podglądu.

Wybierz pozycję **Zapisz wersję roboczą** przed przejściem do następnej karty w menu plan z lewej strony, **konfiguracja techniczna**.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Podaj obrazy i inne właściwości techniczne skojarzone z tym planem.

> [!NOTE]
> Ta karta nie jest wyświetlana, jeśli ten plan został skonfigurowany tak, aby ponownie używał pakietów z innego na karcie **Konfiguracja planu** .

### <a name="operating-system"></a>System operacyjny

Wybierz rodzinę systemów operacyjnych **Windows** lub **Linux** .

Wybierz **dostawcę** **wydania** systemu Windows lub Linux.

Wprowadź **przyjazną nazwę** systemu operacyjnego. Ta nazwa jest widoczna dla klientów.

### <a name="recommended-vm-sizes"></a>Zalecane rozmiary maszyn wirtualnych

Wybierz link, aby wybrać maksymalnie sześć zalecanych rozmiarów maszyn wirtualnych, które mają być wyświetlane w witrynie Azure Marketplace.

### <a name="open-ports"></a>Otwieranie portów

Dodaj otwarte porty publiczne lub prywatne na wdrożonej maszynie wirtualnej.

### <a name="properties"></a>Właściwości

Wybierz, czy maszyna wirtualna **obsługuje przyspieszone sieci**. Aby uzyskać szczegółowe informacje, zobacz [przyspieszone sieci](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Generacje

Generowanie maszyny wirtualnej definiuje sprzęt wirtualny, którego używa. W zależności od potrzeb klientów można opublikować maszynę wirtualną generacji 1, maszynę wirtualną generacji 2 lub obie te funkcje.

1. Podczas tworzenia nowej oferty wybierz **Typ generacji** i wprowadź żądane szczegóły:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="Widok sekcji Szczegóły generowania w centrum partnerskim.":::

2. Aby dodać kolejną generację do planu, wybierz pozycję **Dodaj generację**...

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Widok łącza &quot;Dodaj generację&quot;.":::

    ... i wprowadź żądane szczegóły:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="Widok okna Szczegóły generacji.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Aby zaktualizować istniejącą maszynę wirtualną, która została już opublikowana, Edytuj szczegóły na stronie **konfiguracja techniczna** .

Aby dowiedzieć się więcej o różnicach między możliwościami generacji 1 i 2, zobacz [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>Obrazy maszyn wirtualnych

Podaj wersję dysku i identyfikator URI sygnatury dostępu współdzielonego (SAS) dla obrazów maszyn wirtualnych. Dodaj do 16 dysków danych dla każdego obrazu maszyny wirtualnej. Podaj tylko jedną nową wersję obrazu na plan w określonym założeniu. Po opublikowaniu obrazu nie można go edytować, ale można go usunąć. Usunięcie wersji uniemożliwia nowym i istniejącym użytkownikom wdrażanie nowego wystąpienia usuniętej wersji.

Te dwa wymagane pola są pokazane na poprzedniej ilustracji powyżej:

- **Wersja dysku**: wersja dostarczanego obrazu.
- **Łącze wirtualnego dysku twardego** systemu operacyjnego: lokalizacja na koncie usługi Azure Storage dla wirtualnego dysku twardego systemu operacyjnego. Aby dowiedzieć się, jak uzyskać identyfikator URI sygnatury [dostępu współdzielonego, zobacz temat Uzyskiwanie identyfikatora URI dla tego obrazu maszyny wirtualnej](azure-vm-get-sas-uri.md).

Dyski danych (wybierz opcję **Dodaj dysk danych (maksymalnie 16)**) to również identyfikatory URI sygnatury dostępu współdzielonego, które są przechowywane na kontach usługi Azure Storage. Dodaj tylko jeden obraz do każdego przesłania w planie.

Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Podczas wdrażania klienci nie mogą usunąć dysków, które są częścią obrazu, ale zawsze mogą dodawać dyski w trakcie wdrażania lub po nim.

Wybierz pozycję **Zapisz wersję roboczą**, a następnie wybierz pozycję **Przegląd planu ←** w lewym górnym rogu, aby zobaczyć właśnie utworzony plan.

## <a name="next-steps"></a>Następne kroki

- [Odsprzedaż za pośrednictwem dostawców CSP](azure-vm-create-resell-csp.md)
