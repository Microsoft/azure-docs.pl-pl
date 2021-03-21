---
title: Terminologia używana z usługą Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Opisuje powszechną terminologię używaną w usłudze Device Provisioning Service (DPS) i IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019450"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologia dotycząca IoT Hub Device Provisioning Service (DPS)

IoT Hub Device Provisioning Service to usługa pomocnika dla IoT Hub, która służy do konfigurowania aprowizacji urządzeń bez dotyku do określonego Centrum IoT Hub. Usługa Device Provisioning umożliwia [udostępnianie](about-iot-dps.md#provisioning-process) milionów urządzeń w bezpieczny i skalowalny sposób.

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

* **Niestandardowy (Korzystaj z funkcji platformy Azure)**: niestandardowe zasady alokacji dają większą kontrolę nad sposobem przypisywania urządzeń do centrum IoT Hub. Jest to realizowane za pomocą niestandardowego kodu w funkcji platformy Azure w celu przypisywania urządzeń do centrum IoT. Usługa Device Provisioning wywołuje kod funkcji platformy Azure, dostarczając wszystkie istotne informacje o urządzeniu i rejestracji w kodzie. Kod funkcji jest wykonywany i zwraca informacje o usłudze IoT Hub używane do aprowizacji urządzenia.

## <a name="enrollment"></a>Rejestrowanie

Rejestracja to rekord urządzeń lub grup urządzeń, które mogą zarejestrować się w ramach automatycznej aprowizacji. Rekord rejestracji zawiera informacje o urządzeniu lub grupie urządzeń, w tym:
- [mechanizm zaświadczania](#attestation-mechanism) używany przez urządzenie
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


## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metodą służącą do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania jest konfigurowany w ramach wpisu rejestracji i informuje usługę aprowizacji, której metody należy użyć podczas weryfikacji tożsamości urządzenia podczas rejestracji.

> [!NOTE]
> IoT Hub używa "schematu uwierzytelniania" dla podobnej koncepcji w tej usłudze.

Usługa Device Provisioning obsługuje następujące formy zaświadczania:
* **Certyfikaty x. 509** oparte na standardowym przepływie uwierzytelniania certyfikatu x. 509. Aby uzyskać więcej informacji, zobacz [zaświadczanie X. 509](concepts-x509-attestation.md).
* **Trusted Platform Module (TPM)** na podstawie wyzwania nonce przy użyciu standardu TPM dla kluczy do prezentowania podpisanego tokenu sygnatury dostępu współdzielonego (SAS). Nie wymaga to fizycznego modułu TPM na urządzeniu, ale usługa oczekuje na zaświadczenie przy użyciu klucza poręczenia zgodnie z [specyfikacją modułu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Aby uzyskać więcej informacji, zobacz [zaświadczanie modułu TPM](concepts-tpm-attestation.md).
* **Klucz symetryczny** oparty na [tokenach zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-tokens)sygnatury dostępu współdzielonego (SAS), które obejmują sygnaturę skrótu i osadzoną datę wygaśnięcia. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń (HSM) służy do bezpiecznego, opartego na sprzęcie magazynu wpisów tajnych urządzeń i stanowi najbezpieczniejsza postać magazynu tajnego. Oba certyfikaty X. 509 i tokeny SAS mogą być przechowywane w module HSM. Sprzętowych modułów zabezpieczeń można użyć w przypadku obu mechanizmów zaświadczania obsługiwanych przez usługę aprowizacji.

> [!TIP]
> Zdecydowanie zalecamy używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych na urządzeniach.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest mniej bezpieczną postacią magazynu niż moduł HSM.



## <a name="id-scope"></a>Zakres identyfikatorów

Zakres identyfikatora jest przypisywany do usługi Device Provisioning, gdy zostanie utworzony przez użytkownika i jest używany do unikatowego identyfikowania konkretnej usługi aprowizacji, za pomocą której urządzenie zostanie zarejestrowane. Zakres identyfikatorów jest generowany przez usługę i jest niezmienny, co gwarantuje unikatowość.

> [!NOTE]
> Unikatowość jest ważna w przypadku długotrwałych operacji wdrażania oraz scenariuszy scalania i pozyskiwania.


## <a name="registration"></a>Rejestracja

Rejestracja polega na tym, że rejestr urządzenia pomyślnie zarejestrował się do IoT Hub za pośrednictwem usługi Device Provisioning. Rekordy rejestracji są tworzone automatycznie; można je usunąć, ale nie można ich zaktualizować.


## <a name="registration-id"></a>Identyfikator rejestracji

Identyfikator rejestracji służy do unikatowego identyfikowania rejestracji urządzenia w usłudze Device Provisioning. Identyfikator urządzenia musi być unikatowy w [zakresie identyfikatora](#id-scope)usługi aprowizacji. Każde urządzenie musi mieć identyfikator rejestracji. Identyfikator rejestracji jest alfanumeryczny, bez uwzględniania wielkości liter i może zawierać znaki specjalne, w tym dwukropek, kropkę, podkreślenie i łącznik.

* W przypadku modułu TPM Identyfikator rejestracji jest dostarczany przez moduł TPM.
* W przypadku zaświadczania opartego na X. 509 identyfikator rejestracji jest podawany jako nazwa podmiotu certyfikatu.

## <a name="device-id"></a>Identyfikator urządzenia

Identyfikator urządzenia jest IDENTYFIKATORem wyświetlanym w IoT Hub. Żądany identyfikator urządzenia można ustawić we wpisie rejestracji, ale nie musi być ustawiony. Ustawienie żądanego identyfikatora urządzenia jest obsługiwane tylko w przypadku rejestracji indywidualnych. Jeśli na liście rejestracji nie określono żądanego identyfikatora urządzenia, Identyfikator rejestracji jest używany jako identyfikator urządzenia podczas rejestrowania urządzenia. Dowiedz się więcej o [identyfikatorach urządzeń w IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operacje

Operacje są jednostką rozliczeniową usługi Device Provisioning. Jedną z operacji jest pomyślne zakończenie jednej instrukcji do usługi. Operacje obejmują rejestracje urządzeń i ponowne rejestracje; Operacje obejmują również zmiany po stronie usługi, takie jak Dodawanie wpisów listy rejestracji i aktualizowanie wpisów na liście rejestracji.
