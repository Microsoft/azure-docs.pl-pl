---
title: Często zadawane pytania dotyczące przepływności z obsługą automatycznego skalowania w Azure Cosmos DB
description: Często zadawane pytania dotyczące przepływności z obsługą automatycznego skalowania dla Azure Cosmos DB baz danych i kontenerów
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791786"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Często zadawane pytania dotyczące przepływności dotyczącej automatycznego skalowania w Azure Cosmos DB

W przypadku przepływności z obsługą automatycznego skalowania Azure Cosmos DB automatycznie będzie zarządzać i skalować jednostki RU/s kontenera lub bazy danych na podstawie użycia. Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące skalowania automatycznego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-autoscale-supported-for-all-apis"></a>Czy automatyczne skalowanie jest obsługiwane dla wszystkich interfejsów API?
Tak, automatyczne skalowanie jest obsługiwane dla wszystkich interfejsów API: Core (SQL), Gremlin, Table, Cassandra i API for MongoDB.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>Czy automatyczne skalowanie jest obsługiwane w przypadku kont z wieloma wzorcami?
Tak, Skalowanie automatyczne jest obsługiwane w przypadku kont z wieloma wzorcami. Maksymalna liczba jednostek RU/s jest dostępna w każdym regionie, który jest dodawany do konta Cosmos. 

