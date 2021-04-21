---
title: Przykłady Connected Cache wdrażania w wersji zapoznawczej firmy Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Przykłady Connected Cache wdrażania w wersji zapoznawczej firmy Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811728"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Przykłady Connected Cache wdrażania w wersji zapoznawczej firmy Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Brama Azure IoT Edge bez serwera proxy

Na poniższym diagramie opisano scenariusz, w którym brama usługi Azure IoT Edge, która ma bezpośredni dostęp do zasobów usługi CDN i istnieje urządzenie liścia usługi Azure IoT, takie jak Raspberry PI, jest izolowanym internetowym urządzeniem podrzędnym bramy usługi Azure IoT Edge Gateway. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aktualizacja odłączonego Connected Cache Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Dodaj moduł Microsoft Connected Cache do wdrożenia urządzenia bramy usługi Azure IoT Edge w u [](connected-cache-disconnected-device-update.md) Azure IoT Hub (zobacz Obsługa odłączonych urządzeń, aby uzyskać szczegółowe informacje na temat sposobu uzyskania modułu).
2. Dodaj zmienne środowiskowe dla wdrożenia. Poniżej znajduje się przykład zmiennych środowiskowych.

    **Zmienne środowiskowe**
    
    | Nazwa                          | Wartość                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Zobacz [opisy zmiennych środowiskowych](connected-cache-configure.md) |
    | CUSTOMER_ID                   | Zobacz [opisy zmiennych środowiskowych](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Zobacz [opisy zmiennych środowiskowych](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Dodaj opcje tworzenia kontenera dla wdrożenia. Poniżej znajduje się przykład opcji tworzenia kontenera.

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

Aby zweryfikować prawidłowe działanie usługi Microsoft Connected Cache, wykonaj następujące polecenie w terminalu urządzenia IoT Edge hostującym moduł lub dowolne urządzenie w sieci. Zastąp parametr adresem IP lub nazwą hosta bramy \<Azure IoT Edge Gateway IP\> IoT Edge wirtualnej. (Aby uzyskać informacje na temat widoczności tego raportu, zobacz szczegóły zmiennych środowiskowych).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Brama Azure IoT Edge z nieuwierzytnionym serwerem proxy ruchu wychodzącego

W tym scenariuszu istnieje brama Azure IoT Edge, która ma dostęp do zasobów usługi CDN za pośrednictwem nieuwierzytanego serwera proxy ruchu wychodzącego. Usługa Microsoft Connected Cache jest konfigurowana do buforowania zawartości z repozytorium niestandardowego, a raport podsumowania został widoczny dla wszystkich użytkowników w sieci. Poniżej znajduje się przykład zmiennych środowiskowych MCC, które można ustawić.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Serwer proxy Connected Cache Microsoft Connected Cache" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Dodaj moduł Microsoft Connected Cache do wdrożenia urządzenia bramy Azure IoT Edge w Azure IoT Hub.
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
    | UPSTREAM_PROXY                | Adres IP lub nazwa FQDN serwera proxy                                          |

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

Aby zweryfikować prawidłowe działanie usługi Microsoft Connected Cache, wykonaj następujące polecenie w terminalu urządzenia Azure IoT Edge hostującym moduł lub dowolne urządzenie w sieci. Zastąp wartości adresem IP lub nazwą hosta bramy \<Azure IoT Edge Gateway IP\> IoT Edge wirtualnej. (Zobacz szczegóły zmiennych środowiskowych, aby uzyskać informacje na temat widoczności tego raportu).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
