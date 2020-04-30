---
title: Dodawanie urządzenia Azure IoT Edge do usługi Azure IoT Central | Microsoft Docs
description: Jako operator Dodaj urządzenie Azure IoT Edge do aplikacji IoT Central platformy Azure
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c60cf4b90b089d271c0ccd91031420efe9017b1e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758157"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Samouczek: Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure

*Ten artykuł dotyczy konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak skonfigurować i dodać urządzenie Azure IoT Edge do aplikacji IoT Central platformy Azure. Samouczek korzysta z maszyny wirtualnej z systemem Linux z obsługą IoT Edge z poziomu portalu Azure Marketplace w celu symulowania urządzenia IoT Edge. Urządzenie IoT Edge używa modułu, który generuje symulowane dane telemetryczne środowiska. Dane telemetryczne są wyświetlane na pulpicie nawigacyjnym w aplikacji IoT Central.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szablonu urządzenia dla urządzenia IoT Edge
> * Utwórz urządzenie IoT Edge w IoT Central
> * Wdrażanie symulowanego urządzenia IoT Edge na maszynie wirtualnej z systemem Linux

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) , aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > aplikacji niestandardowej** .

Aby wykonać kroki opisane w tym samouczku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Pobierz plik manifestu IoT Edge z usługi GitHub. Kliknij prawym przyciskiem myszy poniższy link, a następnie wybierz pozycję **Zapisz link jako**: [EnvironmentalSensorManifest. JSON](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Utwórz szablon urządzenia

W tej sekcji utworzysz szablon urządzenia dla IoT Edge urządzenia, które nawiązuje połączenie z aplikacją IoT Central. Zaimportujmy manifest IoT Edge, aby rozpocząć pracę, a następnie zmodyfikować szablon w celu dodania definicji i widoków telemetrii:

### <a name="import-manifest-to-create-template"></a>Importowanie manifestu w celu utworzenia szablonu

Aby utworzyć szablon urządzenia na podstawie manifestu IoT Edge:

1. W aplikacji IoT Central przejdź do **szablonów urządzeń** i wybierz pozycję **+ Nowy**.

1. Na stronie **Wybieranie typu szablonu** wybierz kafelek **Azure IoT Edge** . Następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Przekaż manifest wdrożenia Azure IoT Edge** wybierz pozycję **Przeglądaj** , aby przekazać wcześniej pobrany plik **EnvironmentalSensorManifest. JSON** . Następnie wybierz kolejno pozycje **Dalej: przegląd**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**.

1. Po utworzeniu szablonu zmień jego nazwę na *Urządzenie brzegowe czujnika środowiska*.

1. Wybierz Interfejs **zarządzania** w module **SimulatedTemperatureSensor** , aby wyświetlić dwie właściwości zdefiniowane w manifeście:

![Szablon urządzenia utworzony na podstawie IoT Edge manifestu](./media/tutorial-add-edge-as-leaf-device/imported-manifest.png)

### <a name="add-telemetry-to-manifest"></a>Dodaj telemetrię do manifestu

Manifest IoT Edge nie definiuje telemetrii wysyłanej przez moduł. Należy dodać definicje telemetrii do szablonu urządzenia. Moduł **SimulatedTemperatureSensor** wysyła komunikaty telemetryczne, które wyglądają podobnie jak w poniższym kodzie JSON:

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

Aby dodać definicje telemetrii do szablonu urządzenia:

1. Wybierz pozycję **Zarządzaj** interfejsem w szablonie **urządzenia brzegowego czujnika środowiska** .

1. Wybierz pozycję **+ Dodaj funkcję**. Wprowadź *maszynę* jako **nazwę wyświetlaną** i upewnij się, że **Typ możliwości** to **telemetrię**.

1. Wybierz **obiekt** jako typ schematu, a następnie wybierz pozycję **Definiuj**. Na stronie Definicja obiektu Dodaj *temperaturę* i *ciśnienie* jako atrybuty typu **Double** , a następnie wybierz pozycję **Zastosuj**.

1. Wybierz pozycję **+ Dodaj funkcję**. Wprowadź wartość *otaczającą* jako **nazwę wyświetlaną** i upewnij się, że **Typ możliwości** to **Telemetria**.

1. Wybierz **obiekt** jako typ schematu, a następnie wybierz pozycję **Definiuj**. Na stronie Definicja obiektu Dodaj *temperaturę* i *wilgotność* jako atrybuty typu **Double** , a następnie wybierz pozycję **Zastosuj**.

1. Wybierz pozycję **+ Dodaj funkcję**. Wprowadź *timeCreated* jako **nazwę wyświetlaną** i upewnij się, że **Typ możliwości** to **telemetrię**.

1. Wybierz **element DateTime** jako typ schematu.

1. Wybierz pozycję **Zapisz** , aby zaktualizować szablon.

Interfejs **zarządzania** zawiera teraz typy telemetrii **maszyny**, **otoczenia**i **timeCreated** :

![Interfejs z typami danych telemetrii maszyn i otoczenia](./media/tutorial-add-edge-as-leaf-device/manage-interface.png)

### <a name="add-views-to-template"></a>Dodawanie widoków do szablonu

Szablon urządzenia nie ma jeszcze widoku, który umożliwia operatorowi wyświetlenie danych telemetrycznych z urządzenia IoT Edge. Aby dodać widok do szablonu urządzenia:

1. Wybierz pozycję **widoki** w szablonie **urządzenia brzegowego czujnika środowiska** .

1. Na stronie **Wybierz, aby dodać nowy widok** kliknij kafelek **Wizualizacja urządzenia** .

1. Zmień nazwę widoku, aby *wyświetlić dane telemetryczne urządzenia IoT Edge*.

1. Wybierz typy danych telemetrii **otoczenia** i **maszyny** . Następnie wybierz pozycję **Dodaj kafelek**.

1. Wybierz pozycję **Zapisz** , aby zapisać widok **IoT Edge danych telemetrycznych urządzenia** .

![Szablon urządzenia z widokiem telemetrii](./media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png)

### <a name="publish-the-template"></a>Publikowanie szablonu

Aby można było dodać urządzenie, które używa szablonu **urządzenia brzegowego czujnika środowiska** , należy opublikować szablon.

Przejdź do szablonu **urządzenia brzegowego czujnika środowiska** i wybierz pozycję **Publikuj**. Następnie wybierz pozycję **Publikuj** , aby opublikować szablon:

![Publikowanie szablonu urządzenia](./media/tutorial-add-edge-as-leaf-device/publish-template.png)

## <a name="add-iot-edge-device"></a>Dodawanie urządzenia usługi IoT Edge

Po opublikowaniu szablonu **urządzenia brzegowego czujnika środowiska** możesz dodać urządzenie do aplikacji IoT Central:

1. W aplikacji IoT Central przejdź do strony **urządzenia** i wybierz z listy dostępnych szablonów pozycję **Urządzenie brzegowe czujnika środowiska** .

1. Wybierz **+** , aby dodać nowe urządzenie z szablonu. Na stronie **Tworzenie nowego urządzenia** wybierz pozycję **Utwórz**.

Masz teraz nowe urządzenie o stanie **zarejestrowano**:

![Publikowanie szablonu urządzenia](./media/tutorial-add-edge-as-leaf-device/new-device.png)

### <a name="get-the-device-credentials"></a>Pobieranie poświadczeń urządzenia

Wdrożenie urządzenia IoT Edge w dalszej części tego samouczka wymaga poświadczeń, które umożliwią urządzeniu łączenie się z aplikacją IoT Central. Pobierz poświadczenia urządzenia:

1. Na stronie **urządzenie** wybierz utworzone urządzenie.

1. Wybierz przycisk **Połącz**.

1. Na stronie **połączenie urządzenia** Zanotuj **zakres identyfikatorów**, **Identyfikator urządzenia**i **klucz podstawowy**. Te wartości są używane później.

1. Wybierz polecenie **Zamknij**.

Zakończono konfigurowanie aplikacji IoT Central, aby umożliwić urządzeniu IoT Edge nawiązywanie połączenia.

## <a name="deploy-an-iot-edge-device"></a>Wdrażanie urządzenia IoT Edge

W tym samouczku zostanie użyta maszyna wirtualna z systemem Linux z obsługą Azure IoT Edge, utworzona na platformie Azure w celu symulowania urządzenia IoT Edge. Aby utworzyć maszynę wirtualną z obsługą IoT Edge:

1. Przejdź do [Azure IoT Edge na Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) w portalu Azure Marketplace. Następnie wybierz pozycję **Pobierz teraz**.

1. Na stronie **Tworzenie tej aplikacji na platformie Azure** wybierz pozycję **Kontynuuj**. Ten link prowadzi do Azure Portal, w którym może być konieczne zalogowanie się do subskrypcji platformy Azure.

1. Na stronie **Azure IoT Edge na Ubuntu** w Azure Portal wybierz pozycję **Utwórz**.

1. Na stronie **Tworzenie maszyny wirtualnej > podstawowe** :

    - Wybierz swoją subskrypcję platformy Azure.
    - Utwórz nową grupę zasobów o nazwie **IoT-Edge-Devices**.
    - Użyj nazwy maszyny wirtualnej: **iotedgevm**.
    - Wybierz najbliższy region.
    - Ustaw typ uwierzytelniania na **hasło**.
    - Wybierz nazwę użytkownika i hasło.
    - Pozostałe opcje można pozostawić do wartości domyślnych.
    - Wybierz pozycję **Przegląd + utwórz**.

1. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

Po upływie kilku minut, po zakończeniu wdrażania, wybierz pozycję **Przejdź do zasobu**.

### <a name="provision-vm-as-an-iot-edge-device"></a>Udostępnianie maszyny wirtualnej jako urządzenia IoT Edge 

Aby udostępnić maszynę wirtualną jako urządzenie IoT Edge:

1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**.

1. Naciśnij klawisz **Enter** , `login:` aby wyświetlić monit. Wprowadź nazwę użytkownika i hasło, aby się zalogować.

1. Uruchom następujące polecenie, aby sprawdzić wersję środowiska uruchomieniowego IoT Edge. W momencie pisania wersja jest 1.0.8:

    ```bash
    sudo iotedge --version
    ```

1. Użyj `nano` edytora, aby otworzyć plik IoT Edge config. YAML:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Przewiń w dół do momentu wyświetlenia `# Manual provisioning configuration`. Dodaj komentarz do następnych trzech wierszy, jak pokazano w poniższym fragmencie kodu:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
    ```

1. Przewiń w dół do momentu wyświetlenia `# DPS symmetric key provisioning configuration`. Usuń komentarz z następnych ośmiu wierszy, jak pokazano w poniższym fragmencie kodu:

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

1. Zamień `{scope_id}` na **zakres identyfikatora** dla wykonanej wcześniej uwagi.

1. Zastąp `{registration_id}` ciąg **identyfikatorem urządzenia** , który został wcześniej zanotowany.

1. Zamień `{symmetric_key}` na **klucz podstawowy** , który został wcześniej zanotowany.

1. Zapisz zmiany (**Ctrl-O**) i wyjdź z `nano` edytora (**Ctrl-X**).

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

## <a name="view-the-telemetry"></a>Wyświetlanie telemetrii

Symulowane urządzenie IoT Edge jest teraz uruchomione na maszynie wirtualnej. W aplikacji IoT Central stan urządzenia jest teraz **inicjowany** na stronie **urządzenia** :

![Zainicjowane urządzenie](./media/tutorial-add-edge-as-leaf-device/provisioned-device.png)

Dane telemetryczne są widoczne na stronie **wyświetl IoT Edge telemetrii urządzenia** :

![Dane telemetryczne urządzenia](./media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png)

Na stronie **moduły** zostanie wyświetlony stan modułów IoT Edge:

![Dane telemetryczne urządzenia](./media/tutorial-add-edge-as-leaf-device/edge-module-status.png)

## <a name="next-steps"></a>Następne kroki

Jako deweloper urządzenia teraz wiesz, jak pracować z urządzeniami IoT Edge i zarządzać nimi w IoT Central, sugerowanym następnym krokiem jest odczytanie:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Połącz z usługą Azure IoT Central](./concepts-get-connected.md)
