---
title: Samouczek dotyczący wykrywania anomalii na urządzeniach brzegowych w rozwiązaniu — Azure | Microsoft Docs
description: W tym samouczku pokazano, jak monitorować urządzenia usługi IoT Edge przy użyciu akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 43ba14845765230b9a54c2b34dbc7ccd53af950b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970001"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Samouczek: Wykrywanie anomalii na urządzeniach brzegowych za pomocą akceleratora rozwiązania do monitorowania zdalnego

Podczas pracy z tym samouczkiem utworzysz rozwiązanie do monitorowania zdalnego w celu reagowania na anomalie wykryte przez urządzenie usługi IoT Edge. Urządzenia usługi IoT Edge umożliwiają przetwarzanie danych telemetrycznych na urządzeniach brzegowych, co pozwala na zmniejszenie ilości danych telemetrycznych wysyłanych do rozwiązania oraz szybsze reagowanie na zdarzenia występujące na urządzeniach. Aby dowiedzieć się więcej na temat zalet przetwarzania danych na urządzeniach brzegowych, zobacz [Co to jest usługa Azure IoT Edge](../iot-edge/about-iot-edge.md).

Aby przedstawić sposób przetwarzania danych na urządzeniach brzegowych za pomocą rozwiązania do zdalnego monitorowania, w tym samouczku wykorzystano symulowane urządzenie pompy olejowej. Ta pompa olejowa jest zarządzana przez organizację o nazwie Contoso i jest połączona z akceleratorem rozwiązania do monitorowania zdalnego. Czujniki pompy olejowej mierzą temperaturę i ciśnienie. Operatorzy w firmie Contoso wiedzą, że nadmierny wzrost temperatury może spowodować wolniejszą pracę pompy. Operatorzy w firmie Contoso nie muszą monitorować temperatury urządzenia wtedy, gdy mieści się w normie.

Firma Contoso chce wdrożyć moduł inteligentnego urządzenia brzegowego, wykrywający anomalie temperatury pompy. Kolejny moduł brzegowy będzie wysyłać alerty do rozwiązania do monitorowania zdalnego. Po otrzymaniu alertu operator w firmie Contoso może wysłać technika w celu przeprowadzenia konserwacji. Firma Contoso może również skonfigurować działanie automatyczne, na przykład wysłanie wiadomości e-mail po otrzymaniu alertu przez rozwiązanie.

Na poniższym diagramie przedstawiono najważniejsze składniki w tym scenariuszu samouczka:

![Diagram przedstawia gniazdo pompy olejowej połączone z modułem usługi Stream Analytics (T Edge) na urządzeniu z T Edge dla telemetrii i poleceń. Filtrowane dane telemetryczne przechodzą do urządzenia I o T Edge w akceleratorze rozwiązania do zdalnego monitorowania w chmurze. Chmura zawiera również wdrożenie i pakiet. Wdrożenie wdraża środowisko uruchomieniowe we/wy na urządzeniu.](media/iot-accelerators-remote-monitoring-edge/overview.png)

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Dodawanie urządzenia usługi IoT Edge do rozwiązania
> * Tworzenie manifestu usługi Edge
> * Importowanie manifestu jako pakietu definiującego moduły uruchamiane na urządzeniu
> * Wdrażanie pakietu na urządzeniu usługi IoT Edge
> * Wyświetlanie alertów z urządzenia

Na urządzeniu usługi IoT Edge:

* Środowisko uruchomieniowe odbiera pakiet i instaluje moduły.
* Moduł usługi Stream Analytics wykrywa anomalie temperatury w pompie i wysyła polecenia w celu rozwiązania tego problemu.
* Moduł usługi Stream Analytics przekazuje odfiltrowane dane do akceleratora rozwiązań.

W tym samouczku jako urządzenie usługi IoT Edge jest używana maszyna wirtualna z systemem Linux. Instalujesz również moduł usługi Edge symulujący urządzenie pompy olejowej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Dodawanie urządzenia usługi IoT Edge

