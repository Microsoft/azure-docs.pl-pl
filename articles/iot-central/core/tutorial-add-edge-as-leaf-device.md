---
title: Samouczek — Dodawanie urządzenia Azure IoT Edge do usługi Azure IoT Central | Microsoft Docs
description: Samouczek — jako operator Dodaj urządzenie Azure IoT Edge do aplikacji IoT Central platformy Azure
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724884"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Samouczek: Dodawanie urządzenia Azure IoT Edge do aplikacji IoT Central platformy Azure

*Ten artykuł ma zastosowanie do operatorów, konstruktorów rozwiązań i deweloperów urządzeń.*

W tym samouczku pokazano, jak skonfigurować i dodać urządzenie Azure IoT Edge do aplikacji IoT Central platformy Azure. Samouczek korzysta z maszyny wirtualnej z systemem Linux z obsługą IoT Edge, aby symulować IoT Edge urządzenie. Urządzenie IoT Edge używa modułu, który generuje symulowane dane telemetryczne środowiska. Dane telemetryczne są wyświetlane na pulpicie nawigacyjnym w aplikacji IoT Central.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szablonu urządzenia dla urządzenia IoT Edge
> * Utwórz urządzenie IoT Edge w IoT Central
> * Wdrażanie symulowanego urządzenia IoT Edge na maszynie wirtualnej z systemem Linux

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) , aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > aplikacji niestandardowej** .

Aby wykonać kroki opisane w tym samouczku, musisz mieć aktywną subskrypcję platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Pobierz plik manifestu IoT Edge z usługi GitHub. Kliknij prawym przyciskiem myszy poniższy link, a następnie wybierz pozycję **Zapisz link jako**: [EnvironmentalSensorManifest.js](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json)

## <a name="create-device-template"></a>Utwórz szablon urządzenia

W tej sekcji utworzysz szablon IoT Central urządzenia dla urządzenia IoT Edge. Zaimportujmy manifest IoT Edge, aby rozpocząć pracę, a następnie zmodyfikować szablon w celu dodania definicji i widoków telemetrii:

### <a name="import-manifest-to-create-template"></a>Importowanie manifestu w celu utworzenia szablonu

Aby utworzyć szablon urządzenia na podstawie manifestu IoT Edge:

1. W aplikacji IoT Central przejdź do **szablonów urządzeń** i wybierz pozycję **+ Nowy**.

1. Na stronie **Wybieranie typu szablonu** wybierz kafelek **Azure IoT Edge** . Następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Przekaż manifest wdrożenia Azure IoT Edge** wprowadź w polu Nazwa szablonu urządzenia *Urządzenie brzegowe czujnika środowiska* . Następnie wybierz pozycję **Przeglądaj** , aby przekazać wcześniej pobrany **EnvironmentalSensorManifest.js** . Następnie wybierz kolejno pozycje **Dalej: przegląd**.

1. Na stronie **Przegląd** wybierz pozycję **Utwórz**.

1. Wybierz Interfejs **zarządzania** w module **SimulatedTemperatureSensor** , aby wyświetlić dwie właściwości zdefiniowane w manifeście:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="Szablon urządzenia utworzony na podstawie IoT Edge manifestu":::

> [!TIP]
> Ten manifest wdrożenia pobiera obrazy modułów z repozytorium Azure Container Registry, które nie wymaga żadnych poświadczeń do nawiązania połączenia. Jeśli chcesz używać obrazów modułów z repozytorium prywatnego, Ustaw poświadczenia rejestru kontenerów w manifeście.

### <a name="add-telemetry-to-manifest"></a>Dodaj telemetrię do manifestu

Manifest IoT Edge nie definiuje telemetrii wysyłanej przez moduł. Należy dodać definicje telemetrii do szablonu urządzenia w IoT Central. Moduł **SimulatedTemperatureSensor** wysyła komunikaty telemetryczne, które wyglądają podobnie jak w poniższym kodzie JSON:

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

Interfejs **zarządzania** zawiera teraz typy telemetrii **maszyny**, **otoczenia** i **timeCreated** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Interfejs z typami danych telemetrii maszyn i otoczenia":::

### <a name="add-views-to-template"></a>Dodawanie widoków do szablonu

