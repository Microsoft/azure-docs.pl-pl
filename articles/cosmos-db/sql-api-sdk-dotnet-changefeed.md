---
title: Interfejs API procesora zestawienia zmian platformy .NET w usłudze Azure Cosmos DB, informacje o wersji zestawu SDK
description: Dowiedz się więcej na temat interfejsu API i zestawu SDK procesora zestawienia zmian, w tym dat wydania, dat wycofania i zmian wprowadzonych między poszczególnymi wersjami zestawu SDK procesora zestawienia zmian platformy .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: ff861b5a58de4f108e49e52e6f09c71d7dd678ac
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097196"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Zestaw SDK procesora zestawienia zmian platformy .NET: pobieranie i informacje o wersjach
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zestawienia zmian platformy .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Spring Data 2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data 3](sql-api-sdk-java-spring-v3.md)
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [Dostawca zasobów REST] (/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Moduł wykonywania zbiorczego — .NET 2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Zestaw SDK do pobrania**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API biblioteki procesora zestawienia zmian](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet&preserve-view=true)|
|**Wprowadzenie**|[Wprowadzenie do zestawu .NET SDK procesora zestawienia zmian](change-feed.md)|
|**Bieżąca obsługiwana platforma**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Jeśli używasz procesora zestawienia zmian, zapoznaj się z najnowszą wersją 3.x zestawu [.NET SDK](change-feed-processor.md), która zawiera zestawienie zmian wbudowane w zestaw SDK. 

## <a name="release-notes"></a>Informacje o wersji

### <a name="v2-builds"></a>Kompilacje w wersji 2

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Dodano zgodność magazynu dzierżawy z zestawem SDK w wersji 3, który umożliwia stosowanie aktywnych ścieżek migracji. Aplikację można migrować do zestawu SDK w wersji 3 i przeprowadzić migrację z powrotem do biblioteki procesora zestawienia zmian bez utraty stanu.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Poprawiono przypadek, gdy przyczyna zamknięcia `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` została wysłana do elementu `FeedProcessing.IChangeFeedObserver.CloseAsync`, jeśli nie można odnaleźć partycji lub jeśli replika docelowa nie jest aktualna w sesji odczytu. W tych przypadkach teraz są używane przyczyny zamknięcia `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` i `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`.
* Dodano nową przyczynę zamknięcia `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`, która jest wysyłana w celu zamknięcia obserwatora zestawienia zmian, gdy replika docelowa nie jest aktualna w ramach sesji odczytu.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Dodano nową metodę `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` i odpowiedni interfejs publiczny `ICheckpointPartitionProcessorFactory`. Umożliwia to używanie przez implementację interfejsu `IPartitionProcessor` wbudowanego mechanizmu tworzenia punktów kontrolnych. Nowa fabryka jest podobna do istniejącej fabryki `IPartitionProcessorFactory` z tą różnicą, że jej metoda `Create` również pobiera parametr `ILeaseCheckpointer`.
* Tylko jedna z dwóch metod, `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` lub `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`, może być użyta dla tego samego wystąpienia `ChangeFeedProcessorBuilder`.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Ulepszenia stabilności i diagnostyki:
  * Dodano obsługę wykrywania długiego czasu odczytywania zestawienia zmian. Gdy odczyt trwa dłużej niż wartość określona przez właściwość `ChangeFeedProcessorOptions.ChangeFeedTimeout`, zostaną wykonane następujące kroki:
    * Operacja odczytu zestawienia zmian na partycji, której dotyczy problem, została przerwana.
    * Wystąpienie procesora zestawienia zmian usuwa własność dzierżawy, której dotyczy problem. Usunięta dzierżawa zostanie wybrana podczas kolejnego kroku pozyskiwania dzierżawy, który zostanie wykonany przez to samo lub inne wystąpienie procesora zestawienia zmian. W ten sposób odczytywanie zestawienia zmian zacznie się od początku.
    * Problem jest zgłaszany w monitorze kondycji. Domyślny monitor kondycji wysyła wszystkie zgłoszone problemy do dziennika śledzenia.
  * Dodano nową właściwość publiczną: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Wartość domyślna tej właściwości to 10 minut.
  * Dodano nową publiczną wartość wyliczeniową: `Monitoring.MonitoredOperation.ReadChangeFeed`. Gdy wartość `HealthMonitoringRecord.Operation` jest ustawiona na `Monitoring.MonitoredOperation.ReadChangeFeed`, wskazuje, że problem z kondycją dotyczy odczytywania zestawienia zmian.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Ulepszona strategia równoważenia obciążenia dla scenariusza, w którym pobieranie wszystkich dzierżaw trwa dłużej niż interwał wygaśnięcia dzierżawy, na przykład z powodu problemów z siecią:
  * W tym scenariuszu algorytm równoważenia obciążenia fałszywie traktował dzierżawy jako wygasłe, co powodowało kradzież dzierżaw od aktywnych właścicieli. Może to spowodować niepotrzebne ponowne zrównoważenie wielu dzierżaw.
  * Ten problem został rozwiązany w tej wersji przez uniknięcie ponowienia próby przy konflikcie podczas uzyskiwania wygasłej dzierżawy, której właściciel się nie zmienił, i odroczenia pobierania wygasłej dzierżawy do następnej iteracji równoważenia obciążenia.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Ulepszona obsługa wyjątków obserwatorów.
