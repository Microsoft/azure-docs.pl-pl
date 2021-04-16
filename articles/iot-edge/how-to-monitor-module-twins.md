---
title: Monitorowanie bliźniaczych reprezentacji modułu — Azure IoT Edge
description: Interpretowanie bliźniaczych reprezentacji urządzeń i bliźniaczych reprezentacji modułów w celu określenia łączności i kondycji.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5a31e15c88cef588c93f44c8fe5303d930b5b2c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479376"
---
# <a name="monitor-module-twins"></a>Monitorowanie bliźniaczych reprezentacji modułu

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Bliźniacze Azure IoT Hub umożliwiają monitorowanie łączności i kondycji IoT Edge wdrożeń. Bliźniacze reprezentacji modułów przechowują w centrum IoT przydatne informacje o wydajności uruchomionych modułów. Agent [IoT Edge i](iot-edge-runtime.md#iot-edge-agent) moduły [IoT Edge uruchomieniowe](iot-edge-runtime.md#iot-edge-hub) centrum danych utrzymują bliźniacze bliźniacze reprezentacji modułów i , `$edgeAgent` `$edgeHub` odpowiednio:

* `$edgeAgent` Zawiera dane dotyczące kondycji i łączności dotyczące IoT Edge i IoT Edge środowiska uruchomieniowego centrum danych oraz modułów niestandardowych. Agent IoT Edge odpowiada za wdrażanie modułów, monitorowanie ich i raportowanie stanu połączenia z centrum Azure IoT.
* `$edgeHub` Zawiera dane dotyczące komunikacji między centrum IoT Edge uruchomionym na urządzeniu a centrum Azure IoT Hub. Obejmuje to przetwarzanie komunikatów przychodzących z urządzeń dalszych. IoT Edge jest odpowiedzialne za przetwarzanie komunikacji między Azure IoT Hub a IoT Edge modułami.

Dane są zorganizowane w metadane, tagi oraz zestawy żądanych i zgłaszanych właściwości w strukturach JSON bliźniaczych reprezentacji modułu. Żądane właściwości określone w pliku deployment.jssą kopiowane do bliźniaczych reprezentacji modułu. Agent IoT Edge i centrum IoT Edge aktualizują zgłaszane właściwości modułów.

Podobnie żądane właściwości określone dla modułów niestandardowych w pliku deployment.jssą kopiowane do bliźniaczej reprezentacji modułu, ale rozwiązanie jest odpowiedzialne za podanie wartości zgłaszanych właściwości.

W tym artykule opisano sposób przeglądania bliźniaczych reprezentacji modułu w witrynie Azure Portal, interfejsie wiersza polecenia platformy Azure i w Visual Studio Code. Aby uzyskać informacje na temat monitorowania sposobu odbierania wdrożeń przez urządzenia, zobacz [Monitorowanie IoT Edge wdrożeń.](how-to-monitor-iot-edge-deployments.md) Aby uzyskać omówienie koncepcji bliźniaczych reprezentacji modułów, zobacz [Understand and use module twins in IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md)(Omówienie bliźniaczych reprezentacji modułów i korzystanie z nich w IoT Hub).

> [!TIP]
> Zgłaszane właściwości modułu środowiska uruchomieniowego mogą być nieaktualne, jeśli urządzenie IoT Edge odłączone od centrum IoT. Możesz [wysłać polecenie ping](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` do modułu, aby ustalić, czy połączenie zostało utracone.

## <a name="monitor-runtime-module-twins"></a>Monitorowanie bliźniaczych reprezentacji modułu środowiska uruchomieniowego

Aby rozwiązać problemy z łącznością wdrażania, przejrzyj IoT Edge i IoT Edge modułu środowiska uruchomieniowego centrum danych, a następnie przejdź do szczegółów innych modułów.

### <a name="monitor-iot-edge-agent-module-twin"></a>Monitorowanie IoT Edge bliźniaczej reprezentacji modułu agenta

Poniższy kod JSON przedstawia bliźniacze reprezentacji modułu w Visual Studio Code z zwiniętą `$edgeAgent` większość sekcji JSON.

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

Kod JSON można opisać w poniższych sekcjach, zaczynając od góry:

* Metadane — zawiera dane łączności. Co ciekawe, stan połączenia agenta IoT Edge jest zawsze w stanie rozłączenia: `"connectionState": "Disconnected"` . Przyczyna stanu połączenia dotyczy komunikatów z urządzenia do chmury (D2C), a agent IoT Edge nie wysyła komunikatów D2C.
* Właściwości — zawiera `desired` `reported` podsekcję i .
* Properties.desired — (pokazane zwinięte) Oczekiwane wartości właściwości ustawione przez operator w deployment.jspliku.
* Properties.reported — najnowsze wartości właściwości zgłaszane przez IoT Edge agenta.

Obie sekcje i mają podobną strukturę i zawierają dodatkowe metadane dotyczące informacji o `properties.desired` `properties.reported` schemacie, wersji i środowisku uruchomieniowym. Uwzględniono również sekcję dla wszystkich modułów niestandardowych (takich jak ), a także sekcję `modules` modułów `SimulatedTemperatureSensor` środowiska `systemModules` `$edgeAgent` `$edgeHub` uruchomieniowego i .

Porównując wartości zgłaszanej właściwości z żądanymi wartościami, można określić rozbieżności i zidentyfikować rozłączenia, które mogą pomóc w rozwiązywaniu problemów. Wykonując te porównania, sprawdź zgłoszoną wartość w sekcji pod względem `$lastUpdated` `metadata` badanej właściwości.

Następujące właściwości są ważne do zbadania w celu rozwiązywania problemów:

* **exitcode** — dowolna wartość inna niż zero wskazuje, że moduł został zatrzymany z błędem. Kody błędów 137 lub 143 są jednak używane, jeśli moduł został celowo ustawiony na stan zatrzymania.

* **lastStartTimeUtc** — pokazuje **datę i** godzinę ostatniego rozpoczęcia kontenera. Ta wartość to 0001-01-01T00:00:00Z, jeśli kontener nie został uruchomiony.

* **lastExitTimeUtc** — pokazuje **datę/godzinę** ostatniego zakończenia kontenera. Ta wartość to 0001-01-01T00:00:00Z, jeśli kontener jest uruchomiony i nigdy nie został zatrzymany.

* **runtimeStatus** — może być jedną z następujących wartości:

    | Wartość | Opis |
    | --- | --- |
    | unknown | Stan domyślny do momentu utworzenia wdrożenia. |
    | backoff | Zaplanowano uruchomienie modułu, ale nie jest on obecnie uruchomiony. Ta wartość jest przydatna w przypadku modułu, który przechodzi zmiany stanu podczas ponownego uruchamiania. Gdy moduł, który się nie powróci, oczekuje na ponowne uruchomienie w okresie ochłodzenia, moduł będzie w stanie odczekania. |
    | Uruchomiona | Wskazuje, że moduł jest aktualnie uruchomiony. |
    | Niezdrowe | Wskazuje, że sprawdzanie sondy kondycji nie powiodło się lub ułożyło się przeo jej czas. |
    | Zatrzymany | Wskazuje, że moduł zakończył się pomyślnie (z zerowym kodem zakończenia). |
    | niepowodzenie | Wskazuje, że moduł został exited z kodem zakończenia błędu (niezerowym). Moduł może przejść z powrotem do odsyłania z tego stanu w zależności od obowiązywałych zasad ponownego uruchamiania. Ten stan może wskazywać, że w module wystąpił nieodwracalny błąd. Błąd występuje, gdy Microsoft Monitoring Agent (MMA) nie może już ponownie użyć modułu, co wymaga nowego wdrożenia. |

Aby uzyskać [szczegółowe informacje, zobacz Zgłoszone właściwości edgeAgent.](module-edgeagent-edgehub.md#edgeagent-reported-properties)

### <a name="monitor-iot-edge-hub-module-twin"></a>Monitorowanie IoT Edge bliźniaczej reprezentacji modułu centrum danych

Poniższy kod JSON przedstawia bliźniacze reprezentacji modułu w Visual Studio Code z zwiniętą `$edgeHub` większość sekcji JSON.

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

Kod JSON można opisać w poniższych sekcjach, zaczynając od góry:

* Metadane — zawiera dane łączności.

* Właściwości — zawiera `desired` `reported` podsekcję i .
* Properties.desired — (pokazane zwinięte) Oczekiwane wartości właściwości ustawione przez operator w deployment.jspliku.
* Properties.reported — najnowsze wartości właściwości zgłoszone przez IoT Edge Hub.

Jeśli występują problemy z urządzeniami nadrzędnymi, warto zacząć od zbadania tych danych.

## <a name="monitor-custom-module-twins"></a>Monitorowanie bliźniaczych reprezentacji modułu niestandardowego

Informacje o łączności modułów niestandardowych są zachowywane w bliźniaczej reprezentacji IoT Edge agenta. Bliźniacza bliźniak modułu dla modułu niestandardowego jest używana głównie do obsługi danych dla rozwiązania. Żądane właściwości zdefiniowane w pliku deployment.jssą odzwierciedlane w bliźniaczej reprezentacji modułu, a moduł może aktualizować wartości zgłaszanej właściwości zgodnie z potrzebami.

Możesz użyć preferowanego języka programowania z zestawami SDK urządzeń [Azure IoT Hub,](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) aby zaktualizować zgłaszane wartości właściwości w bliźniaczej reprezentacji modułu na podstawie kodu aplikacji modułu. W poniższej procedurze użyto zestawu Azure SDK dla platformy .NET, aby to zrobić, używając kodu z modułu [SimulatedTemperatureSensor:](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)

1. Utwórz wystąpienie klasy [ModuleClient za](/dotnet/api/microsoft.azure.devices.client.moduleclient) pomocą [metody CreateFromEnvironmentAysnc.](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)

1. Pobierz kolekcję właściwości bliźniaczej reprezentacji modułu za pomocą [metody GetTwinAsync.](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync)

1. Utwórz odbiornik (przekazując wywołanie zwrotne), aby przechwytować zmiany żądanych właściwości za pomocą metody [SetDesiredPropertyUpdateCallbackAsync.](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync)

1. W metodzie wywołania zwrotnego zaktualizuj zgłaszane właściwości w bliźniaczej reprezentacji modułu za pomocą metody [UpdateReportedPropertiesAsync,](/dotnet/api/microsoft.azure.devices.client.moduleclient) przekazując wartość [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) wartości właściwości, które chcesz ustawić.

## <a name="access-the-module-twins"></a>Uzyskiwanie dostępu do bliźniaczych reprezentacji modułu

Możesz przejrzeć JSON dla bliźniaczych reprezentacji modułu w witrynie Azure IoT Hub, w Visual Studio Code i za pomocą interfejsu wiersza polecenia platformy Azure.

### <a name="monitor-in-azure-iot-hub"></a>Monitorowanie w Azure IoT Hub

Aby wyświetlić JSON bliźniaczej reprezentacji modułu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT.
1. Wybierz **IoT Edge** z menu okienka po lewej stronie.
1. Na **karcie IoT Edge urządzenia** wybierz identyfikator  urządzenia z modułami, które chcesz monitorować.
1. Wybierz nazwę modułu na karcie **Moduły,** a następnie wybierz pozycję **Bliźniaczka tożsamości** modułu na górnym pasku menu.

  ![Wybieranie bliźniaczej reprezentacji modułu do wyświetlenia w Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Jeśli zostanie wyświetlony komunikat "Tożsamość modułu nie istnieje dla tego modułu", ten błąd wskazuje, że rozwiązanie usługi back-end nie jest już dostępne, które pierwotnie utworzyło tożsamość.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Monitorowanie bliźniaczych reprezentacji modułu w Visual Studio Code

Aby przejrzeć i edytować bliźniacze reprezentacji modułu:

1. Jeśli nie jest jeszcze zainstalowany, [zainstaluj rozszerzenie Azure IoT Tools dla](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Visual Studio Code.
1. W **eksploratorze** rozwiń **Azure IoT Hub**, a następnie rozwiń urządzenie przy użyciu modułu, który chcesz monitorować.
1. Kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Edytuj bliźniacze reprezentacji modułu.** Tymczasowy plik bliźniaczej reprezentacji modułu jest pobierany na komputer i wyświetlany w Visual Studio Code.

  ![Uzyskiwanie bliźniaczej reprezentacji modułu do edytowania w Visual Studio Code](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Jeśli wprowadzasz zmiany, wybierz pozycję **Zaktualizuj bliźniacze** reprezentacji modułu nad kodem w edytorze, aby zapisać zmiany w centrum IoT.

  ![Aktualizowanie bliźniaczej reprezentacji modułu w Visual Studio Code](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Monitorowanie bliźniaczych reprezentacji modułu w interfejsie wiersza polecenia platformy Azure

Aby sprawdzić, IoT Edge jest uruchomiona, użyj polecenia [az iot hub invoke-module-method,](how-to-edgeagent-direct-method.md#ping) aby wysłać polecenie ping IoT Edge agenta.

Struktura [az iot hub module-twin](/cli/azure/iot/hub/module-twin) udostępnia następujące polecenia:

* **az iot hub module-twin show** — pokaż definicję bliźniaczej reprezentacji modułu.
* **az iot hub module-twin update** — aktualizowanie definicji bliźniaczej reprezentacji modułu.
* **az iot hub module-twin replace** — zastąp definicję bliźniaczej reprezentacji modułu docelowym kodem JSON.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [komunikować się z usługą EdgeAgent przy użyciu wbudowanych metod bezpośrednich.](how-to-edgeagent-direct-method.md)
