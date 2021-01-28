---
title: Strumień strukturalny platformy Spark w usłudze Azure HDInsight
description: Jak korzystać z aplikacji do tworzenia strumieniowego platformy Spark w klastrach usługi HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 9f92007c271da5b6d2cb8db6c3904a62b114e7c2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929501"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Przegląd Apache Spark strukturalnych przesyłania strumieniowego

[Apache Spark](https://spark.apache.org/) Przesyłanie strumieniowe ze strukturą umożliwia implementowanie skalowalnych, o wysokiej przepływności aplikacji odpornych na uszkodzenia na potrzeby przetwarzania strumieni danych. Przesyłanie strumieniowe ze strukturą jest oparte na aparacie Spark SQL i usprawnia konstrukcje z ramek danych SQL platformy Spark i zestawów DataSet, dzięki czemu można pisać zapytania przesyłania strumieniowego w taki sam sposób, jak w przypadku pisania zapytań wsadowych.  

Aplikacje do przesyłania strumieniowego ze strukturą działają w klastrach usługi HDInsight Spark i łączą się z danymi przesyłanymi strumieniowo z [Apache Kafka](https://kafka.apache.org/), gniazda TCP (do celów debugowania), usługi Azure Storage lub Azure Data Lake Storage. Te ostatnie dwie opcje, które korzystają z zewnętrznych usług magazynu, umożliwiają śledzenie nowych plików dodanych do magazynu i przetwarzanie ich zawartości tak, jakby były przesyłane strumieniowo.

Przesyłanie strumieniowe ze strukturą tworzy długotrwałe zapytanie, podczas którego są stosowane operacje do danych wejściowych, takie jak zaznaczenie, projekcja, agregacja, okna i dołączanie do ramki Dataframe z odwołaniami. Następnie wyprowadzasz wyniki do magazynu plików (obiekty blob usługi Azure Storage lub Data Lake Storage) lub do dowolnego magazynu danych przy użyciu kodu niestandardowego (takiego jak SQL Database lub Power BI). Przesyłanie strumieniowe ze strukturą udostępnia również dane wyjściowe do konsoli na potrzeby debugowania lokalnego oraz do tabeli w pamięci, dzięki czemu można zobaczyć dane wygenerowane na potrzeby debugowania w usłudze HDInsight.

![Przetwarzanie strumieni z użyciem usługi HDInsight i przesyłania strumieniowego platformy Spark](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]  
> Proces przesyłania strumieniowego platformy Spark polega na wymianie strumienia Spark (DStreams). Dzięki temu przesyłanie strumieniowe ze strukturą będzie otrzymywać ulepszenia i konserwację, a DStreams będzie tylko w trybie konserwacji. Funkcja przesyłania strumieniowego strukturalnego nie jest obecnie jako DStreams dla źródeł i ujścia, które obsługuje, dlatego należy oszacować wymagania, aby wybrać odpowiednią opcję przetwarzania strumienia Spark.

## <a name="streams-as-tables"></a>Strumienie jako tabele

Przesyłanie strumieniowe platformy Spark reprezentuje strumień danych w postaci tabeli, która jest niezależna. oznacza to, że tabela ciągle rośnie po nadejściu nowych danych. Ta *tabela wejściowa* jest ciągle przetwarzana przez długotrwałe zapytanie i wyniki wysyłane do *tabeli wyjściowej*:

![Koncepcja strukturalnego przesyłania strumieniowego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

W przypadku przesyłania strumieniowego ze strukturą dane docierają do systemu i są natychmiast pozyskiwane w tabeli wejściowej. Zapytania są zapisywane (przy użyciu Dataframe i API DataSet), które wykonują operacje na tej tabeli wejściowej. Dane wyjściowe zapytania dają inną tabelę, *tabelę wyników*. Tabela wyniki zawiera wyniki zapytania, z którego są rysowane dane dla zewnętrznego magazynu danych, takiego jak relacyjna baza danych. Czas przetwarzania danych z tabeli wejściowej jest kontrolowany przez *Interwał wyzwalacza*. Domyślnie interwał wyzwalacza wynosi zero, więc transmisja strukturalna próbuje przetworzyć dane zaraz po nadejściu. W tym przypadku oznacza to, że zaraz po wykonaniu operacji przesyłania strumieniowego ze strukturą przetwarzanie przebiegu poprzedniego zapytania rozpocznie się w wyniku kolejnego przetwarzania danych. Wyzwalacz można skonfigurować tak, aby uruchamiał się z interwałem, dzięki czemu dane przesyłane strumieniowo są przetwarzane w partiach opartych na czasie.

Dane w tabelach wyników mogą zawierać tylko te dane, które są nowe od czasu ostatniego przetworzenia zapytania (*tryb dołączania*) lub tabela może być odświeżana za każdym razem, gdy są nowe dane, tak aby tabela zawierała wszystkie dane wyjściowe od momentu rozpoczęcia zapytania przesyłania strumieniowego (*tryb kompletny*).

### <a name="append-mode"></a>Tryb dołączania

W trybie dołączania tylko wiersze dodane do tabeli wyniki od momentu ostatniego uruchomienia zapytania są obecne w tabeli wyników i zapisywane w magazynie zewnętrznym. Na przykład najprostsza kwerenda po prostu kopiuje wszystkie dane z tabeli wejściowej do tabeli Results bez zmian. Za każdym razem, gdy upłynie interwał wyzwalacza, nowe dane są przetwarzane, a wiersze reprezentujące nowe dane pojawiają się w tabeli wyników.

Rozważmy scenariusz, w którym przetwarzasz dane telemetryczne z czujników temperatury, takich jak termostat. Przyjmij pierwszy wyzwalacz, który przetworzył jedno zdarzenie w czasie 00:01 dla urządzenia 1 z temperaturą odczytywania 95 stopni. W pierwszym wyzwalaczu zapytania w tabeli Results pojawia się tylko wiersz z godziną 00:01. W czasie 00:02 po nadejściu kolejnego zdarzenia jedynym nowym wierszem jest wiersz z godziną 00:02, więc tabela wyników będzie zawierać tylko jeden wiersz.

![Tryb dołączania do strukturalnego przesyłania strumieniowego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

W przypadku korzystania z trybu dołączania zapytanie będzie stosować projekcje (wybierając kolumny, które dba informacje), filtrowanie (pobieranie tylko wierszy, które pasują do określonych warunków) lub łączenie (rozszerzanie danych przy użyciu statycznej tabeli odnośników). Tryb dołączania ułatwia wypychanie tylko odpowiednich nowych punktów danych do magazynu zewnętrznego.

### <a name="complete-mode"></a>Tryb kompletny

Rozważmy ten sam scenariusz, tym razem z trybem kompletnym. W trybie kompletnym cała tabela wyjściowa jest odświeżana na każdym wyzwalaczu, dlatego tabela zawiera dane, które nie są tylko od ostatniego uruchomienia wyzwalacza, ale ze wszystkich przebiegów. Aby skopiować dane niezmienione z tabeli wejściowej do tabeli wyników, można użyć trybu kompletnego. W każdym uruchomieniu wyzwalanym, nowe wiersze wynik pojawiają się wraz ze wszystkimi poprzednimi wierszami. W tabeli wyników wyjściowych zostanie zakończone przechowywanie wszystkich danych zebranych od momentu rozpoczęcia zapytania i ostatecznie zabrakło pamięci. Tryb kompletny jest przeznaczony do użycia z zagregowanymi zapytaniami, które podsumowują przychodzące dane w jakiś sposób, więc na każdym wyzwalaczu tabela wyników jest aktualizowana przy użyciu nowego podsumowania.

Przyjęto założenie, że dane zostały już przetworzone przez pięć sekund, a czas na przetworzenie danych na szóstą sekundę. W tabeli wejściowej znajdują się zdarzenia dotyczące czasu 00:01 i czasu 00:03. Celem tego przykładowego zapytania jest przekazanie średniej temperatury urządzenia co pięć sekund. Implementacja tego zapytania ma zastosowanie agregacji, która pobiera wszystkie wartości, które mieszczą się w każdym oknie 5-sekundowym, oblicza średnią temperaturę i tworzy wiersz dla średniej temperatury w tym interwale. Na koniec pierwszego okna 5-sekundowego istnieją dwie krotki: (00:01, 1, 95) i (00:03, 1, 98). Dlatego w przypadku okna 00:00-00:05 agregacja tworzy spójną temperaturę z 96,5 stopniami. W następnym 5-sekundowym oknie istnieje tylko jeden punkt danych w czasie 00:06, więc wynikiem średniej temperatury jest 98 stopni. W czasie 00:10 przy użyciu trybu kompletnego tabela wyników zawiera wiersze dla systemu Windows 00:00-00:05 i 00:05-00:10, ponieważ zapytanie wyprowadza wszystkie zagregowane wiersze, a nie tylko nowe. W związku z tym tabela wyników nadal rośnie po dodaniu nowych okien.

![Tryb wykonywania przesyłania strumieniowego strukturalnego](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Nie wszystkie zapytania korzystające z trybu Complete spowodują, że tabela zostanie powiększona bez zakresu.  Rozważmy w poprzednim przykładzie, który zamiast średniej temperatury według przedziału czasowego, jest średni zamiast identyfikatora urządzenia. Tabela wynik zawiera ustaloną liczbę wierszy (jeden na urządzenie) ze średnią temperaturą dla urządzenia we wszystkich punktach danych odebranych z tego urządzenia. W miarę otrzymywania nowych temperatur tabela wyników zostaje zaktualizowana tak, aby średnia w tabeli była zawsze aktualna.

## <a name="components-of-a-spark-structured-streaming-application"></a>Składniki aplikacji do przetwarzania strumieniowego platformy Spark

Proste przykładowe zapytanie może podsumowywać odczyty temperatury według długiej godziny systemu Windows. W takim przypadku dane są przechowywane w plikach JSON w usłudze Azure Storage (dołączone jako magazyn domyślny dla klastra usługi HDInsight):

```json
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```

Te pliki JSON są przechowywane w `temps` podfolderze kontenera klastra usługi HDInsight.

### <a name="define-the-input-source"></a>Zdefiniuj źródło danych wejściowych

Najpierw należy skonfigurować ramkę danych opisującą źródło i wszystkie ustawienia wymagane przez to źródło. Ten przykład jest pobierany z plików JSON w usłudze Azure Storage i stosuje do nich schemat w czasie odczytywania.

```sql
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

//Cluster-local path to the folder containing the JSON files
val inputPath = "/temps/" 

//Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

//Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
``` 

#### <a name="apply-the-query"></a>Zastosuj zapytanie

Następnie Zastosuj zapytanie, które zawiera żądane operacje dla ramki danych przesyłania strumieniowego. W takim przypadku agregacja grupuje wszystkie wiersze w 1-godzinnym systemie Windows, a następnie oblicza minimalną, średnią i maksymalną temperaturę w tym oknie 1-godzinnym.

```sql
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
```

### <a name="define-the-output-sink"></a>Definiowanie ujścia danych wyjściowych

Następnie zdefiniuj miejsce docelowe dla wierszy, które są dodawane do tabeli wyników w ramach każdego interwału wyzwalacza. Ten przykład po prostu wyprowadza wszystkie wiersze do tabeli znajdującej się w pamięci `temps` , którą można później wykonać przy użyciu SparkSQL. Pełny tryb wyjściowy zapewnia, że wszystkie wiersze dla wszystkich okien są wyprowadzane za każdym razem.

```sql
val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete")
``` 

### <a name="start-the-query"></a>Uruchom zapytanie

Uruchom kwerendę przesyłania strumieniowego i uruchom do momentu otrzymania sygnału zakończenia.

```sql
val query = streamingOutDF.start() 
``` 

### <a name="view-the-results"></a>Wyświetlanie wyników

Gdy zapytanie jest uruchomione, w tym samym SparkSession można uruchomić zapytanie SparkSQL względem tabeli, w `temps` której są przechowywane wyniki zapytania.

```sql
select * from temps
```

To zapytanie daje wyniki podobne do następujących:

| okno |  min (temp) | Średnia (temp) | Max (temp) |
| --- | --- | --- | --- |
|{u'start ": u" 2016-07-26T02:00:00.000 Z ", u'end"... |    95 |    95,231579 | 99 |
|{u'start ": u" 2016-07-26T03:00:00.000 Z ", u'end"...  |95 |   96,023048 | 99 |
|{u'start ": u" 2016-07-26T04:00:00.000 Z ", u'end"...  |95 |   96,797133 | 99 |
|{u'start ": u" 2016-07-26T05:00:00.000 Z ", u'end"...  |95 |   96,984639 | 99 |
|{u'start ": u" 2016-07-26T06:00:00.000 Z ", u'end"...  |95 |   97,014749 | 99 |
|{u'start ": u" 2016-07-26T07:00:00.000 Z ", u'end"...  |95 |   96,980971 | 99 |
|{u'start ": u" 2016-07-26T08:00:00.000 Z ", u'end"...  |95 |   96,965997 | 99 |  

Aby uzyskać szczegółowe informacje o interfejsie API strukturalnego strumienia Spark, wraz ze źródłem danych wejściowych, operacjami i ujściami wyjściowymi, które obsługuje, zobacz [Apache Spark Przewodnik programowania przesyłania strumieniowego](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Tworzenie punktów kontrolnych i zapisów z wyprzedzeniem

W celu zapewnienia odporności i odporności na uszkodzenia, strumieniowanie strukturalne polega na tworzeniu *punktów kontrolnych* , dzięki czemu przetwarzanie strumieni może być kontynuowane, nawet w przypadku awarii węzłów. W usłudze HDInsight platforma Spark tworzy punkty kontrolne w trwałej pamięci masowej, w usłudze Azure Storage lub Data Lake Storage. Te punkty kontrolne przechowują informacje o postępie dotyczące zapytania przesyłania strumieniowego. Ponadto funkcja przesyłania strumieniowego ze strukturą używa *zapisu z wyprzedzeniem* (WAL). W pliku WAL są przechwytywane otrzymane dane, które zostały odebrane, ale jeszcze nie przetworzone przez zapytanie. Jeśli wystąpi awaria, a przetwarzanie zostanie uruchomione ponownie z pliku WAL, wszelkie zdarzenia otrzymane ze źródła nie zostaną utracone.

## <a name="deploying-spark-streaming-applications"></a>Wdrażanie aplikacji do przesyłania strumieniowego Spark

Zazwyczaj tworzysz aplikację do przesyłania strumieniowego Spark lokalnie do pliku JAR, a następnie wdróż ją na platformie Spark w usłudze HDInsight, kopiując plik JAR do magazynu domyślnego dołączonego do klastra usługi HDInsight. Aplikację można uruchomić za pomocą interfejsów API REST usługi [Apache usługi Livy](https://livy.incubator.apache.org/) dostępnych w klastrze przy użyciu operacji post. Treść wpisu zawiera dokument JSON, który zawiera ścieżkę do pliku JAR, nazwę klasy, której Metoda Main definiuje i uruchamia aplikację przesyłania strumieniowego oraz opcjonalnie wymagania dotyczące zasobów zadania (takie jak liczba modułów wykonujących, pamięć i rdzenie) oraz ustawienia konfiguracji wymagane przez kod aplikacji.

![Wdrażanie aplikacji do przesyłania strumieniowego Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stan wszystkich aplikacji można również sprawdzić za pomocą żądania GET w odniesieniu do punktu końcowego usługi LIVY. Na koniec możesz przerwać działającą aplikację, wydając żądanie DELETE względem punktu końcowego usługi LIVY. Aby uzyskać szczegółowe informacje o interfejsie API usługi LIVY, zobacz [zdalne zadania z Apache usługi Livy](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra platformy Apache Spark w usłudze HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Przewodnik programowania przesyłania strumieniowego Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Zdalne uruchamianie zadań Apache Spark za pomocą platformy Apache usługi LIVY](apache-spark-livy-rest-interface.md)
