---
title: Integracja z uczeniem maszynowym Open Source
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać platform do uczenia maszynowego w języku Python, wdrażania i zarządzania kompleksowymi rozwiązaniami uczenia maszynowego w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223078"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integracja z programem Open Source z projektami Azure Machine Learning

Możesz nauczyć i wdrożyć kompleksowy proces uczenia maszynowego w Azure Machine Learning przy użyciu bibliotek i platform usługi "open source" w języku Python.  Użyj narzędzi programistycznych, takich jak notesy Jupyter i Visual Studio Code, aby korzystać z istniejących modeli i skryptów w programie Azure Machine Learning.  

W tym artykule dowiesz się więcej na temat tych bibliotek i platform typu "open source".

## <a name="train-open-source-machine-learning-models"></a>Uczenie modeli uczenia maszynowego Open Source

Proces uczenia maszynowego obejmuje stosowanie algorytmów do danych w celu osiągnięcia zadania lub rozwiązania problemu. W zależności od tego problemu można wybrać różne algorytmy, które najlepiej pasują do zadania i danych. Aby uzyskać więcej informacji na temat różnych gałęzi uczenia maszynowego, zobacz [artykuł głębokie uczenie](./concept-deep-learning-vs-machine-learning.md) i uczenie maszynowe oraz [Arkusz Ściągawka algorytmu uczenia maszynowego](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Zachowanie prywatności danych przy użyciu różnicowej ochrony prywatności

Do uczenia modelu uczenia maszynowego potrzebne są dane. Czasami dane są poufne i ważne jest, aby upewnić się, że dane są bezpieczne i prywatne. Różnicowa Ochrona prywatności jest techniką zachowania poufności informacji w zestawie danych. Aby dowiedzieć się więcej, zapoznaj się z artykułem dotyczącym [zachowania poufności danych](concept-differential-privacy.md). 

Różnicowe zestawy narzędzi do ochrony prywatności typu open source, takie jak [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) , ułatwiają [zachowanie poufności danych](how-to-differential-privacy.md) w rozwiązaniach Azure Machine Learning.

### <a name="classical-machine-learning-scikit-learn"></a>Klasyczny Uczenie maszynowe: scikit — informacje

Do zadań szkoleniowych obejmujących klasyczne algorytmy uczenia maszynowego zadania takie jak Klasyfikacja, klastrowanie i regresja mogą być takie jak Scikit. Aby dowiedzieć się, jak szkolić model klasyfikacji kwiatów, zobacz [artykuł jak uczenie się przy użyciu Scikit](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Sieci neuronowych: PyTorch, TensorFlow, Keras

Algorytmy uczenia maszynowego typu "open source" znane jako sieci neuronowych, podzestaw uczenia maszynowego, są przydatne do uczenia modeli uczenia głębokiego w Azure Machine Learning.

Środowiska uczenia głębokiego "open source" i przewodniki między innymi obejmują:

 *  [PyTorch](https://github.com/pytorch/pytorch): [uczenie modelu klasyfikacji głębokiej uczenia przy użyciu uczenia przeniesienia](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [Rozpoznaj ręczne cyfry przy użyciu TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [Tworzenie sieci neuronowych w celu analizowania obrazów przy użyciu Keras](how-to-train-keras.md)

Uczenie modelu uczenia głębokiego od podstaw często wymaga dużej ilości czasu, danych i zasobów obliczeniowych. Proces szkolenia można wykonać za pomocą uczenia przeniesienia. Nauka transferu to technika, która stosuje wiedzę uzyskaną w wyniku rozwiązywania jednego problemu do innego, ale związanego z nim problemu. Oznacza to, że można zastosować istniejący model do przeznaczenie. Zobacz [artykuł głębokie uczenie i uczenie maszynowe](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) , aby dowiedzieć się więcej o uczeniu przenoszenia.

### <a name="reinforcement-learning-ray-rllib"></a>Nauka wzmacniania: Ray RLLib

Uczenie wzmacniające to sztuczna technika analizy, która pociąga za siebie odpowiednie działania, Stany i nagrody: agenci nauczania wzmacniający poznają zestaw wstępnie zdefiniowanych akcji, które maksymalizują określone nagrody w zależności od bieżącego stanu środowiska. 

Projekt [Ray RLLib](https://github.com/ray-project/ray) ma zestaw funkcji, które umożliwiają wysoką skalowalność w całym procesie szkolenia. Proces iteracyjny to zarówno czasochłonne, jak i mocno obciążające zasoby, ponieważ agenci uczenia wzmocni próbują poznać optymalny sposób osiągnięcia zadania.  RLLib Ray również natywnie obsługuje platformy uczenia głębokiego, takie jak TensorFlow i PyTorch.  

Aby dowiedzieć się, jak używać Ray RLLib z Azure Machine Learning, zobacz [Jak szkolić model uczenia wzmocnienie](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Monitorowanie wydajności modelu: TensorBoard

Szkolenie jednego lub wielu modeli wymaga wizualizacji i inspekcji żądanych metryk, aby upewnić się, że model działa zgodnie z oczekiwaniami. Możesz [użyć TensorBoard w Azure Machine Learning do śledzenia i wizualizacji metryk eksperymentu](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Struktury dla modeli interpretowanych i uczciwych

Systemy uczenia maszynowego są używane w różnych obszarach społeczeństwa, takich jak bankowość, edukacja i opieka medyczna. W związku z tym ważne jest, aby te systemy były odpowiedzialne za przewidywanie i zalecenia, które mają na celu zapobieganie niezamierzonym konsekwencjom.

Platformy typu open source, takie jak [InterpretML](https://github.com/interpretml/interpret/) i Fairlearn ( https://github.com/fairlearn/fairlearn) Pracuj z Azure Machine Learning, aby tworzyć bardziej przejrzyste i sprawiedliwe modele uczenia maszynowego.

Aby uzyskać więcej informacji na temat tworzenia modeli uczciwych i interpretowanych, zobacz następujące artykuły:

- [Możliwości interpretowania modeli w usłudze Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Interpretuj i wyjaśnij modele uczenia maszynowego](how-to-machine-learning-interpretability-aml.md)
- [Wyjaśnij modele AutoML](how-to-machine-learning-interpretability-automl.md)
- [Eliminowanie godziwości modeli uczenia maszynowego](concept-fairness-ml.md)
- [Użyj Azure Machine Learning do sprawiedliwego modelu zasobów](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Wdrożenie modelu

Gdy modele są przeszkolone i gotowe do produkcji, należy wybrać sposób jej wdrażania. Azure Machine Learning oferuje różne cele wdrożenia. Aby uzyskać więcej informacji, zobacz [artykuł gdzie i jak wdrażać](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Standaryzacja formatów modelu z ONNX

Po szkoleniu zawartość modelu, taka jak parametry poznaniowe, jest serializowana i zapisywana w pliku. Każda struktura ma własny format serializacji. Podczas pracy z różnymi strukturami i narzędziami oznacza to, że należy wdrożyć modele zgodnie z wymaganiami struktury. Aby przeprowadzić standaryzację tego procesu, można użyć formatu Open neuronowych Network Exchange (ONNX). ONNX to format "open source" dla sztucznych modeli analizy. ONNX obsługuje współdziałanie między strukturami. Oznacza to, że możesz nauczyć model w jednym z wielu popularnych platform uczenia maszynowego, takich jak PyTorch, przekonwertować go na format ONNX i korzystać z modelu ONNX w różnych strukturach, takich jak ML.NET.

Aby uzyskać więcej informacji na temat ONNX i sposobu korzystania z modeli ONNX, zobacz następujące artykuły:

- [Tworzenie i przyspieszenie modeli ML za pomocą ONNX](concept-onnx.md)
- [Używanie modeli ONNX w aplikacjach .NET](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Pakowanie i wdrażanie modeli jako kontenerów

Technologie kontenerów, takie jak Docker, są jednym ze sposobów wdrażania modeli jako usług sieci Web. Kontenery zapewniają platformę i zasób niezależny od, aby kompilować i organizować możliwe do skompilowania środowiska oprogramowania. Przy użyciu tych podstawowych technologii można korzystać ze [wstępnie skonfigurowanych środowisk](./how-to-use-environments.md), [wstępnie skonfigurowanych obrazów kontenerów](./how-to-deploy-custom-docker-image.md) lub niestandardowych, aby wdrażać modele uczenia maszynowego, takie jak [klastry Kubernetes](./how-to-deploy-azure-kubernetes-service.md?tabs=python). W przypadku przepływów pracy intensywnie korzystających z procesora GPU można używać narzędzi, takich jak serwer wnioskowania NVIDIA Triton, aby [tworzyć przewidywania przy użyciu procesorów GPU](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Bezpieczne wdrożenia z szyfrowaniem homomorphic

Zabezpieczanie wdrożeń jest ważną częścią procesu wdrażania. Aby [wdrożyć zaszyfrowane usługi inferencing](how-to-homomorphic-encryption-seal.md), użyj `encrypted-inference` biblioteki języka Python Open Source. `encrypted inferencing`Pakiet zawiera powiązania oparte na [programie Microsoft Seal](https://github.com/Microsoft/SEAL)i bibliotece szyfrowania homomorphic.

## <a name="machine-learning-operations-mlops"></a>Operacje Machine Learning (MLOps)

Machine Learning Operations (MLOps), często przemyślane jako DevOps for Machine Learning, umożliwiają tworzenie bardziej niewidocznych, odpornych i odtwarzalnych przepływów pracy uczenia maszynowego. Zapoznaj się z [artykułem co to jest MLOps](./concept-model-management-and-deployment.md) , aby dowiedzieć się więcej o usłudze MLOps. 

Korzystając z DevOps praktyk, takich jak ciągłej integracji (CI) i ciągłego wdrażania (CD), Możesz zautomatyzować kompleksowy cykl życia usługi Machine Learning i przechwycić dane dotyczące zarządzania. Możesz zdefiniować potok ciągłej integracji/ciągłego wdrażania [w usłudze GitHub](./how-to-github-actions-machine-learning.md) , aby uruchomić Azure Machine Learning zadania szkoleniowe i wdrożeniowe. 

Przechwytywanie zależności oprogramowania, metryk, metadanych, wersji danych i modelu jest ważną częścią procesu MLOps, aby tworzyć przezroczyste, odtwarzalne i objęte inspekcją potoki. W przypadku tego zadania można [użyć MLFlow w Azure Machine Learning](how-to-use-mlflow.md) oraz podczas [uczenia modeli uczenia maszynowego w Azure Databricks](./how-to-use-mlflow-azure-databricks.md). [Modele MLflow można także wdrożyć jako usługę sieci Web platformy Azure](how-to-deploy-mlflow-models.md). 
