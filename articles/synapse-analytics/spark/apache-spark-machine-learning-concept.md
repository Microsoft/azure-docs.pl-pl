---
title: Uczenie maszynowe przy użyciu platformy Apache Spark
description: Ten artykuł zawiera omówienie pojęć dotyczących możliwości uczenia maszynowego i analizy danych dostępnych za pomocą Apache Spark w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98116929"
---
# <a name="machine-learning-with-apache-spark"></a>Uczenie maszynowe za pomocą Apache Spark

Apache Spark w usłudze Azure Synapse Analytics umożliwia Uczenie maszynowe przy użyciu danych Big Data, zapewniając możliwość uzyskiwania cennych informacji z dużych ilości danych strukturalnych, niestrukturalnych i szybko przenoszonych. 

Ta sekcja zawiera omówienie i samouczki dotyczące przepływów pracy uczenia maszynowego, w tym analizy danych technologicznych, inżynierów funkcji, szkoleń modeli, oceniania modeli i wdrażania.  

## <a name="synapse-runtime"></a>Środowisko uruchomieniowe Synapse 
Środowisko uruchomieniowe Synapse to nadzorowane środowisko zoptymalizowane pod kątem nauki i uczenia maszynowego. Środowisko uruchomieniowe Synapse udostępnia wiele popularnych bibliotek typu "open source" i domyślnie kompiluje w zestawie SDK Azure Machine Learning. Środowisko uruchomieniowe Synapse zawiera również wiele bibliotek zewnętrznych, w tym PyTorch, Scikit — uczyć się, XGBoost i innych.

Dowiedz się więcej o dostępnych bibliotekach i pokrewnych wersjach, wyświetlając opublikowane [środowisko uruchomieniowe usługi Azure Synapse Analytics](../spark/apache-spark-version-support.md).

## <a name="exploratory-data-analysis"></a>Analiza danych w sposób naukowy
W przypadku korzystania z Apache Spark w usłudze Azure Synapse Analytics istnieją różne wbudowane opcje ułatwiające wizualizację danych, w tym opcje wykresu notesu Synapse, dostęp do popularnych bibliotek typu "open source", takich jak Seaborn i matplotlib, a także integracja z Synapse SQL i Power BI.

