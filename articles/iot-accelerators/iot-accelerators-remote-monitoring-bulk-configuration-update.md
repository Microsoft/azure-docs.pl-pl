---
title: Zarządzanie urządzeniami podłączonymi do rozwiązania do zdalnego monitorowania w trybie zbiorczym — Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak zarządzać urządzeniami podłączonymi do rozwiązania do monitorowania zdalnego w trybie zbiorczym.
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: 8309b625a590131c5f521335e180967ab2c2667c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91363158"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Samouczek: zarządzanie połączonymi urządzeniami w trybie zbiorczym

W tym samouczku użyjesz akceleratora rozwiązania do monitorowania zdalnego, aby zarządzać konfiguracją podłączonych urządzeń w trybie zbiorczym.

Jako operator w firmie Contoso musisz skonfigurować grupę urządzeń przy użyciu nowej wersji oprogramowania układowego. Wolisz uniknąć konieczności aktualizowania oprogramowania układowego na każdym urządzeniu osobno. Aby zaktualizować oprogramowanie układowe w grupie urządzeń, możesz skorzystać z grup urządzeń i funkcji automatycznego zarządzania urządzeniami w akceleratorze rozwiązania do monitorowania zdalnego. Każde urządzenia dodawane do grupy urządzeń otrzyma najnowsze oprogramowanie układowe, gdy tylko będzie online.

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Tworzenie grupy urządzeń
> * Przygotowanie i hostowanie oprogramowania układowego
> * Tworzenie konfiguracji urządzenia w witrynie Azure Portal
> * Importowanie konfiguracji urządzenia do rozwiązania do monitorowania zdalnego
> * Wdrażanie konfiguracji na urządzeniach z grupy urządzeń
> * Monitorować wdrożenie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki tego samouczka, należy wdrożyć wystąpienie akceleratora rozwiązania do monitorowania zdalnego w subskrypcji platformy Azure.

Jeśli jeszcze nie wdrożono akceleratora rozwiązania do monitorowania zdalnego, należy ukończyć przewodnik Szybki start [Wdrażanie rozwiązania do monitorowania zdalnego opartego na chmurze](quickstart-remote-monitoring-deploy.md).

Potrzebujesz konta usługi Azure Storage do hostowania plików oprogramowania układowego. Możesz użyć istniejącego konta magazynu lub [utworzyć nowe konto magazynu](../storage/common/storage-account-create.md) w ramach subskrypcji.

