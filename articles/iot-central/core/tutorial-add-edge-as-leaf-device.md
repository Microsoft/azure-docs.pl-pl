---
title: Dodawanie urządzenia usługi Azure IoT Edge do usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Jako operator dodaj urządzenie Usługi Azure IoT Edge do aplikacji Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758157"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Samouczek: Dodawanie urządzenia usługi Azure IoT Edge do aplikacji Azure IoT Central

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak skonfigurować i dodać urządzenie usługi Azure IoT Edge do aplikacji Usługi Azure IoT Central. W samouczku użyto maszyny wirtualnej systemu Linux obsługującej usługę IoT Edge z witryny Azure Marketplace do symulowania urządzenia usługi IoT Edge. Urządzenie usługi IoT Edge używa modułu, który generuje symulowane dane telemetryczne środowiska. Dane telemetryczne można wyświetlić na pulpicie nawigacyjnym w aplikacji IoT Central.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szablonu urządzenia dla urządzenia IoT Edge
> * Tworzenie urządzenia IoT Edge w centrum IoT
> * Wdrażanie symulowanego urządzenia IoT Edge na maszynie wirtualnej z systemem Linux

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ szybki start [aplikacji Azure IoT Central,](./quick-deploy-iot-central.md) aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > niestandardowej.**

Aby wykonać kroki opisane w tym samouczku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

