---
title: Planowanie oferty maszyny wirtualnej — Microsoft Commercial Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania oferty maszyny wirtualnej w witrynie Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: d92dad445b1aeace24dc0af7d95289f5535a5680
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281787"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Planowanie oferty maszyny wirtualnej

W tym artykule opisano różne opcje i wymagania dotyczące publikowania oferty maszyny wirtualnej w portalu komercyjnym. Oferty maszyn wirtualnych to oferty transakcyjne wdrożone i rozliczane za pomocą witryny Azure Marketplace.

Przed rozpoczęciem [Utwórz komercyjne konto w witrynie Marketplace w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) i upewnij się, że Twoje konto jest zarejestrowane w komercyjnym programie Marketplace.

### <a name="technical-fundamentals"></a>Podstawy techniczne

Proces projektowania, kompilowania i testowania ofert trwa od czasu i wymaga znajomości zarówno platformy Azure, jak i technologii używanych do tworzenia oferty. Zespół inżynieryjny powinien mieć działającą wiedzę o następujących technologiach firmy Microsoft:

- [Projektowanie i Architektura aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)

- Samouczki
  - [Maszyny wirtualne z systemem Linux](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Maszyny wirtualne z systemem Windows](../virtual-machines/windows/tutorial-manage-vm.md)

- Samples
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/powershell-samples.md)
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla maszyn wirtualnych z systemem Windows](../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell dla maszyn wirtualnych z systemem Windows](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

## <a name="technical-requirements"></a>Wymagania techniczne

Maszyna wirtualna zawiera dwa składniki:

- **Wirtualny dysk twardy (VHD) systemu operacyjnego** — zawiera system operacyjny i rozwiązanie, które jest wdrażane wraz z ofertą. Proces przygotowywania dysku VHD różni się w zależności od tego, czy jest to maszyna wirtualna z systemem Linux, Windows-, czy niestandardowa.
- **Wirtualne dyski twarde z danymi** (opcjonalnie) — dedykowany magazyn trwały dla maszyny wirtualnej. Nie używaj wirtualnego dysku twardego systemu operacyjnego (na przykład dysku C:) do przechowywania informacji trwałych. 
    - Możesz dołączyć do 16 dysków danych.
    - Użyj jednego wirtualnego dysku twardego na dysk danych, nawet jeśli dysk jest pusty.

    > [!NOTE]
    > Niezależnie od używanego systemu operacyjnego, należy dodać tylko minimalną liczbę dysków z danymi wymaganą przez rozwiązanie. Klienci nie mogą usunąć dysków, które są częścią obrazu w czasie wdrażania, ale mogą zawsze dodawać dyski w trakcie wdrażania lub po nim.

Oferty maszyn wirtualnych mają następujące wymagania techniczne:

- Należy przygotować jeden wirtualny dysk twardy (VHD) systemu operacyjnego. Dyski VHD z danymi są opcjonalne.
- Klient może w dowolnej chwili zrezygnować z oferty.
- Musisz utworzyć co najmniej jeden plan dla swojej oferty. Cena Twojego planu jest naliczana na podstawie wybranej [opcji licencjonowania](#licensing-options) .
   > [!IMPORTANT]
   > Każdy obraz maszyny wirtualnej w planie musi mieć taką samą liczbę dysków danych.

## <a name="preview-audience"></a>Podgląd odbiorców

Odbiorca w wersji zapoznawczej może uzyskać dostęp do oferty maszyny wirtualnej przed opublikowaniem jej w portalu Azure Marketplace w celu przetestowania kompleksowej funkcjonalności przed opublikowaniem jej na żywo. Na stronie **Podgląd odbiorców** można zdefiniować ograniczonych odbiorców w wersji zapoznawczej. 

> [!NOTE]
> Odbiorca w wersji zapoznawczej różni się od planu prywatnego. Plan prywatny jest dostępny tylko dla określonych odbiorców. Pozwala to na negocjowanie planu niestandardowego z konkretnymi klientami. Aby uzyskać więcej informacji, zobacz następną sekcję: plany.

Możesz wysyłać zaproszenia do adresów e-mail konta Microsoft (MSA) lub Azure Active Directory (Azure AD). Ręcznie Dodaj maksymalnie 10 adresów e-mail lub zaimportuj do 20 za pomocą pliku CSV. Jeśli Twoja oferta już istnieje, możesz zdefiniować odbiorcę w wersji zapoznawczej do testowania wszelkich zmian lub aktualizacji oferty.

## <a name="plans-and-pricing"></a>Plany i cennik

Oferty maszyn wirtualnych wymagają co najmniej jednego planu. Plan definiuje zakres i limity rozwiązania oraz powiązane ceny. Możesz utworzyć wiele planów dla oferty, aby dać klientom różne opcje techniczne i licencjonowania, a także bezpłatne wersje próbne. Zobacz [plany i cenniki dla ofert komercyjnych Marketplace](plans-pricing.md) , aby uzyskać ogólne wytyczne dotyczące planów, w tym modeli cenowych, bezpłatnych wersji próbnych i planów prywatnych. 

Maszyny wirtualne są w pełni obsługiwane w handlu z opcją płatność zgodnie z rzeczywistym użyciem lub BYOL. Firma Microsoft hostuje transakcję handlową i rozlicza klienta w Twoim imieniu. Korzyści wynikające z korzystania z preferowanej relacji płatności między klientem i firmą Microsoft, w tym dowolnymi umowami Enterprise Agreement. Aby uzyskać więcej informacji, zobacz [komercyjne funkcje Transact Marketplace](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations).

> [!NOTE]
> Zobowiązania pieniężne związane z Umowa Enterprise mogą być używane w odniesieniu do użycia platformy Azure przez maszynę wirtualną, ale nie do opłat za licencje na oprogramowanie.

### <a name="licensing-options"></a>Opcje licencjonowania

Podczas przygotowywania do opublikowania nowej oferty maszyny wirtualnej należy zdecydować, którą opcję licencjonowania wybrać. Pozwoli to określić, jakie dodatkowe informacje będą potrzebne później podczas tworzenia oferty w centrum partnerskim.

Dostępne są następujące opcje licencjonowania dla ofert maszyn wirtualnych:

| Opcja licencjonowania | Proces transakcji |
| --- | --- |
| Bezpłatna wersja próbna | Oferuj swoim klientom jedną lub sześć miesięcy bezpłatnej wersji próbnej. |
| Wersja testowa | Ta opcja umożliwia klientom ocenę maszyn wirtualnych bez dodatkowych kosztów. Nie musi to być istniejący klient platformy Azure w celu zaangażować się z wersją próbną. Aby uzyskać szczegółowe informacje, zobacz [co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/what-is-test-drive) |
| BYOL | Opcja Przenieś własną licencję pozwala klientom na przenoszenie istniejących licencji na oprogramowanie na platformę Azure.\* |
| Na podstawie użycia | Opcja ta pozwala klientom na płatność zgodnie z rzeczywistym użyciem. |
| Interaktywny pokaz  | Zapoznaj się z przewodnikiem po rozwiązaniu problemu przy użyciu interaktywnej prezentacji. Korzyścią jest możliwość oferowania wersji próbnej bez konieczności zapewnienia skomplikowanej konfiguracji złożonego rozwiązania. |
|

\* Wydawca obsługuje wszystkie aspekty transakcji licencji na oprogramowanie, w tym (ale nie ograniczone do) kolejności, realizacji, pomiaru, rozliczeń, fakturowania, płatności i kolekcji.

Poniższy przykład przedstawia ofertę maszyny wirtualnej w portalu Azure Marketplace, która ma Cennik oparty na użyciu.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Ekran oferty przykładowej maszyny wirtualnej.":::

### <a name="private-plans"></a>Plany prywatne

Możesz ograniczyć odnajdywanie i wdrażanie maszyny wirtualnej do określonego zestawu klientów, publikując obraz i cennik jako plan prywatny. Plany prywatne odblokują możliwość tworzenia ofert na wyłączność dla najbliższych klientów i oferowania niestandardowego oprogramowania i warunków. Dostosowane warunki umożliwiają wyróżnianie różnorodnych scenariuszy, w tym w przypadku, gdy użytkownik korzysta z wyspecjalizowanych cen i warunków oraz wczesnego dostępu do ograniczonej wersji oprogramowania. Plany prywatne umożliwiają podanie określonych cen lub produktów do ograniczonego zestawu klientów.

Aby uzyskać więcej informacji, zobacz [plany i cenniki dla ofert komercyjnych](plans-pricing.md) i [prywatnych ofert w portalu Azure Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).

## <a name="test-drives"></a>Wersje testowe

Możesz włączyć test dla maszyny wirtualnej. Dyski testowe zapewniają klientom dostęp do wstępnie skonfigurowanego środowiska przez określoną liczbę godzin. Możesz włączyć dyski testowe dla dowolnej opcji publikowania, jednak ta funkcja ma dodatkowe wymagania. Aby dowiedzieć się więcej na temat dysków testowych, zobacz [co to jest dysk testowy?](what-is-test-drive.md) Aby uzyskać informacje o konfigurowaniu różnych rodzajów dysków testowych, zobacz temat [konfiguracja techniczna systemu testowego](test-drive-technical-configuration.md).

> [!TIP]
> Stacja testowa jest różna od [bezpłatnej wersji próbnej](plans-pricing.md#free-trials). Możesz zaoferować wersję testową, bezpłatny okres próbny lub oba te elementy. Oba te osoby zapewniają klientom swoje rozwiązanie w ustalonym czasie. Jednak dysk testowy zawiera również praktyczny przewodnik po najważniejszych funkcjach produktu i korzyści, jakie można przedstawić w rzeczywistym scenariuszu implementacji.

## <a name="customer-leads"></a>Potencjalni klienci

Aby zbierać informacje o klientach, należy połączyć swoją ofertę z systemem zarządzania relacjami z klientami (CRM). Klient zostanie poproszony o zgodę na udostępnienie swoich informacji. Te szczegóły klienta, wraz z nazwą oferty, IDENTYFIKATORem i sklepem online, gdzie znalazły ofertę, zostaną wysłane do skonfigurowanego systemu CRM. Komercyjna witryna Marketplace obsługuje różne systemy CRM, a także opcję używania tabeli platformy Azure lub konfigurowania punktu końcowego HTTPS przy użyciu automatyzacji.

Połączenie programu CRM można dodać lub zmodyfikować w dowolnym momencie podczas tworzenia oferty lub po niej. Aby uzyskać szczegółowe wskazówki, zobacz temat [potencjalni klienci z oferty komercyjnej witryny Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Umowy prawne

Aby uprościć proces zakupów dla klientów i zmniejszyć złożoność prawną dla dostawców oprogramowania, firma Microsoft oferuje standardowy kontrakt, którego możesz użyć dla ofert w portalu komercyjnym. W przypadku oferowania oprogramowania zgodnie z umową standardowa klienci muszą tylko raz odczytywać i akceptować ją i nie muszą tworzyć niestandardowych warunków i postanowień.

Jeśli zdecydujesz się na korzystanie z kontraktu standardowego, możesz dodać postanowienia dotyczące uniwersalnej poprawki i maksymalnie 10 niestandardowych zmian do standardowej umowy. Możesz również użyć własnych warunków i postanowień zamiast standardowej umowy. Te szczegóły będą zarządzane na stronie **Właściwości** . Aby uzyskać szczegółowe informacje, zobacz [Standard Contract for Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Po opublikowaniu oferty przy użyciu standardowej umowy dotyczącej komercyjnej witryny Marketplace nie można użyć własnych niestandardowych warunków i postanowień. Jest to scenariusz "lub". Możesz zaoferować swoje rozwiązanie w ramach standardowej umowy lub własnych warunków i postanowień. Jeśli chcesz zmodyfikować warunki kontraktu standardowego, możesz to zrobić za pomocą standardowych poprawek kontraktu.

## <a name="cloud-solution-providers"></a>Dostawcy rozwiązań w chmurze

Podczas tworzenia oferty w centrum partnerskim zostanie wyświetlona karta **odsprzedawana za pomocą dostawcy CSP** . Ta opcja umożliwia partnerom należącym do programu Microsoft Cloud dostawcy rozwiązań (CSP) odsprzedaż maszyny wirtualnej w ramach powiązanej oferty. Wszystkie plany przyłączenia do własnej licencji (BYOL) są automatycznie dodanych do programu. Możesz również zdecydować się na wybór planów BYOL. Aby uzyskać więcej informacji, zobacz [program Cloud Solution Provider](cloud-solution-providers.md) . 

> [!NOTE]
> Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna. Aby uzyskać więcej informacji na temat marketingu oferty za poorednictwem kanałów partnerów CSP firmy Microsoft, zobacz [**dostawcy rozwiązań w chmurze**](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers).

## <a name="next-steps"></a>Następne kroki

- [Utwórz maszynę wirtualną przy użyciu zatwierdzonej bazy](azure-vm-create-using-approved-base.md) lub [Utwórz maszynę wirtualną przy użyciu własnego obrazu](azure-vm-create-using-own-image.md).
- [Najlepsze rozwiązania dotyczące ofert](gtm-offer-listing-best-practices.md)
