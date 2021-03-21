---
title: Jak wdrożyć moduł OPC bliźniaczy dla platformy Azure od podstaw | Microsoft Docs
description: W tym artykule opisano sposób wdrażania przędzy OPC od podstaw przy użyciu bloku IoT Edge Azure Portal, a także przy użyciu polecenia AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 38235f9b01b321e27664ee837763732971f0b85c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201502"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Wdróż moduł OPC i zależności od podstaw

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Moduł OPC bliźniaczy działa na IoT Edge i oferuje kilka usług brzegowych dla usług OPC i rejestrów urządzeń. 

Istnieje kilka opcji wdrażania modułów do bramy [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) , między nimi

- [Wdrażanie z bloku IoT Edge Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Wdrażanie przy użyciu polecenia AZ CLI](../iot-edge/how-to-deploy-cli-at-scale.md)

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrożenia i instrukcje, zobacz [repozytorium](https://github.com/Azure/azure-iiot-components)GitHub.

## <a name="deployment-manifest"></a>Manifest wdrożenia

Wszystkie moduły są wdrażane przy użyciu manifestu wdrożenia.  Poniżej przedstawiono przykładowy manifest do wdrożenia zarówno [OPC wydawcy](https://github.com/Azure/iot-edge-opc-publisher) , jak i [OPCa](https://github.com/Azure/azure-iiot-opc-twin-module) .

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--tm\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Wdrażanie z Azure Portal

Najprostszym sposobem wdrożenia modułów na urządzeniu bramy Azure IoT Edge jest przechodzenie przez Azure Portal.  

### <a name="prerequisites"></a>Wymagania wstępne

1. Wdróż [zależności](howto-opc-twin-deploy-dependencies.md) OPC i uzyskać powstały `.env` plik. Zanotuj wdrożoną `hub name` `PCS_IOTHUBREACT_HUB_NAME` zmienną w `.env` pliku.

2. Zarejestruj i uruchom bramę IoT Edge [systemu](../iot-edge/how-to-install-iot-edge.md) [Linux](../iot-edge/how-to-install-iot-edge.md) lub Windows i zanotuj jej `device id` .

### <a name="deploy-to-an-edge-device"></a>Wdrażanie na urządzeniu brzegowym

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do centrum IoT Hub.

2. Wybierz **IoT Edge** z menu po lewej stronie.

3. Kliknij identyfikator urządzenia docelowego z listy urządzeń.

4. Wybierz pozycję **Ustaw moduły**.

5. W sekcji **moduły wdrażania** strony wybierz pozycję **Dodaj** i **IoT Edge moduł.**

6. W oknie dialogowym **IoT Edge module niestandardowym** Użyj `opctwin` jako nazwy dla modułu, a następnie określ *Identyfikator URI obrazu* kontenera jako

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Jako *Opcje tworzenia kontenera* Użyj następującego kodu JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Wypełnij pola opcjonalne w razie potrzeby. Aby uzyskać więcej informacji o opcjach tworzenia kontenera, zasadach ponownego uruchamiania i żądanym stanie, zobacz [EdgeAgent wymagane właściwości](../iot-edge/module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat sznurka modułu, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](../iot-edge/module-composition.md#define-or-update-desired-properties).

7. Wybierz pozycję **Zapisz** i powtórz krok **5**.  

8. W oknie dialogowym IoT Edge modułu niestandardowego Użyj `opcpublisher` jako nazwy modułu i *identyfikatora URI obrazu* kontenera jako 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Jako *Opcje tworzenia kontenera* Użyj następującego kodu JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--tm","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Wybierz pozycję **Zapisz** , a następnie kliknij przycisk **dalej** , aby przejść do sekcji trasy.

10. Na karcie trasy Wklej następujące elementy: 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    i wybierz pozycję **dalej** .

11. Przejrzyj informacje o wdrożeniu i manifest.  Powinien wyglądać podobnie do powyższego manifestu wdrażania.  Wybierz pozycję **Prześlij**.

12. Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich na stronie **szczegóły urządzenia** w portalu. Ta strona zawiera nazwę każdego wdrożonego modułu, a także przydatne informacje, takie jak stan wdrożenia i kod zakończenia.

## <a name="deploying-using-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj najnowszą wersję [interfejsu wiersza polecenia platformy Azure (az)](/cli/azure/) w [tym miejscu](/cli/azure/install-azure-cli).

### <a name="quickstart"></a>Szybki start

1. Zapisz powyższy manifest wdrożenia w `deployment.json` pliku.  

2. Użyj następującego polecenia, aby zastosować konfigurację do urządzenia IoT Edge:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   W `device id` parametrze jest rozróżniana wielkość liter. Parametr Content wskazuje plik manifestu wdrożenia, który został zapisany. 
    ![AZ IoT Edge Set-modules Output](/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich za pomocą następującego polecenia:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   W parametrze identyfikatora urządzenia jest rozróżniana wielkość liter. ![AZ IoT Hub module-Identity list Output](/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrożyć OPCą sznurki od podstaw, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Wdróż bliźniaczy OPC do istniejącego projektu](howto-opc-twin-deploy-existing.md)