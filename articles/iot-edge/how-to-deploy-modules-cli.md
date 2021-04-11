---
title: Wdrażanie modułów z poziomu Azure IoT Edge wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure z rozszerzeniem usługi Azure IoT, aby wypchnąć moduł IoT Edge z IoT Hub do urządzenia IoT Edge, zgodnie z konfiguracją manifestu wdrożenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: cc4308cf69ecb99fccb09a6668825397675983cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201145"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Wdrażanie modułów Azure IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Po utworzeniu modułów IoT Edge za pomocą logiki biznesowej należy wdrożyć je na urządzeniach, aby działały na brzegu. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, możesz wdrożyć je wszystkie jednocześnie i zadeklarować reguły routingu, które je łączą.

[Interfejs wiersza polecenia platformy Azure](/cli/azure) to narzędzie wielodostępnej do obsługi wielu platform i zarządzania zasobami platformy Azure, takimi jak IoT Edge. Umożliwia ona zarządzanie zasobami IoT Hub platformy Azure, wystąpieniami usługi Device Provisioning Service i połączonymi centrami spoza pola. Nowe rozszerzenie IoT wzbogaca interfejs wiersza polecenia platformy Azure z funkcjami takimi jak zarządzanie urządzeniami i pełna IoT Edge.

W tym artykule przedstawiono sposób tworzenia manifestu wdrożenia JSON, a następnie użycia tego pliku do wypchnięcia wdrożenia na urządzenie IoT Edge. Aby uzyskać informacje na temat tworzenia wdrożenia, które jest przeznaczone dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-cli-at-scale.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* Urządzenie IoT Edge

  Jeśli nie masz skonfigurowanego urządzenia IoT Edge, możesz je utworzyć na maszynie wirtualnej platformy Azure. Wykonaj kroki opisane w jednym z artykułów z przewodnikiem Szybki Start, aby [utworzyć wirtualne urządzenie](quickstart-linux.md) z systemem Linux lub [utworzyć wirtualne urządzenie systemu Windows](quickstart.md).

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat działania manifestów wdrożenia i sposobu ich tworzenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu interfejsu wiersza polecenia platformy Azure, Zapisz manifest wdrożenia lokalnie jako plik JSON. Ścieżka do pliku zostanie użyta w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację na urządzeniu.

Oto podstawowy manifest wdrożenia z jednym modułem:

>[!NOTE]
>Ten przykładowy manifest wdrażania używa wersji schematu 1,1 dla agenta IoT Edge i centrum. Wersja schematu 1,1 została wydana wraz z IoT Edge wersja 1.0.10 i włącza funkcje takie jak kolejność uruchamiania modułu i priorytetyzacja tras.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
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
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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
          "schemaVersion": "1.1",
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

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Wdrożenie modułów na urządzeniu przez zastosowanie manifestu wdrożenia, który został skonfigurowany za pomocą informacji o module.

Zmień katalog na folder, w którym zapisano manifest wdrożenia. Jeśli użyto jednego z szablonów VS Code IoT Edge, użyj `deployment.json` pliku w folderze **konfiguracyjnym** katalogu rozwiązania, a nie `deployment.template.json` pliku.

Użyj następującego polecenia, aby zastosować konfigurację do urządzenia IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

W parametrze identyfikatora urządzenia jest rozróżniana wielkość liter. Parametr Content wskazuje plik manifestu wdrożenia, który został zapisany.

   ![AZ IoT Edge Set-modules Output](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich za pomocą następującego polecenia:

Wyświetl moduły znajdujące się na urządzeniu usługi IoT Edge:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

W parametrze identyfikatora urządzenia jest rozróżniana wielkość liter.

   ![AZ IoT Hub module-Identity list Output](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak wdrażać i monitorować moduły IoT Edge w odpowiedniej skali](how-to-deploy-at-scale.md)