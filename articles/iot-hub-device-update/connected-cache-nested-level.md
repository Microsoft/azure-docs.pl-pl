---
title: Firma Microsoft Connected Cache dwupoziomową zagnieżdżone Azure IoT Edge Gateway z wychodzącymi nieuwierzy informacje o nieuwierzytanych serwerach proxy | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Samouczek Connected Cache Microsoft Azure IoT Edge zagnieżdżonych w bramie Azure IoT Edge za pomocą nieuwierzytanego serwera proxy ruchu wychodzącego
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811890"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Przykład Connected Cache wdrażania usługi Microsoft Connected Cache w wersji zapoznawczej: dwupoziomowa zagnieżdżona brama Azure IoT Edge z nieuwierzytnionym serwerem proxy ruchu wychodzącego

Na poniższym diagramie opisano scenariusz, w którym jedna Azure IoT Edge ma bezpośredni dostęp do zasobów usługi CDN i działa jako element nadrzędny do innej Azure IoT Edge bramy. Brama IoT Edge pełni funkcję elementu nadrzędnego dla urządzenia liścia usługi Azure IoT, takiego jak Raspberry Pi. Urządzenie podrzędne Azure IoT Edge Azure IoT jest odizolowane od Internetu. W poniższym przykładzie pokazano konfigurację dla dwóch poziomów bram Azure IoT Edge, ale nie ma żadnego limitu głębokości hostów nadrzędnych, które będą Connected Cache microsoft Connected Cache. Opcje tworzenia kontenera usługi Microsoft Connected Cache nie różnią się od poprzednich przykładów.

Zapoznaj się z dokumentacją Łączenie urządzeń IoT Edge — [Azure IoT Edge,](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) aby uzyskać więcej informacji na temat konfigurowania warstwowych wdrożeń Azure IoT Edge bram. Ponadto podczas wdrażania modułów Azure IoT Edge, microsoft Connected Cache i modułów niestandardowych wszystkie moduły muszą znajdować się w tym samym rejestrze kontenerów.

>[!Note]
>Podczas wdrażania Azure IoT Edge, modułów Connected Cache Microsoft i modułów niestandardowych wszystkie moduły muszą znajdować się w tym samym rejestrze kontenerów.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft Connected Cache Zagnieżdżone" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Konfiguracja bramy nadrzędnej
1. Dodaj moduł Microsoft Connected Cache do wdrożenia urządzenia bramy usługi Azure IoT Edge w u [](connected-cache-disconnected-device-update.md) Azure IoT Hub (zobacz Obsługa odłączonych urządzeń, aby uzyskać szczegółowe informacje na temat sposobu uzyskania modułu).
2. Dodaj zmienne środowiskowe dla wdrożenia. Poniżej znajduje się przykład zmiennych środowiskowych.

    **Zmienne środowiskowe**

    | Nazwa                          | Wartość                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Zobacz [opisy zmiennych środowiskowych](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Zobacz [opisy zmiennych środowiskowych](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Zobacz [opisy zmiennych środowiskowych](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Dodaj opcje tworzenia kontenera dla wdrożenia. Opcje tworzenia kontenera MCC nie różnią się od poprzedniego przykładu. Poniżej znajduje się przykład opcji tworzenia kontenera.

### <a name="container-create-options"></a>Opcje tworzenia kontenera

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Konfiguracja bramy podrzędnej

>[!Note]
>Jeśli w twojej konfiguracji zostały zreplikowane kontenery używane we własnym rejestrze prywatnym, we wdrożeniu modułu konieczne będzie wprowadzenie modyfikacji ustawień pliku config.toml i ustawień środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz [Connect downstream IoT Edge devices - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) (Łączenie urządzeń IoT Edge podrzędnego — Azure IoT Edge więcej szczegółów.


1. Zmodyfikuj ścieżkę obrazu agenta edge, jak pokazano w poniższym przykładzie:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Zmodyfikuj ustawienia środowiska uruchomieniowego agenta usługi Edge w Azure IoT Edge, jak pokazano w tym przykładzie:
    
    * W obszarze Centrum krawędzi w polu obrazu wprowadź wartość ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * W obszarze Agent krawędzi w polu obrazu wprowadź wartość ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Dodaj moduł Microsoft Connected Cache do wdrożenia urządzenia bramy Azure IoT Edge w Azure IoT Hub.

   * Wybierz nazwę modułu: ```ConnectedCache```
   * Zmodyfikuj kod URI obrazu: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Dodaj ten sam zestaw zmiennych środowiskowych i opcje tworzenia kontenera, które są używane we wdrożeniu nadrzędnym.
>[!Note]
>Ta CACHE_NODE_ID powinna być unikatowa.  Wartości CUSTOMER_ID i CUSTOMER_KEY będą identyczne z wartościami nadrzędnymi. (zobacz [Konfigurowanie usługi Microsoft Connected Cache](connected-cache-configure.md)

Aby zweryfikować prawidłowe działanie usługi Microsoft Connected Cache, wykonaj następujące polecenie w terminalu urządzenia IoT Edge hostującym moduł lub dowolne urządzenie w sieci. Zastąp parametr adresem IP lub nazwą hosta bramy \<Azure IoT Edge Gateway IP\> IoT Edge wirtualnej. (Aby uzyskać informacje na temat widoczności tego raportu, zobacz szczegóły zmiennych środowiskowych).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```