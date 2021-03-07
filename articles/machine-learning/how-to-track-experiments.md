---
title: Rejestrowanie eksperymentów i metryk usługi ML
titleSuffix: Azure Machine Learning
description: Włącz rejestrowanie przebiegów szkoleniowych w sieci, aby monitorować metryki uruchamiania w czasie rzeczywistym oraz pomóc w diagnozowaniu błędów i ostrzeżeń.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0130af66152d4f70db47191ae2f271630a59e179
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441078"
---
# <a name="enable-logging-in-ml-training-runs"></a>Włącz rejestrowanie w przypadku przebiegów szkoleniowych w ML


Zestaw SDK języka Python usługi Azure Machine Learning umożliwia rejestrowanie informacji w czasie rzeczywistym przy użyciu domyślnego pakietu rejestrowania języka Python i funkcji specyficznej dla zestawu SDK. Możesz rejestrować lokalnie i wysyłać dzienniki do obszaru roboczego w portalu.

Dzienniki mogą ułatwić diagnozowanie błędów i ostrzeżeń lub śledzenie metryk wydajności, takich jak parametry i wydajność modelu. W tym artykule dowiesz się, jak włączyć rejestrowanie w następujących scenariuszach:

> [!div class="checklist"]
> * Interaktywne sesje trenowania
> * Przesyłanie zadań trenowania przy użyciu polecenia ScriptRunConfig
> * Natywne ustawienia rejestrowania (`logging`) języka Python
> * Rejestrowanie z dodatkowych źródeł


> [!TIP]
> W tym artykule pokazano, jak monitorować proces trenowania modelu. Jeśli interesuje Cię monitorowanie użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi trenowania lub ukończone wdrożenia modelu, zobacz [Monitorowanie usługi Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Typy danych

Można rejestrować wiele typów danych, w tym wartości skalarne, listy, tabele, obrazy, katalogi itd. Aby uzyskać więcej informacji oraz przykłady kodu w języku Python dla różnych typów danych, zobacz [stronę referencyjną uruchamiania klasy](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py).

### <a name="logging-run-metrics"></a>Rejestrowanie metryk uruchamiania 

Użyj następujących metod w interfejsie API rejestrowania, aby mieć wpływ na wizualizacje metryk. Zanotuj [limity usługi](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) dla tych zarejestrowanych metryk. 

|Wartość rejestrowana|Przykładowy kod| Formatowanie w portalu|
|----|----|----|
|Rejestruj tablicę wartości liczbowych| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Wykres liniowy z pojedynczą zmienną|
|Rejestruj pojedynczą wartość liczbową o tej samej nazwie metryki wielokrotnie używanej (na przykład w pętli for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Wykres liniowy z pojedynczą zmienną|
|Rejestruj wiersz z 2 kolumnami numerycznymi wielokrotnie|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Wykres liniowy z dwoma zmiennymi|
|Tabela dzienników z 2 kolumnami liczbowymi|`run.log_table(name='Sine Wave', value=sines)`|Wykres liniowy z dwoma zmiennymi|
|Obraz dziennika|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Ta metoda służy do rejestrowania pliku obrazu lub wykresu matplotlib do uruchomienia. Te obrazy będą widoczne i porównywalne w rekordzie przebiegu|

### <a name="logging-with-mlflow"></a>Rejestrowanie przy użyciu MLflow
Użyj MLFlowLogger, aby rejestrować metryki.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Interaktywna sesja rejestrowania

Interaktywne sesje rejestrowania są zwykle używane w środowiskach notesów. Metoda [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) uruchamia interaktywną sesję rejestrowania. Wszystkie metryki zarejestrowane w trakcie sesji są dodawane do rekordu przebiegu w eksperymencie. Metoda [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) kończy sesje i oznacza przebieg jako zakończony.

## <a name="scriptrun-logs"></a>Dzienniki ScriptRun

W tej sekcji dowiesz się, jak dodać kod rejestrowania w ramach przebiegów utworzonych podczas konfigurowania za pomocą klasy ScriptRunConfig. Do hermetyzowania skryptów i środowisk na potrzeby powtarzalnych przebiegów można użyć klasy [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py). Możesz również użyć tej opcji, aby wyświetlić widżet notesów Jupyter Notebook na potrzeby monitorowania.

Ten przykład wykonuje czyszczenie parametrów nad wartościami alfa i przechwytuje wyniki przy użyciu metody [run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----).

1. Utwórz skrypt trenowania zawierający logikę rejestrowania, skrypt `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Prześlij skrypt ```train.py``` w celu uruchomienia go w środowisku zarządzanym przez użytkownika. Na potrzeby trenowania jest przesyłany cały folder skryptu.

   [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = SRC)]


   [! Notes — Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? Name = Run)]

    Parametr `show_output` włącza pełne rejestrowanie, które umożliwia wyświetlenie szczegółowych informacji z procesu trenowania, a także informacji o wszelkich zasobach zdalnych lub docelowych obiektach obliczeniowych. Użyj poniższego kodu, aby włączyć pełne rejestrowanie podczas przesyłania eksperymentu.

```python
run = exp.submit(src, show_output=True)
```

Można również użyć tego samego parametru w funkcji `wait_for_completion` w wynikowym przebiegu.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Natywne rejestrowanie w języku Python

Niektóre dzienniki w zestawie SDK mogą zawierać błąd, który powoduje ustawienie poziomu rejestrowania na DEBUGOWANIE. Aby ustawić poziom rejestrowania, dodaj następujący kod do skryptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Dodatkowe źródła rejestrowania

Usługa Azure Machine Learning może również rejestrować informacje z innych źródeł podczas trenowania, takich jak przebiegi zautomatyzowanego uczenia maszynowego lub kontenery platformy Docker, które uruchamiają zadania. Te dzienniki nie są udokumentowane, ale jeśli wystąpią problemy i skontaktujesz się z pomocą techniczną firmy Microsoft, być może będą oni mogli wykorzystać te dzienniki podczas rozwiązywania problemów.

Informacje dotyczące rejestrowania metryk w projektancie usługi Azure Machine Learning znajdują się w temacie [Sposób rejestrowania metryk w projektancie](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Przykładowe notesy

W poniższych notesach przedstawiono pojęcia opisane w tym artykule:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat korzystania z usługi Azure Machine Learning:

* Dowiedz się, jak [rejestrować metryki w projektancie usługi Azure Machine Learning](how-to-track-designer-experiments.md).

* Zobacz w samouczku [Trenowanie modelu klasyfikacji obrazów za pomocą usługi Azure Machine Learning](tutorial-train-models-with-aml.md) przykład sposobu rejestrowania najlepszego modelu i wdrożenia go.
