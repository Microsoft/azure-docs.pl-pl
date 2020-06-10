---
title: Zmniejsz koszty usługi przy użyciu Azure Advisor
description: Użyj Azure Advisor, aby zoptymalizować koszty wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 99dfec669d8981a557b2e8a8d8979292af74616f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658563"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Zmniejsz koszty usługi przy użyciu Azure Advisor

Usługa Advisor pomaga zoptymalizować i obniżyć ogólne wydatki na platformę Azure, identyfikując bezczynne i niedostateczne zasoby.Możesz uzyskać zalecenia dotyczące kosztów na karcie **koszt** na pulpicie nawigacyjnym usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Zoptymalizuj wydatki na maszyny wirtualne przez zmianę rozmiaru lub zamknięcie nie w pełni wykorzystywanych wystąpień 

Chociaż niektóre scenariusze aplikacji mogą powodować niskie użycie w projekcie, często możesz zaoszczędzić pieniądze, zarządzając rozmiarem i liczbą maszyn wirtualnych. Zaawansowane modele oceny usługi Advisor uwzględniają maszyny wirtualne do zamknięcia, gdy P95th maksymalnej maksymalnej wartości użycia procesora CPU wynosi mniej niż 3%, a wykorzystanie sieci jest mniejsze niż 2% w okresie 7 dni. Maszyny wirtualne są uważane za odpowiedni rozmiar, gdy można dopasować bieżące obciążenie do mniejszej jednostki SKU (w ramach tej samej rodziny SKU) lub mniejszej liczby wystąpień w taki sposób, aby bieżące obciążenie nie przeszedł do 80% w przypadku obciążeń niezwiązanych z użytkownikiem, a nie powyżej 40% w przypadku obciążenia związanego z użytkownikiem. W tym miejscu typ obciążenia jest określany przez analizowanie charakterystyki użycia procesora CPU.

Zalecane akcje są zamykane lub zmieniane, w odniesieniu do zasobów zalecanych dla. Program Advisor pokazuje szacowane oszczędności kosztów dla zalecanych akcji — zmiana rozmiaru lub zamknięcie. Ponadto w przypadku akcji zalecanej do zmiany rozmiaru klasyfikator udostępnia bieżące i docelowe informacje o jednostce SKU. 

Jeśli chcesz bardziej agresywnie identyfikować nieużywane maszyny wirtualne, możesz dostosować regułę użycia procesora CPU dla każdej subskrypcji.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optymalizowanie wydatków pod kątem serwerów MariaDB, MySQL i PostgreSQL według odpowiednich rozmiarów 
Program Advisor analizuje użycie i zaleca, jeśli zasoby serwera bazy danych MariaDB/MySQL/PostgreSQL zostały niewykorzystane przez dłuższy czas w ciągu ostatnich 7 dni. Niewielkie wykorzystanie zasobów skutkuje niepotrzebnymi wydatkami. Ten problem można rozwiązać bez znaczącego wpływu na wydajność. Aby zmniejszyć koszty i efektywnie zarządzać zasobami, zalecamy zmniejszenie liczby rdzeni wirtualnych o połowę.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Zmniejsz koszty, eliminując niezainicjowane obwody usługi ExpressRoute

Program Advisor identyfikuje obwody usługi ExpressRoute, które były w stanie dostawcy *nieobsługiwanym przez więcej* niż jeden miesiąc, i zaleca usunięcie obwodu, jeśli nie planujesz zainicjowania obsługi obwodu u usługodawcy.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Zmniejsz koszty, usuwając lub ponownie konfigurując bezczynne bramy sieci wirtualnej

Doradca identyfikuje bramy sieci wirtualnej, które są bezczynne przez ponad 90 dni. Ponieważ bramy są rozliczane co godzinę, należy rozważyć ponowne skonfigurowanie lub usunięcie ich, jeśli nie zamierzasz ich używać. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane maszyn wirtualnych w celu zaoszczędzenia pieniędzy w porównaniu z płatnością zgodnie z rzeczywistym użyciem

