---
title: Często zadawane pytania dotyczące przepływności z obsługą automatycznego skalowania w Azure Cosmos DB
description: Często zadawane pytania dotyczące przepływności z obsługą automatycznego skalowania dla Azure Cosmos DB baz danych i kontenerów
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 31b96f03a8519b068eaa816443be0a0f374a4a8c
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247033"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące przepływności dotyczącej automatycznego skalowania w Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W przypadku przepływności z obsługą automatycznego skalowania Azure Cosmos DB automatycznie będzie zarządzać i skalować RU/s bazy danych lub kontenera na podstawie użycia. Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące skalowania automatycznego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Jaka jest różnica między elementami "autopilotaż" i "Automatyczne skalowanie" w Azure Cosmos DB?
"Automatyczne skalowanie" lub "przepływność aprowizacji automatycznego skalowania" to zaktualizowana nazwa funkcji, znana wcześniej jako "autopilotaż". W przypadku bieżącej wersji automatycznego skalowania Dodaliśmy nowe funkcje, w tym możliwość ustawiania niestandardowych maksymalnych jednostek RU/s i programistycznych. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Co się dzieje z bazami danych lub kontenerami utworzonymi w poprzednim modelu warstwy autopilotażu?
Zasoby, które zostały utworzone przy użyciu poprzedniego modelu warstwy, są automatycznie obsługiwane przy użyciu nowego niestandardowego maksymalnego modelu RU/s automatycznego skalowania. Górna granica warstwy jest nową maksymalną wartością RU/s, która skutkuje tym samym zakresem skalowania. 

