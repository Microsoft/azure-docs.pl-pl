---
title: Korzystanie z platformy Apache Pig
titleSuffix: Azure HDInsight
description: Dowiedz się, jak używać świni z Apache Hadoop w usłudze HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944619"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Korzystanie z usługi Apache świni z usługą Apache Hadoop w usłudze HDInsight

Dowiedz się, jak używać oprogramowania [Apache świni](https://pig.apache.org/) z usługą HDInsight.

Apache świni to platforma służąca do tworzenia programów dla Apache Hadoop przy użyciu języka proceduralnego znanego jako *świnie*. Świnie są alternatywą dla języka Java do tworzenia rozwiązań *MapReduce* i są dołączone do usługi Azure HDInsight. Skorzystaj z poniższej tabeli, aby poznać różne sposoby używania świni z usługą HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Dlaczego warto używać oprogramowania Apache świni

Jednym z wyzwań związanych z przetwarzaniem danych przy użyciu MapReduce w usłudze Hadoop jest implementowanie logiki przetwarzania przy użyciu tylko mapy i funkcji ograniczania. W przypadku przetwarzania złożonego często trzeba przerwać przetwarzanie w wielu operacjach MapReduce, które są połączone ze sobą, aby osiągnąć żądany wynik.

Świnie umożliwiają definiowanie przetwarzania w postaci serii transformacji, w których dane są przetwarzane przez program w celu wygenerowania żądanych danych wyjściowych.

Język łaciński (wieprzowina) pozwala na opisywanie przepływu danych z nieprzetworzonego wejścia, przez jedno lub więcej transformacji, w celu wygenerowania żądanych danych wyjściowych. Programy z wieprzowiną są zgodne z tym ogólnym wzorcem:

* **Ładowanie**: odczytywanie danych do manipulacji z systemu plików.

* **Przekształcanie**: manipulowanie danymi.

* **Zrzut lub przechowywanie**: dane wyjściowe na ekranie lub przechowywane do przetworzenia.

### <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Wieprzowina również obsługuje funkcje zdefiniowane przez użytkownika (UDF), które umożliwiają wywoływanie zewnętrznych składników implementujących logikę, które są trudne do modelowania w przypadku trzody chlewnej.

Aby uzyskać więcej informacji na temat wieprzowiny, zobacz artykuł dotyczący odniesienia w postaci [wieprzowiny](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) — Podręcznik 1 i [Dokumentacja dotycząca tuszu łacińskiego nr 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Przykładowe dane

Usługa HDInsight oferuje różne przykładowe zestawy danych, które są przechowywane w `/example/data` `/HdiSamples` katalogach i. Te katalogi znajdują się w domyślnym magazynie klastra. Przykładowa świnia w tym dokumencie używa pliku *Log4J* z `/example/data/sample.log` .

Każdy dziennik wewnątrz pliku składa się z wierszy pól, które zawierają `[LOG LEVEL]` pole, aby wyświetlić typ i ważność, na przykład:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

W poprzednim przykładzie poziom dziennika jest błąd.

> [!NOTE]  
> Możesz również wygenerować plik Log4J za pomocą narzędzia rejestrowania usługi [Apache Log4J](https://en.wikipedia.org/wiki/Log4j) , a następnie przekazać ten plik do obiektu BLOB. Aby uzyskać instrukcje [, zobacz Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md) . Aby uzyskać więcej informacji na temat sposobu używania obiektów BLOB w usłudze Azure Storage w usłudze HDInsight, zobacz [Korzystanie z usługi azure BLOB Storage w usłudze HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Przykładowe zadanie

Następujące zadanie łacińskie (świnie) ładuje `sample.log` plik z domyślnego magazynu dla klastra usługi HDInsight. Następnie wykonuje serię transformacji, które powodują, ile razy każdy poziom dziennika miał miejsce w danych wejściowych. Wyniki są zapisywane w strumieniu STDOUT.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

Na poniższej ilustracji przedstawiono podsumowanie poszczególnych transformacji danych.

![Graficzna reprezentacja przekształceń][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Uruchamianie zadania łacińskie

Usługa HDInsight może uruchamiać zadania łacińskie z tusz przy użyciu różnych metod. Skorzystaj z poniższej tabeli, aby zdecydować, która metoda jest dla Ciebie odpowiednia, a następnie postępuj zgodnie z linkiem do przewodnika.

## <a name="pig-and-sql-server-integration-services"></a>Świnie i SQL Server Integration Services

Do uruchomienia zadania świni można użyć SQL Server Integration Services (SSIS). Pakiet Feature Pack dla usług SSIS oferuje następujące składniki, które pracują z zadaniami dotyczącymi trzody chlewnej w usłudze HDInsight.

* [Zadanie usługi Azure HDInsight świnie][pigtask]

* [Menedżer połączeń subskrypcji platformy Azure][connectionmanager]

Więcej informacji na temat pakietu Azure Feature Pack dla usług SSIS [znajdziesz tutaj][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać świni z usługą HDInsight, Skorzystaj z poniższych linków, aby poznać inne sposoby pracy z usługą Azure HDInsight.

* [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](./hadoop/hdinsight-use-hive.md)
* [Korzystanie z programu Apache Sqoop z usługą HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif