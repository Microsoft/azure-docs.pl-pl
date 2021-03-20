---
title: Przygotowywanie zasobów technicznych modułu IoT Edge — Azure Marketplace
description: Dowiedz się więcej o wymaganiach technicznych i konfiguracyjnych, jakie muszą spełnić zasoby techniczne modułu Internet rzeczy (IoT), zanim będzie można je opublikować w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: aca27b89a3b92b410fa560c8b4bd7eb3d4e0a935
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93346778"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Przygotuj zasoby techniczne modułu IoT Edge

W tym artykule opisano wymagania, które muszą spełniać zasoby techniczne modułu brzegowego Internet rzeczy (IoT) przed opublikowaniem w witrynie Azure Marketplace.

## <a name="get-started"></a>Rozpoczęcie pracy

Moduł IoT Edge jest kontenerem zgodnym z platformą Docker, który działa na urządzeniu IoT Edge.

- Aby dowiedzieć się więcej na temat modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](../../iot-edge/iot-edge-modules.md).
- Aby rozpocząć pracę z rozwojem modułu IoT Edge, zobacz [Tworzenie własnych modułów IoT Edge](../../iot-edge/module-development.md).

## <a name="technical-requirements"></a>Wymagania techniczne

Moduł IoT Edge musi spełniać następujące wymagania techniczne, aby można było uzyskać certyfikat i opublikować go w witrynie Azure Marketplace.

### <a name="platform-support"></a>Obsługa platform

Moduł IoT Edge musi obsługiwać jedną z następujących opcji platformy:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy warstwy 1 obsługiwane przez IoT Edge

Moduł musi obsługiwać wszystkie platformy warstwy 1 obsługiwane przez IoT Edge (zgodnie z zarejestrowaniem w [Azure IoT Edge support](../../iot-edge/support.md)). Zalecamy użycie tej opcji, ponieważ oferuje ona lepszy komfort obsługi klienta. Zostaną zaprezentowane moduły spełniające te kryteria. Moduł korzystający z tej opcji platformy musi:

- Podaj najnowszy tag i tag wersji (na przykład 1.0.1), które są tagami manifestu skompilowanymi przy użyciu [narzędzia manifestu usługi GitHub](https://github.com/estesp/manifest-tool).

- Skorzystaj z karty Lista ofert w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace) , aby dodać link w sekcji **przydatne linki** do [wykazu certyfikowanych urządzeń Azure IoT Edge](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podzbiór platform warstwy 1 obsługiwanych przez IoT Edge

Moduł musi obsługiwać podzestaw (co najmniej jeden) platform warstwy 1 obsługiwane przez IoT Edge (zgodnie z zarejestrowaniem w [Azure IoT Edge support](../../iot-edge/support.md)). Moduł korzystający z tej opcji platformy musi:

- Podaj najnowszy tag i tag wersji (na przykład 1.0.1), które są tagami manifestu skompilowanymi przy użyciu [narzędzia manifestu](https://github.com/estesp/manifest-tool) GitHub, jeśli obsługiwana jest więcej niż jedna platforma. Tagi manifestu są opcjonalne tylko wtedy, gdy jedna platforma jest obsługiwana.
- Skorzystaj z karty Lista ofert w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace) , aby dodać link w sekcji **przydatne linki** do co najmniej jednego urządzenia IoT Edge z [katalogu Azure IoT Edge certyfikowane urządzenia](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="To jest obraz sekcji z listą ofert w centrum partnerskim":::

### <a name="device-dimensions"></a>Wymiary urządzenia

Wymiary modułu IoT Edge (takie jak procesor CPU, pamięć RAM, magazyn i procesor GPU) na urządzeniach przeznaczonych IoT Edge muszą spełniać następujące wymagania:

- Moduł musi współpracować z co najmniej jednym urządzeniem IoT Edge z [wykazu Azure IoT Edge certyfikowanych urządzeń](https://catalog.azureiotsolutions.com/).

- Minimalne wymagania sprzętowe muszą być udokumentowane jako ostatni akapit w opisie oferty (na karcie Lista ofert w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace)). Opcjonalnie można również wyświetlić zalecane wymagania sprzętowe, jeśli różnią się one znacznie. Na przykład Dodaj następującą sekcję na końcu opisu oferty:

Skopiuj ten tekst HTML lub użyj odpowiednich funkcji tekstu sformatowanego w oknie edycji.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfigurowanie

Moduł musi zawierać domyślne ustawienia konfiguracji, aby wdrożenie na urządzeniu IoT Edge było możliwie jak najszybciej. Te informacje można znaleźć na stronie **konfiguracja techniczna** planu w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace). Kontener może również zawierać zestaw SDK modułu IoT Edge, aby umożliwić komunikację z centrum brzegowym i IoT Hub.

#### <a name="default-configuration"></a>Konfiguracja domyślna

Moduły IoT Edge muszą mieć możliwość uruchamiania z ustawieniami domyślnymi dostępnymi na stronie **konfiguracji technicznej** planu w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace). Dostępne są następujące ustawienia domyślne:

