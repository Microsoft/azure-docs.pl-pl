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
ms.openlocfilehash: 8efc0301296f64abfe9b827e943882e930929086
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575333"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Samouczek: uczenie i wdrażanie modelu Azure Machine Learning

W tym artykule wykonamy następujące zadania:

* Użyj Azure Notebooks do uczenia modelu uczenia maszynowego.
* Pakuj model szkolony jako obraz kontenera.
* Wdróż obraz kontenera jako moduł Azure IoT Edge.

Azure Notebooks korzystać z obszaru roboczego Azure Machine Learning, czyli podstawy do eksperymentowania, uczenia i wdrażania modeli uczenia maszynowego.

Kroki opisane w tym artykule mogą być zwykle wykonywane przez analityków danych.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="set-up-azure-notebooks"></a>Skonfiguruj Azure Notebooks

Używamy Azure Notebooks do hostowania dwóch notesów Jupyter i plików pomocniczych. W tym miejscu utworzymy i skonfigurujemy projekt Azure Notebooks. Jeśli nie korzystasz z Jupyter i/lub Azure Notebooks, poniżej przedstawiono kilka dokumentów wprowadzających:

* **Szybki Start:** [Tworzenie i udostępnianie notesu](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Samouczek:** [Tworzenie i uruchamianie notesu Jupyter przy użyciu języka Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Używanie Azure Notebooks zapewnia spójne środowisko dla tego ćwiczenia.

> [!NOTE]
> Po skonfigurowaniu usługi Azure Notebooks można uzyskać dostęp z dowolnej maszyny. Podczas instalacji należy użyć maszyny wirtualnej tworzenia, która ma wszystkie pliki, które będą potrzebne.

### <a name="create-an-azure-notebooks-account"></a>Utwórz konto Azure Notebooks

Aby użyć Azure Notebooks, musisz utworzyć konto. Konta notesu platformy Azure są niezależne od subskrypcji platformy Azure.

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com).

1. Kliknij przycisk **Zaloguj** w prawym górnym rogu strony.

1. Zaloguj się przy użyciu konta służbowego (Azure Active Directory) lub konta osobistego (konto Microsoft).

1. Jeśli wcześniej nie korzystasz z Azure Notebooks, zostanie wyświetlony monit o przyznanie dostępu do aplikacji Azure Notebooks.

1. Utwórz identyfikator użytkownika dla Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Przekaż pliki notesu Jupyter

Będziemy przekazywać przykładowe pliki notesu do nowego projektu Azure Notebooks.

1. Na stronie użytkownika nowego konta wybierz pozycję **Moje projekty** z górnego paska menu.

1. Dodaj nowy projekt, wybierając **+** przycisk.

1. W oknie dialogowym **Utwórz nowy projekt** Podaj **nazwę projektu**. 

1. Pozostaw opcję **publiczny** i **plik Readme** niezaznaczone, ponieważ nie ma potrzeby, aby projekt był publiczny lub miał plik Readme.

1. Wybierz przycisk **Utwórz**.

1. Wybierz pozycję **Przekaż** (ikona strzałki w górę) i wybierz pozycję **z komputera**.

1. Wybierz pozycję **Wybierz pliki**.

1. Przejdź do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Zaznacz wszystkie pliki na liście, a następnie kliknij przycisk **Otwórz**.

1. Zaznacz pole wyboru **zaufania zawartości tych plików** .

1. Wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie, a następnie wybierz pozycję **gotowe** po zakończeniu procesu.

### <a name="azure-notebook-files"></a>Pliki notesu platformy Azure

Przejrzyjmy pliki przekazane do projektu Azure Notebooks. Działania w tej części samouczka obejmują między innymi pliki notesów, które korzystają z kilku plików pomocniczych.

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

## <a name="run-azure-notebooks"></a>Uruchom Azure Notebooks

Po utworzeniu projektu można uruchomić notesy. 

1. Na stronie projektu wybierz pozycję **01-TurboFan \_ regresja. ipynb**.

    ![Wybierz pierwszy Notes do uruchomienia](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

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

1. Na stronie projekt Azure Notebooks wybierz pozycję **Pokaż ukryte elementy** , aby nazwy elementów zaczynające się od okresu pojawiły się.

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

W tym artykule użyto dwóch Jupyterych notesów uruchomionych w Azure Notebooks, aby użyć danych z urządzeń TurboFan do uczenia pozostałej klasyfikatora okresu użytkowania (pozostałego czasu eksploatacji), w celu zapisania klasyfikatora jako modelu, utworzenia obrazu kontenera oraz wdrożenia i przetestowania obrazu jako usługi sieci Web.

Przejdź do następnego artykułu, aby utworzyć urządzenie IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzenia usługi IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
