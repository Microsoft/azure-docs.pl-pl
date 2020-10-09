---
title: 'Samouczek: Konfigurowanie rozszerzenia Visual Studio Code (wersja zapoznawcza)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować rozszerzenie Azure Machine Learning Visual Studio Code.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
ms.openlocfilehash: 48fb2ce3672634af202500ccb256e7730933fff8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86199762"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension-preview"></a>Konfigurowanie rozszerzenia Visual Studio Code Azure Machine Learning (wersja zapoznawcza)

Dowiedz się, jak instalować i uruchamiać skrypty przy użyciu rozszerzenia Azure Machine Learning Visual Studio Code.

W tym samouczku zapoznasz się z następującymi zadaniami:

> [!div class="checklist"]
> * Instalowanie rozszerzenia Azure Machine Learning Visual Studio Code
> * Zaloguj się do konta platformy Azure z poziomu Visual Studio Code
> * Użyj rozszerzenia Azure Machine Learning, aby uruchomić przykładowy skrypt

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz takiego konta, zarejestruj się, aby wypróbować [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
- Program Visual Studio Code. Jeśli go nie masz, [Zainstaluj go](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

1. Otwórz program Visual Studio Code.
1. Wybierz ikonę **rozszerzenia** na **pasku działania** , aby otworzyć widok rozszerzenia.
1. W widoku rozszerzeń Wyszukaj ciąg "Azure Machine Learning".
1. Wybierz pozycję **Zainstaluj**.

    > [!div class="mx-imgBorder"]
    > ![Zainstaluj rozszerzenie Azure Machine Learning VS Code](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Alternatywnie możesz zainstalować rozszerzenie Azure Machine Learning za pośrednictwem Visual Studio Marketplace, [pobierając Instalatora bezpośrednio](https://aka.ms/vscodetoolsforai). 

Pozostała część kroków tego samouczka została przetestowana z **wersją 0.6.8** rozszerzenia.

## <a name="sign-in-to-your-azure-account"></a>Zaloguj się do konta platformy Azure

Aby udostępnić zasoby i uruchamiać obciążenia na platformie Azure, musisz zalogować się przy użyciu poświadczeń konta platformy Azure. Aby ułatwić zarządzanie kontami, Azure Machine Learning automatycznie instaluje rozszerzenie konta platformy Azure. Odwiedź następującą witrynę, aby [dowiedzieć się więcej o rozszerzeniu konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Otwórz paletę poleceń, wybierając pozycję **wyświetl > paleta poleceń** na pasku menu. 
1. Wprowadź polecenie "Azure: Sign in" w palecie poleceń, aby uruchomić proces logowania.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Uruchamianie skryptu szkoleniowego modelu uczenia maszynowego na platformie Azure

Po zalogowaniu się do platformy Azure przy użyciu poświadczeń konta wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, jak używać rozszerzenia do uczenia modelu uczenia maszynowego.

1. Pobierz i rozpakuj [vs Code narzędzia dla REPOZYTORIUM AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) w dowolnym miejscu na komputerze.
1. Otwórz `mnist-vscode-docs-sample` katalog w Visual Studio Code.
1. Na pasku działania wybierz ikonę **platformy Azure** .
1. Wybierz ikonę **Uruchom eksperyment** w górnej części widoku Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Uruchom eksperyment](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Gdy zostanie rozwinięta paleta poleceń, postępuj zgodnie z monitami.

    > [!NOTE]
    > Jeśli masz już istniejące zasoby Azure Machine Learning, zapoznaj się z tematem [jak uruchamiać eksperymenty w vs Code przewodniku](./how-to-manage-resources-vscode.md#run-experiment).

    1. Wybierz swoją subskrypcję platformy Azure.
    1. Z listy środowiska wybierz pozycję **plik zależności Conda**.
    1. Naciśnij klawisz **Enter** , aby przeglądać plik zależności Conda. Ten plik zawiera zależności wymagane do uruchomienia skryptu. W takim przypadku plik zależności jest `env.yml` plikiem znajdującym się w `mnist-vscode-docs-sample` katalogu.
    1. Naciśnij klawisz **Enter** , aby przeglądać plik skryptu szkoleniowego. Jest to plik, który zawiera kod do modelu uczenia maszynowego, który klasyfikuje obrazy cyfr odręcznych. W takim przypadku skrypt do uczenia modelu jest `train.py` plikiem znajdującym się w `mnist-vscode-docs-sample` katalogu.

1. W tym momencie plik konfiguracji podobny do przedstawionego poniżej pojawia się w edytorze tekstu. Konfiguracja zawiera informacje wymagane do uruchomienia zadania szkoleniowego, takie jak plik zawierający kod służący do uczenia modelu i wszystkie zależności języka Python określone w poprzednim kroku.

    ```json
    {
        "workspace": "WS06271500",
        "resourceGroup": "WS06271500-rg2",
        "location": "South Central US",
        "experiment": "WS06271500-exp1",
        "compute": {
            "name": "WS06271500-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS06271500-com1-rc1",
            "environment": {
                "name": "WS06271500-env1",
                "conda_dependencies": [
                    "python=3.6.2",
                    "tensorflow=1.15.0",
                    "pip"
                ],
                "pip_dependencies": [
                    "azureml-defaults"
                ],
                "environment_variables": {}
            }
        }
    }
    ```

1. Gdy Twoja konfiguracja zostanie zazadowalająca, Prześlij eksperyment, otwierając paletę poleceń i wprowadzając następujące polecenie:

    ```text
    Azure ML: Submit Experiment
    ```

    Spowoduje to wysłanie `train.py` pliku konfiguracji do obszaru roboczego Azure Machine Learning. Zadanie szkolenia jest następnie uruchamiane w zasobie obliczeniowym na platformie Azure.

### <a name="track-the-progress-of-the-training-script"></a>Śledź postęp skryptu szkoleniowego

Uruchamianie skryptu może potrwać kilka minut. Aby śledzić postęp:

1. Na pasku działania wybierz ikonę **platformy Azure** .
1. Rozwiń węzeł subskrypcji.
1. Rozwiń węzeł aktualnie działającego eksperymentu. Znajduje się on w węźle, w `{workspace}/Experiments/{experiment}` którym wartości obszaru roboczego i eksperymentu są takie same jak właściwości zdefiniowane w pliku konfiguracji.
1. Wszystkie uruchomienia eksperymentu są wymienione na liście, a także ich stan. Aby uzyskać najnowszy stan, kliknij ikonę odświeżania w górnej części widoku Azure Machine Learning.

    > [!div class="mx-imgBorder"]
    > ![Śledzenie postępu eksperymentu](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Pobierz przeszkolony model

Po zakończeniu eksperymentu dane wyjściowe są modelem szkolonym. Aby pobrać dane wyjściowe lokalnie:

1. Kliknij prawym przyciskiem myszy najnowszy przebieg i wybierz pozycję **Pobierz dane wyjściowe**.

    > [!div class="mx-imgBorder"]
    > ![Pobierz przeszkolony model](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Wybierz lokalizację, w której mają zostać zapisane dane wyjściowe.
1. Folder o nazwie przebiegu jest pobierany lokalnie. Przejdź do tej strony.
1. Pliki modelu znajdują się w `outputs/outputs/model` katalogu.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: uczenie i wdrażanie modelu TensorFlow klasyfikacji obrazów przy użyciu rozszerzenia Visual Studio Code Azure Machine Learning](tutorial-train-deploy-image-classification-model-vscode.md).
