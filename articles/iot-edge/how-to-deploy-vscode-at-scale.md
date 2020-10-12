---
title: Wdrażanie modułów na dużą skalę przy użyciu Visual Studio Code-Azure IoT Edge
description: Użyj rozszerzenia IoT Visual Studio Code, aby utworzyć automatyczne wdrożenia dla grup IoT Edge urządzeń.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92540c57179ae0198f78b588681167fe48097362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82134366"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Wdrażanie modułów IoT Edge na dużą skalę przy użyciu Visual Studio Code

Można utworzyć **IoT Edge Automatyczne wdrażanie** przy użyciu Visual Studio Code do zarządzania trwającymi wdrożeniami dla wielu urządzeń jednocześnie. Automatyczne wdrożenia dla IoT Edge są częścią funkcji [automatycznej zarządzania urządzeniami](/azure/iot-hub/iot-hub-automatic-device-management) w programie IoT Hub. Wdrożenia to procesy dynamiczne, które umożliwiają wdrożenie wielu modułów na wielu urządzeniach. Możliwe jest również śledzenie stanu i kondycji modułów oraz wprowadzanie zmian w razie potrzeby.

Aby uzyskać więcej informacji, zobacz [opis IoT Edge wdrożenia automatyczne dla pojedynczych urządzeń lub w odpowiedniej skali](module-deployment-monitoring.md).

W tym artykule opisano konfigurowanie Visual Studio Code i rozszerzenia IoT. Następnie dowiesz się, jak wdrażać moduły na zestawie IoT Edge urządzeń.

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-with-visual-studio-code) z zainstalowanym IoT Edge środowiska uruchomieniowego.
* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) dla Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

Za pomocą rozszerzeń usługi Azure IoT można Visual Studio Code do wykonywania operacji w centrum. Aby te operacje działały, należy zalogować się do konta platformy Azure i wybrać Centrum IoT, nad którym pracujesz.

1. W Visual Studio Code Otwórz widok **Eksploratora** .

1. W dolnej części Eksploratora rozwiń sekcję **IoT Hub platformy Azure** .

1. Kliknij pozycję **...** w nagłówku sekcji **IoT Hub platformy Azure** . Jeśli nie widzisz wielokropka, umieść kursor nad nagłówkiem.

1. Wybierz **pozycję wybierz IoT Hub**.

1. Jeśli nie zalogowano się na koncie platformy Azure, postępuj zgodnie z monitami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz Centrum IoT Hub.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia. Opisuje również sposób przepływu danych między modułami i żądanymi właściwościami modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wdrażania modułów i ustanawiania tras w programie IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu Visual Studio Code, Zapisz manifest wdrożenia lokalnie jako. Plik JSON. Musisz podać swoją lokalizację po uruchomieniu polecenia, aby zastosować konfigurację do urządzenia.

Oto podstawowy manifest wdrożenia z jednym modułem:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Aby określić, które urządzenia IoT Edge można obecnie skonfigurować, uruchom polecenie **IoT Edge: Pobierz informacje o urządzeniu** .

## <a name="identify-devices-with-target-conditions"></a>Identyfikowanie urządzeń z warunkami docelowymi

Aby zidentyfikować IoT Edge urządzeń, które mają otrzymać wdrożenie, należy określić warunek docelowy. Warunek docelowy jest spełniony, gdy określone kryteria są zgodne z identyfikatorem deviceId, wartością tagu lub zgłoszoną wartością właściwości.

Znaczniki można konfigurować w postaci sznurka urządzenia. Poniżej znajduje się przykład sznurka urządzenia z tagami:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

To urządzenie otrzyma wdrożenie, jeśli warunek docelowy wdrożenia zawiera wyrażenie odpowiadające jednemu z wartości tagu, takich jak `tag.location.building = '20'` .

Jeśli chcesz wskazać konkretne urządzenie, niezależnie od jego tagów lub innych wartości, wystarczy określić `deviceId` warunek dla warunku docelowego.

Oto kilka innych przykładów:

* deviceId = "linuxprod1"
* deviceId = "linuxprod1" lub deviceId = "linuxprod2" lub deviceId = "linuxprod3"
* Tagi. Environment = "prod"
* Tagi. Environment = "prod" i Tags. Location = "westus2"
* Tagi. Environment = "prod" lub Tags. Location = "westus2"
* Tags. operator = "Jan" i Tagi. Environment = "prod" i NOT deviceId = "linuxprod1"

Aby uzyskać szczegółowe informacje, zobacz [warunek docelowy](module-deployment-monitoring.md#target-condition) . Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji i tagów urządzeń, zobacz [Omówienie i używanie urządzenia bliźniaczych reprezentacji w IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Edytowanie sznurka urządzenia

Można edytować sznurki urządzenia w Visual Studio Code, aby skonfigurować Tagi. Z menu **Widok** wybierz pozycję **paleta poleceń** i uruchom polecenie **IoT Edge: Edit Device bliźniaczy** . Wybierz urządzenie IoT Edge i pojawi się sznury urządzenia.

W tym przykładzie nie zdefiniowano żadnych znaczników. Zastąp bieżącą pustą sekcję `"tags": {}` własną definicją tagów.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
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
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Po zapisaniu lokalnego pliku Uruchom polecenie **IoT Edge: Update splot urządzenia** .

## <a name="create-deployment-at-scale"></a>Tworzenie wdrożenia na dużą skalę

Po skonfigurowaniu manifestu wdrażania i skonfigurowaniu tagów w ramach sznurka urządzenia można przystąpić do wdrożenia.

1. Z menu **Widok** wybierz pozycję **paleta poleceń** i wybierz polecenie **Azure IoT Edge: Utwórz wdrożenie w skali** .

1. Przejdź do pliku JSON manifestu wdrożenia, którego chcesz użyć, a następnie kliknij pozycję **Wybierz manifest wdrożenia Edge**.

1. Podaj wartości jako monit, rozpoczynając od **identyfikatora wdrożenia**.

   ![Określ identyfikator wdrożenia](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Określ wartości tych parametrów:

  | Parametr | Opis |
  | --- | --- |
  | Identyfikator wdrożenia | Nazwa wdrożenia, które zostanie utworzone w usłudze IoT Hub. Nadaj wdrożenie unikatową nazwę, która jest maksymalnie 128 małymi literami. Unikaj spacji i następujących nieprawidłowych znaków: `& ^ [ ] { } \ | " < > /` . |
  | Warunek docelowy | Wprowadź warunek docelowy, aby określić, które urządzenia będą ukierunkowane na to wdrożenie.Warunek jest oparty na tagach bliźniaczych urządzeń lub w raportowanych właściwościach urządzenia i powinien być zgodny z formatem wyrażenia.Na przykład `tags.environment='test' and properties.reported.devicemodel='4000x'` . |
  | Priorytet |  Dodatnia liczba całkowita. Jeśli co najmniej dwa wdrożenia są przeznaczone dla tego samego urządzenia, zostanie zastosowane wdrożenie o najwyższej wartości liczbowej dla priorytetu. |

  Po określeniu priorytetu Terminal powinien wyświetlać dane wyjściowe podobne do następujących:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitorowanie i modyfikowanie wdrożeń

Użyj [Azure Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) lub [interfejsu wiersza polecenia platformy Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) , aby monitorować, modyfikować i usuwać wdrożenia. Oba zapewniają metryki dla wdrożeń.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wdrażaniu modułów na IoT Edge urządzeniach](module-deployment-monitoring.md).
