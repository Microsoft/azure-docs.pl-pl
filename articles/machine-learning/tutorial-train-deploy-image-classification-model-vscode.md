---
title: 'Samouczek: uczenie & Wdrażanie modeli: VS Code (wersja zapoznawcza)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak nauczyć i wdrożyć model klasyfikacji obrazów przy użyciu TensorFlow i rozszerzenia Visual Studio Code Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.custom: contperf-fy20q4
ms.openlocfilehash: 937b5ebb13bfbf27ff04b6944f2de7e32e7e656c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185856"
---
# <a name="train-and-deploy-an-image-classification-tensorflow-model-using-the-azure-machine-learning-visual-studio-code-extension-preview"></a>Uczenie i wdrażanie modelu TensorFlow klasyfikacji obrazów przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning (wersja zapoznawcza)

Dowiedz się, jak nauczyć i wdrożyć model klasyfikacji obrazów, aby rozpoznawać numery ręczne przy użyciu TensorFlow i rozszerzenia Visual Studio Code Azure Machine Learning.

W tym samouczku zapoznasz się z następującymi zadaniami:

> [!div class="checklist"]
> * Zrozumienie kodu
> * Tworzenie obszaru roboczego
> * Tworzenie eksperymentu
> * Konfigurowanie obiektów docelowych komputera
> * Uruchamianie pliku konfiguracji
> * Szkolenie modelu
> * Rejestrowanie modelu
> * Wdrażanie modelu

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz takiego konta, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
- Zainstaluj [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), lekki Edytor kodu dla wielu platform.
- Azure Machine Learning Studio Visual Studio Code rozszerzenia. Instrukcje dotyczące instalacji znajdują się w [samouczku Azure Machine Learning instalatora Visual Studio Code rozszerzenia](./tutorial-setup-vscode-extension.md)

## <a name="understand-the-code"></a>Zrozumienie kodu

Kod w tym samouczku używa TensorFlow do uczenia modelu uczenia maszynowego klasyfikacji obrazów, który określa kategorie cyfr odpisanych od 0-9. Robi to przez utworzenie sieci neuronowych, która przyjmuje wartości pikseli 28 px x 28 pikseli jako dane wejściowe i wyprowadza listę 10 prawdopodobieństw, jeden dla każdej z nich. Poniżej znajduje się przykład, jak wyglądają dane.  

![Cyfry MNIST ręcznie](./media/tutorial-train-deploy-image-classification-model-vscode/digits.png)

Pobierz kod dla tego samouczka, pobierając i rozpakowywanie [vs Code narzędzia dla REPOZYTORIUM AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) w dowolnym miejscu na komputerze.

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Pierwszą czynnością, którą należy wykonać w celu skompilowania aplikacji w Azure Machine Learning jest utworzenie obszaru roboczego. Obszar roboczy zawiera zasoby służące do uczenia modeli oraz samych szkolonych modeli. Aby uzyskać więcej informacji, zobacz [co to jest obszar roboczy](./concept-workspace.md). 

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** , aby otworzyć widok Azure Machine Learning.
1. Kliknij prawym przyciskiem myszy subskrypcję platformy Azure i wybierz pozycję **Utwórz obszar roboczy**. 
    
    > [!div class="mx-imgBorder"]
    > ![Tworzenie obszaru roboczego](./media/tutorial-train-deploy-image-classification-model-vscode/create-workspace.png)

1. Domyślnie jest generowana nazwa zawierająca datę i godzinę utworzenia. W polu wprowadzanie tekstu zmień nazwę na "TeamWorkspace" i naciśnij klawisz **Enter**.
1. Wybierz pozycję **Utwórz nową grupę zasobów**. 
1. Nazwij grupę zasobów "TeamWorkspace-RG" i naciśnij klawisz **Enter**. 
1. Wybierz lokalizację obszaru roboczego. Zalecane jest wybranie lokalizacji znajdującej się najbliżej lokalizacji planowanej do wdrożenia modelu. Na przykład "zachodnie stany USA 2".
1. Po wyświetleniu monitu o wybranie typu obszaru roboczego wybierz pozycję **podstawowa**.

