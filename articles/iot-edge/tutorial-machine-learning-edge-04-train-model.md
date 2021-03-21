---
title: 'Samouczek: uczenie i wdrażanie modelu — Machine Learning na Azure IoT Edge'
description: W ramach tego samouczka nauczysz model uczenia maszynowego za pomocą Azure Machine Learning a następnie Spakuj model jako obraz kontenera, który można wdrożyć jako moduł Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463106"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: uczenie i wdrażanie modelu Azure Machine Learning

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

W tym artykule wykonamy następujące zadania:

* Użyj Azure Machine Learning Studio do uczenia modelu uczenia maszynowego.
* Pakuj model szkolony jako obraz kontenera.
* Wdróż obraz kontenera jako moduł Azure IoT Edge.

Machine Learning Studio to podstawowy blok służący do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.

Kroki opisane w tym artykule mogą być zwykle wykonywane przez analityków danych.

W tej części samouczka dowiesz się, jak:

> [!div class="checklist"]
> * Utwórz notesy Jupyter w obszarze roboczym Azure Machine Learning, aby szkolić model uczenia maszynowego.
> * Konteneryzowanie przeszkolony model uczenia maszynowego.
> * Utwórz moduł IoT Edge na podstawie modelu uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został już wprowadzony bezpośrednio, zobacz [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) w serii.

## <a name="set-up-azure-machine-learning"></a>Skonfiguruj Azure Machine Learning

Używamy Machine Learning Studio do hostowania dwóch notesów Jupyter i plików pomocniczych. W tym miejscu utworzymy i skonfigurujemy projekt Machine Learning. Jeśli nie korzystasz z Jupyter lub Machine Learning Studio, poniżej przedstawiono dwa dokumenty wprowadzające:

