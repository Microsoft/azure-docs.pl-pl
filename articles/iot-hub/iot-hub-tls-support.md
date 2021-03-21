---
title: Obsługa protokołu TLS w usłudze Azure IoT Hub
description: Dowiedz się więcej o korzystaniu z połączeń Secure TLS dla urządzeń i usług komunikujących się z IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: d36a7917693aef9063ade473759f2f451d3a677f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234022"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Obsługa Transport Layer Security (TLS) w programie IoT Hub

IoT Hub używa Transport Layer Security (TLS) do zabezpieczania połączeń z urządzeń i usług IoT. Obecnie są obsługiwane trzy wersje protokołu TLS, czyli wersje 1,0, 1,1 i 1,2.

Protokoły TLS 1,0 i 1,1 są uznawane za starsze i są planowane jako przestarzałe. Aby uzyskać więcej informacji, zobacz [przestarzałe protokoły TLS 1,0 i 1,1 dla IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Aby uniknąć przyszłych problemów, Użyj protokołu TLS 1,2 jako jedynej wersji protokołu TLS podczas nawiązywania połączenia z usługą IoT Hub.

## <a name="iot-hubs-server-tls-certificate"></a>Certyfikat protokołu TLS IoT Hub serwera

Podczas uzgadniania protokołu TLS IoT Hub przedstawia certyfikaty serwera z kluczowym kluczem RSA do łączenia klientów. Jego katalog główny to główny urząd certyfikacji Baltimore CyberTrust. Ostatnio Wprowadziliśmy zmianę certyfikatu serwera TLS, dzięki czemu zostanie on wystawiony przez nowe pośrednich urzędów certyfikacji (ICA). Aby uzyskać więcej informacji, zobacz [IoT Hub aktualizacji certyfikatu TLS](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/).

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Certyfikat TLS serwera kryptografii krzywej eliptycznej (ECC) (wersja zapoznawcza)

Certyfikat TLS serwera IoT Hub ECC jest dostępny do publicznej wersji zapoznawczej. Chociaż oferują podobne zabezpieczenia dla certyfikatów RSA, sprawdzanie poprawności certyfikatu ECC (za pomocą mechanizmów szyfrowania obsługujących tylko ECC) używa do 40% mniej obliczeniowych, pamięci i przepustowości. Te oszczędności są ważne w przypadku urządzeń IoT ze względu na ich mniejsze profile i pamięć oraz obsługują przypadki użycia w środowiskach ograniczonych przepustowością sieci. 

Aby wyświetlić podgląd certyfikatu serwera ECC IoT Hub:

1. [Utwórz nowe centrum IoT Hub z włączonym trybem wersji zapoznawczej](iot-hub-preview-mode.md).
1. [Skonfiguruj klienta tak](#tls-configuration-for-sdk-and-iot-edge) , aby obejmował *tylko* mechanizmy szyfrowania ECDSA i *wykluczyć* wszystkie RSA. Są to obsługiwane mechanizmy szyfrowania dla publicznej wersji zapoznawczej certyfikatu ECC:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Podłącz klienta do centrum IoT w wersji zapoznawczej.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Wymuszanie protokołu TLS 1,2 dostępne w wybranych regionach

Aby zwiększyć bezpieczeństwo, należy skonfigurować centra IoT w *taki sposób,* aby zezwalały na połączenia klienckie korzystające z protokołu TLS w wersji 1,2 i wymuszać korzystanie z [mechanizmów szyfrowania](#cipher-suites). Ta funkcja jest obsługiwana tylko w następujących regionach:

* East US
* South Central US
* Zachodnie stany USA 2
* US Gov Arizona
* US Gov Wirginia (obsługa protokołu TLS 1.0/1.1 nie jest dostępna w tym regionie — należy włączyć Wymuszanie protokołu TLS 1,2 lub utworzenie centrum IoT nie powiodło się)

Aby włączyć Wymuszanie protokołu TLS 1,2, postępuj zgodnie z instrukcjami w temacie [Tworzenie Centrum IoT Hub w Azure Portal](iot-hub-create-through-portal.md), z wyjątkiem

- Wybierz **region** z jednej z powyższej listy.
- W obszarze **Management-> Advanced-> Transport Layer Security (TLS) — > minimalna wersja protokołu TLS**, wybierz **1,2**. To ustawienie pojawia się tylko w przypadku usługi IoT Hub utworzonej w obsługiwanym regionie.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="Zrzut ekranu przedstawiający sposób włączania protokołu TLS 1,2 podczas tworzenia Centrum IoT":::

Aby użyć szablonu ARM do tworzenia, Zainicjuj obsługę nowego IoT Hub w dowolnym z obsługiwanych regionów i ustaw `minTlsVersion` Właściwość na wartość `1.2` w specyfikacji zasobu:

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

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>Konfiguracja protokołu TLS dla zestawów SDK i IoT Edge

Poniższe linki służą do konfigurowania protokołu TLS 1,2 i dozwolonych szyfrów w IoT Hub zestaw SDK klienta.

| Język | Wersje obsługujące protokół TLS 1,2 | Dokumentacja |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 lub nowszy            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Wersja 2.0.0 lub nowsza             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Wersja 1.21.4 lub nowsza            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Wersja 1.19.0 lub nowsza            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Wersja 1.12.2 lub nowsza            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |

Urządzenia IoT Edge można skonfigurować do korzystania z protokołu TLS 1,2 podczas komunikowania się z IoT Hub. W tym celu Skorzystaj ze [strony dokumentacji IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Uwierzytelnianie urządzeń

Po pomyślnym uzgadnianiu protokołu TLS IoT Hub można uwierzytelnić urządzenie przy użyciu klucza symetrycznego lub certyfikatu X. 509. W przypadku uwierzytelniania opartego na certyfikatach może to być dowolny certyfikat X. 509, w tym ECC. IoT Hub sprawdza poprawność certyfikatu do podania odcisku palca lub urzędu certyfikacji (CA). Aby dowiedzieć się więcej, zobacz [obsługiwane certyfikaty X. 509](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Negocjowanie maksymalnej długości fragmentu TLS (wersja zapoznawcza)

IoT Hub również obsługuje negocjowanie maksymalnej długości fragmentu TLS, która jest czasami znana jako negocjowanie rozmiaru ramki TLS. Ta funkcja jest dostępna w publicznej wersji zapoznawczej. 

Użyj tej funkcji, aby określić maksymalną długość fragmentu w postaci zwykłego tekstu do wartości mniejszej niż domyślna 2 ^ 14 bajtów. Po wynegocjowaniu IoT Hub i rozpoczęciu przez klienta fragmentacji komunikatów, aby upewnić się, że wszystkie fragmenty są mniejsze niż negocjowana długość. To zachowanie jest przydatne w przypadku urządzeń z ograniczeniami obliczeniowymi lub pamięcią. Aby dowiedzieć się więcej, zapoznaj się z [oficjalną specyfikacją protokołu TLS](https://tools.ietf.org/html/rfc6066#section-4).

Oficjalna obsługa zestawu SDK tej publicznej funkcji w wersji zapoznawczej nie jest jeszcze dostępna. Aby rozpocząć

1. [Utwórz nowe centrum IoT Hub z włączonym trybem wersji zapoznawczej](iot-hub-preview-mode.md).
1. W przypadku korzystania z OpenSSL Wywołaj [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) , aby określić rozmiar fragmentu.
1. Podłącz klienta do IoT Hub wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat IoT Hub zabezpieczeń i kontroli dostępu, zobacz [Kontrola dostępu do IoT Hub](iot-hub-devguide-security.md).
- Aby dowiedzieć się więcej o korzystaniu z certyfikatu x509 na potrzeby uwierzytelniania urządzeń, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](iot-hub-x509ca-overview.md)
