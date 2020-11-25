---
title: 'Samouczek: przykład alertów wizualnych IoT'
titleSuffix: Azure Cognitive Services
description: W tym samouczku użyjesz Custom Vision z urządzeniem IoT, aby rozpoznać i zgłosić Stany wizualne z kanału informacyjnego wideo aparatu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: c6405e2fcddef9ae3228ede76dfa57f7542164c8
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020181"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Samouczek: używanie Custom Vision z urządzeniem IoT do zgłaszania Stanów wizualnych

Ta przykładowa aplikacja ilustruje, jak używać Custom Vision do uczenia urządzenia za pomocą aparatu do wykrywania stanów wizualnych. Ten scenariusz wykrywania można uruchomić na urządzeniu IoT za pomocą wyeksportowanego modelu ONNX.

Stan wizualny opisuje zawartość obrazu: puste pomieszczenie lub pomieszczenie z osobami, pustą i niedrogami z ciężarówką i tak dalej. Na poniższej ilustracji widać, że aplikacja zostanie wykryta, gdy banan lub Apple zostanie umieszczony przed kamerą.

![Animacja interfejsu użytkownika z oznaczeniem owocu przed kamerą](./media/iot-visual-alerts-tutorial/scoring.gif)

Ten samouczek przedstawia sposób wykonania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj przykładową aplikację do używania własnych zasobów Custom Vision i IoT Hub.
> * Użyj aplikacji do uczenia projektu Custom Vision.
> * Użyj aplikacji do oceny nowych obrazów w czasie rzeczywistym i wysłania wyników do platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services). 

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Ten projekt musi być projektem klasyfikacji obrazu **kompaktowego** , ponieważ będziemy eksportować model do ONNX w późniejszym czasie.
* Należy również [utworzyć zasób IoT Hub](https://ms.portal.azure.com/#create/Microsoft.IotHub) na platformie Azure.
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com/downloads/)
* Opcjonalnie urządzenie IoT z systemem Windows 10 IoT Core w wersji 17763 lub nowszej. Możesz również uruchomić aplikację bezpośrednio z komputera.
   * W przypadku Raspberry Pi 2 i 3 można skonfigurować system Windows 10 bezpośrednio z poziomu aplikacji pulpitu nawigacyjnego IoT. W przypadku innych urządzeń, takich jak DrangonBoard, należy ją wypróbować za pomocą [metody EMMC](/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Jeśli potrzebujesz pomocy przy konfigurowaniu nowego urządzenia, zobacz [Konfigurowanie urządzenia](/windows/iot-core/tutorials/quickstarter/devicesetup) w dokumentacji systemu Windows IoT.

## <a name="about-the-visual-alerts-app"></a>Informacje o aplikacji alertów wizualnych

Aplikacja alertów wizualnych IoT działa w pętli ciągłej, przełączając się w cztery różne stany zgodnie z potrzebami:

* **Brak modelu**: stan No-op. Aplikacja będzie w sposób ciągły w stanie uśpienia przez jedną sekundę i sprawdzić aparat.
* **Przechwytywanie obrazów szkoleniowych**: w tym stanie aplikacja przechwytuje obraz i przekazuje go jako obraz szkoleniowy do docelowego projektu Custom Vision. Aplikacja stanie się następnie oduśpienia dla 500 MS i powtarza operację do momentu przechwycenia ustawionej docelowej liczby obrazów. Następnie wyzwala szkolenie dotyczące modelu Custom Vision.
* **Oczekiwanie na model szkolony**: w tym stanie aplikacja wywołuje interfejs API Custom Vision co sekundę, aby sprawdzić, czy projekt docelowy zawiera przeszkolone iteracje. Gdy go znajdzie, pobiera odpowiedni model ONNX do pliku lokalnego i przełącza do stanu **oceniania** .
* **Ocenianie**: w tym stanie aplikacja używa systemu Windows ml do oceny pojedynczej ramki z kamery względem lokalnego modelu ONNX. Klasyfikacja obrazu z wynikiem jest wyświetlana na ekranie i wysyłana jako komunikat do IoT Hub. Aplikacja zostanie przechodzenia do trybu uśpienia dla jednej sekundy przed rozpoczęciem tworzenia nowego obrazu.

## <a name="examine-the-code-structure"></a>Badanie struktury kodu

Poniższe pliki obsługują najważniejsze funkcje aplikacji.

| Plik | Opis |
|-------------|-------------|
| [MainPage. XAML](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Ten plik definiuje interfejs użytkownika XAML. Obsługuje kontrolkę kamera sieci Web i zawiera etykiety używane do aktualizacji stanu.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Ten kod steruje zachowaniem interfejsu użytkownika XAML. Zawiera kod przetwarzania maszynowego stanu.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Ta klasa jest otoką obsługującą integrację z Custom Vision Service.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Ta klasa jest otoką obsługującą integrację z systemem Windows ML na potrzeby ładowania modelu ONNX i przetwarzania obrazów.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Ta klasa jest otoką, która obsługuje integrację z IoT Hub na potrzeby przekazywania wyników oceniania do platformy Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Konfigurowanie aplikacji alertów wizualnych

Wykonaj następujące kroki, aby uzyskać dostęp do aplikacji alertów wizualnych IoT działającej na komputerze lub urządzeniu IoT.

1. Sklonuj lub Pobierz [przykład IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) w witrynie GitHub.
1. Otwórz rozwiązanie _IoTVisualAlerts. sln_ w programie Visual Studio
1. Zintegruj projekt Custom Vision:
    1. W skrypcie _CustomVision\CustomVisionServiceWrapper.cs_ zaktualizuj `ApiKey` zmienną za pomocą swojego klucza szkoleniowego.
    1. Następnie zaktualizuj `Endpoint` zmienną za pomocą adresu URL punktu końcowego skojarzonego z kluczem.
    1. Zaktualizuj `targetCVSProjectGuid` zmienną z odpowiednim identyfikatorem projektu Custom Vision, którego chcesz użyć. 
1. Skonfiguruj zasób IoT Hub:
    1. W skrypcie _IoTHub\IotHubWrapper.cs_ zaktualizuj `s_connectionString` zmienną o prawidłowe parametry połączenia dla Twojego urządzenia. 
    1. Na Azure Portal Załaduj wystąpienie IoT Hub, kliknij pozycję **urządzenia IoT** w obszarze **Explorer**, wybierz urządzenie docelowe (lub utwórz je w razie konieczności) i Znajdź parametry połączenia w obszarze **podstawowe parametry połączenia**. Ciąg będzie zawierać nazwę IoT Hub, identyfikator urządzenia i klucz dostępu współdzielonego. ma następujący format: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}` .

## <a name="run-the-app"></a>Uruchamianie aplikacji

Jeśli aplikacja jest uruchamiana na komputerze, wybierz pozycję **komputer lokalny** dla urządzenia docelowego w programie Visual Studio, a następnie wybierz opcję **x64** lub **x86** dla platformy docelowej. Naciśnij klawisz F5, aby uruchomić program. Aplikacja powinna uruchomić i wyświetlić kanał informacyjny na żywo z aparatu i komunikatu o stanie.

W przypadku wdrażania na urządzeniu IoT z procesorem ARM należy wybrać opcję **ARM** jako platformę docelową i **maszynę zdalną** jako urządzenie docelowe. Podaj adres IP urządzenia po wyświetleniu monitu (musi znajdować się w tej samej sieci co komputer). Adres IP z aplikacji domyślnej usługi Windows IoT można uzyskać po uruchomieniu urządzenia i nawiązaniu połączenia z siecią. Naciśnij klawisz F5, aby uruchomić program.

Gdy aplikacja jest uruchamiana po raz pierwszy, nie będzie miała żadnej znajomości Stanów wizualnych. Zostanie wyświetlony komunikat o stanie informujący, że żaden model nie jest dostępny. 

## <a name="capture-training-images"></a>Przechwytywanie obrazów szkoleniowych

Aby skonfigurować model, należy umieścić aplikację w stanie **przechwytywania obrazów szkoleniowych** . Wykonaj jedną z następujących czynności:
* Jeśli używasz aplikacji na komputerze PC, użyj przycisku w prawym górnym rogu interfejsu użytkownika.
* Jeśli aplikacja jest uruchamiana na urządzeniu IoT, należy wywołać `EnterLearningMode` metodę na urządzeniu za pomocą IoT Hub. Możesz wywołać go za pomocą wpisu urządzenia w menu IoT Hub na Azure Portal lub za pomocą narzędzia, takiego jak [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp).
 
Gdy aplikacja przejdzie do stanu **przechwytywania obrazów szkoleniowych** , przechwytuje on dwa obrazy co sekundę do czasu osiągnięcia docelowej liczby obrazów. Domyślnie obiektem docelowym jest 30 obrazów, ale można ustawić ten parametr, przekazując żądaną liczbę jako argument do `EnterLearningMode` metody IoT Hub. 

Gdy aplikacja przechwytuje obrazy, należy uwidocznić kamerę w typach Stanów wizualnych, które mają być wykrywane (na przykład puste pomieszczenie, pomieszczenie z osobami, pustym biurem, biurem i wózkiem zabawka itp.).

## <a name="train-the-custom-vision-model"></a>Uczenie modelu Custom Vision

Po zakończeniu przechwytywania obrazów przez aplikację zostanie ona przekazana, a następnie przejdzie do stanu **oczekiwanie na przeszkolony model** . Na tym etapie należy przejść do [witryny sieci web Custom Vision](https://www.customvision.ai/) i skompilować model na podstawie nowych obrazów szkoleniowych. Poniższe animacje przedstawiają przykład tego procesu.

![Animacja: tagowanie wielu obrazów bananów](./media/iot-visual-alerts-tutorial/labeling.gif)

Aby powtórzyć ten proces z własnym scenariuszem:

1. Zaloguj się do [witryny sieci web Custom Vision](http://customvision.ai).
1. Znajdź projekt docelowy, który powinien teraz zawierać wszystkie obrazy szkoleniowe przekazane przez aplikację.
1. Dla każdego stanu wizualizacji, który ma być identyfikowany, wybierz odpowiednie obrazy i ręcznie Zastosuj tag.
    * Na przykład jeśli chcesz rozróżnić puste pomieszczenie i pomieszczenie z innymi osobami, zalecamy tagowanie pięciu lub większej liczby obrazów osobom jako nowej klasy, **ludzi** i tagowania pięciu lub większej liczby obrazów bez osób jako znacznika **negatywnego** . Pomoże to rozróżnić model między dwoma stanami.
    * Innym przykładem, jeśli chcesz, aby przybliżyć pełny półka, możesz użyć tagów takich jak **EmptyShelf**, **PartiallyFullShelf** i **FullShelf**.
1. Po zakończeniu wybierz przycisk **szkolenie** .
1. Po zakończeniu szkolenia aplikacja wykryje, że dostępna jest przeszkolony iteracja. Rozpocznie się proces eksportowania przeszkolonego modelu do ONNX i pobierania go na urządzenie.

## <a name="use-the-trained-model"></a>Używanie wytrenowanego modelu

Po pobraniu przez aplikację przeszkolonego modelu nastąpi przełączenie do stanu **oceniania** i rozpoczęcie oceniania obrazów z aparatu w pętli ciągłej.

Dla każdego przechwytywanego obrazu aplikacja wyświetli na ekranie górny tag. Jeśli nie rozpoznaje stanu wizualnego, wyświetli **Brak dopasowań**. Aplikacja wysyła również te komunikaty do IoT Hub, a w przypadku wykrycia klasy, komunikat będzie zawierać etykietę, wynik pewności i właściwość o nazwie `detectedClassAlert` , która może być używana przez IoT Hub klientów zainteresowanych wykonywaniem szybkiego routingu komunikatów na podstawie właściwości.

Ponadto przykład korzysta z [biblioteki rozpoznawania wykrywania](https://github.com/emmellsoft/RPi.SenseHat) , aby wykryć, kiedy jest uruchomiona w Raspberry Pi z jednostką Hat, tak aby można było go używać jako danych wyjściowych, ustawiając wszystkie sygnalizatory wyświetlania na czerwono, gdy wykryje klasę i puste, gdy nie wykryje niczego.

## <a name="reuse-the-app"></a>Ponowne używanie aplikacji

Jeśli chcesz zresetować aplikację do stanu pierwotnego, możesz to zrobić, klikając przycisk w prawym górnym rogu interfejsu użytkownika lub wywołując metodę `DeleteCurrentModel` za pomocą IoT hubu.

W dowolnym momencie można powtórzyć krok przekazywania obrazów szkoleniowych, klikając przycisk interfejsu użytkownika prawym przyciskiem myszy lub `EnterLearningMode` ponownie wywołując metodę.

Jeśli aplikacja jest uruchamiana na urządzeniu i należy ponownie pobrać adres IP (w celu nawiązania połączenia zdalnego za pomocą [zdalnego klienta usługi Windows IoT](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)), można wywołać `GetIpAddress` metodę za pomocą IoT Hub.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń projekt Custom Vision, jeśli nie chcesz już go obsługiwać. W [witrynie sieci web Custom Vision](https://customvision.ai)przejdź do pozycji **projekty** i wybierz Kosz, który można obsłużyć w nowym projekcie.

![Zrzut ekranu przedstawiający panel zatytułowany mój nowy projekt z ikoną kosza](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano i uruchomiono aplikację, która wykrywa informacje o stanie wizualizacji na urządzeniu IoT i wysyła wyniki do IoT Hub. Następnie przejrzyj kod źródłowy w dalszej części lub wprowadź jedną z sugerowanych modyfikacji poniżej.

> [!div class="nextstepaction"]
> [Przykład IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Dodaj metodę IoT Hub, aby przełączyć aplikację bezpośrednio do stanu " **oczekiwanie na przeszkolone model** ". W ten sposób można nauczyć model z obrazami, które nie są przechwytywane przez samo urządzenie, a następnie wypchnąć nowy model do urządzenia przy użyciu polecenia.
* Postępuj zgodnie z samouczkiem [Wizualizacja danych w czasie rzeczywistym](../../iot-hub/iot-hub-live-data-visualization-in-power-bi.md) , aby utworzyć pulpit nawigacyjny Power BI do wizualizacji IoT Hub alertów wysyłanych przez przykład.
* Postępuj zgodnie z samouczkiem dotyczącym [monitorowania zdalnego IoT](../../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md) , aby utworzyć aplikację logiki, która reaguje na IoT Hub alerty w przypadku wykrycia Stanów wizualnych.