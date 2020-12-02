---
title: Usługi Cognitive Services dla danych big data
description: Dowiedz się, jak korzystać z usługi Azure Cognitive Services w dużych zestawach danych przy użyciu języków Python, Java i Scala. Za pomocą Cognitive Services dla danych Big Data można osadzić ciągłe ulepszanie, inteligentnych modeli bezpośrednio do Apache Spark &trade; i obliczeń SQL.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461619"
---
# <a name="azure-cognitive-services-for-big-data"></a>Cognitive Services platformy Azure dla danych Big Data

![Cognitive Services platformy Azure dla danych Big Data](media/cognitive-services-big-data-overview.svg)

Cognitive Services platformy Azure dla danych Big Data umożliwia użytkownikom kanał terabajtów danych za pośrednictwem Cognitive Services przy użyciu [Apache Spark &trade; ](/dotnet/spark/what-is-spark). Za pomocą Cognitive Services dla danych Big Data można łatwo tworzyć inteligentne aplikacje o dużej skali przy użyciu dowolnego magazynu danych.

Za pomocą Cognitive Services dla danych Big Data można osadzić ciągłe ulepszanie, inteligentnych modeli bezpośrednio do Apache Spark &trade; i obliczeń SQL. Te narzędzia wykorzystają deweloperów z szczegółów sieci niskiego poziomu, dzięki czemu mogą skupić się na tworzeniu inteligentnych, rozproszonych aplikacji.

## <a name="features-and-benefits"></a>Funkcje i korzyści

Cognitive Services dla danych Big Data mogą korzystać z usług z dowolnego regionu na świecie oraz do [Cognitive Services kontenerów](../cognitive-services-container-support.md). Kontenery obsługują małe lub bezpłatne wdrożenia łączności z odpowiedziami o bardzo małych opóźnieniach. Cognitive Services kontenerów można uruchamiać lokalnie, bezpośrednio w węzłach procesu roboczego klastra Spark lub na zewnętrznym serwerze Orchestrator, takim jak Kubernetes.

## <a name="supported-services"></a>Obsługiwane usługi

[Cognitive Services](../index.yml), do których można uzyskać dostęp za pomocą interfejsów API i zestawów SDK, ułatwiają deweloperom tworzenie inteligentnych aplikacji bez posiadania umiejętności AI lub nauki o danych. Dzięki Cognitive Services można sprawić, aby aplikacje widziały, słyszeć, mówić, zrozumieć i powód. Aby można było korzystać z Cognitive Services, aplikacja musi wysyłać dane do usługi za pośrednictwem sieci. Po odebraniu usługa wysyła inteligentną odpowiedź w programie Return. Następujące usługi są dostępne dla obciążeń danych Big Data:

### <a name="vision"></a>Obraz

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Przetwarzanie obrazów](../computer-vision/index.yml "Przetwarzanie obrazów")| Usługa przetwarzanie obrazów zapewnia dostęp do zaawansowanych algorytmów przetwarzania obrazów i zwracania informacji. |
|[Rozpoznawanie twarzy](../face/index.yml "Rozpoznawanie twarzy")| Usługa kroju obsługi zapewnia dostęp do zaawansowanych algorytmów, co umożliwia wykrywanie i rozpoznawanie atrybutów. |

### <a name="speech"></a>Mowa

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Usługa rozpoznawania mowy](../speech-service/index.yml "Usługa rozpoznawania mowy")|Usługa mowy zapewnia dostęp do funkcji, takich jak rozpoznawanie mowy, synteza mowy, Tłumaczenie mowy oraz weryfikacja i identyfikacja prelegenta.|

### <a name="decision"></a>Decyzja

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Narzędzie do wykrywania anomalii](../anomaly-detector/index.yml "Narzędzie do wykrywania anomalii") | Usługa wykrywania anomalii (wersja zapoznawcza) umożliwia monitorowanie i wykrywanie nieprawidłowych danych szeregów czasowych.|