W tym samouczku jako urządzenia przykładowego użyto urządzenia [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

Na komputerze lokalnym musisz mieć zainstalowane następujące oprogramowanie:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* Rozszerzenie [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) programu VS Code.

Przed rozpoczęciem:

* Upewnij się, że [program inicjujący na urządzeniu IoT DevKit jest w wersji 1.4.0 lub nowszej](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Upewnij się, że zestaw SDK urządzenia IoT DevKit jest w tej samej wersji co program inicjujący. Zestaw SDK urządzenia IoT DevKit możesz zaktualizować za pomocą rozszerzenia Azure IoT Workbench w programie VS Code. Otwórz paletę poleceń i wprowadź polecenie **Arduino: Board Manager**. Aby uzyskać więcej informacji, zobacz [Przygotowywanie środowiska deweloperskiego](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Musisz również połączyć co najmniej jedno urządzenie IoT DevKit z akceleratorem rozwiązania do monitorowania zdalnego. Jeśli nie połączono jeszcze urządzenia IoT DevKit, zobacz [Łączenie urządzenia MXChip IoT DevKit AZ3166 z akceleratorem rozwiązania do monitorowania zdalnego IoT](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Przechodzenie do pulpitu nawigacyjnego

Aby wyświetlić pulpit nawigacyjny rozwiązania do monitorowania zdalnego w przeglądarce, najpierw odwiedź stronę [Akceleratory rozwiązania usługi Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard). Może być konieczne zalogowanie się przy użyciu poświadczeń subskrypcji platformy Azure.

Następnie kliknij pozycję **Uruchom** na kafelku akceleratora rozwiązania do monitorowania zdalnego wdrożonego w przewodniku [Szybki start](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Tworzenie grupy urządzeń

Aby oprogramowanie układowe było automatycznie aktualizowane w grupie urządzeń, urządzenia muszą być elementami członkowskimi grupy urządzeń w rozwiązaniu do monitorowania zdalnego:

1. Na stronie **Urządzenia** wybierz wszystkie urządzenia **IoT DevKit**, które zostały połączone z akceleratorem rozwiązań. Następnie kliknij pozycję **zadania**.

1. W panelu **zadania** wybierz pozycję **Tagi**, Ustaw nazwę zadania na **AddDevKitTag**, a następnie Dodaj tag tekstowy o nazwie **IsDevKitDevice** z wartością **Y**. Następnie kliknij przycisk **Zastosuj**.

1. Teraz możesz użyć wartości tagów do utworzenia grupy urządzeń. Na stronie **urządzenia** kliknij pozycję **Zarządzaj grupami urządzeń**.

1. Utwórz filtr tekstowy, używając w warunku nazwy tagu **IsDevKitDevice** i wartości **Y**. Zapisz grupę urządzeń pod nazwą **Urządzenia IoT DevKit**.

W dalszej części tego samouczka zastosujesz dla tej grupy urządzeń konfigurację urządzenia, która zaktualizuje oprogramowanie układowe wszystkich elementów członkowskich grupy.

## <a name="prepare-and-host-the-firmware"></a>Przygotowanie i hostowanie oprogramowania układowego

Rozszerzenie [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) programu VS Code zawiera przykładowy kod urządzenia powodujący aktualizację oprogramowania układowego.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Otwórz przykład Firmware OTA w programie VS Code

1. Upewnij się, że urządzenie IoT DevKit nie jest podłączone do komputera. Uruchom program VS Code, a następnie podłącz urządzenie DevKit do komputera.

1. Naciśnij klawisz **F1** aby otworzyć paletę poleceń, wpisz i wybierz polecenie **IoT Workbench: Examples**. Następnie jako kartę wybierz pozycję **IoT DevKit**.

1. Znajdź przykład **Firmware OTA** i kliknij przycisk **Open Sample** (Otwórz przykład). Zostanie otwarte nowe okno programu VS Code z wyświetlonym folderem projektu **firmware_ota**:

    ![IoT Workbench — wybieranie przykładu Firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Kompilowanie nowego oprogramowania układowego

Początkową wersją oprogramowania układowego urządzenia jest wersja 1.0.0. Nowe oprogramowanie układowe powinno mieć wyższy numer wersji.

1. W programie VS Code otwórz plik **FirmwareOTA.ino** i zmień wartość parametru `currentFirmwareVersion` z `1.0.0` na `1.0.1`:

    ![Zmiana wersji oprogramowania układowego](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otwórz paletę poleceń, a następnie wpisz i wybierz polecenie **IoT Workbench: Device**. Wybierz pozycję **Device Compile** (Kompilacja urządzenia), aby skompilować kod:

    ![Kompilacja urządzenia](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    Program VS Code zapisuje skompilowany plik w folderze `.build` projektu. W zależności od ustawień program VS Code może ukrywać folder `.build` w widoku eksploratora.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Generowanie wartości CRC i obliczanie rozmiaru pliku oprogramowania układowego

1. Otwórz paletę poleceń, a następnie wpisz i wybierz polecenie **IoT Workbench: Device**. Wybierz pozycję **Generate CRC** (Generuj CRC):

    ![Generowanie CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. Program VS Code generuje i wyświetla w oknie danych wyjściowych wartość CRC, nazwę pliku oprogramowania układowego, ścieżkę do tego pliku i jego rozmiar. Zanotuj te wartości do późniejszego użycia:

    ![Informacje o kontroli CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Przekazywanie oprogramowania układowego do chmury

Użyj swojego konta usługi Azure Storage do hostowania nowego pliku oprogramowania układowego w chmurze.

1. W witrynie Azure Portal przejdź do swojego konta magazynu. W sekcji Usługi wybierz pozycję **Obiekty blob**. Utwórz publiczny kontener o nazwie **firmware** do przechowywania plików oprogramowania układowego:

    ![Tworzenie folderu](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. Aby przekazać plik oprogramowania układowego do kontenera, wybierz kontener **firmware** i kliknij przycisk **Przekaż**.

1. Wybierz plik **FirmwareOTA.ino.bin**. W poprzedniej sekcji zanotowano pełną ścieżkę do tego pliku.

1. Po zakończeniu przekazywania pliku oprogramowania układowego zanotuj adres URL tego pliku.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Kompilowanie oryginalnego oprogramowania układowego i jego przekazywanie do urządzenia IoT DevKit

1. W programie VS Code otwórz plik **FirmwareOTA.ino** i zmień wartość parametru `currentFirmwareVersion` z powrotem na `1.0.0`:

    ![Wersja 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Otwórz paletę poleceń, a następnie wpisz i wybierz polecenie **IoT Workbench: Device**. Wybierz pozycję **Device Upload** (Przekazywanie do urządzenia):

    ![Przekazywanie do urządzenia](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. Program VS Code sprawdza i przekazuje kod do urządzenia IoT DevKit.

1. Po zakończeniu przekazywania następuje ponowne uruchomienie urządzenia IoT DevKit. Po zakończeniu ponownego uruchamiania na ekranie urządzenia IoT DevKit widać komunikat **FW version: 1.0.0** (Oprogramowanie układowe — wersja: 1.0.0) oraz komunikat o sprawdzaniu dostępności nowego oprogramowania układowego:

    ![Na zdjęciu jest wyświetlane urządzenie DevKit I o, na którym jest wyświetlana wersja oprogramowania układowego i sprawdzanie nowego oprogramowania układowego.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Tworzenie konfiguracji urządzenia

Konfiguracja urządzenia określa pożądany stan urządzeń. Zazwyczaj deweloper [tworzy konfigurację](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) na stronie **Konfiguracja urządzeń IoT** w witrynie Azure Portal. Konfiguracja urządzenia to dokument JSON, który określa pożądany stan urządzeń oraz zestaw metryk.

Zapisz poniższą konfigurację na komputerze lokalnym w pliku o nazwie **firmware-update.json**. Zastąp elementy zastępcze `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` i `YOURPACKAGESIZE` wartościami zanotowanymi wcześniej:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Użyjesz tego pliku konfiguracji w poniższej sekcji.

## <a name="import-a-configuration"></a>Importowanie konfiguracji

W tej sekcji zaimportujesz konfigurację urządzenia jako pakiet do akceleratora rozwiązania do monitorowania zdalnego. To zadanie jest zwykle wykonywane przez operatora.

1. W internetowym interfejsie użytkownika zdalnego monitorowania przejdź do strony **Pakiety** i kliknij pozycję **+ Nowy pakiet**:

    ![Nowy pakiet](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. Na panelu **Nowy pakiet** wybierz pozycje **Konfiguracja urządzenia** jako typ pakietu i **Oprogramowanie układowe** jako typ konfiguracji. Kliknij przycisk **Przeglądaj**, aby znaleźć plik **firmware-update.json** na komputerze lokalnym, a następnie kliknij przycisk **Przekaż**:

    ![Przekazywanie pakietu](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. Na liście pakietów znajduje się teraz pakiet **firmware-update**.

## <a name="deploy-the-configuration-to-your-devices"></a>Wdrażanie konfiguracji na urządzeniach

W tej sekcji utworzysz i wykonasz wdrożenie, które zastosuje konfigurację urządzenia do Twojego urządzenia IoT DevKit.

1. W internetowym interfejsie użytkownika zdalnego monitorowania przejdź do strony **Wdrożenia** i kliknij pozycję **+ Nowe wdrożenie**:

    ![Nowe wdrożenie](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. Na panelu **Nowe wdrożenie** utwórz wdrożenie, używając następujących ustawień:

    |Opcja|Wartość|
    |---|---|
    |Nazwa|Wdrażanie aktualizacji oprogramowania układowego|
    |Typ pakietu|Konfiguracja urządzenia|
    |Typ konfiguracji|Oprogramowanie układowe|
    |Pakiet|firmware-update.json|
    |Grupa urządzeń|Urządzenia IoT DevKit|
    |Priorytet|10|

    ![Tworzenie wdrożenia](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Kliknij pozycję **Zastosuj**. Nowe wdrożenie będzie widoczne na stronie **Wdrożenia** razem z następującymi metrykami:

    * **Docelowe** — liczba urządzeń w grupie urządzeń.
    * **Zastosowane** — pokazuje liczbę urządzeń, które zostały zaktualizowane za pomocą zawartości konfiguracji.
    * **Zakończone powodzeniem** — pokazuje liczbę urządzeń w ramach wdrożenia, które raportują powodzenie.
    * **Zakończone niepowodzeniem** — pokazuje liczbę urządzeń w ramach wdrożenia, które raportują niepowodzenie.

## <a name="monitor-the-deployment"></a>Monitorować wdrożenie.

Po kilku minutach urządzenie IoT DevKit uzyska informacje o nowym oprogramowaniu układowym i rozpocznie jego pobieranie:

![Na zdjęciu jest wyświetlane urządzenie z DevKit I o.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

W zależności od szybkości sieci pobieranie może potrwać kilka minut. Po pobraniu oprogramowania układowego urządzenie sprawdza rozmiar pliku oraz wartość CRC. Na ekranie urządzenia MXChip wyświetlany jest komunikat **passed** (zakończone pomyślnie), jeśli weryfikacja zakończy się powodzeniem.

![Na zdjęciu jest wyświetlane urządzenie z DevKit I o, na którym jest wyświetlana wersja oprogramowania układowego i przekazana do powodzenia weryfikacji.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Jeśli sprawdzenie zakończy się pomyślnie, nastąpi ponowne uruchomienie urządzenia. Przed rozpoczęciem ponownego uruchamiania nastąpi odliczanie od **5** do **0**.

![Na zdjęciu zostanie wyświetlone urządzenie DevKit I na urządzeniu, które zostanie przygotowane do ponownego uruchomienia.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Po ponownym uruchomieniu program inicjujący urządzenia IoT DevKit uaktualnia oprogramowanie układowe do nowej wersji. Uaktualnienie może potrwać kilka sekund. Na tym etapie dioda LED RGB urządzenia świeci na czerwono, a ekran pozostaje pusty.

![Na zdjęciu zostanie wyświetlone urządzenie z DevKitem I, które wykonuje uaktualnienie. Nie ma wyświetlania, ale występuje czerwony poświata R G B L E D.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

Po zakończeniu ponownego uruchamiania na urządzeniu IoT DevKit będzie działało oprogramowanie układowe w wersji 1.0.1.

![Na zdjęciu jest wyświetlane urządzenie z DevKit I o.](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

Na stronie **Wdrożenia** kliknij wdrożenie, aby wyświetlić stan urządzeń po ich aktualizacji. Zobaczysz stan poszczególnych urządzeń w grupie urządzeń i zdefiniowane metryki niestandardowe.

![Szczegóły wdrożenia](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak zaktualizować oprogramowanie układowe grupy urządzeń połączonych z rozwiązaniem. Do aktualizowania urządzeń rozwiązanie używa funkcji automatycznego zarządzania urządzeniami. Aby dowiedzieć się więcej na temat funkcji automatycznego zarządzania urządzeniami działającej w bazowym centrum IoT rozwiązania, zobacz [Konfigurowanie i monitorowanie urządzeń IoT na dużą skalę przy użyciu witryny Azure Portal](../iot-hub/iot-hub-auto-device-config.md).