---
title: Zadania przesyłania strumieniowego Spark o wysokiej dostępności w PRZĘDZe — Azure HDInsight
description: Jak skonfigurować Apache Spark przesyłania strumieniowego w scenariuszu wysokiej dostępności w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 602fa1cab71a797dd25aca263e0c6a9f2aa616bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870232"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Tworzenie Apache Spark zadań przesyłania strumieniowego o wysokiej dostępności przy użyciu PRZĘDZy

[Apache Spark](https://spark.apache.org/) Przesyłanie strumieniowe umożliwia wdrożenie skalowalnych aplikacji o wysokiej przepływności i odpornych na uszkodzenia na potrzeby przetwarzania strumieni danych. Aplikacje Spark Streaming można połączyć z klastrem usługi HDInsight Spark do różnych rodzajów źródeł danych, takich jak Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), RAW TCP Sockets lub przez monitorowanie systemu plików [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) w celu wprowadzenia zmian. Funkcja przesyłania strumieniowego Spark obsługuje odporność na uszkodzenia z gwarancją, że każde dane zdarzenie jest przetwarzane dokładnie jeden raz, nawet w przypadku awarii węzła.

Usługa Spark Streaming tworzy długotrwałe zadania, w których można zastosować przekształcenia do danych, a następnie wypchnąć wyniki do systemów plików, baz danych, pulpitów nawigacyjnych i konsoli programu. Procesy przesyłania strumieniowego Spark przetwarzają mikropartie danych, tworząc najpierw partię zdarzeń w określonym przedziale czasu. Następnie ta partia jest wysyłana na potrzeby przetwarzania i wygenerowania danych wyjściowych. Przedziały czasu partii są zwykle zdefiniowane w częściach sekundy.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-streaming.png" alt-text="Przesyłanie strumieniowe Spark" border="false":::

## <a name="dstreams"></a>DStreams

Przesyłanie strumieniowe Spark reprezentuje ciągły strumień danych przy użyciu *strumienia zdyskretyzowanej* (DStream). Tę DStreamę można utworzyć na podstawie źródeł danych wejściowych, takich jak Event Hubs lub Kafka, lub stosując przekształcenia na innym DStream. Gdy zdarzenie dociera do aplikacji Spark streaming, zdarzenie jest przechowywane w niezawodny sposób. Oznacza to, że dane zdarzenia są replikowane, dzięki czemu wiele węzłów ma kopię. Gwarantuje to, że awaria jednego węzła nie spowoduje utraty zdarzenia.

Rdzeń Spark używa *rozproszonych zestawów danych* (odporne). Odporne Dystrybuuj dane między wieloma węzłami w klastrze, gdzie każdy węzeł ogólnie przechowuje swoje dane w pamięci w celu uzyskania najlepszej wydajności. Każdy RDD reprezentuje zdarzenia zbierane w ramach interwału wsadowego. Gdy upłynie interwał wsadowy, przesyłanie strumieniowe w usłudze Spark generuje nowy RDD zawierający wszystkie dane z tego interwału. Ten ciągły zestaw odporne jest zbierany w DStream. Aplikacja do przesyłania strumieniowego Spark przetwarza dane przechowywane w RDD każdej partii.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-dstream.png" alt-text="DStream platformy Spark" border="false":::

## <a name="spark-structured-streaming-jobs"></a>Zadania przetwarzania strumieniowego platformy Spark

Funkcja przesyłania strumieniowego platformy Spark została wprowadzona w platformie Spark 2,0 jako aparat analityczny służący do przesyłania strumieniowego danych strukturalnych. Funkcja przesyłania strumieniowego platformy Spark używa interfejsów API aparatu wsadowego SparkSQL. Podobnie jak w przypadku przesyłania strumieniowego Spark, strumień strukturalny platformy Spark uruchamia swoje obliczenia w sposób ciągły i składający się z mikropartii danych. Funkcja przesyłania strumieniowego platformy Spark reprezentuje strumień danych jako tabelę wejściową z nieograniczonymi wierszami. Oznacza to, że tabela wejściowa nadal rośnie po nadejściu nowych danych. Ta tabela wejściowa jest ciągle przetwarzana przez długotrwałe zapytanie, a wyniki są zapisywane do tabeli wyjściowej.

:::image type="content" source="./media/apache-spark-streaming-high-availability/structured-streaming.png" alt-text="Strumień strukturalny platformy Spark" border="false":::

W przypadku przesyłania strumieniowego ze strukturą dane docierają do systemu i są natychmiast pozyskiwane w tabeli wejściowej. Napisz zapytania, które wykonują operacje na tej tabeli wejściowej. Dane wyjściowe zapytania dają inną tabelę o nazwie tabela wyników. Tabela wyniki zawiera wyniki zapytania, z którego dane mają być wysyłane do zewnętrznego magazynu danych, takiego jak relacyjna baza danych. *Interwał wyzwalacza* określa czas, w którym dane są przetwarzane z tabeli wejściowej. Domyślnie proces przesyłania strumieniowego przetwarza dane zaraz po nadejściu. Można jednak skonfigurować wyzwalacz do uruchamiania na dłuższym interwale, dzięki czemu dane przesyłane strumieniowo są przetwarzane w partiach opartych na czasie. Dane w tabeli wyników mogą być odświeżane za każdym razem, gdy istnieją nowe dane, tak aby zawierały wszystkie dane wyjściowe od momentu rozpoczęcia zapytania przesyłania strumieniowego (*tryb pełny*) lub może zawierać tylko dane, które są nowe od momentu ostatniego przetworzenia zapytania (*tryb Append*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Twórz odporne na błędy zadania przetwarzania strumieniowego Spark

Aby utworzyć środowisko o wysokiej dostępności dla zadań przesyłania strumieniowego platformy Spark, Zacznij od kodowania poszczególnych zadań do odzyskania w przypadku awarii. Takie zadania samoobsługowego odzyskiwania są odporne na uszkodzenia.

Odporne mają kilka właściwości, które pomagają w dużej dostępności i odpornych na błędy zadań przetwarzania strumieniowego Spark:

* Partie danych wejściowych przechowywanych w odporne jako DStream są automatycznie replikowane w pamięci pod kątem odporności na uszkodzenia.
* Dane utracone z powodu błędu procesu roboczego można obliczyć ponownie z replikowanych danych wejściowych w różnych procesach roboczych, o ile te węzły są dostępne.
* Szybkie odzyskiwanie błędów może wystąpić w ciągu sekundy, ponieważ odzyskiwanie z błędów/stragglers odbywa się za pośrednictwem obliczeń w pamięci.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Dokładnie jedna semantyka z funkcją przesyłania strumieniowego Spark

Aby utworzyć aplikację, która przetwarza każde zdarzenie raz (i tylko raz), należy wziąć pod uwagę, w jaki sposób wszystkie punkty systemowe awarii zostały uruchomione ponownie po wystąpieniu problemu, a także jak można uniknąć utraty danych. Dokładnie wtedy, gdy semantyka wymaga, aby żadne dane nie zostały utracone w żadnym momencie, a przetwarzanie komunikatów jest uruchamiane ponownie, niezależnie od tego, gdzie wystąpi awaria. Zobacz [Tworzenie zadań przesyłania strumieniowego platformy Spark przy użyciu dokładnie jednokrotnego przetwarzania zdarzeń](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Przesyłanie strumieniowe Spark i Apache Hadoop

W usłudze HDInsight współpraca klastra jest koordynowana przez *inny* program. Projektowanie wysokiej dostępności dla przesyłania strumieniowego Spark obejmuje techniki przesyłania strumieniowego Spark, a także dla składników PRZĘDZy.  Poniżej przedstawiono przykładową konfigurację przy użyciu PRZĘDZy.

:::image type="content" source="./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png" alt-text="Architektura PRZĘDZy" border="false":::

W poniższych sekcjach opisano zagadnienia dotyczące projektowania tej konfiguracji.

### <a name="plan-for-failures"></a>Planowanie błędów

Aby utworzyć konfigurację PRZĘDZy dla wysokiej dostępności, należy zaplanować ewentualny błąd wykonawcy lub sterownika. Niektóre zadania przesyłania strumieniowego Spark obejmują również wymagania dotyczące gwarancji danych, które wymagają dodatkowej konfiguracji i instalacji. Na przykład aplikacja przesyłania strumieniowego może mieć wymóg biznesowy dla gwarancji o zerowej utracie danych pomimo dowolnego błędu występującego w systemie obsługującym przesyłanie strumieniowe lub klaster usługi HDInsight.

Jeśli **wykonawca** nie powiedzie się, jego zadania i odbiorniki zostaną automatycznie uruchomione ponownie przez platformę Spark, więc nie jest wymagana żadna zmiana konfiguracji.

Jeśli jednak **Sterownik** ulegnie awarii, wszystkie powiązane z nim Moduły wykonawcze zakończą się niepowodzeniem, a wszystkie odebrane bloki i wyniki obliczeń zostaną utracone. Aby odzyskać sprawność po awarii sterownika, należy użyć *punktów kontrolnych DStream* , jak opisano w temacie [Tworzenie zadań strumieniowych usługi Spark za pomocą dokładnie raz na przetwarzanie zdarzeń](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Funkcja DStream Checkpoint okresowo zapisuje *ukierunkowany wykres acykliczne* (DAG) DStreams do magazynu odpornego na błędy, takiego jak usługa Azure Storage.  Tworzenie punktów kontrolnych umożliwia przesyłanie strumieniowe platformy Spark w celu ponownego uruchomienia nieuszkodzonego sterownika z informacji o punkcie kontrolnym.  Ten sterownik uruchamia ponownie uruchamia nowe wykonawcy, a także ponownie uruchamia odbiorniki.

Aby odzyskać sterowniki z DStream Checkpoint:

* Skonfiguruj automatyczne ponowne uruchamianie sterowników w PRZĘDZe z ustawieniem konfiguracji `yarn.resourcemanager.am.max-attempts` .
* Ustaw katalog punktów kontrolnych w systemie plików zgodnym z systemem HDFS przy użyciu programu `streamingContext.checkpoint(hdfsDirectory)` .
* Restrukturyzacja kodu źródłowego do używania punktów kontrolnych do odzyskiwania, na przykład:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Skonfiguruj odzyskiwanie utraconych danych, włączając dziennik zapisu (WAL) w programie `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` , i Wyłącz replikację w pamięci dla danych wejściowych DStreams z `StorageLevel.MEMORY_AND_DISK_SER` .

Aby podsumować przy użyciu punktów kontrolnych + WAL i niezawodnych odbiorców, można dostarczyć "co najmniej raz" odzyskiwanie danych:

* Dokładnie raz, tak długo, jak odebrane dane nie są tracone i wyjścia są idempotentne lub transakcyjne.
* Dokładnie raz, przy użyciu nowej metody Kafka Direct, która używa Kafka jako zreplikowanego dziennika, zamiast używać odbiorników lub WALs.

### <a name="typical-concerns-for-high-availability"></a>Typowe problemy dotyczące wysokiej dostępności

* Monitorowanie zadań przesyłania strumieniowego jest trudniejsze niż w przypadku zadań wsadowych. Zadania przesyłania strumieniowego Spark są zwykle długotrwałe i PRZĘDZa nie agreguje dzienników do momentu zakończenia zadania.  Punkty kontrolne platformy Spark są tracone podczas uaktualniania aplikacji lub platformy Spark, a podczas uaktualniania należy wyczyścić katalog punktów kontrolnych.

* Skonfiguruj tryb klastra PRZĘDZy, aby uruchamiać sterowniki nawet wtedy, gdy klient ulegnie awarii. Aby skonfigurować automatyczne ponowne uruchamianie dla sterowników:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Platforma Spark i interfejs użytkownika funkcji przesyłania strumieniowego Spark mają konfigurowalny system metryk. Możesz również użyć dodatkowych bibliotek, takich jak grafit/Grafana, aby pobrać metryki pulpitu nawigacyjnego, takie jak "liczba rekordów przetworzonych", "użycie pamięci/GC na sterowniku & wykonywania", "całkowitego opóźnienia", "użycie klastra" i tak dalej. W przypadku przesyłania strumieniowego ze strukturą w wersji 2,1 lub nowszej można użyć `StreamingQueryListener` programu, aby zebrać dodatkowe metryki.

* Należy dzielić długotrwałe zadania.  Gdy aplikacja do przesyłania strumieniowego Spark jest przesyłana do klastra, należy zdefiniować kolejkę PRZĘDZy, w której uruchomiono zadanie. Można użyć [harmonogramu pojemności przędzy](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) do przesyłania długotrwałych zadań do oddzielnych kolejek.

* Bezpiecznie zamykaj aplikację przesyłania strumieniowego. Jeśli Twoje przesunięcia są znane i wszystkie Stany aplikacji są przechowywane zewnętrznie, można programowo zatrzymać aplikację przesyłania strumieniowego w odpowiednim miejscu. Jedną z technik jest użycie "punktów zaczepienia wątku" w Spark, sprawdzając flagę zewnętrzną co *n* sekund. Można również użyć *pliku znacznika* , który jest tworzony w systemie plików HDFS podczas uruchamiania aplikacji, a następnie usunąć go, gdy chcesz zatrzymać. W przypadku podejścia do pliku znacznika Użyj oddzielnego wątku w aplikacji Spark, który wywołuje kod podobny do tego:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Następne kroki

* [Omówienie Apache Spark Streaming](apache-spark-streaming-overview.md)
* [Twórz Apache Spark zadania przesyłania strumieniowego za pomocą dokładnie jednokrotnego przetwarzania zdarzeń](apache-spark-streaming-exactly-once.md)
* [Długotrwałe wykonywanie zadań przesyłania strumieniowego Apache Spark na PRZĘDZe](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Przesyłanie strumieniowe strukturalne: semantyka odporna na uszkodzenia](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Zdyskretyzowanej strumienie: model Fault-Tolerant na potrzeby skalowalnego przetwarzania strumieniowego](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
