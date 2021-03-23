---
title: Dostosowywanie ocen dla Azure Migrate | Microsoft Docs
description: Opisuje sposób dostosowywania ocen utworzonych za pomocą Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785210"
---
# <a name="customize-an-assessment"></a>Dostosowywanie oceny

W tym artykule opisano sposób dostosowywania ocen utworzonych przez narzędzie Azure Migrate odnajdywania i oceny.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Za pomocą narzędzia do odnajdywania i oceny Azure Migrate można tworzyć oceny dla lokalnych maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V w ramach przygotowania do migracji na platformę Azure. Narzędzie do odnajdywania i oceny ocenia serwery lokalne na potrzeby migracji do maszyn wirtualnych IaaS platformy Azure i rozwiązania VMware platformy Azure (Automatyczna synchronizacja). 

## <a name="about-assessments"></a>Informacje o ocenach

Oceny tworzone za pomocą narzędzia do odnajdywania i oceny są migawką danych w danym momencie. Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: odnajdywania i oceny.

**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Możesz ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md), [maszyny wirtualne funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md)i [serwery fizyczne](how-to-set-up-appliance-physical.md) do migracji na platformę Azure przy użyciu tego typu oceny. (concepts-assessment-calculation.md)
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) pod kątem migracji do usługi Azure VMware Solution (AVS).[Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

Opcje ustalania rozmiarów kryteriów w Azure Migrate oceny:

**Kryterium określania rozmiaru** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na danych użycia procesora i pamięci.<br/><br/> Rekomendacja dotycząca typu dysku (dysk HDD/SSD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) jest oparta na liczbie operacji we/wy na sekundę i przepływności dysków lokalnych.<br/><br/>**Ocena usługi Azure SQL**: Konfiguracja usługi Azure SQL jest oparta na danych wydajności wystąpień SQL i baz danych, w tym: użycie procesora CPU, użycie pamięci, operacje we/wy na sekundę (pliki danych i dziennika), przepływność i opóźnienie operacji wejścia/wyjścia<br/><br/>**Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na danych użycia procesora i pamięci.
**Zgodnie ze środowiskiem lokalnym** | Oceny, które nie wykorzystują danych wydajności w celu wydawania rekomendacji. | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na lokalnym rozmiarze maszyny wirtualnej<br/><br> Zalecany typ dysku jest określany na podstawie tego, co zostało wybrane w ustawieniu typu magazynu na potrzeby oceny.<br/><br/> **Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na lokalnym rozmiarze maszyny wirtualnej.

## <a name="how-is-an-assessment-done"></a>Jak odbywa się Ocena?

Ocena Azure Migrate odnajdywania i oceny ma trzy etapy. Ocena rozpoczyna się od analizy gotowości, a następnie ustalania rozmiarów, a wreszcie szacowania kosztów miesięcznych. Komputer jest przenoszony tylko do późniejszego etapu, jeśli przekaże poprzednią. Na przykład jeśli komputer nie może sprawdzić przydatności do platformy Azure, zostanie oznaczony jako nieodpowiedni dla platformy Azure, a zmiany rozmiarów i kosztów nie będą wykonywane. [Dowiedz się więcej.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Co to jest ocena maszyny wirtualnej platformy Azure?

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Lokalizacja platformy Azure, do której chcesz przeprowadzić migrację.<br/> Ocena maszyny wirtualnej platformy Azure obsługuje obecnie następujące regiony docelowe: Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Kanada środkowa, Kanada Wschodnia, Indie Środkowe, środkowe stany USA, Chiny Wschodnie, Chiny Północne, Azja Wschodnia, Wschodnie stany USA, East stany USA 2, Niemcy środkowe, Niemcy Wschodnie, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa, Południowe stany USA, Europa Północna, Południowe stany USA, Azja Południowo-Wschodnia, Indie Południowo-środkowe, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo , US Gov Teksas, US Gov Wirginia, zachodnio-środkowe stany USA, Europa Zachodnia, Indie Zachodnie, zachodnie stany USA i zachodnie stany USA 2.
**Typ magazynu** | Za pomocą tej właściwości można określić typ dysków, na które chcesz przenieść na platformę Azure.<br/><br/> W przypadku lokalnego określania rozmiarów można określić docelowy typ magazynu jako dyski zarządzane w warstwie Premium, dyski zarządzane SSD w warstwie Standardowa lub dyski zarządzane HDD w warstwie Standardowa. W przypadku ustalania rozmiarów na podstawie wydajności można określić typ dysku docelowego w postaci dysków zarządzanych w warstwie Premium, dysków zarządzanych przez program HDD w warstwie Standardowa lub dysków zarządzanych przy użyciu SSD w warstwie Standardowa.<br/><br/> Jeśli typ magazynu zostanie określony jako automatyczny, zalecenia dotyczące dysku są wykonywane na podstawie danych wydajności dysków (IOPS i przepływności). W przypadku określenia typu magazynu jako warstwy Premium/standardowa Ocena będzie polecać dyskową jednostkę SKU w wybranym typie magazynu. Jeśli chcesz uzyskać umowę SLA dotyczącą pojedynczej instancji na maszynę wirtualną o 99,9%, możesz określić typ magazynu jako dysk zarządzany w warstwie Premium. Gwarantuje to, że wszystkie dyski w ocenie są zalecane jako dyski zarządzane w warstwie Premium. Azure
**Wystąpienia zarezerwowane (RI)** | Ta właściwość pozwala określić, czy istnieją [wystąpienia zarezerwowane](https://azure.microsoft.com/pricing/reserved-vm-instances/) na platformie Azure, szacowania kosztów w ocenie są następnie wykonywane z uwzględnieniem zniżek na RI. Wystąpienia zarezerwowane są obecnie obsługiwane tylko w przypadku oferty z opcją płatność zgodnie z rzeczywistym użyciem w Azure Migrate.
**Kryterium ustalania wielkości** | Kryterium, które ma być używane dla maszyn wirtualnych o odpowiednim rozmiarze dla platformy Azure. Można albo zmienić rozmiar maszyny wirtualnej na *podstawie wydajności* , jak i *w ten sposób*, bez uwzględniania historii wydajności.
**Historia wydajności** | Czas, który należy wziąć pod uwagę przy ocenie danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest *oparte na wydajności*.
**Użycie percentyla** | Wartość percentyla próbki wydajności uwzględniana w celu prawidłowego określenia rozmiaru. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest *oparty na wydajności*.
**Serie maszyn wirtualnych** |     Określić możesz serie maszyn wirtualnych, które mają być brane pod uwagę podczas ustalania właściwego rozmiaru. Na przykład jeśli masz środowisko produkcyjne, które nie jest planowane do migracji na maszyny wirtualne z serii A na platformie Azure, możesz wykluczyć serię z listy lub serii, a po prawej stronie jest wykonywane tylko w wybranej serii.
**Współczynnik komfortu** | Ocena maszyn wirtualnych platformy Azure traktuje bufor (współczynnik komfortu) podczas oceny. Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną.
**Oferta** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/), w której dokonano rejestracji. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.
**Waluta** | Twoja waluta rozliczeniowa.
**Rabat (%)** | Dowolny rabat skojarzony z daną subskrypcją, stosowany do całej oferty platformy Azure.<br/> Ustawienie domyślne to 0%.
**Czas pracy maszyny wirtualnej** | Jeśli maszyny wirtualne nie będą działały 24x7 na platformie Azure, możesz określić czas trwania (liczbę dni miesięcznie i liczbę godzin dziennie), dla których byłyby uruchomione, a oszacowania kosztów zostaną odpowiednio wykonane.<br/> Wartość domyślna to 31 dni w miesiącu i 24 godziny dziennie.
**Korzyść użycia hybrydowego platformy Azure** | Określ, czy masz program Software Assurance i czy masz uprawnienia do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). W przypadku wybrania opcji Yes maszyny wirtualne z systemem Windows są wyceniane jak platforma Azure z systemami innymi niż Windows. Wartość domyślna to Yes.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Co to jest ocena rozwiązania Azure VMware (Automatyczna synchronizacja)?

Oto nowości zawarta w ocenie automatycznej synchronizacji:


| **Właściwość** | **Szczegóły** |
| - | - |
| **Lokalizacja docelowa** | Określa lokalizację chmury prywatnej automatycznej synchronizacji, do której chcesz przeprowadzić migrację.<br/><br/> Ocena automatycznej synchronizacji obsługuje obecnie te regiony docelowe: Wschodnie stany USA, Europa Zachodnia, zachodnie stany USA. |
| **Typ magazynu** | Określa aparat magazynu, który ma być używany w ramach automatycznej synchronizacji.<br/><br/> Należy zauważyć, że oceny automatycznej synchronizacji obsługują tylko sieci vSAN jako domyślny typ magazynu. |
**Wystąpienia zarezerwowane (RIs)** | Ta właściwość pomaga określić zarezerwowane wystąpienia w automatycznej synchronizacji. Usługi RIs nie są obecnie obsługiwane w przypadku węzłów automatycznej synchronizacji. |
**Typ węzła** | Określa [Typ węzła automatycznej synchronizacji](../azure-vmware/concepts-private-clouds-clusters.md) , używany do mapowania lokalnych maszyn wirtualnych. Należy pamiętać, że domyślnym typem węzła jest AV36. <br/><br/> Azure Migrate będzie wymagała wymaganej liczby węzłów do migracji maszyn wirtualnych do automatycznej synchronizacji. |
**Ustawienie FTT, poziom RAID** | Określa, że ma to zastosowanie do niedopuszczalnych połączeń i kombinacji RAID. Wybrana opcja FTT z wymaganiami dotyczącymi lokalnego dysku maszyny wirtualnej określi łączny magazyn sieci vSAN wymagany w ramach automatycznej synchronizacji. |
**Kryterium ustalania wielkości** | Ustawia kryteria do użycia dla maszyn wirtualnych o _odpowiednim rozmiarze_ na potrzeby automatycznej synchronizacji. Możesz wybrać opcję ustalania rozmiarów na _podstawie wydajności_ lub _jako lokalne_ , bez uwzględniania historii wydajności. |
**Historia wydajności** | Określa czas, jaki należy wziąć pod uwagę podczas oceniania danych wydajności maszyn. Ta właściwość ma zastosowanie tylko wtedy, gdy kryterium ustalania wielkości jest _oparte na wydajności_. |
**Użycie percentyla** | Określa wartość percentylości zestawu próbek wydajności, który ma być brany pod uwagę w przypadku zmiany wielkości liter. Ta właściwość ma zastosowanie tylko wtedy, gdy rozmiar jest oparty na wydajności.|
**Współczynnik komfortu** | Podczas oceny usługa Azure Migrate uwzględnia bufor (współczynnik komfortu). Jest on stosowany do wszystkich danych użycia maszyn wirtualnych (procesora, pamięci, dysku i sieci). Współczynnik komfortu uwzględnia kwestie, takie jak okresowe użycie, krótka historia wydajności i prawdopodobne zwiększenie użycia w przyszłości.<br/><br/> Na przykład 10-rdzeniowa maszyna wirtualna o użyciu na poziomie 20% jest w normalnych warunkach równoważna 2-rdzeniowej maszynie wirtualnej. Jednak wynik zastosowania współczynnika komfortu o wartości 2 daje 4-rdzeniową maszynę wirtualną. |
**Oferta** | Wyświetla [ofertę platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , która jest zarejestrowana. Zgodnie z tym usługa Azure Migrate odpowiednio szacuje koszty.|
**Waluta** | Przedstawia walutę rozliczeń dla Twojego konta. |
**Rabat (%)** | Wyświetla rabat związany z subskrypcją, który otrzymujesz w górnej części oferty platformy Azure. Ustawienie domyślne to 0%. |
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz program Software Assurance i kwalifikujesz się do [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Chociaż nie ma to wpływu na ceny rozwiązań VMware platformy Azure ze względu na cenę opartą na węzłach, klienci mogą nadal stosować licencje lokalnych systemów operacyjnych (Microsoft based) w ramach automatycznej synchronizacji przy użyciu korzyści użycia hybrydowego platformy Azure. Inni dostawcy systemu operacyjnego oprogramowania będą musieli podać własne warunki licencjonowania, takie jak RHEL. |
**vCPU nadsubskrypcji** | Określa stosunek liczby rdzeni wirtualnych związanych z 1 rdzeniem fizycznym w węźle automatyczna synchronizacja. Wartość domyślna w obliczeniach to 4 vCPU: 1 rdzeń fizyczny w wersji zaautomatycznej. <br/><br/> Użytkownicy interfejsu API mogą ustawić tę wartość jako liczbę całkowitą. Należy pamiętać, że vCPU nad> 4:1 może rozpocząć spadek wydajności, ale może być używany do obciążeń typu serwer sieci Web. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Jakie właściwości są używane do tworzenia i dostosowywania oceny usługi Azure SQL?

Oto nowości uwzględnione we właściwościach oceny usługi Azure SQL:

**Właściwość** | **Szczegóły**
--- | ---
**Lokalizacja docelowa** | Region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację. Konfiguracja usługi Azure SQL i zalecenia dotyczące kosztów są zależne od określonej lokalizacji.
**Docelowy typ wdrożenia** | Docelowy typ wdrożenia, dla którego chcesz przeprowadzić ocenę: <br/><br/> Wybierz pozycję **zalecane**, jeśli chcesz, aby usługa Azure Migrate mogła ocenić gotowość serwerów SQL do migracji do usługi Azure SQL i usługi Azure SQL DB oraz zalecać najlepszą, docelową opcję wdrożenia, warstwę docelową, konfigurację SQL platformy Azure i oszacowania miesięczne.<br/><br/>Wybierz pozycję **Azure SQL DB**, jeśli chcesz ocenić serwery SQL do migracji do baz danych Azure SQL, a następnie przejrzyj warstwę docelową, konfigurację bazy danych SQL Azure i szacunki miesięczne.<br/><br/>Wybierz pozycję **Azure SQL mi**, jeśli chcesz ocenić serwery SQL do migracji do baz danych Azure SQL, a następnie przejrzyj warstwę docelową, konfigurację i miesięczne szacunkowe usługi Azure SQL.
**Pojemność zarezerwowana** | Określa zarezerwowaną pojemność, dzięki czemu szacowania kosztów w ocenie są uwzględniane.<br/><br/> W przypadku wybrania opcji zarezerwowanej pojemności nie można określić "rabat (%)".
**Kryterium określania rozmiaru** | Ta właściwość służy do poprawnego rozmiaru konfiguracji usługi Azure SQL. <br/><br/> Jest ona domyślnie oparta na **wydajności** , co oznacza, że Ocena będzie zbierać metryki wydajności SQL Server wystąpień i baz danych, aby zalecać optymalne i nieodpowiednie zalecenia dotyczące warstwy/lub Azure SQL Database usługi Azure SQL.
**Historia wydajności** | Historia wydajności określa czas trwania używany podczas oceniania danych wydajności.
**Użycie percentyla** | Użycie percentylu określa wartość percentylości próbki wydajności używanej dla odpowiedniej zmiany.
**Współczynnik komfortu** | Bufor używany podczas oceny. Konta IT dotyczą problemów, takich jak sezonowe użycie, krótka historia wydajności i prawdopodobnie wzrasta w przyszłości.<br/><br/> Na przykład wystąpienie 10-rdzeniowe z wykorzystaniem 20% zwykle powoduje wystąpienie dwurdzeniowe. Ze współczynnikiem komfortu wynoszącym 2,0, wynikiem jest wystąpienie z czterema rdzeniami.
**Program oferty/licencjonowania** | [Oferta platformy Azure](https://azure.microsoft.com/support/legal/offer-details/) , w której zarejestrowano Cię. Obecnie możesz wybrać tylko opcję płatność zgodnie z rzeczywistym użyciem i płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie. Pamiętaj, że możesz skorzystać z dodatkowego rabatu, stosując zarezerwowaną pojemność i Korzyść użycia hybrydowego platformy Azure na podstawie oferty z płatność zgodnie z rzeczywistym użyciem.
**Warstwa usług** | Najbardziej odpowiednia opcja warstwy usług w celu uwzględnienia potrzeb firmy na potrzeby migracji do Azure SQL Database i/lub wystąpienia zarządzanego Azure SQL:<br/><br/>**Zalecane** , jeśli chcesz, aby Azure Migrate zalecić najlepszą dodaną warstwę usług dla serwerów. Może to być ogólne lub krytyczne dla działania firmy. <br/><br/> **Ogólnego przeznaczenia** Jeśli chcesz, aby konfiguracja usługi Azure SQL została zaprojektowana na potrzeby obciążeń zorientowanych na budżet. [Więcej informacji](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Krytyczne dla działania firmy** Jeśli chcesz, aby konfiguracja usługi Azure SQL została zaprojektowana dla obciążeń o małych opóźnieniach i ma wysoką odporność na awarie i szybkie przełączanie do trybu failover. [Więcej informacji](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Waluta** | Waluta rozliczeń dla Twojego konta.
**Rabat (%)** | Wszystkie zniżki związane z subskrypcją, które otrzymujesz w ramach oferty platformy Azure. Ustawienie domyślne to 0%.
**Korzyść użycia hybrydowego platformy Azure** | Określa, czy masz już licencję SQL Serverową. <br/><br/> Jeśli to zrobisz, a zostaną one objęte aktywnym programem Software Assurance SQL Server subskrypcje, możesz zastosować Korzyść użycia hybrydowego platformy Azure po przeniesieniu licencji na platformę Azure.

## <a name="edit-assessment-properties"></a>Edytuj właściwości oceny

Aby edytować właściwości oceny po utworzeniu oceny, wykonaj następujące czynności:

1. W projekcie Azure Migrate kliknij pozycję **serwery**.
2. W **Azure Migrate: odnajdywanie i Ocena** kliknij liczbę ocen.
3. W obszarze **Ocena** kliknij odpowiednią ocenę > **Edytuj właściwości**.
5. Dostosuj właściwości oceny zgodnie z powyższymi tabelami.
6. Kliknij przycisk **Zapisz** , aby zaktualizować ocenę.


Możesz również edytować właściwości oceny podczas tworzenia oceny.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](concepts-assessment-calculation.md) na temat obliczania ocen maszyn wirtualnych platformy Azure.
- [Dowiedz się więcej](concepts-azure-sql-assessment-calculation.md) na temat obliczania ocen usługi Azure SQL.
- [Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md) o tym, jak są obliczane oceny automatycznej synchronizacji.