W tym momencie żądanie do platformy Azure jest tworzone w celu utworzenia nowego obszaru roboczego na koncie. Po kilku minutach nowy obszar roboczy zostanie wyświetlony w węźle subskrypcja. 

## <a name="create-an-experiment"></a>Tworzenie eksperymentu

Co najmniej jedno eksperymenty można utworzyć w obszarze roboczym, aby śledzić i analizować poszczególne przebiegi szkoleniowe modelu. Uruchomienia można wykonać w chmurze platformy Azure lub na komputerze lokalnym.

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning.
1. Rozwiń węzeł subskrypcji.
1. Rozwiń węzeł **TeamWorkspace** . 
1. Kliknij prawym przyciskiem myszy węzeł **eksperymenty** .
1. Wybierz pozycję **Utwórz eksperyment** z menu kontekstowego.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/create-experiment.png)

1. Nazwij eksperyment "samouczek-programu vscode-MNIST ręcznie" i naciśnij klawisz **Enter** , aby utworzyć nowy eksperyment. 

Podobnie jak obszary robocze, żądanie jest wysyłane do platformy Azure w celu utworzenia eksperymentu z podaną konfiguracją. Po kilku minutach nowy eksperyment zostanie wyświetlony w węźle *eksperymenty* w obszarze roboczym. 

## <a name="configure-compute-targets"></a>Skonfiguruj cele obliczeń

Obiekt docelowy obliczeń jest zasobem obliczeniowym lub środowiskiem, w którym uruchamiane są skrypty i wdrażają przeszkolone modele. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą programu Azure Machine Learning COMPUTE targets](./concept-compute-target.md).

Aby utworzyć obiekt docelowy obliczeń:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace** . 
1. W węźle obszar roboczy kliknij prawym przyciskiem myszy węzeł **Klastry obliczeniowe** i wybierz polecenie **Utwórz obliczenia**. 

    > [!div class="mx-imgBorder"]
    > ![Tworzenie obiektu docelowego obliczeń](./media/tutorial-train-deploy-image-classification-model-vscode/create-compute.png)

1. Wybierz pozycję **Azure Machine Learning COMPUTE (AmlCompute)**. Azure Machine Learning COMPUTE to infrastruktura obliczeniowa, która umożliwia użytkownikowi łatwe tworzenie obliczeń pojedynczego lub wielowęzłowego, które mogą być używane z innymi użytkownikami w obszarze roboczym.
1. Wybierz rozmiar maszyny wirtualnej. Wybierz pozycję **Standard_F2s_v2** z listy opcji. Rozmiar maszyny wirtualnej ma wpływ na ilość czasu potrzebną do uczenia modeli. Aby uzyskać więcej informacji na temat rozmiarów maszyny wirtualnej, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure](../virtual-machines/sizes.md).
1. Nazwij obliczenia "TeamWkspc-com" i naciśnij klawisz **Enter** , aby utworzyć obliczenia.

    Plik jest wyświetlany w VS Code z zawartością podobną do poniższej:

    ```json
    {
        "location": "westus2",
        "tags": {},
        "properties": {
            "computeType": "AmlCompute",
            "description": "",
            "properties": {
                "vmSize": "Standard_F2s_v2",
                "vmPriority": "dedicated",
                "scaleSettings": {
                    "maxNodeCount": 4,
                    "minNodeCount": 0,
                    "nodeIdleTimeBeforeScaleDown": "PT120S"
                }
            }
        }
    }
    ```

1. Po spełnieniu konfiguracji, Otwórz paletę poleceń, wybierając pozycję **wyświetl > palecie poleceń**.
1. Wprowadź następujące polecenie w palecie poleceń, aby zapisać plik konfiguracji uruchomieniowej.

    ```text
    Azure ML: Save and Continue
    ```

Po kilku minutach nowy obiekt docelowy obliczeń zostanie wyświetlony w węźle *Klastry obliczeniowe* obszaru roboczego.