Dodawanie urządzenia do akceleratora rozwiązania do monitorowania zdalnego przebiega w dwóch etapach. W tej sekcji przedstawiono następujące czynności:

* Dodawanie urządzenia usługi IoT Edge na stronie **Device Explorer** w internetowym interfejsie użytkownika zdalnego monitorowania.
* Instalowanie środowiska uruchomieniowego usługi IoT Edge na maszynie wirtualnej z systemem Linux.

### <a name="add-an-iot-edge-device-to-your-solution"></a>Dodawanie urządzenia usługi IoT Edge do rozwiązania

Aby dodać urządzenie usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego, przejdź do strony **Device Explorer** w internetowym interfejsie użytkownika i kliknij pozycję **+ Nowe urządzenie**.

Na panelu **Nowe urządzenie** wybierz pozycję **Urządzenie usługi IoT Edge** i wpisz **oil-pump** jako identyfikator urządzenia. Dla innych właściwości możesz pozostawić ustawienia domyślne. Następnie kliknij pozycję **Zastosuj**:

[![Dodaj urządzenie IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Zanotuj parametry połączenia urządzenia, które będą potrzebne w kolejnej sekcji tego samouczka.

Urządzenie zarejestrowane w centrum IoT Hub w akceleratorze rozwiązania do monitorowania zdalnego pojawi się na liście na stronie **Device Explorer** w internetowym interfejsie użytkownika:

[![Nowe urządzenie IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Aby ułatwić zarządzanie urządzeniami usługi IoT Edge w ramach rozwiązania, utwórz grupę urządzeń i dodaj do niej urządzenie usługi IoT Edge:

1. Wybierz urządzenie **oil-pump** z listy na stronie **Device Explorer**, a następnie kliknij pozycję **Zadania**.

1. Utwórz zadanie obejmujące dodanie tagu **IsEdge** do urządzenia, używając następujących ustawień:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Zadanie     | Tagi  |
    | Nazwa zadania | AddEdgeTag |
    | Klucz     | IsOilPump |
    | Wartość   | T     |
    | Typ    | Tekst  |

    [![Dodaj tag](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Kliknij przycisk **Zastosuj**, a następnie **Zamknij**.

1. Na stronie **Device Explorer** kliknij pozycję **Zarządzaj grupami urządzeń**.

1. Kliknij przycisk **Utwórz nową grupę urządzeń**. Utwórz nową grupę urządzeń z następującymi ustawieniami:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa    | OilPumps |
    | Pole   | Tags.IsOilPump |
    | Operator | = Równa się |
    | Wartość    | T |
    | Typ     | Tekst |

    [![Utwórz grupę urządzeń](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Kliknij przycisk **Zapisz**.

Urządzenie usługi IoT Edge jest teraz w grupie **OilPumps**.

### <a name="install-the-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi Edge

Urządzenia usługi Edge wymagają zainstalowanego środowiska uruchomieniowego usługi Edge. Podczas pracy z tym samouczkiem zainstalujesz środowisko uruchomieniowe usługi Edge na maszynie wirtualnej platformy Azure z systemem Linux, aby przetestować ten scenariusz. W poniższych krokach użyjesz usługi Azure Cloud Shell do zainstalowania i skonfigurowania maszyny wirtualnej:

1. Aby utworzyć maszynę wirtualną z systemem Linux na platformie Azure, uruchom następujące polecenia. Możesz użyć lokalizacji w pobliżu Ciebie:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Aby skonfigurować parametry połączenia urządzenia w środowisku uruchomieniowym usługi Edge, uruchom następujące polecenie, podając parametry połączenia urządzenia zanotowane wcześniej:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Pamiętaj, aby ująć parametry połączenia w cudzysłów.

Masz teraz zainstalowane i skonfigurowane środowisko uruchomieniowe usługi IoT Edge na urządzeniu z systemem Linux. W dalszej części tego samouczka użyjesz rozwiązania do monitorowania zdalnego, aby wdrożyć moduły usługi IoT Edge na tym urządzeniu.

## <a name="create-an-edge-manifest"></a>Tworzenie manifestu usługi Edge

Aby zasymulować urządzenie pompy olejowej, należy dodać następujące moduły na urządzeniu usługi Edge:

* Moduł symulacji temperatury.
* Wykrywanie anomalii w usłudze Stream Analytics.

Poniższe kroki pokazują, jak utworzyć manifest wdrożenia usługi Edge, która zawiera te moduły. W dalszej części tego samouczka zaimportujesz ten manifest jako pakiet w akceleratorze rozwiązania do monitorowania zdalnego.

### <a name="create-the-azure-stream-analytics-job"></a>Tworzenie zadania usługi Azure Stream Analytics

Przed spakowaniem zadania usługi Stream Analytics jako modułu usługi Edge należy zdefiniować je w portalu.

1. W witrynie Azure Portal utwórz konto usługi Azure Storage w grupie zasobów **IoTEdgeDevices**, używając opcji domyślnych. Zanotuj wybraną dla niego nazwę.

1. W witrynie Azure Portal utwórz **Zadanie usługi Stream Analytics** w grupie zasobów **IoTEdgeDevices**. Użyj następujących wartości konfiguracji:

    | Opcja | Wartość |
    | ------ | ----- |
    | Nazwa zadania | EdgeDeviceJob |
    | Subskrypcja | Twoja subskrypcja platformy Azure |
    | Grupa zasobów | IoTEdgeDevices |
    | Lokalizacja | Wschodnie stany USA |
    | Środowisko hostingu | Edge |
    | Jednostki przesyłania strumieniowego | 1 |

1. Otwórz zadanie usługi Stream Analytics **EdgeDeviceJob** w portalu, kliknij pozycję Dane wejściowe, a następnie dodaj strumień danych wejściowych **Edge Hub** o nazwie **telemetry**.

1. W obszarze zadania usługi Stream Analytics **EdgeDeviceJob** w portalu kliknij pozycję **Dane wyjściowe**, a następnie dodaj dane wyjściowe **Edge Hub** o nazwie **output**.

1. W obszarze zadania usługi Stream Analytics **EdgeDeviceJob** w portalu kliknij pozycję **Dane wyjściowe**, a następnie dodaj kolejne dane wyjściowe **Edge Hub** o nazwie **alert**.

1. W obszarze zadania usługi Stream Analytics **EdgeDeviceJob** w portalu kliknij pozycję **Zapytanie** i dodaj następującą instrukcję **select**:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. W obszarze zadania usługi Stream Analytics **EdgeDeviceJob** w portalu kliknij pozycję **Ustawienia konta magazynu**. Dodaj konto magazynu utworzone w grupie zasobów **IoTEdgeDevices** na początku tej sekcji. Utwórz nowy kontener o nazwie **edgeconfig**.

Poniższy zrzut ekranu przedstawia zapisane zadanie usługi Stream Analytics:

[![Zadanie Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Masz teraz zdefiniowanie zadanie usługi Stream Analytics do uruchomienia na urządzeniu brzegowym. Zadanie oblicza średnią temperaturę w 5-sekundowym przedziale. Wysyła także alert, jeśli średnia temperatura w 3-sekundowym przedziale przekroczy 400.

### <a name="create-the-iot-edge-deployment"></a>Tworzenie wdrożenia usługi IoT Edge

Następne należy utworzyć manifest wdrożenia usługi IoT Edge, definiujący moduły do uruchomienia na urządzeniu usługi Edge. W następnej sekcji zaimportujesz ten manifest jako pakiet w rozwiązaniu do monitorowania zdalnego.

1. W witrynie Azure Portal przejdź do centrum IoT Hub w rozwiązaniu do monitorowania zdalnego. Możesz znaleźć to centrum IoT Hub w grupie zasobów o takiej samej nazwie, jak rozwiązanie do monitorowania zdalnego.

1. W obszarze centrum IoT Hub kliknij pozycję **IoT Edge** w sekcji **Automatyczne zarządzanie urządzeniami**. Kliknij pozycję **Dodaj wdrożenie usługi IoT Edge**.

1. Na stronie **Tworzenie wdrożenia > Nazwa i etykieta** wpisz nazwę **oil-pump-device**. Kliknij przycisk **Dalej**.

1. Na stronie **Tworzenie wdrożenia > Dodawanie modułów** kliknij pozycję **+ Dodaj**. Wybierz pozycję **Moduł usługi IoT Edge**.

1. Na panelu **Moduły niestandardowe usługi IoT Edge** wpisz nazwę **temperatureSensor** oraz identyfikator URI obrazu **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64**. Kliknij przycisk **Zapisz**.

1. Na stronie **Tworzenie wdrożenia > Dodawanie modułów** kliknij pozycję **+ Dodaj**, aby dodać drugi moduł. Wybierz pozycję **Moduł usługi Azure Stream Analytics**.

1. Na panelu **Wdrożenie usługi Edge** wybierz swoją subskrypcję oraz zadanie **EdgeDeviceJob** utworzone w poprzedniej sekcji. Kliknij przycisk **Zapisz**.

1. Na stronie **Tworzenie wdrożenia > Dodawanie modułów** kliknij pozycję **Dalej**.

1. Na stronie **Tworzenie wdrożenia > Określanie tras** dodaj następujący kod:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Ten kod kieruje dane wyjściowe z modułu usługi Stream Analytics do poprawnych lokalizacji.

    Kliknij przycisk **Dalej**.

1. Na stronie **Tworzenie wdrożenia > Określanie metryk** kliknij pozycję **Dalej**.

1. Na stronie **Tworzenie wdrożenia > Urządzenia docelowe** wpisz 10 jako wartość priorytetu. Kliknij przycisk **Dalej**.

1. Na stronie **Tworzenie wdrożenia > Przegląd wdrożenia** kliknij pozycję **Prześlij**:

    [![Przegląd wdrożenia](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Na stronie głównej usługi **IoT Edge** kliknij pozycję **Wdrożenia usługi IoT Edge**. Na liście wdrożeń zobaczysz pozycję **oil-pump-device**.

1. Kliknij wdrożenie **oil-pump-device**, a następnie kliknij polecenie **Pobierz manifest usługi IoT Edge**. Zapisz plik jako **oil-pump-device.json** w odpowiedniej lokalizacji na komputerze lokalnym. Ten plik będzie potrzebny w kolejnej sekcji tego samouczka.

Utworzono manifest IoT Edge, który ma zostać zaimportowany do rozwiązania do zdalnego monitorowania jako pakiet. Na ogół moduły usługi IoT Edge i plik manifestu są tworzone przez dewelopera.

## <a name="import-a-package"></a>Importowanie pakietu

W tej sekcji zaimportujesz manifest usługi Edge jako pakiet w rozwiązaniu do monitorowania zdalnego.

1. W internetowym interfejsie użytkownika zdalnego monitorowania przejdź do strony **Pakiety** i kliknij pozycję **+ Nowy pakiet**:

    [![Nowy pakiet](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Na panelu **Nowy pakiet** wybierz typ pakietu **Manifest usługi Edge**, kliknij przycisk **Przeglądaj**, aby znaleźć plik **oil-pump-device.json** na komputerze lokalnym, a następnie kliknij przycisk **Przekaż**:

    [![Przekazywanie pakietu](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    Na liście pakietów znajduje się teraz pakiet **oil-pump-device.json**.

W następnej sekcji utworzysz wdrożenie, aby zastosować ten pakiet na urządzeniu usługi Edge.

## <a name="deploy-a-package"></a>Wdrażanie pakietu

Teraz możesz przystąpić do wdrażania pakietu na urządzeniu.

1. W internetowym interfejsie użytkownika zdalnego monitorowania przejdź do strony **Wdrożenia** i kliknij pozycję **+ Nowe wdrożenie**:

    [![Nowe wdrożenie](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Na panelu **Nowe wdrożenie** utwórz wdrożenie, używając następujących ustawień:

    | Opcja | Wartość |
    | ------ | ----- |
    | Nazwa   | OilPumpDevices |
    | Typ pakietu | Manifest usługi Edge |
    | Pakiet | oil-pump-device.json |
    | Grupa urządzeń | OilPumps |
    | Priorytet | 10 |

    [![Utwórz wdrożenie](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Kliknij pozycję **Zastosuj**.

Musisz poczekać kilka minut na wdrożenie pakietu na urządzeniu i rozpoczęcie przesyłania danych telemetrycznych z urządzenia.

[![Wdrożenie aktywne](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

Na stronie **Wdrożenia** są widoczne następujące metryki:

* **Docelowe** — liczba urządzeń w grupie urządzeń.
* **Zastosowane** — liczba urządzeń, na których zastosowano zawartość wdrożenia.
* **Powodzenie** — liczba urządzeń usługi Edge w ramach wdrożenia zgłaszających powodzenie za pośrednictwem środowiska uruchomieniowego klienta usługi IoT Edge.
* **Niepowodzenie** — liczba urządzeń usługi Edge w ramach wdrożenia zgłaszających niepowodzenie za pośrednictwem środowiska uruchomieniowego klienta usługi IoT Edge.

## <a name="monitor-the-device"></a>Monitorowanie urządzenia

Możesz wyświetlać przesyłane z urządzenia pompy dane telemetryczne dotyczące temperatury w internetowym interfejsie użytkownika zdalnego monitorowania:

1. Przejdź do strony **Device Explorer** i wybierz urządzenie pompy olejowej.
1. W sekcji **Telemetria** na panelu **Szczegóły urządzenia** kliknij pozycję **Temperatura**:

    [![Wyświetlanie danych telemetrycznych](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Zobaczysz, jak temperatura wzrasta, aż do wartości progowej. Moduł usługi Stream Analytics dla usługi Edge wykryje osiągnięcie wartości progowej temperatury i wyśle do urządzenia polecenie natychmiastowego obniżenia temperatury. Wszystkie te czynności przetwarzania są wykonywane na urządzeniu, bez komunikowania się z chmurą.

Jeśli chcesz, aby operatorzy byli powiadamiani o osiągnięciu wartości progowej, możesz utworzyć regułę w internetowym interfejsie użytkownika zdalnego monitorowania:

1. Przejdź do strony **Reguły** i kliknij pozycję **+Nowa reguła**.
1. Utwórz nową regułę z następującymi ustawieniami:

    | Opcja | Wartość |
    | ------ | ----- |
    | Nazwa reguły | Temperatura pompy olejowej |
    | Opis | Temperatura pompy olejowej przekracza 300 |
    | Grupa urządzeń | OilPumps |
    | Obliczenia | Błyskawiczne |
    | Pole | temperature |
    | Operator | > |
    | Wartość | 300 |
    | Poziom ważności | Info |

    [![Utwórz regułę](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Kliknij pozycję **Zastosuj**.

1. Przejdź do strony **Pulpit nawigacyjny**. Alert zostanie wyświetlony na panelu **Alerty**, gdy temperatura urządzenia **oil-pump** przekroczy 300.

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, w jaki sposób dodać i skonfigurować urządzenie usługi IoT Edge połączone z akceleratorem rozwiązania do monitorowania zdalnego. Aby dowiedzieć się więcej na temat pracy z pakietami usługi IoT Edge w ramach rozwiązania do monitorowania zdalnego, zobacz następujący przewodnik:

> [!div class="nextstepaction"]
> [Importowanie pakietu usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-import-edge-package.md)

Aby dowiedzieć się więcej na temat instalowania środowiska uruchomieniowego usługi IoT Edge, zobacz [Install the Azure IoT Edge runtime on Linux x64 (Instalowanie środowiska uruchomieniowego usługi Azure IoT Edge w systemie Linux w wersji x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Aby dowiedzieć się więcej na temat usługi Azure Stream Analytics na urządzeniach IoT Edge, zobacz [Wdrażanie usługi Azure Stream Analytics jako modułu usługi IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
