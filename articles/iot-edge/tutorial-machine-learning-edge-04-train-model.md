---
title: 'Samouczek: uczenie i wdrażanie modelu — Machine Learning na Azure IoT Edge'
description: W ramach tego samouczka nauczysz model uczenia maszynowego przy użyciu Azure Machine Learning a następnie Spakuj model jako obraz kontenera, który można wdrożyć jako moduł Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121150"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: uczenie i wdrażanie modelu Azure Machine Learning

W tym artykule wykonamy następujące zadania:

* Użyj Azure Machine Learning Studio do uczenia modelu uczenia maszynowego.
* Pakuj model szkolony jako obraz kontenera.
* Wdróż obraz kontenera jako moduł Azure IoT Edge.

Azure Machine Learning Studio to podstawowy blok służący do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.

Kroki opisane w tym artykule mogą być zwykle wykonywane przez analityków danych.

W tej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Twórz notesy Jupyter w Obszar roboczy usługi Azure Machine Learning, aby uczenie modelu uczenia maszynowego.
> * Konteneryzowanie przeszkolony model uczenia maszynowego.
> * Utwórz moduł Azure IoT Edge na podstawie modelu uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="set-up-azure-machine-learning"></a>Skonfiguruj Azure Machine Learning 

Używamy Azure Machine Learning Studio do hostowania dwóch notesów Jupyter i plików pomocniczych. W tym miejscu utworzymy i skonfigurujemy projekt Azure Machine Learning. Jeśli nie korzystasz z Jupyter i/lub Azure Machine Learning Studio, poniżej przedstawiono kilka dokumentów wprowadzających:

