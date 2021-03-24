---
title: Głębokie szczegółowe — analiza zaawansowana — Azure HDInsight
description: Dowiedz się, w jaki sposób usługa Advanced Analytics używa algorytmów do przetwarzania danych Big Data w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0780f66c981f0cebebc1ab327d783954753db965
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866730"
---
# <a name="deep-dive---advanced-analytics"></a>Głębokie szczegółowe — analiza zaawansowana

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Co to jest zaawansowana Analiza usługi HDInsight?

Usługa HDInsight umożliwia uzyskanie cennych informacji z dużych ilości danych strukturalnych, niestrukturalnych i szybko przenoszonych. Zaawansowana analiza to korzystanie z wysoce skalowalnych architektur, modeli uczenia maszynowego i analitycznego oraz inteligentnych pulpitów nawigacyjnych, dzięki którym można uzyskiwać wgląd w szczegółowe dane. Uczenie maszynowe lub *Analiza predykcyjna* używa algorytmów, które identyfikują i uczyją się od relacji w danych, aby wprowadzać prognozy i przetwarzać decyzje.

## <a name="advanced-analytics-process"></a>Proces zaawansowanej analizy

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png" alt-text="Przepływ procesu zaawansowanej analizy" border="false":::

Po zidentyfikowaniu problemu biznesowego i rozpoczęciu zbierania i przetwarzania danych należy utworzyć model, który reprezentuje pytanie, które ma zostać przewidywalna. Model będzie używać co najmniej jednego algorytmu uczenia maszynowego w celu zapewnienia typu przewidywania najlepiej dopasowanego do potrzeb Twojej firmy.  Większość danych powinna być używana do uczenia modelu przy użyciu reszty służącej do testowania lub szacowania.

Po utworzeniu, załadowaniu, przetestowaniu i ocenie modelu, następnym krokiem jest wdrożenie modelu, aby rozpocząć dostarczanie odpowiedzi na pytania. Ostatnim krokiem jest monitorowanie wydajności modelu i dostrojenie go w razie potrzeby.

## <a name="common-types-of-algorithms"></a>Typowe typy algorytmów

Zaawansowane rozwiązania analityczne zapewniają zestaw algorytmów uczenia maszynowego. Poniżej znajduje się podsumowanie kategorii algorytmów i skojarzonych typowych przypadków użycia biznesowego.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png" alt-text="Machine Learning podsumowania kategorii" border="false":::

Wraz z wybraniem algorytmów najlepszego dopasowania należy wziąć pod uwagę, czy należy podać dane do szkoleń. Algorytmy uczenia maszynowego są podzielone w następujący sposób:

* Algorytm nadzorowany musi być szkolony na zestawie danych z etykietą, zanim będzie mógł dostarczyć wyniki
* Algorytm częściowo nadzorowany można rozszerzyć o dodatkowe elementy docelowe za pomocą interakcyjnych zapytań przez Trainer, które nie były dostępne podczas początkowego etapu szkolenia
* Nienadzorowany algorytm nie wymaga danych szkoleniowych
* Algorytm wzmacniania używa agentów oprogramowania do określenia idealnego zachowania w określonym kontekście (często używanym w programie Robotic)

| Kategoria algorytmu| Zastosowanie | Typ uczenia | Algorytmy |
| --- | --- | --- | -- |
| Klasyfikacja | Klasyfikowanie osób lub rzeczy do grup | Nadzorowane | Drzewa decyzyjne, regresja logistyczna, sieci neuronowyche |
| Klastrowanie | Dzielenie zestawu przykładów do jednorodnych grup | Nienadzorowanych | K-oznacza klastrowanie |
| Wykrywanie wzorca | Zidentyfikuj częste skojarzenia danych | Nienadzorowanych | Association rules |
| Regresja | Przewidywanie wyników liczbowych | Nadzorowane | Regresja liniowa, sieci neuronowych |
| Wzmocnienie (Reinforcement) | Określanie optymalnego zachowania dla robotów | Wzmocnienie (Reinforcement) | Symulacje Carlo Monte, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Uczenie maszynowe w usłudze HDInsight

Usługa HDInsight ma kilka opcji uczenia maszynowego dla przepływu pracy zaawansowanej analizy:

* Machine Learning i Apache Spark
* Usługi R i ML
* Azure Machine Learning i Apache Hive
* Apache Spark i uczenie głębokie

