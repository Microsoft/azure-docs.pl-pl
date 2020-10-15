---
title: Łączenie usługi IoT DevKit z rozwiązaniem do zdalnego monitorowania — Azure | Microsoft Docs
description: W tym przewodniku krok po kroku dowiesz się, jak wysyłać dane telemetryczne z czujników na urządzeniu IoT DevKit AZ3166, do akceleratora rozwiązania do monitorowania zdalnego do monitorowania i wizualizacji.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 7a9285ebe2e749f5ac7f248a2703518c8ac237bc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071680"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Łączenie urządzenia IoT DevKit z akceleratorem rozwiązania do zdalnego monitorowania

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym przewodniku opisano sposób uruchamiania przykładowej aplikacji na urządzeniu IoT DevKit. Przykładowy kod wysyła dane telemetryczne z czujników na urządzeniu DevKit do akceleratora rozwiązania.

[Zestawu deweloperskiego IoT DevKit](https://aka.ms/iot-devkit) to cała tablica zgodny z programem Arduino z rozbudowanymi urządzeniami peryferyjnymi i czujnikami. Możesz to zrobić za pomocą [usługi Azure IoT Device Workbench](https://aka.ms/iot-workbench) lub pakietu rozszerzeń [narzędzi Azure iot Tools](https://aka.ms/azure-iot-tools) w Visual Studio Code. [Wykaz projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) zawiera przykładowe aplikacje, które ułatwiają prototyp rozwiązań IoT.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, najpierw wykonaj następujące zadania:

* Przygotuj swój DevKit, wykonując kroki opisane w temacie [Connect IoT DEVKIT AZ3166 to Azure IoT Hub w chmurze](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md).

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

Aby otworzyć przykład zdalnego monitorowania w VS Code:

1. Upewnij się, że DevKit IoT nie jest komputerem. Najpierw Rozpocznij VS Code, a następnie połącz DevKit z komputerem.

1. Kliknij `F1` , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Otwórz przykłady...**. Następnie wybierz pozycję **IoT DevKit** jako tablicę.

1. Znajdź **zdalne monitorowanie** i kliknij pozycję **Otwórz przykład**. Zostanie otwarte nowe okno VS Code pokazujące folder projektu:

   ![Workbench IoT, wybierz przykład zdalnego monitorowania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurowanie urządzenia

Aby skonfigurować IoT Hub parametry połączenia urządzenia na urządzeniu DevKit:

1. Przełącz DevKit IoT do **trybu konfiguracji**:

    * Przytrzymaj przycisk **A**.
    * Wypchnij i zwolnij przycisk **Resetuj** .

1. Na ekranie zostanie wyświetlony Identyfikator DevKit i `Configuration` .

    ![Tryb konfiguracji DevKit IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Naciśnij klawisz **F1** , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Konfigurowanie ustawień urządzenia... > parametry połączenia urządzenia**.

1. Wklej wcześniej skopiowane parametry połączenia i naciśnij klawisz **Enter** w celu skonfigurowania urządzenia.

## <a name="build-the-code"></a>Kompilowanie kod

Aby skompilować i przekazać kod urządzenia:

1. Naciśnij klawisz `F1` , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**:

1. VS Code kompiluje i przekazuje kod na urządzenie DevKit:

    ![IoT Workbench: przekazano urządzenie >](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Urządzenie DevKit wykonuje ponowny rozruch i uruchamia przekazany kod.

## <a name="test-the-sample"></a>Testowanie przykładu

Aby sprawdzić, czy Przykładowa aplikacja zaprzekazywana do urządzenia DevKit działa, wykonaj następujące czynności:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Wyświetl dane telemetryczne wysyłane do rozwiązania do zdalnego monitorowania

Po uruchomieniu przykładowej aplikacji urządzenie DevKit wysyła dane telemetryczne z danych czujników za pośrednictwem Wi-Fi do akceleratora rozwiązania. Aby wyświetlić dane telemetryczne:

1. Przejdź do pulpitu nawigacyjnego rozwiązania, a następnie kliknij przycisk **Device Explorer**.

1. Kliknij nazwę urządzenia urządzenia DevKit. na karcie po prawej stronie można zobaczyć dane telemetryczne z DevKit w czasie rzeczywistym:

    ![Dane czujnika w Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Sterowanie urządzeniem DevKit

Akcelerator rozwiązania do monitorowania zdalnego umożliwia zdalne sterowanie urządzeniem. Przykładowy kod implementuje trzy metody, które można wyświetlić w sekcji **metody** po wybraniu urządzenia na stronie **Device Explorer** :

![Metody DevKit IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Aby zmienić kolor jednego z diod LED DevKit, użyj metody **LedColor** :

1. Wybierz nazwę urządzenia z listy urządzeń, a następnie kliknij pozycję **zadania**:

    ![Tworzenie zadania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Skonfiguruj zadania przy użyciu następujących wartości, a następnie kliknij przycisk **Zastosuj**:

   * Wybierz zadanie: **Run — metoda**
   * Nazwa metody: **LedColor**
   * Nazwa zadania: **ChangeLedColor**

     ![Ustawienia zadania](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Po kilku sekundach kolor diody LED RGB (poniżej przycisku A) w DevKit zmiany:

    ![Czerwona dioda DevKit IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do samouczków, nie usuwaj wdrożenia akceleratora rozwiązania do monitorowania zdalnego.

Jeśli akcelerator rozwiązania nie jest już potrzebny, usuń go ze strony wstępnie zainicjowanych rozwiązań, wybierając ją, a następnie klikając pozycję Usuń rozwiązanie:

![Usuwanie rozwiązania](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz jakiekolwiek problemy, zapoznaj się z tematem [często zadawanych pytań dotyczących usługi IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktuj się z nami przy użyciu następujących kanałów:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak podłączyć urządzenie DevKit do akceleratora rozwiązania do monitorowania zdalnego, Oto kilka sugerowanych następnych kroków:

* [Akceleratory rozwiązań usługi Azure IoT — Omówienie](./index.yml)
* [Dostosowywanie interfejsu użytkownika](iot-accelerators-remote-monitoring-customize.md)