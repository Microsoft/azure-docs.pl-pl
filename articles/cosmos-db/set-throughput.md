---
title: Obsługa przepływności w kontenerach i bazach danych platformy Azure Cosmos
description: Dowiedz się, jak ustawić zainicjowaną przepływność dla kontenerów i baz danych platformy Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 7caa29807f2779ee1f52cb22de2bf95fdb9cb37e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367129"
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

Po wystąpieniu ograniczenia szybkości można zwiększyć zainicjowaną przepływność dla całego kontenera lub wykonać operację ponownie. Należy również upewnić się, że wybrano klucz partycji, który równomiernie dystrybuuje magazyn i żąda woluminu. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowanie i skalowanie w poziomie w Azure Cosmos DB](partitioning-overview.md).

Zalecamy skonfigurowanie przepływności na poziomie szczegółowości kontenera, gdy chcesz przewidywalna wydajność kontenera.

Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna hostuje co najmniej jedną partycję logiczną kontenera:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Partycja fizyczna, która hostuje co najmniej jedną partycję logiczną kontenera" border="false":::

## <a name="set-throughput-on-a-database"></a>Ustawianie przepływności dla bazy danych

W przypadku aprowizacji przepływności w bazie danych Azure Cosmos przepływność jest udostępniana dla wszystkich kontenerów (nazywanych kontenerami udostępnionej bazy danych) w bazie danych programu. Wyjątkiem jest sytuacja, gdy aprowizowana przepływność zostanie określona dla konkretnych kontenerów w bazie danych. Udostępnianie Przełożonej przepływności na poziomie bazy danych między kontenerami jest analogiczne do hostowania bazy danych w klastrze maszyn. Ze względu na to, że wszystkie kontenery w ramach bazy danych współużytkują zasoby dostępne na komputerze, nie ma możliwości przewidywalnej wydajności w żadnym konkretnym kontenerze. Aby dowiedzieć się, jak skonfigurować zainicjowaną przepływność dla bazy danych, zobacz [Konfigurowanie aprowizacji przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md). Aby dowiedzieć się, jak skonfigurować przepływność automatycznego skalowania dla bazy danych, zobacz temat [udostępnianie przepływności skalowania automatycznego](how-to-provision-autoscale-throughput.md).

Ponieważ wszystkie kontenery w ramach bazy danych współużytkują przepływność, Azure Cosmos DB nie zapewnia żadnych przewidywalnych gwarancji przepływności dla określonego kontenera w tej bazie danych. Część przepływności, do której może odbierać określony kontener, zależy od:

* Liczba kontenerów.
* Wybór kluczy partycji dla różnych kontenerów.
* Rozkład obciążenia między różnymi partycjami logicznymi kontenerów. 

Zalecamy skonfigurowanie przepływności dla bazy danych, jeśli chcesz udostępnić przepływność w wielu kontenerach, ale nie chcesz przeznaczyć przepływności do żadnego konkretnego kontenera. 

W poniższych przykładach pokazano, gdzie preferowany jest udostępnienie przepływności na poziomie bazy danych:

* Udostępnienie przepływności aprowizacji bazy danych w zestawie kontenerów jest przydatne w przypadku aplikacji wielodostępnych. Każdy użytkownik może być reprezentowany przez odrębny kontener usługi Azure Cosmos.

* Udostępnianie przeprowadzonej przepływności bazy danych w zestawie kontenerów jest przydatne w przypadku migrowania bazy danych NoSQL, takiej jak MongoDB lub Cassandra, hostowanej w klastrze maszyn wirtualnych lub lokalnych serwerów fizycznych do Azure Cosmos DB. Zastanów się o zainicjowanej przepływności skonfigurowanej w bazie danych Azure Cosmos jako logiczne równoważne, ale tańsze i elastyczne, do tej pojemności obliczeniowej klastra MongoDB lub Cassandra.  

Wszystkie kontenery utworzone w bazie danych z zainicjowaną przepływność muszą zostać utworzone za pomocą [klucza partycji](partitioning-overview.md). W dowolnym momencie przepływność przydzielona do kontenera w ramach bazy danych jest dystrybuowana do wszystkich partycji logicznych tego kontenera. W przypadku kontenerów, które współużytkują zainicjowaną przepływność skonfigurowaną w bazie danych, nie można wybiórczo zastosować przepływności do określonego kontenera lub partycji logicznej. 

Jeśli obciążenie partycji logicznej zużywa więcej niż przepływność przydzieloną do określonej partycji logicznej, operacje są ograniczone proporcjonalnie. W przypadku wystąpienia ograniczenia szybkości można zwiększyć przepływność całej bazy danych lub wykonać operację ponownie. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycje logiczne](partitioning-overview.md).