Usługa Advisor sprawdzi użycie maszyny wirtualnej w ciągu ostatnich 30 dni i określi, czy możesz zaoszczędzić pieniądze, kupując rezerwację na platformie Azure. W usłudze Advisor zostaną wyświetlone regiony i rozmiary, w których można było korzystać z najbardziej oszczędności i będą widoczne szacowane oszczędności wynikające z kupowania rezerwacji. Dzięki rezerwacji na platformie Azure można wstępnie zakupić podstawowe koszty dla maszyn wirtualnych. Rabaty będą automatycznie stosowane do nowych lub istniejących maszyn wirtualnych o tym samym rozmiarze i regionie co rezerwacje. [Dowiedz się więcej o Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Program Advisor powiadamia również użytkownika o wystąpieniach zarezerwowanych, które wygaśnie w ciągu następnych 30 dni. Zalecamy zakupienie nowych wystąpień zarezerwowanych, aby uniknąć płacenia cen płatności zgodnie z rzeczywistym użyciem.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane dla kilku typów zasobów, aby zaoszczędzić według kosztów płatność zgodnie z rzeczywistym użyciem

Analizujemy wzorzec użycia dla poniższej listy zasobów, w ciągu ostatnich 30 dni i zalecamy zakupienie zarezerwowanej pojemności, która maksymalizuje oszczędności. 
### <a name="cosmos-db-reserved-capacity"></a>Pojemność zarezerwowana usługi Cosmos DB
Na podstawie analiz wzorca użycia usługi Cosmos DB w ciągu ostatnich 30 dni zalecamy zakup pojemności zarezerwowanej, która pozwala obniżyć koszty. Pojemność zarezerwowana pozwala z wyprzedzeniem zakupić użycie godzinowe usługi Cosmos DB i uzyskać większe oszczędności niż w przypadku korzystania z modelu płatności zgodnie z rzeczywistym użyciem. Rabat jest udzielany przy rozliczeniu, a pojemność zarezerwowana jest automatycznie stosowana w nowych lub istniejących wdrożeniach. Oszczędności są szacowane dla indywidualnych subskrypcji na podstawie cennika trzyletniej rezerwacji oraz ekstrapolacji wzorca użycia z ostatnich 30 dni. Zakup rezerwacji obejmuje zalecenia dotyczące współdzielonego zakresu, które umożliwiają dodatkowe zwiększenie oszczędności.

### <a name="sql-paas-reserved-capacity"></a>Zarezerwowana pojemność SQL PaaS
Na podstawie analiz elastycznych pul SQL PaaS oraz wzorca użycia wystąpienia zarządzanego w ciągu ostatnich 30 dni zalecamy zakup pojemności zarezerwowanej, która pozwala zmaksymalizować oszczędności. Pojemność zarezerwowana pozwala z wyprzedzeniem zakupić użycie godzinowe bazy danych SQL i zmniejszyć koszty użycia zasobów obliczeniowych SQL. Licencja SQL jest rozliczana oddzielnie, a rezerwacja nie powoduje zastosowania rabatu. Rabat jest udzielany przy rozliczeniu, a pojemność zarezerwowana jest automatycznie stosowana w nowych lub istniejących wdrożeniach. Oszczędności są szacowane dla indywidualnych subskrypcji na podstawie cennika trzyletniej rezerwacji oraz ekstrapolacji wzorca użycia z ostatnich 30 dni. Zakup rezerwacji obejmuje zalecenia dotyczące współdzielonego zakresu, które umożliwiają dodatkowe zwiększenie oszczędności.

### <a name="app-service-stamp-fee-reserved-capacity"></a>Zarezerwowana pojemność opłaty za sygnaturę usługi App Service
Analizujemy wzorzec użycia opłat za sygnatury środowiska App Service izolowany w ciągu ostatnich 30 dni i zalecamy zakupienie zarezerwowanej pojemności, która maksymalizuje oszczędności. Pojemność zarezerwowana pozwala z wyprzedzeniem zakupić użycie godzinowe opłaty jednostkowej izolowanego środowiska i uzyskać większe oszczędności niż w przypadku korzystania z modelu płatności zgodnie z rzeczywistym użyciem. Należy pamiętać, że pojemność zarezerwowana dotyczy tylko opłaty jednostkowej, a nie wystąpień usługi App Service. Rabat jest udzielany przy rozliczeniu, a pojemność zarezerwowana jest automatycznie stosowana w nowych lub istniejących wdrożeniach. Oszczędności są szacowane dla indywidualnych subskrypcji na podstawie cennika trzyletniej rezerwacji i wzorca użycia z ostatnich 30 dni.

### <a name="blob-storage-reserved-capacity"></a>Zarezerwowana pojemność magazynu obiektów BLOB
Na podstawie analizy użycia usług Azure Blob i Datalake Storage z ostatnich 30 dni dokonaliśmy obliczeń dotyczących zakupu pojemności zarezerwowanej, co doprowadziłoby do znacznego zwiększenia oszczędności. Dzięki pojemności zarezerwowanej można z wyprzedzeniem zakupić użycie godzinowe i zmniejszyć bieżące koszty użycia zasobów na żądanie. Pojemność zarezerwowana usługi Blob Storage dotyczy tylko danych przechowywanych w usługach Azure Blob (GPv2) i Azure Data Lake Storage (Gen 2). Rabat jest udzielany przy rozliczeniu, a pojemność zarezerwowana jest automatycznie stosowana w nowych lub istniejących wdrożeniach. Oszczędności są szacowane dla indywidualnych subskrypcji na podstawie cennika trzyletniej rezerwacji oraz wzorca użycia zaobserwowanego w czasie ostatnich 30 dni. Zakup rezerwacji obejmuje zalecenia dotyczące współdzielonego zakresu, które umożliwiają dodatkowe zwiększenie oszczędności.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Zarezerwowana pojemność MariaDB, MySQL i PostgreSQL
Analizujemy wzorzec użycia Azure Database for MariaDB, MySQL i PostgreSQL w ciągu ostatnich 30 dni i zalecamy zakup zarezerwowanej pojemności, która maksymalizuje oszczędności. Korzystając z zarezerwowanej pojemności, możesz wstępnie kupić MariaDB, MySQL i PostgreSQL użycie godzinowe i zaoszczędzić na kosztach. Rabat jest udzielany przy rozliczeniu, a pojemność zarezerwowana jest automatycznie stosowana w nowych lub istniejących wdrożeniach. Oszczędności są szacowane dla indywidualnych subskrypcji na podstawie cennika trzyletniej rezerwacji oraz wzorca użycia z ostatnich 30 dni. Zakup rezerwacji obejmuje zalecenia dotyczące współdzielonego zakresu, które umożliwiają dodatkowe zwiększenie oszczędności.

### <a name="synapse-analytics-formerly-sql-dw-reserved-capacity"></a>Zarezerwowana pojemność Synapse Analytics (wcześniej SQL DW)
Analizujemy wzorzec użycia usługi Azure Synapse Analytics w ciągu ostatnich 30 dni i zalecamy zakup zarezerwowanej pojemności, która maksymalizuje oszczędności. Dzięki pojemności zarezerwowanej można z wyprzedzeniem zakupić użycie godzinowe usługi Synapse Analytics i zmniejszyć koszty użycia zasobów na żądanie. Rabat jest udzielany przy rozliczeniu, a pojemność zarezerwowana jest automatycznie stosowana w nowych lub istniejących wdrożeniach. Oszczędności są szacowane dla indywidualnych subskrypcji na podstawie cennika trzyletniej rezerwacji oraz wzorca użycia zaobserwowanego w czasie ostatnich 30 dni. Zakup rezerwacji obejmuje zalecenia dotyczące współdzielonego zakresu, które umożliwiają dodatkowe zwiększenie oszczędności.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Usuń nieskojarzone publiczne adresy IP, aby zaoszczędzić pieniądze

Usługa Advisor identyfikuje publiczne adresy IP, które nie są obecnie skojarzone z zasobami platformy Azure, takimi jak moduły równoważenia obciążenia lub maszyny wirtualne. Te publiczne adresy IP są naliczane przy użyciu stawki nominalnej. Jeśli nie planujesz ich używania, usunięcie ich może spowodować zmniejszenie kosztów.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Usuń potoki usługi Azure Data Factory, które kończą się niepowodzeniem

Azure Advisor wykryje Azure Data Factory potoki, które wielokrotnie kończą się niepowodzeniem i zalecamy rozwiązanie problemów lub usunięcie potoków zakończonych niepowodzeniem, jeśli nie są już potrzebne. Za te potoki będą naliczane opłaty nawet wtedy, gdy nie są one obsługiwane w przypadku awarii. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Użyj standardowych migawek dla Managed Disks
Aby obniżyć koszt o 60%, zalecamy przechowywanie migawek w usłudze Standard Storage niezależnie od typu magazynu na dysku nadrzędnym. Ta opcja jest opcją domyślną dla migawek Managed Disks. Azure Advisor zidentyfikuje migawki, które są przechowywane Premium Storage i zaleca Migrowanie migawki z magazynu w warstwie Premium do warstwy Standardowa. [Dowiedz się więcej o cenach dysku zarządzanego](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Korzystanie z zarządzania cyklem życia
Azure Advisor będzie używać analizy dotyczącej liczby obiektów usługi Azure Blob Storage, łącznego rozmiaru i transakcji w celu wykrycia, czy co najmniej jedno konto magazynu najlepiej nadaje się do włączenia zarządzania cyklem życia do danych warstwy. Spowoduje to wyświetlenie monitu o utworzenie reguł zarządzania cyklem życia w celu automatycznego przechowania danych w celu zoptymalizowania lub archiwizacji, aby zoptymalizować koszty magazynowania podczas zachowywania danych w usłudze Azure Blob Storage w celu zapewnienia zgodności aplikacji.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Zalecenie dotyczące tworzenia efemerycznego dysku systemu operacyjnego
Dzięki tymczasowemu [dyskowi systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machines/windows/ephemeral-os-disks)klienci uzyskują następujące korzyści: oszczędności związane z magazynowaniem dysku systemu operacyjnego. Mniejsze opóźnienie odczytu/zapisu na dysku systemu operacyjnego. Szybsza operacja odtwarzania maszyny wirtualnej obrazu przez resetowanie systemu operacyjnego (i dysku tymczasowego) do jego pierwotnego stanu. Bardziej preferowane jest używanie tymczasowych dysków systemu operacyjnego na krótkoterminowych maszynach wirtualnych IaaS lub maszynach wirtualnych o bezstanowych obciążeniach. Doradca ma rekomendacje dotyczące zasobów, które mogą posłużyć do korzystania z tymczasowych dysków systemu operacyjnego. 


## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp do zaleceń dotyczących kosztów w Azure Advisor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wyszukaj i wybierz opcję [**Advisor**](https://aka.ms/azureadvisordashboard) z dowolnej strony.

1. Na pulpicie nawigacyjnym usługi **Advisor** wybierz kartę **koszt** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące wysokiej dostępności usługi Advisor](advisor-high-availability-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)