Na przykład jeśli wcześniej wybrano warstwę, która jest skalowana w zakresie od 400 do 4000 RU/s, baza danych lub kontener będzie teraz wyświetlana jako maksymalna wartość RU/s z 4000 RU/s, która skaluje się między 400 do 4000 RU/s. W tym miejscu możesz zmienić wartość maksymalną RU/s na niestandardową, aby dopasować ją do obciążenia. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Jak szybko skalowanie skalowania w górę na podstawie skoków ruchu?
Dzięki funkcji automatycznego skalowania system skaluje przepływność (RU/s) `T` w górę lub w dół w ramach `0.1 * Tmax` `Tmax` zakresu i, w zależności od ruchu przychodzącego. Ponieważ skalowanie jest automatyczne i natychmiastowe, w dowolnym momencie można wykorzystać do zainicjowania obsługi `Tmax` bez opóźnień. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Jak mogę określić, do czego system jest obecnie skalowany?
[Metryki Azure monitor](how-to-choose-offer.md#measure-and-monitor-your-usage) służą do monitorowania zarówno maksymalnego skalowania automatycznego, jak i bieżącej przepływności (ru/s), do którego jest skalowane system. 

### <a name="what-is-the-pricing-for-autoscale"></a>Jakie są ceny automatycznego skalowania?
Każda godzina zostanie naliczona za najwyższą przepływność `T` , którą system przeskaluje do godziny. Jeśli zasób nie ma żadnych żądań w ciągu godziny lub nie przekroczy skalowania `0.1 * Tmax` , zostanie naliczona stawka za minimum `0.1 * Tmax` . Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Jak automatyczne skalowanie jest widoczne na rachunku?
W przypadku kont w jednym regionie zapisu częstotliwość skalowania automatycznego na 100 RU/s jest 1,5 x częstotliwością (ręcznie) zainicjowanej przepływności. Na rachunku zostanie wyświetlony istniejący standardowy licznik przepływności. Ilość tego licznika zostanie pomnożona przez 1,5. Na przykład jeśli najwyższego poziomu RU/s system przeskalowany do godziny wynosił 6000 RU/s, opłaty są naliczane 60 * 1,5 = 90 jednostki gazomierza dla tej godziny.

W przypadku kont z wieloma regionami zapisu częstotliwość skalowania automatycznego na 100 RU/s jest taka sama jak stawka standardowa (ręczna), dla której zainicjowano obsługę wielu regionów zapisu. Na rachunku zobaczysz istniejący licznik wielu regionów zapisu. Ponieważ stawki są takie same, w przypadku korzystania z funkcji automatycznego skalowania będzie wyświetlana taka sama ilość jak w przypadku standardowej przepływności.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Czy automatyczne skalowanie jest wykonywane z użyciem zarezerwowanej pojemności?
Tak. W przypadku zakupu zarezerwowanej pojemności dla kont z pojedynczymi regionami zapisu rabat rezerwacji dla zasobów automatycznego skalowania jest stosowany do użycia licznika przy współczynniku wynoszącym 1,5 * [stosunek określonego regionu](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region). 

Zarezerwowana pojemność regionu wielokrotnego zapisu działa w taki sam sposób, jak w przypadku przepływności z obsługą skalowania automatycznego i standardowego (ręcznie). Zobacz [Azure Cosmos DB zarezerwowana pojemność](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>Czy automatyczne skalowanie jest wykonywane w ramach warstwy Bezpłatna?
Tak. W warstwie Bezpłatna można używać funkcji automatycznego skalowania w kontenerze. Obsługa udostępnionych baz danych przepływności automatycznego skalowania przy użyciu niestandardowych maksymalnych wartości RU/s nie jest jeszcze dostępna. Zobacz, jak [rozliczanie w warstwie Bezpłatna działa z automatycznym skalowaniem](understand-your-bill.md#azure-free-tier).

### <a name="is-autoscale-supported-for-all-apis"></a>Czy automatyczne skalowanie jest obsługiwane dla wszystkich interfejsów API?
Tak, automatyczne skalowanie jest obsługiwane dla wszystkich interfejsów API: Core (SQL), Gremlin, Table, Cassandra i API for MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>Czy automatyczne skalowanie jest obsługiwane dla wieloregionowych kont zapisu?
Tak. Maksymalna liczba RU/s jest dostępna w każdym regionie, który jest dodawany do konta Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Jak mogę włączyć skalowanie automatyczne dla nowych baz danych lub kontenerów?
Zapoznaj się z tym artykułem [, jak włączyć automatyczne skalowanie](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Czy mogę włączyć funkcję automatycznego skalowania dla istniejącej bazy danych lub kontenera?
Tak. W razie konieczności można również przełączać się między funkcją automatycznego skalowania i standardową (ręczną). Obecnie w przypadku wszystkich interfejsów API do wykonywania tych operacji można używać tylko [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) .

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Jak działa Migracja między funkcją automatycznego skalowania i standardową (ręczną)?
Ze pojęciową zmianą typ przepływności jest procesem dwuetapowym. Najpierw należy wysłać żądanie zmiany ustawień przepływności w celu użycia funkcji automatycznego skalowania lub ręcznej aprowizacji. W obu przypadkach system automatycznie określi i ustawi początkową wartość RU na podstawie bieżących ustawień przepływności i magazynu. W tym kroku nie zostanie zaakceptowana wartość RU/s dostarczona przez użytkownika. Następnie po zakończeniu aktualizacji można [zmienić ru/s](#can-i-change-the-max-rus-on-the-database-or-container) , aby dopasować obciążenie. 

**Migracja z warstwy Standardowa (ręczna) do automatycznego skalowania**

W przypadku kontenera Użyj następującej formuły, aby oszacować początkową maksymalną wartość automatycznego skalowania RU/s: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` , zaokrągloną do najbliższych 1000 ru/s. Rzeczywiste początkowe skalowanie maksymalne RU/s może się różnić w zależności od konfiguracji konta.

Przykład #1: Załóżmy, że masz kontener z obsługą administracyjną 10 000 RU/s i 25 GB miejsca w magazynie. Po włączeniu automatycznego skalowania wartość początkowa maksymalna wartość automatycznego skalowania jest taka: 10 000 RU/s, która będzie skalowana między 1000 10 000 RU/s. 

Przykład #2: Załóżmy, że masz kontener z obsługą administracyjną 50 000 RU/s i 2500 GB miejsca w magazynie. Po włączeniu automatycznego skalowania wartość początkowa maksymalna wartość automatycznego skalowania jest taka: 250 000 RU/s, która będzie skalowana między 25 000 250 000 RU/s. 

**Migracja z automatycznego skalowania do warstwy Standardowa (ręczna) obsługiwana przepustowość**

Początkowa ręczna administracyjna przepływność będzie równa bieżącemu automatycznemu skalowaniu RU/s. 

Przykład: Załóżmy, że istnieje automatyczne skalowanie bazy danych lub kontenera z maksymalną liczbą jednostek RU/s z 20 000 RU/s (skaluje się między 2000 – 20 000 RU/s). Gdy aktualizujesz się do korzystania z ręcznie zainicjowanej przepływności, początkowa przepływność będzie 20 000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Czy do zarządzania bazami danych lub kontenerami przy użyciu automatycznego skalowania jest obsługiwany interfejs wiersza polecenia platformy Azure lub środowisko PowerShell?
Obecnie można tworzyć i zarządzać zasobami tylko przy użyciu funkcji automatycznego skalowania na podstawie Azure Portal, zestawu SDK platformy .NET v3, zestawu SDK Java v4 i Azure Resource Manager. Obsługa w interfejsie wiersza polecenia platformy Azure, programie PowerShell i innych zestawach SDK nie jest jeszcze dostępna.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Czy funkcja automatycznego skalowania jest obsługiwana w przypadku udostępnionych baz danych przepływności?
Tak, Skalowanie automatyczne jest obsługiwane w przypadku udostępnionych baz danych przepływności. Aby włączyć tę funkcję, wybierz opcję automatycznego skalowania i **przepływność udostępniania** podczas tworzenia bazy danych. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Jaka jest liczba dozwolonych kontenerów na udostępnioną bazę danych przepływności, gdy funkcja automatycznego skalowania jest włączona?
Azure Cosmos DB wymusza maksymalnie 25 kontenerów w udostępnionej bazie danych przepływności, która ma zastosowanie do baz danych ze skalowaniem automatycznym lub standardowym (ręcznym) przepływności. 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Jaki jest wpływ automatycznego skalowania na poziom spójności bazy danych?
Nie ma to wpływu na poziom spójności w bazie danych.
Zobacz artykuł [poziomy spójności](consistency-levels.md) , aby uzyskać więcej informacji na temat dostępnych poziomów spójności.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaki jest limit magazynowania skojarzony z każdą maksymalną opcją RU/s?  
Limit magazynowania w GB dla każdej maksymalnej wartości RU/s to: Max RU/s bazy danych lub kontenera/100. Na przykład jeśli maksymalna wartość RU/s wynosi 20 000 RU/s, zasób może obsługiwać 200 GB miejsca w magazynie. Zapoznaj się z artykułem [limity skalowania automatycznego](provision-throughput-autoscale.md#autoscale-limits) dla dostępnych maksymalnych opcji ru/s i magazynu. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co się stanie w przypadku przekroczenia limitu magazynu związanego z moją maksymalną przepływność?
W przypadku przekroczenia limitu magazynowania związanego z maksymalną przepływność bazy danych lub kontenera Azure Cosmos DB automatycznie zwiększy maksymalną przepływność do następnych najwyższych wartości RU/s, które będą obsługiwać ten poziom magazynu.

Jeśli na przykład zaczniesz od maksimum RU/s z 50 000 RU/s (skale się między 5000 50 000 RU/s), możesz przechowywać do 500 GB danych. Jeśli przekroczysz 500 GB (np. miejsce do magazynowania będzie wynosić 600 GB), nową maksymalną wartością RU/s będzie 60 000 RU/s (skalowanie w zakresie 6000 – 60 000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Czy mogę zmienić maksymalną wartość RU/s dla bazy danych lub kontenera? 
Tak. Zapoznaj się z tym [artykułem](how-to-provision-autoscale-throughput.md) dotyczącym zmiany maksymalnego ru/s. W przypadku zmiany maksymalnego RU/s, w zależności od żądanej wartości może to być operacja asynchroniczna, która może zająć trochę czasu (może być do 4-6 godzin, w zależności od wybranego obiektu RU/s)

#### <a name="increasing-the-max-rus"></a>Zwiększenie maksymalnego RU/s
W przypadku wysłania żądania zwiększenia maksymalnej wartości RU/s `Tmax` , w zależności od wybranej maksymalnej wartości ru/s, usługa postanowi więcej zasobów do obsługi wyższych maksymalnych jednostek ru/s. W takim przypadku istniejące obciążenie i operacje nie będą miały żadnego oddziaływania. System będzie kontynuował skalowanie bazy danych lub kontenera między poprzednimi `0.1*Tmax` do `Tmax` aż do momentu, aż nowy zakres skalowania `0.1*Tmax_new` do `Tmax_new` będzie gotowy.

#### <a name="lowering-the-max-rus"></a>Obniżenie maksymalnej wartości RU/s
W przypadku obniżenia maksymalnej wartości RU/s wartość minimalna, którą można ustawić na: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` , zaokrąglana do najbliższej 1000 ru/s. 

Przykład #1: Załóżmy, że masz kontener automatycznego skalowania z maksimum RU/s z 20 000 RU/s (skaluje się między 2000 20 000 RU/s) i 50 GB magazynu. Najniższa, minimalna wartość można ustawić jako maksimum RU/s na wartość: MAX (4000, 20 000/10, **50 * 100**) = 5000 ru/s (skaluje się między 500-5000 ru/s).

Przykład #2: Załóżmy, że masz kontener automatycznego skalowania z maksymalną liczbą jednostek RU/s z 100 000 RU/s i 100 GB miejsca w magazynie. Teraz można skalować maksymalną liczbę jednostek RU/s do 150 000 RU/s (skaluje się między 15 000 – 150 000 RU/s). Najniższa, minimalna wartość można teraz ustawić Max RU/s na: MAX (4000, **150 000/10**, 100 * 100) = 15 000 ru/s (skale się między 1500-15 000 ru/s). 

W przypadku udostępnionej bazy danych przepływności w przypadku obniżenia maksymalnej wartości RU/s wartość minimalna, którą można ustawić na: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` , zaokrąglana do najbliższej 1000 ru/s.  

Powyższe formuły i przykłady odnoszą się do minimalnej maksymalnego skalowania RU/s, które można ustawić, i różni się od `0.1 * Tmax` do zakresu, w `Tmax` którym system automatycznie skaluje się. Niezależnie od tego, jaka jest maksymalna wartość RU/s, system zawsze będzie skalowany między `0.1 * Tmax` do `Tmax` . 

### <a name="how-does-ttl-work-with-autoscale"></a>Jak działa czas wygaśnięcia (TTL) przy automatycznym skalowaniu?
W przypadku automatycznego skalowania operacje TTL nie wpływają na skalowanie RU/s. Wszystkie jednostek ru wykorzystane z powodu czasu wygaśnięcia nie są częścią rozliczanego obiektu RU/s kontenera skalowania automatycznego. 

Załóżmy na przykład, że masz kontener skalowania automatycznego z 400 – 4000 RU/s:
- Godzina 1: T = 0: kontener nie ma użycia (bez czasu wygaśnięcia ani żądań obciążeń). Miliardy RU/s to 400 RU/s.
- Godzina 1: T = 1: czas wygaśnięcia jest włączony.
- Godzina 1: T = 2: kontener rozpoczyna pobieranie żądań, co zużywa 1000 RU w ciągu 1 sekundy. Dostępne są również 200 jednostek ru wartości TTL. Możliwe do rozliczenia RU/s nadal 1000 RU/s. Bez względu na to, kiedy wystąpi TTLs, nie wpłynie to na logikę skalowania automatycznego skalowania.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Co to jest mapowanie między maksymalną liczbą jednostek RU/s a partycjami fizycznymi?
W przypadku wybrania opcji Maksymalna liczba jednostek RU na sekundę Azure Cosmos DB będzie można zapewnić: maks. RU/s/10 000 RU/s = # partycji fizycznych. Każda [partycja fizyczna](partitioning-overview.md#physical-partitions) może obsługiwać do 10 000 ru/s i 50 GB miejsca w magazynie. W miarę wzrostu rozmiaru magazynu Azure Cosmos DB automatycznie podzielić partycje w celu dodania większej liczby partycji fizycznych, aby obsłużyć wzrost magazynu, lub zwiększyć maksymalną wartość RU/s, jeśli [Rozmiar magazynu przekracza związany z nimi limit](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Maksymalna wartość RU/s bazy danych lub kontenera jest dzielona równomiernie między wszystkie partycje fizyczne. W związku z tym całkowita przepływność może być skalowana na jedną partycję fizyczną: maksymalna wartość RU/s bazy danych lub partycji fizycznych kontenera/#. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co się stanie, jeśli żądania przychodzące przekroczą maksymalną wartość RU/s bazy danych lub kontenera?
Jeśli całkowity zużyty RU/s przekracza maksymalną wartość RU/s bazy danych lub kontenera, żądania, które przekraczają maksymalną wartość RU/s, zostaną ograniczone i zwróci kod stanu 429. Żądania, które powodują przekroczenie 100% znormalizowanych użycia również zostaną ograniczone. Znormalizowane użycie jest zdefiniowane jako maksymalne użycie RU/s we wszystkich partycjach fizycznych. Załóżmy na przykład, że maksymalna przepływność wynosi 20 000 RU/s i masz dwie partycje fizyczne, P_1 i P_2, z których każdy może skalować do 10 000 RU/s. W danym drugim, jeśli P_1 użył 6000 jednostek ru i P_2 8000 jednostek ru, znormalizowanym wykorzystaniem jest MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Zestawy SDK klienta Azure Cosmos DB i narzędzia importu danych (Azure Data Factory, biblioteka modułu wykonawczego zbiorczego) automatycznie ponawiają próbę 429s, więc sporadyczne 429s są bardzo precyzyjne. Trwała duża liczba 429s może wskazywać konieczność zwiększenia maksymalnego RU/s lub zapoznania się z strategią partycjonowania dla [partycji gorąca](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Czy wciąż można zobaczyć 429s (ograniczanie przepustowości/ograniczanie szybkości), gdy automatyczne skalowanie jest włączone? 
Tak. Kody 429 mogą pojawiać się w dwóch scenariuszach. Po pierwsze, gdy całkowity zużyty RU/s przekroczy maksymalną wartość RU/s bazy danych lub kontenera, usługa będzie odpowiednio ograniczać żądania. 

Druga, jeśli istnieje partycja gorąca, czyli wartość klucza logicznej partycji, która ma nieproporcjonalnie wyższą liczbę żądań w porównaniu do innych wartości kluczy partycji, istnieje możliwość przekroczenia przez podstawową partycję RU/s budżetu. Najlepszym rozwiązaniem, aby uniknąć gorących partycji, jest [wybranie właściwego klucza partycji](partitioning-overview.md#choose-partitionkey), który zapewni równomierny rozkład magazynowania i przepływności. 

Jeśli na przykład zostanie wybrana opcja 20 000 RU/s i ma 200 GB miejsca w magazynie, z czterema partycjami fizycznymi, każda partycja fizyczna może być automatycznie skalowana do 5000 RU/s. Jeśli w konkretnym kluczu partycji logicznej znajduje się partycja gorąca, zobaczysz 429s, gdy bazowa partycja fizyczna, w której znajduje się ta wartość, przekracza 5000 RU/s, czyli przekracza 100% znormalizowane użycie.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [włączyć automatyczne skalowanie na Azure Cosmos DB bazie danych lub kontenerze](how-to-provision-autoscale-throughput.md).
* Dowiedz się więcej na temat [korzyści z przedziału IT obsługiwanego przez automatyczne skalowanie](provision-throughput-autoscale.md#benefits-of-autoscale).
* Dowiedz się więcej o [partycjach logicznych i fizycznych](partitioning-overview.md).
                        
