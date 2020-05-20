---
title: Azure Cosmos DB interfejs API procesora źródła zmian platformy .NET, informacje o wersji zestawu SDK
description: Dowiedz się więcej na temat interfejsu API procesora i zestawu SDK, w tym daty wydania, daty wycofania i zmian wprowadzonych między poszczególnymi wersjami zestawu SDK procesora kanału zmian platformy .NET.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: e39cef33d8d402b6e04c6b9952cae21848e02424
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660432"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Zestaw SDK procesora kanału informacyjnego platformy .NET: pobieranie i informacje o wersji

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [Źródło zmian platformy .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Zestaw Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Pobieranie zestawu SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentacja interfejsu API**|[Dokumentacja referencyjna interfejsu API biblioteki procesora zmian](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Rozpoczęcie pracy**|[Wprowadzenie do zestawu .NET SDK procesora źródła zmian](change-feed.md)|
|**Bieżąca obsługiwana platforma**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET rdzeń](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Jeśli używasz procesora z zestawem danych zmian, zapoznaj się z najnowszą wersją 3. x [zestawu SDK platformy .NET](change-feed-processor.md), który zawiera zestaw SDK ze źródłem zmian. 

## <a name="release-notes"></a>Informacje o wersji

### <a name="v2-builds"></a>2 kompilacje

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Dodano nową metodę `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` i odpowiedni interfejs publiczny `ICheckpointPartitionProcessorFactory` . Umożliwia to implementację `IPartitionProcessor` interfejsu przy użyciu wbudowanego mechanizmu tworzenia punktów kontrolnych. Nowa fabryka jest podobna do istniejącej `IPartitionProcessorFactory` , z tą różnicą, że jej `Create` Metoda pobiera również `ILeaseCheckpointer` parametr.
* Tylko jedna z dwóch metod (lub) `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` może być używana dla tego samego `ChangeFeedProcessorBuilder` wystąpienia.

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Ulepszenia stabilności i diagnostyki:
  * Dodano obsługę wykrywania źródła zmian odczytywania przez dłuższy czas. Gdy trwa dłużej niż wartość określona przez `ChangeFeedProcessorOptions.ChangeFeedTimeout` Właściwość, zostaną wykonane następujące kroki:
    * Operacja odczytu źródła zmian na problematycznej partycji została przerwana.
    * Wystąpienie procesora kanału informacyjnego zmiany porzuca własność nieproblematycznej dzierżawy. Usunięta dzierżawa zostanie wybrana podczas kolejnego kroku pozyskiwania dzierżawy, który zostanie wykonany przez to samo lub inne wystąpienie procesora źródła zmian. W ten sposób odczytywanie kanału informacyjnego zmian zostanie rozpoczęte.
    * Problem jest raportowany w monitorze kondycji. Domyślny monitor kondycji wysyła wszystkie zgłoszone problemy do dziennika śledzenia.
  * Dodano nową właściwość publiczną: `ChangeFeedProcessorOptions.ChangeFeedTimeout` . Wartość domyślna tej właściwości to 10 minut.
  * Dodano nową publiczną wartość wyliczenia: `Monitoring.MonitoredOperation.ReadChangeFeed` . Gdy wartość `HealthMonitoringRecord.Operation` jest ustawiona na `Monitoring.MonitoredOperation.ReadChangeFeed` , wskazuje, że problem z kondycją jest związany ze źródłem zmian odczytywania.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Ulepszona strategia równoważenia obciążenia dla scenariusza, w którym wszystkie dzierżawy zajmują czas dłuższy niż interwał wygaśnięcia dzierżawy, na przykład z powodu problemów z siecią:
  * W tym scenariuszu algorytm równoważenia obciążenia używany do fałszywego traktowania dzierżaw jako wygasłych, co powoduje kradzież dzierżaw od aktywnych właścicieli. Może to spowodować niepotrzebne ponowne zrównoważenie wielu dzierżaw.
  * Ten problem został rozwiązany w tej wersji przez uniknięcie ponowienia konfliktu podczas uzyskiwania wygasłej dzierżawy, która nie zmieniła się, i odroczono pobieranie wygasłej dzierżawy do następnej iteracji równoważenia obciążenia.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Ulepszona obsługa wyjątków obserwatorów.
* Bogatsze informacje dotyczące błędów obserwatora:
  * Gdy obserwator zostanie zamknięty z powodu wyjątku zgłoszonego przez ProcessChangesAsync obserwatora, CloseAsync otrzyma teraz parametr przyczyny ustawiony na ChangeFeedObserverCloseReason. ObserverError.
  * Dodano ślady do identyfikowania błędów w kodzie użytkownika w obserwatorze.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Dodano obsługę funkcji Split w kolekcjach, które używają przepływności udostępnionej bazy danych.
  * W tej wersji rozwiązano problem, który może wystąpić podczas dzielenia w kolekcje przy użyciu przepływności udostępnionej bazy danych w przypadku podzielenia wyniku na ponowne równoważenie partycji z tylko jednym utworzonym zakresem kluczy partycji podrzędnej, a nie dwoma. W takim przypadku procesor źródła zmian może spowodować zatrzymanie usuwania dzierżawy dla starego zakresu kluczy partycji i nie tworzenia nowych dzierżaw. Problem został rozwiązany w tej wersji.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Dodano nową właściwość ChangeFeedProcessorOptions. StartContinuation, aby można było obsługiwać uruchamianie źródła zmian z tokenu kontynuacji żądania. Ta wartość jest używana tylko wtedy, gdy kolekcja dzierżawcy jest pusta lub dzierżawa nie ma ustawionego zestawu ContinuationToken. W przypadku dzierżaw w kolekcji dzierżawy, które mają ustawioną ContinuationToken, używany jest ContinuationToken i ChangeFeedProcessorOptions. StartContinuation jest ignorowana.

### <a name="223"></a><a name="2.2.3"/>Regulamin
* Dodano obsługę używania magazynu niestandardowego do utrwalania tokenów kontynuacji na partycję.
  * Na przykład niestandardowa magazyn dzierżawy może być Azure Cosmos DB kolekcje dzierżawy w dowolny sposób.
  * Niestandardowe magazyny dzierżaw mogą korzystać z nowej rozszerzalności ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) i interfejsu publicznego ILeaseStoreManager.
  * Refaktoryzacja interfejsu ILeaseManager w wielu interfejsach roli.
* Drobna zmiana podziału: Usunięto punkt rozszerzalności ChangeFeedProcessorBuilder. WithLeaseManager (ILeaseManager), zamiast tego należy użyć ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager).

