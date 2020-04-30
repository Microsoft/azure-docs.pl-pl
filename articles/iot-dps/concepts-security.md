---
title: Azure IoT Hub Device Provisioning Service — pojęcia dotyczące zabezpieczeń
description: Zawiera opis pojęć dotyczących aprowizacji zabezpieczeń specyficznych dla urządzeń z usługą Device Provisioning (DPS) i IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79271566"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT Hub Device Provisioning Service pojęcia dotyczące zabezpieczeń 

IoT Hub Device Provisioning Service to usługa pomocnika dla IoT Hub, która służy do konfigurowania aprowizacji urządzeń bez dotyku do określonego Centrum IoT Hub. Usługa Device Provisioning umożliwia [samoobsługowe inicjowanie](concepts-auto-provisioning.md) milionów urządzeń w bezpieczny i skalowalny sposób. Ten artykuł zawiera omówienie koncepcji *zabezpieczeń* związanych z obsługą administracyjną urządzeń. Ten artykuł dotyczy wszystkich osób związanych z przygotowaniem urządzenia do wdrożenia.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metodą służącą do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania ma również zastosowanie do listy rejestracji, która informuje usługę aprowizacji o metodzie zaświadczania, która ma być używana z danym urządzeniem.

> [!NOTE]
> IoT Hub używa "schematu uwierzytelniania" dla podobnej koncepcji w tej usłudze.

