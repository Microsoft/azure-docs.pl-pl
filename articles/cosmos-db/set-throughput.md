---
title: Obsługa przepływności w kontenerach i bazach danych platformy Azure Cosmos
description: Dowiedz się, jak ustawić zainicjowaną przepływność dla kontenerów i baz danych platformy Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 81a31448a588849a410b37868cf579fbb0a9ceb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777780"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Wprowadzenie do zainicjowanej przepływności w Azure Cosmos DB

Azure Cosmos DB pozwala ustawić przepływność aprowizacji dla baz danych i kontenerów. Istnieją dwa typy zainicjowanej przepływności, standardowe (ręczne) lub automatyczne skalowanie. Ten artykuł zawiera omówienie sposobu działania zainicjowanej przepływności. 

Baza danych usługi Azure Cosmos jest jednostką zarządzania dla zestawu kontenerów. Baza danych składa się z zestawu niezależnych od schematów kontenerów. Kontenerem usługi Azure Cosmos jest jednostka skalowalności zarówno przepływności, jak i magazynu. Kontener jest partycjonowany w poziomie między maszynami z zestawu znajdującego się w regionie świadczenia usługi Azure i jest dystrybuowany między wszystkimi regionami świadczenia usługi Azure skojarzonymi z Twoim kontem usługi Azure Cosmos.

Za pomocą Azure Cosmos DB można zainicjować przepływność przy dwóch stopniach szczegółowości:
 
- Kontenery usługi Azure Cosmos
- Bazy danych usługi Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Ustawianie przepływności dla kontenera  

Przepływność obsługiwana w kontenerze usługi Azure Cosmos jest zarezerwowana wyłącznie dla tego kontenera. Kontener otrzymuje zainicjowaną przepływność przez cały czas. Tymczasowa przepływność na kontenerze jest obsługiwana finansowo przez umowy SLA. Aby dowiedzieć się, jak skonfigurować standardową (ręczną) przepływność na kontenerze, zobacz temat [udostępnianie przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md). Aby dowiedzieć się, jak skonfigurować przepływność automatycznego skalowania w kontenerze, zobacz temat [Obsługa przepływności automatycznego skalowania](how-to-provision-autoscale-throughput.md).

Ustawienie przedziału czasu obsługi administracyjnej w kontenerze to najczęściej używana opcja. Można elastycznie skalować przepływność dla kontenera przez zainicjowanie dowolnej ilości przepływności przy użyciu [jednostek żądań (jednostek ru)](request-units.md). 

Przepływność obsługiwana dla kontenera jest równomiernie dystrybuowana między partycjami fizycznymi, przy założeniu, że dobry klucz partycji, który dystrybuuje partycje logiczne równomiernie między partycjami fizycznymi, przepływność jest również równomiernie dystrybuowana we wszystkich partycjach logicznych kontenera. Nie można selektywnie określić przepływności dla partycji logicznych. Ponieważ co najmniej jedna partycja logiczna kontenera jest hostowana przez partycję fizyczną, partycje fizyczne należą wyłącznie do kontenera i obsługują przepływność zainicjowaną na tym kontenerze. 

Jeśli obciążenie uruchomione na partycji logicznej zużywa więcej niż przepływność przydzieloną do źródłowej partycji fizycznej, istnieje możliwość, że operacje będą ograniczane proporcjonalnie. Co jest znane jako _gorąca partycja_ , gdy jedna partycja logiczna ma nieproporcjonalną liczbę żądań niż inne wartości klucza partycji.

Po wystąpieniu ograniczenia szybkości można zwiększyć zainicjowaną przepływność dla całego kontenera lub wykonać operację ponownie. Należy również upewnić się, że wybrano klucz partycji, który równomiernie dystrybuuje magazyn i żąda woluminu. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowanie i skalowanie w poziomie w Azure Cosmos DB](partition-data.md).

Zalecamy skonfigurowanie przepływności na poziomie szczegółowości kontenera, gdy potrzebna jest gwarantowana wydajność dla kontenera.

Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna hostuje co najmniej jedną partycję logiczną kontenera:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Partycja fizyczna, która hostuje co najmniej jedną partycję logiczną kontenera" border="false":::

## <a name="set-throughput-on-a-database"></a>Ustawianie przepływności dla bazy danych

