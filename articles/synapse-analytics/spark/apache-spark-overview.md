---
title: Co to jest Apache Spark
description: Ten artykuł zawiera wprowadzenie do Apache Spark usługi Azure Synapse Analytics i różnych scenariuszy, w których można używać platformy Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b31fe5daaa0882dc0927c1340902b20df56eb6b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450436"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Apache Spark w usłudze Azure Synapse Analytics

Apache Spark jest platformą przetwarzania równoległego, która obsługuje przetwarzanie w pamięci w celu zwiększania wydajności aplikacji do analizy danych big data. Apache Spark w usłudze Azure Synapse Analytics jest jednym z implementacji Apache Spark w chmurze firmy Microsoft. Usługa Azure Synapse ułatwia tworzenie i Konfigurowanie puli Apache Spark bezserwerowej na platformie Azure. Pule platformy Spark w usłudze Azure Synapse są zgodne z usługą Azure Storage i magazynem Azure Data Lake generacji 2. Można używać pul platformy Spark do przetwarzania danych przechowywanych na platformie Azure.

![Platforma Spark: ujednolicona struktura](./media/apache-spark-overview/spark-overview.png)

## <a name="what-is-apache-spark"></a>Co to jest Apache Spark

Apache Spark zapewnia elementy podstawowe do przetwarzania klastrów w pamięci. Zadanie Spark może ładować i buforować dane w pamięci, a następnie wielokrotnie wykonywać zapytania względem tych danych. Przetwarzanie w pamięci jest znacznie szybsze niż w przypadku aplikacji opartych na dyskach. Platforma Spark integruje się również z wieloma językami programowania, aby umożliwić manipulowanie rozproszonymi zestawami danych, takimi jak kolekcje lokalne. Nie ma potrzeby, aby wszystkie elementy były obejmowane strukturami operacji mapowania i redukcji.

