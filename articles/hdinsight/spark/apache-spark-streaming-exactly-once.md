---
title: Przesyłanie strumieniowe Spark & przetwarzanie zdarzeń dokładnie raz — usługa Azure HDInsight
description: Jak skonfigurować Apache Spark streaming, aby przetwarzać zdarzenie raz i tylko raz.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2018
ms.openlocfilehash: 4ba7df665b24a3eba2cd185d85a17bd0ef456b0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929676"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Twórz Apache Spark zadania przesyłania strumieniowego z przetwarzaniem zdarzeń dokładnie raz

Aplikacje przetwarzania strumieniowego wykorzystują różne podejścia do sposobu obsługi komunikatów ponownego przetwarzania po wystąpieniu błędu w systemie:

* Co najmniej raz: Każdy komunikat jest gwarantowany do przetworzenia, ale może zostać przetworzony więcej niż raz.
* Maksymalnie raz: Każdy komunikat może być nieprzetwarzany lub nie można go przetworzyć. Jeśli komunikat jest przetwarzany, jest przetwarzany tylko raz.
* Dokładnie raz: Każdy komunikat jest gwarantowany do przetworzenia jednokrotnie i tylko raz.

W tym artykule opisano sposób konfigurowania przesyłania strumieniowego platformy Spark w celu osiągnięcia dokładnie jednego przetwarzania.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Semantyka dokładnie jednokrotna z Apache Spark Streaming

Najpierw rozważ, w jaki sposób wszystkie punkty systemu awarii nie są ponownie uruchomione po wystąpieniu problemu, i jak można uniknąć utraty danych. Aplikacja do przesyłania strumieniowego Spark:

* Źródło danych wejściowych.
* Co najmniej jeden proces odbiorcy, który pobiera dane ze źródła danych wejściowych.
* Zadania, które przetwarzają dane.
* Ujścia danych wyjściowych.
* Proces sterownika, który zarządza długotrwałym zadaniem.

Semantyka dokładnie jednokrotne wymaga, aby w żadnym momencie żadne dane nie zostały utracone i że przetwarzanie komunikatów jest uruchamiane ponownie, niezależnie od tego, gdzie wystąpi awaria.

### <a name="replayable-sources"></a>Źródła odtwarzania

Źródłem aplikacji Spark Streaming jest odczytywanie Twoich zdarzeń z programu, które muszą być *odtwarzane*. Oznacza to, że w przypadkach, gdy wiadomość została pobrana, ale system nie mógł zostać utrwalony lub przetworzony, Źródło musi dostarczyć ten sam komunikat ponownie.