> [!NOTE]
> Przepływność aprowizacji w bazie danych Azure Cosmos nie jest obecnie możliwa w przypadku kont, w których włączono obsługę [kluczy zarządzanych przez klienta](how-to-setup-cmk.md) .

W przypadku aprowizacji przepływności w bazie danych Azure Cosmos przepływność jest udostępniana dla wszystkich kontenerów (nazywanych kontenerami udostępnionej bazy danych) w bazie danych programu. Wyjątkiem jest sytuacja, gdy aprowizowana przepływność zostanie określona dla konkretnych kontenerów w bazie danych. Udostępnianie Przełożonej przepływności na poziomie bazy danych między kontenerami jest analogiczne do hostowania bazy danych w klastrze maszyn. Ze względu na to, że wszystkie kontenery w ramach bazy danych współużytkują zasoby dostępne na komputerze, nie ma możliwości przewidywalnej wydajności w żadnym konkretnym kontenerze. Aby dowiedzieć się, jak skonfigurować zainicjowaną przepływność dla bazy danych, zobacz [Konfigurowanie aprowizacji przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md). Aby dowiedzieć się, jak skonfigurować przepływność automatycznego skalowania dla bazy danych, zobacz temat [udostępnianie przepływności skalowania automatycznego](how-to-provision-autoscale-throughput.md).

Ustawienie przepływności w bazie danych usługi Azure Cosmos gwarantuje, że dla tej bazy danych jest odbierana przepustowość. Ponieważ wszystkie kontenery w ramach bazy danych współużytkują przepływność, Azure Cosmos DB nie zapewnia żadnych przewidywalnych gwarancji przepływności dla określonego kontenera w tej bazie danych. Część przepływności, do której może odbierać określony kontener, zależy od:

* Liczba kontenerów.
* Wybór kluczy partycji dla różnych kontenerów.
* Rozkład obciążenia między różnymi partycjami logicznymi kontenerów. 

Zalecamy skonfigurowanie przepływności dla bazy danych, jeśli chcesz udostępnić przepływność w wielu kontenerach, ale nie chcesz przeznaczyć przepływności do żadnego konkretnego kontenera. 

W poniższych przykładach pokazano, gdzie preferowany jest udostępnienie przepływności na poziomie bazy danych:

* Udostępnienie przepływności aprowizacji bazy danych w zestawie kontenerów jest przydatne w przypadku aplikacji wielodostępnych. Każdy użytkownik może być reprezentowany przez odrębny kontener usługi Azure Cosmos.

* Udostępnianie przeprowadzonej przepływności bazy danych w zestawie kontenerów jest przydatne w przypadku migrowania bazy danych NoSQL, takiej jak MongoDB lub Cassandra, hostowanej w klastrze maszyn wirtualnych lub lokalnych serwerów fizycznych do Azure Cosmos DB. Zastanów się o zainicjowanej przepływności skonfigurowanej w bazie danych Azure Cosmos jako logiczne równoważne, ale tańsze i elastyczne, do tej pojemności obliczeniowej klastra MongoDB lub Cassandra.  

Wszystkie kontenery utworzone w bazie danych z zainicjowaną przepływność muszą zostać utworzone za pomocą [klucza partycji](partition-data.md). W dowolnym momencie przepływność przydzielona do kontenera w ramach bazy danych jest dystrybuowana do wszystkich partycji logicznych tego kontenera. W przypadku kontenerów, które współużytkują zainicjowaną przepływność skonfigurowaną w bazie danych, nie można wybiórczo zastosować przepływności do określonego kontenera lub partycji logicznej. 

Jeśli obciążenie partycji logicznej zużywa więcej niż przepływność przydzieloną do określonej partycji logicznej, operacje są ograniczone proporcjonalnie. W przypadku wystąpienia ograniczenia szybkości można zwiększyć przepływność całej bazy danych lub wykonać operację ponownie. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycje logiczne](partition-data.md).