### <a name="what-is-the-pricing-for-autoscale-"></a>Jakie są ceny automatycznego skalowania?
Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Jak mogę włączyć skalowanie automatyczne dla moich kontenerów lub baz danych?
Skalowanie automatyczne można włączyć w nowych kontenerach i bazach danych utworzonych przy użyciu Azure Portal.

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>Czy istnieje obsługa interfejsu wiersza polecenia lub zestawu SDK do tworzenia kontenerów lub baz danych z elastyczną alokacją automatycznego skalowania?
Obecnie można tworzyć tylko zasoby z zainicjowaną przepustowością automatycznego skalowania na podstawie Azure Portal. Obsługa interfejsu wiersza polecenia i zestawu SDK nie jest jeszcze dostępna.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Czy mogę włączyć funkcję automatycznego skalowania dla istniejącego kontenera lub bazy danych?
Obecnie można włączyć funkcję automatycznego skalowania dla nowych kontenerów i baz danych podczas ich tworzenia. Obsługa automatycznego skalowania w istniejących kontenerach i bazach danych nie jest jeszcze dostępna. Istniejące kontenery można migrować do nowego kontenera przy użyciu [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) lub [źródła zmian](change-feed.md). 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>Czy można wyłączyć automatyczne skalowanie na kontenerze lub w bazie danych?
Tak, możesz wyłączyć automatyczne skalowanie, przełączając się do opcji "standardowe (ręczne)" dla alokowanej przepływności. W bieżącej wersji, po przełączeniu z automatycznego skalowania na przepustowość standardową, nie można włączyć automatycznego skalowania dla tego samego zasobu. 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Czy funkcja automatycznego skalowania jest obsługiwana w przypadku udostępnionych baz danych przepływności?
Tak, Skalowanie automatyczne jest obsługiwane w przypadku udostępnionych baz danych przepływności. Aby włączyć tę funkcję, wybierz opcję automatycznego skalowania i **przepływność udostępniania** podczas tworzenia bazy danych. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Jaka jest liczba dozwolonych kolekcji na udostępnioną bazę danych przepływności, gdy funkcja automatycznego skalowania jest włączona?
W przypadku udostępnionych baz danych przepływności z włączonym skalowaniem automatycznym liczba dozwolonych kolekcji to: minimum (25, Max RU/s bazy danych/1000). Na przykład jeśli maksymalna przepływność bazy danych to 20 000 RU/s, baza danych może mieć MINIMALną (25, 20 000 RU/s/1000) = 20 kolekcji. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Jaki jest limit magazynowania skojarzony z każdą maksymalną opcją RU/s?  
Limit magazynowania w GB dla każdej maksymalnej wartości RU/s to: Max RU/s bazy danych lub kontenera/100. Na przykład jeśli maksymalna wartość RU/s wynosi 20 000 RU/s, zasób może obsługiwać 200 GB miejsca w magazynie. Zapoznaj się z artykułem [limity skalowania automatycznego](provision-throughput-autoscale.md#autoscale-limits) dla dostępnych maksymalnych opcji ru/s i magazynu. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Co się stanie w przypadku przekroczenia limitu magazynu związanego z moją maksymalną przepływność?
W przypadku przekroczenia limitu magazynowania związanego z maksymalną przepływność bazy danych lub kontenera Azure Cosmos DB automatycznie zwiększy maksymalną przepływność do następnej najwyższej warstwy, która będzie obsługiwała ten poziom magazynu. Załóżmy na przykład, że Zainicjowano obsługę bazy danych lub kontenera z opcją 4000 RU/s maksymalną przepływność, która ma limit magazynowania równy 50 GB. Jeśli magazyn zasobów ulegnie zwiększeniu do 100 GB, maksymalna wartość RU/s bazy danych lub kontenera zostanie automatycznie zwiększona do 20 000 RU/s, co może obsłużyć do 200 GB. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Jak szybko skalowanie w górę i w dół w zależności od skoków ruchu?
Funkcja automatycznego skalowania umożliwia natychmiastowe skalowanie w górę lub w dół jednostek RU/s w ramach minimalnego i maksymalnego zakresu RU/s na podstawie ruchu przychodzącego. Rozliczenia odbywają się z dokładnością do 1 godziny, w której opłaty są naliczane za najwyższy RU/s w określonej godzinie.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>Czy można określić niestandardową maksymalną przepływność (RU/s) dla skalowania automatycznego?
Obecnie można wybrać jedną z [czterech opcji](provision-throughput-autoscale.md#autoscale-limits) maksymalnej przepływności (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Czy mogę zwiększyć maksymalną liczbę jednostek RU/s (przenieść do wyższej warstwy) na bazę danych lub kontener? 
Tak. W opcji **skalowanie & ustawienia** dla kontenera lub opcji **skalowania** dla bazy danych można wybrać wyższą maksymalną wartość ru/s dla skalowania automatycznego. Jest to asynchroniczna operacja skalowania, która może zająć trochę czasu (zwykle 4-6 godzin, w zależności od wybranego obiektu RU/s), ponieważ usługa stanowi więcej zasobów do obsługi wyższej skali. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Czy mogę zmniejszyć maksymalną liczbę jednostek RU/s (przenieść do niższej warstwy) na bazę danych lub kontener?
Tak. Tak długo, jak bieżący magazyn bazy danych lub kontenera znajduje się poniżej [limitu magazynu](#what-is-the-storage-limit-associated-with-each-max-rus-option) skojarzonego z maksymalną warstwą ru/s, do której chcesz skalować w dół, możesz zmniejszyć maksymalną liczbę jednostek ru/s do tej warstwy. Na przykład jeśli wybrano 20 000 RU/s jako maksymalną wartość RU/s, można skalować maksymalną wartość RU/s do 4000 RU/s, jeśli masz mniej niż 50 GB magazynu (limit magazynowania skojarzony z usługami 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Co to jest mapowanie między maksymalną liczbą jednostek RU/s a partycjami fizycznymi?
W przypadku wybrania opcji Maksymalna liczba jednostek RU na sekundę Azure Cosmos DB będzie można zapewnić: maks. RU/s/10 000 RU/s = # partycji fizycznych. Każda [partycja fizyczna](partition-data.md#physical-partitions) może obsługiwać do 10 000 ru/s i 50 GB pamięci. W miarę wzrostu rozmiaru magazynu Azure Cosmos DB automatycznie podzielić partycje w celu dodania większej liczby partycji fizycznych, aby obsłużyć wzrost magazynu, lub zwiększyć maksymalną warstwę RU/s, jeśli [Rozmiar magazynu przekracza związany z nim limit](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Maksymalna wartość RU/s bazy danych lub kontenera jest dzielona równomiernie między wszystkie partycje fizyczne. W związku z tym całkowita przepływność może być skalowana na jedną partycję fizyczną: maksymalna wartość RU/s bazy danych lub partycji fizycznych kontenera/#. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Co się stanie, jeśli żądania przychodzące przekroczą maksymalną wartość RU/s bazy danych lub kontenera?
Jeśli całkowity zużyty RU/s przekracza maksymalną wartość RU/s kontenera lub bazy danych, żądania, które przekraczają maksymalną wartość RU/s, zostaną ograniczone i zwróci kod stanu 429. Żądania, które powodują przekroczenie 100% znormalizowanych użycia również zostaną ograniczone. Znormalizowane użycie jest zdefiniowane jako maksymalne użycie RU/s we wszystkich partycjach fizycznych. Załóżmy na przykład, że maksymalna przepływność wynosi 20 000 RU/s i masz dwie partycje fizyczne, P_1 i P_2, z których każdy może skalować do 10 000 RU/s. W danym drugim, jeśli P_1 użył 6000 jednostek ru i P_2 8000 jednostek ru, znormalizowanym wykorzystaniem jest MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Zestawy SDK klienta Azure Cosmos DB i narzędzia importu danych (Azure Data Factory, biblioteka modułu wykonawczego zbiorczego) automatycznie ponawiają próbę 429s, więc sporadyczne 429s są bardzo precyzyjne. Trwała duża liczba 429s może wskazywać konieczność zwiększenia maksymalnego RU/s lub zapoznania się z strategią partycjonowania dla [partycji gorąca](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Czy wciąż można zobaczyć 429s (ograniczanie przepustowości/ograniczanie szybkości), gdy automatyczne skalowanie jest włączone? 
Tak. Można zobaczyć 429s w dwóch scenariuszach. Po pierwsze, gdy całkowity zużyty RU/s przekracza maksymalną wartość RU/s kontenera lub bazy danych, usługa będzie ograniczać żądania. 

Druga, jeśli istnieje partycja gorąca, czyli wartość klucza logicznej partycji, która ma nieproporcjonalnie wyższą liczbę żądań w porównaniu do innych wartości kluczy partycji, istnieje możliwość przekroczenia przez podstawową partycję RU/s budżetu. Najlepszym rozwiązaniem, aby uniknąć używania partycji, należy [wybrać dobry klucz partycji](partitioning-overview.md#choose-partitionkey) , który powoduje równomierne rozłożenie magazynu i przepływność. 

Jeśli na przykład zostanie wybrana opcja 20 000 RU/s i ma 200 GB miejsca w magazynie, z czterema partycjami fizycznymi, każda partycja fizyczna może być automatycznie skalowana do 5000 RU/s. Jeśli w konkretnym kluczu partycji logicznej znajduje się partycja gorąca, zobaczysz 429s, gdy bazowa partycja fizyczna, w której znajduje się ta wartość, przekracza 5000 RU/s, czyli przekracza 100% znormalizowane użycie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [włączyć funkcję automatycznego skalowania w kontenerze lub bazie danych usługi Azure Cosmos](provision-throughput-autoscale.md#create-db-container-autoscale).
* Dowiedz się więcej na temat [korzyści z przedziału IT obsługiwanego przez automatyczne skalowanie ](provision-throughput-autoscale.md#autoscale-benefits).
* Dowiedz się więcej o [partycjach logicznych i fizycznych](partition-data.md).
