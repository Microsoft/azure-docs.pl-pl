---
title: 'Samouczek: uczenie swojego pierwszego modelu uczenia maszynowego — Python'
titleSuffix: Azure Machine Learning
description: W części 3 serii Rozpoczynanie pracy z Azure Machine Learningą przedstawiono sposób uczenia modelu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
ms.openlocfilehash: 7e035406ce220728c316aaa2005e0f4d6089b5e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935519"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Samouczek: uczenie swojego pierwszego modelu uczenia maszynowego (część 3 z 4)

W tym samouczku przedstawiono sposób uczenia modelu uczenia maszynowego w Azure Machine Learning.

Ten samouczek jest *częścią 3 serii samouczków z czterema częściami* , w której przedstawiono podstawowe informacje na temat Azure Machine Learning i kończenia zadań uczenia maszynowego opartych na zadaniach na platformie Azure. Ten samouczek kompiluje pracę wykonaną w [części 1: Konfiguracja](tutorial-1st-experiment-sdk-setup-local.md) i [część 2: uruchamianie "Hello World!"](tutorial-1st-experiment-hello-world.md) serii.

W tym samouczku zostanie przemieszczony następny krok, przesyłając skrypt, który pociągnie za model uczenia maszynowego. Ten przykład pomoże Ci zrozumieć, jak Azure Machine Learning ułatwia zachowanie spójności między debugowaniem lokalnym i zdalnym.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie skryptu uczenia.
> * Użyj Conda, aby zdefiniować środowisko Azure Machine Learning.
> * Utwórz skrypt kontrolny.
> * Poznaj klasy Azure Machine Learning ( `Environment` , `Run` , `Metrics` ).
> * Prześlij i uruchom skrypt szkoleniowy.
> * Wyświetlanie danych wyjściowych kodu w chmurze.
> * Rejestruj metryki do Azure Machine Learning.
> * Wyświetl metryki w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