Pobierz plik manifestu usługi IoT Edge z usługi GitHub. Kliknij prawym przyciskiem myszy poniższy link, a następnie wybierz **pozycję Zapisz łącze jako**: [EnvironmentalSensorManifest.json](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Tworzenie szablonu urządzenia

W tej sekcji utworzysz szablon urządzenia dla urządzenia Usługi IoT Edge, które łączy się z aplikacją IoT Central. Zaimportować manifest usługi IoT Edge, aby rozpocząć, a następnie zmodyfikuj szablon, aby dodać definicje i widoki telemetryczne:

### <a name="import-manifest-to-create-template"></a>Zaimportuj manifest do tworzenia szablonu

Aby utworzyć szablon urządzenia z manifestu IoT Edge:

1. W aplikacji IoT Central przejdź do **szablonów urządzeń** i wybierz + **Nowy**.

1. Na stronie **Wybierz typ szablonu** wybierz kafelek **usługi Azure IoT Edge.** Następnie wybierz **przycisk Dalej: Dostosuj**.

1. Na stronie **narzędzia azure ioT edge wdrożenia** usługi Azure Wybierz pozycję **Przeglądaj,** aby przekazać **środowisko EnvironmentalSensorManifest.json** pobrane wcześniej. Następnie wybierz **przycisk Dalej: Przejrzyj**.

1. Na stronie **Recenzja** wybierz pozycję **Utwórz**.

1. Po utworzeniu szablonu zmień jego nazwę na *Urządzenie krawędziowe czujnika środowiskowego*.

1. Wybierz interfejs **Zarządzania** w module **SimulatedTemperatureSensor,** aby wyświetlić dwie właściwości zdefiniowane w manifeście:

![Szablon urządzenia utworzony na podstawie manifestu IoT Edge](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Dodawanie danych telemetrycznych do manifestu

Manifest usługi IoT Edge nie definiuje danych telemetrycznych wysyłanej przez moduł. Należy dodać definicje danych telemetrycznych do szablonu urządzenia. Moduł **SimulatedTemperatureSensor** wysyła komunikaty telemetryczne, które wyglądają jak następujący JSON:

```json
{
    "machine": {
        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Aby dodać definicje danych telemetrycznych do szablonu urządzenia:

1. Wybierz interfejs **Zarządzanie** w szablonie **Urządzenie krawędzi czujnika środowiska.**

1. Wybierz **+ Dodaj możliwość**. Wprowadź *komputer* jako **nazwę wyświetlaną** i upewnij się, że **typem możliwości** jest **Telemetria**.

1. Zaznacz **obiekt** jako typ schematu, a następnie wybierz polecenie **Definiuj**. Na stronie definicji obiektu dodaj *temperaturę* i *ciśnienie* jako atrybuty typu **Double,** a następnie wybierz pozycję **Zastosuj**.

1. Wybierz **+ Dodaj możliwość**. Wprowadź *otoczenie* jako **nazwę wyświetlaną** i upewnij się, że **typem możliwości** jest **Telemetria**.

1. Zaznacz **obiekt** jako typ schematu, a następnie wybierz polecenie **Definiuj**. Na stronie definicji obiektu dodaj *temperaturę* i *wilgotność* jako atrybuty typu **Double,** a następnie wybierz pozycję **Zastosuj**.

1. Wybierz **+ Dodaj możliwość**. Wprowadź *timeCreated* jako **nazwę wyświetlaną** i upewnij się, że **typem możliwości** jest **Telemetria**.

1. Wybierz **DateTime** jako typ schematu.

1. Wybierz **pozycję Zapisz,** aby zaktualizować szablon.

Interfejs **Zarządzanie** zawiera teraz **typy**danych telemetrycznych maszyny, **otoczenia**i **czasuPracowane:**

![Interfejs z typami telemetrii maszynowej i otoczenia](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Dodawanie widoków do szablonu

Szablon urządzenia nie ma jeszcze widoku, który umożliwia operatorowi wyświetlanie danych telemetrycznych z urządzenia usługi IoT Edge. Aby dodać widok do szablonu urządzenia:

1. Wybierz **widoki** w szablonie **Urządzenie krawędzi czujnika środowiska.**

1. Na stronie **Wybierz, aby dodać nowy widok,** wybierz kafelek **Wizualizacja urządzenia.**

1. Zmień nazwę widoku na *Wyświetlanie danych telemetrycznych urządzenia usługi IoT Edge*.

1. Wybierz typy danych telemetrycznych **otoczenia** i **maszyny.** Następnie wybierz pozycję **Dodaj kafelek**.

1. Wybierz **pozycję Zapisz,** aby zapisać widok **telemetrii urządzenia Widoku IoT Edge.**

![Szablon urządzenia z widokiem telemetrycznym](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publikowanie szablonu

Aby można było dodać urządzenie korzystające z szablonu **Urządzenia krawędzi czujnika środowiska,** należy opublikować szablon.

Przejdź do szablonu **Urządzenia krawędzi czujnika środowiskowego** i wybierz pozycję **Publikuj**. Następnie wybierz **pozycję Publikuj,** aby opublikować szablon:

![Publikowanie szablonu urządzenia](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Dodawanie urządzenia usługi IoT Edge

Teraz opublikowano szablon **Urządzenia krawędzi czujnika środowiska,** możesz dodać urządzenie do aplikacji IoT Central:

1. W aplikacji IoT Central przejdź do strony **Urządzenia** i wybierz **pozycję Urządzenie krawędziowe czujnika środowiska** na liście dostępnych szablonów.

1. Wybierz, **+** aby dodać nowe urządzenie z szablonu. Na stronie **Tworzenie nowego urządzenia** wybierz pozycję **Utwórz**.

Masz teraz nowe urządzenie o statusie **Zarejestrowany:**

![Publikowanie szablonu urządzenia](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Uzyskaj poświadczenia urządzenia

Podczas wdrażania urządzenia usługi IoT Edge w dalszej części tego samouczka, należy poświadczenia, które umożliwiają urządzeniu połączyć się z aplikacją IoT Central. Pobierz poświadczenia urządzenia:

1. Na stronie **Urządzenie** wybierz utworzone urządzenie.

1. Wybierz przycisk **Połącz**.

1. Na stronie **Połączenie z urządzeniem** zanotuj **zakres identyfikatora**, **identyfikator urządzenia**i **klucz podstawowy**. Te wartości są używane później.

1. Wybierz polecenie **Zamknij**.

Zakończono konfigurowanie aplikacji IoT Central, aby umożliwić połączenie urządzenia Usługi IoT Edge.

## <a name="deploy-an-iot-edge-device"></a>Wdrażanie urządzenia IoT Edge

W tym samouczku używasz maszyny wirtualnej z systemem Linux z obsługą usługi Azure IoT Edge, utworzonej na platformie Azure w celu symulowania urządzenia usługi IoT Edge. Aby utworzyć maszynę wirtualną z obsługą krawędzi IoT:

1. Przejdź do [usługi Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) w portalu Azure Marketplace. Następnie wybierz pozycję **Pobierz teraz**.

1. Na stronie **Utwórz tę aplikację na platformie Azure** wybierz pozycję **Kontynuuj**. To łącze spowoduje przejście do witryny Azure portal, w której może być konieczne zalogowanie się do subskrypcji platformy Azure.

1. Na stronie **Azure IoT Edge na Ubuntu** w witrynie Azure portal wybierz pozycję **Utwórz**.

1. Na stronie **Tworzenie maszyny wirtualnej > podstawy:**

    - Wybierz swoją subskrypcję platformy Azure.
    - Utwórz nową grupę zasobów o nazwie **iot-edge-devices**.
    - Użyj nazwy maszyny wirtualnej: **iotedgevm**.
    - Wybierz najbliższy region.
    - Ustaw typ uwierzytelniania na **Hasło**.
    - Wybierz nazwę użytkownika i hasło.
    - Pozostałe opcje można pozostawić wartościom domyślnym.
    - Wybierz pozycję **Przegląd + utwórz**.

1. Po zakończeniu sprawdzania poprawności wybierz pozycję **Utwórz**.

Po kilku minutach po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Aprowizuj maszynę wirtualną jako urządzenie Z krawędzią IoT 

Aby aprowizować maszynę wirtualną jako urządzenie Z krawędzią IoT:

1. W sekcji **Pomoc techniczna + rozwiązywanie problemów** wybierz **pozycję Konsola szeregowa**.

1. Naciśnij **klawisz Enter,** aby wyświetlić `login:` monit. Wprowadź swoją nazwę użytkownika i hasło, aby się zalogować.

1. Uruchom następujące polecenie, aby sprawdzić wersję środowiska wykonawczego IoT Edge. W momencie pisania, wersja jest 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Użyj `nano` edytora, aby otworzyć plik config.yaml usługi IoT Edge:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Przewiń w `# Manual provisioning configuration`dół, aż zobaczysz . Skomentuj następne trzy wiersze, jak pokazano w poniższym urywek:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Przewiń w `# DPS symmetric key provisioning configuration`dół, aż zobaczysz . Odkomentuj następne osiem wierszy, jak pokazano w poniższym urywek:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Zamień `{scope_id}` na **zakres identyfikatora,** o których wcześniej zanotowano.

1. Zastąp `{registration_id}` **identyfikatorem urządzenia,** o którego wcześniej zanotowano.

1. Zamień `{symmetric_key}` **na klucz podstawowy,** który wcześniej zanotowano.

1. Zapisz zmiany (**Ctrl-O**) i exit ( `nano` **Ctrl-X**) edytora.

1. Uruchom następujące polecenie, aby ponownie uruchomić demona IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Aby sprawdzić stan modułów IoT Edge, uruchom następujące polecenie:

    ```bash
    iotedge list
    ```

    Dane wyjściowe wyglądają następująco:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

## <a name="view-the-telemetry"></a>Wyświetlanie danych telemetrycznych

Symulowane urządzenie IoT Edge jest teraz uruchomione na maszynie wirtualnej. W aplikacji IoT Central stan urządzenia jest teraz **aprowizowana** na stronie **Urządzenia:**

![Urządzenie aprowizowane](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Dane telemetryczne można wyświetlić na stronie **danych telemetrycznych urządzenia Widok usługi IoT Edge:**

![Telemetria urządzenia](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Strona **Moduły** pokazuje stan modułów IoT Edge:

![Telemetria urządzenia](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Następne kroki

Jako deweloper urządzeń, teraz, gdy masz instrukcje pracy z urządzeniami IoT Edge i zarządzania nimi w UIOT Central, sugerowanym następnym krokiem jest przeczytanie:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Nawiązywać połączenia z usługą Azure IoT Central](./concepts-get-connected.md)
