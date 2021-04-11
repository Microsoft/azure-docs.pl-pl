---
title: Przykłady scenariusza wdrożenia w podglądzie połączonej pamięci podręcznej firmy Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Samouczki dotyczące scenariusza wdrożenia w podglądzie połączonej pamięci podręcznej Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101664776"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Przykłady scenariusza wdrożenia w podglądzie połączonej pamięci podręcznej firmy Microsoft

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Brama Azure IoT Edge na poziomie jednego poziomu nie ma serwera proxy

Na poniższym diagramie przedstawiono scenariusz, w którym Brama Azure IoT Edge, która ma bezpośredni dostęp do zasobów sieci CDN, i istnieje urządzenie typu liść usługi Azure IoT, takie jak Raspberry PI, które jest zewnętrznymi urządzeniami podrzędnymi Azure IoT Edge bramy. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Aktualizacja urządzenia odłączona pamięć podręczna firmy Microsoft" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Dodaj moduł połączonej pamięci podręcznej firmy Microsoft do wdrożenia urządzenia Azure IoT Edge bramy w usłudze Azure IoT Hub (zobacz `MCC concepts` , aby uzyskać szczegółowe informacje na temat pobierania modułu).
2. Dodaj zmienne środowiskowe dla wdrożenia. Poniżej znajduje się przykład zmiennych środowiskowych.

    **Zmienne środowiskowe**
    
    | Nazwa                 | Wartość                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Zobacz opis zmiennej środowiskowej powyżej. |
    | CUSTOMER_ID                   | Zobacz opis zmiennej środowiskowej powyżej. |
    | CUSTOMER_KEY                  | Zobacz opis zmiennej środowiskowej powyżej. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Dodaj opcje tworzenia kontenera dla wdrożenia. Poniżej znajduje się przykład opcji tworzenia kontenera.

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

Aby sprawdzić poprawność działania połączonej pamięci podręcznej firmy Microsoft, wykonaj następujące polecenie w terminalu IoT Edge urządzenia obsługującego moduł lub dowolne urządzenie w sieci.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Brama Azure IoT Edge z jednym poziomem z wychodzącym nieuwierzytelnionym serwerem proxy

W tym scenariuszu istnieje Brama Azure IoT Edge, która ma dostęp do zasobów sieci CDN za poorednictwem wychodzącego nieuwierzytelnionego serwera proxy. Połączona pamięć podręczna firmy Microsoft jest konfigurowana w celu buforowania zawartości z niestandardowego repozytorium, a raport podsumowujący był widoczny dla wszystkich użytkowników w sieci. Poniżej znajduje się przykład zmiennych środowiskowych MCC, które zostałyby ustawione.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Serwer proxy w połączonej pamięci podręcznej firmy Microsoft" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

Aby sprawdzić poprawność działania połączonej pamięci podręcznej firmy Microsoft, wykonaj następujące polecenie w terminalu Azure IoT Edge urządzenia obsługującego moduł lub dowolne urządzenie w sieci.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
