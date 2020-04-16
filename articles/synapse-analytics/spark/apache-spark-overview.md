---
title: Co to jest platforma Apache Spark w usłudze Azure Synapse Analytics?
description: Ten artykuł zawiera wprowadzenie do platformy Apache Spark w usłudze Azure Synapse Analytics i różnych scenariuszy, w których można użyć platformy Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4155e419bb03613abad808ca09d84bcb6583291f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423649"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>Co to jest platforma Apache Spark w usłudze Azure Synapse Analytics

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Apache Spark w usłudze Azure Synapse Analytics jest jedną z implementacji platformy Microsoft apache Spark w chmurze. Usługa Azure Synapse ułatwia tworzenie i konfigurowanie puli platformy Spark (wersja zapoznawcza) na platformie Azure. Pule platformy Spark w usłudze Azure Synapse są zgodne z usługą Azure Storage i usługi Azure Data Lake Generation 2 Storage. Dzięki temu można używać pul platformy Azure Spark do przetwarzania danych przechowywanych na platformie Azure.

![Platforma Spark: ujednolicona struktura](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Co to jest Apache Spark

Apache Spark zapewnia wierzchołki dla obliczeń klastra w pamięci. Zadanie platformy Spark może ładować dane do pamięci, buforować je i wielokrotnie przesyłać do nich zapytania. Przetwarzanie w pamięci jest znacznie szybsze niż aplikacje oparte na dysku. Platforma Spark integruje się również z wieloma językami programowania, aby umożliwić manipulowanie rozproszonymi zestawami danych, takimi jak kolekcje lokalne. Nie ma potrzeby, aby wszystkie elementy były obejmowane strukturami operacji mapowania i redukcji.

![Tradycyjna mapawreduce vs. Iskra](./media/apache-spark-overview/map-reduce-vs-spark.png)

Pule platformy Spark w usłudze Azure Synapse oferują w pełni zarządzaną usługę Spark. Korzyści z utworzenia puli platformy Spark w Synapse Analytics są wymienione tutaj.

| Funkcja | Opis |
| --- | --- |
| Szybkość i wydajność |Spark wystąpienia rozpocząć w około 2 minuty dla mniej niż 60 węzłów i około 5 minut dla więcej niż 60 węzłów. Wystąpienie zostanie domyślnie zamknięte 5 minut po wykonaniu ostatniego zadania, chyba że jest utrzymywane przy życiu przez połączenie notesu. |
| Łatwość tworzenia |Nową pulę platformy Spark w usłudze Azure Synapse można utworzyć w ciągu kilku minut przy użyciu witryny Azure Portal, Azure PowerShell lub Synapse Analytics .NET SDK. Zobacz [Wprowadzenie do puli platformy Spark w usłudze Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md). |
| Łatwość obsługi |Synapse Analytics zawiera niestandardowy notes pochodzący z [Nteract](https://nteract.io/). Można ich używać do interakcyjnego przetwarzania danych i wizualizacji.|
| Interfejsy API REST |Spark w Synapse Analytics zawiera [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), serwer zadań Spark oparty na interfejsie API REST do zdalnego przesyłania i monitorowania zadań. |
| Obsługa generacji usługi Azure Data Storage 2| Pule platformy Spark w usłudze Azure Synapse mogą używać usługi Azure Data Lake Storage Generation 2 oraz magazynu obiektów BLOB. Aby uzyskać więcej informacji o usłudze Data Lake Storage, zobacz temat [Przegląd usługi Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integracja ze zintegrowanymi środowiskami projektowymi innych firm | Usługa Azure Synapse udostępnia wtyczkę IDE dla [intellij idea jetbrains,](https://www.jetbrains.com/idea/) która jest przydatna do tworzenia i przesyłania aplikacji do puli platformy Spark. |
| Wstępnie załadowane biblioteki Anaconda |Pule platformy Spark w usłudze Azure Synapse są fabrycznie zainstalowane z fabrycznie zainstalowanymi bibliotekami Anaconda. Platforma [Anaconda](https://docs.continuum.io/anaconda/) dostarcza prawie 200 bibliotek do uczenia maszynowego, analizy danych, wizualizacji itp. |
| Skalowalność | Platforma Apache Spark w pulach usługi Azure Synapse może mieć włączoną funkcję skalowania automatycznego, dzięki czemu pule są skalowane w górę i w dół w razie potrzeby. Ponadto pule platformy Spark można zamknąć bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub usługi Data Lake Storage. |

Pule platformy Spark w usłudze Azure Synapse obejmują następujące składniki, które są domyślnie dostępne w pulach.

- [Spark Core](https://spark.apache.org/docs/latest/). Zawiera Spark Core, Spark SQL, GraphX i MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Notebook Nteract](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Architektura basenu iskry

Łatwo jest zrozumieć składniki platformy Spark, aby zrozumieć, jak działa platforma Spark w usłudze Synapse Analytics.

Aplikacje platformy Spark są uruchamiane jako niezależne zestawy procesów w puli, koordynowane przez obiekt SparkContext w głównym programie (nazywany programem sterowników).

SparkContext można połączyć się z menedżerem klastra, który przydziela zasoby między aplikacjami. Menedżer klastra jest [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Po połączeniu spark uzyskuje executors w węzłach w puli, które są procesy, które uruchamiają obliczenia i przechowywania danych dla aplikacji. Następnie platforma Spark wysyła kod aplikacji (zdefiniowany przez pliki JAR lub Python przekazywane do obiektu SparkContext) do funkcji wykonawczych. Na koniec obiekt SparkContext wysyła do funkcji wykonawczych zadania do uruchomienia.

SparkContext uruchamia główną funkcję użytkownika i wykonuje różne operacje równoległe w węzłach. Następnie pobiera wyniki operacji. Węzły odczytują i zapisują dane z i do systemu plików. Węzły buforuje również przekształcone dane w pamięci jako odporne rozproszone zestawy danych (RDD).

SparkContext łączy się z pulą platformy Spark i jest odpowiedzialny za konwersję aplikacji na wykres skierowany (DAG). Wykres składa się z poszczególnych zadań, które są wykonywane w ramach procesu executor w węzłach. Poszczególne aplikacje uzyskują własne procesy wykonawcze, które istnieją przez cały czas działania aplikacji i pozwalają uruchamiać zadania w wielu wątkach.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark w synapse Analytics używa przypadków

Pule iskier w usłudze Synapse Analytics umożliwiają następujące kluczowe scenariusze:

### <a name="data-engineeringdata-preparation"></a>Inżynieria danych/przygotowanie danych

Apache Spark zawiera wiele funkcji językowych do obsługi przygotowania i przetwarzania dużych ilości danych, dzięki czemu mogą być bardziej wartościowe, a następnie używane przez inne usługi w Synapse Analytics. Jest to włączone za pośrednictwem wielu języków (C#, Scala, PySpark, Spark SQL) i dostarczonych bibliotek do przetwarzania i łączności.

### <a name="machine-learning"></a>Usługa Machine Learning

Apache Spark jest wyposażony [w MLlib](https://spark.apache.org/mllib/), bibliotekę uczenia maszynowego zbudowaną na platformie Spark, której można używać z puli platformy Spark w usłudze Synapse Analytics. Pule platformy Spark w usłudze Synapse Analytics obejmują również Anakondę, dystrybucję języka Python z różnymi pakietami do nauki o danych, w tym uczenia maszynowego. W połączeniu z wbudowaną obsługą notesów masz środowisko do tworzenia aplikacji uczenia maszynowego.

## <a name="where-do-i-start"></a>Od czego zacząć

Skorzystaj z następujących artykułów, aby dowiedzieć się więcej o apache spark w synapse analytics:

- [Szybki start: tworzenie puli iskier w usłudze Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
- [Samouczek: Uczenie maszynowe przy użyciu platformy Spark apache](./apache-spark-machine-learning-mllib-notebook.md)
- [Oficjalna dokumentacja Apache Spark](https://spark.apache.org/docs/latest/)

> [!NOTE]
> Niektóre oficjalne apache Spark dokumentacji opiera się na użyciu konsoli iskry, to nie jest dostępne na platformie Azure Synapse Spark, zamiast tego użyj notebooka lub intellij środowisk

## <a name="next-steps"></a>Następne kroki

W tym przeglądzie można uzyskać podstawową wiedzę o Apache Spark w usłudze Azure Synapse Analytics. Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć pulę platformy Spark w usłudze Azure Synapse Analytics:

- [Tworzenie puli platformy Spark w usłudze Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
