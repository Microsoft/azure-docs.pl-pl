---
title: Obsługa protokołu TLS w usłudze Azure IoT Hub
description: Najlepsze rozwiązania w zakresie używania bezpiecznych połączeń TLS dla urządzeń i usług komunikujących się z IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 5b9f6b993b0d0f527d041b4ee055bf51fefa1253
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848249"
---
# <a name="tls-support-in-iot-hub"></a>Obsługa protokołu TLS w IoT Hub

IoT Hub używa Transport Layer Security (TLS) do zabezpieczania połączeń z urządzeń i usług IoT. Obecnie są obsługiwane trzy wersje protokołu TLS, czyli wersje 1,0, 1,1 i 1,2.

Protokoły TLS 1,0 i 1,1 są uznawane za starsze i są planowane jako przestarzałe. Aby uzyskać więcej informacji, zobacz [przestarzałe protokoły TLS 1,0 i 1,1 dla IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Zdecydowanie zaleca się użycie protokołu TLS 1,2 jako preferowanej wersji protokołu TLS podczas nawiązywania połączenia z usługą IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Ogranicz połączenia do protokołu TLS 1,2 w zasobie IoT Hub

W celu zwiększenia bezpieczeństwa zaleca się skonfigurowanie centrów IoT w *taki sposób,* aby zezwalały na połączenia klienckie korzystające z protokołu TLS w wersji 1,2 i wymuszanie użycia [zalecanych szyfrów](#recommended-ciphers).

W tym celu należy udostępnić nowe IoT Hub w którymkolwiek z [obsługiwanych regionów](#supported-regions) i ustawić `minTlsVersion` Właściwość na wartość `1.2` w specyfikacji zasobów usługi IoT Hub szablonu Azure Resource Manager:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Utworzony zasób IoT Hub przy użyciu tej konfiguracji będzie odrzucać klientów urządzeń i usług próbujących nawiązać połączenie przy użyciu protokołu TLS w wersji 1,0 i 1,1. Podobnie uzgadnianie protokołu TLS zostanie odrzucone, jeśli wiadomość POWITALNa klienta nie zawiera listy [zalecanych szyfrów](#recommended-ciphers).

> [!NOTE]
> `minTlsVersion`Właściwość jest tylko do odczytu i nie można jej zmienić po utworzeniu zasobu IoT Hub. W związku z tym należy prawidłowo testować i sprawdzać, czy *wszystkie* urządzenia i usługi IoT są zgodne z protokołem TLS 1,2 i [zalecanymi szyframi](#recommended-ciphers) z wyprzedzeniem.

## <a name="supported-regions"></a>Obsługiwane regiony

Centra IoT, które wymagają użycia protokołu TLS 1,2, można tworzyć w następujących regionach:

* Wschodnie stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA 2
* US Gov Arizona
* US Gov Wirginia

> [!NOTE]
> Po przełączeniu w tryb failover `minTlsVersion` właściwość IoT Hub będzie obowiązywać w regionie z sparowanym przełączeniem w tryb failover.

## <a name="recommended-ciphers"></a>Zalecane szyfry

Centra IoT, które są skonfigurowane do akceptowania tylko protokołu TLS 1,2, będą również wymuszać użycie następujących zalecanych szyfrów:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

W przypadku centrów IoT nieskonfigurowanych do wymuszania protokołu TLS 1,2 protokół TLS 1,2 nadal działa z następującymi szyframi:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Korzystanie z protokołu TLS 1,2 w zestawach SDK IoT Hub

Poniższe linki służą do konfigurowania protokołu TLS 1,2 i dozwolonych szyfrów w IoT Hub zestaw SDK klienta.

| Język | Wersje obsługujące protokół TLS 1,2 | Dokumentacja |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 lub nowszy            | [Powiązań](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Wersja 2.0.0 lub nowsza             | [Powiązań](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Wersja 1.21.4 lub nowsza            | [Powiązań](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Wersja 1.19.0 lub nowsza            | [Powiązań](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Wersja 1.12.2 lub nowsza            | [Powiązań](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Korzystanie z protokołu TLS 1,2 w konfiguracji IoT Edge

Urządzenia IoT Edge można skonfigurować do korzystania z protokołu TLS 1,2 podczas komunikowania się z IoT Hub. W tym celu Skorzystaj ze [strony dokumentacji IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).