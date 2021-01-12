---
title: Uczenie modeli uczenia maszynowego za pomocą Apache Spark
description: Używanie Apache Spark w usłudze Azure Synapse Analytics do uczenia modeli uczenia maszynowego
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116810"
---
# <a name="train-machine-learning-models"></a>Uczenie modeli uczenia maszynowego
Apache Spark w usłudze Azure Synapse Analytics umożliwia Uczenie maszynowe przy użyciu danych Big Data, zapewniając możliwość uzyskiwania cennych informacji z dużych ilości danych strukturalnych, niestrukturalnych i szybko przenoszonych. Istnieje kilka opcji szkolenia modeli uczenia maszynowego przy użyciu platformy Azure Spark w usłudze Azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning i różne inne biblioteki Open Source. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML i MLlib
Apache Spark w usłudze Azure Synapse Analytics jest jednym z implementacji Apache Spark w chmurze firmy Microsoft. Zapewnia ujednoliconą, równoległą platformę przetwarzania danych, która obsługuje przetwarzanie w pamięci w celu zwiększenia analiz danych Big Data. Aparat przetwarzania Spark jest zbudowany z myślą o szybkości, łatwości użycia i zaawansowanej analizie. Możliwości obliczeniowe rozproszone w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla algorytmów iteracyjnych używanych w obliczeniach uczenia maszynowego i grafu. 

Istnieją dwie skalowalne biblioteki uczenia maszynowego, które wprowadzają możliwości modelowania algorytmów do tego środowiska rozproszonego: MLlib i SparkML. MLlib zawiera oryginalny interfejs API zbudowany w oparciu o odporne. SparkML to nowszy pakiet, który udostępnia interfejs API wyższego poziomu zbudowany na podstawie ramek dataframes do konstruowania potoków ML. SparkML jeszcze nie obsługuje wszystkich funkcji MLlib, ale zastępuje MLlib jako bibliotekę uczenia maszynowego w warstwie Standardowa platformy Spark.

> [!NOTE]
> 
> Więcej informacji na temat tworzenia modelu SparkML można uzyskać, wykonując czynności opisane w tym [samouczku](../spark/apache-spark-azure-machine-learning-tutorial.md).

## <a name="popular-libraries"></a>Popularne biblioteki
Każda pula Apache Spark w usłudze Azure Synapse Analytics zawiera zestaw wstępnie załadowanych i popularnych bibliotek uczenia maszynowego. Te biblioteki zapewniają kod wielokrotnego użytku, który można uwzględnić w programach lub projektach. Niektóre z odpowiednich bibliotek uczenia maszynowego, które są uwzględnione domyślnie, obejmują:
- [Scikit — Poznaj](https://scikit-learn.org/stable/index.html) jedną z najpopularniejszych bibliotek uczenia maszynowego z jednym węzłem dla klasycznych algorytmów ml. Scikit — informacje obsługują większość nadzorowanych i nienadzorowanych algorytmów uczenia i mogą być również używane do wyszukiwania i analizowania danych.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) to popularna Biblioteka uczenia maszynowego zawierająca zoptymalizowane algorytmy dla drzew decyzyjnych i lasów losowych. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) są zaawansowanymi bibliotekami głębokiego uczenia w języku Python. W puli Apache Spark w usłudze Azure Synapse Analytics można używać tych bibliotek do tworzenia modeli pojedynczych maszyn, ustawiając liczbę modułów wykonujących w puli na zero. Mimo że Apache Spark nie działa w ramach tej konfiguracji, jest to prosty i ekonomiczny sposób tworzenia modeli na jednym komputerze.

Aby dowiedzieć się więcej na temat dostępnych bibliotek i pokrewnych wersji, zobacz opublikowane [środowisko uruchomieniowe usługi Azure Synapse Analytics](../spark/apache-spark-version-support.md).

## <a name="mmlspark"></a>MMLSpark
Biblioteka Machine Learning firmy Microsoft dla Apache Spark jest [MMLSpark](https://github.com/Azure/mmlspark). Ta biblioteka została zaprojektowana, aby umożliwić analitykom danych wydajniejsze korzystanie z platformy Spark, zwiększyć częstotliwość eksperymentowania i korzystać z najnowocześniejszych technik uczenia maszynowego, w tym głębokiej uczenia się w dużych zestawach danych. 

Funkcja MMLSpark zapewnia warstwę na podstawie interfejsów API niskiego poziomu SparkML podczas tworzenia skalowalnych modeli ML, takich jak indeksowanie ciągów, Przekształcanie danych w układ oczekiwany przez algorytmy uczenia maszynowego i konstruowanie wektorów funkcji. Biblioteka MMLSpark upraszcza te i inne typowe zadania związane z tworzeniem modeli w PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>Automatyczna ML w Azure Machine Learning 
Azure Machine Learning to środowisko oparte na chmurze, które umożliwia uczenie, wdrażanie, Automatyzowanie i śledzenie modeli uczenia maszynowego oraz zarządzanie nimi. Automatyczna ML w Azure Machine Learning akceptuje dane szkoleniowe i ustawienia konfiguracji i automatycznie wykonuje iterację przez kombinacje różnych metod normalizacji/normalizacji funkcji, modeli i parametrów, aby dotrzeć do najlepszego modelu. 

W przypadku korzystania z zautomatyzowanej ML w ramach usługi Azure Synapse Analytics można wykorzystać głębokiej integracji między różnymi usługami, aby uprościć uwierzytelnianie & modeli. 

> [!NOTE]
> 
> Aby dowiedzieć się więcej na temat tworzenia Azure Machine Learning zautomatyzowanego eksperymentu ML, wykonaj czynności opisane w tym [samouczku](./spark/../apache-spark-azure-machine-learning-tutorial.md).

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Usługa Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) zapewnia możliwości uczenia maszynowego w celu rozwiązywania ogólnych problemów, takich jak analizowanie tekstu emocjonalnej tonacji lub analizowanie obrazów w celu rozpoznawania obiektów lub twarzy. Korzystanie z tych usług nie wymaga specjalnej wiedzy z zakresu uczenia maszynowego ani nauki o danych. Usługa poznawcze udostępnia część lub wszystkie składniki w rozwiązaniu do uczenia maszynowego: dane, algorytmy i model szkolony. Te usługi są przeznaczone do wymagania ogólnej wiedzy na temat danych bez potrzeby korzystania z uczenia maszynowego i analizy danych. Wstępnie przeszkolone Cognitive Services można wykorzystać automatycznie w usłudze Azure Synapse Analytics.

## <a name="next-steps"></a>Następne kroki
Ten artykuł zawiera omówienie różnych opcji uczenia modeli uczenia maszynowego w ramach pul Apache Spark w usłudze Azure Synapse Analytics. Więcej informacji na temat szkoleń modeli można znaleźć w poniższym samouczku:

- Uruchamianie zautomatyzowanych eksperymentów ML przy użyciu Azure Machine Learning i usługi Azure Synapse Analytics: [samouczek zautomatyzowanej ml](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- Uruchamianie eksperymentów SparkML: [Apache SparkML — samouczek](../spark/apache-spark-machine-learning-mllib-notebook.md)
- Wyświetlanie bibliotek domyślnych: [środowisko uruchomieniowe usługi Azure Synapse Analytics](../spark/apache-spark-version-support.md)