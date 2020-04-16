---
title: 'Samouczek: Przykład alertów wizualnych IoT'
titleSuffix: Azure Cognitive Services
description: W tym samouczku używasz funkcji Custom Vision z urządzeniem IoT do rozpoznawania i raportowania stanów wizualnych z kanału wideo kamery.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f66347727ad3c1b8eaf1f0e023abe1f2eeefcacb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403719"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Samouczek: Raportowanie stanów wizualnych za pomocą funkcji Niestandardowych wizji z urządzeniem IoT

Ta przykładowa aplikacja ilustruje, jak używać funkcji Custom Vision do szkolenia urządzenia z kamerą w celu wykrywania stanów wizualnych. Ten scenariusz wykrywania można uruchomić na urządzeniu IoT przy użyciu eksportowanego modelu ONNX.

Stan wizualny opisuje zawartość obrazu: pusty pokój lub pokój z ludźmi, pusty podjazd z ciężarówką i tak dalej. Na poniższej ilustracji możesz zobaczyć, jak aplikacja wykrywa, kiedy banan lub jabłko jest umieszczone przed kamerą.

![Animacja interfejsu użytkownika oznaczającego owoce przed kamerą](./media/iot-visual-alerts-tutorial/scoring.gif)

Ten samouczek przedstawia sposób wykonania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj przykładową aplikację do używania własnych zasobów usługi Custom Vision i Usługi IoT Hub.
> * Użyj aplikacji, aby wyszkolić swój projekt Custom Vision.
> * Użyj aplikacji, aby zdobyć nowe obrazy w czasie rzeczywistym i wysłać wyniki na platformę Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Ten projekt musi być projekt klasyfikacji obrazu **Compact,** ponieważ będziemy eksportowania modelu do ONNX później.
* Należy również utworzyć [zasób Usługi IoT Hub](https://ms.portal.azure.com/#create/Microsoft.IotHub) na platformie Azure.
* [Visual Studio 2015 lub nowsze](https://www.visualstudio.com/downloads/)
* Opcjonalnie urządzenie IoT z systemem Windows 10 IoT Core w wersji 17763 lub nowszej. Aplikację można również uruchomić bezpośrednio z komputera.
   * W przypadku Raspberry Pi 2 i 3 system Windows 10 można skonfigurować bezpośrednio z aplikacji Pulpit nawigacyjny IoT. W przypadku innych urządzeń, takich jak DrangonBoard, musisz go flashować za pomocą [metody eMMC.](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices) Jeśli potrzebujesz pomocy dotyczącej konfigurowania nowego urządzenia, zobacz [Konfigurowanie urządzenia](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) w dokumentacji IoT systemu Windows.

## <a name="about-the-visual-alerts-app"></a>Informacje o aplikacji Alerty wizualne

Aplikacja Alerty wizualne IoT działa w pętli ciągłej, przełączając się między czterema różnymi stanami:

* **Brak modelu:** Stan no-op. Aplikacja będzie stale spać przez jedną sekundę i sprawdzać aparat.
* **Przechwytywanie obrazów szkoleniowych:** W tym stanie aplikacja przechwytuje obraz i przesyła go jako obraz szkolenia do docelowego projektu niestandardowej wizji. Następnie aplikacja przeszywa przez 500 ms i powtarza operację, dopóki nie zostanie przechwycona ustawiona liczba docelowa obrazów. Następnie wyzwala szkolenie modelu niestandardowych wizji.
* **Oczekiwanie na przeszkolony model:** W tym stanie aplikacja wywołuje interfejs API niestandardowej wizji co sekundę, aby sprawdzić, czy projekt docelowy zawiera uszkodzone iteracji. Gdy go znajdzie, pobiera odpowiedni model ONNX do pliku lokalnego i przełącza się do stanu **Oceniania.**
* **Scoring:** W tym stanie aplikacja używa systemu Windows ML do oceny pojedynczej klatki z kamery względem lokalnego modelu ONNX. Wynikowa klasyfikacja obrazu jest wyświetlana na ekranie i wysyłana jako wiadomość do centrum IoT Hub. Aplikacja następnie przeszukuje przez jedną sekundę przed punktowaniem nowego obrazu.

## <a name="understand-the-code-structure"></a>Opis struktury kodu

Następujące pliki obsługują główne funkcje aplikacji.

| Plik | Opis |
|-------------|-------------|
| [Strona MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Ten plik definiuje interfejs użytkownika XAML. Obsługuje sterowanie kamerą internetową i zawiera etykiety używane do aktualizacji stanu.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Ten kod kontroluje zachowanie interfejsu użytkownika XAML. Zawiera kod przetwarzania maszyny stanu.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Ta klasa jest otoką, która obsługuje integrację z usługą Custom Vision Service.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Ta klasa jest otoką, która obsługuje integrację z systemem Windows ML do ładowania modelu ONNX i oceniania obrazów przeciwko niemu.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Ta klasa jest otoką, która obsługuje integrację z Centrum IoT do przekazywania wyników oceniania na platformę Azure.|

## <a name="set-up-the-visual-alerts-app"></a>Konfigurowanie aplikacji Alerty wizualne

Wykonaj następujące kroki, aby uzyskać aplikację Alerty wizualne IoT uruchomione na komputerze lub urządzeniu IoT.

1. Klonuj lub pobierz [próbkę IoTVisualAlerts](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) na gitHub.
1. Otwórz rozwiązanie _IoTVisualAlerts.sln_ w programie Visual Studio
1. Zintegruj swój projekt Custom Vision:
    1. W skrypcie _CustomVision\CustomVisionServiceWrapper.cs_ zaktualizuj zmienną `ApiKey` za pomocą klucza szkoleniowego.
    1. Następnie zaktualizuj zmienną `Endpoint` za pomocą adresu URL punktu końcowego skojarzonego z kluczem.
    1. Zaktualizuj zmienną `targetCVSProjectGuid` o odpowiednim identyfikatorze projektu usługi Custom Vision, którego chcesz użyć. 
1. Konfigurowanie zasobu Usługi IoT Hub:
    1. W skrypcie _IoTHub\IotHubWrapper.cs_ zaktualizuj zmienną `s_connectionString` za pomocą odpowiedniego ciągu połączenia dla urządzenia. 
    1. W witrynie Azure portal załaduj wystąpienie usługi IoT Hub, kliknij **pozycję Urządzenia IoT** w obszarze **Eksploratory**, wybierz na urządzeniu docelowym (lub utwórz je w razie potrzeby) i znajdź parametry połączenia w obszarze **Ciąg połączenia podstawowego**. Ciąg będzie zawierał nazwę usługi IoT Hub, identyfikator urządzenia i udostępniony klucz dostępu; ma następujący format: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>Uruchomienie aplikacji

Jeśli korzystasz z aplikacji na komputerze, wybierz **opcję Komputer lokalny** dla urządzenia docelowego w programie Visual Studio i wybierz **x64** lub **x86** dla platformy docelowej. Następnie naciśnij klawisz F5, aby uruchomić program. Aplikacja powinna uruchomić i wyświetlić transmisję na żywo z kamery oraz komunikat o stanie.

Jeśli wdrażasz na urządzeniu IoT z procesorem ARM, musisz wybrać **ARM** jako platformę docelową i **komputer zdalny** jako urządzenie docelowe. Po po wyświetleniu monitu podaj adres IP urządzenia (musi on znajdować się w tej samej sieci co komputer). Adres IP można uzyskać z domyślnej aplikacji IoT systemu Windows po uruchomieniu urządzenia i podłączeniu go do sieci. Naciśnij klawisz F5, aby uruchomić program.

Po uruchomieniu aplikacji po raz pierwszy, nie będzie miał żadnej wiedzy o stanach wizualnych. Zostanie wyświetlony komunikat o stanie, że żaden model nie jest dostępny. 

## <a name="capture-training-images"></a>Przechwytywanie obrazów szkoleniowych

Aby skonfigurować model, należy umieścić aplikację w stanie **Przechwytywanie obrazów szkoleniowych.** Wykonać jeden z następujących kroków:
* Jeśli korzystasz z aplikacji na komputerze, użyj przycisku w prawym górnym rogu interfejsu użytkownika.
* Jeśli korzystasz z aplikacji na urządzeniu IoT, `EnterLearningMode` wywołaj metodę na urządzeniu za pośrednictwem centrum IoT Hub. Można to wywołać za pomocą wpisu urządzenia w menu Centrum IoT w witrynie Azure portal lub za pomocą narzędzia, takiego jak [Eksplorator urządzeń usługi IoT Hub.](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
 
Gdy aplikacja wejdzie w stan **Przechwytywanie obrazów szkoleniowych,** przechwytuje około dwóch obrazów co sekundę, dopóki nie osiągnie docelowej liczby obrazów. Domyślnie obiekt docelowy to 30 obrazów, ale można ustawić ten parametr, `EnterLearningMode` przekazując żądaną liczbę jako argument do metody Centrum IoT. 

Podczas gdy aplikacja rejestruje obrazy, musisz narazić aparat na typy stanów wizualnych, które chcesz wykryć (na przykład pusty pokój, pokój z ludźmi, puste biurko, biurko z wózkiem ścieliczy i tak dalej).

## <a name="train-the-custom-vision-model"></a>Trenuj model Custom Vision

Po zakończeniu przechwytywania obrazów aplikacja przekaże je, a następnie przełączy się do stanu **Oczekiwanie na przeszkolony model.** W tym momencie należy przejść do [portalu niestandardowej wizji](https://www.customvision.ai/) i utworzyć model na podstawie nowych obrazów szkoleniowych. Poniższa animacja przedstawia przykład tego procesu.

![Animacja: oznaczanie wielu obrazów bananów](./media/iot-visual-alerts-tutorial/labeling.gif)

Aby powtórzyć ten proces z własnym scenariuszem:

1. Zaloguj się do [portalu Custom Vision](http://customvision.ai).
1. Znajdź swój projekt docelowy, który powinien mieć teraz wszystkie obrazy szkoleniowe, które aplikacja została przesłana.
1. Dla każdego stanu wizualnego, który chcesz zidentyfikować, wybierz odpowiednie obrazy i ręcznie zastosuj znacznik.
    * Jeśli na przykład twoim celem jest rozróżnienie między pustym pokojem a pokojem z osobami w nim, zalecamy oznaczenie co najmniej pięciu obrazów osobami jako nową klasą **, Osoby**i oznaczenie pięciu lub więcej obrazów bez osób jako **tagu Negatywne.** Pomoże to model rozróżnić między dwoma stanami.
    * Innym przykładem jest przybliżanie, jak pełna jest półka, możesz użyć tagów, takich jak **EmptyShelf**, **PartiallyFullShelf**i **FullShelf**.
1. Po zakończeniu wybierz przycisk **Pociąg.**
1. Po zakończeniu szkolenia aplikacja wykryje, że dostępna jest uszkolona iteracja. Rozpocznie proces eksportowania przeszkolonego modelu do ONNX i pobierania go do urządzenia.

## <a name="use-the-trained-model"></a>Używanie wytrenowanego modelu

Gdy aplikacja pobierze przeszkolony model, przełączy się do stanu **Oceniania** i rozpocznie ocenianie obrazów z aparatu w pętli ciągłej.

Dla każdego przechwyconego obrazu aplikacja wyświetli górny znacznik na ekranie. Jeśli nie rozpozna stanu wizualnego, wyświetli **brak dopasowania**. Aplikacja wysyła również te komunikaty do Centrum IoT, a jeśli jest wykrywana klasa, komunikat będzie zawierał `detectedClassAlert`etykietę, wynik zaufania i właściwość o nazwie , która może być używana przez klientów Usługi IoT Hub zainteresowanych szybkim routingiem wiadomości na podstawie właściwości.

Ponadto przykład używa [biblioteki Sense HAT](https://github.com/emmellsoft/RPi.SenseHat) do wykrywania, kiedy jest uruchomiony na Raspberry Pi z jednostką Sense HAT, dzięki czemu można go używać jako wyświetlacza wyjściowego, ustawiając wszystkie kontrolki wyświetlania na czerwony, gdy wykryje klasę i puste, gdy nic nie wykryje.

## <a name="reuse-the-app"></a>Ponowne używanie aplikacji

Jeśli chcesz zresetować aplikację z powrotem do stanu pierwotnego, możesz to zrobić, klikając przycisk w prawym górnym rogu interfejsu `DeleteCurrentModel` użytkownika lub wywołując metodę za pośrednictwem centrum IoT Hub.

W dowolnym momencie można powtórzyć krok przekazywania obrazów szkoleniowych, klikając przycisk interfejsu `EnterLearningMode` użytkownika w prawym górnym rogu lub wywołując metodę ponownie.

Jeśli korzystasz z aplikacji na urządzeniu i musisz ponownie pobrać adres IP (na przykład ustanowić połączenie zdalne za pośrednictwem `GetIpAddress` [klienta zdalnego IoT systemu Windows),](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)możesz wywołać tę metodę za pośrednictwem centrum IoT Hub.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń projekt wizji niestandardowej, jeśli nie chcesz go już konserwować. W [witrynie internetowej Usługi Custom Vision](https://customvision.ai)przejdź do pozycji **Projekty** i wybierz kosz w ramach nowego projektu.

![Zrzut ekranu przedstawiający panel z ikoną kosza z ikoną kosza](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowałeś i uruchomiono aplikację, która wykrywa informacje o stanie wizualnym na urządzeniu IoT i wysyła wyniki do centrum IoT Hub. Następnie zapoznaj się z kodem źródłowym dalej lub dokonać jednej z sugerowanych modyfikacji poniżej.

> [!div class="nextstepaction"]
> [Próbka IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Dodaj metodę Centrum IoT, aby przełączyć aplikację bezpośrednio do stanu **Oczekiwanie na przeszkolony model.** W ten sposób można trenować model z obrazami, które nie są przechwytywane przez samo urządzenie, a następnie wypchnąć nowy model do urządzenia w poleceniu.
* Postępuj zgodnie z samouczkiem [wizualizacji danych z czujnika w czasie rzeczywistym,](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) aby utworzyć pulpit nawigacyjny usługi Power BI, aby wizualizować alerty usługi IoT Hub wysyłane przez przykład.
* Postępuj zgodnie z [samouczka monitorowania zdalnego IoT,](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) aby utworzyć aplikację logiki, która odpowiada na alerty Usługi IoT Hub po wykryciu stanów wizualnych.
