---
title: Obsługa protokołu TLS w usłudze Azure IoT Hub
description: Najlepsze rozwiązania w zakresie używania bezpiecznych połączeń TLS dla urządzeń i usług komunikujących się z IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jlian
ms.openlocfilehash: 08ecb766a1a9bd7ff75bf97647be811577212eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006044"
---
# <a name="tls-support-in-iot-hub"></a>Obsługa protokołu TLS w IoT Hub

IoT Hub używa Transport Layer Security (TLS) do zabezpieczania połączeń z urządzeń i usług IoT. Obecnie są obsługiwane trzy wersje protokołu TLS, czyli wersje 1,0, 1,1 i 1,2.

Protokoły TLS 1,0 i 1,1 są uznawane za starsze i są planowane jako przestarzałe. Aby uzyskać więcej informacji, zobacz [przestarzałe protokoły TLS 1,0 i 1,1 dla IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Zdecydowanie zaleca się użycie protokołu TLS 1,2 jako preferowanej wersji protokołu TLS podczas nawiązywania połączenia z usługą IoT Hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Wymuszanie protokołu TLS 1,2 dostępne w wybranych regionach

Aby zwiększyć bezpieczeństwo, należy skonfigurować centra IoT w *taki sposób,* aby zezwalały na połączenia klienckie korzystające z protokołu TLS w wersji 1,2 i wymuszać korzystanie z [mechanizmów szyfrowania](#cipher-suites). Ta funkcja jest obsługiwana tylko w następujących regionach:

* East US
* South Central US
* Zachodnie stany USA 2
* US Gov Arizona
* US Gov Wirginia

W tym celu należy udostępnić nowe IoT Hub w którymkolwiek z obsługiwanych regionów i ustawić `minTlsVersion` Właściwość na wartość `1.2` w specyfikacji zasobów usługi IoT Hub szablonu Azure Resource Manager:

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

Utworzony zasób IoT Hub przy użyciu tej konfiguracji będzie odrzucać klientów urządzeń i usług próbujących nawiązać połączenie przy użyciu protokołu TLS w wersji 1,0 i 1,1. Podobnie uzgadnianie protokołu TLS zostanie odrzucone, jeśli `ClientHello` komunikat nie zawiera żadnych [zalecanych szyfrów](#cipher-suites).

> [!NOTE]
> `minTlsVersion`Właściwość jest tylko do odczytu i nie można jej zmienić po utworzeniu zasobu IoT Hub. W związku z tym należy prawidłowo testować i sprawdzać, czy *wszystkie* urządzenia i usługi IoT są zgodne z protokołem TLS 1,2 i [zalecanymi szyframi](#cipher-suites) z wyprzedzeniem.
> 
> Po przełączeniu w tryb failover `minTlsVersion` właściwość IoT Hub będzie obowiązywać w regionie z sparowanym przełączeniem w tryb failover.

## <a name="cipher-suites"></a>Mechanizmy szyfrowania

Centra IoT skonfigurowane do akceptowania tylko protokołu TLS 1,2 również wymuszą użycie następujących zalecanych mechanizmów szyfrowania:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

W przypadku centrów IoT nieskonfigurowanych do wymuszania protokołu TLS 1,2 protokół TLS 1,2 nadal działa z następującymi mechanizmami szyfrowania:

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

Klient programu może zasugerować listę wyższych mechanizmów szyfrowania do użycia podczas działania `ClientHello` . Niektóre z nich mogą jednak nie być obsługiwane przez IoT Hub (na przykład `ECDHE-ECDSA-AES256-GCM-SHA384` ). W takim przypadku IoT Hub będzie podejmować próby przestrzegania preferencji klienta, ale ostatecznie negocjuje mechanizm szyfrowania z `ServerHello` .

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

## <a name="device-authentication"></a>Uwierzytelnianie urządzeń

Po pomyślnym uzgadnianiu protokołu TLS IoT Hub można uwierzytelnić urządzenie przy użyciu klucza symetrycznego lub certyfikatu X. 509. W przypadku uwierzytelniania opartego na certyfikatach może to być dowolny certyfikat X. 509, w tym ECC. IoT Hub sprawdza poprawność certyfikatu do podania odcisku palca lub urzędu certyfikacji (CA). IoT Hub nie obsługuje jeszcze wzajemnego uwierzytelniania opartego na protokole X. 509 (mTLS). Aby dowiedzieć się więcej, zobacz [obsługiwane certyfikaty X. 509](iot-hub-devguide-security.md#supported-x509-certificates).