Kontenery w bazie danych z udostępnioną przepływnością współdzielą przepływność (RU/s) przydzieloną do tej bazy danych. W bazie danych można mieć maksymalnie cztery kontenery z minimalną przepływnością 400 RU/s. W standardowym (ręcznym) rozpoczęciu przepływności każdy nowy kontener po pierwszym cztery będzie wymagać dodatkowego 100 RU/s. Przykładowo jeśli masz bazę danych z udostępnioną przepływnością z ośmioma kontenerami, minimalna wartość przepływności w tej bazie danych będzie wynosiła 800 RU/s. W przypadku przepływności z obsługą automatycznego skalowania można mieć maksymalnie 25 kontenerów w bazie danych z automatycznym skalą 4000 RU/s (skaluje się między 400 – 4000 RU/s).

> [!NOTE]
> W lutym 2020 Wprowadziliśmy zmianę, która umożliwia korzystanie z maksymalnie 25 kontenerów w udostępnionej bazie danych przepływności, co zapewnia lepszą obsługę przepływności w kontenerach. Po pierwszych 25 kontenerach możesz dodać więcej kontenerów do bazy danych tylko wtedy, gdy są one udostępniane [z dedykowaną przepływność](#set-throughput-on-a-database-and-a-container), która jest oddzielona od udostępnionej przepływności bazy danych.<br>
Jeśli konto Azure Cosmos DB zawiera już udostępnioną bazę danych przepływności z >= 25 kontenerami, konto i wszystkie inne konta w tej samej subskrypcji platformy Azure są wykluczone z tej zmiany. [Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli masz opinię lub pytania. 

Jeśli Twoje obciążenia wymagają usunięcia i ponownego utworzenia wszystkich kolekcji w bazie danych, zaleca się porzucenie pustej bazy danych i ponowne utworzenie nowej bazy danych przed utworzeniem kolekcji. Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna może hostować co najmniej jedną partycję logiczną, która należy do różnych kontenerów w bazie danych:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Partycja fizyczna, która hostuje co najmniej jedną partycję logiczną kontenera" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności dla bazy danych i kontenera

Można połączyć te dwa modele. Przepływność aprowizacji zarówno dla bazy danych, jak i kontenera jest dozwolona. Poniższy przykład pokazuje, jak zainicjować standardową (ręczną) przepływność administracyjną dla bazy danych Cosmos Azure i kontenera:

* Można utworzyć bazę danych usługi Azure Cosmos o nazwie *z* w standardowym (ręcznym) przepływności *"K"* jednostek ru. 
* Następnie utwórz pięć kontenerów o nazwie *a*, *B*, *C*, *D*i *E* w ramach bazy danych. Podczas tworzenia kontenera B upewnij się, że włączono **dedykowaną przepływność dla tej opcji kontenera** , a następnie jawnie Skonfiguruj *"P"* jednostek ru na potrzeby aprowizacji dla tego kontenera. Należy pamiętać, że w przypadku tworzenia bazy danych i kontenera można skonfigurować udostępnioną i dedykowaną przepływność. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Partycja fizyczna, która hostuje co najmniej jedną partycję logiczną kontenera":::

* Przepływność jednostek ru *"K"* jest udostępniana w czterech kontenerach *a*, *C*, *D*i *E*. Dokładna ilość przepływności *dostępna dla,* *C*, *D*lub *E* jest różna. Dla każdego z przepływności poszczególnych kontenerów nie ma umowy SLA.
* Kontener o nazwie *B* jest zagwarantowany do uzyskania jednostek ru przepływności *"P"* przez cały czas. Jest ona obsługiwana przez umowy SLA.

> [!NOTE]
> Nie można przekonwertować kontenera z zainicjowaną przepływność na kontener udostępnionej bazy danych. Nie można przekształcić kontenera udostępnionej bazy danych w celu uzyskania dedykowanej przepływności.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizowanie przepływności dla bazy danych lub kontenera

Po utworzeniu kontenera usługi Azure Cosmos lub bazy danych można zaktualizować zainicjowaną przepływność. Nie ma żadnego limitu maksymalnej alokowanej przepływności, którą można skonfigurować dla bazy danych lub kontenera. 

W celu oszacowania [minimalnej alokowanej przepływności](concepts-limits.md#storage-and-database-operations) bazy danych lub kontenera Znajdź wartość maksymalną:

* 400 RU/s 
* Bieżący magazyn w GB * 10 RU/s
* Najwyższy poziom RU/s zainicjowany dla bazy danych lub kontenera/100
* Liczba kontenerów * 100 RU/s (tylko udostępniona baza danych przepływności)

Rzeczywiste minimum RU/s może się różnić w zależności od konfiguracji konta. Za pomocą [metryk Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) można wyświetlić historię zainicjowanej przepływności (ru/s) i magazynu w ramach zasobu.

Minimalną przepływność kontenera lub bazy danych można pobrać programowo przy użyciu zestawów SDK lub wyświetlić wartość w Azure Portal. W przypadku korzystania z zestawu SDK platformy .NET [kontener. Metoda ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) umożliwia skalowanie wartości przepływności alokowanej. W przypadku korzystania z zestawu SDK języka Java [CosmosContainer. replaceProvisionedThroughput](sql-api-java-sdk-samples.md) Metoda pozwala skalować wartość przepływności.

W przypadku korzystania z zestawu .NET SDK Metoda [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) umożliwia pobieranie minimalnej przepływności kontenera lub bazy danych. 

W dowolnym momencie można skalować zainicjowaną przepływność kontenera lub bazy danych. Po wykonaniu operacji skalowania w celu zwiększenia przepływności może upłynąć dłuższy czas, ponieważ zadania systemowe zainicjują wymagane zasoby. Stan operacji skalowania można sprawdzić w Azure Portal lub programowo przy użyciu zestawów SDK. Korzystając z zestawu SDK platformy .NET, można uzyskać stan operacji skalowania przy użyciu `Container.ReadThroughputAsync` metody.

## <a name="comparison-of-models"></a>Porównanie modeli
W tej tabeli przedstawiono porównanie standardowego (ręcznej) przepływności dla bazy danych i kontenera. 

|**Parametr**  |**Przepustowość standardowa (ręczna) w bazie danych**  |**Standardowa (ręczna) przepływność na kontenerze**|**Automatyczne skalowanie przepływności bazy danych** | **Automatyczne skalowanie przepływności dla kontenera**|
|---------|---------|---------|---------|---------|
|Punkt wejścia (minimalny RU/s) |400 RU/s. Po pierwsze cztery kontenery każdy dodatkowy kontener wymaga co najmniej 100 RU/s</li> |400| Automatyczne skalowanie w zakresie od 400 do 4000 RU/s. Może mieć do 25 kontenerów bez minimum RU/s na kontener</li> | Automatyczne skalowanie w zakresie od 400 do 4000 RU/s.|
|Minimalna wartość RU/s na kontener|100|400|--|Skalowanie automatyczne z zakresu od 400 do 4000 RU/s|
|Maksymalna jednostek ru|Bez ograniczeń, w bazie danych.|Bez ograniczeń, w kontenerze.|Bez ograniczeń, w bazie danych.|Bez ograniczeń, w kontenerze.
|Jednostek ru przypisane lub dostępne dla określonego kontenera|Brak gwarancji. Jednostek ru przypisane do danego kontenera zależą od właściwości. Właściwościami mogą być wybór kluczy partycji kontenerów, które współdzielą przepływność, rozkład obciążenia oraz liczbę kontenerów. |Wszystkie jednostek ru skonfigurowane w kontenerze są zarezerwowane wyłącznie dla kontenera.|Brak gwarancji. Jednostek ru przypisane do danego kontenera zależą od właściwości. Właściwościami mogą być wybór kluczy partycji kontenerów, które współdzielą przepływność, rozkład obciążenia oraz liczbę kontenerów. |Wszystkie jednostek ru skonfigurowane w kontenerze są zarezerwowane wyłącznie dla kontenera.|
|Maksymalna ilość miejsca w magazynie dla kontenera|Ograniczona.|Nieograniczona liczba|Nieograniczona liczba|Nieograniczona liczba|
|Maksymalna przepływność na partycję logiczną kontenera|10 000 jednostek RU/s|10 000 jednostek RU/s|10 000 jednostek RU/s|10 000 jednostek RU/s|
|Maksymalna ilość miejsca (Data + indeks) na partycję logiczną kontenera|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [partycji logicznych](partition-data.md).
* Dowiedz się, jak [udostępnić Standard (ręczny) w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [zainicjować standardową (ręczną) przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
* Dowiedz się, jak [udostępnić przepływność automatycznego skalowania dla bazy danych lub kontenera usługi Azure Cosmos](how-to-provision-autoscale-throughput.md).
