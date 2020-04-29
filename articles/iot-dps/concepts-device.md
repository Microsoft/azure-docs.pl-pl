---
title: Pojęcia dotyczące urządzeń w usłudze Azure Device Provisioning | Microsoft Docs
description: Zawiera opis pojęć dotyczących aprowizacji urządzeń, które są specyficzne dla urządzeń z usługą Device Provisioning (DPS) i IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79285164"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service device concepts (Pojęcia dotyczące urządzenia dla usługi IoT Hub Device Provisioning Service)

IoT Hub Device Provisioning Service to usługa pomocnika dla IoT Hub, która służy do konfigurowania aprowizacji urządzeń bez dotyku do określonego Centrum IoT Hub. Usługa Device Provisioning umożliwia udostępnianie milionów urządzeń w bezpieczny i skalowalny sposób.

Ten artykuł zawiera omówienie pojęć dotyczących *urządzeń* związanych z obsługą administracyjną urządzeń. Ten artykuł jest najbardziej przydatny dla osób związanych z [etapem produkcyjnym](about-iot-dps.md#manufacturing-step) umożliwiającym przygotowanie urządzenia do wdrożenia.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metodą służącą do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania ma również zastosowanie do listy rejestracji, która informuje usługę aprowizacji o metodzie zaświadczania, która ma być używana z danym urządzeniem.

> [!NOTE]
> IoT Hub używa "schematu uwierzytelniania" dla podobnej koncepcji w tej usłudze.

Usługa Device Provisioning obsługuje następujące formy zaświadczania:
* **Certyfikaty x. 509** oparte na standardowym przepływie uwierzytelniania certyfikatu x. 509.
* **Moduł TPM (TPM)** na podstawie wyzwania nonce przy użyciu standardu TPM dla kluczy do prezentowania podpisanego tokenu sygnatury dostępu współdzielonego (SAS). Nie wymaga to fizycznego modułu TPM na urządzeniu, ale usługa oczekuje na zaświadczenie przy użyciu klucza poręczenia zgodnie z [specyfikacją modułu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Klucz symetryczny** oparty na [tokenach zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-tokens)sygnatury dostępu współdzielonego (SAS), które obejmują sygnaturę skrótu i osadzoną datę wygaśnięcia. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń (HSM) służy do bezpiecznego, opartego na sprzęcie magazynu wpisów tajnych urządzeń i stanowi najbezpieczniejsza postać magazynu tajnego. Oba certyfikaty X. 509 i tokeny SAS mogą być przechowywane w module HSM. Sprzętowych modułów zabezpieczeń można użyć w przypadku obu mechanizmów zaświadczania obsługiwanych przez usługę aprowizacji.

> [!TIP]
> Zdecydowanie zalecamy używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych na urządzeniach.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest mniej bezpieczną postacią magazynu niż moduł HSM.

## <a name="registration-id"></a>Identyfikator rejestracji

Identyfikator rejestracji służy do unikatowego identyfikowania urządzenia w usłudze Device Provisioning. Identyfikator urządzenia musi być unikatowy w [zakresie identyfikatora](#id-scope)usługi aprowizacji. Każde urządzenie musi mieć identyfikator rejestracji. Identyfikator rejestracji jest alfanumeryczny, bez uwzględniania wielkości liter i może zawierać znaki specjalne, w tym dwukropek, kropkę, podkreślenie i łącznik.

* W przypadku modułu TPM Identyfikator rejestracji jest dostarczany przez moduł TPM.
* W przypadku zaświadczania opartego na X. 509 identyfikator rejestracji jest podawany jako nazwa podmiotu certyfikatu.

## <a name="device-id"></a>Identyfikator urządzenia

Identyfikator urządzenia jest IDENTYFIKATORem wyświetlanym w IoT Hub. Żądany identyfikator urządzenia można ustawić we wpisie rejestracji, ale nie musi być ustawiony. Ustawienie żądanego identyfikatora urządzenia jest obsługiwane tylko w przypadku rejestracji indywidualnych. Jeśli na liście rejestracji nie określono żądanego identyfikatora urządzenia, Identyfikator rejestracji jest używany jako identyfikator urządzenia podczas rejestrowania urządzenia. Dowiedz się więcej o [identyfikatorach urządzeń w IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Zakres identyfikatorów

Zakres identyfikatora jest przypisywany do usługi Device Provisioning, gdy zostanie utworzony przez użytkownika i jest używany do unikatowego identyfikowania konkretnej usługi aprowizacji, za pomocą której urządzenie zostanie zarejestrowane. Zakres identyfikatorów jest generowany przez usługę i jest niezmienny, co gwarantuje unikatowość.

> [!NOTE]
> Unikatowość jest ważna w przypadku długotrwałych operacji wdrażania oraz scenariuszy scalania i pozyskiwania.
