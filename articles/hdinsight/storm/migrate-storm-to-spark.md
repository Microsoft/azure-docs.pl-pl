---
title: Migrowanie Apache Storm usługi Azure HDInsight 3,6 do programu HDInsight 4,0 Apache Spark
description: Przepływ różnic i migracji w celu migrowania obciążeń Apache Storm do przesyłania strumieniowego platformy Spark lub przetwarzania strumieniowego platformy Spark.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: b8b054d06c9c0987508abfdf03bbcf9470572bd1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868770"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrowanie Apache Storm usługi Azure HDInsight 3,6 do programu HDInsight 4,0 Apache Spark

W tym dokumencie opisano sposób migrowania obciążeń Apache Storm w usłudze HDInsight 3,6 do usługi HDInsight 4,0. Usługa HDInsight 4,0 nie obsługuje typu klastra Apache Storm i należy przeprowadzić migrację do innej platformy danych przesyłanych strumieniowo. Dwie odpowiednie opcje są Apache Spark przesyłania strumieniowego i przetwarzania strukturalnego platformy Spark. W tym dokumencie opisano różnice między tymi platformami, a także zalecane przepływy pracy służące do migrowania obciążeń Apache Storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Ścieżki migracji burzy w usłudze HDInsight

Jeśli chcesz przeprowadzić migrację z Apache Storm w usłudze HDInsight 3,6, masz wiele opcji:

* Przesyłanie strumieniowe Spark w usłudze HDInsight 4,0
* Strumień strukturalny platformy Spark w usłudze HDInsight 4,0
* Usługa Azure Stream Analytics

Ten dokument zawiera Przewodnik migracji z Apache Storm do strumienia Spark i przesyłania strumieniowego platformy Spark.

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="Ścieżka migracji burzy usługi HDInsight" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Porównanie między Apache Storm i Spark streaming, przetwarzania strumieniowego platformy Spark

System Apache Storm zapewnia różne poziomy gwarantowanego przetwarzania komunikatów. Na przykład podstawowa aplikacja burzowa może gwarantować co najmniej jednokrotne przetwarzanie, a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) może zagwarantować dokładnie jedno przetwarzanie. Funkcja przetwarzania strumieniowego platformy Spark i przetwarzania strumieniowego platformy Spark gwarantuje, że każde zdarzenie wejściowe jest przetwarzane dokładnie jeden raz, nawet jeśli wystąpi awaria węzła. Burza ma model, który przetwarza każde pojedyncze zdarzenie, i można również użyć modelu Micro Batch z Trident. Model przetwarzania strumieniowego platformy Spark i przesyłania strumieniowego platformy Spark zapewniają Micro-Batche.

