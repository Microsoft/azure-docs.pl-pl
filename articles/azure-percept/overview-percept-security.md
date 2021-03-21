---
title: Omówienie zabezpieczeń usługi Azure Percept
description: Dowiedz się więcej o zabezpieczeniach platformy Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 6a3049709c6c094f722a8132ee4c4b2051e24d95
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616693"
---
# <a name="azure-percept-security-overview"></a>Omówienie zabezpieczeń usługi Azure Percept

Urządzenia z systemem Azure Percept DK są zaprojektowane z użyciem sprzętowego katalogu głównego zaufania: dodatkowe wbudowane zabezpieczenia na każdym urządzeniu. Pomaga chronić czujniki zależne od prywatności, takie jak kamery i Mikrotelefony, dane wnioskowania oraz umożliwia uwierzytelnianie i autoryzację urządzeń dla usług Azure Percept Studio.

> [!NOTE]
> Usługa Azure Percept DK ma licencję na korzystanie wyłącznie w środowiskach deweloperskich i testowych.

## <a name="devices"></a>Urządzenia

### <a name="azure-percept-dk"></a>Azure Percept DK

Usługa Azure Percept DK zawiera Trusted Platform Module (TPM) w wersji 2,0, która może być używana do łączenia urządzenia z usługami Azure Device Provisioning z dodatkowymi zabezpieczeniami. Moduł TPM jest standardem ISO opartym na całej branży, a na podstawie Trusted Computing Group można dowiedzieć się więcej na temat modułu TPM i uzyskać [pełną specyfikację modułu tpm 2,0](https://trustedcomputinggroup.org/resource/tpm-library-specification/) lub specyfikację ISO/IEC 11889. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa DPS może bezpiecznie udostępnić urządzenia, zobacz [Azure IoT Hub Device Provisioning Service — zaświadczanie modułu TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>System Azure Percept w module (SOM)

Usługa Azure Percept DK z obsługą wizji z możliwością przetwarzania w module (SOM) i modelem Microsoft Azure Percept audio accessor umożliwiają ochronę dostępu do osadzonych czujników AI. Przy każdym rozruchu oprogramowanie układowe MIKROKONTROLERY uwierzytelnia i autoryzuje akcelerator AI z usługami Azure Percept Studio przy użyciu architektury aparatu kompozycji identyfikatora urządzenia (indeks). KOŚĆ działa, dzieląc rozruch na warstwy i tworząc klucze tajne unikatowe dla każdej warstwy i konfiguracji w oparciu o unikatowy klucz tajny urządzenia. Jeśli w dowolnym momencie w łańcuchu zostanie uruchomiony inny kod lub konfiguracja, wpisy tajne będą się różnić. Więcej informacji na temat kości można znaleźć na stronie [specyfikacji grupy roboczej](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Aby skonfigurować dostęp do usługi Azure Percept Studio i wymaganych usług, zobacz **Konfigurowanie zapór na platformie Azure PERCEPT DK** poniżej.

Urządzenia usługi Azure Percept używają głównego zaufania sprzętowego do zabezpieczenia oprogramowania układowego. PAMIĘĆ poddawana rozruchowym zapewnia integralność oprogramowania układowego między pamięcią ROM a programem ładującym systemu operacyjnego, co z kolei gwarantuje integralność innych składników oprogramowania tworzących łańcuch zaufania.

## <a name="services"></a>Usługi

### <a name="iot-edge"></a>IoT Edge

Usługa Azure Percept DK nawiązuje połączenie z usługą Azure Percept Studio z dodatkowymi zabezpieczeniami i innymi usługami platformy Azure wykorzystującymi protokół Transport Layer Security (TLS). Azure Percept DK to urządzenie z włączonym Azure IoT Edge. Środowisko uruchomieniowe IoT Edge to zbiór programów, które włączają urządzenie do urządzenia IoT Edge. Zbiorowo składniki środowiska uruchomieniowego IoT Edge umożliwiają IoT Edge urządzeniom otrzymywanie kodu do uruchomienia na brzegu i przekazywanie wyników. Usługa Azure Percept DK używa kontenerów platformy Docker do izolowania obciążeń IoT Edge z systemu operacyjnego hosta i aplikacji z włączonymi krawędziami. Aby uzyskać więcej informacji na temat środowiska zabezpieczeń Azure IoT Edge, Przeczytaj o programie [IoT Edge Security Manager](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager).

### <a name="device-update-for-iot-hub"></a>Aktualizacja urządzenia dla IoT Hub

Aktualizacja urządzenia dla IoT Hub umożliwia bezpieczniejsze, skalowalne i niezawodne aktualizacje w trybie failover, które zapewniają odnawialne zabezpieczenia dla urządzeń Percept platformy Azure. Zapewnia ona rozbudowane formanty zarządzania i aktualizuje zgodność poprzez szczegółowe informacje. Usługa Azure Percept DK obejmuje wstępnie zintegrowane rozwiązanie do aktualizacji urządzeń, które zapewnia odporną aktualizację (A/B) od oprogramowania układowego do warstw systemu operacyjnego.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Konfigurowanie zapór dla platformy Azure Percept DK

Jeśli konfiguracja sieci wymaga jawnie zezwolenia na połączenia realizowane z urządzeń z systemem Azure Percept DK, przejrzyj poniższą listę składników.

Ta lista kontrolna jest punktem początkowym dla reguł zapory:

|Adres URL (* = symbol wieloznaczny) |Wychodzące porty TCP|    Użycie|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Azure DK — uwierzytelnianie i autoryzacja modelu SOM|
|*. auth.projectsantacruz.azure.net| 443|    Azure DK — uwierzytelnianie i autoryzacja modelu SOM|

Ponadto Przejrzyj listę [połączeń używanych przez Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

<!---
## Additional Recommendations for Deployment to Production

Azure Percept DK offers a great variety of security capabilities out of the box. In addition to those powerful security features included in the current release, Microsoft also suggests the following guidelines when considering production deployments:

- Strong physical protection of the device itself
- Ensuring data at rest encryption is enabled
- Continuously monitoring the device posture and quickly responding to alerts
- Limiting the number of administrators who have access to the device
--->


## <a name="next-steps"></a>Następne kroki

Poznaj dostępne [modele usługi Azure PERCEPT AI](./overview-ai-models.md).
