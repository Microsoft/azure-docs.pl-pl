---
title: Pojęcia dotyczące usług w usłudze Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Zawiera opis pojęć dotyczących aprowizacji usług dotyczących urządzeń z usługą Device Provisioning (DPS) i IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79285216"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Koncepcje IoT Hub Device Provisioning Service

IoT Hub Device Provisioning Service to usługa pomocnika dla IoT Hub, która służy do konfigurowania aprowizacji urządzeń bez dotyku do określonego Centrum IoT Hub. Usługa Device Provisioning umożliwia [samoobsługowe inicjowanie](concepts-auto-provisioning.md) milionów urządzeń w bezpieczny i skalowalny sposób.

Inicjowanie obsługi urządzeń jest procesem dwuczęściowym. Pierwsza część ustanawia początkowe połączenie między urządzeniem a rozwiązaniem IoT przez *zarejestrowanie* urządzenia. Druga część stosuje odpowiednią *konfigurację* do urządzenia na podstawie określonych wymagań rozwiązania. Po zakończeniu obu kroków urządzenie zostało w pełni *zainicjowane*. Usługa Device Provisioning automatyzuje oba kroki, aby zapewnić bezproblemowe środowisko aprowizowania dla urządzenia.

Ten artykuł zawiera omówienie pojęć dotyczących aprowizacji, które są najbardziej odpowiednie do zarządzania *usługą*. Ten artykuł jest najbardziej przydatny dla osób związanych z [krokami konfiguracji chmury](about-iot-dps.md#cloud-setup-step) w celu przygotowania urządzenia do wdrożenia.

## <a name="service-operations-endpoint"></a>Punkt końcowy operacji usługi

Punkt końcowy operacji usługi jest punktem końcowym do zarządzania ustawieniami usługi i obsługi list rejestracji. Ten punkt końcowy jest używany tylko przez administratora usługi; nie jest on używany przez urządzenia.

## <a name="device-provisioning-endpoint"></a>Punkt końcowy aprowizacji urządzeń

Punkt końcowy aprowizacji urządzeń jest pojedynczym punktem końcowym używanym przez funkcję autoaprowizacji. Ten adres URL jest taki sam dla wszystkich wystąpień usługi aprowizacji, aby wyeliminować konieczność ponownego wypróbowania urządzeń z nowymi informacjami o połączeniu w scenariuszach łańcucha dostaw. Zakres identyfikatorów zapewnia izolację dzierżawy.

## <a name="linked-iot-hubs"></a>Połączone centra IoT Hub

Usługa Device Provisioning może udostępniać tylko urządzenia do centrów IoT, które zostały z nią połączone. Połączenie usługi IoT Hub z wystąpieniem usługi Device Provisioning zapewnia uprawnienia do odczytu/zapisu w rejestrze urządzenia Centrum IoT. za pomocą linku usługa Device Provisioning może zarejestrować identyfikator urządzenia i ustawić początkową konfigurację w ramach sznurka urządzenia. Połączone centra IoT mogą znajdować się w dowolnym regionie świadczenia usługi Azure. Możesz połączyć centra w innych subskrypcjach z usługą aprowizacji.

## <a name="allocation-policy"></a>Zasady alokacji

Ustawienie poziomu usługi, które określa sposób przypisywania urządzeń do usługi IoT Hub przez usługę Device Provisioning. Są obsługiwane trzy zasady alokacji:

* **Dystrybucja z równymi wagami**: połączone centra IoT są równie podobne, aby urządzenia były do nich obsługiwane. Ustawienie domyślne. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie.

* **Najmniejsze opóźnienie**: urządzenia są obsługiwane w centrum IoT Hub z najniższym opóźnieniem dla urządzenia. Jeśli wiele połączonych centrów IoT będzie zapewniało takie samo najniższe opóźnienie, usługa aprowizacji będzie mieszać urządzenia w tych centrach

* **Konfiguracja statyczna za pośrednictwem listy rejestracji**: Specyfikacja żądanego Centrum IoT na liście rejestracji ma pierwszeństwo przed zasadami alokacji poziomu usługi.

## <a name="enrollment"></a>Rejestrowanie

Rejestracja to rekord urządzeń lub grup urządzeń, które mogą zarejestrować się w ramach automatycznej aprowizacji. Rekord rejestracji zawiera informacje o urządzeniu lub grupie urządzeń, w tym:
- [mechanizm zaświadczania](concepts-security.md#attestation-mechanism) używany przez urządzenie
- Opcjonalna początkowa wymagana konfiguracja
- żądane Centrum IoT Hub
- żądany identyfikator urządzenia

Usługa Device Provisioning obsługuje dwa typy rejestracji:

### <a name="enrollment-group"></a>Grupa rejestracji

Grupa rejestracji to grupa urządzeń, które współużytkują określony mechanizm zaświadczania. Grupy rejestracji obsługują zarówno X. 509, jak i symetryczne. Wszystkie urządzenia w grupie rejestracji X. 509 zawierają certyfikaty X. 509, które zostały podpisane przez ten sam główny lub pośredni urząd certyfikacji (CA). Każde urządzenie w grupie rejestracji klucza symetrycznego zaprezentuje tokeny sygnatury dostępu współdzielonego na podstawie klucza symetrycznego grupy. Nazwa grupy rejestracji i nazwa certyfikatu muszą być alfanumeryczne, małe litery i mogą zawierać łączniki.

> [!TIP]
> Zalecamy używanie grupy rejestracji dla dużej liczby urządzeń, które współużytkują żądaną konfigurację początkową, lub dla urządzeń, które przechodzą do tej samej dzierżawy.

### <a name="individual-enrollment"></a>Rejestracja indywidualna

Rejestracja indywidualna to wpis dla jednego urządzenia, które może się zarejestrować. Rejestracje indywidualne mogą używać certyfikatów liścia X. 509 lub tokenów SAS (z poziomu fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Identyfikator rejestracji w rejestracji indywidualnej jest alfanumeryczny, pisany małymi literami i może zawierać łączniki. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

> [!TIP]
> Zalecamy używanie indywidualnych rejestracji dla urządzeń, które wymagają unikatowej konfiguracji początkowej, lub urządzeń, które mogą być uwierzytelniane tylko przy użyciu tokenów SAS za pośrednictwem zaświadczania TPM.

## <a name="registration"></a>Rejestracja

Rejestracja polega na tym, że rejestr urządzenia pomyślnie zarejestrował się do IoT Hub za pośrednictwem usługi Device Provisioning. Rekordy rejestracji są tworzone automatycznie; można je usunąć, ale nie można ich zaktualizować.

## <a name="operations"></a>Operacje

Operacje są jednostką rozliczeniową usługi Device Provisioning. Jedną z operacji jest pomyślne zakończenie jednej instrukcji do usługi. Operacje obejmują rejestracje urządzeń i ponowne rejestracje; Operacje obejmują również zmiany po stronie usługi, takie jak Dodawanie wpisów listy rejestracji i aktualizowanie wpisów na liście rejestracji.