- **Trasy** domyślne
- Domyślne **Właściwości sznurka modułu**
- Domyślne **zmienne środowiskowe**
- Domyślne **Opcje tworzenia kontenera**

W scenariuszu, w którym parametr, który jest wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), należy dodać parametr jako wartość domyślną. Ta wartość jest wielką literą i ujęta w nawiasy. W tym przykładzie skonfigurowano następującą domyślną zmienną środowiskową:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentacja dotycząca konfiguracji

Wszystkie ustawienia konfiguracji modułu IoT Edge muszą być jasno udokumentowane. Na przykład należy udokumentować sposób korzystania z jego tras, pożądanych właściwości, zmiennych środowiskowych, opcji i tak dalej. Musisz podać link do dokumentacji lub udostępnić go części oferty lub opisu planu. Te informacje można podać na stronie **aukcji oferty** i **planu** w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Znaczniki i przechowywanie wersji

Klienci muszą być w stanie łatwo wdrożyć moduł i automatycznie pobierać aktualizacje z portalu Marketplace (w scenariuszu dla deweloperów). Muszą oni również mieć możliwość użycia i zablokowania dokładnej wersji, które zostały przetestowane (w scenariuszu produkcyjnym).

W celu spełnienia tych oczekiwań klientów i ich opublikowania w portalu Marketplace moduły IoT Edge muszą spełniać następujące wymagania

- Uwzględnij najnowszy tag manifestu, który wskazuje najnowszą wersję na wszystkich obsługiwanych platformach.
- Utwórz Tagi wersji w postaci X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi.
- Dołącz tag "Version", taki jak 1.0.1, który wskazuje określoną wersję na wszystkich obsługiwanych platformach.
- Nie należy aktualizować tagów "Version", takich jak 1.0.1, ponieważ nie można ich zmieniać.

> [!NOTE]
> Opcjonalnie przechowywanie wersji może obejmować Tagi "wersja krocząca", takie jak 2,0 i 1,0. Obsługuje to jednocześnie obsługę wielu głównych wersji.

### <a name="telemetry"></a>Telemetria

Moduły korzystające z zestawu SDK modułu IoT muszą ustawić unikatowy identyfikator modułu na PublisherId. OfferId. identyfikatora skuId na potrzeby telemetrii. Unikatowy identyfikator ułatwia witrynie Azure Marketplace zidentyfikowanie liczby wystąpień modułów, które są uruchomione.

Użyj jednej z następujących metod z zestawów SDK modułu IoT, aby ustawić ProductInfo dla tego identyfikatora:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [S](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

W przypadku modułów, które nie używają zestawu SDK modułu IoT, mniej dokładne szczegółowe informacje są dostępne za pomocą Centrum partnerskiego, na przykład liczby pobrań.

### <a name="security"></a>Zabezpieczenia

Moduły IoT Edge muszą unikać [uprzywilejowanych modułów](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Zamiast tego Zażądaj najmniejszego uprzywilejowanego dostępu do hosta, jak to możliwe.

### <a name="module-iot-sdk"></a>Zestaw SDK modułu IoT

Uwzględnienie zestawu SDK modułu IoT nie jest wymaganiem wstępnym certyfikacji. Jednak w tym zestaw SDK modułu IoT może zapewnić lepszy komfort pracy użytkowników. Na przykład w celu obsługi routingu lub wysyłania komunikatów do chmury.

Zestaw SDK modułu IoT jest wymagany do pobrania danych telemetrycznych dotyczących liczby uruchomionych wystąpień modułu.

## <a name="recertification-process"></a>Proces recertyfikacji

Partnerzy są powiadamiani o każdej istotnej zmianie wpływającej na moduły, takie jak:

- Macierz obsługi systemu operacyjnego warstwy 1/Arch obsługiwana przez IoT Edge
- Zestaw SDK modułu IoT
- Środowisko uruchomieniowe usługi IoT Edge
- Wytyczne dotyczące certyfikacji modułu IoT Edge

Partnerzy muszą zaktualizować i zatwierdzić swoje oferty przez ich ponowne opublikowanie w [centrum partnerskim](https://partner.microsoft.com/dashboard/commercial-marketplace).

Twoja oferta zostanie również zatwierdzona, jeśli ją zaktualizujesz, na przykład dodając nowe znaczniki obrazu.

## <a name="host-module-in-azure-container-registry"></a>Moduł hosta w Azure Container Registry

Aby przekazać moduł IoT Edge do portalu Azure Marketplace, najpierw musisz go hostować w [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Moduł musi zawierać wszystkie Tagi, które mają zostać opublikowane, w tym Tagi obrazu, do których odwołuje się tag manifestu. Aby uzyskać więcej informacji, zobacz samouczek [Tworzenie usługi Azure Container Registry i wypychanie obrazu kontenera](../../container-instances/container-instances-tutorial-prepare-acr.md).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie oferty modułu usługi IoT Edge](azure-iot-edge-module-creation.md)