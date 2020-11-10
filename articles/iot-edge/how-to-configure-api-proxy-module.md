---
title: Konfigurowanie modułu proxy interfejsu API — Azure IoT Edge | Microsoft Docs
description: Dowiedz się, jak dostosować moduł proxy interfejsu API dla hierarchii usługi IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: f7536034eeac8548304f6a7f861910a99cd72a27
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447960"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Skonfiguruj moduł proxy interfejsu API dla scenariusza hierarchii bramy (wersja zapoznawcza)

Moduł proxy interfejsu API umożliwia urządzeniom IoT Edge wysyłanie żądań HTTP za pośrednictwem bram zamiast bezpośredniego połączenia z usługami w chmurze. W tym artykule opisano opcje konfiguracji umożliwiające dostosowanie modułu do wymagań hierarchii bramy.

>[!NOTE]
>Ta funkcja wymaga IoT Edge w wersji 1,2, która jest w publicznej wersji zapoznawczej z uruchomionymi kontenerami systemu Linux.

W niektórych architekturach sieci IoT Edge urządzenia za bramami nie mają bezpośredniego dostępu do chmury. Wszystkie moduły, które próbują nawiązać połączenie z usługami w chmurze, zakończą się niepowodzeniem. Moduł proxy interfejsu API obsługuje urządzenia podrzędne IoT Edge w tej konfiguracji przez ponowne routing połączeń modułu, aby przełączać się między warstwami hierarchii bramy. Zapewnia bezpieczną metodę komunikacji klientów z wieloma usługami za pośrednictwem protokołu HTTPS bez tunelowania, ale przez przerwanie połączeń w każdej warstwie. Moduł proxy interfejsu API pełni rolę modułu proxy między urządzeniami IoT Edge w hierarchii bramy, dopóki nie osiągną IoT Edge urządzenia w górnej warstwie. W tym momencie usługi działające na najwyższej warstwie IoT Edge urządzenie obsłużą te żądania, a moduł proxy interfejsu API w celu wypróbowania całego ruchu HTTP z usług lokalnych do chmury za pośrednictwem jednego portu.

Moduł proxy interfejsu API umożliwia włączenie wielu scenariuszy dla hierarchii bramy, w tym umożliwienie urządzeniom niższej warstwy ściągania obrazów kontenera lub wypychania obiektów BLOB do magazynu. Konfiguracja reguł serwera proxy definiuje sposób, w jaki dane są kierowane. W tym artykule omówiono kilka typowych opcji konfiguracji.

## <a name="deploy-the-proxy-module"></a>Wdróż moduł proxy

