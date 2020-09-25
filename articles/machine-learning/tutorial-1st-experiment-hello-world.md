---
title: 'Samouczek: uruchamianie skryptu Python "Hello world"'
titleSuffix: Azure Machine Learning
description: Część 2 serii Rozpoczynanie pracy z usługą Azure ML pokazuje, jak przesłać prosty skrypt języka Python "Hello World" do chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 52a9932db4fc261b8f3d740a316af3e852559a32
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320493"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Samouczek: uruchamianie skryptu Python "Hello world" (część 2 z 4)

W tym samouczku dowiesz się, jak używać zestawu SDK języka Python Azure Machine Learning do przesyłania i uruchamiania skryptu języka Python "Hello world".

Ten samouczek jest **drugą częścią serii samouczków z czterema częściami** , w której przedstawiono podstawowe informacje na temat Azure Machine Learning i zakończenia zadań uczenia maszynowego opartych na zadaniach na platformie Azure. Ten samouczek kompiluje pracę wykonaną w [samouczku część 1: Skonfiguruj maszynę lokalną do Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Tworzenie i uruchamianie skryptu języka Python "Hello world" lokalnie
> * Utwórz skrypt kontrolny języka Python, aby przesłać "Hello World" do Azure Machine Learning
> * Zrozumienie Azure Machine Learning koncepcji w skrypcie kontroli
> * Prześlij i uruchom "Hello world"
> * Wyświetlanie danych wyjściowych kodu w chmurze

## <a name="prerequisites"></a>Wymagania wstępne

- Ukończ [część 1 samouczka dotyczącego konfiguracji komputera lokalnego](tutorial-1st-experiment-sdk-setup-local.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning.
- Wstępna wiedza dotycząca języka Python i przepływów pracy uczenia maszynowego.
- Lokalne środowisko programistyczne. Obejmuje to, ale nie jest ograniczony do Visual Studio Code, Jupyter lub platformy PyCharm itd.
- Python (wersja 3.5-3.7).

## <a name="create-and-run-a-python-script-locally"></a>Tworzenie i uruchamianie skryptu języka Python lokalnie

Utwórz nowy podkatalog o nazwie `src` w `tutorial` katalogu do przechowywania kodu, który ma być uruchamiany w klastrze obliczeniowym Azure Machine Learning. W `src` podkatalogu Utwórz skrypt języka `hello.py` Python:

```python
# src/hello.py
print("Hello world!")
```

Struktura katalogów projektu będzie teraz wyglądać następująco:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Lokalne testowanie skryptu

Kod można uruchomić lokalnie, który ma zalety interaktywnego debugowania kodu przy użyciu ulubionego środowiska IDE lub za pośrednictwem terminalu:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Utwórz skrypt kontrolny

*Skrypt kontrolny* umożliwia uruchomienie `hello.py` skryptu w chmurze.  Skrypt kontrolny pozwala kontrolować, jak i gdzie jest uruchamiany kod uczenia maszynowego.  

W katalogu samouczka Utwórz nowy plik w języku Python o nazwie `03-run-hello.py` i Skopiuj poniższy kod do tego pliku:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>Zrozumienie kodu

Poniżej znajduje się opis sposobu działania skryptu kontroli:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Obszar roboczy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) nawiązuje połączenie z obszarem roboczym Azure Machine Learning, dzięki czemu można komunikować się z zasobami Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Eksperyment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) zapewnia prosty sposób organizowania wielu przebiegów w ramach jednej nazwy. Później można zobaczyć, jak eksperymenty ułatwiają porównywanie metryk między dziesiątami z przebiegów.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) otacza `hello.py` kod i przekazuje go do obszaru roboczego. Jak sugeruje nazwa, można użyć tej klasy, aby _skonfigurować_ sposób _uruchamiania_ _skryptu_ w Azure Machine Learning. Określa również miejsce docelowe obliczeń, na których będzie wykonywany skrypt.  W tym kodzie obiektem docelowym jest klaster obliczeniowy utworzony w [samouczku Instalatora](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Przesyła skrypt. To przesyłanie jest nazywane [przebiegiem](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true).  Przebieg hermetyzuje pojedyncze wykonanie kodu. Użyj przebiegu, aby monitorować postęp skryptu, przechwycić dane wyjściowe, analizować wyniki, wizualizować metryki i nie tylko.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run`Obiekt udostępnia dojście do wykonania kodu. Monitoruj postęp z Azure Machine Learning Studio przy użyciu adresu URL, który jest drukowany ze skryptu języka Python.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Prześlij i Uruchom swój kod w chmurze

Uruchom skrypt kontrolny, który z kolei zostanie uruchomiony w `hello.py` klastrze obliczeniowym utworzonym w [samouczku Instalatora](tutorial-1st-experiment-sdk-setup-local.md).

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Monitoruj kod w chmurze przy użyciu programu Studio

Dane wyjściowe będą zawierać linki do Azure Machine Learning Studio, które wyglądają następująco: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Postępuj zgodnie z linkiem i przejdź do karty dane **wyjściowe i dzienniki** . Można wyświetlić plik `70_driver_log.txt` podobny do tego:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

W wierszu 8 zobaczysz "Hello World!" rozdzielczości.

`70_driver_log.txt`Plik zawiera standardowe dane wyjściowe z przebiegu. Ten plik może być przydatny podczas debugowania zdalnych przebiegów w chmurze.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono prosty skrypt "Hello World" i uruchomiono go na platformie Azure. Dowiesz się, jak nawiązać połączenie z obszarem roboczym Azure Machine Learning, utworzyć eksperyment i przesłać `hello.py` kod do chmury.

W następnym samouczku utworzysz te informacje, uruchamiając coś bardziej interesującego niż `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Tutorial: Train a model](tutorial-1st-experiment-sdk-train.md) (Samouczek: uczenie modelu)

>[!NOTE] 
> Jeśli chcesz zakończyć serię samouczków, a nie postępować w następnym kroku, pamiętaj o [oczyszczeniu zasobów](tutorial-1st-experiment-bring-data.md#clean-up-resources)
