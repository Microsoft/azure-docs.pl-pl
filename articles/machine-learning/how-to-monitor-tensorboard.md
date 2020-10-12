---
title: Wizualizowanie eksperymentów za pomocą rozwiązania TensorBoard
titleSuffix: Azure Machine Learning
description: Uruchom TensorBoard, aby wizualizować historie uruchamiania eksperymentu i zidentyfikować potencjalne obszary do dostrajania parametrów i przeszkolenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2ed8910db8b903dab3b81d9db6c9b5798d2b6b69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542057"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Wizualizuj przebiegi eksperymentu i metryki za pomocą TensorBoard i Azure Machine Learning


Ten artykuł zawiera informacje na temat sposobu wyświetlania przebiegów eksperymentu i metryk w programie TensorBoard przy użyciu [ `tensorboard` pakietu](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py&preserve-view=true) w głównym zestawie SDK Azure Machine Learning. Po sprawdzeniu przebiegów eksperymentów możesz lepiej dostosowywać i ponownie szkolić modele uczenia maszynowego.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) to pakiet aplikacji sieci Web służący do sprawdzania i analizowania struktury eksperymentów oraz wydajności.

Sposób uruchamiania programu TensorBoard z Azure Machine Learning eksperymenty są zależne od typu eksperymentu:
+ Jeśli eksperyment natywnie wyprowadza pliki dziennika, które są konsumowane przez TensorBoard, takie jak PyTorch, Łańcucher i TensorFlow eksperymenty, można [uruchomić TensorBoard bezpośrednio](#launch-tensorboard) z historii przebiegów eksperymentu. 

+ W przypadku eksperymentów, które nie umożliwiają natywnego wyprowadzania plików TensorBoard, takich jak Scikit-nauka lub Azure Machine Learning eksperymenty, użyj [ `export_to_tensorboard()` metody](#export) do eksportowania historii uruchamiania jako TensorBoard dzienników i uruchamiania TensorBoard z tego miejsca. 

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z usługi Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby uruchomić TensorBoard i wyświetlić historie uruchamiania eksperymentów, eksperymenty muszą mieć wcześniej włączone rejestrowanie, aby śledzić metryki i wydajność.  
* Kod w tym dokumencie można uruchomić w dowolnym z następujących środowisk: 
    * Wystąpienie obliczeniowe Azure Machine Learning — nie jest wymagane pobieranie ani instalacja
        * Ukończ [Samouczek: Zainstaluj środowisko i obszar roboczy](tutorial-1st-experiment-sdk-setup.md) , aby utworzyć dedykowany serwer notesu wstępnie załadowany z zestawem SDK i przykładowym repozytorium.
        * W folderze Samples na serwerze notesu Znajdź dwa ukończone i rozwinięte notesy, przechodząc do następujących katalogów:
            * **Instrukcje-to-use-Azure > śledzenie i monitorowanie eksperymentów > tensorboard > Export-Run-tensorboard > Export-Run-History-to-tensorboard. ipynb**
            * **Instrukcje-to-use-Azure > śledzenie i monitorowanie eksperymentów > tensorboard > tensorboard > tensorboard. ipynb**
    * Własny serwer notesu Juptyer
       * [Zainstaluj zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) z `tensorboard` dodatkowymi
        * [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).  
        * [Utwórz plik konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Opcja 1: bezpośrednie wyświetlanie historii uruchamiania w TensorBoard

Ta opcja działa w przypadku eksperymentów, które natywnie wyprowadzają pliki dzienników TensorBoard, takie jak PyTorch, Łańcucher i eksperymenty TensorFlow. Jeśli nie jest to przypadek eksperymentu, zamiast tego użyj [ `export_to_tensorboard()` metody](#export) .

Poniższy przykładowy kod używa [eksperymentu demonstracyjnego mnist ręcznie](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) z repozytorium TensorFlow w zdalnym miejscu docelowym obliczeń Azure Machine Learning COMPUTE. Następnie skonfigurujemy i uruchomimy przebieg do uczenia modelu TensorFlow, a następnie zaczniemy TensorBoard na ten eksperyment TensorFlow.

### <a name="set-experiment-name-and-create-project-folder"></a>Ustawianie nazwy eksperymentu i tworzenie folderu projektu

Tutaj nazwijmy eksperyment i utworzysz jego folder. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Pobierz kod eksperymentu demonstracyjnego TensorFlow

Repozytorium TensorFlow zawiera demonstrację MNIST ręcznie z obszerną Instrumentacją TensorBoard. Nie musimy zmieniać żadnego kodu demonstracyjnego, aby móc współpracować z Azure Machine Learning. W poniższym kodzie pobieramy kod MNIST ręcznie i zapisujemy go w nowo utworzonym folderze eksperymentów.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
W pliku kodu mnist ręcznie mnist_with_summaries. PR należy zauważyć, że istnieją linie, które wywołują `tf.summary.scalar()` ,  `tf.summary.histogram()` `tf.summary.FileWriter()` itp. Te metody grupują i rejestrują kluczowe metryki dotyczące eksperymentów w historii uruchamiania. `tf.summary.FileWriter()`Jest to szczególnie ważne podczas serializacji danych z zarejestrowanych metryk eksperymentów, co umożliwia TensorBoard wygenerowanie wizualizacji z nich.

 ### <a name="configure-experiment"></a>Konfigurowanie eksperymentu

W poniższej tabeli skonfigurujemy eksperyment i konfigurujesz katalogi dla dzienników i danych. Te dzienniki zostaną przekazane do historii uruchamiania, która TensorBoard później dostęp do nich.

> [!Note]
> Na potrzeby tego przykładu TensorFlow należy zainstalować TensorFlow na komputerze lokalnym. Ponadto moduł TensorBoard (to jest, który jest dołączony do TensorFlow) musi być dostępny dla jądra tego notesu, ponieważ komputer lokalny jest uruchomionym TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Tworzenie klastra dla eksperymentu
Dla tego eksperymentu tworzymy klaster AmlCompute, jednak eksperymenty mogą być tworzone w dowolnym środowisku i nadal można uruchamiać TensorBoard z historii uruchamiania eksperymentów. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Konfigurowanie i przesyłanie przebiegu szkoleniowego

Skonfiguruj zadanie szkoleniowe, tworząc obiekt ScriptRunConfig.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>Uruchom TensorBoard

TensorBoard można uruchomić w trakcie działania lub po jego zakończeniu. W poniższej tabeli utworzysz wystąpienie obiektu TensorBoard, `tb` które przyjmuje historię uruchamiania eksperymentu załadowanej w `run` , a następnie uruchamia TensorBoard z `start()` metodą. 
  
[Konstruktor TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true) pobiera tablicę przebiegów, więc upewnij się, że jest ona przekazana jako tablica jednoelementowa.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> W tym przykładzie użyto TensorFlow, TensorBoard może być łatwo używany z PyTorchem lub łańcuchem. TensorFlow musi być dostępny na maszynie z systemem TensorBoard, ale nie jest to konieczne na maszynie wykonującej obliczenia PyTorch lub łańcucha. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Opcja 2: Eksportuj historię jako dziennik do wyświetlania w TensorBoard

Poniższy kod umożliwia skonfigurowanie przykładowego eksperymentu, rozpoczęcie procesu rejestrowania przy użyciu interfejsów API historii uruchamiania Azure Machine Learning i eksportowanie historii przebiegów eksperymentu do dzienników, których można używać w celu wizualizacji. 

### <a name="set-up-experiment"></a>Konfigurowanie eksperymentu

Poniższy kod konfiguruje nowy eksperyment i nazywa katalog uruchamiania `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Tutaj ładujemy zestaw danych cukrzycą — wbudowany mały zestaw danych, który jest dostarczany z scikit — uczenie się i dzielenie go na Zestawy testowe i szkoleniowe.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Uruchom eksperymenty i Rejestruj metryki

W przypadku tego kodu nauczymy model regresji liniowej i metryki klucza dziennika, współczynnik alfa, `alpha` i średni błąd kwadratowy, `mse` w historii uruchamiania.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Eksportuj przebiegi do TensorBoard

Dzięki metodzie [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py&preserve-view=true) zestawu SDK możemy wyeksportować historię uruchamiania naszego eksperymentu usługi Azure Machine Learning do dzienników tensorboard, aby można było wyświetlić je za pośrednictwem tensorboard.  

W poniższym kodzie tworzymy folder `logdir` w naszym bieżącym katalogu roboczym. Ten folder umożliwia wyeksportowanie historii uruchamiania eksperymentu i dzienników z programu `root_run` , a następnie oznaczenie tego przebiegu jako ukończone. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

> [!Note]
> Można również wyeksportować konkretny przebieg do TensorBoard przez określenie nazwy przebiegu  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Uruchamianie i zatrzymywanie TensorBoard
Gdy nasza historia przebiegu tego eksperymentu zostanie wyeksportowana, można uruchomić TensorBoard z użyciem metody [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true#&preserve-view=truestart-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Gdy skończysz, pamiętaj, aby wywołać metodę [stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true#&preserve-view=truestop--) obiektu TensorBoard. W przeciwnym razie TensorBoard będzie nadal działać do momentu wyłączenia jądra notesu. 

```python
tb.stop()
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono dwa eksperymenty i zapoznaj się z informacjami na temat sposobu uruchamiania TensorBoard na ich historie, aby identyfikować obszary umożliwiające dostosowanie i przeszkolenie. 

* Jeśli Twój model jest zadowalający, przejdź do naszego [sposobu wdrażania artykułu modelu](how-to-deploy-and-where.md) . 
* Dowiedz się więcej na temat [dostrajania parametrów](how-to-tune-hyperparameters.md). 