Szablon urządzenia nie ma jeszcze widoku, który umożliwia operatorowi wyświetlenie danych telemetrycznych z urządzenia IoT Edge. Aby dodać widok do szablonu urządzenia:

1. Wybierz pozycję **widoki** w szablonie **urządzenia brzegowego czujnika środowiska** .

1. Na stronie **Wybierz, aby dodać nowy widok** kliknij kafelek **Wizualizacja urządzenia** .

1. Zmień nazwę widoku, aby *wyświetlić dane telemetryczne urządzenia IoT Edge*.

1. Wybierz typy danych telemetrii **otoczenia** i **maszyny** . Następnie wybierz pozycję **Dodaj kafelek**.

1. Wybierz pozycję **Zapisz** , aby zapisać widok **IoT Edge danych telemetrycznych urządzenia** .

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Szablon urządzenia z widokiem telemetrii":::

### <a name="publish-the-template"></a>Publikowanie szablonu

Aby można było dodać urządzenie, które używa szablonu **urządzenia brzegowego czujnika środowiska** , należy opublikować szablon.

Przejdź do szablonu **urządzenia brzegowego czujnika środowiska** i wybierz pozycję **Publikuj**. Na panelu **publikowanie tego szablonu urządzenia** wybierz pozycję **Publikuj** , aby opublikować szablon:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Publikowanie szablonu urządzenia":::

## <a name="add-iot-edge-device"></a>Dodawanie urządzenia usługi IoT Edge

Po opublikowaniu szablonu **urządzenia brzegowego czujnika środowiska** możesz dodać urządzenie do aplikacji IoT Central:

1. W aplikacji IoT Central przejdź do strony **urządzenia** i wybierz z listy dostępnych szablonów pozycję **Urządzenie brzegowe czujnika środowiska** .

1. Wybierz pozycję **+ Nowy** , aby dodać nowe urządzenie z szablonu. Na stronie **Tworzenie nowego urządzenia** wybierz pozycję **Utwórz**.

Masz teraz nowe urządzenie o stanie **zarejestrowano**:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Nowe, zarejestrowane urządzenie":::

### <a name="get-the-device-credentials"></a>Pobieranie poświadczeń urządzenia

Wdrożenie urządzenia IoT Edge w dalszej części tego samouczka wymaga poświadczeń, które umożliwią urządzeniu łączenie się z aplikacją IoT Central. Pobierz poświadczenia urządzenia:

1. Na stronie **urządzenie** wybierz utworzone urządzenie.

1. Wybierz pozycję **Połącz**.

1. Na stronie **połączenie urządzenia** Zanotuj **zakres identyfikatorów**, **Identyfikator urządzenia** i **klucz podstawowy**. Te wartości są używane później.

1. Wybierz pozycję **Zamknij**.

Zakończono konfigurowanie aplikacji IoT Central, aby umożliwić urządzeniu IoT Edge nawiązywanie połączenia.

## <a name="deploy-an-iot-edge-device"></a>Wdrażanie urządzenia IoT Edge

W tym samouczku zostanie użyta maszyna wirtualna z systemem Linux z obsługą Azure IoT Edge, utworzona na platformie Azure w celu symulowania urządzenia IoT Edge. Aby utworzyć maszynę wirtualną z obsługą IoT Edge w ramach subskrypcji platformy Azure, kliknij:

