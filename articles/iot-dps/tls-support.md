---
title: Obsługa protokołu TLS w usłudze Azure IoT Device Provisioning Service (DPS)
description: Najlepsze rozwiązania w zakresie używania bezpiecznych połączeń TLS dla urządzeń i usług komunikujących się z usługą Device Provisioning Service (DPS) usługi IoT
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761298"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Obsługa protokołu TLS w usłudze Azure IoT Hub Device Provisioning Service (DPS)

Usługa DPS używa [Transport Layer Security (TLS)](http://wikipedia.org/wiki/Transport_Layer_Security) do zabezpieczania połączeń z urządzeń IoT. 

Bieżące wersje protokołu TLS obsługiwane przez usługę DPS są następujące: 
* TLS 1.2

Protokoły TLS 1,0 i 1,1 są uznawane za starsze i są planowane jako przestarzałe. Aby uzyskać więcej informacji, zobacz [przestarzałe protokoły TLS 1,0 i 1,1 dla IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>Ogranicz połączenia do protokołu TLS 1,2

W celu zwiększenia bezpieczeństwa zaleca się skonfigurowanie wystąpień usługi DPS w taki sposób, *aby zezwalały na połączenia z klientami* urządzeń korzystające z protokołu TLS w wersji 1,2 i wymuszanie użycia [zalecanych szyfrów](#recommended-ciphers).

W tym celu należy zastanowić się nad nowym zasobem DPS ustawiającym `minTlsVersion` Właściwość na wartość `1.2` w specyfikacji zasobu w usłudze dps szablonu Azure Resource Manager. Poniższy przykładowy szablon JSON określa `minTlsVersion` Właściwość nowego wystąpienia usługi DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Szablon można wdrożyć za pomocą następującego polecenia platformy Azure. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Aby uzyskać więcej informacji na temat tworzenia zasobów usługi DPS przy użyciu szablonów Menedżer zasobów, zobacz temat [Konfigurowanie usługi DPS przy użyciu szablonu Azure Resource Manager](quick-setup-auto-provision-rm.md).

Zasób DPS utworzony przy użyciu tej konfiguracji będzie odrzucać urządzenia, które próbują nawiązać połączenie przy użyciu protokołu TLS w wersji 1,0 i 1,1. Podobnie uzgadnianie protokołu TLS zostanie odrzucone, jeśli wiadomość POWITALNa klienta urządzenia nie zawiera listy [zalecanych szyfrów](#recommended-ciphers).

> [!NOTE]
> `minTlsVersion`Właściwość jest tylko do odczytu i nie można jej zmienić po utworzeniu zasobu DPS. W związku z tym ważne jest, aby prawidłowo testować i sprawdzać, czy *wszystkie* urządzenia IoT są zgodne z protokołem TLS 1,2 i [zalecanymi szyframi](#recommended-ciphers) z wyprzedzeniem.


> [!NOTE]
> Po przełączeniu w tryb failover `minTlsVersion` Właściwość punktu dystrybucji pozostanie obowiązująca w przypadku przełączenia w tryb failover z parowaniem geograficznym.

## <a name="recommended-ciphers"></a>Zalecane szyfry

Wystąpienia DPS skonfigurowane do akceptowania tylko protokołu TLS 1,2 również wymuszać użycie następujących mechanizmów szyfrowania:


| Zalecane mechanizmy szyfrowania TLS 1,2 |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Starsze mechanizmy szyfrowania 

Te mechanizmy szyfrowania są obecnie obsługiwane przez usługę DPS, ale będą amortyzowane. Użyj zalecanych mechanizmów szyfrowania, o ile jest to możliwe.

| Opcja #1 (lepsza ochrona) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Opcja #2 (lepsza wydajność) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>Korzystanie z protokołu TLS 1,2 w zestawach SDK IoT

Poniższe linki służą do konfigurowania protokołu TLS 1,2 i dozwolonych szyfrów w zestawach SDK klienta usługi Azure IoT.

| Język | Wersje obsługujące protokół TLS 1,2 | Dokumentacja |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 lub nowszy            | [Powiązań](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Wersja 2.0.0 lub nowsza             | [Powiązań](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Wersja 1.21.4 lub nowsza            | [Powiązań](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Wersja 1.19.0 lub nowsza            | [Powiązań](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Wersja 1.12.2 lub nowsza            | [Powiązań](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>Użyj protokołu TLS 1,2 z IoT Hub

IoT Hub można skonfigurować do korzystania z protokołu TLS 1,2 podczas komunikowania się z urządzeniami. Aby uzyskać więcej informacji, zobacz [przestarzałe protokoły TLS 1,0 i 1,1 dla IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>Użyj protokołu TLS 1,2 z IoT Edge

Urządzenia IoT Edge można skonfigurować do korzystania z protokołu TLS 1,2 podczas komunikowania się z usługą IoT Hub i DPS. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).
