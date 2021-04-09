---
title: Monitorowanie modułu bliźniaczych reprezentacji — Azure IoT Edge
description: Jak interpretować bliźniaczych reprezentacji urządzeń i bliźniaczych reprezentacji modułu w celu określenia łączności i kondycji.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b7013979199eefa873a651d99e87dc8b2c47856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201596"
---
# <a name="monitor-module-twins"></a>Monitorowanie bliźniaczych reprezentacji modułu

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Bliźniaczych reprezentacji modułów na platformie Azure IoT Hub umożliwia monitorowanie łączności i kondycji wdrożeń IoT Edge. Moduł bliźniaczych reprezentacji przechowuje przydatne informacje w usłudze IoT Hub dotyczące wydajności uruchomionych modułów. [IoT Edge Agent](iot-edge-runtime.md#iot-edge-agent) i moduły środowiska uruchomieniowego [Centrum IoT Edge](iot-edge-runtime.md#iot-edge-hub) każdy z nich utrzymuje bliźniaczych reprezentacji modułów `$edgeAgent` i `$edgeHub` , odpowiednio:

* `$edgeAgent` zawiera dane o kondycji i łączności dotyczące modułów IoT Edge Agent i IoT Edge Hub oraz modułów niestandardowych. Agent IoT Edge jest odpowiedzialny za wdrażanie modułów, monitorowanie ich i raportowanie stanu połączenia do usługi Azure IoT Hub.
* `$edgeHub` zawiera dane dotyczące komunikacji między centrum IoT Edge działającym na urządzeniu a usługą Azure IoT Hub. Obejmuje to przetwarzanie komunikatów przychodzących z urządzeń podrzędnych. Centrum IoT Edge jest odpowiedzialne za przetwarzanie komunikacji między usługą Azure IoT Hub i urządzeniami IoT Edge i modułami.

Dane są zorganizowane w metadane, znaczniki, wraz z żądanymi i zgłoszonymi zestawami właściwości w strukturach JSON modułu bliźniaczych reprezentacji. Żądane właściwości określone w deployment.jsw pliku są kopiowane do modułu bliźniaczych reprezentacji. Agent IoT Edge i centrum IoT Edge każda aktualizuje raportowane właściwości dla swoich modułów.

Podobnie wymagane właściwości określone dla modułów niestandardowych w deployment.jsw pliku są kopiowane do postaci sznurka modułowego, ale rozwiązanie jest odpowiedzialne za dostarczanie wartości właściwości, które zostały zgłoszone.

W tym artykule opisano sposób przeglądania modułu bliźniaczych reprezentacji w Azure Portal, interfejsu wiersza polecenia platformy Azure i Visual Studio Code. Aby uzyskać informacje na temat monitorowania sposobu, w jaki urządzenia odbierają wdrożenia, zobacz [monitorowanie wdrożeń IoT Edge](how-to-monitor-iot-edge-deployments.md). Aby zapoznać się z omówieniem koncepcji modułu bliźniaczych reprezentacji, zobacz temat Omówienie [i Używanie modułu bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> Raportowane właściwości modułu uruchomieniowego mogą być nieodświeżone, jeśli urządzenie IoT Edge zostanie odłączone od centrum IoT Hub. Można [wysłać polecenie ping](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` do modułu, aby określić, czy połączenie zostało utracone.

## <a name="monitor-runtime-module-twins"></a>Monitorowanie modułu uruchomieniowego bliźniaczych reprezentacji

Aby rozwiązać problemy z łącznością wdrożenia, przejrzyj bliźniaczych reprezentacji agenta i IoT Edge module środowiska uruchomieniowego Centrum IoT Edge a następnie przejdź do szczegółów innych modułów.

### <a name="monitor-iot-edge-agent-module-twin"></a>Monitorowanie sznurka modułu IoT Edge Agent

Poniższy kod JSON pokazuje `$edgeAgent` sznurki modułu w Visual Studio Code z większością sekcji JSON zwinięte.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

KOD JSON można opisać w poniższych sekcjach, zaczynając od początku:

* Metadata — zawiera dane łączności. W interesujący sposób stan połączenia agenta IoT Edge jest zawsze w stanie rozłączenia: `"connectionState": "Disconnected"` . Powód stanu połączenia odnosi się do komunikatów z urządzenia do chmury (D2C), a agent IoT Edge nie wysyła komunikatów D2C.
* Właściwości — zawiera `desired` `reported` podsekcje i.
* Properties. żądana — (pokazana zwinięte) oczekiwano wartości właściwości ustawionych przez operatora w deployment.jspliku.
* Właściwości. zgłoszono najnowsze wartości właściwości zgłoszone przez agenta IoT Edge.

Obie `properties.desired` sekcje i `properties.reported` mają podobną strukturę i zawierają dodatkowe metadane dotyczące schematu, wersji i informacji o środowisku uruchomieniowym. Uwzględniona jest także `modules` sekcja dla wszystkich modułów niestandardowych (takich jak `SimulatedTemperatureSensor` ) i `systemModules` sekcja dla `$edgeAgent` i `$edgeHub` modułów środowiska uruchomieniowego.

Porównując wartości właściwości raportowanych z odpowiednimi wartościami, można określić rozbieżności i zidentyfikować odłączenia, które mogą pomóc w rozwiązywaniu problemów. W tym porównań Sprawdź `$lastUpdated` wartość raportowaną w `metadata` sekcji dla właściwości, którą badasz.

Następujące właściwości są ważne do sprawdzenia pod kątem rozwiązywania problemów:

* **ExitCode** — każda wartość różna od zera wskazuje, że moduł został zatrzymany z błędem. Jednak kody błędów 137 lub 143 są używane, jeśli moduł został celowo ustawiony na stan zatrzymany.

* **lastStartTimeUtc** — pokazuje **datę i godzinę** ostatniego uruchomienia kontenera. Ta wartość to 0,001-01-01T00:00:00Z, jeśli kontener nie został uruchomiony.

* **lastExitTimeUtc** — pokazuje **datę i godzinę** ostatniego zakończenia kontenera. Ta wartość to 0,001-01-01T00:00:00Z, jeśli kontener jest uruchomiony i nigdy nie został zatrzymany.

* **runtimeStatus** — może być jedną z następujących wartości:

    | Wartość | Opis |
    | --- | --- |
    | unknown | Stan domyślny do momentu utworzenia wdrożenia. |
    | wycofywania | Zaplanowano uruchomienie modułu, ale nie jest on obecnie uruchomiony. Ta wartość jest przydatna dla modułu, w którym zmiany stanu są zmieniane po ponownym uruchomieniu. Gdy niepowodzenie modułu oczekuje na ponowne uruchomienie w okresie chłodzenia, moduł będzie w stanie wycofywania. |
    | uruchomion | Wskazuje, że moduł jest obecnie uruchomiony. |
    | złej kondycji | Wskazuje, że sprawdzanie kondycji nie powiodło się lub upłynął limit czasu. |
    | przerwać | Wskazuje, że moduł został zakończony pomyślnie (z kodem zakończenia zero). |
    | niepowodzenie | Wskazuje, że moduł zakończył pracę z kodem zakończenia błędu (niezerowym). Moduł może przejść z powrotem do wycofywania z tego stanu w zależności od zasad ponownego uruchomienia. Ten stan może wskazywać, że moduł napotkał nieodwracalny błąd. Błąd występuje, gdy Microsoft Monitoring Agent (MMA) nie może już resuscitate modułu, wymagając nowego wdrożenia. |

Aby uzyskać szczegółowe informacje, zobacz [EdgeAgent raportowane właściwości](module-edgeagent-edgehub.md#edgeagent-reported-properties) .

### <a name="monitor-iot-edge-hub-module-twin"></a>Monitorowanie sznurka modułu IoT Edge Hub

Poniższy kod JSON pokazuje `$edgeHub` sznurki modułu w Visual Studio Code z większością sekcji JSON zwinięte.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

KOD JSON można opisać w poniższych sekcjach, zaczynając od początku:

* Metadata — zawiera dane łączności.

* Właściwości — zawiera `desired` `reported` podsekcje i.
* Properties. żądana — (pokazana zwinięte) oczekiwano wartości właściwości ustawionych przez operatora w deployment.jspliku.
* Properties. zgłosiło najnowsze wartości właściwości zgłoszone przez Centrum IoT Edge.

Jeśli występują problemy z urządzeniami podrzędnymi, badanie tych danych byłoby dobrym miejscem do rozpoczęcia.

## <a name="monitor-custom-module-twins"></a>Monitorowanie niestandardowego modułu bliźniaczych reprezentacji

Informacje o łączności modułów niestandardowych są utrzymywane w wieloosiowym module agenta IoT Edge. Sznurek modułu dla modułu niestandardowego jest używany głównie do obsługi danych dla rozwiązania. Żądane właściwości zdefiniowane w deployment.jsw pliku są odzwierciedlane w postaci sznurka modułowego, a moduł może aktualizować raportowane wartości właściwości zgodnie z potrzebami.

Możesz użyć preferowanego języka programowania z zestawami [SDK urządzeń IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) , aby zaktualizować raportowane wartości właściwości w postaci sznurka modułu na podstawie kodu aplikacji modułu. Poniższa procedura korzysta z zestawu Azure SDK dla platformy .NET w tym celu przy użyciu kodu z modułu [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) :

1. Utwórz wystąpienie [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) za pomocą metody [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) .

1. Pobierz kolekcję właściwości sznurka modułu za pomocą metody [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) .

1. Utwórz odbiornik (przekazując wywołanie zwrotne), aby przechwycić zmiany do żądanych właściwości przy użyciu metody [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) .

1. W metodzie wywołania zwrotnego zaktualizuj raportowane właściwości w postaci sznurka modułu za pomocą metody [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) , przekazując element [bliźniaczycollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) wartości właściwości, które chcesz ustawić.

## <a name="access-the-module-twins"></a>Dostęp do modułu bliźniaczych reprezentacji

Możesz przejrzeć plik JSON dla modułu bliźniaczych reprezentacji w IoT Hub platformy Azure, w Visual Studio Code i przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="monitor-in-azure-iot-hub"></a>Monitorowanie w usłudze Azure IoT Hub

Aby wyświetlić kod JSON dla sznurka modułu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. W menu po lewej stronie wybierz pozycję **IoT Edge** .
1. Na karcie **IoT Edge urządzenia** wybierz **Identyfikator urządzenia** dla urządzenia z modułami, które chcesz monitorować.
1. Wybierz nazwę modułu z karty **moduły** , a następnie wybierz opcję **sznury tożsamości modułu** z górnego paska menu.

  ![Wybierz sznurek modułu do wyświetlenia w Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Jeśli zobaczysz komunikat "tożsamość modułu nie istnieje dla tego modułu", ten błąd wskazuje, że rozwiązanie zaplecza nie jest już dostępne, które pierwotnie utworzyło tożsamość.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitorowanie modułu bliźniaczych reprezentacji w Visual Studio Code

Aby przejrzeć i edytować sznurek modułu:

1. Jeśli jeszcze nie zainstalowano, zainstaluj [rozszerzenie narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla Visual Studio Code.
1. W **Eksploratorze** rozwiń węzeł **Azure IoT Hub**, a następnie rozwiń urządzenie przy użyciu modułu, który chcesz monitorować.
1. Kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Edytuj sznurek modułu**. Tymczasowy plik sznurka modułu jest pobierany na komputer i wyświetlany w Visual Studio Code.

  ![Pobierz sznurek modułu do edycji w Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Jeśli wprowadzisz zmiany, wybierz pozycję **Aktualizuj sznurek modułu** powyżej kodu w edytorze, aby zapisać zmiany w centrum IoT.

  ![Aktualizowanie sznurka modułu w Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitorowanie modułu bliźniaczych reprezentacji w interfejsie wiersza polecenia platformy Azure

Aby sprawdzić, czy IoT Edge jest uruchomiona, użyj polecenia [AZ IoT Hub Invoke-module-Method](how-to-edgeagent-direct-method.md#ping) , aby wysłać polecenie ping do agenta IoT Edge.

Polecenie [AZ IoT Hub module-sznuring](/cli/azure/ext/azure-iot/iot/hub/module-twin) udostępnia następujące polecenia:

* **AZ IoT Hub module-sznury show** -show a module — definicja
* **AZ IoT Hub module-splot Update** -Update a module — definicja
* **AZ IoT Hub module-sznury zastępuje** definicję z docelową notacją JSON.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [komunikować się z usługą EdgeAgent przy użyciu wbudowanych metod bezpośrednich](how-to-edgeagent-direct-method.md).
