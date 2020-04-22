---
title: Łączenie urządzenia DevKit z aplikacją Azure IoT Central | Dokumenty firmy Microsoft
description: Jako deweloper urządzeń dowiedz się, jak połączyć urządzenie MXChip IoT DevKit z aplikacją Azure IoT Central przy użyciu funkcji IoT Plug and Play (wersja zapoznawcza).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756811"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Łączenie urządzenia MXChip IoT DevKit z aplikacją Azure IoT Central

*Ten artykuł dotyczy deweloperów urządzeń.*

W tym artykule pokazano, jak połączyć urządzenie MXChip IoT DevKit (DevKit) z aplikacją Azure IoT Central. Urządzenie używa certyfikowanego modelu IoT Plug and Play (wersja zapoznawcza) urządzenia DevKit do konfigurowania połączenia z centrum IoT.

W tym artykule in how-to:

- Pobierz szczegóły połączenia z aplikacji IoT Central.
- Przygotuj urządzenie i podłącz go do aplikacji IoT Central.
- Wyświetlanie danych telemetrycznych i właściwości z urządzenia w centrum IoT.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

- [Urządzenie DevKit](https://aka.ms/iot-devkit-purchase).
- Aplikacja IoT Central. Możesz wykonać kroki opisane w [aplikacji Tworzenie aplikacji IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Uzyskaj szczegóły połączenia urządzenia

1. W aplikacji Azure IoT Central wybierz kartę **Szablony urządzeń** i wybierz pozycję **+ Nowy**. W sekcji **Użyj wstępnie skonfigurowanego szablonu urządzenia**wybierz **MXChip IoT DevKit**.

    ![Szablon urządzenia dla zestawu MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Wybierz **pozycję Dalej: Dostosuj,** a następnie **utwórz**.

1. Wybierz kartę **Urządzenia.** Na liście urządzeń wybierz **MXChip IoT DevKit** i wybierz **+ Nowy,** aby utworzyć nowe urządzenie z szablonu.

    ![Nowe urządzenie](media/howto-connect-devkit/new-device.png)

1. W wyskakującym oknie **Device ID** wprowadź identyfikator `SampleDevKit` urządzenia jako `MXChip IoT DevKit - Sample`i nazwę **urządzenia** jako . Upewnij się, że opcja **Symulowana** jest wyłączona. Następnie wybierz pozycję **Utwórz**.

    ![Identyfikator i nazwa urządzenia](media/howto-connect-devkit/device-id-name.png)

1. Wybierz utworzone urządzenie, a następnie wybierz pozycję **Połącz**. Zanotuj **zakres identyfikatora,** **identyfikator urządzenia**i **klucz podstawowy**. Te wartości są potrzebne w dalszej części tego artykułu.

    ![Informacje o połączeniu urządzenia](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Przygotowanie urządzenia

1. Pobierz najnowsze [wstępnie utworzone oprogramowanie układowe Azure IoT Central Plug and Play (wersja zapoznawcza)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) dla urządzenia DevKit z usługi GitHub.

1. Podłącz urządzenie DevKit do urządzenia deweloperującego za pomocą kabla USB. W systemie Windows otwiera się okno Eksploratora plików na dysku mapowanym do magazynu na urządzeniu DevKit. Na przykład dysk może być nazywany **AZ3166 (D:)**.

1. Przeciągnij plik **iotc_devkit.bin** na okno dysku. Po zakończeniu kopiowania urządzenie zostanie ponownie uruchomione z nowym oprogramowaniem układowym.

    > [!NOTE]
    > Jeśli na ekranie są widoczne błędy, takie jak **Brak sieci Wi-Fi,** jest to spowodowane tym, że DevKit nie został jeszcze połączony z siecią Wi-Fi.

1. Na DevKit przytrzymaj **przycisk B**, naciśnij i zwolnij przycisk **Reset,** a następnie zwolnij **przycisk B**. Urządzenie jest teraz w trybie punktu dostępu. Aby potwierdzić, na ekranie jest wyświetlany komunikat "IoT DevKit - AP" i adres IP portalu konfiguracji.

1. Na komputerze lub tablecie połącz się z nazwą sieci Wi-Fi wyświetlaną na ekranie urządzenia. Sieć WiFi zaczyna się od **AZ,** po którym następuje adres MAC. Gdy łączysz się z tą siecią, nie masz dostępu do Internetu. Ten stan jest oczekiwany, a połączenie z tą siecią jest podłączane tylko przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę internetową [http://192.168.0.1/](http://192.168.0.1/)i przejdź do pliku . Na następującej stronie internetowej są wyświetlane:

    ![Interfejs użytkownika konfiguracji](media/howto-connect-devkit/config-ui.png)

    Na stronie internetowej wpisz:

    - Nazwa sieci Wi-Fi (SSID).
    - Hasło do sieci Wi-Fi.
    - Szczegóły połączenia: wprowadź **identyfikator urządzenia,** **zakres identyfikatora**i **klucza podstawowego SAS,** o których wcześniej zanotowano.

    > [!NOTE]
    > Obecnie IoT DevKit może łączyć się tylko z siecią Wi-Fi 2,4 GHz, 5 GHz nie jest obsługiwane z powodu ograniczeń sprzętowych.

1. Wybierz **pozycję Konfiguruj urządzenie**, urządzenie DevKit uruchamia ponownie i uruchamia aplikację:

    ![Ponowne uruchomienie interfejsu użytkownika](media/howto-connect-devkit/reboot-ui.png)

    Na ekranie DevKit jest wyświetlane potwierdzenie, że aplikacja jest uruchomiona:

    ![DevKit uruchomiony](media/howto-connect-devkit/devkit-running.png)

DevKit najpierw rejestruje nowe urządzenie w aplikacji IoT Central, a następnie rozpoczyna wysyłanie danych.

## <a name="view-the-telemetry"></a>Wyświetlanie danych telemetrycznych

W tym kroku można wyświetlić dane telemetryczne w aplikacji Azure IoT Central.

W aplikacji IoT Central wybierz kartę **Urządzenia,** wybierz dodane urządzenie. Na karcie **Przegląd** można wyświetlić dane telemetryczne z urządzenia DevKit:

![Omówienie urządzenia IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Przeglądanie kodu

Aby przejrzeć kod lub zmodyfikować go i skompilować, przejdź do [przykładów kodu](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzeń, niektóre z sugerowanych następnych kroków to:

- Przeczytaj o [łączności urządzenia w usłudze Azure IoT Central](./concepts-get-connected.md)
- Dowiedz się, jak [monitorować łączność urządzenia przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
