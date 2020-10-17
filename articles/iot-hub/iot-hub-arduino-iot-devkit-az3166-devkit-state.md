---
title: Korzystanie z usługi Azure Device bliźniaczych reprezentacji do sterowania zestawu deweloperskiego IoT DevKit użytkownika Microsoft Docs
description: W tym samouczku dowiesz się, jak monitorować Stany DevKit i kontrolować diody LED użytkownika przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 8c43c8a0b9fdf30b5ce5ae6ecbf123b563099ff6
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148776"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Tego przykładu można użyć do monitorowania informacji zestawu deweloperskiego IoT DevKit Wi-Fi i Stanów czujnika oraz do kontrolowania koloru diody LED użytkownika przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji.

## <a name="what-you-learn"></a>Omawiane zagadnienia

- Jak monitorować Stany zestawu deweloperskiego IoT DevKit sensor States.

- Jak za pomocą usługi Azure Device bliźniaczych reprezentacji sterować kolorem diody LED RGB DevKit.

## <a name="what-you-need"></a>Potrzebne elementy

- Skonfiguruj środowisko programistyczne, postępując zgodnie z [przewodnikiem wprowadzenie](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

- W oknie terminalu GitBash (lub innym interfejsie wiersza polecenia usługi Git) wpisz następujące polecenia:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

1. Kliknij menu rozwijane **zadania** w Visual Studio Code i wybierz polecenie **Uruchom zadanie...**  -  **chmura — Inicjowanie obsługi administracyjnej**.

2. Postęp jest wyświetlany na karcie **Terminal** panelu **powitalnego** .

3. Po wyświetleniu monitu z komunikatem, którą *subskrypcję chcesz wybrać*, wybierz subskrypcję.

4. Wybierz lub wybierz grupę zasobów. 
 
   > [!NOTE]
   > Jeśli masz już bezpłatną IoT Hub, możesz pominąć ten krok.

5. Po wyświetleniu monitu z komunikatem *co chcesz wybrać Centrum IoT*wybierz lub Utwórz IoT Hub.

6. Jest wyświetlana coś podobnego do *aplikacji funkcji: Nazwa aplikacji funkcji: xxx*. Zanotuj nazwę aplikacji funkcji; zostanie ona użyta w późniejszym kroku.

7. Poczekaj na zakończenie wdrażania szablonu Azure Resource Manager, który jest wskazywany, gdy zostanie wyświetlony komunikat *Menedżer zasobów wdrożenie szablonu: gotowe* .

## <a name="deploy-function-app"></a>Wdróż aplikacja funkcji

1. Kliknij menu rozwijane **zadania** w Visual Studio Code i wybierz polecenie **Uruchom zadanie...**  -  **chmura — wdrażanie**.

2. Poczekaj, aż kod aplikacji funkcji przekaże proces do końca; zostanie wyświetlone okno *wdrażanie aplikacji funkcji wiadomości: gotowe* .

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurowanie parametrów połączenia urządzenia IoT Hub w DevKit

1. Połącz swoje zestawu deweloperskiego IoT DevKit z komputerem.

2. Kliknij menu rozwijane **zadania** w Visual Studio Code i wybierz polecenie **Uruchom zadanie...**  -  **Konfiguracja-urządzenie-połączenie**

3. Na zestawu deweloperskiego IoT DevKit naciśnij przycisk i przytrzymaj klawisz **a**, naciśnij przycisk **Resetuj** , a następnie zwolnij przycisk, **Aby wprowadzić** tryb konfiguracji DekKit.

4. Poczekaj na zakończenie procesu konfiguracji parametrów połączenia.

## <a name="upload-arduino-code-to-devkit"></a>Przekaż kod Arduino do DevKit

Wraz z zestawu deweloperskiego IoT DevKit podłączonym do komputera:

1. Kliknij menu rozwijane **zadania** w Visual Studio Code i wybierz polecenie **Uruchom zadanie kompilacji...** Szkic Arduino jest kompilowany i przekazywany do DevKit.

2. Po pomyślnym przekazaniu szkicu *kompilacja & Przekaż szkic:* zostanie wyświetlony komunikat o powodzeniu.

## <a name="monitor-devkit-state-in-browser"></a>Monitorowanie stanu DevKit w przeglądarce

1. W przeglądarce sieci Web otwórz plik, `DevKitState\web\index.html` który został utworzony podczas wykonywania kroku.

2. Zostanie wyświetlona następująca strona sieci Web:![Określ nazwę aplikacji funkcji.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Wprowadź wcześniej zapisaną nazwę aplikacji funkcji.

4. Kliknij przycisk **Połącz**

5. W ciągu kilku sekund Strona odświeża i wyświetla stan połączenia Wi-Fi DevKit oraz stan wszystkich czujników dołączania.

## <a name="control-the-devkits-user-led"></a>Kontrolowanie diody LED użytkownika DevKit

1. Kliknij grafikę diody LED użytkownika na stronie sieci Web.

2. W ciągu kilku sekund ekran odświeża i pokazuje bieżący stan koloru diody LED użytkownika.

3. Spróbuj zmienić wartość koloru diody LED RGB, klikając w różnych miejscach w kontrolkach suwaka RGB.

## <a name="example-operation"></a>Przykładowa operacja

![Przykładowa procedura testowa](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Możesz zobaczyć pierwotne dane z sznurka urządzenia w Azure Portal: IoT Hub- \> IoT Devices — \> *\<your device\>*  - \> sznurek urządzenia.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak:
- Połącz urządzenie zestawu deweloperskiego IoT DevKit z akceleratorem rozwiązania do monitorowania zdalnego usługi Azure IoT.
- Użyj funkcji bliźniaczych reprezentacji urządzenia Azure IoT, aby wykrywać i kontrolować kolor diody LED RGB DevKit.

Oto sugerowany następny krok: [Omówienie akceleratora rozwiązań zdalnego monitorowania usługi Azure IoT](/azure/iot-suite/)