Kontenery w bazie danych z udostępnioną przepływnością współdzielą przepływność (RU/s) przydzieloną do tej bazy danych. W bazie danych można mieć maksymalnie cztery kontenery z minimalną przepływnością 400 RU/s. W standardowym (ręcznym) rozpoczęciu przepływności każdy nowy kontener po pierwszym cztery będzie wymagać dodatkowego 100 RU/s. Przykładowo jeśli masz bazę danych z udostępnioną przepływnością z ośmioma kontenerami, minimalna wartość przepływności w tej bazie danych będzie wynosiła 800 RU/s. W przypadku przepływności z obsługą automatycznego skalowania można mieć maksymalnie 25 kontenerów w bazie danych z automatycznym skalą 4000 RU/s (skaluje się między 400 – 4000 RU/s).

> [!NOTE]
> W lutym 2020 Wprowadziliśmy zmianę, która umożliwia korzystanie z maksymalnie 25 kontenerów w udostępnionej bazie danych przepływności, co zapewnia lepszą obsługę przepływności w kontenerach. Po pierwszych 25 kontenerach możesz dodać więcej kontenerów do bazy danych tylko wtedy, gdy są one udostępniane [z dedykowaną przepływność](#set-throughput-on-a-database-and-a-container), która jest oddzielona od udostępnionej przepływności bazy danych.<br>
Jeśli konto Azure Cosmos DB zawiera już udostępnioną bazę danych przepływności z >= 25 kontenerami, konto i wszystkie inne konta w tej samej subskrypcji platformy Azure są wykluczone z tej zmiany. [Skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , jeśli masz opinię lub pytania. 

Jeśli Twoje obciążenia wymagają usunięcia i ponownego utworzenia wszystkich kolekcji w bazie danych, zaleca się porzucenie pustej bazy danych i ponowne utworzenie nowej bazy danych przed utworzeniem kolekcji. Na poniższej ilustracji przedstawiono, w jaki sposób partycja fizyczna może hostować co najmniej jedną partycję logiczną, która należy do różnych kontenerów w bazie danych:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Partycja fizyczna, która hostuje co najmniej jedną partycję logiczną kontenera" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Ustawianie przepływności dla bazy danych i kontenera

Można połączyć te dwa modele. Przepływność aprowizacji zarówno dla bazy danych, jak i kontenera jest dozwolona. Poniższy przykład pokazuje, jak zainicjować standardową (ręczną) przepływność administracyjną dla bazy danych Cosmos Azure i kontenera:

* Można utworzyć bazę danych usługi Azure Cosmos o nazwie *z* w standardowym (ręcznym) przepływności *"K"* jednostek ru. 
* Następnie utwórz pięć kontenerów o nazwie *a*, *B*, *C*, *D*i *E* w ramach bazy danych. Podczas tworzenia kontenera B upewnij się, że włączono **dedykowaną przepływność dla tej opcji kontenera** , a następnie jawnie Skonfiguruj *"P"* jednostek ru na potrzeby aprowizacji dla tego kontenera. Można skonfigurować udostępnioną i dedykowaną przepływność tylko podczas tworzenia bazy danych i kontenera. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Partycja fizyczna, która hostuje co najmniej jedną partycję logiczną kontenera":::

* Przepływność jednostek ru *"K"* jest udostępniana w czterech kontenerach *a*, *C*, *D*i *E*. Dokładna ilość przepływności *dostępna dla,* *C*, *D*lub *E* jest różna. Dla każdego z przepływności poszczególnych kontenerów nie ma umowy SLA.
* Kontener o nazwie *B* jest zagwarantowany do uzyskania jednostek ru przepływności *"P"* przez cały czas. Jest ona obsługiwana przez umowy SLA.

> [!NOTE]
> Nie można przekonwertować kontenera z zainicjowaną przepływność na kontener udostępnionej bazy danych. Nie można przekształcić kontenera udostępnionej bazy danych w celu uzyskania dedykowanej przepływności.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aktualizowanie przepływności dla bazy danych lub kontenera

Po utworzeniu kontenera usługi Azure Cosmos lub bazy danych można zaktualizować zainicjowaną przepływność. Nie ma żadnego limitu maksymalnej alokowanej przepływności, którą można skonfigurować dla bazy danych lub kontenera.

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> Bieżąca obsługiwana przepływność

Można pobrać zainicjowaną przepływność kontenera lub bazy danych w Azure Portal lub przy użyciu zestawów SDK:

* [Container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) w zestawie .NET SDK.
* [CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput?view=azure-java-stable&preserve-view=true) w zestawie Java SDK.

Odpowiedź tych metod zawiera również [minimalną zainicjowaną przepływność](concepts-limits.md#storage-and-database-operations) dla kontenera lub bazy danych:

* [ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput?view=azure-dotnet&preserve-view=true) w zestawie .NET SDK.
* [ThroughputResponse. getMinThroughput ()](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput?view=azure-java-stable&preserve-view=true) w zestawie Java SDK.

Rzeczywiste minimum RU/s może się różnić w zależności od konfiguracji konta. Zwykle jest to maksymalna wartość:

* 400 RU/s 
* Bieżący magazyn w GB * 10 RU/s
* Najwyższy poziom RU/s zainicjowany dla bazy danych lub kontenera/100
* Liczba kontenerów * 100 RU/s (tylko udostępniona baza danych przepływności)

### <a name="changing-the-provisioned-throughput"></a>Zmiana zainicjowanej przepływności

Zainicjowaną przepływność kontenera lub bazy danych można skalować za pomocą Azure Portal lub przy użyciu zestawów SDK:

* [Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) w zestawie .NET SDK.
* [CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput?view=azure-java-stable&preserve-view=true) w zestawie Java SDK.

W przypadku **obniżenia ilości zainicjowanej przepływności**będzie można wykonać [minimalnie](#current-provisioned-throughput).

Jeśli **rośnie przepływność**, większość czasu operacja jest chwilowo. Istnieją jednak sytuacje, w których operacja może trwać dłużej, ze względu na zadania systemowe do udostępniania wymaganych zasobów. W takim przypadku próba modyfikacji zainicjowanej przepływności podczas wykonywania tej operacji spowoduje zwrócenie odpowiedzi HTTP 423 z komunikatem o błędzie z informacją o tym, że inna operacja skalowania jest w toku.

> [!NOTE]
> Jeśli planujesz bardzo duże obciążenie pozyskiwania, które będzie wymagało dużego wzrostu przepływności, należy pamiętać, że operacja skalowania nie ma umowy SLA i, jak wspomniano w poprzednim akapicie, może zająć dużo czasu, gdy wzrost jest duży. Możesz chcieć zaplanować i rozpocząć skalowanie przed rozpoczęciem obciążenia i użyć poniższych metod w celu sprawdzenia postępu.

Możesz programowo sprawdzić postęp skalowania, odczytując [bieżącą przepływność](#current-provisioned-throughput) i korzystając z:

* [ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending?view=azure-dotnet&preserve-view=true) w zestawie .NET SDK.
* [ThroughputResponse. isReplacePending ()](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending?view=azure-java-stable&preserve-view=true) w zestawie Java SDK.

Za pomocą [metryk Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) można wyświetlić historię zainicjowanej przepływności (ru/s) i magazynu w ramach zasobu.

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> Duży magazyn/niska przepływność

Zgodnie z opisem w powyższej sekcji dotyczącej [przepływności](#current-provisioned-throughput) , minimalna przepływność, którą można udostępnić na kontenerze lub w bazie danych, zależy od wielu czynników. Jedną z nich jest ilość przechowywanych danych, ponieważ Azure Cosmos DB wymusza minimalną przepływność 10 RU/s na GB miejsca w magazynie.

Może to stanowić problem w sytuacjach, w których należy przechowywać duże ilości danych, ale mają one niewielkie wymagania dotyczące przepływności. Aby lepiej dopasować te scenariusze, Azure Cosmos DB wprowadził **"wysoki magazyn/niska przepływność"** , który zmniejsza ograniczenie ru/s na GB z 10 do 1 na kwalifikujących się kontach.

Obecnie musisz mieć co najmniej 1 bazę danych o pojemności lub udostępnionej przepływności zawierającej więcej niż 1 TB danych na koncie, aby można było kwalifikować się do korzystania z niego. Aby dołączyć do tego programu i ocenić swoje pełne uprawnienia, musisz wypełnić [tę ankietę](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u). Zespół Azure Cosmos DB będzie następnie kontynuował pracę i Doprowadzimy do dołączania.

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

* Dowiedz się więcej na temat [partycji logicznych](partitioning-overview.md).
* Dowiedz się, jak [udostępnić Standard (ręczny) w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).
* Dowiedz się, jak [zainicjować standardową (ręczną) przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).
* Dowiedz się, jak [udostępnić przepływność automatycznego skalowania dla bazy danych lub kontenera usługi Azure Cosmos](how-to-provision-autoscale-throughput.md).
