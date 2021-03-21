---
title: Łączenie zestawu deweloperskiego IoT DevKit z platformą Azure IoT Hub zdalne monitorowanie
description: W tym samouczku dowiesz się, jak wysyłać informacje o stanie czujników w usłudze IoT DevKit AZ3166 do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785020"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Połącz usługę zestawu deweloperskiego IoT DevKit z akceleratorem rozwiązania do monitorowania zdalnego usługi Azure IoT

W tym samouczku dowiesz się, jak uruchomić przykładową aplikację w usłudze DevKit, aby wysłać dane czujnika do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT.

[Zestawu deweloperskiego IoT DevKit](https://aka.ms/iot-devkit) to cała tablica zgodny z programem Arduino z rozbudowanymi urządzeniami peryferyjnymi i czujnikami. Można go opracowywać przy użyciu [rozszerzenia Visual Studio Code Arduino](https://aka.ms/arduino). Wraz z [katalogiem](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) rosnących projektów można utworzyć prototypy rozwiązań Internet rzeczy (IoT), które wykorzystują usługi Microsoft Azure Services.

## <a name="what-you-need"></a>Potrzebne elementy

Zakończ [przewodnik wprowadzenie](./iot-hub-arduino-iot-devkit-az3166-get-started.md) , aby:

* Połączono z usługą DevKit do Wi-Fi
* Przygotowywanie środowiska deweloperskiego

Aktywna subskrypcja platformy Azure. Jeśli go nie masz, możesz zarejestrować się przy użyciu jednej z następujących dwóch metod:

* Aktywuj [bezpłatne 30-dniowe konto wersji próbnej Microsoft Azure](https://azure.microsoft.com/free/)

* Zarezerwuj środki na korzystanie z [platformy Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) , jeśli jesteś subskrybentem MSDN lub Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Tworzenie akceleratora rozwiązań do monitorowania zdalnego usługi Azure IoT

1. Przejdź do [strony Akceleratory rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/) i kliknij pozycję **Utwórz nowe rozwiązanie**.

   ![Wybierz typ akceleratora rozwiązań usługi Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Domyślnie ten przykład tworzy IoT Hub S2 po utworzeniu akceleratora rozwiązania do monitorowania zdalnego IoT. Jeśli to centrum IoT nie jest używane z ogromną liczbą urządzeń, zdecydowanie zalecamy jego obniżenie z warstwy S2 do S1 i usunięcie akceleratora rozwiązania do monitorowania zdalnego IoT, dzięki czemu powiązane IoT Hub można również usunąć, gdy nie będą już potrzebne. 

2. Wybierz pozycję **zdalne monitorowanie**.

3. Wprowadź nazwę rozwiązania, wybierz subskrypcję i region, a następnie kliknij pozycję **Utwórz rozwiązanie**. Zainicjowanie rozwiązania może potrwać trochę czasu.
  
   ![Tworzenie rozwiązania](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Po zakończeniu aprowizacji kliknij przycisk **Uruchom**. Niektóre symulowane urządzenia są tworzone dla rozwiązania podczas procesu aprowizacji. Kliknij pozycję **urządzenia** , aby je sprawdzić.

   ![Pulpit nawigacyjny](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsola](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kliknij pozycję **Dodaj urządzenie**.

6. Kliknij pozycję **Dodaj nowe** dla **urządzenia niestandardowego**.
  
   ![Dodawanie nowego urządzenia](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kliknij pozycję **pozwól mi zdefiniować własny identyfikator urządzenia**, wprowadź `AZ3166` , a następnie kliknij przycisk **Utwórz**.
  
   ![Utwórz urządzenie o IDENTYFIKATORze](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Zanotuj **IoT Hub nazwę hosta**, a następnie kliknij przycisk **gotowe**.

## <a name="open-the-remotemonitoring-sample"></a>Otwieranie przykładu RemoteMonitoring

1. Odłącz DevKit od komputera, jeśli jest podłączony.

2. Uruchom program VS Code.

3. Połącz DevKit z komputerem. VS Code automatycznie wykrywa DevKit i otwiera następujące strony:

   * Strona wprowadzenia DevKit.
   * Przykłady Arduino: praktyczne przykłady umożliwiające rozpoczęcie pracy z usługą DevKit.

4. Rozwiń sekcję **przykłady Arduino** lewej strony, przejdź do **przykładów dla zestawu deweloperskiego AZ3166 > AzureIoT**, a następnie wybierz pozycję **RemoteMonitoring**. Otwiera nowe okno VS Code z folderem projektu.

   > [!NOTE]
   > W przypadku zamknięcia okienka można go otworzyć ponownie. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ), aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie Znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-required-azure-services"></a>Udostępnianie wymaganych usług platformy Azure

W oknie rozwiązanie Uruchom zadanie przez `Ctrl+P` (macOS: `Cmd+P` ), wprowadzając `task cloud-provision` w podanym polu tekstowym.

W terminalu VS Code interaktywny wiersz polecenia przeprowadzi Cię przez proces aprowizacji wymaganych usług platformy Azure.

![Udostępnianie zasobów platformy Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Kompilowanie i przekazywanie kodu urządzenia

1. Użyj `Ctrl+P` (macOS: `Cmd + P` ) i wpisz **config zadania-Device-Connection**.

2. Terminal pyta, czy chcesz użyć parametrów połączenia pobieranych z `task cloud-provision` kroku. Możesz również wprowadzić własne parametry połączenia urządzenia, klikając pozycję "Utwórz nowy..."

3. Na terminalu zostanie wyświetlony komunikat z prośbą o wprowadzenie do trybu konfiguracji. Aby to zrobić, przytrzymaj przycisk A, a następnie wypchnij i zwolnij przycisk Resetuj. Na ekranie zostanie wyświetlony Identyfikator DevKit i "Konfiguracja".

   ![Parametry połączenia wejściowego](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Po `task config-device-connection` zakończeniu kliknij przycisk, `F1` aby załadować vs Code polecenia i wybrać opcję `Arduino: Upload` . VS Code zaczyna weryfikować i przekazywać szkic Arduino.
  
   ![Weryfikacja i przekazywanie szkicu Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamia kod.

## <a name="test-the-project"></a>Testowanie projektu

Gdy aplikacja Przykładowa zostanie uruchomiona, DevKit wysyła dane czujnika za pośrednictwem sieci Wi-Fi do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT. Aby zobaczyć wynik, wykonaj następujące kroki:

1. Przejdź do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT, a następnie kliknij pozycję **pulpit nawigacyjny**.

2. W konsoli rozwiązania do monitorowania zdalnego zobaczysz stan czujnika DevKit.

   ![Dane czujnika w akceleratorze rozwiązań do monitorowania zdalnego usługi Azure IoT](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Zmień identyfikator urządzenia

Jeśli chcesz zmienić stałe **AZ3166** na dostosowany identyfikator urządzenia w kodzie, zmodyfikuj wiersz kodu wyświetlany w [przykładzie zdalnego monitorowania](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z tematem [często zadawane pytania dotyczące zestawu IoT Developer](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak podłączyć urządzenie DevKit do akceleratora rozwiązania do monitorowania zdalnego usługi Azure IoT i wizualizować dane z czujnika, poniżej przedstawiono sugerowane następne kroki:

* [Akceleratory rozwiązań usługi Azure IoT — Omówienie](/azure/iot-suite/)

* [Pakiet IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/)