* Bardziej szczegółowe informacje dotyczące błędów obserwatora:
  * Gdy obserwator zostanie zamknięty z powodu wyjątku zgłoszonego przez element ProcessChangesAsync obserwatora, element CloseAsync otrzyma teraz parametr przyczyny ustawiony na ChangeFeedObserverCloseReason.ObserverError.
  * Dodano ślady do identyfikowania błędów w kodzie użytkownika w obserwatorze.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Dodano obsługę podziału w kolekcjach, które używają przepływności udostępnionej bazy danych.
  * W tej wersji rozwiązano problem, który może wystąpić podczas podziału kolekcji używających przepływności udostępnionej bazy danych, gdy wynik zostanie podzielony na ponowne równoważenie partycji z tylko jednym utworzonym zakresem kluczy partycji podrzędnej, a nie dwoma. W takim przypadku procesor zestawienia zmian może zatrzymać się na usuwaniu dzierżawy dla starego zakresu kluczy partycji i nie tworzyć nowych dzierżaw. Problem został rozwiązany w tej wersji.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Dodano nową właściwość ChangeFeedProcessorOptions.StartContinuation, aby można było obsługiwać uruchamianie zestawienia zmian z tokenu kontynuacji żądania. Ta wartość jest używana tylko wtedy, gdy kolekcja dzierżaw jest pusta lub dzierżawa nie ma ustawionego elementu ContinuationToken. W przypadku dzierżaw w kolekcji, które mają ustawiony element ContinuationToken, używany jest element ContinuationToken, a właściwość ChangeFeedProcessorOptions.StartContinuation jest ignorowana.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Dodano obsługę używania magazynu niestandardowego do utrwalania tokenów kontynuacji na partycję.
  * Na przykład niestandardowym magazynem dzierżawy może być kolekcja dzierżaw usługi Azure Cosmos DB podzielona na partycje w dowolny sposób.
  * Niestandardowe magazyny dzierżaw mogą korzystać z nowego punktu rozszerzenia ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) i interfejsu publicznego ILeaseStoreManager.
  * Zrefaktoryzowano interfejs ILeaseManager w wielu interfejsach roli.
