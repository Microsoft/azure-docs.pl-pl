---
title: Przygotowywanie zasobów technicznych modułu Usługi IoT Edge — Azure Marketplace
description: Dowiedz się więcej o wymaganiach technicznych i konfiguracyjnych, które muszą spełniać zasoby techniczne modułu usługi Internet of Things (IoT) przed opublikowaniem ich w portalu Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730697"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Przygotowanie zasobów technicznych modułu IoT Edge

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami modułu IoT Edge z portalu Cloud Partner do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [aplikacji Przygotowanie zasobów technicznych modułu Usługi IoT edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) dla portalu Cloud Partner Portal, aby zarządzać ofertami.

W tym artykule opisano wymagania, które muszą spełniać zasoby techniczne modułu usługi Internet of Things (IoT) przed opublikowaniem w portalu Azure Marketplace.

## <a name="get-started"></a>Rozpoczęcie pracy

Moduł usługi IoT Edge to kontener zgodny z programem Docker, który działa na urządzeniu usługi IoT Edge.

- Aby dowiedzieć się więcej o modułach usługi IoT Edge, zobacz [Opis modułów usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Aby rozpocząć pracę z rozwojem modułów IoT Edge, zobacz [Tworzenie własnych modułów IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Wymagania techniczne

Moduł usługi IoT Edge musi spełniać następujące wymagania techniczne, aby uzyskać certyfikat i opublikowanie go w portalu Azure Marketplace.

### <a name="platform-support"></a>Obsługa platform

Moduł IoT Edge musi obsługiwać jedną z następujących opcji platformy:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Platformy poziomu 1 obsługiwane przez IoT Edge

Moduł musi obsługiwać wszystkie platformy warstwy 1 obsługiwane przez usługę IoT Edge (zarejestrowaną w [usłudze Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Zalecamy tę opcję, ponieważ zapewnia lepszą obsługę klienta. Zaprezentowane zostaną moduły spełniające te kryteria. Moduł korzystający z tej opcji platformy musi:

- Podaj najnowszy tag i tag wersji (na przykład 1.0.1), które są znacznikami manifestu utworzonymi za pomocą [narzędzia Manifestu GitHub](https://github.com/estesp/manifest-tool).

- Karta Lista ofert w [Centrum partnerów](https://partner.microsoft.com/dashboard/commercial-marketplace) służy do dodawania łącza w sekcji **Przydatne łącza** do katalogu urządzeń [z certyfikatem usługi Azure IoT Edge](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Podzbiór platform warstwy 1 obsługiwanych przez IoT Edge

Moduł musi obsługiwać podzbiór (co najmniej jeden) platform warstwy 1 obsługiwanych przez usługę IoT Edge (zgodnie z zapisem w [usłudze Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Moduł korzystający z tej opcji platformy musi:

- Podaj najnowszy tag i tag wersji (na przykład 1.0.1), które są znacznikami manifestu utworzonymi za pomocą [narzędzia manifestu](https://github.com/estesp/manifest-tool) GitHub, jeśli obsługiwana jest więcej niż jedna platforma. Znaczniki manifestu są opcjonalne tylko wtedy, gdy obsługiwana jest jedna platforma.
- Karta Lista ofert w [Centrum partnerów](https://partner.microsoft.com/dashboard/commercial-marketplace) służy do dodawania łącza w sekcji **Przydatne łącza** do co najmniej jednego urządzenia usługi IoT Edge z katalogu urządzeń [z certyfikatem usługi Azure IoT Edge](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Jest to obraz sekcji Aukcja ofert w Centrum partnerów":::

### <a name="device-dimensions"></a>Wymiary urządzenia

Wymiary modułu usługi IoT Edge (takie jak procesor, pamięć RAM, pamięć masowa i procesor gpu) na docelowych urządzeniach usługi IoT Edge muszą spełniać następujące wymagania:

- Moduł musi współpracować z co najmniej jednym urządzeniem usługi IoT Edge z [katalogu urządzeń z certyfikatem usługi Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

- Minimalne wymagania sprzętowe muszą być udokumentowane jako ostatni akapit w opisie oferty (w zakładce lista ofert w [Centrum partnerów).](https://partner.microsoft.com/dashboard/commercial-marketplace) Opcjonalnie można również wyświetlić listę zalecanych wymagań sprzętowych, jeśli znacznie się różnią. Na przykład dodaj następującą sekcję na końcu opisu oferty:

Skopiuj ten tekst HTML lub użyj odpowiednich funkcji tekstu sformatującego w oknie edycji.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfigurowanie

Moduł musi zawierać domyślne ustawienia konfiguracji, aby wdrożenie na urządzeniu usługi IoT Edge było tak proste, jak to możliwe. Informacje te można podać na stronie **Konfiguracji technicznej** planu w [Centrum partnerów](https://partner.microsoft.com/dashboard/commercial-marketplace). Kontener może również zawierać SDK modułu usługi IoT Edge, aby umożliwić komunikację z koncentratorem brzegowym i centrum IoT Hub.

#### <a name="default-configuration"></a>Konfiguracja domyślna

Moduły usługi IoT Edge muszą być w stanie rozpocząć od ustawień domyślnych podanych na stronie **Konfiguracja techniczna** dla planu w [Centrum partnerów](https://partner.microsoft.com/dashboard/commercial-marketplace). Dostępne są następujące ustawienia domyślne:

- **Trasy domyślne**
- Domyślne **właściwości modułu bliźniaczej żądane**
- Domyślne **zmienne środowiskowe**
- Domyślne **opcje tworzenia kontenera**

W scenariuszu, w którym parametr, który jest wymagany dla wartości domyślnej nie ma sensu (na przykład adres IP serwera klienta), dodaj parametr jako wartość domyślną. Ta wartość jest wielką literą i ujęta w nawiasy. W tym przykładzie należy skonfigurować następującą domyślną zmienną środowiskową:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentacja konfiguracjna

Wszystkie ustawienia konfiguracyjne modułu usługi IoT Edge muszą być wyraźnie udokumentowane. Na przykład należy udokumentować sposób używania jego tras, bliźniaczej reprezentacji żądane właściwości, zmiennych środowiskowych, createOptions i tak dalej. Musisz podać link do dokumentacji lub uczynić ją częścią swojej oferty lub opisu planu. Informacje te można podać na stronie **Aukcja oferty** i **Lista planu** w [Centrum partnerów](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Tagi i przechowywanie wersji

Klienci muszą mieć możliwość łatwego wdrażania modułu i automatycznego ujmowanie aktualizacji z portalu Marketplace (w scenariuszu dla deweloperów). Muszą również mieć możliwość użycia i zamrożenia dokładnej wersji, którą przetestowali (w scenariuszu produkcyjnym).

Aby spełnić te oczekiwania klientów i zostać opublikowane na rynku, moduły IoT Edge muszą spełniać następujące wymagania

- Dołącz manifest najnowszy tag, który wskazuje na najnowszą wersję na wszystkich obsługiwanych platformach.
- Tworzenie tagów wersji w postaci X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi.
- Dołącz tag "version", taki jak 1.0.1, który wskazuje określoną wersję na wszystkich obsługiwanych platformach.
- Nie aktualizuj tagów "version", takich jak 1.0.1, ponieważ nie wolno ich zmieniać.

> [!NOTE]
> Opcjonalnie przechowywanie wersji może zawierać tagi "wersja stopniowa", takie jak 2.0 i 1.0. Obsługuje to utrzymanie wielu głównych wersji równolegle.

### <a name="telemetry"></a>Telemetria

Moduły przy użyciu zestawu SDK modułu IoT muszą ustawić unikatowy identyfikator modułu na PublisherId.OfferId.SkuId dla celów telemetrycznych. Unikatowy identyfikator pomaga w usłudze Azure Marketplace zidentyfikować liczbę uruchomionych wystąpień modułu.

Użyj jednej z następujących metod z zestawu SDK modułu IoT, aby ustawić ProductInfo na ten identyfikator:

- [C #](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

W przypadku modułów, które nie używają zestawu SDK modułu IoT, mniej precyzyjne informacje są dostępne za pośrednictwem Centrum partnerów, takie jak liczba pobrań.

### <a name="security"></a>Zabezpieczenia

Moduły IoT Edge muszą unikać [modułów uprzywilejowanych.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) Zamiast tego poprosić o najmniej uprzywilejowany dostęp do hosta, jak to możliwe.

### <a name="module-iot-sdk"></a>Moduł IoT SDK

Włączenie SDK modułu IoT nie jest warunkiem wstępnym certyfikacji. Jednak w tym moduł IoT SDK moduł może zapewnić lepsze środowisko użytkownika. Na przykład do obsługi routingu lub wysyłania wiadomości do chmury.

Zestaw SDK modułu IoT jest wymagany do uzyskania danych telemetrycznych dotyczących liczby uruchomionych wystąpień modułu.

## <a name="recertification-process"></a>Proces ponownej certyfikacji

Partnerzy są powiadamiani za każdym razem, gdy występuje przełomowa zmiana, która wpływa na ich moduły, takie jak:

- Macierz wsparcia systemu operacyjnego/łuku warstwy 1 obsługiwana przez ioT Edge
- SDK modułu IoT
- Środowisko uruchomieniowe usługi IoT Edge
- Wskazówki dotyczące certyfikacji modułów IoT Edge

Partnerzy muszą zaktualizować i ponownie certyfikować swoje oferty, publikując je ponownie w [Centrum partnerów.](https://partner.microsoft.com/dashboard/commercial-marketplace)

Oferta zostanie również ponownie certyfikowana, jeśli ją zaktualizujesz, na przykład dodając nowe znaczniki obrazu.

## <a name="host-module-in-azure-container-registry"></a>Moduł hosta w rejestrze kontenerów platformy Azure

Aby przekazać moduł usługi IoT Edge do portalu Azure Marketplace, musisz najpierw hostować go w [rejestrze kontenerów platformy Azure](https://azure.microsoft.com/services/container-registry/) (ACR). Moduł musi zawierać wszystkie znaczniki, które chcesz opublikować, w tym znaczniki obrazu, do których odwołuje się znacznik manifestu. Aby uzyskać więcej informacji, zobacz samouczek [Tworzenie rejestru kontenerów platformy Azure i wypychanie obrazu kontenera](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie oferty modułu usługi IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)