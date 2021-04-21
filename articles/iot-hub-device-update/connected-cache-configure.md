---
title: Konfigurowanie usługi Microsoft Connected Cache aktualizacji urządzenia dla Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Omówienie usługi Microsoft Connected Cache aktualizacji urządzenia dla Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811835"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Konfigurowanie usługi Microsoft Connected Cache aktualizacji urządzenia dla Azure IoT Hub

Usługa Microsoft Connected Cache jest wdrażana Azure IoT Edge bramach jako Azure IoT Edge module. Podobnie jak Azure IoT Edge modułów, zmienne środowiskowe wdrażania modułu MCC i opcje tworzenia kontenera są używane do konfigurowania modułów Connected Cache Microsoft.  W tej sekcji zdefiniowano zmienne środowiskowe i opcje tworzenia kontenera wymagane przez klienta do pomyślnego wdrożenia modułu Microsoft Connected Cache do użycia przez usługę Device Update dla Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Szczegóły Connected Cache Azure IoT Edge wdrażania modułu microsoft Connected Cache Azure IoT Edge

Nazwa modułu Microsoft Connected Cache jest wedle uznania administratora. Nie ma żadnych innych interakcji modułów ani usług, które opierają się na nazwie modułu na potrzeby komunikacji. Ponadto relacja nadrzędny-podrzędny serwerów microsoft Connected Cache nie jest zależna od tej nazwy modułu, ale raczej nazwy FQDN lub adresu IP bramy usługi Azure IoT Edge, która została skonfigurowana zgodnie z wcześniejszym omówieniem.

Zmienne środowiskowe Connected Cache Azure IoT Edge Microsoft Connected Cache Azure IoT Edge module są używane do przekazania podstawowych informacji o tożsamości modułu i ustawień modułu funkcjonalnego do kontenera.

| Nazwa zmiennej                 | Format wartości                           | Wymagane/Opcjonalne | Funkcjonalność                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Identyfikator GUID subskrypcji platformy Azure             | Wymagane          | Jest to klucz klienta, który zapewnia bezpieczeństwo<br>uwierzytelnianie węzła pamięci podręcznej do Optymalizacja dostarczania<br>Usług.<br>Wymagane, aby moduł działał. |
| CACHE_NODE_ID                 | Identyfikator GUID węzła pamięci podręcznej                     | Wymagane          | Unikatowo identyfikuje Connected Cache<br>z węzła do Optymalizacja dostarczania Services.<br>Wymagane w kolejności<br> aby moduł działał. |
| CUSTOMER_KEY                  | Identyfikator GUID klucza klienta                     | Wymagane          | Jest to klucz klienta, który zapewnia bezpieczeństwo<br>uwierzytelnianie węzła pamięci podręcznej w usługach Optymalizacja dostarczania Services.<br>Wymagane, aby moduł działał.|
| STORAGE_ *N* _SIZE_GB           | Gdzie N to dysk pamięci podręcznej   | Wymagane          | Określ maksymalnie 9 dysków do buforowania zawartości i określ maksymalną ilość miejsca w<br>Gigabajty do przydzielenia dla zawartości na każdym dysku pamięci podręcznej. Przykłady:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Liczba dysków musi odpowiadać określonym wartościom powiązania dysku pamięci podręcznej<br>w wartości N opcji tworzenia kontenera MicrosoftConnectedCache<br>Minimalny rozmiar pamięci podręcznej to 10 GB.|
| UPSTREAM_HOST                 | FQDN/IP                                | Opcjonalne          | Ta wartość może określać nadrzędne połączenie firmy Microsoft<br>Węzeł pamięci podręcznej, który działa jako serwer proxy, jeśli Connected Cache węzła<br> jest odłączony od Internetu. To ustawienie jest używane do obsługi<br> zagnieżdżony scenariusz IoT.<br>**Uwaga:** Usługa Microsoft Connected Cache nasłuchuje na domyślnym porcie HTTP 80.|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | Opcjonalne          | Wychodzący internetowy serwer proxy.<br>Może to być również serwer proxy OT DMZ, jeśli sieć ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>Nazwa FQDN                        | Opcjonalne          | Wymagane do obsługi niestandardowych repozytoriów pakietów.<br>Repozytoria mogą być hostowane lokalnie lub w Internecie.<br>Nie ma żadnego limitu liczby hostów niestandardowych, które można skonfigurować.<br><br>Przykłady:<br>Nazwa = CACHEABLE_CUSTOM_1_HOST = packages.foo.com<br> Nazwa = CACHEABLE_CUSTOM_2_HOST wartość = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Opcjonalne          | Wymagane do obsługi niestandardowych repozytoriów pakietów.<br>Ta wartość może służyć jako alias i będzie używana przez serwer pamięci podręcznej do odwołania<br>różne nazwy DNS. Na przykład nazwa hosta zawartości repozytorium może być packages.foo.com,<br>ale w różnych regionach może być dodatkowy prefiks dodawany do nazwy hosta<br>na westuscdn.packages.foo.com i eastuscdn.packages.foo.com.<br>Ustawiając alias kanoniczny, upewniasz się, że zawartość nie jest duplikowana<br>dla zawartości pochodzącej z tego samego hosta, ale z różnych źródeł cdn.<br>Format wartości kanonicznej nie jest ważny, ale musi być unikatowy dla hosta.<br>Najprostszym rozwiązaniem może być ustawienie wartości zgodnej z wartością hosta.<br><br>Przykłady oparte na przykładach hostów niestandardowych powyżej:<br>Nazwa = CACHEABLE_CUSTOM_1_CANONICAL wartość = foopackages<br> Nazwa = CACHEABLE_CUSTOM_2_CANONICAL = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Prawda czy fałsz                          | Opcjonalne          | Umożliwia wyświetlanie raportu podsumowania w sieci lokalnej lub w Internecie.<br>Użycie klucza interfejsu API (omówionego w dalszej części) jest wymagane do wyświetlenia raportu podsumowania, jeśli ustawiono wartość true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Prawda czy fałsz                          | Opcjonalne          | Umożliwia wyświetlanie raportu podsumowania w sieci lokalnej lub Internecie bez<br>użycie klucza interfejsu API z dowolnego urządzenia w sieci. Użyj , jeśli nie chcesz blokować dostępu<br>do wyświetlania danych podsumowania serwera pamięci podręcznej za pośrednictwem przeglądarki. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opcje tworzenia kontenera Connected Cache Azure IoT Edge Microsoft Connected Cache Azure IoT Edge module

