---
title: 'Samouczek: uruchamianie "Hello World!" Skrypt języka Python'
titleSuffix: Azure Machine Learning
description: Część 2 szeregu Azure Machine Learning Get-Started pokazuje, jak przesłać prosty "Hello World!" Skrypt języka Python do chmury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 43a483f49a9e9004a4f487e82195198f2600a919
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071157"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>Samouczek: uruchamianie "Hello World!" Skrypt języka Python (część 2 z 4)

W ramach tego samouczka nauczysz się używać zestawu SDK Azure Machine Learning dla języka Python do przesyłania i uruchamiania języka Python "Hello World!" napisy.

Ten samouczek jest *częścią 2 serii samouczków* , w której przedstawiono podstawowe informacje na temat Azure Machine Learning i zakończenia zadań uczenia maszynowego opartych na zadaniach na platformie Azure. W tym samouczku przedstawiono pracę wykonaną w [części 1: Skonfiguruj maszynę lokalną do Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Utwórz i uruchom "Hello World!" Skrypt języka Python lokalnie.
> * Utwórz skrypt kontrolny języka Python, aby przesłać "Hello World!" do Azure Machine Learning.
> * Zapoznaj się z pojęciami Azure Machine Learning w skrypcie kontroli.
> * Prześlij i uruchom "Hello World!" napisy.
> * Wyświetlanie danych wyjściowych kodu w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [części 1](tutorial-1st-experiment-sdk-setup-local.md) , jeśli nie masz jeszcze obszaru roboczego Azure Machine Learning.

## <a name="create-and-run-a-python-script-locally"></a>Tworzenie i uruchamianie skryptu języka Python lokalnie

Utwórz nowy podkatalog o nazwie `src` w `tutorial` katalogu do przechowywania kodu, który ma być uruchamiany w klastrze obliczeniowym Azure Machine Learning. W `src` podkatalogu Utwórz `hello.py` skrypt języka Python:

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


### <a name="test-your-script-locally"></a><a name="test"></a>Lokalne testowanie skryptu

Kod można uruchomić lokalnie, korzystając z ulubionego środowiska IDE lub terminalu. Uruchamianie kodu lokalnie ma zalety interaktywnego debugowania kodu.  W oknie, które ma aktywowane środowisko *tutorial1* Conda, uruchom plik języka Python:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

> [!div class="nextstepaction"]
> [Skrypt został uruchomiony lokalnie](?success=run-local#control-script) [, wystąpił problem](https://www.research.net/r/7C2NTH7?issue=run-local)

## <a name="create-a-control-script"></a><a name="control-script"></a> Utwórz skrypt kontrolny

*Skrypt kontrolny* umożliwia uruchomienie `hello.py` skryptu w chmurze. Skrypt kontrolny służy do kontrolowania sposobu i miejsca uruchomienia kodu uczenia maszynowego.  

W katalogu samouczka Utwórz nowy plik w języku Python o nazwie `03-run-hello.py` i skopiuj/wklej następujący kod do tego pliku:

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
      [Obszar roboczy](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) nawiązuje połączenie z obszarem roboczym Azure Machine Learning, dzięki czemu można komunikować się z zasobami Azure Machine Learning.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Eksperyment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) zapewnia prosty sposób organizowania wielu przebiegów w ramach jednej nazwy. Później można zobaczyć, jak eksperymenty ułatwiają porównywanie metryk między dziesiątami z przebiegów.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) otacza `hello.py` kod i przekazuje go do obszaru roboczego. Jak sugeruje nazwa, można użyć tej klasy, aby _skonfigurować_ sposób _uruchamiania_ _skryptu_ w Azure Machine Learning. Określa również miejsce docelowe obliczeń, w których będzie wykonywany skrypt. W tym kodzie obiektem docelowym jest klaster obliczeniowy utworzony w [samouczku Instalatora](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Przesyła skrypt. To przesyłanie jest nazywane [przebiegiem](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py). Przebieg hermetyzuje pojedyncze wykonanie kodu. Użyj przebiegu, aby monitorować postęp skryptu, przechwycić dane wyjściowe, analizować wyniki, wizualizować metryki i nie tylko.
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

> [!div class="nextstepaction"]
> [Utworzono skrypt kontrolny,](?success=create-control-script#submit) [w którym wystąpił problem](https://www.research.net/r/7C2NTH7?issue=create-control-script)

## <a name="submit-and-run-your-code-in-the-cloud"></a><a name="submit"></a> Prześlij i Uruchom swój kod w chmurze

Uruchom skrypt kontrolny, który z kolei zostanie uruchomiony w `hello.py` klastrze obliczeniowym utworzonym w [samouczku Instalatora](tutorial-1st-experiment-sdk-setup-local.md).


```bash
python 03-run-hello.py
```

> [!TIP]
> Jeśli uruchomienie tego kodu spowoduje błąd, że nie masz dostępu do subskrypcji, zobacz [nawiązywanie połączenia z obszarem roboczym](how-to-manage-workspace.md?tab=python#connect-multi-tenant) w celu uzyskania informacji na temat opcji uwierzytelniania.

> [!div class="nextstepaction"]
> [Kod został przesłany w chmurze, w którym](?success=submit-to-cloud#monitor) [Wystąpił problem](https://www.research.net/r/7C2NTH7?issue=submit-to-cloud)

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a><a name="monitor"></a>Monitoruj kod w chmurze za pomocą programu Studio

Dane wyjściowe skryptu będą zawierać link do programu Studio, który wygląda następująco: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>` .

Postępuj zgodnie z linkiem.  W pierwszej kolejności zobaczysz stan **przygotowywania**.  Pierwsze uruchomienie zajmie 5-10 minut. Dzieje się tak, ponieważ występują następujące sytuacje:

* Obraz platformy Docker jest zbudowany w chmurze
* Rozmiar klastra obliczeniowego jest zmieniany z 0 na 1 węzeł
* Obraz platformy Docker zostanie pobrany do obliczenia. 

Kolejne uruchomienia są znacznie szybsze (~ 15 sekund), ponieważ obraz platformy Docker jest buforowany w ramach obliczeń. Można to przetestować przez ponowne przesłanie poniższego kodu po zakończeniu pierwszego uruchomienia.

Po zakończeniu zadania przejdź do karty dane **wyjściowe i dzienniki** . Zobaczysz `70_driver_log.txt` plik, który wygląda następująco:

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

`70_driver_log.txt`Plik zawiera standardowe dane wyjściowe z przebiegu. Ten plik może być przydatny w przypadku debugowania zdalnych przebiegów w chmurze.

> [!div class="nextstepaction"]
> [Pojawił się dziennik w programie Studio, w którym](?success=monitor-in-studio#next-steps) [Wystąpił problem](https://www.research.net/r/7C2NTH7?issue=monitor-in-studio)

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz prostą "Hello World!" skrypt i uruchomiono go na platformie Azure. Dowiesz się, jak nawiązać połączenie z obszarem roboczym Azure Machine Learning, utworzyć eksperyment i przesłać `hello.py` kod do chmury.

W następnym samouczku utworzysz te informacje, uruchamiając coś bardziej interesującego niż `print("Hello world!")` .

> [!div class="nextstepaction"]
> [Tutorial: Train a model](tutorial-1st-experiment-sdk-train.md) (Samouczek: uczenie modelu)

>[!NOTE] 
> Jeśli chcesz zakończyć serię samouczków, a nie postępować w następnym kroku, pamiętaj, aby [wyczyścić zasoby](tutorial-1st-experiment-bring-data.md#clean-up-resources).