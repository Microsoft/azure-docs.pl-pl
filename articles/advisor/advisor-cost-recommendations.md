---
title: Zmniejsz koszty usługi przy użyciu Azure Advisor
description: Użyj Azure Advisor, aby zoptymalizować koszty wdrożeń platformy Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 346b790c6970abc8670661e1ec180662957af47d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503378"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Obniżenie kosztów usługi przy użyciu Azure Advisor

Azure Advisor pomaga zoptymalizować i zmniejszyć ogólne wydatki na platformę Azure, identyfikując bezczynne i niedostosowane zasoby. Możesz uzyskać zalecenia dotyczące kosztów na karcie **koszt** na pulpicie nawigacyjnym usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Zoptymalizuj wydatki na maszyny wirtualne przez zmianę rozmiaru lub zamknięcie nie w pełni wykorzystywanych wystąpień 

Chociaż niektóre scenariusze aplikacji mogą powodować niskie użycie w projekcie, często możesz zaoszczędzić pieniądze, zarządzając rozmiarem i liczbą maszyn wirtualnych. 

Zalecane akcje są wyłączane lub zmieniane, w odróżnieniu od wartości ocenianego zasobu.

Zaawansowany model oceny w usłudze Advisor uwzględnia wyłączenie maszyn wirtualnych, gdy wszystkie te instrukcje są prawdziwe: 
- P95th maksymalnej wartości użycia procesora CPU jest mniejsza niż 3%. 
- Wykorzystanie sieci jest mniejsze niż 2% w okresie siedmiu dni.
- Wykorzystanie pamięci jest mniejsze niż wartości progowe

Klasyfikator rozważa zmianę rozmiaru maszyn wirtualnych, gdy istnieje możliwość dopasowania bieżącego obciążenia do mniejszej jednostki SKU (w ramach tej samej rodziny SKU) lub mniejszej liczby wystąpień, takich jak:
- Bieżące obciążenie nie działa powyżej 80% w przypadku obciążeń, które nie są związane z użytkownikiem. 
- Obciążenie nie przechodzi powyżej 40% dla obciążeń związanych z użytkownikiem. 

Tutaj należy określić typ obciążenia, analizując charakterystykę użycia procesora CPU.

Program Advisor pokazuje szacowane oszczędności kosztów dla zalecanej akcji: zmiana rozmiaru lub wyłączenie. W przypadku zmiany rozmiaru klasyfikator udostępnia bieżące i docelowe informacje o jednostce SKU.

Jeśli chcesz bardziej agresywnie identyfikować nieużywane maszyny wirtualne, możesz dostosować regułę użycia procesora CPU dla każdej subskrypcji.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optymalizowanie wydatków pod kątem serwerów MariaDB, MySQL i PostgreSQL przez odpowiednie rozmiary 
Program Advisor analizuje użycie i sprawdza, czy zasoby serwera bazy danych MariaDB, MySQL i PostgreSQL zostały poddane niedostatecznej dostępności przez dłuższy czas w ciągu ostatnich siedmiu dni. Niewielkie wykorzystanie zasobów skutkuje niepotrzebnymi wydatkami, które można naprawić bez znaczącego wpływu na wydajność. Aby zmniejszyć koszty i efektywnie zarządzać zasobami, zalecamy zmniejszenie rozmiaru obliczeń (rdzeni wirtualnych) o połowę.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Zmniejsz koszty, eliminując niezainicjowane obwody usługi ExpressRoute

Usługa Advisor identyfikuje obwody usługi Azure ExpressRoute, które były w stanie dostawcy **nieobsługiwanym** przez więcej niż jeden miesiąc. Zaleca się usunięcie obwodu, jeśli nie planujesz zainicjować obsługi obwodu u dostawcy połączenia.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Zmniejsz koszty, usuwając lub ponownie konfigurując bezczynne bramy sieci wirtualnej