Moduł proxy interfejsu API jest dostępny w Container Registry firmy Microsoft (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:latest` .

Moduł proxy interfejsu API można również wdrożyć bezpośrednio w portalu Azure Marketplace: [IoT Edge proxy interfejsu API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Zrozumienie modułu proxy

Moduł serwera proxy interfejsu API korzysta z Nginx zwrotnego serwera proxy do przesyłania danych za pomocą warstwy sieci. Serwer proxy jest osadzony w module, co oznacza, że obraz modułu musi obsługiwać konfigurację serwera proxy. Na przykład, jeśli serwer proxy nasłuchuje na określonym porcie, moduł musi mieć otwarty ten port.

Serwer proxy rozpoczyna się od domyślnego pliku konfiguracji osadzonego w module. Nową konfigurację można przekazać do modułu z chmury przy użyciu [sznurka modułu](../iot-hub/iot-hub-devguide-module-twins.md). Ponadto można użyć zmiennych środowiskowych, aby włączyć lub wyłączyć ustawienia konfiguracji w czasie wdrażania.

Ten artykuł koncentruje się najpierw na domyślnym pliku konfiguracji i sposobach używania zmiennych środowiskowych w celu włączenia jego ustawień. Następnie omówiono Dostosowywanie pliku konfiguracji na końcu.

### <a name="default-configuration"></a>Konfiguracja domyślna

Moduł proxy interfejsu API zawiera domyślną konfigurację, która obsługuje typowe scenariusze i umożliwia dostosowanie. Domyślną konfigurację można kontrolować za pomocą zmiennych środowiskowych modułu.

Obecnie domyślne zmienne środowiskowe obejmują:

| Zmienna środowiskowa | Opis |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Wyświetl listę wszystkich zmiennych, które mają zostać zaktualizowane na liście rozdzielanej przecinkami. Ten krok zapobiega przypadkowemu zmodyfikowaniu nieprawidłowych ustawień konfiguracji.
| `NGINX_DEFAULT_PORT` | Zmienia port, na który nasłuchuje serwer proxy Nginx. W przypadku aktualizowania tej zmiennej środowiskowej upewnij się, że wybrany port jest również uwidoczniony w module pliku dockerfile i zadeklarowany jako powiązanie portu w manifeście wdrożenia.<br><br>Wartość domyślna to 443.<br><br>W przypadku wdrożenia z portalu Azure Marketplace domyślny port jest aktualizowany do 8000, aby zapobiec konfliktom z modułem edgeHub. Aby uzyskać więcej informacji, zobacz [minimalizowanie otwartych portów](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Adres do trasy żądań platformy Docker. Zmodyfikuj tę zmienną na urządzeniu warstwy najwyższej, aby wskazywała na moduł rejestru.<br><br>Wartość domyślna to nadrzędna nazwa hosta. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Adres do przesyłania żądań rejestru obiektów BLOB. Zmodyfikuj tę zmienną na urządzeniu warstwy najwyższej, aby wskazywała na moduł BLOB Storage.<br><br>Wartość domyślna to nadrzędna nazwa hosta. |

## <a name="minimize-open-ports"></a>Minimalizuj otwarte porty

Aby zminimalizować liczbę otwartych portów, moduł proxy interfejsu API powinien przekazać cały ruch HTTPS (port 443), w tym ruch skierowany do modułu edgeHub. Moduł proxy interfejsu API jest domyślnie skonfigurowany, aby ponownie kierować cały ruch edgeHub na porcie 443.

Wykonaj następujące kroki, aby skonfigurować wdrożenie w celu zminimalizowania liczby otwartych portów:

1. Zaktualizuj ustawienia modułu edgeHub, aby nie powiązać się z portem 443. w przeciwnym razie wystąpią konflikty powiązań portów. Domyślnie moduł edgeHub jest powiązany z portami 443, 5671 i 8883. Usuń powiązanie portu 443 i pozostaw pozostałe dwa w miejscu:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Skonfiguruj moduł proxy interfejsu API, aby powiązać go z portem 443.

   1. Ustaw wartość zmiennej środowiskowej **NGINX_DEFAULT_PORT** na `443` .
   1. Zaktualizuj opcje tworzenia kontenera, aby powiązać je z portem 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Jeśli nie musisz zminimalizować otwartych portów, możesz pozwolić, aby moduł edgeHub używał portu 443 i skonfigurować moduł proxy interfejsu API do nasłuchiwania na innym porcie. Na przykład moduł proxy interfejsu API może nasłuchiwać na porcie 8000 przez ustawienie wartości zmiennej środowiskowej **NGINX_DEFAULT_PORT** na `8000` i utworzenie powiązania portu dla portu 8000.

## <a name="enable-container-image-download"></a>Włącz pobieranie obrazu kontenera

Typowym przypadkiem użycia dla modułu proxy interfejsu API jest umożliwienie IoT Edge urządzeniom w niższych warstwach do ściągania obrazów kontenerów. W tym scenariuszu [moduł Docker Registry](https://hub.docker.com/_/registry) jest wykorzystywany do pobierania obrazów kontenerów z chmury i buforowania ich na najwyższej warstwie. Serwer proxy interfejsu API przekazuje wszystkie żądania HTTPS, aby pobrać obraz kontenera z niższych warstw, które mają być obsługiwane przez moduł rejestru w górnej warstwie.

Ten scenariusz wymaga, aby podrzędne urządzenia IoT Edge wskazywały nazwę domeny, `$upstream` a następnie numer portu modułu proxy interfejsu API zamiast rejestru kontenerów obrazu. Na przykład: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Ten przypadek użycia przedstawiono w samouczku [Tworzenie hierarchii IoT Edge urządzeń przy użyciu bram](tutorial-nested-iot-edge.md).

Skonfiguruj następujące moduły w **górnej warstwie** :

* Moduł rejestru platformy Docker
  * Skonfiguruj moduł z zapamiętaną nazwą, taką jak *Rejestr* , i Uwidocznij port w module, aby otrzymywać żądania.
  * Skonfiguruj moduł do mapowania na rejestr kontenerów.
* Moduł proxy interfejsu API
  * Skonfiguruj następujące zmienne środowiskowe:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Nazwa modułu rejestru i otwarty port. Na przykład `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Port, na którym serwer proxy Nginx nasłuchuje żądań z urządzeń podrzędnych. Na przykład `8000`. |

  * Skonfiguruj następujące opcje:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Skonfiguruj następujący moduł na dowolnej **niższej warstwie** w tym scenariuszu:

* Moduł proxy interfejsu API
  * Skonfiguruj następujące zmienne środowiskowe:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Port, na którym serwer proxy Nginx nasłuchuje żądań z urządzeń podrzędnych. Na przykład `8000`. |

  * Skonfiguruj następujące opcje:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Włącz przekazywanie obiektów BLOB

Innym przypadkiem użycia dla modułu proxy interfejsu API jest umożliwienie IoT Edge urządzeniom w niższych warstwach przekazywania obiektów BLOB. Ten przypadek użycia umożliwia korzystanie z funkcji rozwiązywania problemów na niższych urządzeniach warstwy, takich jak przekazywanie dzienników modułów lub przekazywanie pakietu obsługi.

W tym scenariuszu do obsługi tworzenia i przekazywania obiektów BLOB są używane [BLOB Storage platformy Azure w IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) module w górnej warstwie.

Skonfiguruj następujące moduły w **górnej warstwie** :

* Blob Storage platformy Azure na IoT Edge module.
* Moduł proxy interfejsu API
  * Skonfiguruj następujące zmienne środowiskowe:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Nazwa modułu magazynu obiektów blob i otwarty port. Na przykład `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Port, na którym serwer proxy Nginx nasłuchuje żądań z urządzeń podrzędnych. Na przykład `8000`. |

  * Skonfiguruj następujące opcje:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Skonfiguruj następujący moduł na dowolnej **niższej warstwie** w tym scenariuszu:

* Moduł proxy interfejsu API
  * Skonfiguruj następujące zmienne środowiskowe:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Port, na którym serwer proxy Nginx nasłuchuje żądań z urządzeń podrzędnych. Na przykład `8000`. |

  * Skonfiguruj następujące opcje:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Wykonaj następujące kroki, aby przekazać pakiet obsługi lub plik dziennika do modułu BLOB Storage znajdującego się w górnej warstwie:

1. Utwórz kontener obiektów BLOB przy użyciu Eksplorator usługi Azure Storage lub interfejsów API REST. Aby uzyskać więcej informacji, zobacz artykuł [Przechowuj dane na krawędzi za pomocą usługi Azure Blob Storage w IoT Edge](how-to-store-data-blob.md).
1. Poproś o dziennik lub Załaduj pakiet pomocy technicznej zgodnie z instrukcjami w temacie [pobieranie dzienników z wdrożeń IoT Edge](how-to-retrieve-iot-edge-logs.md), ale Użyj nazwy domeny `$upstream` i portu open proxy zamiast adresu modułu usługi BLOB Storage. Na przykład:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Edytowanie konfiguracji serwera proxy

Domyślny plik konfiguracji jest osadzony w module proxy interfejsu API, ale można przekazać nową konfigurację do modułu za pośrednictwem chmury przy użyciu sznurka modułu.

Podczas pisania własnej konfiguracji można nadal używać środowiska do dostosowywania ustawień na wdrożenie. Użyj następującej składni:

* Służy `${MY_ENVIRONMENT_VARIABLE}` do pobierania wartości zmiennej środowiskowej.
* Użyj instrukcji warunkowych, aby włączyć lub wyłączyć ustawienia na podstawie wartości zmiennej środowiskowej:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Gdy moduł serwera proxy interfejsu API analizuje konfigurację serwera proxy, najpierw zastępuje wszystkie zmienne środowiskowe wymienione w `PROXY_CONFIG_ENV_VAR_LIST` podanych wartościach przy użyciu podstawienia. Następnie wszystko `#if_tag` `#endif_tag` jest zastępowane przez parę i. Następnie moduł udostępnia przeanalizowana konfigurację do zwrotnego serwera proxy Nginx.

Aby dynamicznie zaktualizować konfigurację serwera proxy, wykonaj następujące czynności:

1. Napisz plik konfiguracji. Tego szablonu domyślnego można użyć jako odwołania: [nginx_default_config. conf](hhttps://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Skopiuj tekst pliku konfiguracji i przekonwertuj go na format Base64.
1. Wklej zakodowany plik konfiguracji jako wartość `proxy_config` żądanej właściwości w formie sznurka modułu.

   ![Wklej zakodowany plik konfiguracyjny jako wartość właściwości proxy_config](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Następne kroki

Użyj modułu proxy interfejsu API, aby [połączyć urządzenie podrzędne IoT Edge z bramą Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md).