Opcje tworzenia kontenera dla wdrożenia modułu MCC zapewniają kontrolę nad ustawieniami związanymi z magazynem i portami używanymi przez moduł MCC. Jest to lista wymaganych zmiennych utworzonych przez kontener używany do wdrażania mcc.

### <a name="container-to-host-os-drive-mappings"></a>Mapowania dysków systemu operacyjnego hosta kontenera

Wymagane do mapowania lokalizacji magazynu kontenera na lokalizację magazynu na dysku.< można określić maksymalnie dziewięć lokalizacji.

>[!Note]
>Liczba dysków musi odpowiadać wartościom powiązania dysku pamięci podręcznej określonym w zmiennej środowiskowej STORAGE_ *N* _SIZE_GB wartości, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Kontener do hostowania mapowań portów TCP

Ta opcja określa port HTTP zewnętrznego komputera, na podstawie których mcc nasłuchuje żądań zawartości. Domyślny port HostPort to port 80, a inne porty nie są obecnie obsługiwane, ponieważ klient usługi ADU wysyła obecnie żądania na porcie 80. Port TCP 8081 to wewnętrzny port kontenera, na który nasłuchuje mcc i nie można go zmienić.

### <a name="container-service-tcp-port-mappings"></a>Mapowania portów TCP usługi kontenera

Moduł Microsoft Connected Cache zawiera usługę .NET Core, która jest używana przez aparat buforowania dla różnych funkcji.

>[!Note]
>Aby obsługiwać usługę Azure IoT Nested Edge, nie można ustawić portu HostPort na wartość 5000, ponieważ moduł serwera proxy rejestru nasłuchuje już na porcie hosta 5000.


Opcje tworzenia przykładowego kontenera

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
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

## <a name="microsoft-connected-cache-summary-report"></a>Raport podsumowania Connected Cache Microsoft

Raport podsumowujący jest obecnie jedynym sposobem, aby klient wyświetlał dane buforowania dla wystąpień usługi Microsoft Connected Cache wdrożonych w Azure IoT Edge bramach. Raport jest generowany co 15 sekund i zawiera uśrednione statystyki dla okresu, a także zagregowane statystyki dla okresu istnienia modułu. Najważniejsze statystyki, które będą interesować klientów, to:

* **hitBytes** — jest to suma bajtów dostarczonych bezpośrednio z pamięci podręcznej.
* **missBytes —** jest to suma bajtów dostarczonych przez firmę Microsoft Connected Cache do pobrania z usługi CDN w celu zobaczenia pamięci podręcznej.
* **eggressBytes** — jest to suma hitBytes i missBytes oraz całkowita liczba bajtów dostarczonych do klientów.
* **hitRatioBytes** — jest to stosunek hitBytes do egressBytes.  Gdyby 100% eggressBytes dostarczonych w okresie było równe hitBytes, byłoby to na przykład 1.


Raport podsumowujący jest dostępny w teście Zastąp adresem IP lub `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` \<Azure IoT Edge Gateway IP\> nazwą hosta IoT Edge bramy. (Zobacz szczegóły zmiennych środowiskowych, aby uzyskać informacje na temat widoczności tego raportu).