### <a name="language"></a>Język

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Analiza tekstu](../text-analytics/index.yml "Analiza tekstu")| Usługa analiza tekstu zapewnia przetwarzanie języka naturalnego w tekście nieprzetworzonym w celu analizy tonacji, wyodrębniania kluczowych fraz i wykrywania języka.|

### <a name="search"></a>Wyszukiwanie

|Nazwa usługi|Opis usługi|
|:-----------|:------------------|
|[Wyszukiwanie obrazów w usłudze Bing](/azure/cognitive-services/bing-image-search "Wyszukiwanie obrazów w usłudze Bing")|Usługa wyszukiwanie obrazów Bing zwraca wyświetlanie obrazów określonych jako istotne dla kwerendy użytkownika.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Obsługiwane języki programowania dla Cognitive Services danych Big Data

Cognitive Services dla danych Big Data są oparte na Apache Spark. Apache Spark to dystrybuowana Biblioteka obliczeń obsługująca język Java, Scala, Python, R i wiele innych języków. Te języki są obecnie obsługiwane.

### <a name="python"></a>Python

Udostępniamy interfejs API PySpark w `mmlspark.cognitive` przestrzeni nazw [Microsoft ML dla Apache Spark](https://aka.ms/spark). Aby uzyskać więcej informacji, zobacz [interfejs API deweloperów języka Python](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Aby zapoznać się z przykładami użycia, zobacz [przykłady języka Python](samples-python.md).

### <a name="scala-and-java"></a>Scala i Java

Udostępniamy interfejs API Spark oparty na technologii Scala i Java w `com.microsoft.ml.spark.cognitive` przestrzeni nazw [Microsoft ML dla Apache Spark](https://aka.ms/spark). Aby uzyskać więcej informacji, zobacz [Scala Developer API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Aby zapoznać się z przykładami użycia, zobacz [przykłady Scala](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Obsługiwane platformy i łączniki

Cognitive Services dla danych Big Data wymaga Apache Spark. Istnieje kilka Apache Spark platform, które obsługują Cognitive Services do danych Big Data.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) to platforma analizy oparta na usłudze Apache Spark i zoptymalizowana pod kątem platformy usług w chmurze Microsoft Azure. Udostępnia on konfigurację jednego kliknięcia, usprawnione przepływy pracy i interaktywny obszar roboczy, który obsługuje współpracę między analitykami danych, inżynierami danych i specjalistami biznesowymi.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Usługa Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) to magazyn danych przedsiębiorstwa, który używa ogromnego przetwarzania równoległego. Dzięki Synapse Analytics można szybko uruchamiać złożone zapytania na petabajtów danych. Usługa Azure Synapse Analytics udostępnia zarządzane pule platformy Spark umożliwiające uruchamianie zadań platformy Spark za pomocą intuicyjnego interfejsu Jupyter Notebook.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Usługa Azure Kubernetes Service (AKS)](../../aks/index.yml) organizuje kontenery platformy Docker i aplikacje rozproszone w ogromnej skali. AKS to zarządzana oferta Kubernetes, która upraszcza korzystanie z Kubernetes na platformie Azure. Kubernetes może umożliwić precyzyjne sterowanie skalowalnością, opóźnieniem i siecią usługi poznawczej. Zaleca się jednak używanie Azure Databricks lub analizy Synapse Azure, jeśli nie masz doświadczenia z Apache Spark.

### <a name="data-connectors"></a>Łączniki danych

