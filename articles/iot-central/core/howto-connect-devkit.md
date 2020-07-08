---
title: Połącz urządzenie DevKit z aplikacją usługi Azure IoT Central | Microsoft Docs
description: Jako deweloper urządzenia dowiesz się, jak połączyć urządzenie zestawu deweloperskiego IoT DevKit z aplikacją platformy Azure IoT Central przy użyciu usługi IoT Plug and Play (wersja zapoznawcza).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81756811"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Łączenie urządzenia zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central

*Ten artykuł ma zastosowanie do deweloperów urządzeń.*

W tym artykule pokazano, jak podłączyć urządzenie zestawu deweloperskiego IoT DevKit (DevKit) do aplikacji IoT Central platformy Azure. Urządzenie używa certyfikowanego modelu IoT Plug and Play (wersja zapoznawcza) dla urządzenia DevKit, aby skonfigurować połączenie z IoT Central.

W tym artykule poznasz następujące informacje:

- Pobierz szczegóły połączenia z aplikacji IoT Central.
- Przygotuj urządzenie i połącz je z aplikacją IoT Central.
- Wyświetl dane telemetryczne i właściwości z urządzenia w IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

- [Urządzenie DevKit](https://aka.ms/iot-devkit-purchase).
- Aplikacja IoT Central. Możesz wykonać kroki opisane w temacie [Tworzenie aplikacji IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Pobierz szczegóły połączenia z urządzeniem

1. W aplikacji IoT Central platformy Azure wybierz kartę **Szablony urządzeń** i wybierz pozycję **+ Nowy**. W sekcji **Użyj wstępnie skonfigurowanego szablonu urządzenia**wybierz pozycję **zestawu deweloperskiego IoT DevKit**.

    ![Szablon urządzenia dla usługi zestawu deweloperskiego IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Wybierz pozycję **Dalej: Dostosuj** , a następnie **Utwórz**.

1. Wybierz kartę **urządzenia** . Na liście urządzenia wybierz pozycję **zestawu deweloperskiego IoT DevKit** i wybierz pozycję **+ New (nowy** ), aby utworzyć nowe urządzenie na podstawie szablonu.

    ![Nowe urządzenie](media/howto-connect-devkit/new-device.png)

1. W oknie podręcznym wprowadź **Identyfikator urządzenia** `SampleDevKit` i **nazwę urządzenia** jako `MXChip IoT DevKit - Sample` . Upewnij się, że **symulowana** opcja jest wyłączona. Następnie wybierz pozycję **Utwórz**.

    ![Identyfikator i nazwa urządzenia](media/howto-connect-devkit/device-id-name.png)

1. Wybierz utworzone urządzenie, a następnie wybierz pozycję **Połącz**. Zanotuj **zakres identyfikatora**, **Identyfikator urządzenia**i **klucz podstawowy**. Te wartości są potrzebne w dalszej części tego artykułu.

    ![Informacje o połączeniu urządzenia](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Przygotowywanie urządzenia

1. Pobierz najnowsze [wstępnie skompilowane oprogramowanie układowe usługi Azure IoT Central Plug and Play (wersja zapoznawcza)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) dla urządzenia DevKit z witryny GitHub.

1. Podłącz urządzenie DevKit do komputera deweloperskiego przy użyciu kabla USB. W systemie Windows okno Eksploratora plików otwiera się na dysku mapowanym na magazyn na urządzeniu DevKit. Na przykład dysk może mieć nazwę **AZ3166 (D:)**.

1. Przeciągnij plik **iotc_devkit. bin** do okna dysku. Po zakończeniu kopiowania urządzenie zostanie uruchomione ponownie z nowym oprogramowaniem układowym.

    > [!NOTE]
    > Jeśli widzisz błędy na ekranie, np. w przypadku **braku sieci Wi-Fi**, jest to spowodowane tym, że usługa DevKit nie została jeszcze połączona z siecią WiFi.

1. Na DevKit, przytrzymaj **przycisk B**, wypchnij i zwolnij przycisk **Reset** , a następnie zwolnij **przycisk b**. Urządzenie jest teraz w trybie punktu dostępu. Aby potwierdzić, na ekranie zostanie wyświetlony komunikat "IoT DevKit-AP" oraz adres IP portalu konfiguracji.

1. Na komputerze lub tablecie Połącz się z nazwą sieci Wi-Fi widoczną na ekranie urządzenia. Sieć Wi-Fi zaczyna się od **AZ-** a po nim adresu Mac. Po nawiązaniu połączenia z tą siecią nie masz dostępu do Internetu. Ten stan jest oczekiwany, a połączenie z tą siecią jest nawiązywane przez krótki czas podczas konfigurowania urządzenia.

1. Otwórz przeglądarkę internetową i przejdź do [http://192.168.0.1/](http://192.168.0.1/) . Zostanie wyświetlona następująca strona sieci Web:

    ![Interfejs użytkownika konfiguracji](media/howto-connect-devkit/config-ui.png)

    Na stronie sieci Web wprowadź:

    - Nazwa sieci Wi-Fi (SSID).
    - Hasło sieciowe sieci Wi-Fi.
    - Szczegóły połączenia: wprowadź **Identyfikator urządzenia**, **zakres identyfikatora**i **klucz podstawowy sygnatury dostępu współdzielonego** , które zostało wcześniej wykonane.

    > [!NOTE]
    > Obecnie DevKit IoT może łączyć się tylko z 2,4 GHz Wi-Fi, 5 GHz nie jest obsługiwany z powodu ograniczeń sprzętowych.

1. Wybierz pozycję **Konfiguruj urządzenie**, a urządzenie DevKit wykonuje ponowny rozruch i uruchomi aplikację:

    ![Uruchom ponownie interfejs użytkownika](media/howto-connect-devkit/reboot-ui.png)

    Na ekranie DevKit zostanie wyświetlone potwierdzenie, że aplikacja jest uruchomiona:

    ![DevKit uruchomione](media/howto-connect-devkit/devkit-running.png)

DevKit najpierw rejestruje nowe urządzenie w IoT Central aplikacji, a następnie uruchamia wysyłanie danych.

## <a name="view-the-telemetry"></a>Wyświetlanie telemetrii

Ten krok umożliwia wyświetlenie danych telemetrycznych w aplikacji IoT Central platformy Azure.

W aplikacji IoT Central wybierz kartę **urządzenia** , wybierz urządzenie, które zostało dodane. Na karcie **Omówienie** można wyświetlić dane telemetryczne z urządzenia DevKit:

![Omówienie urządzenia IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Przeglądanie kodu

Aby przejrzeć kod lub zmodyfikować go i skompilować, przejdź do [przykładów kodu](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem urządzenia, Oto kilka sugerowanych następnych kroków:

- Przeczytaj o [łączności urządzeń w usłudze Azure IoT Central](./concepts-get-connected.md)
- Dowiedz się, jak [monitorować łączność z urządzeniem przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-monitor-devices-azure-cli.md)
