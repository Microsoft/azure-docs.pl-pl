---
title: Samouczek — wdrażanie Azure Machine Learning na urządzeniu przy użyciu Azure IoT Edge
description: W tym samouczku utworzysz model Azure Machine Learning, a następnie wdrożono go jako moduł na urządzeniu brzegowym
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 292c82624103fc2eae46d8aecb5e85b2181e7938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463055"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Samouczek: wdrażanie usługi Azure Machine Learning jako modułu usługi IoT Edge (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Użyj Azure Notebooks do opracowania modułu uczenia maszynowego i wdrożenia go na urządzeniu z systemem Linux z Azure IoT Edge.
Moduły usługi IoT Edge umożliwiają wdrożenie kodu implementującego logikę biznesową bezpośrednio na urządzeniach usługi IoT Edge. Ten samouczek zawiera opis sposobu wdrażania modułu usługi Azure Machine Learning, który przewiduje awarię urządzenia na podstawie symulowanych danych dotyczących temperatury maszyny. Aby uzyskać więcej informacji na temat Azure Machine Learning na IoT Edge, zobacz [dokumentację Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Moduły usługi Azure Machine Learning w usłudze Azure IoT Edge są dostępne w publicznej wersji zapoznawczej.

Moduł usługi Azure Machine Learning tworzony w tym samouczku odczytuje dane środowiska wygenerowane przez urządzenie i oznacza, czy komunikaty wskazują wystąpienie anomalii, czy nie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz moduł Azure Machine Learning.
> * Wypchnij kontener modułu do usługi Azure Container Registry.
> * Wdróż moduł Azure Machine Learning na urządzeniu IoT Edge.
> * Wyświetlanie wygenerowanych danych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie usługi Azure IoT Edge:

* Korzystając z przewodników Szybki Start, można skonfigurować urządzenie z systemem [Linux](quickstart-linux.md) lub [urządzenie systemu Windows](quickstart.md).
* Moduł Azure Machine Learning nie obsługuje kontenerów systemu Windows.
* Moduł Azure Machine Learning nie obsługuje procesorów ARM.

Zasoby w chmurze:

* Usługa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w warstwie Bezpłatna lub Standardowa na platformie Azure.
* Obszar roboczy usługi Azure Machine Learning. Postępuj zgodnie z instrukcjami w temacie [Use the Azure Portal, aby rozpocząć pracę z Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) , aby je utworzyć i dowiedzieć się, jak z niej korzystać.
  * Zanotuj nazwę obszaru roboczego, grupę zasobów i Identyfikator subskrypcji. Te wartości są dostępne w obszarze roboczym przegląd w Azure Portal. Te wartości będą używane w dalszej części samouczka, aby połączyć plik Azure Notebooks z zasobami obszaru roboczego.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Tworzenie i wdrażanie modułu Azure Machine Learning

W tej sekcji przekonwertujesz przeszkolone pliki modelu uczenia maszynowego na kontener Azure Machine Learning. Wszystkie wymagane składniki obrazu platformy Docker znajdują się w [repozytorium zestawu narzędzi SI dla usługi Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Wykonaj następujące kroki, aby przekazać repozytorium do Microsoft Azure Notebooks, aby utworzyć kontener i wypchnąć go do Azure Container Registry.

1. Przejdź do projektu Azure Notebooks. Możesz uzyskać dostęp do obszaru roboczego Azure Machine Learning w [Azure Portal](https://portal.azure.com) lub, logując się do [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) przy użyciu konta platformy Azure.

2. Wybierz pozycję **Przekaż repozytorium GitHub**.

3. Podaj następującą nazwę repozytorium GitHub: `Azure/ai-toolkit-iot-edge` . Usuń zaznaczenie pola **publicznego** , jeśli chcesz zachować prywatny projekt. Wybierz pozycję **Importuj**.

4. Po zakończeniu importowania przejdź do nowego projektu **AI-Toolkit-IoT-Edge** i Otwórz folder **samouczek wykrywania anomalii IoT Edge** .

5. Sprawdź, czy projekt jest uruchomiony. W przeciwnym razie wybierz pozycję **Uruchom przy bezpłatnej obliczeń**.

   ![Uruchamianie przy bezpłatnych obliczeniach](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Otwórz **aml_config/config.jsna** pliku.

7. Edytuj plik konfiguracji, aby uwzględnić wartości identyfikatora subskrypcji platformy Azure, grupy zasobów w subskrypcji oraz nazwę obszaru roboczego Azure Machine Learning. Wszystkie te wartości można uzyskać z sekcji **Przegląd** obszaru roboczego na platformie Azure.

8. Zapisz plik konfiguracji.

9. Otwórz plik **00-anomalia-Detection-samouczek. ipynb** .

10. Po wyświetleniu monitu wybierz jądro **Python 3,6** , a następnie wybierz pozycję **Ustaw jądro**.

11. Edytuj pierwszą komórkę w notesie zgodnie z instrukcjami w komentarzach. Użyj tej samej grupy zasobów, identyfikatora subskrypcji i nazwy obszaru roboczego dodanej do pliku konfiguracji.

12. Uruchom komórki w notesie, zaznaczając je i wybierając polecenie **Uruchom** lub naciskając `Shift + Enter` .

    >[!TIP]
    >Niektóre komórki w notesie z samouczkiem wykrywania anomalii są opcjonalne, ponieważ tworzą zasoby, które niektórzy użytkownicy mogą lub nie mogą jeszcze nie, jak IoT Hub. Jeśli w pierwszej komórce umieścisz informacje o istniejących zasobach, zostaną wyświetlone błędy w przypadku uruchomienia komórek, które tworzą nowe zasoby, ponieważ platforma Azure nie będzie tworzyć zduplikowanych zasobów. Jest to dokładne i można zignorować błędy lub całkowicie pominąć te opcjonalne sekcje.

Wykonując wszystkie kroki w notesie, przeszkolony model wykrywania anomalii, skonstruowany jako obraz kontenera Docker i wypchnięciem tego obrazu do Azure Container Registry. Następnie przetestowano model i ostatecznie go wdrożono na urządzeniu IoT Edge.

## <a name="view-container-repository"></a>Wyświetl repozytorium kontenera

Sprawdź, czy obraz kontenera został pomyślnie utworzony i zapisany w rejestrze kontenerów platformy Azure skojarzonym ze środowiskiem uczenia maszynowego. W notesie użytym w poprzedniej sekcji automatycznie udostępniono obraz kontenera i poświadczenia rejestru na urządzeniu IoT Edge, ale należy wiedzieć, gdzie są przechowywane, aby można było później znaleźć informacje.

1. W [Azure Portal](https://portal.azure.com)przejdź do obszaru roboczego usługi Machine Learning.

2. Sekcja **Przegląd** zawiera szczegóły obszaru roboczego, a także powiązane z nim zasoby. Wybierz wartość **rejestru** , która powinna być nazwą obszaru roboczego, a następnie liczbami losowymi.

3. W rejestrze kontenerów w obszarze **usługi** wybierz pozycję **repozytoria**. Powinno zostać wyświetlone repozytorium o nazwie **tempanomalydetection** , które zostało utworzone przez Notes uruchomiony w poprzedniej sekcji.

4. Wybierz pozycję **tempanomalydetection**. Powinno być widoczne, że repozytorium ma jeden tag: **1**.

   Teraz, gdy znasz nazwę rejestru, nazwę repozytorium i tag, znasz pełną ścieżkę obrazu do kontenera. Ścieżki obrazów wyglądają jak **\<registry_name\> . azurecr.IO/tempanomalydetection:1**. Możesz użyć ścieżki obrazu do wdrożenia tego kontenera na urządzeniach usługi IoT Edge.

5. W rejestrze kontenerów w obszarze **Ustawienia** wybierz pozycję **klucze dostępu**. Powinna zostać wyświetlona liczba poświadczeń dostępu, w tym **serwer logowania** i **Nazwa użytkownika** oraz **hasło** administratora.

   Te poświadczenia można dołączyć do manifestu wdrożenia, aby umożliwić urządzeniu usługi IoT Edge dostęp do ściągania obrazów kontenera z rejestru.

Teraz wiesz, gdzie jest przechowywany obraz kontenera Machine Learning. W następnej sekcji omówiono procedurę wyświetlania kontenera działającego jako moduł na urządzeniu IoT Edge.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

Wyświetlać możesz komunikaty generowane przez poszczególne moduły usługi IoT Edge oraz dostarczane do Twojego centrum IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Wyświetlanie danych na urządzeniu usługi IoT Edge

Na urządzeniu usługi IoT Edge można wyświetlać komunikaty wysyłane z każdego pojedynczego modułu.

Może być konieczne użycie `sudo` programu w celu uzyskania podwyższonych uprawnień do uruchamiania `iotedge` poleceń. Wylogowanie się i ponowne zalogowanie do urządzenia automatycznie aktualizuje Twoje uprawnienia.

1. Wyświetl wszystkie moduły na urządzeniu usługi IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Wyświetl komunikaty wysyłane z określonego urządzenia. Użyj nazwy modułu z danych wyjściowych poprzedniego polecenia.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Wyświetlanie danych otrzymywanych przez centrum IoT

Możesz wyświetlić komunikaty z urządzenia do chmury odbierane przez Centrum IoT przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Poniższe kroki pokazują, jak skonfigurować program Visual Studio Code w celu monitorowania komunikatów urządzenie-chmura w centrum IoT.

1. W Eksploratorze Visual Studio Code w sekcji **IoT Hub platformy Azure** rozwiń węzeł **urządzenia** , aby wyświetlić listę urządzeń IoT.

2. Kliknij prawym przyciskiem myszy nazwę urządzenia IoT Edge i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Obserwuj komunikaty przychodzące z modułu tempSensor co pięć sekund. Treść komunikatu zawiera właściwość o nazwie **anomaly**, którą moduł machinelearningmodule ustawia na wartość true lub false. Właściwość **AzureMLResponse** zawiera wartość „OK”, jeśli model został uruchomiony pomyślnie.

   ![Odpowiedź Azure Machine Learning w treści komunikatu](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego zalecanego artykułu, możesz zachować utworzone zasoby oraz konfiguracje i użyć ich ponownie. Możesz także nadal używać tego samego urządzenia usługi IoT Edge jako urządzenia testowego.

W przeciwnym razie możesz usunąć konfigurację lokalną i zasoby platformy Azure utworzone podczas pracy z tym artykułem, aby uniknąć naliczania opłat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono moduł usługi IoT Edge obsługiwany przez usługę Azure Machine Learning. Możesz teraz kontynuować pracę, korzystając ze wszystkich innych samouczków, aby dowiedzieć się o innych metodach, za pomocą których usługa Azure IoT Edge może ułatwiać przekształcanie danych w analizy biznesowe na urządzeniach brzegowych.

> [!div class="nextstepaction"]
> [Klasyfikowanie obrazów za pomocą usługi Custom Vision](tutorial-deploy-custom-vision.md)