Na platformie Azure usługa Azure Event Hubs i [Apache Kafka](https://kafka.apache.org/) w usłudze HDInsight udostępniają źródła, które można odtworzyć. Innym przykładem źródła odtwarzania jest system plików odporny na uszkodzenia, taki jak [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), obiekty blob usługi Azure Storage lub Azure Data Lake Storage, gdzie wszystkie dane są przechowywane w nieskończoność i w dowolnym momencie można w całości odczytywać dane.

### <a name="reliable-receivers"></a>Niezawodne odbiorcy

W przypadku przesyłania strumieniowego Spark źródła, takie jak Event Hubs i Kafka, mają *niezawodne odbiorcy*, gdzie każdy odbiorca śledzi postęp odczytywania źródła. Niezawodny odbiorca utrzymuje swój stan w magazynie odpornym na uszkodzenia, w ramach [Apache ZooKeeper](https://zookeeper.apache.org/) lub punktów kontrolnych przetwarzania strumieniowego platformy Spark, które są zapisywane w systemie plików HDFS. Jeśli taki odbiornik ulegnie awarii i zostanie później uruchomiony ponownie, może zostać wznowiony w miejscu, w którym został pozostawiony.

### <a name="use-the-write-ahead-log"></a>Korzystanie z dziennika Write-Ahead

Funkcja przesyłania strumieniowego Spark obsługuje dziennik Write-Ahead, w którym każde odebrane zdarzenie jest najpierw zapisywane w katalogu punktów kontrolnych platformy Spark w magazynie z odpornym na błędy, a następnie przechowywane w odpornym na rozdzielonym zestawie danych (RDD). Na platformie Azure Magazyn Odporny na uszkodzenia to system plików HDFS objęty usługą Azure Storage lub Azure Data Lake Storage. W aplikacji do przesyłania strumieniowego Spark dziennik Write-Ahead jest włączony dla wszystkich odbiorników przez ustawienie `spark.streaming.receiver.writeAheadLog.enable` konfiguracji na wartość `true` . Dziennik Write-Ahead zapewnia odporność na uszkodzenia dla niepowodzeń zarówno sterownika, jak i programu wykonującego.

W przypadku pracowników, którzy uruchamiają zadania dotyczące danych zdarzeń, każda RDD jest określana jako replikacja i dystrybucja między wieloma pracownikami. Jeśli zadanie nie powiedzie się z powodu awarii procesu roboczego, zadanie zostanie uruchomione ponownie w innym procesie roboczym, który ma replikę danych zdarzenia, więc zdarzenie nie zostanie utracone.

### <a name="use-checkpoints-for-drivers"></a>Używanie punktów kontrolnych dla sterowników

Należy ponownie uruchomić sterowniki zadań. Jeśli sterownik z uruchomioną aplikacją Spark Streaming nie ulegnie awarii, zajmie się wszystkimi uruchomionymi odbiornikami, zadaniami i dowolnymi odporne przechowywania danych zdarzeń. W takim przypadku należy mieć możliwość zapisania postępu zadania, aby można było je później wznowić. Jest to osiągane przez kontrolowanie bezpośredniego grafu (DAG) DStream okresowo dla magazynu odpornego na błędy. Metadane DAG obejmują konfigurację używaną do tworzenia aplikacji przesyłania strumieniowego, operacje definiujące aplikację oraz wszystkie partie, które znajdują się w kolejce, ale nie zostały jeszcze zakończone. Te metadane umożliwiają ponowne uruchomienie uszkodzonego sterownika z informacji o punkcie kontrolnym. Po ponownym uruchomieniu sterownika zostanie uruchomiony nowy odbiornik, który odzyska dane zdarzenia z powrotem do odporne z dziennika Write-Ahead.

Punkty kontrolne są włączane w ramach przesyłania strumieniowego Spark w dwóch krokach.

1. W obiekcie StreamingContext Skonfiguruj ścieżkę magazynu dla punktów kontrolnych:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    W usłudze HDInsight te punkty kontrolne powinny być zapisane w domyślnym magazynie dołączonym do klastra, w usłudze Azure Storage lub Azure Data Lake Storage.

2. Następnie określ interwał punktów kontrolnych (w sekundach) na DStream. W każdym interwale dane stanu pochodzące ze zdarzenia wejściowego są utrwalane w magazynie. Utrwalone dane stanu mogą zmniejszyć ilość obliczeń potrzebnych podczas odbudowywania stanu ze zdarzenia źródłowego.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Korzystanie z ujścia idempotentne

Docelowy ujścia, do którego zadanie zapisuje wyniki, musi być w stanie obsłużyć sytuację, w której ma ten sam wynik więcej niż jeden raz. Ujścia musi mieć możliwość wykrywania takich zduplikowanych wyników i ich ignorowania. Ujścia *idempotentne* można wywołać wiele razy z tymi samymi danymi bez zmiany stanu.

Można utworzyć ujścia idempotentne przez implementację logiki, która najpierw sprawdza obecność przychodzącego wyniku w magazynie danych. Jeśli wynik już istnieje, zapis powinien pojawić się z perspektywy zadania Spark, ale w rzeczywistości magazyn danych zignorował zduplikowane dane. Jeśli wynik nie istnieje, obiekt sink powinien wstawić ten nowy wynik do magazynu.

Na przykład można użyć procedury składowanej z Azure SQL Database, która wstawia zdarzenia do tabeli. Ta procedura składowana najpierw wyszukuje zdarzenie według pól kluczy i tylko wtedy, gdy nie znaleziono pasujących zdarzeń, rekord wstawiony do tabeli.

Innym przykładem jest użycie podzielonego systemu plików, takiego jak obiekty blob usługi Azure Storage lub Azure Data Lake Storage. W takim przypadku logika ujścia nie musi sprawdzać istnienia pliku. Jeśli plik reprezentujący zdarzenie istnieje, jest po prostu zastępowany tymi samymi danymi. W przeciwnym razie w ścieżce obliczanej zostanie utworzony nowy plik.

## <a name="next-steps"></a>Następne kroki

* [Omówienie Apache Spark Streaming](apache-spark-streaming-overview.md)
* [Tworzenie Apache Spark zadań przesyłania strumieniowego o wysokiej dostępności w ramach Apache Hadoop PRZĘDZy](apache-spark-streaming-high-availability.md)