- [Anaconda](https://www.anaconda.com/download/) lub [Miniconda](https://www.anaconda.com/download/) , aby zarządzać środowiskami wirtualnymi języka Python i zainstalować pakiety.
- Zakończenie [part1](tutorial-1st-experiment-sdk-setup-local.md) i [część 2](tutorial-1st-experiment-hello-world.md) serii.

## <a name="create-training-scripts"></a>Tworzenie skryptów szkoleniowych

Najpierw Zdefiniuj architekturę sieci neuronowych w `model.py` pliku. Cały kod szkoleniowy zostanie uwzględniony w `src` podkatalogu, w tym `model.py` .

Następujący kod jest pobierany z [tego przykładu wprowadzającego](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) z PyTorch. Należy zauważyć, że pojęcia Azure Machine Learning dotyczą dowolnego kodu uczenia maszynowego, a nie tylko PyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Następnie zdefiniujesz skrypt szkoleniowy. Ten skrypt Pobiera zestaw danych CIFAR10 za pomocą `torchvision.dataset` interfejsów API PyTorch, konfiguruje sieć zdefiniowaną w `model.py` i pociąga za dwie epoki przy użyciu standardowej SGD i straty między entropiami.

Utwórz `train.py` skrypt w `src` podkatalogu:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Masz teraz następującą strukturę katalogów:

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="Struktura katalogów przedstawia train.py w podkatalogu src":::


> [!div class="nextstepaction"]
> [Moje skrypty szkoleniowe zostały utworzone](?success=create-scripts#environment) [w ramach problemu](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-new-python-environment"></a><a name="environment"></a> Utwórz nowe środowisko języka Python

Utwórz plik o nazwie `pytorch-env.yml` w `.azureml` ukrytym katalogu:

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

To środowisko ma wszystkie zależności wymagane przez model i skrypt szkoleniowy. Zwróć uwagę na to, że nie ma zależności od zestawu SDK Azure Machine Learning dla języka Python.

> [!div class="nextstepaction"]
> [Został utworzony plik środowiska, w którym](?success=create-env-file#test-local) [Wystąpił problem](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Testuj lokalnie

W terminalu lub w oknie monitu Anaconda Użyj poniższego kodu, aby przetestować skrypt lokalnie w nowym środowisku.  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

Po uruchomieniu tego skryptu zobaczysz dane pobrane do katalogu o nazwie `tutorial/data` .

> [!div class="nextstepaction"]
> [Uruchomiono kod lokalnie,](?success=test-local#create-local) [Wystąpił problem](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> Utwórz skrypt kontrolny

Różnica między następującym skryptem kontrolnym a tym, który został użyty do przesłania "Hello World!" polega na dodaniu kilku dodatkowych linii w celu ustawienia środowiska.

Utwórz nowy plik w języku Python w `tutorial` katalogu o nazwie `04-run-pytorch.py` :

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>Zrozumienie zmian w kodzie

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning oferuje koncepcję [środowiska](/python/api/azureml-core/azureml.core.environment.environment) , aby reprezentować przemieszczone w wersji środowisko Python w celu uruchamiania eksperymentów. Można łatwo utworzyć środowisko na podstawie lokalnego środowiska Conda lub PIP.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Dodaje środowisko do [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Utworzono skrypt kontrolny,](?success=control-script#submit) [w którym wystąpił problem](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> Prześlij przebieg do Azure Machine Learning

Wróć do środowiska *samouczka* , w którym zainstalowano zestaw SDK Azure Machine Learning dla języka Python. Ponieważ kod szkoleniowy nie jest uruchomiony na komputerze, nie trzeba instalować PyTorch.  Ale potrzebujesz `azureml-sdk` , który znajduje się w środowisku *samouczków* .

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> Przy pierwszym uruchomieniu tego skryptu Azure Machine Learning spowoduje skompilowanie nowego obrazu platformy Docker ze środowiska PyTorch. Wykonanie całego przebiegu może potrwać od 5 do 10 minut. 
>
> Dzienniki kompilacji platformy Docker można wyświetlić w programie Azure Machine Learning Studio. Postępuj zgodnie z linkiem do programu Studio, wybierz kartę **wyjściowe + dzienniki** , a następnie wybierz pozycję `20_image_build_log.txt` .
>
> Ten obraz zostanie ponownie użyty w przyszłych przebiegach, aby umożliwić ich uruchamianie znacznie szybciej.

Po skompilowaniu obrazu wybierz opcję `70_driver_log.txt` wyświetlania danych wyjściowych skryptu szkoleniowego.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Jeśli zobaczysz błąd `Your total snapshot size exceeds the limit` , katalog znajduje się `data` w `source_directory` wartości używanej w `ScriptRunConfig` .
>
> Przenieś `data` poza `src` .

Środowiska można rejestrować w obszarze roboczym za pomocą `env.register(ws)` . Można je następnie łatwo udostępnić, ponownie wykorzystać i wersja. Środowiska ułatwiają odtworzenie poprzednich wyników i współpracę z zespołem.

Azure Machine Learning utrzymuje również kolekcję środowiska nadzorowanego. Te środowiska obejmują typowe scenariusze uczenia maszynowego i są obsługiwane przez buforowane obrazy platformy Docker. Buforowane obrazy platformy Docker sprawiają, że pierwsze uruchomienie zdalne przebiega szybciej.

W krótkim czasie używanie zarejestrowanych środowisk może zaoszczędzić czas. Aby uzyskać więcej informacji [, zobacz jak używać środowisk](./how-to-use-environments.md) .

> [!div class="nextstepaction"]
> W trakcie [działania został zgłoszony](?success=test-w-environment#log) [problem](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> Rejestrowanie metryk szkoleń

Teraz, gdy masz już szkolenia modeli w Azure Machine Learning, Rozpocznij śledzenie niektórych metryk wydajności.

Bieżący skrypt szkoleniowy drukuje metryki do terminalu. Azure Machine Learning udostępnia mechanizm rejestrowania metryk z większą funkcjonalnością. Dodawanie kilku wierszy kodu pozwala uzyskać możliwość wizualizacji metryk w Studio i porównywania metryk między wieloma przebiegami.

### <a name="modify-trainpy-to-include-logging"></a>Modyfikuj `train.py` , aby uwzględnić rejestrowanie

Zmodyfikuj `train.py` skrypt, aby uwzględnić dwa więcej wierszy kodu:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>Zapoznaj się z dodatkowymi dwoma wierszami kodu

W programie `train.py` dostęp do obiektu Run można uzyskać z _poziomu_ samego skryptu szkoleniowego za pomocą `Run.get_context()` metody i używać go do rejestrowania metryk:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Metryki w Azure Machine Learning są:

- Są zorganizowane przez eksperymenty i przebiegi, dzięki czemu można łatwo śledzić i porównywać metryki.
- Wyposażone w interfejs użytkownika, dzięki któremu można wizualizować wydajność szkoleń w programie Studio.
- Zaprojektowana do skalowania, dzięki czemu można zachować te korzyści nawet w przypadku uruchamiania setek eksperymentów.

> [!div class="nextstepaction"]
> [Train.py ](?success=modify-train#log) [mi problem](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>Aktualizowanie pliku środowiska Conda

`train.py`Skrypt właśnie przejął nową zależność od `azureml.core` . Zaktualizuj `pytorch-env.yml` , aby uwzględnić tę zmianę:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Zaktualizowano plik środowiska, w którym](?success=update-environment#submit-again) [Wystąpił problem](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> Prześlij przebieg do Azure Machine Learning
Prześlij ten skrypt jeszcze raz:

```bash
python 04-run-pytorch.py
```

Tym razem, gdy odwiedzasz Studio, przejdź na kartę **metryki** , w której teraz można zobaczyć aktualizacje na żywo w modelu.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Wykres strat szkoleniowych na karcie metryki.":::

> [!div class="nextstepaction"]
> Pomyślnie [przesłano przebieg](?success=resubmit-with-logging#next-steps) [problemu](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Następne kroki

W tej sesji uaktualniono z poziomu podstawowego "Witaj świecie!" skrypt do bardziej realistycznego skryptu szkoleniowego, który wymagał uruchomienia określonego środowiska języka Python. Dowiesz się, jak przełączyć lokalne środowisko Conda do chmury za pomocą środowisk Azure Machine Learning. Na koniec pokazano, jak w kilku wierszach kodu można rejestrować metryki do Azure Machine Learning.

Istnieją inne sposoby tworzenia środowisk Azure Machine Learning, w tym [z pliku pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) lub [z istniejącego lokalnego środowiska Conda](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-).

W następnej sesji zobaczysz, jak korzystać z danych w Azure Machine Learning, przekazując zestaw danych CIFAR10 na platformę Azure.

> [!div class="nextstepaction"]
> [Samouczek: przenoszenie własnych danych](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Jeśli chcesz zakończyć serię samouczków, a nie postępować w następnym kroku, pamiętaj, aby [wyczyścić zasoby](tutorial-1st-experiment-bring-data.md#clean-up-resources).
