---
title: Zmienianie wzorców projektowania pliku danych w usłudze Azure Cosmos DB
description: Omówienie wzorów projektowania wspólnego kanału informacyjnego zmian
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450355"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Zmienianie wzorców projektowania pliku danych w usłudze Azure Cosmos DB

Źródło danych usługi Azure Cosmos DB umożliwia wydajne przetwarzanie dużych zestawów danych z dużą ilością zapisów. Plik danych o zmianie oferuje również alternatywę dla wykonywania zapytań dotyczących całego zestawu danych w celu zidentyfikowania zmian. Ten dokument koncentruje się na typowych wzorcach projektowania pliku danych zmian, kompromisach projektowych i ograniczeniach pliku danych zmian.

Usługa Azure Cosmos DB doskonale nadaje się do aplikacji do rejestrowania IoT, gier, sprzedaży detalicznej i rejestrowania operacyjnego. Typowy wzorzec projektu w tych aplikacjach jest użycie zmian w danych, aby wyzwolić dodatkowe akcje. Przykłady dodatkowych działań obejmują:

* Wyzwalanie powiadomienia lub wywołania interfejsu API, gdy element jest wstawiany lub aktualizowany.
* Przetwarzanie strumienia w czasie rzeczywistym dla IoT lub analizy w czasie rzeczywistym przetwarzania danych operacyjnych.
* Przenoszenie danych, takie jak synchronizacja z pamięcią podręczną, wyszukiwarką, magazynem danych lub magazynem chłodni.

Źródło danych zmian w usłudze Azure Cosmos DB umożliwia tworzenie wydajnych i skalowalnych rozwiązań dla każdego z tych wzorców, jak pokazano na poniższej ilustracji:

