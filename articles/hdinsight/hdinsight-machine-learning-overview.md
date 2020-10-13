---
title: Omówienie uczenia maszynowego — Azure HDInsight
description: Omówienie opcji uczenia maszynowego dla danych Big Data dla klastrów w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: a3c050b414e322f08fd45a569a22cea2db591b99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91302693"
---
# <a name="machine-learning-on-hdinsight"></a>Uczenie maszynowe w usłudze HDInsight

Usługa HDInsight umożliwia Uczenie maszynowe przy użyciu danych Big Data, co zapewnia możliwość uzyskania cennych informacji o dużych ilościach (petabajtów, a nawet eksabajtowej) ze strukturą, bez struktury i szybko przenoszone dane. Istnieje kilka opcji uczenia maszynowego w usłudze HDInsight: SparkML i Apache Spark MLlib, R, Apache Hive i Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML i MLlib

[HDInsight Spark](spark/apache-spark-overview.md) to oparta na platformie Azure oferta [Apache Spark](https://spark.apache.org/), ujednolicona, równoległa platforma przetwarzania danych, która obsługuje przetwarzanie w pamięci w celu zwiększenia analiz danych Big Data. Aparat przetwarzania Spark jest zbudowany z myślą o szybkości, łatwości użycia i zaawansowanej analizie. Możliwości obliczeniowe rozproszone w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla algorytmów iteracyjnych używanych w obliczeniach uczenia maszynowego i grafu. Istnieją dwie skalowalne biblioteki uczenia maszynowego, które wprowadzają możliwości modelowania algorytmów do tego środowiska rozproszonego: MLlib i SparkML. MLlib zawiera oryginalny interfejs API zbudowany w oparciu o odporne. SparkML to nowszy pakiet, który udostępnia interfejs API wyższego poziomu zbudowany na podstawie ramek dataframes do konstruowania potoków ML. SparkML jeszcze nie obsługuje wszystkich funkcji MLlib, ale zastępuje MLlib jako bibliotekę uczenia maszynowego w warstwie Standardowa platformy Spark.

Biblioteka Machine Learning firmy Microsoft dla Apache Spark jest [MMLSpark](https://github.com/Azure/mmlspark). Ta biblioteka została zaprojektowana w celu zwiększenia produktywności analityków danych na platformie Spark, zwiększania liczby eksperymentów i korzystania z najnowocześniejszych technik uczenia maszynowego, w tym uczenie głębokie, na bardzo dużych zestawach danych. Funkcja MMLSpark zapewnia warstwę na podstawie interfejsów API niskiego poziomu SparkML podczas tworzenia skalowalnych modeli ML, takich jak indeksowanie ciągów, Przekształcanie danych w układ oczekiwany przez algorytmy uczenia maszynowego i konstruowanie wektorów funkcji. Biblioteka MMLSpark upraszcza te i inne typowe zadania związane z tworzeniem modeli w PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) to obecnie najpopularniejszy język programowania statystycznego na świecie. Jest to narzędzie do wizualizacji danych typu open source ze społecznością ponad 2 500 000 użytkowników i rośnie. Dzięki rozznaniom bazy użytkowników i ponad 8 000 pakietów z wkładem R jest prawdopodobnie wybór dla wielu firm, które wymagają uczenia maszynowego. Klaster usługi HDInsight można utworzyć za pomocą usług ML, gotowy do użycia z olbrzymimi zestawami danych i modelami. Ta funkcja zapewnia analitykom danych i statystyków za pomocą znanego interfejsu języka R, który umożliwia skalowanie na żądanie za pomocą usługi HDInsight, bez konieczności konfigurowania i konserwacji klastra.

![Szkolenie na potrzeby przewidywania przy użyciu oprogramowania R Server](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R.  Skrypty języka R można także uruchamiać w węzłach klastra przy użyciu mapy usługi Hadoop w usłudze skalowanie w celu zmniejszenia liczby lub w przypadku wystąpień obliczeniowych platformy Spark.

Za pomocą usługi ML w usłudze HDInsight z platformą Spark można zrównoleglanie szkolenie w węzłach klastra przy użyciu kontekstu obliczeniowego platformy Spark. Skrypty języka R można uruchamiać bezpośrednio w węźle brzegowym, w razie konieczności przy użyciu wszystkich dostępnych rdzeni. Alternatywnie można uruchomić kod z węzła brzegowego, aby rozpocząć przetwarzanie, które jest dystrybuowane między wszystkimi węzłami w klastrze. Usługi ML w usłudze HDInsight z platformą Spark umożliwiają również korzystanie z funkcji przekształcają z pakietów języka R Open Source w razie potrzeby.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning i Apache Hive

Azure Machine Learning udostępnia narzędzia do modelowania analizy predykcyjnej i w pełni zarządzanej usługi, których można użyć do wdrożenia modeli predykcyjnych jako gotowych do użycia usług sieci Web. Azure Machine Learning to kompletne rozwiązanie do analizy predykcyjnej w chmurze, za pomocą którego można tworzyć, testować i operacjonalizować modele predykcyjne oraz zarządzać nimi. Wybierz jedną z wielu bibliotek algorytmów, użyj programu Studio opartego na sieci Web do tworzenia modeli i łatwo Wdrażaj model jako usługę sieci Web.

![Microsoft Azure omówienie uczenia maszynowego](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Tworzenie funkcji dla danych w klastrze usługi HDInsight Hadoop przy użyciu [zapytań programu Hive](../machine-learning/team-data-science-process/create-features-hive.md). *Inżynieria funkcji* próbuje zwiększyć predykcyjne algorytmy uczenia, tworząc funkcje z danych pierwotnych, które ułatwiają proces uczenia się. Za pomocą [modułu Importuj dane](../machine-learning/classic/import-data.md)można uruchamiać zapytania HiveQL z Azure Machine Learning Studio (klasycznego) i uzyskiwać dostęp do danych przetworzonych w programie Hive i przechowywanych w usłudze BLOB Storage.

## <a name="microsoft-cognitive-toolkit"></a>Zestaw narzędzi usług Microsoft Cognitive

[Uczenie głębokie](https://www.microsoft.com/en-us/research/group/dltc/) to rozgałęzienie uczenia maszynowego, które korzysta z sieci neuronowych, inspirowane procesami biologicznymi mózgów. Wielu badaczy widzą uczenie głębokie jako obietnicowe podejście do ulepszania sztucznej analizy. Przykłady uczenia głębokiego to wypowiadane translatory języka, systemy rozpoznawania obrazów i przyczyny maszyn.

Aby pomóc w zapoznaniu się z swoją działalnością w ramach uczenia głębokiego, firma Microsoft opracowała bezpłatną, łatwą w użyciu, [Microsoft Cognitive Toolkitę](https://www.microsoft.com/en-us/cognitive-toolkit/)Open Source. Ten zestaw narzędzi jest używany przez różne produkty firmy Microsoft, przedsiębiorstwa na całym świecie, z potrzebą wdrożenia głębokiej uczenia na dużą skalę i studentów zainteresowanych najnowszymi algorytmami i technikami.

## <a name="see-also"></a>Zobacz też

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generowanie zaleceń dotyczących filmów za pomocą platformy Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive i Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive i Azure Machine Learning kompleksowe](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Uczenie maszynowe za pomocą Apache Spark w usłudze HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Zasoby dotyczące głębokiego uczenia

* [Używanie Microsoft Cognitive Toolkit modelu uczenia głębokiego z klastrem Azure HDInsight Spark](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Uczenie głębokie i platformy AI na Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
