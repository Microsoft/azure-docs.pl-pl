---
title: Co to jest szkolenie rozproszone?
titleSuffix: Azure Machine Learning
description: Więcej informacji na temat szkoleń rozproszonych i sposobu ich obsługi przez Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 62edee6a882191551ce2409646ea8b617576c059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651157"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Uczenie rozproszone z Azure Machine Learning

Ten artykuł zawiera informacje na temat szkoleń rozproszonych i sposobu, w jaki usługa Azure Machine Learning ją obsługuje na potrzeby modeli uczenia głębokiego. 

W przypadku szkoleń rozproszonych obciążenie związane z modelem są dzielone i współużytkowane przez wiele procesorów mini, nazywanych węzłami procesu roboczego. Te węzły procesu roboczego działają równolegle w celu przyspieszenia szkolenia modeli. Szkolenie rozproszone może być używane w przypadku tradycyjnych modeli ML, ale jest lepiej dostosowane do zadań obliczeniowych i intensywnie korzystających z czasu, takich jak [głębokie uczenie](concept-deep-learning-vs-machine-learning.md) się do szkoleń głębokiej sieci neuronowych. 

## <a name="deep-learning-and-distributed-training"></a>Uczenie głębokie i szkolenia rozproszone 

Istnieją dwa główne typy szkoleń rozproszonych: [równoległości danych](#data-parallelism) i [równoległości modeli](#model-parallelism). W przypadku szkoleń rozproszonych dotyczących modeli uczenia głębokiego [zestaw SDK Azure Machine Learning w języku Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) obsługuje integracje z popularnymi strukturami, PyTorch i TensorFlow. Obie platformy wykorzystują równoległość danych do szkolenia rozproszonego i mogą wykorzystać [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) do optymalizacji szybkości obliczeniowej. 

* [Uczenie rozproszone z PyTorch](how-to-train-pytorch.md#distributed-training)

* [Uczenie rozproszone z TensorFlow](how-to-train-tensorflow.md#distributed-training)

W przypadku modeli ML, które nie wymagają szkolenia rozproszonego, zobacz temat [uczenie modeli z Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) , aby poznać różne sposoby uczenia modeli przy użyciu zestawu SDK języka Python.

## <a name="data-parallelism"></a>Równoległość danych

Równoległość danych to najłatwiejsza implementacja dwóch rozwiązań szkolenia rozproszonego i jest wystarczająca dla większości przypadków użycia.

W tym podejściu dane są podzielone na partycje, gdzie liczba partycji jest równa całkowitej liczbie dostępnych węzłów w klastrze obliczeniowym. Model jest kopiowany w każdym z tych węzłów procesu roboczego, a każdy proces roboczy działa na własnym podzbiorze danych. Należy pamiętać, że każdy węzeł musi mieć pojemność do obsługi modelu, który jest szkolony, czyli że model ma całkowicie pasować do każdego węzła. Na poniższym diagramie przedstawiono wizualizację tego podejścia.

![Równoległość danych — diagram koncepcji](./media/concept-distributed-training/distributed-training.svg)

Każdy węzeł niezależnie oblicza błędy między przewidywaniami dla swoich przykładów szkoleniowych i oznaczonymi wynikami. Z kolei każdy węzeł aktualizuje model na podstawie błędów i musi przekazać wszystkie zmiany do innych węzłów w celu zaktualizowania odpowiednich modeli. Oznacza to, że węzły procesu roboczego muszą synchronizować parametry modelu lub gradienty na końcu obliczeń wsadowych, aby upewnić się, że są szkoleń spójnych modeli. 

## <a name="model-parallelism"></a>Równoległość modelu

W modelu równoległości, znanej również jako równoległość sieci, model jest podzielony na różne części, które mogą być uruchamiane współbieżnie w różnych węzłach, a każda z nich będzie działać na tych samych danych. Skalowalność tej metody zależy od stopnia zadania przetwarzanie równoległe algorytmu i jest bardziej skomplikowana niż implementacja równoległości danych. 

W modelu równoległości węzły procesu roboczego muszą synchronizować parametry udostępnione tylko raz dla każdego kroku do przodu lub do tyłu. Ponadto większe modele nie są problemem, ponieważ każdy węzeł działa w podsekcji modelu na tych samych danych szkoleniowych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [używać obiektów docelowych obliczeń do uczenia modeli](how-to-set-up-training-targets.md) przy użyciu zestawu SDK języka Python.
* Aby zapoznać się z przykładem technicznym, zobacz [scenariusz architektury referencyjnej](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Uczenie modeli ml z TensorFlow](how-to-train-tensorflow.md).
* [Uczenie modeli ml z PyTorch](how-to-train-pytorch.md). 