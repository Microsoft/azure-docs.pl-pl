---
title: Korzystanie z narzędzi Azure IoT Hub Device Provisioning Service SDK
description: Ten dokument zawiera przegląd narzędzi dostępnych w zestawach SDK usługi Azure IoT Hub Device Provisioning Service (DPS) na potrzeby programowania
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 984d38752df93f233c6d87458e3c9ba713696177
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967248"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak korzystać z narzędzi dostępnych w zestawach SDK, aby uprościć programowanie do obsługi administracyjnej
IoT Hub Device Provisioning Service upraszcza proces aprowizacji bez dotknięcia, w bezpieczny i skalowalny [sposób w trybie](about-iot-dps.md#provisioning-process) online.  Zaświadczanie zabezpieczeń w postaci certyfikatu X. 509 lub moduł TPM (TPM) jest wymagane.  Firma Microsoft współpracuje również z [innymi partnerami sprzętu zabezpieczeń](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) w celu poprawy zaufania wdrożenia IoT. Zrozumienie wymagania dotyczącego zabezpieczeń sprzętu może być dla deweloperów bardzo trudne. Dostępne są zestawy SDK usługi Azure IoT Provisioning, dzięki którym deweloperzy mogą korzystać z wygodnej warstwy do pisania klientów, którzy komunikują się z usługą aprowizacji. Zestawy SDK udostępniają również przykłady dla typowych scenariuszy, a także zestaw narzędzi do uproszczenia zaświadczania o zabezpieczeniach w trakcie opracowywania.

## <a name="trusted-platform-module-tpm-simulator"></a>Moduł TPM (TPM) symulator
[Moduł TPM](./concepts-service.md#attestation-mechanism) może odwoływać się do standardu w celu bezpiecznego przechowywania kluczy w celu uwierzytelniania platformy lub może odwoływać się do interfejsu we/wy używanego do korzystania z modułów implementujących Standard. Moduły TPM może istnieć jako sprzęt dyskretny, zintegrowany sprzęt, oprogramowanie układowe lub oprogramowanie.  W środowisku produkcyjnym moduł TPM znajduje się na urządzeniu jako dyskretny sprzęt, zintegrowany sprzęt lub oprogramowanie układowe. W fazie testowania do deweloperów jest dostarczany oprogramowanie symulatora modułu TPM.  Ten symulator jest dostępny tylko do programowania na platformie Windows.

Kroki związane z korzystaniem z symulatora modułu TPM są następujące:
1. [Przygotuj środowisko programistyczne](./quick-enroll-device-x509-java.md) i Sklonuj repozytorium GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Przejdź do folderu symulatora modułu TPM ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` .
3. Uruchom Simulator.exe przed uruchomieniem dowolnej aplikacji klienckiej dla urządzenia aprowizacji.
4. Pozwól, aby symulator działał w tle przez cały proces aprowizacji, aby uzyskać identyfikator rejestracji i klucz poręczenia.  Obie wartości są prawidłowe tylko dla jednego wystąpienia przebiegu.

## <a name="x509-certificate-generator"></a>Generator certyfikatów X. 509
[Certyfikaty X. 509](./concepts-x509-attestation.md#x509-certificates) mogą służyć jako mechanizm zaświadczania do skalowania produkcji i uproszczenia aprowizacji urządzeń.  Istnieje [kilka sposobów](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) uzyskania certyfikatu X. 509:
* W środowisku produkcyjnym zalecamy zakup certyfikatu X. 509 urzędu certyfikacji z publicznego urzędu certyfikacji.
* W środowisku testowym można wygenerować certyfikat główny X. 509 lub łańcuch certyfikatów X. 509 przy użyciu:
    * OpenSSL: można użyć skryptów do generowania certyfikatów:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell lub bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulator aparatu kompozycji tożsamości urządzeń (kość): kość może służyć do identyfikacji urządzeń kryptograficznych i zaświadczania opartego na protokole TLS i certyfikatach klienta X. 509.  [Dowiedz się](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) więcej o tożsamości urządzenia ze kością.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Używanie generatora certyfikatów X. 509 z emulatorem kości
Zestawy SDK udostępniają Generator certyfikatów X. 509 z emulatorem kości, który znajduje się w [zestawie SDK języka Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Ten Generator działa na wiele platform.  Wygenerowany certyfikat może służyć do tworzenia aplikacji w innych językach.

Obecnie, natomiast emulator indeksów wyprowadza certyfikat główny, certyfikat pośredni, certyfikat liścia i skojarzony klucz prywatny.  Certyfikat główny lub certyfikat pośredni nie można jednak użyć do podpisania oddzielnego certyfikatu liścia.  Jeśli zamierzasz testować scenariusz rejestracji grupy, w którym jeden certyfikat podpisywania jest używany do podpisywania certyfikatów liści wielu urządzeń, możesz użyć OpenSSL, aby utworzyć łańcuch certyfikatów.

Aby wygenerować certyfikat X. 509 przy użyciu tego generatora:
1. [Przygotuj środowisko programistyczne](./quick-enroll-device-x509-java.md) i Sklonuj repozytorium GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Zmień katalog główny na Azure-IoT-SDK-Java.
3. Uruchom, ```mvn install -DskipTests=true``` Aby pobrać wszystkie wymagane pakiety i skompilować zestaw SDK
4. Przejdź do katalogu głównego dla generatora certyfikatów X. 509 w temacie ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` .
5. Kompiluj z ```mvn clean install```
6. Uruchom narzędzie przy użyciu następujących poleceń:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Po wyświetleniu monitu można opcjonalnie wprowadzić _nazwę pospolitą_ dla certyfikatów.
8. Narzędzie lokalnie generuje **certyfikat klienta**, **klucz prywatny certyfikatu klienta**, **certyfikat pośredni** i **certyfikat główny**.

Certyfikat **klienta** to element typu liść na urządzeniu.  **Certyfikat klienta** i skojarzony **klucz prywatny certyfikatu klienta** są zbędne w kliencie urządzenia. W zależności od wybranego języka mechanizm, który ma zostać umieszczony w aplikacji klienckiej, może być różny.  Aby uzyskać więcej informacji, zobacz [Przewodniki Szybki Start](./quick-create-simulated-device-x509.md) dotyczący tworzenia symulowanego urządzenia przy użyciu X. 509, aby uzyskać więcej informacji.

Certyfikatu głównego lub pośredniego można użyć do utworzenia grupy rejestracji lub rejestracji indywidualnej w programie [programowo](./how-to-manage-enrollments-sdks.md) lub za pomocą [portalu](./how-to-manage-enrollments.md).

## <a name="next-steps"></a>Następne kroki
* Opracowywanie zawartości przy użyciu [zestawu Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) dla platformy Azure IoT Hub i platformy Azure IoT Hub Device Provisioning Service