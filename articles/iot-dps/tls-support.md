---
title: Obsługa protokołu TLS w usłudze Azure IoT Device Provisioning Service (DPS)
description: Najlepsze rozwiązania w zakresie używania bezpiecznych połączeń TLS dla urządzeń i usług komunikujących się z usługą Device Provisioning Service (DPS) usługi IoT
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984852"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Obsługa protokołu TLS w usłudze Azure IoT Hub Device Provisioning Service (DPS)

Usługa DPS używa Transport Layer Security (TLS) do zabezpieczania połączeń z urządzeń IoT. Obecnie są obsługiwane trzy wersje protokołu TLS, czyli wersje 1,0, 1,1 i 1,2.

Protokoły TLS 1,0 i 1,1 są uznawane za starsze i są planowane jako przestarzałe. Aby uzyskać więcej informacji, zobacz [przestarzałe protokoły TLS 1,0 i 1,1 dla IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). Zdecydowanie zaleca się użycie protokołu TLS 1,2 jako preferowanej wersji protokołu TLS podczas nawiązywania połączenia z usługą DPS.

## <a name="restrict-connections-to-tls-12"></a>Ogranicz połączenia do protokołu TLS 1,2

W celu zwiększenia bezpieczeństwa zaleca się skonfigurowanie wystąpień usługi DPS w taki sposób, *aby zezwalały na połączenia z klientami* urządzeń korzystające z protokołu TLS w wersji 1,2 i wymuszanie użycia [zalecanych szyfrów](#recommended-ciphers).

W tym celu należy zastanowić się nad nowym zasobem DPS w dowolnym z [obsługiwanych regionów](#supported-regions) i `minTlsVersion` ustawić `1.2` właściwość na wartość w specyfikacji zasobu "DPS" szablonu Azure Resource Manager. Poniższy przykładowy szablon JSON określa `minTlsVersion` właściwość nowego wystąpienia usługi DPS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
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
> `minTlsVersion` Właściwość jest tylko do odczytu i nie można jej zmienić po utworzeniu zasobu DPS. W związku z tym ważne jest, aby prawidłowo testować i sprawdzać, czy *wszystkie* urządzenia IoT są zgodne z protokołem TLS 1,2 i [zalecanymi szyframi](#recommended-ciphers) z wyprzedzeniem.

## <a name="supported-regions"></a>Obsługiwane regiony

Wystąpienia IoT DPS wymagające użycia protokołu TLS 1,2 można utworzyć w następujących regionach:

* US Gov Arizona
* US Gov Wirginia

> [!NOTE]
> Po przełączeniu w `minTlsVersion` tryb failover Właściwość punktu dystrybucji pozostanie obowiązująca w przypadku przełączenia w tryb failover z parowaniem geograficznym.

## <a name="recommended-ciphers"></a>Zalecane szyfry

Wystąpienia DPS skonfigurowane do akceptowania tylko protokołu TLS 1,2 również wymuszać użycie następujących zalecanych szyfrów:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Korzystanie z protokołu TLS 1,2 w zestawach SDK IoT

Poniższe linki służą do konfigurowania protokołu TLS 1,2 i dozwolonych szyfrów w zestawach SDK klienta usługi Azure IoT.

| Język | Wersje obsługujące protokół TLS 1,2 | Dokumentacja |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 lub nowszy            | [Łącze](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Wersja 2.0.0 lub nowsza             | [Łącze](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Wersja 1.21.4 lub nowsza            | [Łącze](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Wersja 1.19.0 lub nowsza            | [Łącze](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Wersja 1.12.2 lub nowsza            | [Łącze](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>Użyj protokołu TLS 1,2 z IoT Edge

Urządzenia IoT Edge można skonfigurować do korzystania z protokołu TLS 1,2 podczas komunikowania się z usługą IoT Hub i DPS. W tym celu Skorzystaj ze [strony dokumentacji IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).