[![Przycisk Wdrażanie na platformie Azure dla szablonu iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Na stronie **wdrożenie niestandardowe** :

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów o nazwie *Central-Edge-RG*.

1. Wybierz region blisko siebie.

1. Dodaj unikatowy **prefiks etykiety DNS** , taki jak *contoso-środkowe krawędzie*.

1. Wybierz nazwę użytkownika administratora dla maszyny wirtualnej.

1. Wprowadź *temp* jako parametry połączenia. Później można skonfigurować urządzenie w celu nawiązania połączenia przy użyciu usługi DPS.

1. Zaakceptuj wartości domyślne dla rozmiaru maszyny wirtualnej, wersji Ubuntu i lokalizacji.

1. Wybierz pozycję **Password (hasło** ) jako typ uwierzytelniania.

1. Wprowadź hasło dla maszyny wirtualnej.

1. Następnie wybierz pozycję **Przegląd + Utwórz**.

1. Przejrzyj wybrane opcje, a następnie wybierz pozycję **Utwórz**:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="Tworzenie maszyny wirtualnej IoT Edge":::

Wdrożenie może potrwać kilka minut. Po zakończeniu wdrażania przejdź do grupy zasobów **Central-Edge-RG** w Azure Portal.

### <a name="configure-the-iot-edge-vm"></a>Konfigurowanie maszyny wirtualnej IoT Edge

Aby skonfigurować IoT Edge na maszynie wirtualnej w celu zarejestrowania i nawiązania połączenia z aplikacją IoT Central przy użyciu programu DPS:

1. W grupie zasobów **contoso-Edge-RG** wybierz wystąpienie maszyny wirtualnej.

1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**. Jeśli zostanie wyświetlony monit o skonfigurowanie diagnostyki rozruchu, postępuj zgodnie z instrukcjami w portalu.

1. Naciśnij klawisz **Enter** , aby wyświetlić `login:` monit. Wprowadź nazwę użytkownika i hasło, aby się zalogować.

1. Uruchom następujące polecenie, aby sprawdzić wersję środowiska uruchomieniowego IoT Edge. W momencie pisania wersja jest 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. Użyj `nano` edytora, aby otworzyć plik IoT Edge config. YAML:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Przewiń w dół do momentu wyświetlenia `# Manual provisioning configuration` . Dodaj komentarz do następnych trzech wierszy, jak pokazano w poniższym fragmencie kodu:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Przewiń w dół do momentu wyświetlenia `# DPS symmetric key provisioning configuration` . Usuń komentarz z następnych ośmiu wierszy, jak pokazano w poniższym fragmencie kodu:

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

    > [!TIP]
    > Upewnij się, że nie ma miejsca na początku `provisioning:`

1. Zamień `{scope_id}` na **zakres identyfikatorów** , który został wcześniej zanotowany.

1. Zastąp ciąg `{registration_id}` **identyfikatorem urządzenia** , który został wcześniej zanotowany.

1. Zamień na `{symmetric_key}` **klucz podstawowy** , który został wcześniej zanotowany.

1. Zapisz zmiany (**Ctrl-O**) i wyjdź z edytora (**Ctrl-X**) `nano` .

1. Uruchom następujące polecenie, aby ponownie uruchomić demona IoT Edge:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Aby sprawdzić stan modułów IoT Edge, uruchom następujące polecenie:

    ```bash
    iotedge list
    ```

    Następujące przykładowe dane wyjściowe przedstawiają uruchomione moduły:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Może być konieczne poczekanie, aż wszystkie moduły zaczną działać.

## <a name="view-the-telemetry"></a>Wyświetlanie danych telemetrycznych

Symulowane urządzenie IoT Edge jest teraz uruchomione na maszynie wirtualnej. W aplikacji IoT Central stan urządzenia jest teraz **inicjowany** na stronie **urządzenia** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Zainicjowane urządzenie IoT Edge":::

Dane telemetryczne są widoczne na urządzeniu na stronie **wyświetl IoT Edge telemetrii urządzenia** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Dane telemetryczne urządzenia":::

Na stronie **moduły** zostanie wyświetlony stan modułów IoT Edge na urządzeniu:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Stan modułu urządzenia":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z maszyną wirtualną IoT Edge, możesz zachować i ponownie użyć zasobów używanych w tym samouczku. W przeciwnym razie możesz usunąć zasoby utworzone w tym samouczku, aby uniknąć dodatkowych opłat:

* Aby usunąć IoT Edge maszynę wirtualną i skojarzone z nią zasoby, Usuń grupę zasobów **contoso-Edge-RG** w Azure Portal.
* Aby usunąć aplikację IoT Central, przejdź do strony **aplikacji** w sekcji **Administracja** aplikacji i wybierz pozycję **Usuń**.

Jako deweloper rozwiązania lub operator, teraz wiesz już, jak pracować z urządzeniami IoT Edge i zarządzać nimi w IoT Central, sugerowanym następnym krokiem jest:

> [!div class="nextstepaction"]
> [Używanie grup urządzeń do analizowania danych telemetrycznych urządzenia](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Następne kroki

Jako deweloper urządzenia teraz wiesz, jak pracować z urządzeniami IoT Edge i zarządzać nimi w IoT Central, sugerowanym następnym krokiem jest odczytanie:

> [!div class="nextstepaction"]
> [Opracowywanie modułów IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)