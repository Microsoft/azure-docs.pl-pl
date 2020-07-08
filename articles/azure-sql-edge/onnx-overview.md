---
title: Uczenie maszynowe i AI z ONNX w usłudze Azure SQL Edge (wersja zapoznawcza)
description: Uczenie maszynowe w usłudze Azure SQL Edge (wersja zapoznawcza) obsługuje modele w formacie Open neuronowych Network Exchange (ONNX). ONNX to otwarty format, którego można użyć do wymiany modeli między różnymi strukturami i narzędziami uczenia maszynowego.
keywords: Wdróż program SQL Edge
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 2db6c728ac35c6fc2f1fee1a602725371e448104
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368060"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Uczenie maszynowe i AI z ONNX w usłudze SQL Edge (wersja zapoznawcza)

Uczenie maszynowe w usłudze Azure SQL Edge (wersja zapoznawcza) obsługuje modele w formacie [Open neuronowych Network Exchange (ONNX)](https://onnx.ai/) . ONNX to otwarty format, którego można użyć do wymiany modeli między różnymi [strukturami i narzędziami uczenia maszynowego](https://onnx.ai/supported-tools).

## <a name="overview"></a>Omówienie

Aby wywnioskować modele uczenia maszynowego w usłudze Azure SQL Edge, najpierw musisz uzyskać model. Może to być model wstępnie szkolony lub model niestandardowy szkolony z Twoją platformą. Usługa Azure SQL Edge obsługuje format ONNX i należy przekonwertować model na ten format. Nie powinno mieć wpływu na dokładność modelu i po utworzeniu modelu ONNX można wdrożyć model w usłudze Azure SQL Edge i użyć [natywnego oceniania z przewidywaną funkcją T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Pobierz modele ONNX

Aby uzyskać model w formacie ONNX:

- **Usługi do kompilowania modeli**: usługi takie jak [Funkcja automatycznego Machine Learning w systemach Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) i [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) obsługują bezpośrednio eksportowanie przeszkolonego modelu w formacie ONNX.

- [**Konwertowanie i/lub eksportowanie istniejących modeli**](https://github.com/onnx/tutorials#converting-to-onnx-format): kilka platform szkoleniowych (np. [PyTorch](https://pytorch.org/docs/stable/onnx.html), łańcucher i Caffe2) obsługuje natywne funkcje eksportu do ONNX, co pozwala na zapisywanie przeszkolonego modelu w określonej wersji formatu ONNX. W przypadku struktur, które nie obsługują eksportu natywnego, istnieją Autonomiczne pakiety instalowalne konwertera ONNX, które umożliwiają konwersję modeli przeszkolonych z różnych platform uczenia maszynowego w formacie ONNX.

     **Obsługiwane struktury**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit — informacje](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Aby zapoznać się z pełną listą obsługiwanych platform i przykładów, zobacz [konwertowanie do formatu ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Ograniczenia

Obecnie nie wszystkie modele ONNX są obsługiwane przez usługę Azure SQL Edge. Obsługa jest ograniczona do modeli o **liczbowych typach danych**:

- [int i bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [rzeczywiste i zmiennoprzecinkowe](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Inne typy liczbowe mogą być konwertowane na obsługiwane typy przy użyciu [Cast i Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Dane wejściowe modelu powinny mieć strukturę, aby każde wejście do modelu odnosi się do pojedynczej kolumny w tabeli. Na przykład jeśli używasz Pandas Dataframe do uczenia modelu, każdy element wejściowy powinien być oddzielną kolumną do modelu.

## <a name="next-steps"></a>Następne kroki

- [Wdróż program SQL Edge za Azure Portal](deploy-portal.md)
- [Wdrażanie modelu ONNX w usłudze Azure SQL Edge (wersja zapoznawcza)](deploy-onnx.md)