## <a name="create-a-run-configuration"></a>Utwórz konfigurację uruchamiania

Po przesłaniu szkolenia do elementu docelowego obliczeń należy również przesłać konfigurację wymaganą do uruchomienia zadania szkoleniowego. Na przykład skrypt zawierający kod szkoleniowy i zależności języka Python, które są niezbędne do jego uruchomienia.

Aby utworzyć konfigurację uruchamiania:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **Klastry obliczeniowe TeamWorkspace >** . 
1. W węźle obliczenia kliknij prawym przyciskiem myszy węzeł obliczeniowy **TeamWkspc-com** i wybierz polecenie **Utwórz konfigurację przebiegu**.

    > [!div class="mx-imgBorder"]
    > ![Utwórz konfigurację uruchamiania](./media/tutorial-train-deploy-image-classification-model-vscode/create-run-configuration.png)

1. Nazwij konfigurację uruchomieniową "MNIST ręcznie-RC" i naciśnij klawisz **Enter** , aby utworzyć konfigurację przebiegu.
1. Następnie wybierz pozycję **Utwórz nowe środowisko usługi Azure ml**. Środowiska definiują zależności wymagane do uruchamiania skryptów.
1. Nazwij środowisko "MNIST ręcznie-ENV" i naciśnij klawisz **Enter**.
1. Wybierz z listy **plik zależności Conda** .
1. Naciśnij klawisz **Enter** , aby przeglądać plik zależności Conda. W takim przypadku plik zależności jest `env.yml` plikiem znajdującym się w `vscode-tools-for-ai/mnist-vscode-docs-sample` katalogu.

    Plik jest wyświetlany w VS Code z zawartością podobną do poniższej:

    ```json
    {
        "name": "MNIST-env",
        "version": "1",
        "python": {
            "interpreterPath": "python",
            "userManagedDependencies": false,
            "condaDependencies": {
                "name": "vs-code-azure-ml-tutorial",
                "channels": [
                    "defaults"
                ],
                "dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip",
                    {
                        "pip": [
                            "azureml-defaults"
                        ]
                    }
                ]
            },
            "baseCondaEnvironment": null
        },
        "environmentVariables": {},
        "docker": {
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "baseDockerfile": null,
            "baseImageRegistry": {
                "address": null,
                "username": null,
                "password": null
            },
            "enabled": false,
            "arguments": []
        },
        "spark": {
            "repositories": [],
            "packages": [],
            "precachePackages": true
        },
        "inferencingStackVersion": null
    }
    ```

1. Po zakończeniu konfiguracji Zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

1. Ten przykład nie używa zestawu danych zarejestrowanego w Azure Machine Learning. Zamiast tego jest ładowany podczas uruchamiania *Train.py* . Po wyświetleniu monitu o utworzenie odwołania do danych dla przebiegu szkoleniowego wprowadź "n" w wierszu polecenia i naciśnij klawisz **Enter**.
1. Naciśnij klawisz **Enter** , aby przeglądać plik skryptu, który ma zostać uruchomiony w ramach obliczeń. W takim przypadku skrypt do uczenia modelu jest `train.py` plikiem znajdującym się w `vscode-tools-for-ai/mnist-vscode-docs-sample` katalogu.

    Plik o nazwie `MNIST-rc.runconfig` występuje w vs Code z zawartością podobną do poniższej:

    ```json
    {
        "script": "train.py",
        "arguments": [],
        "framework": "Python",
        "communicator": "None",
        "target": "TeamWkspc-com",
        "environment": {
            "name": "MNIST-env",
            "version": "1",
            "python": {
                "interpreterPath": "python",
                "userManagedDependencies": false,
                "condaDependencies": {
                    "name": "vs-code-azure-ml-tutorial",
                    "channels": [
                        "defaults"
                    ],
                    "dependencies": [
                        "python=3.6.2",
                        "tensorflow=1.15.0",
                        "pip",
                        {
                            "pip": [
                                "azureml-defaults"
                            ]
                        }
                    ]
                },
                "baseCondaEnvironment": null
            },
            "environmentVariables": {},
            "docker": {
                "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
                "baseDockerfile": null,
                "baseImageRegistry": {
                    "address": null,
                    "username": null,
                    "password": null
                },
                "enabled": false,
                "arguments": []
            },
            "spark": {
                "repositories": [],
                "packages": [],
                "precachePackages": true
            },
            "inferencingStackVersion": null
        },
        "history": {
            "outputCollection": true,
            "snapshotProject": false,
            "directoriesToWatch": [
                "logs"
            ]
        }
    }
    ```