### <a name="222"></a><a name="2.2.2"/>ppkt
* W tej wersji rozwiązano problem występujący podczas przetwarzania podziału w monitorowanej kolekcji i używania kolekcji dzierżaw z podziałem na partycje. Podczas przetwarzania dzierżawy dla partycji dzielonej nie można usunąć dzierżawy odpowiadającej tej partycji. Problem został rozwiązany w tej wersji.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Stałe obliczanie szacowania dla kont z wieloma wzorcami i nowy format tokenu sesji.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Dodano obsługę partycjonowanych kolekcji dzierżaw. Klucz partycji musi być zdefiniowany jako/ID.
* Drobna zmiana podziału: metody interfejsu IChangeFeedDocumentClient i klasy ChangeFeedDocumentClient zostały zmienione w taki sposób, aby zawierały parametry RequestOptions i CancellationToken. IChangeFeedDocumentClient to zaawansowany punkt rozszerzalności, który umożliwia udostępnianie niestandardowej implementacji klienta dokumentu do użycia z procesorem kanału informacyjnego zmiany, np. dekorować DocumentClient i przechwycenie wszystkich wywołań do niego w celu dodatkowego śledzenia, obsługi błędów itp. W przypadku tej aktualizacji kod implementujący IChangeFeedDocumentClient należy zmienić, aby uwzględnić nowe parametry w implementacji.
* Ulepszenia diagnostyki pomocniczej.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Dodano nowy interfejs API, zadanie &lt; IReadOnlyList &lt; RemainingPartitionWork &gt; &gt; IRemainingWorkEstimator. GetEstimatedRemainingWorkPerPartitionAsync (). Może to służyć do uzyskania szacowanej pracy dla każdej partycji.
* Obsługuje pakiet Microsoft. Azure. DocumentDB SDK 2,0. Wymaga programu Microsoft. Azure. DocumentDB 2,0 lub nowszego.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Dodano Właściwość publiczną ChangeFeedEventHost. HostName w celu zapewnienia zgodności z v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Naprawiono sytuację wyścigu, która występuje podczas dzielenia partycji. Sytuacja wyścigu może prowadzić do uzyskania dzierżawy i natychmiastowej utraty jej podczas dzielenia partycji i powodowania rywalizacji. Problem z warunkiem wyścigu jest rozwiązywany w tej wersji.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* ZESTAW SDK GA

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3 — wydanie wstępne
* Rozwiązano następujące problemy:
  * Po rozdzieleniu partycji może istnieć zduplikowane przetwarzanie dokumentów zmodyfikowane przed podziałem.
  * Interfejs API GetEstimatedRemainingWork zwrócił wartość 0, gdy w kolekcji dzierżawy nie ma żadnych dzierżaw.