* Drobna zmiana powodująca niezgodność: usunięto punkt rozszerzenia ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), zamiast tego należy użyć elementu ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* W tej wersji rozwiązano problem występujący podczas przetwarzania podziału w monitorowanej kolekcji i używania kolekcji dzierżaw z podziałem na partycje. Podczas przetwarzania dzierżawy dla podzielonej partycji nie można usunąć dzierżawy odpowiadającej tej partycji. Problem został rozwiązany w tej wersji.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Poprawiono obliczanie narzędzia do szacowania w przypadku kont z wieloma regionami zapisu i nowym formatem tokenu sesji.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Dodano obsługę partycjonowanych kolekcji dzierżaw. Klucz partycji musi być zdefiniowany jako /id.
* Drobna zmiana powodująca niezgodność: metody interfejsu IChangeFeedDocumentClient i klasy ChangeFeedDocumentClient zostały zmienione, tak aby zawierały parametry RequestOptions i CancellationToken. IChangeFeedDocumentClient to zaawansowany punkt rozszerzenia, który umożliwia niestandardowe zaimplementowanie klienta dokumentu do użycia z procesorem zestawienia zmian, na przykład dekorowanie elementu DocumentClient i przechwycenie wszystkich jego wywołań na potrzeby dodatkowego śledzenia, obsługi błędów itp. W przypadku tej aktualizacji kod implementujący element IChangeFeedDocumentClient należy zmienić, aby uwzględniał nowe parametry w implementacji.
* Drobne ulepszenia diagnostyki.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Dodano nowy interfejs API Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Może on służyć do uzyskania szacowanej pracy dla każdej partycji.
* Obsługuje zestaw Microsoft.Azure.DocumentDB SDK 2.0. Wymaga rozwiązania Microsoft.Azure.DocumentDB 2.0 lub nowszego.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* Dodano właściwość publiczną ChangeFeedEventHost.HostName w celu zapewnienia zgodności z wersją 1.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Naprawiono sytuację wyścigu, która występuje podczas dzielenia partycji. Sytuacja wyścigu może prowadzić do uzyskania dzierżawy i jej natychmiastowej utraty podczas dzielenia partycji, co powoduje rywalizację. Problem z sytuacją wyścigu został rozwiązywany w tej wersji.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* Zestaw SDK ogólnej dostępności

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3 — wersja wstępna
* Rozwiązano następujące problemy:
  * Po podzieleniu partycji może istnieć zduplikowane przetwarzanie dokumentów zmodyfikowanych przed podziałem.
  * Interfejs API GetEstimatedRemainingWork zwrócił wartość 0, gdy w kolekcji dzierżaw nie ma żadnych dzierżaw.

* Następujące wyjątki są udostępniane publicznie. Rozszerzenia implementujące element IPartitionProcessor mogą zgłosić te wyjątki.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2 — wersja wstępna
* Drobne zmiany interfejsu API:
  * Usunięto element ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, który został oznaczony jako przestarzały.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1 — wersja wstępna
* Ulepszenia stabilności:
  * Lepsza obsługa inicjowania magazynu dzierżawy. Gdy magazyn dzierżawy jest pusty, może go zainicjować tylko jedno wystąpienie procesora, a pozostałe będą czekać.
  * Stabilniejsze/wydajniejsze odnowienie/zwolnienie dzierżawy. Odnawianie i zwalnianie dzierżawy na jednej partycji jest niezależne od odnawiania innych. W wersji 1, która została wykonana sekwencyjnie dla wszystkich partycji.
* Nowy interfejs API w wersji2:
  * Wzorzec konstruktora dla elastycznej konstrukcji procesora: klasa ChangeFeedProcessorBuilder.
    * Może przyjmować dowolną kombinację parametrów.
    * Może przyjmować wystąpienie elementu DocumentClient do monitorowania i/lub kolekcji dzierżaw (niedostępne w wersji 1).
  * Element IChangeFeedObserver.ProcessChangesAsync teraz przyjmuje element CancellationToken.
  * IRemainingWorkEstimator — narzędzie do szacowania pozostałej części pracy może być używane niezależnie od procesora.
  * Nowe punkty rozszerzeń:
    * IPartitionLoadBalancingStrategy — w przypadku niestandardowego równoważenia obciążenia partycji między wystąpieniami procesora.
    * ILease, ILeaseManager — do niestandardowego zarządzania dzierżawą.
    * IPartitionProcessor — w przypadku zmian przetwarzania niestandardowego na partycji.
