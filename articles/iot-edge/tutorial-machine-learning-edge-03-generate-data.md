---
title: 'Samouczek: generowanie danych symulowanego urządzenia — Machine Learning na Azure IoT Edge'
description: Samouczek — Tworzenie urządzeń wirtualnych generujących symulowane dane telemetryczne, które mogą być później używane do uczenia modelu uczenia maszynowego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fdd762834d351119116c5e4854dd4233671c29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463133"
---
# <a name="tutorial-generate-simulated-device-data"></a>Samouczek: generowanie danych symulowanego urządzenia

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

W tym artykule wykorzystamy dane szkoleniowe dotyczące uczenia maszynowego w celu symulowania przesyłania danych telemetrycznych urządzenia do usługi Azure IoT Hub. Jak zostało to opisane we wprowadzeniu, ten samouczek używa [zestawu danych symulacji degradacji aparatu TurboFan](https://c3.nasa.gov/dashlink/resources/139/) w celu symulowania danych z zestawu aparatów samolotowych na potrzeby szkolenia i testowania.

W naszym scenariuszu eksperymentalnym wiemy, że:

* Dane składają się z wielu wieloczynnikowa szeregów czasowych.
* Każdy zestaw danych jest podzielony na podzbiory szkoleniowe i testowe.
* Każda seria czasu pochodzi z innego aparatu.
* Każdy silnik rozpoczyna się od różnych stopni początkowych zużycia i produkcji.

W tym samouczku jest używany podzestaw danych szkolenia jednego zestawu danych (FD003).

W rzeczywistości każdy aparat będzie niezależnym urządzeniem IoT. Przy założeniu, że nie masz dostępnych kolekcji turbofanych podłączonych do Internetu aparatów dla tych urządzeń zostanie utworzona.

Symulator to program w języku C#, który używa IoT Hub interfejsów API do programistycznego rejestrowania urządzeń wirtualnych z IoT Hub. Następnie odczytujemy dane dla każdego urządzenia z podzestawu danych NASA i wysyłamy je do usługi IoT Hub przy użyciu symulowanego urządzenia IoT. Cały kod tego fragmentu samouczka można znaleźć w katalogu DeviceHarness repozytorium.

Projekt DeviceHarness jest projektem platformy .NET Core, który jest tworzony w języku C# zawierającym cztery klasy:

* **Program:** Punkt wejścia do wykonania odpowiedzialny za obsługę danych wejściowych i ogólnej koordynacji użytkownika.
* **TrainingFileManager:** Odpowiedzialny za odczytywanie i analizowanie wybranego pliku danych.
* **Danymi cycledata:** Reprezentuje pojedynczy wiersz danych w pliku przekonwertowanym na format wiadomości.
* **TurbofanDevice:** Odpowiedzialny za tworzenie urządzenia IoT, który odnosi się do jednego urządzenia (szeregu czasowego), w danych i przesyłania danych do IoT Hub.

Zadania opisane w tym artykule powinny zająć około 20 minut.

Rzeczywisty, podobny do pracy w tym kroku, będzie prawdopodobnie wykonywany przez deweloperów urządzeń i deweloperów rozwiązań w chmurze.

W tej części samouczka dowiesz się, jak:

> [!div class="checklist"]
>
> * Uwzględnij projekt zewnętrzny w środowisku deweloperskim.
> * Użyj przykładowego projektu DeviceHarness, aby wygenerować symulowane dane urządzenia IoT.
> * Wyświetl wygenerowane dane w IoT Hub.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurowanie Visual Studio Code i kompilowanie projektu DeviceHarness

1. Otwórz sesję pulpitu zdalnego na maszynie wirtualnej deweloperskiej.

1. W programie Visual Studio Code Otwórz `C:\source\IoTEdgeAndMlSample\DeviceHarness` folder.

1. Ponieważ używasz rozszerzeń na tym komputerze po raz pierwszy, Niektóre rozszerzenia będą aktualizować i instalować ich zależności. Może zostać wyświetlony monit o zaktualizowanie rozszerzenia. Jeśli tak, wybierz pozycję **Załaduj ponownie okno**.

   Jeśli błędy OmniSharp pojawiają się w oknie danych wyjściowych, należy odinstalować rozszerzenie języka C#.

1. Zostanie wyświetlony monit o dodanie wymaganych zasobów dla DeviceHarness. Wybierz pozycję **tak** , aby je dodać.

   * Wyświetlenie powiadomienia może potrwać kilka sekund.
   * Jeśli to powiadomienie zostało pominięte, sprawdź ikonę dzwonka w prawym dolnym rogu.

   ![Okno podręczne rozszerzenia VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Wybierz pozycję **Przywróć** , aby przywrócić zależności pakietu.

   ![Monit VS Code przywrócenia](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Jeśli te powiadomienia nie są wyświetlane, Zamknij Visual Studio Code, Usuń pliki bin i obj w `C:\source\IoTEdgeAndMlSample\DeviceHarness` , otwórz Visual Studio Code i ponownie otwórz folder DeviceHarness.

1. Sprawdź, czy środowisko jest prawidłowo skonfigurowane, wyzwalając kompilację, **Ctrl**  +  **SHIFT**  +  **B** lub   >  **zadanie kompilacji Uruchom kompilację**.

1. Zostanie wyświetlony monit o wybranie zadania kompilacji do uruchomienia. Wybierz pozycję **kompilacja**.

1. Kompilacja zostanie uruchomiona i wygeneruje komunikat o powodzeniu.

   ![Pomyślnie zakończono Tworzenie komunikatu wyjściowego](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Możesz wprowadzić tę kompilację jako domyślne zadanie kompilacji, wybierając pozycję **Terminal**  >  **Skonfiguruj domyślne zadanie kompilacji...** i wybierając opcję **Kompiluj** z poziomu wiersza polecenia.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Nawiązywanie połączenia z usługą IoT Hub i uruchamianie DeviceHarness

Teraz, gdy mamy już kompilację projektu, Połącz się z Centrum IoT Hub, aby uzyskać dostęp do parametrów połączenia i monitorować postęp generowania danych.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logowanie do platformy Azure w programie Visual Studio Code

1. Zaloguj się do subskrypcji platformy Azure w Visual Studio Code, otwierając paletę poleceń `Ctrl + Shift + P` lub **wyświetlając**  >  **paletę poleceń**.

1. Wyszukaj polecenie **Azure: Sign in** .

   Zostanie otwarte okno przeglądarki i zostanie wyświetlony komunikat z prośbą o Twoje poświadczenia. Gdy nastąpi przekierowanie do strony sukces, możesz zamknąć przeglądarkę.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Nawiązywanie połączenia z Centrum IoT Hub i pobieranie parametrów połączenia centrum

1. W dolnej części Eksploratora Visual Studio Code Wybierz ramkę **IoT Hub platformy Azure** , aby ją rozwinąć.

1. W rozwiniętej ramce kliknij pozycję **wybierz IoT Hub**.

1. Po wyświetleniu monitu wybierz subskrypcję platformy Azure, a następnie Centrum IoT Hub.

1. Kliknij przycisk **...** po prawej stronie **IoT Hub platformy Azure** , aby uzyskać więcej akcji. Wybierz pozycję **kopiuj IoT Hub parametry połączenia**.

   ![Kopiuj parametry połączenia IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Uruchamianie projektu DeviceHarness

1. Wybierz pozycję **Wyświetl**  >  **Terminal** , aby otworzyć Visual Studio Code terminala.

   Jeśli nie widzisz monitu, naciśnij klawisz ENTER.

1. Wprowadź w terminalu polecenie `dotnet run`.

1. Po wyświetleniu monitu o parametry połączenia IoT Hub wklej parametry połączenia skopiowane w poprzedniej sekcji.

1. W ramce **usługi Azure IoT Hub Devices** kliknij przycisk Odśwież.

   ![Odświeżanie listy urządzeń IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Należy zauważyć, że urządzenia są dodawane do IoT Hub i że urządzenia są wyświetlane w kolorze zielonym, aby wskazać, że dane są wysyłane za pośrednictwem tego urządzenia. Po wysłaniu komunikatów do centrum IoT przez urządzenia zostaną one rozłączone i wyświetlone jako niebieskie.

1. Komunikaty wysyłane do centrum można wyświetlić, klikając prawym przyciskiem myszy dowolne urządzenie i wybierając pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Komunikaty będą wyświetlane w okienku danych wyjściowych w Visual Studio Code.

1. Zatrzymaj monitorowanie, klikając w okienku danych wyjściowych **IoT Hub Azure** , a następnie wybierz pozycję **Zatrzymaj monitorowanie wbudowanego punktu końcowego zdarzenia**.

1. Pozwól, aby aplikacja działała, co potrwa kilka minut.

## <a name="check-iot-hub-for-activity"></a>Sprawdź działanie IoT Hub

Dane wysłane przez DeviceHarness zostały przekazane do centrum IoT Hub, gdzie można je zweryfikować w Azure Portal.

1. Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do centrum IoT Hub utworzonego dla tego samouczka.

1. W menu po lewej stronie w obszarze **monitorowanie** wybierz pozycję **metryki**.

1. Na stronie definicja wykresu kliknij listę rozwijaną **Metryka** , przewiń w dół listy i wybierz pozycję **Routing: dane dostarczone do magazynu**. Wykres powinien pokazać skok, gdy dane były kierowane do magazynu.

   ![Wykres przedstawia skoki podczas dostarczania danych do magazynu](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Weryfikowanie danych w usłudze Azure Storage

Dane, które właśnie wysłaliśmy do centrum IoT Hub, zostały przekazane do kontenera magazynu utworzonego w poprzednim artykule. Przyjrzyjmy się danych na naszym koncie magazynu.

1. W witrynie Azure Portal przejdź do swojego konta magazynu.

1. W oknie Nawigator konta magazynu wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza)**.

1. W Eksploratorze magazynu wybierz pozycję **kontenery obiektów BLOB** `devicedata` .

1. W okienku Zawartość kliknij folder nazwa usługi IoT Hub, a następnie pozycję Year, month, Day i Hour. Po zapisaniu danych zobaczysz kilka folderów reprezentujących minuty.

   ![Wyświetlanie folderów w usłudze BLOB Storage](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Kliknij jeden z tych folderów, aby znaleźć pliki danych z etykietami **00** i **01** odpowiadające partycji.

1. Pliki są zapisywane w formacie [Avro](https://avro.apache.org/) . Kliknij dwukrotnie jeden z tych plików, aby otworzyć kolejną kartę przeglądarki i częściowo renderować dane. Jeśli zostanie wyświetlony monit o otwarcie pliku w programie, możesz wybrać VS Code i będzie on prawidłowo renderowany.

1. Nie ma potrzeby próby odczytu lub interpretacji danych teraz; zajmiemy się tym w następnym artykule.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Zaczekaj na oczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto projektu .NET Core do utworzenia zestawu wirtualnych urządzeń IoT i wysłania danych z nich do naszego centrum IoT Hub i kontenera usługi Azure Storage. Ten projekt symuluje rzeczywisty scenariusz, w którym fizyczne urządzenia IoT wysyłają dane do IoT Hub i w dalszej postaci do magazynu nadzorowanego. Te dane obejmują odczyty czujników, Ustawienia operacyjne, sygnały błędów i tryby itd. Po zebraniu wystarczającej ilości danych używamy go do uczenia modeli, które przewidują pozostały okres eksploatacji (pozostałego czasu eksploatacji) dla urządzenia. Zaprezentowanie tej uczenia maszynowego w następnym artykule.

Przejdź do następnego artykułu, aby przeszkolić model uczenia maszynowego z danymi.

> [!div class="nextstepaction"]
> [Trenowanie i wdrażanie modelu usługi Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