![Tradycyjny MapReduce a Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Pule Spark w usłudze Azure Synapse oferują w pełni zarządzaną usługę Spark. Zalety tworzenia puli platformy Spark w usłudze Azure Synapse Analytics są wymienione tutaj.

| Cechy | Opis |
| --- | --- |
| Szybkość i wydajność |Wystąpienia platformy Spark zaczynają się około 2 minut przez mniej niż 60 węzłów i około 5 minut przez więcej niż 60 węzłów. Wystąpienie jest zamykane, domyślnie 5 minut od ostatniego wykonanego zadania, chyba że jest ono utrzymywane przez połączenie notesu. |
| Łatwość tworzenia |Nową pulę platformy Spark można utworzyć na platformie Azure Synapse w ciągu kilku minut, korzystając z zestawu SDK Azure Portal, Azure PowerShell lub Synapse Analytics. Zobacz Rozpoczynanie [pracy z pulami platformy Spark w usłudze Azure Synapse Analytics](../quickstart-create-apache-spark-pool-studio.md). |
| Łatwość użycia |Synapse Analytics zawiera niestandardowy Notes pochodzący z [nteract](https://nteract.io/). Można ich używać do interakcyjnego przetwarzania danych i wizualizacji.|
| Interfejsy API REST |Platforma Spark w usłudze Azure Synapse Analytics obejmuje platformę [Apache usługi Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), oparty na interfejsie API REST serwer zadań platformy Spark do zdalnego przesyłania i monitorowania zadań. |
| Obsługa Azure Data Lake Storage generacji 2| Pule Spark w usłudze Azure Synapse mogą Azure Data Lake Storage używać generacji 2 oraz magazynu obiektów BLOB. Aby uzyskać więcej informacji o usłudze Data Lake Storage, zobacz temat [Przegląd usługi Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integracja ze zintegrowanymi środowiskami projektowymi innych firm | Usługa Azure Synapse udostępnia wtyczkę IDE dla [JetBrains "IntelliJ"](https://www.jetbrains.com/idea/) , która jest przydatna do tworzenia i przesyłania aplikacji do puli platformy Spark. |
| Wstępnie załadowane biblioteki Anaconda |Pule platformy Spark w usłudze Azure Synapse są dostarczane z preinstalowanymi bibliotekami Anaconda. Platforma [Anaconda](https://docs.continuum.io/anaconda/) dostarcza prawie 200 bibliotek do uczenia maszynowego, analizy danych, wizualizacji itp. |
| Skalowalność | Apache Spark w pulach usługi Azure Synapse może mieć włączoną funkcję automatycznego skalowania, dzięki czemu pule są skalowane przez dodawanie lub usuwanie węzłów zgodnie z potrzebami. Ponadto pule platformy Spark można wyłączyć bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub Data Lake Storage. |

Pule platformy Spark w usłudze Azure Synapse obejmują następujące składniki, które są domyślnie dostępne w pulach.

- [Spark Core](https://spark.apache.org/docs/2.4.5/). Obejmuje takie składniki jak Spark Core, Spark SQL, GraphX oraz MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract Notes](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Architektura puli platformy Spark

Można łatwo zrozumieć składniki platformy Spark, opisując sposób działania platformy Spark w usłudze Azure Synapse Analytics.

Aplikacje platformy Spark działają jako niezależne zestawy procesów w puli, skoordynowane przez obiekt SparkContext w głównym programie (nazywanym programem sterownika).

SparkContext może połączyć się z menedżerem klastra, który przydziela zasoby między aplikacjami. Menedżer klastra jest [Apache HADOOP przędzą](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Po nawiązaniu połączenia platforma Spark uzyskuje wykonawcy na węzłach w puli, które są procesami, które uruchamiają obliczenia i przechowują dane dla aplikacji. Następnie platforma Spark wysyła kod aplikacji (zdefiniowany przez pliki JAR lub Python przekazywane do obiektu SparkContext) do funkcji wykonawczych. Na koniec obiekt SparkContext wysyła do funkcji wykonawczych zadania do uruchomienia.

SparkContext uruchamia główną funkcję użytkownika i wykonuje różne operacje równoległe na węzłach. Następnie pobiera wyniki operacji. Węzły odczytują i zapisują dane z i do systemu plików. Węzły również buforują dane przekształcone w pamięci jako odporne rozproszone zestawy danych (odporne).

SparkContext nawiązuje połączenie z pulą Spark i jest odpowiedzialny za konwersję aplikacji na ukierunkowany wykres o postaci acykliczne (DAG). Wykres składa się z poszczególnych zadań, które są wykonywane w procesie wykonawcy w węzłach. Poszczególne aplikacje uzyskują własne procesy wykonawcze, które istnieją przez cały czas działania aplikacji i pozwalają uruchamiać zadania w wielu wątkach.

## <a name="apache-spark-in-azure-synapse-analytics-use-cases"></a>Apache Spark w przypadku użycia usługi Azure Synapse Analytics

Pule Spark w usłudze Azure Synapse Analytics umożliwiają korzystanie z następujących kluczowych scenariuszy:

### <a name="data-engineeringdata-preparation"></a>Inżynieria danych/Przygotowywanie danych

Apache Spark zawiera wiele funkcji języka do obsługi przygotowania i przetwarzania dużych ilości danych, dzięki czemu może być bardziej cenny, a następnie zużywane przez inne usługi w ramach usługi Azure Synapse Analytics. Jest to możliwe za pośrednictwem wielu języków (C#, Scala, PySpark, Spark SQL) i dostarczonych bibliotek do przetwarzania i łączności.

### <a name="machine-learning"></a>Usługa Machine Learning

Apache Spark to [MLlib](https://spark.apache.org/mllib/), biblioteka uczenia maszynowego oparta na platformie Spark, która może być używana z puli platformy Spark w usłudze Azure Synapse Analytics. Pule platformy Spark w usłudze Azure Synapse Analytics obejmują również Anaconda, dystrybucję w języku Python z wieloma pakietami do nauki o danych, w tym uczenia maszynowego. W połączeniu z wbudowaną obsługą notesów zyskujesz środowisko do tworzenia aplikacji do uczenia maszynowego.

## <a name="where-do-i-start"></a>Od czego zacząć

Skorzystaj z poniższych artykułów, aby dowiedzieć się więcej na temat Apache Spark w usłudze Azure Synapse Analytics:

- [Szybki Start: Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
- [Szybki Start: Tworzenie notesu Apache Spark](../quickstart-apache-spark-notebook.md)
- [Samouczek: Uczenie maszynowe przy użyciu Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark Oficjalna dokumentacja](https://spark.apache.org/docs/2.4.5/)

> [!NOTE]
> Niektóre oficjalne dokumenty Apache Spark opierają się na użyciu konsoli platformy Spark, ale nie są dostępne w usłudze Azure Synapse Spark, zamiast tego należy użyć notesu lub środowiska IntelliJ

## <a name="next-steps"></a>Następne kroki

W tym omówieniu uzyskasz podstawowe informacje na temat Apache Spark w usłudze Azure Synapse Analytics. Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć pulę platformy Spark w usłudze Azure Synapse Analytics:

- [Tworzenie puli platformy Spark na platformie Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