* **Notesy Jupyter:** [Praca z notesami Jupyter w Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [wprowadzenie do Azure Machine Learning w notesach Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> Po skonfigurowaniu usługi Azure Machine Learning można uzyskać dostęp z dowolnej maszyny. Podczas instalacji należy użyć maszyny wirtualnej tworzenia, która ma wszystkie pliki, które będą potrzebne.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Zainstaluj rozszerzenie Azure Machine Learning Visual Studio Code
Na maszynie wirtualnej deweloperskiej należy zainstalować to rozszerzenie. VS Code Jeśli używasz programu na innym wystąpieniu, zainstaluj ponownie rozszerzenie, zgodnie z opisem w [tym miejscu.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Utwórz konto Azure Machine Learning  
Aby udostępnić zasoby i uruchamiać obciążenia na platformie Azure, musisz zalogować się przy użyciu poświadczeń konta platformy Azure.

1. W Visual Studio Code Otwórz paletę poleceń, wybierając pozycję **Widok**  >  **paleta poleceń** na pasku menu. 

1. Wprowadź polecenie `Azure: Sign In` w palecie poleceń, aby uruchomić proces logowania. Postępuj zgodnie z instrukcjami, aby ukończyć logowanie. 

1. Utwórz wystąpienie usługi Azure środowisko obliczeniowe usługi ML, aby uruchomić obciążenie. Za pomocą palety poleceń wprowadź polecenie `Azure ML: Create Compute` . 
1. Wybierz swoją subskrypcję platformy Azure
1. Wybierz pozycję **+ Utwórz nowy obszar roboczy usługi Azure ml** i wprowadź nazwę `turbofandemo` .
1. Wybierz grupę zasobów, która była używana w tej wersji demonstracyjnej.
1. Postęp tworzenia obszaru roboczego powinien być widoczny w prawym dolnym rogu okna VS Code: **Tworzenie obszaru roboczego: turobofandemo** (może to potrwać minutę lub dwa). 
1. Poczekaj na pomyślne utworzenie obszaru roboczego. Powinienmy powiedzieć, że **utworzono Turbofandemo Azure ml Workspace**.


### <a name="upload-jupyter-notebook-files"></a>Przekaż pliki Jupyter Notebook

Będziemy przekazywać przykładowe pliki notesu do nowego obszaru roboczego usługi Azure ML.

1. Przejdź do ml.azure.com i zaloguj się.
1. Wybierz katalog Microsoft, subskrypcję platformy Azure i nowo utworzony obszar roboczy usługi Azure ML.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Wybierz obszar roboczy usługi Azure ML." :::

1. Po zalogowaniu się do obszaru roboczego usługi Azure ML przejdź do sekcji **notesy** przy użyciu menu po lewej stronie.
1. Wybierz kartę **Moje pliki** .

1. Wybieranie opcji **Przekaż** (ikona strzałki w górę) 


1. Przejdź do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Zaznacz wszystkie pliki na liście, a następnie kliknij przycisk **Otwórz**.

1. Zaznacz pole wyboru **zaufania zawartości tych plików** .

1. Wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie, a następnie wybierz pozycję **gotowe** po zakończeniu procesu.

### <a name="jupyter-notebook-files"></a>Pliki Jupyter Notebook

Przejrzyjmy pliki przekazane do obszaru roboczego usługi Azure ML. Działania w tej części samouczka obejmują między innymi pliki notesów, które korzystają z kilku plików pomocniczych.

* **01-TurboFan \_ regresja. ipynb:** ten Notes używa obszaru roboczego usługi Machine Learning, aby utworzyć i uruchomić eksperyment uczenia maszynowego. W szerokim zakresie Notes wykonuje następujące czynności:

  1. Pobiera dane z konta usługi Azure Storage, które zostało wygenerowane przez zespół.
  1. Eksploruje i przygotowuje dane, a następnie używa danych do uczenia modelu klasyfikatora.
  1. Oceń model z eksperymentu przy użyciu zestawu danych testowych (test \_FD003.txt).
  1. Publikuje najlepszy model klasyfikatora w obszarze roboczym usługi Machine Learning.

* **02 — TurboFan \_ Deploy \_ model. ipynb:** ten Notes przyjmuje model utworzony w poprzednim notesie i używa go do utworzenia obrazu kontenera gotowego do wdrożenia na urządzeniu Azure IoT Edge. Notes wykonuje następujące czynności:

  1. Tworzy skrypt oceniania dla modelu.
  1. Tworzy obraz kontenera przy użyciu modelu klasyfikatora, który został zapisany w obszarze roboczym usługi Machine Learning.
  1. Wdraża obraz jako usługę sieci Web w wystąpieniu kontenera platformy Azure.
  1. Używa usługi sieci Web do walidacji modelu i obrazu działa zgodnie z oczekiwaniami. Sprawdzony obraz zostanie wdrożony na naszym urządzeniu IoT Edge w części [Tworzenie i wdrażanie niestandardowych modułów IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) w tym samouczku.

* **Test \_FD003.txt:** ten plik zawiera dane, które będą używane jako nasz zestaw testów podczas walidacji przeszkolonego klasyfikatora. Wybrano użycie danych testowych, zgodnie z opisem dla oryginalnego konkursu, jako nasz zestaw testów dla uproszczenia.

* **Pozostałego czasu eksploatacji \_FD003.txt:** ten plik zawiera pozostały okres istnienia (pozostałego czasu eksploatacji) dla ostatniego cyklu każdego urządzenia w \_ plikuFD003.txt testowym. \\ \\ \\ Aby uzyskać szczegółowy opis danych, zobacz readme.txt i Modeling.pdf propagacji uszkodzonych plików w C: Source IoTEdgeAndMlSample Data \\ TurboFan.

* **Utils.py:** Zawiera zestaw funkcji narzędzia Python do pracy z danymi. Pierwszy Notes zawiera szczegółowy opis funkcji.

* **README.MD:** Plik Readme opisujący korzystanie z notesów.  

## <a name="run-jupyter-notebooks"></a>Uruchamianie notesów programu Jupyter

Po utworzeniu obszaru roboczego możesz uruchomić notesy. 

1. Na stronie **Moje pliki** wybierz pozycję **01-TurboFan \_ regresja. ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Wybierz pierwszy Notes do uruchomienia. ":::

1. Jeśli Notes jest wymieniony jako **niezaufany**, kliknij widżet **niezaufany** w prawym górnym rogu notesu. Po wyświetleniu okna dialogowego wybierz pozycję **Ufaj**.

1. Aby uzyskać najlepsze wyniki, zapoznaj się z dokumentacją dla każdej komórki i uruchom ją pojedynczo. Wybierz pozycję **Uruchom** na pasku narzędzi. Później będzie można uruchomić wiele komórek. Można zignorować ostrzeżenia o uaktualnieniu i wycofaniu.

    Gdy komórka jest uruchomiona, wyświetla gwiazdkę między nawiasami kwadratowymi ([ \* ]). Po zakończeniu operacji komórki gwiazdka jest zastępowana liczbą, a odpowiednie dane wyjściowe mogą pojawić się. Komórki w notesie kompilują się sekwencyjnie i tylko jeden może być uruchomiony w danym momencie.

    Możesz również użyć opcji Run z menu **komórka** , `Ctrl`  +  `Enter` Aby uruchomić komórkę i `Shift`  +  `Enter` uruchomić komórkę i przejść do następnej komórki.

    > [!TIP]
    > Aby zapewnić spójne operacje na komórkach, należy unikać uruchamiania tego samego notesu z wielu kart w przeglądarce.

1. W komórce, która jest zgodna z instrukcjami dotyczącymi **właściwości globalnych** , wpisz wartości w polu subskrypcja, ustawienia i zasoby platformy Azure. Następnie uruchom komórkę.

    ![Ustawianie właściwości globalnych w notesie](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Po uruchomieniu, w komórce poprzedniej do **szczegółów obszaru roboczego**, poszukaj linku, który nakazuje zalogowanie się w celu uwierzytelnienia:

    ![Monit logowania w celu uwierzytelnienia urządzenia](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otwórz link i wprowadź określony kod. Ta procedura logowania służy do uwierzytelniania notesu Jupyter w celu uzyskania dostępu do zasobów platformy Azure przy użyciu międzyplatformowego interfejsu wiersza polecenia Microsoft Azure.  

    ![Potwierdzenie uwierzytelniania aplikacji na urządzeniu](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. W komórce, która poprzedza **wyniki**, skopiuj wartość z identyfikatora uruchomienia i wklej ją dla identyfikatora uruchomienia w komórce, która następuje po odniesieniu **przebiegu**.

   ![Kopiuj identyfikator przebiegu między komórkami](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Uruchom pozostałe komórki w notesie.

1. Zapisz Notes i wróć do strony projektu.

1. Otwórz **przystawkę TurboFan \_ Deploy \_ model. ipynb** i uruchom każdą komórkę. Musisz zalogować się w celu uwierzytelnienia w komórce, która następuje po **skonfigurowaniu obszaru roboczego**.

1. Zapisz Notes i wróć do strony projektu.

### <a name="verify-success"></a>Weryfikowanie sukcesu

Aby sprawdzić, czy notesy zostały ukończone pomyślnie, sprawdź, czy utworzono kilka elementów.

1. Na karcie **Moje pliki** notesów usługi Azure ml wybierz pozycję **Odśwież**.

1. Sprawdź, czy zostały utworzone następujące pliki:

    | Plik | Opis |
    | --- | --- |
    | ./aml_config/.azureml/config.jsna | Plik konfiguracji służący do tworzenia Obszar roboczy usługi Azure Machine Learning. |
    | ./aml_config/model_config.jswłączone | Plik konfiguracji, który będzie musiał wdrożyć model w obszarze roboczym **turbofanDemo** Machine Learning na platformie Azure. |
    | MyENV. yml| Zawiera informacje o zależnościach wdrożonego modelu Machine Learning.|

1. Sprawdź, czy zostały utworzone następujące zasoby platformy Azure. Nazwy niektórych zasobów są dodawane losowo.

    | Zasób platformy Azure | Nazwa |
    | --- | --- |
    | Machine Learning obszar roboczy | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Usługi Application Insights | turbofaninsightxxxxxxxx |
    | Usługa Key Vault | turbofankeyvaultbxxxxxxxx |
    | Magazyn | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Debugowanie

Możesz wstawić instrukcje języka Python do notesu do debugowania, takie jak `print()` polecenie do wyświetlania wartości. Jeśli widzisz zmienne lub obiekty, które nie są zdefiniowane, uruchom komórki, w których są one po raz pierwszy zadeklarowane lub utworzone.

Jeśli chcesz ponownie wykonać te notesy, może być konieczne usunięcie wcześniej utworzonych plików i zasobów platformy Azure.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Zaczekaj na oczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym artykule wykorzystano dwa notesy Jupyter uruchomione na platformie Azure ML Studio do użycia danych z urządzeń TurboFan do uczenia pozostałego klasyfikatora okresu użytkowania (pozostałego czasu eksploatacji) w celu zapisania klasyfikatora jako modelu, utworzenia obrazu kontenera oraz wdrożenia i przetestowania obrazu jako usługi sieci Web.

Przejdź do następnego artykułu, aby utworzyć urządzenie IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia usługi IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)