* **Jupyter Notebook**: [Praca z notesami Jupyter w programie Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning**: [wprowadzenie do Azure Machine Learning w notesach Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Po skonfigurowaniu usługi Machine Learning można uzyskać dostęp z dowolnej maszyny. Podczas instalacji należy użyć maszyny wirtualnej tworzenia, która ma wszystkie pliki, które będą potrzebne.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Zainstaluj rozszerzenie Azure Machine Learning Visual Studio Code

Na maszynie wirtualnej deweloperskiej należy zainstalować to rozszerzenie. Visual Studio Code Jeśli używasz programu na innym wystąpieniu, zainstaluj ponownie rozszerzenie zgodnie z opisem w temacie [Konfigurowanie rozszerzenia Visual Studio Code](../machine-learning/tutorial-setup-vscode-extension.md).

### <a name="create-an-azure-machine-learning-account"></a>Utwórz konto Azure Machine Learning

Aby udostępnić zasoby i uruchamiać obciążenia na platformie Azure, zaloguj się przy użyciu poświadczeń konta platformy Azure.

1. W Visual Studio Code Otwórz paletę poleceń, wybierając pozycję **Widok**  >  **paleta poleceń** na pasku menu.

1. Wprowadź polecenie `Azure: Sign In` w palecie poleceń, aby uruchomić proces logowania. Postępuj zgodnie z instrukcjami, aby ukończyć logowanie.

1. Utwórz wystąpienie obliczeniowe Machine Learning, aby uruchomić obciążenie. W palecie poleceń wprowadź polecenie `Azure ML: Create Compute` .
1. Wybierz swoją subskrypcję platformy Azure.
1. Wybierz pozycję **+ Utwórz nowy obszar roboczy usługi ml platformy Azure**, a następnie wprowadź nazwę **turbofandemo**.
1. Wybierz grupę zasobów, która była używana w tej wersji demonstracyjnej.
1. Postęp tworzenia obszaru roboczego powinien być widoczny w prawym dolnym rogu okna Visual Studio Code: **Tworzenie obszaru roboczego: turobofandemo**. Ten krok może potrwać minutę lub dwa.
1. Poczekaj na pomyślne utworzenie obszaru roboczego. Powinienmy powiedzieć, że **utworzono Turbofandemo Azure ml Workspace**.

### <a name="upload-jupyter-notebook-files"></a>Przekaż pliki Jupyter Notebook

Przekażemy przykładowe pliki notesu do nowego obszaru roboczego Machine Learning.

1. Przejdź do ml.azure.com i zaloguj się.
1. Wybierz katalog Microsoft, subskrypcję platformy Azure i nowo utworzony obszar roboczy Machine Learning.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Zrzut ekranu pokazujący wybór obszaru roboczego Azure Machine Learning." :::

1. Po zalogowaniu się do obszaru roboczego Machine Learning przejdź do sekcji **notesy** przy użyciu menu po lewej stronie.
1. Wybierz kartę **Moje pliki** .

1. Wybierz pozycję **Przekaż** (ikona strzałki w górę).

1. Przejdź do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Zaznacz wszystkie pliki na liście, a następnie wybierz pozycję **Otwórz**.

1. Zaznacz pole wyboru **ufaj zawartości tych plików** .

1. Wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie. Następnie wybierz pozycję **gotowe** po zakończeniu procesu.

### <a name="jupyter-notebook-files"></a>Pliki Jupyter Notebook

Przejrzyjmy pliki przekazane do obszaru roboczego Machine Learning. Działania w tej części samouczka obejmują między innymi pliki notesów, które korzystają z kilku plików pomocniczych.

* **01-TurboFan \_ regresja. ipynb**: ten Notes używa obszaru roboczego Machine Learning do tworzenia i uruchamiania eksperymentu uczenia maszynowego. W szerokim zakresie Notes wykonuje następujące czynności:

  1. Pobiera dane z konta usługi Azure Storage, które zostało wygenerowane przez zespół.
  1. Eksploruje i przygotowuje dane, a następnie używa tych danych do uczenia modelu klasyfikatora.
  1. Oblicza model z eksperymentu przy użyciu zestawu danych testowych (test \_FD003.txt).
  1. Publikuje najlepszy model klasyfikatora w obszarze roboczym Machine Learning.

* **02 — TurboFan \_ Deploy \_ model. ipynb**: ten Notes przyjmuje model utworzony w poprzednim notesie i używa go do utworzenia obrazu kontenera gotowego do wdrożenia na urządzeniu IoT Edge. Notes wykonuje następujące czynności:

  1. Tworzy skrypt oceniania dla modelu.
  1. Tworzy obraz kontenera przy użyciu modelu klasyfikatora, który został zapisany w obszarze roboczym Machine Learning.
  1. Wdraża obraz jako usługę sieci Web na Azure Container Instances.
  1. Używa usługi sieci Web do walidacji modelu i obrazu działa zgodnie z oczekiwaniami. Sprawdzony obraz zostanie wdrożony na naszym urządzeniu IoT Edge w części [Tworzenie i wdrażanie niestandardowych modułów IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) w tym samouczku.

* **Test \_FD003.txt**: ten plik zawiera dane, które będą używane jako nasz zestaw testów podczas weryfikacji przeszkolonego klasyfikatora. Wybrano użycie danych testowych, zgodnie z opisem dla oryginalnego konkursu, jako nasz zestaw testów dla uproszczenia.
* **Pozostałego czasu eksploatacji \_FD003.txt**: ten plik zawiera pozostały okres istnienia (pozostałego czasu eksploatacji) dla ostatniego cyklu każdego urządzenia w \_ plikuFD003.txt testowym. Zapoznaj się z informacjami o readme.txt i propagacji uszkodzeń Modeling.pdf plików w języku C: \\ Source \\ IoTEdgeAndMlSample \\ Data \\ TurboFan, aby uzyskać szczegółowe wyjaśnienie danych.
* **Utils.py**: ten plik zawiera zestaw funkcji narzędzia Python do pracy z danymi. Pierwszy Notes zawiera szczegółowy opis funkcji.
* **README.MD**: ten plik README opisuje korzystanie z notesów.

## <a name="run-the-jupyter-notebooks"></a>Uruchamianie notesów Jupyter

Po utworzeniu obszaru roboczego możesz uruchomić notesy.

1. Na stronie **Moje pliki** wybierz pozycję **01-TurboFan \_ regresja. ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Zrzut ekranu pokazujący wybór pierwszego notesu do uruchomienia.":::

1. Jeśli Notes jest wymieniony jako **niezaufany**, Wybierz widżet **niezaufany** w prawym górnym rogu notesu. Gdy pojawi się okno dialogowe, wybierz pozycję **Ufaj**.

1. Aby uzyskać najlepsze wyniki, zapoznaj się z dokumentacją dla każdej komórki i uruchom ją pojedynczo. Wybierz pozycję **Uruchom** na pasku narzędzi. Później będzie można uruchomić wiele komórek. Można zignorować ostrzeżenia o uaktualnieniu i wycofaniu.

    Gdy komórka jest uruchomiona, wyświetla gwiazdkę między nawiasami kwadratowymi ([ \* ]). Po zakończeniu operacji komórki gwiazdka jest zastępowana liczbą i odpowiednie wyniki mogą pojawić się. Komórki w notesie kompilują się sekwencyjnie i tylko jedna komórka może być uruchamiana w danym momencie.

    Możesz również użyć opcji uruchamiania z menu **komórki** . Wybierz **kombinację klawiszy Ctrl + Enter** , aby uruchomić komórkę, a następnie wybierz **klawisze SHIFT + ENTER** , aby uruchomić komórkę i przejść do następnej komórki.

    > [!TIP]
    > Aby zapewnić spójne operacje na komórkach, należy unikać uruchamiania tego samego notesu z wielu kart w przeglądarce.

1. W komórce, która następuje po instrukcji **Set Global Properties** , wprowadź wartości dla subskrypcji, ustawień i zasobów platformy Azure. Następnie uruchom komórkę.

    ![Zrzut ekranu pokazujący Ustawianie właściwości globalnych w notesie.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Po uruchomieniu, w komórce poprzedniej do **szczegółów obszaru roboczego**, poszukaj linku, który nakazuje zalogowanie się w celu uwierzytelnienia.

    ![Zrzut ekranu pokazujący monit logowania na potrzeby uwierzytelniania urządzenia.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otwórz link i wprowadź określony kod. Ta procedura logowania służy do uwierzytelniania notesu Jupyter w celu uzyskania dostępu do zasobów platformy Azure przy użyciu międzyplatformowego interfejsu wiersza polecenia Microsoft Azure.

    ![Zrzut ekranu przedstawiający uwierzytelnianie aplikacji na urządzeniu.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. W komórce, która poprzedza **wyniki**, skopiuj wartość z identyfikatora uruchomienia i wklej ją dla identyfikatora uruchomienia w komórce, która następuje po odniesieniu **przebiegu**.

   ![Zrzut ekranu pokazujący kopiowanie identyfikatora przebiegu między komórkami.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Uruchom pozostałe komórki w notesie.

1. Zapisz Notes i wróć do strony projektu.

1. Otwórz pozycję **02-TurboFan \_ Deploy \_ model. ipynb** i uruchom każdą komórkę. Musisz zalogować się w celu uwierzytelnienia w komórce, która następuje po **skonfigurowaniu obszaru roboczego**.

1. Zapisz Notes i wróć do strony projektu.

### <a name="verify-success"></a>Weryfikowanie sukcesu

Aby sprawdzić, czy notesy zostały ukończone pomyślnie, sprawdź, czy utworzono kilka elementów.

1. Na karcie Machine Learning notesy **Moje pliki** wybierz pozycję **Odśwież**.

1. Sprawdź, czy zostały utworzone następujące pliki.

    | Plik | Opis |
    | --- | --- |
    | ./aml_config/.azureml/config.jsna | Plik konfiguracji służący do tworzenia obszaru roboczego Machine Learning. |
    | ./aml_config/model_config.jswłączone | Plik konfiguracji, który będzie musiał wdrożyć model w obszarze roboczym **turbofanDemo** Machine Learning na platformie Azure. |
    | MyENV. yml| Zawiera informacje o zależnościach wdrożonego modelu Machine Learning.|

1. Sprawdź, czy zostały utworzone następujące zasoby platformy Azure. Niektóre nazwy zasobów są dodawane losowo ze znakami.

    | Zasób platformy Azure | Nazwa |
    | --- | --- |
    | Obszar roboczy usługi Azure Machine Learning | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Debugowanie

Możesz wstawić instrukcje języka Python do notesu do debugowania, takie jak `print()` polecenie do wyświetlania wartości. Jeśli widzisz zmienne lub obiekty, które nie są zdefiniowane, uruchom komórki, w których są one po raz pierwszy zadeklarowane lub utworzone.

Jeśli chcesz ponownie wykonać te notesy, może być konieczne usunięcie wcześniej utworzonych plików i zasobów platformy Azure.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Poczekaj na wyczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto dwóch notesów Jupyter działających w Machine Learning Studio, aby używać danych z urządzeń TurboFan w celu:

- Uczenie klasyfikatora pozostałego czasu eksploatacji.
- Zapisz klasyfikator jako model.
- Utwórz obraz kontenera.
- Wdróż i przetestuj obraz jako usługę sieci Web.

Przejdź do następnego artykułu, aby utworzyć urządzenie IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia usługi IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)