|  |Storm |Przesyłanie strumieniowe w usłudze Spark | Strumień strukturalny platformy Spark|
|---|---|---|---|
|**Gwarancja przetwarzania zdarzeń**|Co najmniej raz <br> Dokładnie raz (Trident) |[Dokładnie raz](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Dokładnie raz](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Model przetwarzania**|Przesyłanie w czasie rzeczywistym <br> Micro Batch (Trident) |Micro Batch |Micro Batch |
|**Obsługa czasu zdarzenia**|[Tak](https://storm.apache.org/releases/2.0.0/Windowing.html)|Nie|[Tak](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Języki**|Java itp.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Przesyłanie strumieniowe Spark i przesyłanie strumieniowe platformy Spark

Proces przesyłania strumieniowego platformy Spark polega na wymianie strumienia Spark (DStreams). Przesyłanie strumieniowe ze strukturą będzie nadal otrzymywać ulepszenia i konserwację, a DStreams będzie tylko w trybie konserwacji. **Uwaga: musisz mieć linki, aby wyróżnić ten punkt**. Przesyłanie strumieniowe ze strukturą nie ma tylu funkcji jak DStreams dla źródeł i ujścia, które obsługuje, dlatego należy oszacować wymagania, aby wybrać odpowiednią opcję przetwarzania strumienia Spark.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Przetwarzanie strumieniowe (Single Event) i przetwarzanie Micro-Batch przetwarzania

Burza zawiera model, który przetwarza każde pojedyncze zdarzenie. Oznacza to, że wszystkie rekordy przychodzące będą przetwarzane zaraz po ich nadejściu. Aplikacje strumieniowe Spark muszą czekać część sekund, aby zebrać każdą mikropartię zdarzeń przed wysłaniem tej partii na potrzeby przetwarzania. Z kolei aplikacja oparta na zdarzeniach natychmiast przetwarza każde zdarzenie. Opóźnienie przesyłania strumieniowego Spark jest zwykle w ciągu kilku sekund. Zalety metody Micro-Batch to wydajniejsze przetwarzanie danych i prostsze obliczenia zagregowane.

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="przesyłanie strumieniowe i przetwarzanie mikropartii" border="false":::

## <a name="storm-architecture-and-components"></a>Architektura i składniki burzy

Topologie systemu Storm obejmują wiele składników rozmieszczonych w skierowanym grafie acyklicznym (DAG). Dane przepływają między składnikami tego grafu. Każdy składnik używa przynajmniej jednego strumienia danych i może opcjonalnie emitować przynajmniej jeden strumień.

|Składnik |Opis |
|---|---|
|Elementu Spout|Przenosi dane do topologii. Wysyłają one co najmniej jeden strumień danych do topologii.|
|Bolt|Wykorzystuje strumienie emitowane z elementy Spout lub innych piorunów. Składniki typu bolt mogą opcjonalnie emitować strumienie do topologii. Odpowiadają również za zapisywanie danych w usługach zewnętrznych lub magazynie — takim jak system plików HDFS, platforma Kafka lub usługa HBase.|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="Interakcja ze składnikami burzy" border="false":::

Burza składa się z następujących trzech demonów, które uniemożliwiają działanie klastra burzy.

|Demon |Opis |
|---|---|
|Nimbus|Podobnie jak w przypadku usługi Hadoop JobTracker, jest on odpowiedzialny za dystrybuowanie kodu wokół klastra i przypisywanie zadań do maszyn i monitorowanie pod kątem błędów.|
|Dozorcy|Służy do koordynowania klastra.|
|Nadzorca|Nasłuchuje pracy przypisanej do komputera i uruchamiania i zatrzymywania procesów roboczych na podstawie dyrektyw z Nimbus. Każdy proces roboczy wykonuje podzestaw topologii. Logika aplikacji użytkownika (elementy Spout i Piorun) jest uruchamiana w tym miejscu.|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="demony Nimbus, dozorcy i nadzorujące" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Architektura i składniki przesyłania strumieniowego platformy Spark

Poniższe kroki podsumowują sposób współdziałania składników w usłudze Spark streaming (DStreams) i przesyłania strumieniowego platformy Spark:

* Po uruchomieniu przesyłania strumieniowego Spark sterownik uruchamia zadanie w programie wykonującym.
* Moduł wykonujący otrzymuje strumień ze źródła danych przesyłania strumieniowego.
* Gdy moduł wykonujący otrzymuje strumienie danych, dzieli strumień na bloki i przechowuje je w pamięci.
* Bloki danych są replikowane do innych programów wykonujących.
* Przetworzone dane są następnie przechowywane w docelowym magazynie danych.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="ścieżka przesyłania strumieniowego Spark do danych wyjściowych" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>Przepływ pracy Spark streaming (DStream)

Po upływie każdego interwału partii tworzony jest nowy RDD, który zawiera wszystkie dane z tego interwału. Ciągłe zestawy odporne są zbierane do DStream. Jeśli na przykład interwał wsadowy jest dłuższy niż jedna sekunda, DStream emituje wsadowe co sekundę zawierające jedną RDD, która zawiera wszystkie dane, które zostały odebrane w ciągu sekundy. Podczas przetwarzania DStream, zdarzenie temperatury pojawia się w jednej z tych partii. Aplikacja Spark Streaming przetwarza partie zawierające zdarzenia i ostatecznie działa na danych przechowywanych w każdym RDD.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="przetwarzanie wsadowe przetwarzania strumieniowego platformy Spark" border="false":::

Aby uzyskać szczegółowe informacje o różnych przekształceniach dostępnych w ramach przesyłania strumieniowego Spark, zobacz [przekształcenia w DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Strumień strukturalny platformy Spark

Przesyłanie strumieniowe platformy Spark reprezentuje strumień danych w postaci tabeli, która jest nieograniczona. Tabela ciągle rośnie po nadejściu nowych danych. Ta tabela wejściowa jest ciągle przetwarzana przez długotrwałe zapytanie, a wyniki są wysyłane do tabeli wyjściowej.

W przypadku przesyłania strumieniowego ze strukturą dane docierają do systemu i są natychmiast pozyskiwane w tabeli wejściowej. Zapytania są zapisywane (przy użyciu Dataframe i API DataSet), które wykonują operacje na tej tabeli wejściowej.

Dane wyjściowe zapytania dają *tabelę wyników*, która zawiera wyniki zapytania. Dane można narysować z tabeli wyników dla zewnętrznego magazynu danych, takiego jak relacyjna.

Czas przetwarzania danych z tabeli wejściowej jest kontrolowany przez interwał wyzwalacza. Domyślnie interwał wyzwalacza wynosi zero, więc transmisja strukturalna próbuje przetworzyć dane zaraz po nadejściu. W tym przypadku oznacza to, że zaraz po wykonaniu operacji przesyłania strumieniowego ze strukturą przetwarzanie przebiegu poprzedniego zapytania rozpocznie się w wyniku kolejnego przetwarzania danych. Wyzwalacz można skonfigurować tak, aby uruchamiał się z interwałem, dzięki czemu dane przesyłane strumieniowo są przetwarzane w partiach opartych na czasie.

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="przetwarzanie danych w ramach przesyłania strumieniowego ze strukturą" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="model programowania dla przesyłania strumieniowego ze strukturą" border="false":::

## <a name="general-migration-flow"></a>Ogólny przepływ migracji

Zalecany przepływ migracji z burzy do platformy Spark przyjmuje następującą architekturę początkową:

* Kafka jest używany jako źródło danych przesyłania strumieniowego
* Kafka i burzy są wdrażane w tej samej sieci wirtualnej
* Dane przetworzone przez burzę są zapisywane w ujściach danych, takich jak usługa Azure Storage lub Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="Diagram przypuszczalnego środowiska bieżącego"  border="false":::

Aby przeprowadzić migrację aplikacji z burzy do jednego z interfejsów API przesyłania strumieniowego Spark, wykonaj następujące czynności:

1. **Wdróż nowy klaster.** Wdróż nowy klaster usługi HDInsight 4,0 Spark w tej samej sieci wirtualnej i Wdróż na nim aplikację Spark Streaming lub Spark Structured Streaming oraz przetestuj ją dokładnie.

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="Nowe wdrożenie platformy Spark w usłudze HDInsight" border="false":::

1. **Zatrzymywanie używania starego klastra burzy.** W istniejącej burze, Przestań zużywać dane ze źródła danych przesyłania strumieniowego i zaczekaj, aż dane zakończą zapisywanie do docelowego ujścia.

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="Zatrzymywanie używania bieżącego klastra" border="false":::

1. **Rozpocznij korzystanie z nowego klastra Spark.** Rozpocznij przesyłanie strumieniowe danych z nowo wdrożonego klastra usługi HDInsight 4,0 Spark. W tym momencie proces jest przejęty przez konsumowanie z ostatniego przesunięcia Kafka.

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="Rozpocznij korzystanie z nowego klastra" border="false":::

1. **W razie konieczności Usuń stary klaster.** Po zakończeniu i poprawnym działaniu przełącznika Usuń stary klaster burzy usługi HDInsight 3,6 zgodnie z wymaganiami.

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="Usuń stare klastry usługi HDInsight zgodnie z wymaganiami" border="false":::

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat burzy, przesyłania strumieniowego platformy Spark i przesyłania strumieniowego platformy Spark, zobacz następujące dokumenty:

* [Omówienie Apache Spark przesyłania strumieniowego](../spark/apache-spark-streaming-overview.md)
* [Przegląd Apache Spark strukturalnych przesyłania strumieniowego](../spark/apache-spark-structured-streaming-overview.md)