* Następujące wyjątki są udostępniane publicznie. Rozszerzenia implementujące IPartitionProcessor mogą zgłosić te wyjątki.
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. LeaseLostException. 
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. Partitionexception. 
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. PartitionNotFoundException.
  * Microsoft. Azure. Documents. ChangeFeedProcessor. Exceptions. PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2 — wydanie wstępne
* Drobne zmiany interfejsu API:
  * Usunięto ChangeFeedProcessorOptions. IsAutoCheckpointEnabled, który został oznaczony jako przestarzały.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1 — wersja wstępna
* Ulepszenia stabilności:
  * Lepsza obsługa inicjowania magazynu dzierżawy. Gdy magazyn dzierżawy jest pusty, można zainicjować tylko jedno wystąpienie procesora, a pozostałe będą oczekiwać.
  * Bardziej stabilne/wydajne odnowienie/wydanie dzierżawy. Odnawianie i zwalnianie dzierżawy jedna partycja nie jest zależna od odnawiania innych. W wersji 1, która została wykonana sekwencyjnie dla wszystkich partycji.
* Nowy interfejs API v2:
  * Wzorzec konstruktora dla elastycznej konstrukcji procesora: Klasa ChangeFeedProcessorBuilder.
    * Może przyjmować dowolną kombinację parametrów.
    * Może przyjmować DocumentClient wystąpienie do monitorowania i/lub zbierania dzierżaw (niedostępne w wersji 1).
  * IChangeFeedObserver. ProcessChangesAsync teraz trwa CancellationToken.
  * IRemainingWorkEstimator — pozostała część pracy szacowania może być używana niezależnie od procesora.
  * Nowe punkty rozszerzalności:
    * IPartitionLoadBalancingStrategy — w przypadku niestandardowego równoważenia obciążenia partycji między wystąpieniami procesora.
    * ILease, ILeaseManager — do niestandardowego zarządzania dzierżawą.
    * IPartitionProcessor — w przypadku zmian przetwarzania niestandardowego na partycji.