Dowiedz się więcej o opcjach wizualizacji danych i analizy danych, wyświetlając artykuł dotyczący [wizualizacji danych przy użyciu notesów Azure Synapse](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Inżynieria cech
Domyślnie środowisko uruchomieniowe Synapse zawiera zestaw bibliotek, które są często używane na potrzeby inżynierii funkcji. W przypadku dużych zestawów danych można używać platformy Spark SQL, MLlib i Koalas na potrzeby inżynierii funkcji. W przypadku mniejszych zestawów danych, biblioteki innych firm, takie jak numpy, Pandas i Scikit — zapewniają również przydatne metody dla tych scenariuszy.

## <a name="train-models"></a>Szkolenie modeli
Istnieje kilka opcji szkolenia modeli uczenia maszynowego przy użyciu platformy Azure Spark w usłudze Azure Synapse Analytics: Apache Spark MLlib, Azure Machine Learning i różne inne biblioteki Open Source. 

Dowiedz się więcej o możliwościach uczenia maszynowego, wyświetlając artykuł dotyczący [uczenia modeli w usłudze Azure Synapse Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>SparkML i MLlib
Możliwości obliczeniowe rozproszone w pamięci platformy Spark sprawiają, że jest dobrym rozwiązaniem dla algorytmów iteracyjnych używanych w obliczeniach uczenia maszynowego i grafu. ```spark.ml``` zapewnia jednolity zestaw interfejsów API wysokiego poziomu, które ułatwiają użytkownikom tworzenie i dostrajanie potoków uczenia maszynowego. Aby dowiedzieć się więcej o programie ```spark.ml``` , można odwiedzić [Przewodnik programowania w Apache Spark ml](https://spark.apache.org/docs/1.2.2/ml-guide.html).

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning automatyczna ML
[Azure Machine Learning zautomatyzowanej ml](../../machine-learning/concept-automated-ml.md) (automatyczne Uczenie maszynowe) pomaga zautomatyzować proces opracowywania modeli uczenia maszynowego. Umożliwia ona analitykom danych, specjalistom i deweloperom tworzenie modeli ML o wysokiej skalowalności, wydajności i produktywności, a jednocześnie zapewnia wysoką jakość modelu. Składniki do uruchamiania Azure Machine Learning zautomatyzowanego zestawu SDK są kompilowane bezpośrednio w środowisku uruchomieniowym Synapse.

### <a name="open-source-libraries"></a>Biblioteki Open Source
Każda pula Apache Spark w usłudze Azure Synapse Analytics zawiera zestaw wstępnie załadowanych i popularnych bibliotek uczenia maszynowego.  Niektóre z odpowiednich bibliotek uczenia maszynowego, które są uwzględnione domyślnie, obejmują:

- [Scikit — Poznaj](https://scikit-learn.org/stable/index.html) jedną z najpopularniejszych bibliotek uczenia maszynowego z jednym węzłem dla klasycznych algorytmów ml. Scikit — informacje obsługują większość nadzorowanych i nienadzorowanych algorytmów uczenia i mogą być również używane do wyszukiwania i analizowania danych.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) to popularna Biblioteka uczenia maszynowego zawierająca zoptymalizowane algorytmy dla drzew decyzyjnych i lasów losowych. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) są zaawansowanymi bibliotekami głębokiego uczenia w języku Python. W puli Apache Spark w usłudze Azure Synapse Analytics można używać tych bibliotek do tworzenia modeli pojedynczych maszyn, ustawiając liczbę modułów wykonujących w puli na zero. Mimo że Apache Spark nie działa w ramach tej konfiguracji, jest to prosty i ekonomiczny sposób tworzenia modeli na jednym komputerze.

## <a name="track-model-development"></a>Śledzenie opracowywania modeli
[MLFlow](https://www.mlflow.org/) to biblioteka typu open source służąca do zarządzania cyklem życia eksperymentów uczenia maszynowego. Śledzenie MLFlow to składnik MLflow, który rejestruje i śledzi wskaźniki uruchamiania szkoleniowe i artefakty modelu. Aby dowiedzieć się więcej na temat korzystania ze śledzenia MLFlow za pomocą usługi Azure Synapse Analytics i Azure Machine Learning, odwiedź ten samouczek dotyczący korzystania z programu [MLFlow](../../machine-learning/how-to-use-mlflow.md).

## <a name="model-scoring"></a>Ocenianie modelu
Ocenianie modelu, lub inferencing, to faza, w której model jest używany do tworzenia prognoz. Dla oceny modelu z SparkML lub MLLib, można użyć natywnych metod Spark do wykonywania inferencing bezpośrednio w ramce Dataframe platformy Spark. W przypadku innych bibliotek typu "open source" i typów modeli można także utworzyć system UDF w celu skalowania w poziomie w dużych zestawach danych. W przypadku mniejszych zestawów danych można również użyć natywnych metod wnioskowania modelu udostępnianych przez bibliotekę.

## <a name="register-and-serve-models"></a>Rejestrowanie i obsługiwanie modeli
Zarejestrowanie modelu umożliwia przechowywanie, wersję i śledzenie metadanych na temat modeli w obszarze roboczym. Po zakończeniu uczenia modelu możesz zarejestrować model w [rejestrze modelu Azure Machine Learning](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere). Po zarejestrowaniu modele ONNX mogą być również używane do [wzbogacania danych](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) przechowywanych w dedykowanych pulach SQL.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć pracę z uczeniem maszynowym w usłudze Azure Synapse Analytics, zapoznaj się z następującymi samouczkami:
- [Analizowanie danych za pomocą notesów usługi Azure Synapse](../spark/apache-spark-data-visualization-tutorial.md)

- [Uczenie modelu uczenia maszynowego za pomocą zautomatyzowanej ML](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Uczenie modelu uczenia maszynowego za pomocą Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