### <a name="machine-learning-and-apache-spark"></a>Machine Learning i Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) to oparta na platformie Azure oferta [Apache Spark](https://spark.apache.org/), ujednolicona platforma przetwarzania danych typu open source, która korzysta z przetwarzania w pamięci, aby zwiększyć analizę danych Big Data. Aparat przetwarzania Spark jest zbudowany z myślą o szybkości, łatwości użycia i zaawansowanej analizie. Możliwości obliczeniowe rozproszone w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla algorytmów iteracyjnych używanych w obliczeniach uczenia maszynowego i grafu.

Istnieją trzy skalowalne biblioteki uczenia maszynowego, które doprowadzają możliwości modelowania algorytmów do tego środowiska rozproszonego:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib zawiera oryginalny interfejs API zbudowany w oparciu o platformę Spark odporne.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML to nowszy pakiet, który udostępnia interfejs API wyższego poziomu, który jest oparty na platformie Spark dataframes do konstruowania potoków ml.
* [**MMLSpark**](https://github.com/Azure/mmlspark)  — Biblioteka Machine Learning firmy Microsoft dla Apache Spark (MMLSpark) została zaprojektowana w celu zwiększenia produktywności analityków danych na platformie Spark, zwiększania liczby eksperymentów i korzystania z najnowocześniejszych technik uczenia maszynowego, w tym głębokiego uczenia się na bardzo dużych zestawach danych. Biblioteka MMLSpark upraszcza typowe zadania modelowania do kompilowania modeli w PySpark.

### <a name="r-and-ml-services"></a>Usługi R i ML

W ramach programu HDInsight można utworzyć klaster usługi HDInsight z [usługami ml](../r-server/r-server-overview.md) , gotowy do użycia z olbrzymimi zestawami danych i modelami. Ta nowa funkcja zapewnia analitykom danych i statystyków za pomocą znanego interfejsu języka R, który umożliwia skalowanie na żądanie za pomocą usługi HDInsight, bez konieczności konfigurowania i konserwacji klastra.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning i Apache Hive

[Azure Machine Learning Studio (klasyczny)](https://studio.azureml.net/) oferuje narzędzia do modelowania analizy predykcyjnej i w pełni zarządzanej usługi, których można użyć do wdrożenia modeli predykcyjnych jako gotowych do użycia usług sieci Web. Azure Machine Learning udostępnia narzędzia do tworzenia kompletnych rozwiązań analizy predykcyjnej w chmurze, które umożliwiają szybkie tworzenie, testowanie i operacjonalizować modeli predykcyjnych oraz zarządzanie nimi. Wybierz jedną z wielu bibliotek algorytmów, użyj programu Studio opartego na sieci Web do tworzenia modeli i łatwo Wdrażaj model jako usługę sieci Web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark i uczenie głębokie

[Uczenie głębokie](https://www.microsoft.com/research/group/dltc/) to rozgałęzienie uczenia maszynowego, które korzysta z *głębokiej sieci neuronowych Networks* (DNNs), inspirowane procesami biologicznymi mózgów ludzkich. Wielu badaczy widzą uczenie głębokie jako obietnice dla sztucznej analizy. Przykłady uczenia głębokiego to wypowiadane translatory języka, systemy rozpoznawania obrazów i przyczyny maszyn. Aby pomóc w zapoznaniu się z swoją działalnością w ramach uczenia głębokiego, firma Microsoft opracowała bezpłatną, łatwą w użyciu, [Microsoft Cognitive Toolkitę](https://www.microsoft.com/en-us/cognitive-toolkit/)Open Source. Zestaw narzędzi jest szeroko używany przez różne produkty firmy Microsoft, przedsiębiorstwa na całym świecie, dzięki czemu należy wdrożyć głębokie uczenie na dużą skalę i według uczniów zainteresowanych najnowszymi algorytmami i technikami.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenariusz — obrazy oceny służące do identyfikowania wzorców w rozwoju miast

Zapoznaj się z przykładem zaawansowanego potoku uczenia maszynowego analizy przy użyciu usługi HDInsight.

W tym scenariuszu zobaczysz, jak DNNs produkowany w ramach platformy uczenia głębokiego, Cognitive Toolkit firmy Microsoft (CNTK), może być funkcjonujący do oceniania dużych kolekcji obrazów przechowywanych na koncie platformy Azure Blob Storage przy użyciu PySpark w klastrze usługi HDInsight Spark. Takie podejście jest stosowane do typowego przypadku użycia DNN, klasyfikacji obrazów lotniczych i może służyć do identyfikowania ostatnich wzorców rozwoju w miastach.  Będziesz używać wstępnie nauczonego modelu klasyfikacji obrazu. Model jest wstępnie szkolony w [zestawie danych CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) i został zastosowany do 10 000 obrazów nieodebranych.

W tym scenariuszu w ramach zaawansowanej analizy istnieją trzy kluczowe zadania:

1. Utwórz klaster Azure HDInsight Hadoop przy użyciu dystrybucji Apache Spark 2.1.0.
2. Uruchom skrypt niestandardowy, aby zainstalować Microsoft Cognitive Toolkit na wszystkich węzłach klastra Azure HDInsight Spark.
3. Przekaż wstępnie zbudowany Jupyter Notebook do klastra usługi HDInsight Spark, aby zastosować przeszkolony Microsoft Cognitive Toolkit model uczenia głębokiego do plików na koncie Blob Storage platformy Azure przy użyciu interfejsu API Spark Python (PySpark).

W tym przykładzie zastosowano zestaw obrazu CIFAR-10 skompilowany i rozdystrybuowany przez polecenie Alex Krizhevsky, Vinod Nair i Geoffrey Hinton. Zestaw danych CIFAR-10 zawiera 60 000 32 x 32 kolorowych obrazów należących do 10 wzajemnie wyłącznych klas:

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png" alt-text="Przykładowe obrazy Machine Learning" border="false":::

Aby uzyskać więcej informacji na temat zestawu danych, zobacz Alex Krizhevsky — [uczenie wielu warstw funkcji z niewielkich obrazów](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Zestaw danych został podzielony na partycje do zestawu szkoleniowego 50 000 obrazów i zestawu testowego z 10 000 obrazów. Pierwszy zestaw został użyty do uczenia modelu sieci splotowych (ResNet) o 20 warstwach z systemem za pomocą Microsoft Cognitive Toolkit, wykonując czynności opisane w [tym samouczku](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) w repozytorium Cognitive Toolkit GitHub. Pozostałe obrazy 10 000 zostały użyte do testowania dokładności modelu. Jest to miejsce, w którym nastąpi odtworzenie rozproszonego przetwarzania: zadanie wstępnego przetwarzania i oceniania obrazów jest wysoce działania równoległego. Dzięki zapisanemu przeszkolonemu modelowi używamy:

* PySpark do dystrybuowania obrazów i przeszkolonego modelu do węzłów procesu roboczego klastra.
* Środowisko Python do wstępnego przetwarzania obrazów w każdym węźle klastra usługi HDInsight Spark.
* Cognitive Toolkit załadować modelu i oceny wstępnie przetworzonych obrazów w każdym węźle.
* Jupyter notesy do uruchamiania skryptu PySpark, agregowania wyników i używania [matplotlib](https://matplotlib.org/) do wizualizacji wydajności modelu.

Całe przetwarzanie i ocenianie obrazów 10 000 trwa krócej niż jedna minuta w klastrze z 4 węzłami procesu roboczego. Model dokładnie przewidywalnuje etykiety ~ 9 100 (91%) rastrow. Macierz niepomyle ilustruje najczęstsze błędy klasyfikacji. Na przykład macierz pokazuje, że nieoznakowanie psów jako koty i odwrotnie występuje częściej niż w przypadku innych par etykiet.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png" alt-text="Wykres wyników Machine Learning" border="false":::

### <a name="try-it-out"></a>Wypróbuj!

Postępuj zgodnie z [tym samouczkiem](../spark/apache-spark-microsoft-cognitive-toolkit.md) , aby zaimplementować wdrożenie tego rozwiązania: skonfiguruj klaster usługi HDInsight Spark, zainstaluj Cognitive Toolkit i uruchom Jupyter Notebook, które oceniają 10 000 obrazów CIFAR.

## <a name="next-steps"></a>Następne kroki

Apache Hive i Azure Machine Learning

* [Apache Hive i Azure Machine Learning kompleksowe](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Używanie klastra Azure HDInsight Hadoop w zestawie danych o pojemności 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark i MLLib

* [Uczenie maszynowe za pomocą Apache Spark w usłudze HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark z Machine Learning: używanie Apache Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: Użyj Apache Spark w usłudze HDInsight, aby przewidzieć wyniki inspekcji żywności](../spark/apache-spark-machine-learning-mllib-ipython.md)

Uczenie głębokie, Cognitive Toolkit i inne

* [Maszyna wirtualna platformy Azure o danych](../../machine-learning/data-science-virtual-machine/overview.md)
* [Wprowadzenie do usługi H2O.ai w usłudze Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