![Korzystanie z kanału informacyjnego usługi Azure Cosmos DB w celu zasilania analiz w czasie rzeczywistym i scenariuszy obliczeniowych opartych na zdarzeniach](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Przetwarzanie zdarzeń i powiadomienia

Źródło danych usługi Azure Cosmos DB może uprościć scenariusze, które muszą wyzwolić powiadomienie lub wywołanie interfejsu API na podstawie określonego zdarzenia. Za pomocą [biblioteki procesów kanału informacyjnego zmiany](change-feed-processor.md) można automatycznie sondować kontener pod kątem zmian i wywoływać zewnętrzny interfejs API za każdym razem, gdy na chwilę zapisu lub aktualizacji.

Można również selektywnie wyzwolić powiadomienie lub wysłać wywołanie do interfejsu API na podstawie określonych kryteriów. Na przykład jeśli czytasz z kanału informacyjnego zmiany przy użyciu [usługi Azure Functions](change-feed-functions.md), można umieścić logikę w funkcji tylko wysłać powiadomienie, jeśli określone kryteria zostały spełnione. Podczas gdy kod funkcji platformy Azure będzie wykonywany podczas każdego zapisu i aktualizacji, powiadomienie zostanie wysłane tylko wtedy, gdy spełnione zostały określone kryteria.

## <a name="real-time-stream-processing"></a>Przetwarzanie strumienia w czasie rzeczywistym

Źródło danych usługi Azure Cosmos DB może służyć do przetwarzania strumienia w czasie rzeczywistym do przetwarzania IoT lub analizy w czasie rzeczywistym na danych operacyjnych.
Na przykład można odbierać i przechowywać dane zdarzeń z urządzeń, czujników, infrastruktury i aplikacji oraz przetwarzać te zdarzenia w czasie rzeczywistym, używając [platformy Spark](../hdinsight/spark/apache-spark-overview.md). Na poniższej ilustracji pokazano, jak można zaimplementować architekturę lambda przy użyciu usługi Azure Cosmos DB za pośrednictwem źródła danych zmian:

![Potok lambdy oparty na usłudze Azure Cosmos DB do pozyskiwania i wykonywania zapytań](./media/change-feed/lambda.png)

W wielu przypadkach implementacje przetwarzania strumienia najpierw odbierają dużą ilość przychodzących danych do tymczasowej kolejki komunikatów, takich jak Usługa Azure Event Hub lub Apache Kafka. Źródło danych zmian jest doskonałą alternatywą ze względu na zdolność usługi Azure Cosmos DB do obsługi trwałego wysokiego wskaźnika pozyskiwania danych z gwarantowanym niskim opóźnieniem odczytu i zapisu. Zalety kanału informacyjnego usługi Azure Cosmos DB w kolejce komunikatów obejmują:

### <a name="data-persistence"></a>Trwałość danych

Dane zapisane w usłudze Azure Cosmos DB pojawią się w zestawieniu zmian i zostaną zachowane do czasu usunięcia. Kolejki komunikatów zazwyczaj mają maksymalny okres przechowywania. Na przykład [usługa Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) oferuje maksymalne przechowywanie danych 90 dni.

### <a name="querying-ability"></a>Możliwość wykonywania zapytań

Oprócz odczytu z kanału informacyjnego zmiany kontenera usługi Cosmos można również uruchamiać kwerendy SQL na danych przechowywanych w usłudze Azure Cosmos DB. Źródło danych zmian nie jest powielaniem danych już w kontenerze, ale raczej innym mechanizmem odczytywania danych. W związku z tym jeśli odczytujesz dane z źródła danych zmian, zawsze będzie zgodne z zapytaniami tego samego kontenera usługi Azure Cosmos DB.

### <a name="high-availability"></a>Wysoka dostępność

Usługa Azure Cosmos DB oferuje dostępność do odczytu i zapisu w wysokości do 99,999%. W przeciwieństwie do wielu kolejek komunikatów, dane usługi Azure Cosmos DB można łatwo globalnie dystrybuować i konfigurować z [RTO (Recovery Time Objective)](consistency-levels-tradeoffs.md#rto) zero.

Po przetworzeniu elementów w zestawieniu zmian można utworzyć zmaterializowany widok i utrwalić zagregowane wartości z powrotem w usłudze Azure Cosmos DB. Jeśli używasz usługi Azure Cosmos DB do tworzenia gry, możesz na przykład użyć kanału informacyjnego zmian, aby zaimplementować rankingi w czasie rzeczywistym na podstawie wyników z ukończonych gier.

## <a name="data-movement"></a>Przenoszenie danych

Można również odczytać z pliku danych zmian do przenoszenia danych w czasie rzeczywistym.

Na przykład kanał informacyjny zmian ułatwia efektywne wykonywanie następujących zadań:

* Zaktualizuj pamięć podręczną, indeks wyszukiwania lub magazyn danych za pomocą danych przechowywanych w usłudze Azure Cosmos DB.

* Wykonaj zerową migrację w dół do innego konta usługi Azure Cosmos lub innego kontenera usługi Azure Cosmos z innym kluczem partycji logicznej.

* Zaimplementuj warstwę danych i archiwizację na poziomie aplikacji. Na przykład można przechowywać "gorące dane" w usłudze Azure Cosmos DB i zerować "zimne dane" do innych systemów magazynu, takich jak [Usługa Azure Blob Storage.](../storage/common/storage-introduction.md)

Gdy trzeba [zdenormalizować dane między partycjami i kontenerami,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)można odczytać z źródła danych zmiany kontenera jako źródło tej replikacji danych. Replikacja danych w czasie rzeczywistym z zestawieniem danych zmian może tylko zagwarantować spójność ostateczną. Można [monitorować, jak daleko procesor kanału informacyjnego zmiany pozostaje](how-to-use-change-feed-estimator.md) w tyle w przetwarzaniu zmian w kontenerze usługi Cosmos.

## <a name="event-sourcing"></a>Pozyskiwanie zdarzeń

[Wzorzec pozyskiwania zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) polega na użyciu magazynu tylko do dołączania do rejestrowania pełnej serii akcji na tych danych. Źródło danych usługi Azure Cosmos DB to świetny wybór jako centralny magazyn danych w architekturach pozyskiwania zdarzeń, w których wszystkie pozyskiwanie danych jest modelowane jako zapisy (bez aktualizacji lub usuwania). W takim przypadku każdy zapis do usługi Azure Cosmos DB jest "zdarzeniem" i będziesz mieć pełny rekord przeszłych zdarzeń w pliku danych o zmianach. Typowe zastosowania zdarzeń publikowanych przez centralny magazyn zdarzeń służą do utrzymywania zmaterializowanych widoków lub integracji z systemami zewnętrznymi. Ponieważ nie ma limitu czasu przechowywania w pliku danych o zmianach, można odtworzyć wszystkie zdarzenia z przeszłości, czytając od początku kanału informacyjnego zmiany kontenera Usługi Cosmos.

Możesz mieć [wiele zmian, które konsumenci subskrybują w tym samym pliku danych o zmianach kontenera.](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers) Oprócz aprowizowanej przepływności [kontenera dzierżawy](change-feed-processor.md#components-of-the-change-feed-processor) nie ma żadnych kosztów wykorzystania źródła danych o zmianach. Plik danych jest dostępny w każdym pojemniku, niezależnie od tego, czy jest używany.

Usługa Azure Cosmos DB to doskonały centralny magazyn danych trwałych tylko do dołączania w wzorcu pozyskiwania zdarzeń ze względu na jego mocne strony w skalowalności poziomej i wysokiej dostępności. Ponadto biblioteka procesora danych strumieniowych zmian oferuje gwarancję ["co najmniej raz",](change-feed-processor.md#error-handling) dzięki której nie przegapisz przetwarzania żadnych zdarzeń.

## <a name="current-limitations"></a>Bieżące ograniczenia

Plik danych o zmianach ma ważne ograniczenia, które należy zrozumieć. Podczas gdy elementy w kontenerze usługi Cosmos zawsze pozostanie w pliku danych o zmianach, źródło danych zmiany nie jest dziennikiem pełnej operacji. Istnieją ważne obszary, które należy wziąć pod uwagę podczas projektowania aplikacji, która wykorzystuje źródło danych ze zmian.

### <a name="intermediate-updates"></a>Aktualizacje pośrednie

W pliku danych o zmianach uwzględniono tylko ostatnią zmianę dla danego elementu. Podczas przetwarzania zmian, można przeczytać najnowszą dostępną wersję elementu. Jeśli istnieje wiele aktualizacji tego samego elementu w krótkim okresie czasu, istnieje możliwość pominięcia przetwarzania aktualizacji pośrednich. Jeśli chcesz śledzić aktualizacje i być w stanie odtworzyć poprzednie aktualizacje elementu, zalecamy modelowanie tych aktualizacji jako serii zapisów zamiast tego.

### <a name="deletes"></a>Usuwa

Źródło danych ze zmian nie przechwytuje usuwa. Jeśli usuniesz element z kontenera, zostanie on również usunięty z kanału informacyjnego zmiany. Najczęstszą metodą obsługi tego jest dodanie znacznika miękkiego na elementach, które są usuwane. Można dodać właściwość o nazwie "usunięte" i ustawić go na "true" w momencie usuwania. Ta aktualizacja dokumentu pojawi się w zestawieniu zmian. W tym elemencie można ustawić czas wygaśnięcia, tak aby można go było automatycznie usunąć później.

### <a name="guaranteed-order"></a>Gwarantowane zamówienie

Istnieje gwarantowana kolejność w zestawieniu zmian w ramach wartości klucza partycji, ale nie w wartościach klucza partycji. Należy wybrać klucz partycji, który daje znaczącą gwarancję zamówienia.

Rozważmy na przykład aplikacji sieci sprzedaży przy użyciu wzorca projektowania pozyskiwania zdarzeń. W tej aplikacji różne akcje użytkownika są każdy "zdarzenia", które są modelowane jako zapisy do usługi Azure Cosmos DB. Wyobraź sobie, że niektóre przykładowe zdarzenia wystąpiły w następującej kolejności:

1. Klient dodaje przedmiot A do koszyka
2. Klient dodaje przedmiot B do koszyka
3. Klient usuwa przedmiot A z koszyka
4. Wyewidencjonowywania klienta i zawartość koszyka są wysyłane

Zmaterializowany widok bieżącej zawartości koszyka jest zachowywany dla każdego klienta. Ta aplikacja musi upewnić się, że te zdarzenia są przetwarzane w kolejności, w jakiej występują. Jeśli na przykład wyewidencjonowanie koszyka miało zostać przetworzone przed usunięciem towaru A, jest prawdopodobne, że odbiorca miałby towar A wysłany, w przeciwieństwie do żądanego towaru B. Aby zagwarantować, że te cztery zdarzenia są przetwarzane w kolejności ich wystąpienia, powinny one mieszczą się w tej samej wartości klucza partycji. Jeśli wybierzesz **nazwę użytkownika** (każdy klient ma unikatową nazwę użytkownika) jako klucz partycji, możesz zagwarantować, że te zdarzenia są wyświetlane w zestawieniu zmian w tej samej kolejności, w jakiej są zapisywane w usłudze Azure Cosmos DB.

## <a name="examples"></a>Przykłady

Oto kilka przykładów kodu pliku danych zmian w świecie rzeczywistym, które wykraczają poza zakres przykładów dostarczonych w docs firmy Microsoft:

- [Wprowadzenie do kanału zmian](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Przypadek użycia IoT wyśrodkowany wokół kanału informacyjnego zmian](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Przypadek użycia w sieci sprzedaży wyśrodkowany wokół pliku danych o zmianach](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Następne kroki

* [Omówienie zestawienia zmian](change-feed.md)
* [Opcje odczytu kanału informacyjnego zmiany](read-change-feed.md)
* [Korzystanie z kanału informacyjnego zmian za pomocą funkcji azure](change-feed-functions.md)