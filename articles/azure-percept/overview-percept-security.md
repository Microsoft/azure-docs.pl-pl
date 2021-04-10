---
title: Omówienie zabezpieczeń usługi Azure Percept
description: Dowiedz się więcej o zabezpieczeniach platformy Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567648"
---
# <a name="azure-percept-security-overview"></a>Omówienie zabezpieczeń usługi Azure Percept

Urządzenia Percept platformy Azure są zaprojektowane z użyciem sprzętowego katalogu głównego zaufania. To wbudowane zabezpieczenia pomaga chronić dane wnioskowania i czujniki zależne od prywatności, takie jak aparaty fotograficzne i mikrofony, a ponadto umożliwia uwierzytelnianie i autoryzację urządzeń w usługach Azure Percept Studio.

> [!NOTE]
> Usługa Azure Percept DK ma licencję na korzystanie wyłącznie w środowiskach deweloperskich i testowych.

## <a name="devices"></a>Urządzenia

### <a name="azure-percept-dk"></a>Azure Percept DK

Usługa Azure Percept DK zawiera Trusted Platform Module (TPM) w wersji 2,0, która może zostać wykorzystana do łączenia urządzenia z usługami Azure Device Provisioning Service (DPS) z dodatkowymi zabezpieczeniami. Moduł TPM jest standardem ISO opartym na całej branży, na podstawie Trusted Computing Group. Zapoznaj się z [witryną sieci web Trusted Computing Group](https://trustedcomputinggroup.org/resource/tpm-library-specification/) , aby uzyskać więcej informacji na temat kompletnej specyfikacji modułu TPM 2,0 lub specyfikacji ISO/IEC 11889. Aby uzyskać więcej informacji na temat sposobu, w jaki usługa DPS może bezpiecznie udostępniać urządzenia, zapoznaj się z [zaświadczeniem modułu TPM IoT Hub Device Provisioning Service](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept system-on-modules (SoMs)

Usługa Azure Percept Vision system-on-module (SoM) i model sieci SoM Azure Percept audio obejmują jednostkę mikrokontrolerem (MIKROKONTROLERY) do ochrony dostępu do osadzonych czujników AI. Przy każdym rozruchu oprogramowanie układowe MIKROKONTROLERY uwierzytelnia i autoryzuje akcelerator AI z usługami Azure Percept Studio przy użyciu architektury aparatu kompozycji identyfikatora urządzenia (indeks). KOŚĆ działa przez rozdzielenie rozruchu na warstwy i tworzenie unikatowych wpisów tajnych urządzeń dla każdej warstwy i konfiguracji. Jeśli w dowolnym momencie w łańcuchu zostanie uruchomiony inny kod lub konfiguracja, wpisy tajne będą się różnić. Więcej informacji na temat kości można znaleźć na stronie [specyfikacji grupy roboczej](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Aby skonfigurować dostęp do usługi Azure Percept Studio i wymaganych usług, zobacz artykuł dotyczący [konfigurowania zapór dla platformy Azure PERCEPT DK](concept-security-configuration.md).

Urządzenia usługi Azure Percept używają głównego katalogu sprzętowego zaufania do zabezpieczenia oprogramowania układowego. PAMIĘĆ poddawana rozruchowym zapewnia integralność oprogramowania układowego między pamięcią ROM a programem ładującym systemu operacyjnego, co z kolei zapewnia integralność innych składników oprogramowania, tworząc łańcuch zaufania.

## <a name="services"></a>Usługi

### <a name="iot-edge"></a>IoT Edge

Usługa Azure Percept DK nawiązuje połączenie z usługą Azure Percept Studio z dodatkowymi zabezpieczeniami i innymi usługami platformy Azure wykorzystującymi protokół Transport Layer Security (TLS). Azure Percept DK to urządzenie z obsługą Azure IoT Edge. Środowisko uruchomieniowe IoT Edge to zbiór programów, które włączają urządzenie do urządzenia IoT Edge. Zbiorowo składniki środowiska uruchomieniowego IoT Edge umożliwiają IoT Edge urządzeniom otrzymywanie kodu do uruchomienia na brzegu i przekazywanie wyników. Usługa Azure Percept DK używa kontenerów platformy Docker do izolowania obciążeń IoT Edge od systemu operacyjnego hosta i aplikacji z obsługą krawędzi. Aby uzyskać więcej informacji na temat środowiska zabezpieczeń Azure IoT Edge, Przeczytaj o programie [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Aktualizacja urządzenia dla IoT Hub

Aktualizacja urządzenia dla IoT Hub umożliwia bezpieczniejsze, skalowalne i niezawodne aktualizacje w trybie failover, które zapewniają odnawialne zabezpieczenia dla urządzeń Percept platformy Azure. Zapewnia ona rozbudowane formanty zarządzania i aktualizuje zgodność poprzez szczegółowe informacje. Usługa Azure Percept DK obejmuje wstępnie zintegrowane rozwiązanie do aktualizacji urządzeń, które zapewnia odporną aktualizację (A/B) od oprogramowania układowego do warstw systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o konfiguracjach zapory i zaleceniach dotyczących zabezpieczeń](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Kupowanie usługi Azure Percept DK ze sklepu Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