1. Po zakończeniu konfiguracji Zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

`MNIST-rc`Konfiguracja przebiegu jest dodawana do węzła obliczeniowego *TeamWkspc-com* , a `MNIST-env` Konfiguracja środowiska jest dodawana w węźle *środowiska* .

## <a name="train-the-model"></a>Trenowanie modelu

W trakcie procesu szkolenia model TensorFlow jest tworzony przez przetwarzanie danych szkoleniowych i wzorców uczenia osadzonych w nim dla każdej z odpowiednich cyfr, które są klasyfikowane. 

Aby uruchomić eksperyment Azure Machine Learning:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning. 
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace > eksperymenty** . 
1. Kliknij prawym przyciskiem myszy eksperyment **mnist ręcznie** .
1. Wybierz pozycję **Uruchom eksperyment**.

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/run-experiment.png)

1. Z listy opcji elementu docelowego obliczeń wybierz element docelowy obliczeń **TeamWkspc-com** .
1. Następnie wybierz konfigurację przebiegu **mnist ręcznie-RC** .
1. W tym momencie żądanie jest wysyłane do platformy Azure w celu uruchomienia eksperymentu w wybranym miejscu docelowym obliczeń w obszarze roboczym. Ten proces trwa kilka minut. W czasie wykonywania zadania szkolenia ma wpływ kilka czynników, takich jak typ obliczeń i rozmiar danych szkoleniowych. Aby śledzić postęp eksperymentu, kliknij prawym przyciskiem myszy bieżący węzeł przebiegu i wybierz polecenie **Wyświetl Uruchom w Azure Portal**.
1. Gdy zostanie wyświetlone okno dialogowe z prośbą o otwarcie zewnętrznej witryny sieci Web, wybierz pozycję **Otwórz**.

    > [!div class="mx-imgBorder"]
    > ![Śledzenie postępu eksperymentu](./media/tutorial-train-deploy-image-classification-model-vscode/track-experiment-progress.png)

Gdy model jest gotowy do szkolenia, etykieta stanu obok węzła uruchamiania aktualizuje do "ukończone".

## <a name="register-the-model"></a>Rejestrowanie modelu

Teraz, gdy masz już szkolony model, możesz zarejestrować go w obszarze roboczym. 

Aby zarejestrować model:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning.
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **TeamWorkspace > eksperymenty > mnist ręcznie** .
1. Pobierz dane wyjściowe modelu wygenerowane na podstawie szkolenia modelu. Kliknij prawym przyciskiem myszy węzeł uruchamiania **1** i wybierz polecenie **Pobierz dane wyjściowe**. 

    > [!div class="mx-imgBorder"]
    > ![Pobierz dane wyjściowe modelu](./media/tutorial-train-deploy-image-classification-model-vscode/download-outputs.png)

1. Wybierz katalog, do którego mają zostać zapisane pobrane dane wyjściowe. Domyślnie dane wyjściowe są umieszczane w katalogu aktualnie otwartym w Visual Studio Code.
1. Kliknij prawym przyciskiem myszy węzeł **modele** i wybierz polecenie **zarejestruj model**.

    > [!div class="mx-imgBorder"]
    > ![Rejestrowanie modelu](./media/tutorial-train-deploy-image-classification-model-vscode/register-model.png)