* Rejestrowanie — używa biblioteki [LibLog](https://github.com/damianh/LibLog).
* 100% zgodności wstecznej z interfejsem API w wersji1.
* Nowa baza kodu.
* Zgodność z [zestawem SQL .NET SDK](sql-api-sdk-dotnet.md) w wersji 1.21.1 lub nowszej.

### <a name="v1-builds"></a>Kompilacje w wersji 1

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Dodano więcej rejestrowania.
* Naprawiono wyciek elementu DocumentClient podczas wielokrotnego wywoływania szacowania oczekujących zadań.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Poprawki w zakresie szacowania oczekujących zadań.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Ulepszenia stabilności.
  * Poprawka dotycząca problemu z obsługą anulowanych zadań, co może prowadzić do zatrzymania obserwatorów na niektórych partycjach.
* Obsługa ręcznego tworzenia punktów kontrolnych.
* Zgodność z [zestawem SQL .NET SDK](sql-api-sdk-dotnet.md) w wersji 1.21 lub nowszej.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Dodano obsługę platformy .NET Standard 2.0. Pakiet obsługuje teraz krótkie nazwy platform `netstandard2.0` i `net451`.
* Zgodność z [zestawem SQL .NET SDK](sql-api-sdk-dotnet.md) w wersji 1.17.0 lub nowszej.
* Zgodność z [zestawem SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) w wersji 1.5.1 lub nowszej.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Rozwiązano problem z obliczeniem szacowanej liczby pozostałych zadań, gdy zestawienie zmian było puste lub nie było oczekujących zadań.
* Zgodność z [zestawem SQL .NET SDK](sql-api-sdk-dotnet.md) w wersji 1.13.2 lub nowszej.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Dodano metodę w celu uzyskania szacowanej pozostałej liczby zadań, które mają zostać przetworzone w zestawieniu zmian.
* Zgodność z [zestawem SQL .NET SDK](sql-api-sdk-dotnet.md) w wersji 1.13.2 lub nowszej.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* Zestaw SDK ogólnej dostępności
* Zgodność z [zestawem SQL .NET SDK](sql-api-sdk-dotnet.md) w wersji 1.14.1 lub starszej.

## <a name="release--retirement-dates"></a>Daty wydania i wycofania

Firma Microsoft udostępni powiadomienie co najmniej **12 miesięcy** przed wycofaniem zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji. Nowe funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualniać do najnowszej wersji zestawu SDK tak szybko, jak to możliwe.

> [!WARNING]
> Po 31 sierpnia 2022 r. usługa Azure Cosmos DB nie będzie już oferować poprawek błędów, nowych funkcji i obsługi wersji 1.x zestawu Azure Cosmos DB .NET lub .NET Core SDK dla interfejsu API SQL. Jeśli nie chcesz przeprowadzać uaktualnienia, żądania wysyłane z wersji 1.x zestawu SDK będą nadal obsługiwane przez usługę Azure Cosmos DB.

<br/>

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11 sierpnia 2020 r. |--- |
| [2.3.1](#2.3.1) |30 lipca 2020 r. |--- |
| [2.3.0](#2.3.0) |2 kwietnia 2020 r. |--- |
| [2.2.8](#2.2.8) |28 października 2019 r. |--- |
| [2.2.7](#2.2.7) |14 maja 2019 r. |--- |
| [2.2.6](#2.2.6) |29 stycznia 2019 r. |--- |
| [2.2.5](#2.2.5) |13 grudnia 2018 r. |--- |
| [2.2.4](#2.2.4) |29 listopada 2018 r. |--- |
| [2.2.3](#2.2.3) |19 listopada 2018 r. |--- |
| [2.2.2](#2.2.2) |31 października 2018 r. |--- |
| [2.2.1](#2.2.1) |24 października 2018 r. |--- |
| [1.3.3](#1.3.3) |8 maja 2018 r. |--- |
| [1.3.2](#1.3.2) |18 kwietnia 2018 r. |--- |
| [1.3.1](#1.3.1) |13 marca 2018 r. |--- |
| [1.2.0](#1.2.0) |31 października 2017 r. |--- |
| [1.1.1](#1.1.1) |29 sierpnia 2017 r. |--- |
| [1.1.0](#1.1.0) |13 sierpnia 2017 r. |--- |
| [1.0.0](#1.0.0) |7 lipca 2017 r. |--- |

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz też

Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
