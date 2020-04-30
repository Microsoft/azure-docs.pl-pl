---
title: Utwórz zasoby techniczne modułu Azure IoT Edge | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla modułu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 1a523872bb15981958eeb1678caa1d911a9cbbba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142404"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Przygotuj zasoby techniczne modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą modułu IoT Edge w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Create an IoT Edge module](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano wymagania, które zasoby techniczne modułu IoT Edge muszą spełnić przed opublikowaniem w witrynie Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Zrozumienie IoT Edge modułów i wprowadzenie

Moduł IoT Edge jest kontenerem zgodnym z platformą Docker, który jest uruchamiany na urządzeniu IoT Edge.

- Aby dowiedzieć się więcej na temat modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Aby rozpocząć pracę z rozwojem modułu IoT Edge, zobacz temat [wymagania i narzędzia do opracowywania modułów IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Wymagania techniczne

Aby moduł IoT Edge mógł być certyfikowany i publikowany w portalu Azure Marketplace, muszą zostać spełnione następujące wymagania techniczne.

### <a name="platform-support"></a>Obsługa platform

Moduł IoT Edge musi obsługiwać jedną z następujących opcji platformy.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy warstwy 1 obsługiwane przez IoT Edge

Obsługa wszystkich platform warstwy 1 obsługiwanych przez IoT Edge (zgodnie z zapisaniem w [Azure IoT Edge support](https://docs.microsoft.com/azure/iot-edge/support)). Zalecamy użycie tej opcji, ponieważ oferuje ona lepszy komfort obsługi klienta. Zostaną zaprezentowane moduły spełniające te kryteria. Moduł korzystający z tej opcji platformy musi:

- Podaj `latest` tag i tag wersji (na przykład `1.0.1`), które są tagami manifestu skompilowanymi przy użyciu [narzędzia manifestu](https://github.com/estesp/manifest-tool)usługi GitHub.
- Skorzystaj z [karty Marketplace](./cpp-marketplace-tab.md) , aby dodać łącze do [certyfikatu IoT Edge](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}), witryny sieci Web, w której klienci mogą przeglądać lub wyszukiwać certyfikowane urządzenia. Ta witryna sieci Web jest również znana jako [Azure IoT Edge wykaz certyfikowanych](https://catalog.azureiotsolutions.com/) urządzeń.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podzbiór platform warstwy 1 obsługiwanych przez IoT Edge
  
Obsługa podzestawu (co najmniej jednej) platform warstwy 1 obsługiwanej przez IoT Edge (zgodnie z zarejestrowaniem w [Azure IoT Edge support](https://docs.microsoft.com/azure/iot-edge/support)). Moduł korzystający z tej opcji platformy musi:

- Podaj `latest` tag i tag wersji (na przykład `1.0.1`), które są tagami manifestu skompilowanymi przy użyciu [narzędzia manifestu](https://github.com/estesp/manifest-tool) GitHub, jeśli obsługiwana jest więcej niż jedna platforma. Tagi manifestu są opcjonalne tylko wtedy, gdy jedna platforma jest obsługiwana.
- Skorzystaj z [karty Marketplace](./cpp-marketplace-tab.md) , aby udostępnić link do co najmniej jednego urządzenia IoT Edge w katalogu [Azure IoT Edge certyfikowane](https://catalog.azureiotsolutions.com/) urządzenia.

### <a name="device-dimensions"></a>Wymiary urządzenia

Wymiary modułu IoT Edge (procesor CPU/pamięć RAM/magazyn/procesor GPU/itp.) na urządzeniach przeznaczonych IoT Edge muszą spełniać następujące wymagania:

- Moduł musi **współpracować z co najmniej jednym IoT Edge certyfikowanym** urządzeniem w katalogu [Azure IoT Edge certyfikowanych](https://catalog.azureiotsolutions.com/) urządzeń.
- **Minimalne wymagania sprzętowe** muszą być udokumentowane jako ostatni akapit w opisie oferty (na [karcie Marketplace](./cpp-marketplace-tab.md)). Opcjonalnie można również wyświetlić zalecane wymagania sprzętowe, jeśli różnią się one znacznie. Na przykład Dodaj następującą sekcję na końcu opisu oferty:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguracja

Zawiera również domyślne ustawienia konfiguracji, które umożliwiają wdrożenie na urządzeniu IoT Edge, jak to możliwe. Kontener może również zawierać zestaw SDK modułu IoT Edge, aby umożliwić komunikację z edgeHub i IoT Hub.

#### <a name="default-configuration"></a>Konfiguracja domyślna

Moduły IoT Edge muszą mieć możliwość uruchamiania z ustawieniami domyślnymi dostępnymi na [karcie jednostki SKU portalu Cloud partner](./cpp-skus-tab.md). Dostępne są następujące ustawienia domyślne:

- **Trasy** domyślne
- Domyślne **Właściwości przędzy**
- Domyślne **zmienne środowiskowe**
- Domyślne **Opcje**

W scenariuszu, w którym parametr wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), należy dodać parametr jako wartość domyślną. Ta wartość jest ujęta w nawiasy klamrowe i wielkie litery. W tym przykładzie skonfigurowano następującą domyślną zmienną środowiskową:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentacja dotycząca konfiguracji

Wszystkie ustawienia konfiguracji modułu IoT Edge muszą być jasno udokumentowane (jak używać tras, pożądanych właściwości, zmiennych środowiskowych, opcji i tak dalej). Podaj link do dokumentacji lub dokumentacja musi być częścią opisu oferty/jednostki SKU.

### <a name="tags-and-versioning"></a>Znaczniki i przechowywanie wersji

Klienci muszą być w stanie łatwo wdrożyć moduł i automatycznie pobierać aktualizacje z portalu Marketplace (w scenariuszu dla deweloperów). Muszą oni również mieć możliwość użycia i zablokowania dokładnej wersji, które zostały przetestowane (w scenariuszu produkcyjnym).

W celu spełnienia oczekiwań klientów i ich opublikowania w portalu Marketplace moduły IoT Edge muszą spełniać następujące wymagania:

- Dołącz tag manifestu `latest` , który wskazuje najnowszą wersję na wszystkich obsługiwanych platformach.
- Tagi wersji muszą mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi.
- Dołącz tag "Version", taki jak `1.0.1`, który wskazuje określoną wersję na wszystkich obsługiwanych platformach.
- Nie należy aktualizować tagów "Version", `1.0.1`takich jak, ponieważ muszą one być niezmienne.

>[!Note]
>Opcjonalnie przechowywanie wersji może obejmować Tagi "wersja stopniowa", takie jak `2.0` i `1.0`. Obsługuje to jednocześnie obsługę wielu głównych wersji.

### <a name="telemetry"></a>Telemetria

Moduły korzystające z zestawu SDK modułu IoT muszą ustawić unikatowy identyfikator modułu `PublisherId.OfferId.SkuId` na potrzeby telemetrii. Unikatowy identyfikator umożliwia korzystanie z portalu Azure Marketplace w celu zidentyfikowania liczby wystąpień modułów, które są uruchomione.

 Użyj następujących metod z zestawów SDK modułu IoT, aby ustawić `ProductInfo` dla tego identyfikatora:

- [S\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [S](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

W przypadku modułów, które nie używają zestawu SDK modułu IoT, mniej dokładne szczegółowe informacje są dostępne za pomocą portal Cloud Partner takich jak Liczba pobrań.

### <a name="security"></a>Zabezpieczenia

Moduły IoT Edge muszą od możliwie najmniejszych uprawnień uzyskać dostęp do hosta. Należy unikać [uprzywilejowanych modułów](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) .

### <a name="module-iot-sdk"></a>Zestaw SDK modułu IoT

Uwzględnienie zestawu SDK modułu IoT nie jest wymaganiem wstępnym certyfikacji. Jednak w tym zestaw SDK modułu IoT może zapewnić lepszy komfort pracy użytkowników. Na przykład w celu obsługi routingu lub wysyłania komunikatów do chmury.

Zestaw SDK modułu IoT jest wymagany do pobrania danych telemetrycznych dotyczących liczby uruchomionych wystąpień modułu.


## <a name="recertification-process"></a>Proces recertyfikacji

<!-- Add legal time windows-->
Partnerzy otrzymają powiadomienie, gdy istnieje istotna zmiana wpływająca na moduły, na przykład:

- Macierz obsługi systemu operacyjnego warstwy 1/Arch obsługiwana przez IoT Edge
- Zestaw SDK modułu IoT
- Środowisko uruchomieniowe IoT Edge
- Wytyczne dotyczące certyfikacji modułu IoT Edge

Partnerzy będą musieli zaktualizować swoje moduły i zatwierdzili je za pomocą narzędzia portal Cloud Partner.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Hostowanie modułu IoT Edge w Azure Container Registry

Aby przekazać moduł IoT Edge do portal Cloud Partner, musisz najpierw hostować go w [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Moduł musi zawierać wszystkie Tagi, które mają zostać opublikowane, w tym Tagi obrazu, do których odwołuje się tag manifestu.


## <a name="next-steps"></a>Następne kroki

- [Tworzenie oferty modułu IoT Edge](./cpp-create-offer.md)