Gdy masz klaster Spark, następnym krokiem jest nawiązanie połączenia z danymi. Apache Spark ma szeroką kolekcję łączników baz danych. Te łączniki umożliwiają aplikacjom współpracują z dużymi zestawami danych bez znaczenia, gdzie są przechowywane. Aby uzyskać więcej informacji na temat obsługiwanych baz danych i łączników, zapoznaj się z [listą obsługiwanych elementów DataSources dla Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Pojęcia

### <a name="spark"></a>platforma Spark

[Apache Spark &trade; ](http://spark.apache.org/) jest to ujednolicony aparat analityczny do przetwarzania danych na dużą skalę. Jego architektura przetwarzania równoległego zwiększa wydajność danych Big Data i aplikacji analitycznych. Platforma Spark może działać jako system przetwarzania wsadowego i przesyłania strumieniowego bez konieczności zmieniania kodu podstawowego aplikacji.

Podstawą platformy Spark jest ramka Dataframe: tabelaryczna kolekcja danych dystrybuowana między węzłami procesu roboczego Apache Spark. Ramka Dataframe jest taka sama jak tabela w relacyjnej bazie danych lub ramka danych w języku R/Python, ale z nieograniczoną skalą. Ramki danych mogą być zbudowane z wielu źródeł, takich jak pliki z danymi strukturalnymi, tabele w programie Hive lub zewnętrzne bazy danych. Gdy dane są w ramce Dataframe, możesz:

   - Obliczenia w stylu SQL, takie jak sprzężenie i tabele filtrów.
   - Zastosuj funkcje do dużych zestawów danych przy użyciu równoległości stylu MapReduce.
   - Zastosuj rozproszone Machine Learning przy użyciu programu Microsoft Machine Learning do Apache Spark.
   - Użyj Cognitive Services danych Big Data, aby wzbogacić dane za pomocą gotowych do użycia inteligentnych usług.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning dla Apache Spark (MMLSpark)

[Microsoft Machine Learning for Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) to "open source", rozproszona Biblioteka uczenia maszynowego (ml) oparta na Apache Spark. Ten pakiet zawiera Cognitive Services danych Big Data. Ponadto MMLSpark zawiera kilka innych narzędzi ML do Apache Spark, takich jak LightGBM, Vowpal Wabbit, OpenCV, LIMONowe i inne. Za pomocą MMLSpark można tworzyć zaawansowane modele predykcyjne i analityczne z dowolnego źródła danych Spark.

### <a name="http-on-spark"></a>Protokół HTTP na platformie Spark

Cognitive Services dla danych Big Data to przykład integracji inteligentnych usług sieci Web z danymi Big Data. Usługi sieci Web zużywają wiele aplikacji na całym świecie i większość usług komunikują się za pośrednictwem protokołu HTTP (Hypertext Transfer Protocol). Aby korzystać z *dowolnych* usług sieci Web w dużych skalach, udostępniamy protokół http na platformie Spark. Za pomocą protokołu HTTP w systemie Spark można przekazać terabajty danych za pośrednictwem dowolnej usługi sieci Web. Korzystając z tej technologii, można Cognitive Services do obsługi danych Big Data.

## <a name="developer-samples"></a>Przykłady dla deweloperów

- [Przepis: konserwacja predykcyjna](recipes/anomaly-detection.md)
- [Przepis: inteligentne Eksploracja](recipes/art-explorer.md)

## <a name="blog-posts"></a>Wpisy na blogach

- [Dowiedz się więcej o tym, jak Cognitive Services pracy na Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Zapisywanie Leopards śniegu z uczeniem głębokim i przetwarzanie obrazów na platformie Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Podcast badawcze firmy Microsoft: MMLSpark, dodanie AI w dobrej drodze ze znacznikiem Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Oficjalne oficjalny dokument: inteligentne mikrousługi na dużą skalę](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Seminaria internetowe i wideo

- [Cognitive Services platformy Azure na platformie Spark: klastry z osadzonymi usługami inteligentnymi](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Prezentację szczytu platformy Spark: skalowalne AI dla dobra](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services danych Big Data w Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Porozmawiaj z inteligentnymi mikrousługami skalowania](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie z Cognitive Services dla danych Big Data](getting-started.md)
- [Proste przykłady języka Python](samples-python.md)
- [Proste przykłady Scala](samples-scala.md)