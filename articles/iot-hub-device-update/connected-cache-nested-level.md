---
title: Połączona pamięć podręczna firmy Microsoft dwa poziomy zagnieżdżona Brama Azure IoT Edge z wychodzącym nieuwierzytelnionym serwerem proxy | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Połączona pamięć podręczna firmy Microsoft dwa poziomy zagnieżdżona Brama Azure IoT Edge z wychodzącym samouczkiem serwera proxy
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 7facb74cd407c576b2a7b119f19427dcd185f04e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568821"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Przykład scenariusza wdrożenia w podglądzie połączonej pamięci podręcznej firmy Microsoft: dwupoziom zagnieżdżony Azure IoT Edge

Na poniższym diagramie w tym scenariuszu jest dostępna Brama Azure IoT Edge i Azure IoT Edge urządzenie podrzędne, a jedna brama Azure IoT Edge została zadana jako nadrzędna dla innej bramy Azure IoT Edge i serwera proxy w strefie DMZ IT. Poniżej przedstawiono przykład zmiennych środowiskowych pamięci podręcznej połączonej przez firmę Microsoft, które można ustawić w interfejsie użytkownika Azure Portal dla obu modułów MCC wdrożonych na bramach Azure IoT Edge. W pokazanym przykładzie przedstawiono konfigurację dwóch poziomów Azure IoT Edge bram, ale nie ma żadnego limitu dla hostów nadrzędnych, które obsługują pamięć podręczną połączoną przez firmę Microsoft. Nie ma żadnych różnic w MCC kontenera Utwórz opcje z poprzednich przykładów.

Aby uzyskać więcej informacji na temat konfigurowania wdrożeń warstwowych Azure IoT Edge bram, zapoznaj się z dokumentacją [IoT Edge Azure IoT Edge urządzeniach podrzędnych](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) . Dodatkowo należy zauważyć, że podczas wdrażania Azure IoT Edge, połączonej pamięci podręcznej firmy Microsoft i modułów niestandardowych wszystkie moduły muszą znajdować się w tym samym rejestrze kontenerów.

Na poniższym diagramie przedstawiono scenariusz, w którym jedna brama Azure IoT Edge jako bezpośredni dostęp do zasobów sieci CDN działa jako element nadrzędny dla innej bramy Azure IoT Edge działającej jako element nadrzędny dla urządzenia typu liść usługi Azure IoT, takiego jak Raspberry Pi. Tylko nadrzędny Azure IoT Edge bramy ma łączność z Internetem z zasobami sieci CDN i zarówno dla Azure IoT Edge podrzędnych, jak i z urządzenia Azure IoT. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Zagnieżdżona pamięć podręczna Microsoft" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Konfiguracja bramy nadrzędnej

1. Dodaj moduł połączonej pamięci podręcznej firmy Microsoft do wdrożenia urządzenia bramy Azure IoT Edge w usłudze Azure IoT Hub.
2. Dodaj zmienne środowiskowe dla wdrożenia. Poniżej znajduje się przykład zmiennych środowiskowych.

    **Zmienne środowiskowe**

    | Nazwa                 | Wartość                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Zobacz opis zmiennej środowiskowej powyżej. |
    | CUSTOMER_ID                   | Zobacz opis zmiennej środowiskowej powyżej. |
    | CUSTOMER_KEY                  | Zobacz opis zmiennej środowiskowej powyżej. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | Adres IP lub nazwa FQDN serwera proxy                     |

3. Dodaj opcje tworzenia kontenera dla wdrożenia. W poprzednim przykładzie nie ma żadnych różnic w kontenerach MCC. Poniżej znajduje się przykład opcji tworzenia kontenera.

### <a name="container-create-options"></a>Opcje tworzenia kontenera

```markdown
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
```

## <a name="child-gateway-configuration"></a>Konfiguracja bramy podrzędnej

>[!Note]
>Jeśli masz zreplikowane kontenery używane w konfiguracji w Twoim prywatnym rejestrze, należy zmodyfikować ustawienia konfiguracji. toml i ustawienia środowiska uruchomieniowego w ramach wdrożenia modułu. Aby uzyskać więcej informacji, zobacz [samouczek — Tworzenie hierarchii IoT Edge urządzeń — Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-the-lower-layer-device) , aby uzyskać więcej szczegółów.

1. Zmodyfikuj ścieżkę obrazu dla agenta usługi Edge, jak pokazano w poniższym przykładzie:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Zmodyfikuj ustawienia centrum Edge i środowisko uruchomieniowe agenta brzegowego w ramach wdrażania Azure IoT Edge, jak pokazano w tym przykładzie:
    
    * W obszarze centrum brzegowe w polu obraz wpisz ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * W obszarze Agent Edge w polu obraz wpisz ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Dodaj moduł połączonej pamięci podręcznej firmy Microsoft do wdrożenia urządzenia bramy Azure IoT Edge w usłudze Azure IoT Hub.

   * Wybierz nazwę modułu: ```ConnectedCache```
   * Modyfikuj identyfikator URI obrazu: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Dodaj te same zmienne środowiskowe i opcje tworzenia kontenera, które są używane w ramach wdrożenia nadrzędnego.

Aby sprawdzić poprawność działania połączonej pamięci podręcznej firmy Microsoft, wykonaj następujące polecenie w terminalu IoT Edge urządzenia obsługującego moduł lub dowolne urządzenie w sieci.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```