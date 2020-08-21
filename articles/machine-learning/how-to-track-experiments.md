---
title: Rejestruj eksperymenty w ML & metryki
titleSuffix: Azure Machine Learning
description: Monitoruj eksperymenty dotyczące platformy Azure ML i monitoruj metryki uruchamiania, aby usprawnić proces tworzenia modelu. Dodaj rejestrowanie do skryptu szkoleniowego za pomocą polecenia Run. log, Run. start_logging lub ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 18c7e5b3a1401540d7a94186fda647d413d562c0
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723847"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Włącz rejestrowanie w przebiegach szkoleniowych usługi Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zestaw Azure Machine Learning Python SDK umożliwia rejestrowanie informacji w czasie rzeczywistym przy użyciu domyślnego pakietu rejestrowania języka Python i funkcji specyficznych dla zestawu SDK. Możesz logować się lokalnie i wysyłać dzienniki do obszaru roboczego w portalu.

Dzienniki mogą ułatwić Diagnozowanie błędów i ostrzeżeń lub śledzenie metryk wydajności, takich jak parametry i wydajność modelu. W tym artykule dowiesz się, jak włączyć rejestrowanie w następujących scenariuszach:

> [!div class="checklist"]
> * Interaktywne sesje szkoleniowe
> * Przesyłanie zadań szkoleniowych przy użyciu ScriptRunConfig
> * Ustawienia natywne języka Python `logging`
> * Rejestrowanie z dodatkowych źródeł


> [!TIP]
> W tym artykule pokazano, jak monitorować proces szkolenia modelu. Jeśli interesuje Cię monitorowanie użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak limity przydziału, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Typy danych

Można rejestrować wiele typów danych, w tym wartości skalarne, listy, tabele, obrazy, katalogi itd. Aby uzyskać więcej informacji i przykłady kodu w języku Python dla różnych typów danych, zobacz [stronę informacje o klasie uruchomieniowej](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## <a name="interactive-logging-session"></a>Interaktywna sesja rejestrowania

Interaktywne sesje rejestrowania są zwykle używane w środowiskach notesów. Metoda [Eksperymentuj. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) uruchamia interaktywną sesję rejestrowania. Wszystkie metryki zarejestrowane w trakcie sesji są dodawane do rekordu uruchomienia w eksperymentie. Metoda [Run. Complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) kończy sesje i oznacza przebieg jako zakończony.

## <a name="scriptrunconfig-logs"></a>Dzienniki ScriptRunConfig

W tej sekcji dowiesz się, jak dodać kod rejestrowania wewnątrz ScriptConfig. Klasy [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) można użyć do hermetyzowania skryptów i środowisk do powtarzających się uruchomień. Możesz również użyć tej opcji, aby wyświetlić widżet Visual Jupyter notesy do monitorowania.

Ten przykład wykonuje przeczyszczenie parametrów nad wartościami alfa i przechwytuje wyniki przy użyciu metody [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) .

1. Utwórz skrypt szkoleniowy zawierający logikę rejestrowania `train.py` .

   [! code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Prześlij ```train.py``` skrypt do uruchomienia w środowisku zarządzanym przez użytkownika. Cały folder skryptu jest przesyłany do szkoleń.

   [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = SRC)] [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = Run)]

    `show_output`Parametr włącza pełną rejestrację, która umożliwia wyświetlenie szczegółowych informacji z procesu szkolenia, a także informacji o wszelkich zdalnych zasobach lub obiektach docelowych obliczeniowych. Użyj poniższego kodu, aby włączyć pełne rejestrowanie podczas przesyłania eksperymentu.

```python
run = exp.submit(src, show_output=True)
```

Można również użyć tego samego parametru w funkcji w `wait_for_completion` uruchomionym przebiegu.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Natywne rejestrowanie języka Python

Niektóre dzienniki w zestawie SDK mogą zawierać błąd, który powoduje ustawienie poziomu rejestrowania na Debugowanie. Aby ustawić poziom rejestrowania, Dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Dodatkowe źródła rejestrowania

Azure Machine Learning może również rejestrować informacje z innych źródeł podczas szkoleń, takich jak automatyczne uruchomienia uczenia maszynowego lub kontenerów platformy Docker, które uruchamiają zadania. Te dzienniki nie są udokumentowane, ale jeśli wystąpią problemy i skontaktuje się z pomocą techniczną firmy Microsoft, mogą oni korzystać z tych dzienników podczas rozwiązywania problemów.

Informacje dotyczące rejestrowania metryk w programie Azure Machine Learning Designer (wersja zapoznawcza) znajdują się w temacie [How to log Metrics in projektant (wersja zapoznawcza)](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Przykładowe notesy

W poniższych notesach przedstawiono Koncepcje opisane w tym artykule:
* [Jak używać — Azure/szkolenia/uczenie lokalne](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [Jak korzystać z programu — usługi Azure/śledzenia i monitorowania — eksperymenty/rejestrowanie — interfejs API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat korzystania z Azure Machine Learning:

* Dowiedz się, jak [rejestrować metryki w programie Azure Machine Learning Designer (wersja zapoznawcza)](how-to-track-designer-experiments.md).

* Zapoznaj się z przykładem sposobu rejestracji najlepszego modelu i wdrożenia go w samouczku, [uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).