* Rejestrowanie — używa biblioteki [LibLog](https://github.com/damianh/LibLog) .
* 100% wstecz zgodne z interfejsem API v1.
* Nowa baza kodu.
* Zgodne z [zestawami SDK programu SQL .NET](sql-api-sdk-dotnet.md) 1.21.1 i nowszymi wersjami.

### <a name="v1-builds"></a>kompilacje w wersji 1

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Dodano więcej rejestrowania.
* Naprawiono wyciek DocumentClient podczas wywołania szacowanej pracy w toku wiele razy.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Poprawki w szacowaniu oczekujących zadań.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Ulepszenia stabilności.
  * Poprawka dotycząca problemu z zadaniami anulowanymi, które mogą prowadzić do zatrzymania obserwatorów na niektórych partycjach.
* Obsługa ręcznego tworzenia punktów kontrolnych.
* Zgodne z [zestawem SDK programu SQL .NET](sql-api-sdk-dotnet.md) w wersji 1,21 lub nowszej.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Dodaje obsługę .NET Standard 2,0. Pakiet obsługuje teraz `netstandard2.0` `net451` monikery struktury.
* Zgodne z [zestawami SDK programu SQL .NET](sql-api-sdk-dotnet.md) 1.17.0 i nowszymi wersjami.
* Zgodne z programem [SQL zestaw .NET Core SDK](sql-api-sdk-dotnet-core.md) w wersji 1.5.1 i nowszych.

### <a name="111"></a><a name="1.1.1"/>ppkt
* Rozwiązuje problem z obliczeniem oszacowania liczby pozostałej pracy, gdy źródło zmian jest puste lub nie oczekuje pracy.
* Zgodne z [zestawami SDK programu SQL .NET](sql-api-sdk-dotnet.md) 1.13.2 i nowszymi wersjami.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Dodano metodę w celu uzyskania oszacowania pozostałej pracy, która ma zostać przetworzona w źródle zmian.
* Zgodne z [zestawami SDK programu SQL .NET](sql-api-sdk-dotnet.md) 1.13.2 i nowszymi wersjami.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* ZESTAW SDK GA
* Zgodne z wersjami [zestawu SDK programu SQL .NET](sql-api-sdk-dotnet.md) 1.14.1 i poniżej.

## <a name="release--retirement-dates"></a>Data wycofania &

Firma Microsoft zapewni powiadomienie co najmniej **12 miesięcy** przed WYCOFANIEM zestawu SDK w celu zapewnienia sprawnego przejścia do nowszej/obsługiwanej wersji.

Nowe funkcje i funkcje i optymalizacje są dodawane tylko do bieżącego zestawu SDK, dlatego zaleca się, aby zawsze uaktualnić do najnowszej wersji zestawu SDK tak szybko, jak to możliwe. 

Każde żądanie Cosmos DB za pomocą wycofanego zestawu SDK zostanie odrzucone przez usługę.

<br/>

| Wersja | Data wydania | Data wycofania |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2 kwietnia 2020 |--- |
| [2.2.8](#2.2.8) |28 października 2019 |--- |
| [2.2.7](#2.2.7) |14 maja 2019 r. |--- |
| [2.2.6](#2.2.6) |29 stycznia 2019 |--- |
| [2.2.5](#2.2.5) |13 grudnia 2018 |--- |
| [2.2.4](#2.2.4) |29 listopada 2018 |--- |
| [Regulamin](#2.2.3) |19 listopada 2018 |--- |
| [ppkt](#2.2.2) |31 października 2018 r. |--- |
| [2.2.1](#2.2.1) |24 października 2018 |--- |
| [1.3.3](#1.3.3) |Maja 08, 2018 |--- |
| [1.3.2](#1.3.2) |18 kwietnia 2018 |--- |
| [1.3.1](#1.3.1) |13 marca 2018 r. |--- |
| [1.2.0](#1.2.0) |31 października 2017 r. |--- |
| [ppkt](#1.1.1) |29 sierpnia 2017 |--- |
| [1.1.0](#1.1.0) |13 sierpnia 2017 |--- |
| [1.0.0](#1.0.0) |07 lipca 2017 |--- |

## <a name="faq"></a>Najczęściej zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także

Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .
