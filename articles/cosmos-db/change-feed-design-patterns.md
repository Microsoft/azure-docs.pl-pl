---
title: Zmień wzorce projektowe kanału informacyjnego w Azure Cosmos DB
description: Przegląd wspólnych wzorców projektowych źródeł zmian
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 443d00e61e593daacca04a4451b90bb78cc7d854
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334611"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Zmień wzorce projektowe kanału informacyjnego w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kanał informacyjny zmiany Azure Cosmos DB umożliwia wydajne przetwarzanie dużych zestawów danych przy użyciu dużej ilości operacji zapisu. Kanał informacyjny zmiany oferuje również alternatywę do wykonywania zapytań dotyczących całego zestawu danych w celu zidentyfikowania zmian. Ten dokument koncentruje się na wspólnych wzorcach, zaprojektowaniu, kompromisie i ograniczeniach dotyczących źródła zmian.

Azure Cosmos DB doskonale nadaje się w przypadku aplikacji IoT, gier, sprzedaży detalicznej i rejestrowania operacyjnego. Typowy Wzorzec projektowy w tych aplikacjach polega na użyciu zmian danych w celu wyzwolenia dodatkowych akcji. Przykłady dodatkowych akcji obejmują:

* Wyzwalanie powiadomienia lub wywołania interfejsu API, gdy element zostanie wstawiony lub zaktualizowany.
* Przetwarzanie strumienia w czasie rzeczywistym dla usługi IoT lub przetwarzania analiz w czasie rzeczywistym na danych operacyjnych.
* Przenoszenie danych, takie jak synchronizowanie z pamięcią podręczną, aparat wyszukiwania, magazyn danych lub chłodny magazyn.

Źródło zmian w Azure Cosmos DB umożliwia tworzenie wydajnych i skalowalnych rozwiązań dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Korzystanie z kanału informacyjnego zmiany Azure Cosmos DB do analiz w czasie rzeczywistym i scenariuszy obliczeniowych opartych na zdarzeniach" border="false":::

## <a name="event-computing-and-notifications"></a>Przetwarzanie i powiadomienia o zdarzeniach

Źródło zmian Azure Cosmos DB może uprościć scenariusze, które wymagają wyzwolenia powiadomienia lub wysłanie wywołania do interfejsu API na podstawie określonego zdarzenia. Za pomocą [biblioteki procesów kanału informacyjnego zmiany](change-feed-processor.md) można automatycznie sondować kontener pod kątem zmian i wywoływać zewnętrzny interfejs API za każdym razem, gdy występuje zapis lub aktualizacja.

Możesz również wybiórczo wyzwalać powiadomienie lub wysyłać wywołanie do interfejsu API na podstawie określonych kryteriów. Na przykład w przypadku odczytywania danych ze źródła zmian przy użyciu [Azure Functions](change-feed-functions.md)można umieścić logikę w funkcji, aby wysłać powiadomienie tylko w przypadku spełnienia określonych kryteriów. Chociaż kod funkcji platformy Azure będzie wykonywany podczas każdego zapisu i aktualizacji, powiadomienie będzie wysyłane tylko wtedy, gdy zostały spełnione określone kryteria.

## <a name="real-time-stream-processing"></a>Przetwarzanie strumienia w czasie rzeczywistym

Kanału informacyjnego zmiany Azure Cosmos DB można użyć do przetwarzania strumieniowego w czasie rzeczywistym na potrzeby przetwarzania w usłudze IoT lub analizy w czasie rzeczywistym na danych operacyjnych.
Na przykład można odbierać i przechowywać dane zdarzeń z urządzeń, czujników, infrastruktury i aplikacji, a następnie przetwarzać te zdarzenia w czasie rzeczywistym przy użyciu [platformy Spark](../hdinsight/spark/apache-spark-overview.md). Na poniższej ilustracji przedstawiono sposób implementacji architektury lambda przy użyciu Azure Cosmos DB za pośrednictwem źródła zmian:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Potok lambda oparty na Azure Cosmos DBach na potrzeby pozyskiwania i zapytania" border="false":::

W wielu przypadkach implementacje przetwarzania strumieniowego najpierw odbierają dużą ilość danych przychodzących do tymczasowej kolejki komunikatów, takiej jak usługa Azure Event Hub lub Apache Kafka. Kanał informacyjny zmiany jest doskonałym rozwiązaniem alternatywnym ze względu na zdolność Azure Cosmos DB do obsługi pozyskanej wysokiej szybkości pozyskiwania danych z gwarantowanym małym opóźnieniem odczytu i zapisu. Zalety Azure Cosmos DB kanału informacyjnego zmiany przez kolejkę komunikatów obejmują:

### <a name="data-persistence"></a>Trwałość danych

Dane zapisane do Azure Cosmos DB będą wyświetlane w strumieniu zmian i będą przechowywane do momentu usunięcia. Kolejki komunikatów zwykle mają maksymalny okres przechowywania. Na przykład [usługa Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) oferuje maksymalne przechowywanie danych przez 90 dni.

### <a name="querying-ability"></a>Możliwość wykonywania zapytań

Oprócz odczytywania danych z kanału informacyjnego zmiany kontenera Cosmos można także uruchamiać zapytania SQL na dane przechowywane w Azure Cosmos DB. Kanał informacyjny zmiany nie jest duplikatem danych znajdujących się już w kontenerze, ale raczej innym mechanizmem odczytu danych. W związku z tym, jeśli dane są odczytywane ze źródła zmian, zawsze będą spójne z zapytaniami tego samego kontenera Azure Cosmos DB.

### <a name="high-availability"></a>Wysoka dostępność

Azure Cosmos DB oferuje dostępność do 99,999% odczytu i zapisu. W przeciwieństwie do wielu kolejek komunikatów, Azure Cosmos DB dane można łatwo dystrybuować i konfigurować za pomocą [RTO (cel czasu odzyskiwania)](./consistency-levels.md#rto) równego zero.

Po przetworzeniu elementów w kanale informacyjnym zmiany można utworzyć materiałowy widok i zachować zagregowane wartości z powrotem w Azure Cosmos DB. Jeśli używasz Azure Cosmos DB do kompilowania gry, możesz na przykład użyć kanału informacyjnego zmiany w celu zaimplementowania rankingi w czasie rzeczywistym na podstawie wyników z ukończonych gier.

## <a name="data-movement"></a>Przenoszenie danych

Możesz również czytać ze źródła zmian w czasie rzeczywistym.

Na przykład kanał informacyjny zmiany ułatwia wykonywanie następujących zadań wydajnie:

* Aktualizowanie pamięci podręcznej, indeksu wyszukiwania lub magazynu danych przy użyciu danych przechowywanych w Azure Cosmos DB.

* Wykonaj zero migracji w dół do innego konta usługi Azure Cosmos lub innego kontenera platformy Azure Cosmos z innym kluczem partycji logicznej.

* Implementowanie warstwowych i archiwizowania danych na poziomie aplikacji. Można na przykład przechowywać "gorącą dane" w Azure Cosmos DB i w wieku "zimnych" danych do innych systemów magazynowania, takich jak [Azure Blob Storage](../storage/common/storage-introduction.md).

Gdy konieczne jest [denormalizacja danych w partycjach i kontenerach](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
), można odczytać ze źródła zmian kontenera jako źródło dla tej replikacji danych. Replikacja danych w czasie rzeczywistym ze źródłem zmian może gwarantować jedynie spójność ostateczną. Można [monitorować, jak daleko procesor kanału informacyjnego zmiany spowolnienia poza](how-to-use-change-feed-estimator.md) przetwarzaniem zmian w kontenerze Cosmos.

## <a name="event-sourcing"></a>Określanie źródła zdarzeń

[Wzorzec określania źródła zdarzeń](/azure/architecture/patterns/event-sourcing) obejmuje użycie magazynu tylko do dołączania w celu rejestrowania pełnej serii działań na tych danych. Azure Cosmos DB kanału informacyjnego zmiany to doskonały wybór jako centralny magazyn danych w architekturze pozyskiwania zdarzeń, w których wszystkie dane są modelowane jako zapisy (bez aktualizacji ani usuwania). W takim przypadku każdy zapis do Azure Cosmos DB jest "zdarzenie" i masz pełny rekord przeszłych zdarzeń ze źródła zmian. Typowe zastosowania zdarzeń opublikowanych w centralnym magazynie zdarzeń są przeznaczone do obsługi widoków z materiałami lub integracji z systemami zewnętrznymi. Ponieważ nie ma limitu czasu przechowywania w kanale informacyjnym zmiany, można odtworzyć wszystkie przeszłe zdarzenia, odczytując od początku źródła zmian kontenera Cosmos.

[Wielu odbiorców źródła danych zmian można subskrybować w ramach tego samego kontenera zmian](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Poza zainicjowaną przepływność [kontenera dzierżawy](change-feed-processor.md#components-of-the-change-feed-processor) nie ma kosztu korzystania ze źródła zmian. Kanał informacyjny zmiany jest dostępny w każdym kontenerze bez względu na to, czy jest on używany.

Azure Cosmos DB to doskonały centralny magazyn danych tylko do dołączania w wzorcu określania źródła zdarzeń ze względu na jego siłę w zakresie skalowalności poziomej i wysokiej dostępności. Ponadto biblioteka procesora kanału informacyjnego zmiany oferuje gwarancję ["co najmniej raz"](change-feed-processor.md#error-handling) , dzięki czemu nie można pominąć przetwarzania żadnych zdarzeń.

## <a name="current-limitations"></a>Bieżące ograniczenia

Źródło zmian ma ważne ograniczenia, które należy zrozumieć. Mimo że elementy w kontenerze Cosmos zawsze pozostają w zestawie strumieniowym zmian, źródłem zmian nie jest pełny dziennik operacji. Istnieją ważne obszary, które należy wziąć pod uwagę podczas projektowania aplikacji, która korzysta ze źródła zmian.

### <a name="intermediate-updates"></a>Aktualizacje pośrednie

Tylko Ostatnia zmiana dla danego elementu jest uwzględniona w źródle zmian. Podczas przetwarzania zmian zostanie odczytana najnowsza dostępna wersja elementu. Jeśli w krótkim czasie istnieje wiele aktualizacji tego samego elementu, można pominąć przetwarzanie aktualizacji pośrednich. Jeśli chcesz śledzić aktualizacje i być w stanie powtarzać wcześniejsze aktualizacje dla elementu, zalecamy modelowanie tych aktualizacji jako serii zapisów.

### <a name="deletes"></a>Kaskad

Kanał informacyjny zmiany nie przechwytuje usunięć. Usunięcie elementu z kontenera spowoduje również usunięcie go ze źródła zmian. Najbardziej typową metodą obsługi jest dodanie znacznika miękkiego do elementów, które są usuwane. Można dodać właściwość o nazwie "usunięte" i ustawić ją na wartość "true" w momencie usunięcia. Ta aktualizacja dokumentu zostanie wyświetlona w źródle zmian. Możesz ustawić wartość TTL dla tego elementu, aby można było ją automatycznie później usunąć.

### <a name="guaranteed-order"></a>Kolejność gwarantowana

W podanym kluczu partycji istnieje gwarantowana kolejność, ale nie między wartościami klucza partycji. Należy wybrać klucz partycji, który zapewnia znaczącą gwarancję zamówienia.

Rozważmy na przykład aplikację detaliczną przy użyciu wzorca projektowego określania źródła zdarzeń. W tej aplikacji różne akcje użytkownika to każdy "zdarzenia", które są modelowane jako zapisy do Azure Cosmos DB. Załóżmy, że niektóre Przykładowe zdarzenia wystąpią w następującej kolejności:

1. Klient dodaje element A do swojego koszyka zakupów
2. Klient dodaje element B do swojego koszyka zakupów
3. Klient usuwa element A z koszyka zakupów
4. Wyewidencjonowanie klienta i zawartości koszyka zakupów

W przypadku każdego klienta jest utrzymywana materiałowy widok bieżącej zawartości koszyka zakupów. Ta aplikacja musi zapewnić, że te zdarzenia są przetwarzane w kolejności ich występowania. Jeśli na przykład wyewidencjonowanie koszyka miało zostać przetworzone przed usunięciem elementu, prawdopodobnie klient miałby element, który wysłał, w przeciwieństwie do żądanego elementu B. W celu zagwarantowania, że te cztery zdarzenia są przetwarzane w kolejności ich występowania, powinny należeć do tej samej wartości klucza partycji. Jeśli wybierzesz opcję **username** (każdy klient ma unikatową nazwę użytkownika) jako klucz partycji, możesz zagwarantować, że te zdarzenia będą wyświetlane w źródle zmian w tej samej kolejności, w której są zapisywane w Azure Cosmos DB.

## <a name="examples"></a>Przykłady

Poniżej przedstawiono niektóre przykładowe kod kanału informacyjnego zmiany, który wykracza poza zakres przykładów dostępnych w witrynie Microsoft docs:

- [Wprowadzenie do kanału informacyjnego zmiany](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Przypadek użycia IoT wyśrodkowany wokół kanału informacyjnego zmiany](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Przypadek użycia detalicznego wyśrodkowany wokół źródła zmian](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Następne kroki

* [Omówienie zestawienia zmian](change-feed.md)
* [Opcje odczytu źródła zmian](read-change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)