Usługa Device Provisioning obsługuje następujące formy zaświadczania:
* **Certyfikaty x. 509** oparte na standardowym przepływie uwierzytelniania certyfikatu x. 509.
* **Moduł TPM (TPM)** na podstawie wyzwania nonce przy użyciu standardu TPM dla kluczy do prezentowania podpisanego tokenu sygnatury dostępu współdzielonego (SAS). Ta forma zaświadczania nie wymaga fizycznego modułu TPM na urządzeniu, ale usługa oczekuje na zaświadczenie przy użyciu klucza poręczenia zgodnie z [specyfikacją modułu TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Klucz symetryczny** oparty na [tokenach zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-tokens)sygnatury dostępu współdzielonego (SAS), które obejmują sygnaturę skrótu i osadzoną datę wygaśnięcia. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń (HSM) służy do bezpiecznego, opartego na sprzęcie magazynu wpisów tajnych urządzeń i stanowi najbezpieczniejsza postać magazynu tajnego. Oba certyfikaty X. 509 i tokeny SAS mogą być przechowywane w module HSM. Sprzętowych modułów zabezpieczeń można używać w przypadku obu mechanizmów zaświadczania obsługiwanych przez funkcję aprowizacji.

> [!TIP]
> Zdecydowanie zalecamy używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych na urządzeniach.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest mniej bezpieczną postacią magazynu niż moduł HSM.

## <a name="trusted-platform-module"></a>Moduł TPM

Moduł TPM może odwoływać się do standardu w celu bezpiecznego przechowywania kluczy używanych do uwierzytelniania platformy lub może odwoływać się do interfejsu we/wy używanego do współpracy z modułami implementującymi standard. Moduły TPM może istnieć jako sprzęt dyskretny, zintegrowany sprzęt, oprogramowanie układowe lub oprogramowanie. Dowiedz się więcej o [zaświadczeniu moduły TPM i modułu TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Usługa Device Provisioning obsługuje tylko moduł TPM 2,0.

Zaświadczanie modułu TPM jest oparte na wyzwaniem nonce, które używa kluczy głównych potwierdzeń i magazynów w celu zaprezentowania podpisanego tokenu sygnatury dostępu współdzielonego (SAS).

### <a name="endorsement-key"></a>Klucz poręczenia

Klucz poręczenia jest kluczem asymetrycznym zawartym wewnątrz modułu TPM, który został wewnętrznie wygenerowany lub wprowadzony w czasie produkcji i jest unikatowy dla każdego modułu TPM. Nie można zmienić ani usunąć klucza poręczenia. Prywatna część klucza poręczenia nigdy nie jest wydawana poza modułem TPM, podczas gdy publiczna część klucza poręczenia jest używana do rozpoznawania oryginalnego modułu TPM. Dowiedz się więcej na temat [klucza poręczenia](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Klucz główny magazynu

Klucz główny magazynu jest przechowywany w module TPM i służy do ochrony kluczy modułu TPM utworzonych przez aplikacje, dzięki czemu klucze te nie mogą być używane bez modułu TPM. Klucz główny magazynu jest generowany podczas przejmowania własności modułu TPM; Po wyczyszczeniu modułu TPM, aby nowy użytkownik mógł przejąć własność, generowany jest nowy klucz główny magazynu. Dowiedz się więcej o [kluczu głównym magazynu](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certyfikaty X. 509

Używanie certyfikatów X. 509 jako mechanizmu zaświadczania jest doskonałym sposobem na skalowanie produkcji i uproszczenie aprowizacji urządzeń. Certyfikaty X. 509 są zwykle uporządkowane w łańcuchu certyfikatów zaufania, w którym każdy certyfikat w łańcuchu jest podpisany przez klucz prywatny następnego wyższego certyfikatu itd., kończy się w certyfikacie głównym z podpisem własnym. To rozmieszczenie nawiązuje delegowany łańcuch zaufania z certyfikatu głównego wygenerowanego przez zaufany główny urząd certyfikacji (CA) przez każdy pośredni urząd certyfikacji do certyfikatu typu "liść" jednostki końcowej zainstalowanego na urządzeniu. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](/azure/iot-hub/iot-hub-x509ca-overview). 

Często łańcuch certyfikatów reprezentuje niepewną logiczną lub fizyczną hierarchię skojarzoną z urządzeniami. Na przykład producent może:
- wystawianie certyfikatu głównego urzędu certyfikacji z podpisem własnym
- Użyj certyfikatu głównego, aby wygenerować unikatowy certyfikat pośredniego urzędu certyfikacji dla każdej fabryki
- Użyj certyfikatu każdej fabryki, aby wygenerować unikatowy certyfikat pośredniego urzędu certyfikacji dla każdej linii produkcyjnej w zakładzie
- i na koniec Użyj certyfikatu wiersza produkcji, aby wygenerować unikatowy certyfikat urządzenia (klasy końcowej) dla każdego urządzenia wygenerowanego w wierszu. 

Aby dowiedzieć się więcej, zobacz temat [pojęcia dotyczące pojęć związanych z certyfikatami urzędu certyfikacji X. 509 w branży IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certyfikat główny

Certyfikat główny to certyfikat X. 509 z podpisem własnym reprezentujący urząd certyfikacji (CA). Jest to Terminus lub zakotwiczenie zaufania w łańcuchu certyfikatów. Certyfikaty główne mogą być wystawiane przez organizację lub zakupione z głównego urzędu certyfikacji. Aby dowiedzieć się więcej, zobacz [pobieranie certyfikatów urzędu certyfikacji X. 509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Certyfikat główny może być również określony jako certyfikat głównego urzędu certyfikacji.

### <a name="intermediate-certificate"></a>Certyfikat pośredni

Certyfikat pośredni to certyfikat X. 509, który został podpisany przez certyfikat główny (lub inny certyfikat pośredni z certyfikatem głównym w łańcuchu). Ostatni certyfikat pośredni w łańcuchu jest używany do podpisywania certyfikatu liścia. Certyfikat pośredni może być również określany jako certyfikat pośredniego urzędu certyfikacji.

### <a name="end-entity-leaf-certificate"></a>Certyfikat typu "liść" jednostki końcowej

Certyfikat liścia lub certyfikat jednostki końcowej identyfikuje właściciela certyfikatu. Ma certyfikat główny w łańcuchu certyfikatów, a także zero lub więcej certyfikatów pośrednich. Certyfikat liścia nie jest używany do podpisywania innych certyfikatów. Jednoznacznie identyfikuje urządzenie w usłudze aprowizacji i jest czasami określane jako certyfikat urządzenia. Podczas uwierzytelniania urządzenie używa klucza prywatnego skojarzonego z tym certyfikatem, aby odpowiedzieć na potwierdzenie istnienia usługi.

Certyfikaty liści używane z [indywidualnym](./concepts-service.md#individual-enrollment) wpisem rejestracji mają wymaganie, aby **nazwa podmiotu** była ustawiona na identyfikator rejestracji indywidualnego wpisu rejestracji. Certyfikaty liści używane z wpisem [grupy rejestracji](./concepts-service.md#enrollment-group) powinny mieć ustawioną **nazwę podmiotu** na żądany identyfikator urządzenia, który będzie wyświetlany w **rekordach rejestracji** dla uwierzytelnionego urządzenia w grupie rejestracji.

Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń podpisanych za pomocą certyfikatów X. 509 urzędu certyfikacji](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrolowanie dostępu urządzenia do usługi aprowizacji za pomocą certyfikatów X. 509

Usługa aprowizacji ujawnia dwa typy wpisów rejestracji, za pomocą których można kontrolować dostęp do urządzeń korzystających z mechanizmu zaświadczania X. 509:  

- [Poszczególne wpisy rejestracji](./concepts-service.md#individual-enrollment) są konfigurowane przy użyciu certyfikatu urządzenia skojarzonego z określonym urządzeniem. Te wpisy kontrolują rejestracje dla konkretnych urządzeń.
- Wpisy [grupy rejestracji](./concepts-service.md#enrollment-group) są skojarzone z określonym pośrednim lub głównym certyfikatem urzędu certyfikacji. Te wpisy kontrolują rejestracje dla wszystkich urządzeń, które mają ten certyfikat pośredni lub główny w łańcuchu certyfikatów. 

Gdy urządzenie nawiązuje połączenie z usługą aprowizacji, usługa ustala priorytet bardziej szczegółowych wpisów rejestracji przy użyciu mniej szczegółowych wpisów rejestracji. Oznacza to, że jeśli istnieje indywidualna Rejestracja urządzenia, usługa aprowizacji stosuje ten wpis. Jeśli nie istnieje indywidualna Rejestracja dla danego urządzenia, a grupa rejestracji pierwszego certyfikatu pośredniego w łańcuchu certyfikatów urządzenia istnieje, usługa zastosuje ten wpis i tak dalej, łańcuch do katalogu głównego. Usługa stosuje pierwszy stosowny wpis, który znajdzie, tak że:

- W przypadku włączenia pierwszego wpisu rejestracji usługa Inicjuje obsługę administracyjną urządzenia.
- Jeśli pierwszy znaleziony wpis rejestracji jest wyłączony, usługa nie udostępnia tego urządzenia.  
- Jeśli wpis rejestracji nie zostanie znaleziony dla żadnego z certyfikatów w łańcuchu certyfikatów urządzenia, usługa nie udostępnia tego urządzenia. 

Ten mechanizm i hierarchiczna struktura łańcuchów certyfikatów zapewniają zaawansowaną elastyczność w zakresie kontrolowania dostępu do poszczególnych urządzeń oraz grup urządzeń. Załóżmy na przykład, że można przystąpić do pięciu urządzeń przy użyciu następujących łańcuchów certyfikatów: 

- *Urządzenie 1*: certyfikat główny — certyfikat > certyfikat A > urządzenie 1
- *Urządzenie 2*: certyfikat główny — certyfikat > certyfikat A > Urządzenie 2
- *Urządzenie 3*: certyfikat główny — certyfikat > certyfikat A > urządzenie 3
- *Urządzenie 4*: certyfikat główny — certyfikat > B — > certyfikat urządzenia 4
- *Urządzenie 5*: certyfikat główny — certyfikat > B-> certyfikat urządzenia 5

Początkowo można utworzyć pojedynczy wpis rejestracji grupy z włączonym certyfikatem głównym, aby umożliwić dostęp do wszystkich pięciu urządzeń. Jeśli certyfikat B później zostanie naruszony, można utworzyć wyłączony wpis grupy rejestracji dla certyfikatu B w celu uniemożliwienia rejestracji *urządzeń 4* i *5 na urządzeniu* . Jeśli nadal później zostanie naruszone bezpieczeństwo *urządzenia 3* , można utworzyć wyłączony indywidualny wpis rejestracji dla swojego certyfikatu. Spowoduje to odwołanie dostępu do *urządzenia 3*, ale nadal umożliwia zarejestrowanie *urządzenia 1* i *2* .