Program Advisor identyfikuje bramy sieci wirtualnej, które są bezczynne przez ponad 90 dni. Ponieważ bramy są rozliczane co godzinę, należy rozważyć ponowne skonfigurowanie lub usunięcie ich, jeśli nie zamierzasz ich używać. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane maszyn wirtualnych w celu zaoszczędzenia pieniędzy w porównaniu z płatnością zgodnie z rzeczywistym użyciem

Usługa Advisor przegląda użycie maszyny wirtualnej w ciągu ostatnich 30 dni, aby określić, czy można zaoszczędzić pieniądze, kupując rezerwację na platformie Azure. W usłudze Advisor przedstawiono regiony i rozmiary, w których możliwy jest poziom oszczędności, a szacowane oszczędności wynikające z zakupu rezerwacji. Dzięki rezerwacji na platformie Azure można wstępnie zakupić podstawowe koszty dla maszyn wirtualnych. Rabaty są automatycznie stosowane do nowych lub istniejących maszyn wirtualnych o tym samym rozmiarze i regionie co rezerwacje. [Dowiedz się więcej o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Program Advisor powiadamia również o wystąpieniach zarezerwowanych, które wygaśnie w ciągu następnych 30 dni. Zalecane jest zakupienie nowych wystąpień zarezerwowanych, aby uniknąć cen zgodnie z rzeczywistym użyciem.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane dla kilku typów zasobów, aby zaoszczędzić według kosztów płatność zgodnie z rzeczywistym użyciem

Klasyfikator analizuje wzorce użycia w ciągu ostatnich 30 dni dla następujących zasobów i zaleca zakupy zarezerwowanych pojemności, które optymalizują koszty.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB zarezerwowana pojemność
Klasyfikator analizuje Azure Cosmos DB wzorców użycia w ciągu ostatnich 30 dni i zaleca zakupy zarezerwowanych zdolności produkcyjnych w celu optymalizacji kosztów. Korzystając z zarezerwowanej pojemności, można wstępnie kupić Azure Cosmos DB godzinowe użycie i zaoszczędzić koszty związane z płatność zgodnie z rzeczywistym użyciem. Zarezerwowana pojemność to korzyść rozliczenia i automatycznie stosowana do nowych i istniejących wdrożeń. Doradca oblicza oszczędności dla indywidualnych subskrypcji przy użyciu cen rezerwacji 3-letnich i ekstrapolacji wzorców użycia zaobserwowanych w ciągu ostatnich 30 dni. Zalecenia dotyczące zakresu udostępnionego są dostępne dla zarezerwowanych zakupów pojemności i mogą zwiększyć oszczędności.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>Zarezerwowana pojemność SQL Database i wystąpienia zarządzanego SQL
Klasyfikator analizuje wzorce użycia wystąpienia zarządzanego SQL Database i SQL w ciągu ostatnich 30 dni. Następnie zaleca się zarezerwowanych zakupów pojemności, które optymalizują koszty. Korzystając z zarezerwowanej pojemności, można wstępnie kupić użycie w bazie danych SQL DB i zaoszczędzić na kosztach obliczeń SQL. Licencja SQL jest naliczana osobno i nie podlega rabatowi za rezerwację. Zarezerwowana pojemność to korzyść rozliczenia i automatycznie stosowana do nowych i istniejących wdrożeń. Doradca oblicza oszczędności dla indywidualnych subskrypcji przy użyciu cen rezerwacji 3-letnich i ekstrapolacji wzorców użycia zaobserwowanych w ciągu ostatnich 30 dni. Zalecenia dotyczące zakresu udostępnionego są dostępne dla zarezerwowanych zakupów pojemności i mogą zwiększyć oszczędności. Aby uzyskać szczegółowe informacje, zobacz [Azure SQL Database & zarezerwowana pojemność wystąpienia zarządzanego SQL](../azure-sql/database/reserved-capacity-overview.md).

### <a name="app-service-stamp-fee-reserved-capacity"></a>Zarezerwowana pojemność opłaty za sygnaturę App Service
Klasyfikator analizuje wzorzec użycia opłaty za sygnaturę dla Azure App Service środowiska izolowanego w ciągu ostatnich 30 dni i zaleca zakupy zarezerwowanych pojemności, które optymalizują koszty. Korzystając z zarezerwowanej pojemności, można wstępnie zakupić opłaty godzinowe za użycie sygnatury środowiska izolowanego i zaoszczędzić na kosztach płatnych zgodnie z rzeczywistym użyciem. Należy zauważyć, że zarezerwowana pojemność dotyczy tylko opłaty za sygnaturę, a nie do App Service wystąpień. Zarezerwowana pojemność to korzyść rozliczenia i automatycznie stosowana do nowych i istniejących wdrożeń. Doradca oblicza oszczędności dla indywidualnych subskrypcji, korzystając z cen rezerwacji 3-letnich w oparciu o wzorce użycia w ciągu ostatnich 30 dni.

### <a name="blob-storage-reserved-capacity"></a>Zarezerwowana pojemność magazynu obiektów BLOB
Usługa Advisor analizuje magazyn obiektów blob platformy Azure i Azure Data Lake użycie magazynu w ciągu ostatnich 30 dni. Następnie oblicza zarezerwowane zakupy pojemności, które optymalizują koszty. Dzięki zarezerwowanej pojemności można wstępnie zakupić użycie godzinowe i zaoszczędzić na bieżących kosztach na żądanie. Zarezerwowana pojemność magazynu obiektów BLOB ma zastosowanie tylko do danych przechowywanych w usłudze Azure Blob ogólnego przeznaczenia w wersji 2 i Azure Data Lake Storage Gen2. Zarezerwowana pojemność to korzyść rozliczenia i automatycznie stosowana do nowych i istniejących wdrożeń. Doradca oblicza oszczędności dla indywidualnych subskrypcji przy użyciu cen rezerwacji 3-letnich i wzorców użycia zaobserwowanych w ciągu ostatnich 30 dni. Zalecenia dotyczące zakresu udostępnionego są dostępne dla zarezerwowanych zakupów pojemności i mogą zwiększyć oszczędności.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Zarezerwowana pojemność MariaDB, MySQL i PostgreSQL
Program Advisor analizuje wzorce użycia Azure Database for MariaDB, Azure Database for MySQL i Azure Database for PostgreSQL w ciągu ostatnich 30 dni. Następnie zaleca się zarezerwowanych zakupów pojemności, które optymalizują koszty. Korzystając z zarezerwowanej pojemności, można wstępnie kupić MariaDB, MySQL i PostgreSQL użycie godzinowe i zaoszczędzić na bieżących kosztach. Zarezerwowana pojemność to korzyść rozliczenia i automatycznie stosowana do nowych i istniejących wdrożeń. Doradca oblicza oszczędności dla indywidualnych subskrypcji przy użyciu cen rezerwacji 3-letnich i wzorców użycia zaobserwowanych w ciągu ostatnich 30 dni. Zalecenia dotyczące zakresu udostępnionego są dostępne dla zarezerwowanych zakupów pojemności i mogą zwiększyć oszczędności.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Zarezerwowana pojemność usługi Azure Synapse Analytics
Usługa Advisor analizuje wzorce użycia usługi Azure Synapse Analytics w ciągu ostatnich 30 dni i zaleca zakupy zarezerwowanych pojemności, które optymalizują koszty. Korzystając z zarezerwowanej pojemności, możesz wstępnie kupić Synapseą analizę godzinową i zaoszczędzić na kosztach na żądanie. Zarezerwowana pojemność to korzyść rozliczenia i automatycznie stosowana do nowych i istniejących wdrożeń. Doradca oblicza oszczędności dla indywidualnych subskrypcji przy użyciu cen rezerwacji 3-letnich i wzorców użycia zaobserwowanych w ciągu ostatnich 30 dni. Zalecenia dotyczące zakresu udostępnionego są dostępne dla zarezerwowanych zakupów pojemności i mogą zwiększyć oszczędności.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Usuń nieskojarzone publiczne adresy IP, aby zaoszczędzić pieniądze

Usługa Advisor identyfikuje publiczne adresy IP, które nie są skojarzone z zasobami platformy Azure, takimi jak moduły równoważenia obciążenia i maszyny wirtualne. Opłata nominalna jest skojarzona z tymi publicznymi adresami IP. Jeśli nie planujesz korzystania z nich, możesz zaoszczędzić pieniądze, usuwając je.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Usuń potoki usługi Azure Data Factory, które kończą się niepowodzeniem

Program Advisor wykrywa potoki Azure Data Factory, które wielokrotnie kończą się niepowodzeniem. Zalecamy Rozwiązywanie problemów lub usuwanie potoków, jeśli nie są one potrzebne. Opłaty są naliczane za te potoki nawet wtedy, gdy nie są one obsługiwane w przypadku awarii.

## <a name="use-standard-snapshots-for-managed-disks"></a>Używanie standardowych migawek dla dysków zarządzanych
Aby zaoszczędzić 60% kosztów, zalecamy przechowywanie migawek w magazynie w warstwie Standardowa, niezależnie od typu magazynu dysku nadrzędnego. Ta opcja jest opcją domyślną dla migawek dysków zarządzanych. Usługa Advisor identyfikuje migawki przechowywane w usłudze Premium Storage i zaleca migrowanie ich z magazynu w warstwie Premium do wersji Standard. [Dowiedz się więcej o cenach dysku zarządzanego.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Korzystanie z zarządzania cyklem życia
Korzystając z analizy o liczbie obiektów usługi Azure Blob Storage, łącznym rozmiarze i transakcjach, usługa Advisor wykrywa, czy należy włączyć zarządzanie cyklem życia w celu uzyskania warstwy danych na co najmniej jednym koncie magazynu. Zostanie wyświetlony komunikat z prośbą o utworzenie reguł zarządzania cyklem życia w celu automatycznej warstwy danych do magazynu chłodnego lub archiwum w celu zoptymalizowania kosztów magazynowania podczas zachowywania danych w usłudze Azure Blob Storage w celu zapewnienia zgodności aplikacji.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Zalecenie dotyczące tworzenia efemerycznego dysku systemu operacyjnego
Dysk tymczasowej wersji [systemu operacyjnego](../virtual-machines/ephemeral-os-disks.md) pozwala: 
- Oszczędzaj koszty magazynowania dysków systemu operacyjnego. 
- Uzyskaj mniejsze opóźnienie odczytu/zapisu na dyskach systemu operacyjnego. 
- Uzyskaj szybsze operacje na oddziałach maszyn wirtualnych przez zresetowanie systemu operacyjnego (i dysku tymczasowego) do jego oryginalnego stanu.

Zalecane jest używanie tymczasowych dysków systemu operacyjnego do krótkoterminowych maszyn wirtualnych IaaS lub maszyn wirtualnych o bezstanowych obciążeniach. Klasyfikator zawiera zalecenia dotyczące zasobów, które mogą korzystać z systemu z systemem tymczasowych systemów operacyjnych.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Ogranicz pamięć podręczną tabeli Eksplorator danych platformy Azure — okres (zasady) dla optymalizacji kosztu klastra (wersja zapoznawcza)
Usługa Advisor identyfikuje zasoby, których obniżenie zasad dotyczących pamięci podręcznej tabeli spowoduje zwolnienie węzłów klastra Eksplorator danych platformy Azure z niskim użyciem procesora CPU, pamięcią i wysoką konfiguracją rozmiaru pamięci podręcznej.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących kosztów w Azure Advisor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj i wybierz opcję [**Advisor**](https://aka.ms/azureadvisordashboard) z dowolnej strony.

1. Na pulpicie nawigacyjnym usługi **Advisor** wybierz kartę **koszt** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Ocena klasyfikatora](azure-advisor-score.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
