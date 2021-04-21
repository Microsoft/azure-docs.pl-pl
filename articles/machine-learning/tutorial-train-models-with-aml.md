---
title: 'Samouczek klasyfikacji obrazów: szkolenie modeli'
titleSuffix: Azure Machine Learning
description: Użyj Azure Machine Learning do trenowania modelu klasyfikacji obrazów za pomocą biblioteki scikit-learn w języku Python Jupyter Notebook. Ten samouczek jest częścią pierwszej z dwóch części.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 6c5691759983d8ec40598834e5dbcd507ccf00cf
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816876"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Samouczek: szkolenie modeli klasyfikacji obrazów przy użyciu danych MNIST i biblioteki scikit-learn 


W tym samouczku przeprowadzisz szkolenie modelu uczenia maszynowego na zdalnych zasobach obliczeniowych. Użyjesz przepływu pracy trenowania i wdrażania dla Azure Machine Learning w środowisku języka Python Jupyter Notebook.  Następnie możesz użyć notesu jako szablonu do uczenia własnego modelu uczenia maszynowego z użyciem własnych danych. Ten samouczek jest jedną z dwóch części serii **samouczków.**  

Ten samouczek uczy prostej regresji logistycznej przy użyciu [zestawu danych MNIST](http://yann.lecun.com/exdb/mnist/) i [biblioteki scikit-learn](https://scikit-learn.org) Azure Machine Learning. MNIST jest popularnym zestawem danych składającym się z 70 000 obrazów w skali szarości. Każdy obraz ma rozmiar 28 x 28 pikseli i przedstawia odręcznie napisaną cyfrę z zakresu od 0 do 9. Celem jest utworzenie klasyfikatora wieloklasowego do identyfikacji cyfry reprezentowanej przez dany obraz.

Dowiedz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego.
> * Uzyskiwanie dostępu do danych i badanie ich.
> * Trenowanie prostego modelu regresji logistycznej w klastrze zdalnym.
> * Przeglądanie wyników uczenia i rejestrowanie najlepszego modelu.

Tego, jak wybrać i wdrożyć model, dowiesz się z [drugiej części tego samouczka](tutorial-deploy-models-with-aml.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

>[!NOTE]
> Kod w tym artykule został przetestowany przy [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) w wersji 1.13.0.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [samouczek: wprowadzenie do tworzenia pierwszego eksperymentu usługi Azure ML,](tutorial-1st-experiment-sdk-setup.md) aby:
    * Tworzenie obszaru roboczego
    * Sklonuj notes samouczków do folderu w obszarze roboczym.
    * Tworzenie wystąpienia obliczeniowego opartego na chmurze.

* W sklonowanym folderze *tutorials/image-classification-mnist-data* otwórz notes *img-classification-part1-training.ipynb.* 


Samouczek i **utils.py** pliku są również dostępne w witrynie [GitHub,](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) jeśli chcesz używać go we własnym [środowisku lokalnym.](how-to-configure-environment.md#local) Uruchom `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` , aby zainstalować zależności na czas pracy z tym samouczkiem.

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, co w notesie.  
>
> Przejdź do Jupyter Notebook teraz, jeśli chcesz przeczytać kod podczas uruchamiania kodu. 
> Aby uruchomić pojedynczą komórkę kodu w notesie, kliknij komórkę code i naciśnij klawisze **Shift+Enter.** Możesz też uruchomić cały notes, wybierając opcję **Uruchom wszystko** na górnym pasku narzędzi.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Konfigurowanie środowiska projektowego

Cała konfiguracja dla prac programistycznych może zostać wykonana w notesie języka Python. Konfiguracja obejmuje następujące czynności:

* Importowanie pakietów języka Python.
* Nawiązywanie połączenia z obszarem roboczym, aby komputer lokalny mógł komunikować się z zasobami zdalnymi.
* Tworzenie eksperymentu do śledzenia wszystkich przebiegów.
* Tworzenie zdalnego docelowego zasobu obliczeniowego na potrzeby uczenia.

### <a name="import-packages"></a>Importowanie pakietów

Zaimportuj pakiety języka Python, które są potrzebne w tej sesji. Wyświetl również wersję zestawu Azure Machine Learning SDK:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Łączenie z obszarem roboczym

Utwórz obiekt obszaru roboczego na podstawie istniejącego obszaru roboczego. Metoda `Workspace.from_config()` odczytuje plik **config.json** i ładuje szczegóły do obiektu o nazwie `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> Przy pierwszym uruchomieniu poniższego kodu może pojawić się monit o uwierzytelnienie w obszarze roboczym. Wykonaj instrukcje wyświetlane na ekranie.

### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Utwórz eksperyment do śledzenia przebiegów w Twoim obszarze roboczym. Obszar roboczy może zawierać wiele eksperymentów:

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Tworzenie lub dołączanie istniejącego docelowego obiektu obliczeniowego

Za pomocą usługi zarządzanej Azure Machine Learning Compute analitycy danych mogą szkolić modele uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. Przykłady obejmują maszyny wirtualne z obsługą procesorów GPU. W tym samouczku utworzysz usługę Azure Machine Learning Compute jako środowisko uczenia. Kod języka Python do uruchomienia na tej maszynie wirtualnej zostanie przesłany w dalszej części tego samouczka. 

Poniższy kod utworzy za Ciebie klastry obliczeniowe, jeśli nie istnieją one jeszcze w Twoim obszarze roboczym. Konfiguruje klaster, który będzie skalowany w dół do wartości 0, gdy nie jest w użyciu, i może skalować w górę do maksymalnie 4 węzłów. 

 **Tworzenie docelowego obiektu obliczeniowego trwa około pięciu minut.** Jeśli zasób obliczeniowy znajduje się już w obszarze roboczym, kod używa go i pomija proces tworzenia.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Teraz masz pakiety i zasoby obliczeniowe niezbędne do przeprowadzenia uczenia modelu w chmurze. 

## <a name="explore-data"></a>Eksplorowanie danych

Zanim nauczysz model, musisz zrozumieć dane używane na potrzeby uczenia. W tej sekcji dowiesz się, jak wykonać następujące czynności:

* Pobieranie zestawu danych MNIST
* Wyświetlanie przykładowych obrazów

### <a name="download-the-mnist-dataset"></a>Pobieranie zestawu danych MNIST

Użyj Azure Open Datasets, aby pobrać nieprzetworzone pliki danych MNIST. [Azure Open Datasets](../open-datasets/overview-what-are-open-datasets.md) to publiczne zestawy danych, których można używać do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu dokładniejszego użycia modeli. Każdy zestaw danych ma odpowiadającą `MNIST` mu klasę, w tym przypadku do pobierania danych na różne sposoby.

Ten kod pobiera dane jako `FileDataset` obiekt , który jest podklasą `Dataset` . Odwołuje `FileDataset` się do jednego lub wielu plików w dowolnym formacie w magazynach danych lub publicznych adresach URL. Klasa zapewnia możliwość pobrania lub instalacji plików w obliczeniach przez utworzenie odwołania do lokalizacji źródła danych. Ponadto należy zarejestrować zestaw danych w obszarze roboczym w celu łatwego pobierania podczas trenowania.

Postępuj zgodnie z [how-to-to-learn](how-to-create-register-datasets.md) more about Datasets and their usage in the SDK (Zestawy danych i ich użycie w zestawie SDK).

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Wyświetlanie przykładowych obrazów

Załaduj pliki skompresowane do tablic `numpy`. Następnie użyj `matplotlib` do wykreślenia 30 losowych obrazów z zestawu danych wraz z ich etykietami nad nimi. Ten krok wymaga funkcji `load_data` uwzględnionej w pliku `utils.py`. Ten plik znajduje się w folderze przykładu. Upewnij się, że znajduje się on w tym samym folderze co ten notes. Funkcja `load_data` po prostu analizuje skompresowane pliki i przetwarza je w tablice numpy.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Zostanie wyświetlona losowa próbka obrazów:

![Losowa próbka obrazów](./media/tutorial-train-models-with-aml/digits.png)

Teraz wiesz już, jak wyglądają te obrazy i jakie są oczekiwane wyniki przewidywania.

## <a name="train-on-a-remote-cluster"></a>Uczenie w klastrze zdalnym

W przypadku tego zadania prześlij zadanie do uruchomienia w klastrze trenowania zdalnego, który został wcześniej ustawiony.  W celu przesłania zadania wykonywane są następujące czynności:
* Tworzenie katalogu
* Tworzenie skryptu uczenia
* Tworzenie konfiguracji uruchamiania skryptu
* Przesyłanie zadania

### <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz katalog w celu dostarczenia niezbędnego kodu ze swojego komputera do zasobu zdalnego.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Tworzenie skryptu uczenia

Aby przesłać zadanie do klastra, najpierw utwórz skrypt uczenia. Uruchom poniższy kod, aby utworzyć skrypt uczenia o nazwie `train.py` w katalogu, który został właśnie utworzony.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Zwróć uwagę, jak skrypt pobiera dane i zapisuje modele:

+ Skrypt uczenia odczytuje argument, aby znaleźć katalog zawierający dane. Podczas późniejszego przesyłania zadania wskażesz magazyn danych dla tego argumentu: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Skrypt trenowania zapisuje model w katalogu o nazwie **outputs**. Dowolne pliki zapisane w tym katalogu są automatycznie przekazywane do Twojego obszaru roboczego. W dalszej części samouczka z poziomu tego katalogu uzyskasz dostęp do swojego modelu. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Skrypt trenowania wymaga pliku do `utils.py` poprawnego załadowania zestawu danych. Poniższy kod kopiuje do pliku , aby można było uzyskać do niego dostęp wraz ze skryptem `utils.py` `script_folder` trenowania w zasobie zdalnym.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>Konfigurowanie zadania trenowania

Utwórz obiekt [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) aby określić szczegóły konfiguracji zadania trenowania, w tym skrypt trenowania, środowisko do użycia i docelowy obiekt obliczeniowy do uruchomienia. Skonfiguruj skrypt ScriptRunConfig, określając:

* Katalog zawierający Twoje skrypty. Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania.
* Docelowy zasób obliczeniowy. W tym przypadku użyjesz utworzonego klastra obliczeniowego usługi Azure Machine Learning.
* Nazwa skryptu uczenia, **train.py**.
* Środowisko, które zawiera biblioteki potrzebne do uruchomienia skryptu.
* Argumenty wymagane ze skryptu trenowania.

W tym samouczku elementem docelowym jest usługa AmlCompute. Wszystkie pliki w folderze skryptów są przekazywane do węzłów klastra w celu uruchomienia. Wartość **--data_folder** jest ustawiona do używania zestawu danych.

Najpierw utwórz środowisko zawierające: bibliotekę scikit-learn, azureml-dataset-runtime wymagane do uzyskiwania dostępu do zestawu danych oraz azureml-defaults, które zawiera zależności metryk rejestrowania. Wartość azureml-defaults zawiera również zależności wymagane do wdrożenia modelu jako usługi internetowej w dalszej części 2 samouczka.

Po zdefiniowanym środowisku zarejestruj je w obszarze roboczym, aby ponownie używać go w części 2 samouczka.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Następnie utwórz element ScriptRunConfig, określając skrypt trenowania, docelowy obiekt obliczeniowy i środowisko.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Przesyłanie zadania do klastra

Uruchom eksperyment, przesyłając obiekt ScriptRunConfig:

```python
run = exp.submit(config=src)
run
```

Ponieważ wywołanie jest asynchroniczne, zwraca ono stan **Przygotowywanie** lub **Uruchomiono** zaraz po uruchomieniu zadania.

## <a name="monitor-a-remote-run"></a>Monitorowanie zdalnego przebiegu

Łącznie pierwszy przebieg trwa **około 10 minut**. Jednak podczas kolejnych przebiegów, o ile zależności skryptu nie ulegną zmianie, jest ponownie używany ten sam obraz. Dlatego czas uruchamiania kontenera jest znacznie krótszy.

Co się dzieje podczas oczekiwania:

- **Tworzenie obrazu:** tworzony jest obraz platformy Docker, który odpowiada środowisku języka Python określonemu przez środowisko usługi Azure ML. Obraz jest przekazywany do obszaru roboczego. Tworzenie obrazu i jego przekazywanie trwa **około pięciu minut**.

  Ten etap jest wykonywany tylko raz dla każdego środowiska Python, ponieważ kontener jest buforowany dla kolejnych przebiegów. Podczas tworzenia obrazu dzienniki są przesyłane strumieniowo do historii uruchamiania. Postęp tworzenia obrazu możesz monitorować przy użyciu tych dzienników.

- **Skalowanie:** jeśli uruchomienie klastra zdalnego wymaga więcej węzłów niż jest obecnie dostępnych, dodatkowe węzły są dodawane automatycznie. Skalowanie zazwyczaj trwa **około pięciu minut.**

- **Uruchomienie**: na tym etapie niezbędne skrypty i pliki są wysyłane do docelowego obiektu obliczeniowego. Następnie magazyny danych są instalowane lub kopiowane. Następnie uruchamiany jest skrypt **entry_script**. Podczas działania zadania dane z wyjścia **stdout** i katalogu **./logs** są przesyłane strumieniowo do historii uruchamiania. Postęp przebiegu możesz monitorować przy użyciu tych dzienników.

- **Przetwarzanie po:** katalog **./outputs** uruchomienia jest kopiowany do historii uruchamiania w obszarze roboczym, dzięki czemu można uzyskać dostęp do tych wyników.

Postęp działającego zadania możesz sprawdzić na kilka sposobów. W tym samouczku jest używany widżet Jupyter oraz metoda `wait_for_completion`.

### <a name="jupyter-widget"></a>Widżet Jupyter

Obserwuj postęp uruchomienia za pomocą [widżetu Jupyter.](/python/api/azureml-widgets/azureml.widgets) Podobnie jak przesyłanie przebiegu, widżet jest asynchroniczny i udostępnia aktualizacje na bieżąco co 10–15 sekund aż do zakończenia zadania:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Widżet będzie wyglądać podobnie do poniższego na końcu szkolenia:

![Widżet notesu](./media/tutorial-train-models-with-aml/widget.png)

Jeśli musisz anulować przebieg, możesz wykonać [te instrukcje](./how-to-track-monitor-analyze-runs.md).

### <a name="get-log-results-upon-completion"></a>Pobieranie wyników dziennika po zakończeniu

Uczenie i monitorowanie modelu odbywa się w tle. Poczekaj na zakończenie uczenia modelu przed uruchomieniem dalszego kodu. Za pomocą metody `wait_for_completion` można wyświetlić informację o zakończeniu trenowania modelu:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Wyświetlanie wyników przebiegu

Teraz masz nauczony model w klastrze zdalnym. Pobierz dokładność modelu:

```python
print(run.get_metrics())
```

Dane wyjściowe pokazują, że model zdalny ma dokładność 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

W następnym samouczku bardziej szczegółowo poznasz ten model.

## <a name="register-model"></a>Rejestrowanie modelu

W ramach ostatniego kroku skryptu uczenia plik `outputs/sklearn_mnist_model.pkl` został zapisany w katalogu o nazwie `outputs` na maszynie wirtualnej klastra, gdzie zadanie jest uruchamiane. Katalog `outputs` jest katalogiem specjalnym, ponieważ cała jego zawartość jest automatycznie przekazywana do Twojego obszaru roboczego. Ta zawartość jest widoczna w rekordzie przebiegu eksperymentu w ramach Twojego obszaru roboczego. W związku z tym plik modelu jest teraz również dostępny w Twoim obszarze roboczym.

Możesz zobaczyć pliki skojarzone z tym przebiegiem:

```python
print(run.get_file_names())
```

Zarejestruj model w obszarze roboczym, aby umożliwić sobie (lub innym współpracownikom) późniejsze badanie i wdrażanie tego modelu oraz wykonywanie zapytań względem niego:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Usunąć możesz również sam klaster obliczeniowy usługi Azure Machine Learning. Jednak ponieważ włączona jest funkcja skalowania automatycznego i minimalna wielkość klastra jest równa zero, dla tego konkretnego zasobu nie będą naliczane dodatkowe opłaty za obliczenia, gdy nie jest on używany:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Następne kroki

W tym Azure Machine Learning samouczka język Python został użyty do wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie środowiska projektowego.
> * Uzyskiwanie dostępu do danych i badanie ich.
> * Szkolenie wielu modeli w klastrze zdalnym przy użyciu popularnej biblioteki uczenia maszynowego scikit-learn
> * Przeglądanie szczegółów uczenia i rejestrowanie najlepszego modelu.

Wszystko jest już gotowe do wdrożenia tego zarejestrowanego modelu zgodnie z instrukcjami w następnej części serii samouczków:

> [!div class="nextstepaction"]
> [Samouczek 2 — wdrażanie modeli](tutorial-deploy-models-with-aml.md)