1. Nazwij model "MNIST ręcznie-TensorFlow-model" i naciśnij klawisz **Enter**.
1. Model TensorFlow składa się z kilku plików. Wybierz pozycję **folder modelu** jako format ścieżki modelu z listy opcji. 
1. Wybierz `azureml_outputs/Run_1/outputs/outputs/model` katalog.

    Plik zawierający konfiguracje modelu jest wyświetlany w Visual Studio Code z podobną zawartością do poniższego:

    ```json
    {
        "modelName": "MNIST-TensorFlow-model",
        "tags": {
            "": ""
        },
        "modelPath": "c:\\Dev\\vscode-tools-for-ai\\mnist-vscode-docs-sample\\azureml_outputs\\Run_1\\outputs\\outputs\\model",
        "description": ""
    }
    ```

1. Po zakończeniu konfiguracji Zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

Po kilku minutach model zostanie wyświetlony w węźle *modele* .

## <a name="deploy-the-model"></a>Wdrażanie modelu

W Visual Studio Code można wdrożyć model jako usługę sieci Web, aby:

+ Azure Container Instances (ACI).
+ Usługa Azure Kubernetes Service (AKS).

Nie musisz tworzyć kontenera ACI, aby przetestować z wyprzedzeniem, ponieważ kontenery ACI są tworzone w razie potrzeby. Należy jednak skonfigurować klastry AKS z wyprzedzeniem. Aby uzyskać więcej informacji na temat opcji wdrażania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md) .

Aby wdrożyć usługę sieci Web jako ACI:

1. Na pasku działania Visual Studio Code wybierz ikonę **platformy Azure** . Zostanie wyświetlony widok Azure Machine Learning.
1. Rozwiń węzeł subskrypcji. 
1. Rozwiń węzeł **modele > TeamWorkspace** . 
1. Kliknij prawym przyciskiem myszy **model mnist ręcznie-TensorFlow** i wybierz polecenie **Wdróż usługę z zarejestrowanego modelu**.

    > [!div class="mx-imgBorder"]
    > ![Wdrażanie modelu](./media/tutorial-train-deploy-image-classification-model-vscode/deploy-model.png)

1. Wybierz **Azure Container Instances**.
1. Nazwij usługę "mnist ręcznie-tensorflow-svc" i naciśnij klawisz **Enter**.
1. Wybierz skrypt do uruchomienia w kontenerze, naciskając klawisz **Enter** w polu wejściowym i przeglądając `score.py` plik w `mnist-vscode-docs-sample` katalogu.
1. Podaj zależności, które są konieczne do uruchomienia skryptu, naciskając klawisz **Enter** w polu wejściowym i przeglądając `env.yml` plik w `mnist-vscode-docs-sample` katalogu.

    Plik zawierający konfiguracje modelu jest wyświetlany w Visual Studio Code z podobną zawartością do poniższego:

    ```json
    {
        "name": "mnist-tensorflow-svc",
        "imageConfig": {
            "runtime": "python",
            "executionScript": "score.py",
            "dockerFile": null,
            "condaFile": "env.yml",
            "dependencies": [],
            "schemaFile": null,
            "enableGpu": false,
            "description": ""
        },
        "deploymentConfig": {
            "cpu_cores": 1,
            "memory_gb": 10,
            "tags": {
                "": ""
            },
            "description": ""
        },
        "deploymentType": "ACI",
        "modelIds": [
            "MNIST-TensorFlow-model:1"
        ]
    }
    ```

1. Po zakończeniu konfiguracji Zapisz ją, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Save and Continue
    ```

W tym momencie żądanie jest wysyłane do platformy Azure w celu wdrożenia usługi sieci Web. Ten proces trwa kilka minut. Po wdrożeniu Nowa usługa zostanie wyświetlona w węźle *punkty końcowe* .

## <a name="next-steps"></a>Następne kroki

* Aby poznać wskazówki dotyczące uczenia się z Azure Machine Learning poza Visual Studio Code, zobacz [Samouczek: uczenie modeli przy użyciu Azure Machine Learning](tutorial-train-models-with-aml.md).
* Aby zapoznać się z przewodnikiem dotyczącym sposobu edytowania, uruchamiania i debugowania kodu lokalnie, zobacz [samouczek Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
