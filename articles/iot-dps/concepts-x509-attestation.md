---
title: Azure IoT Hub Device Provisioning Service — zaświadczanie certyfikatu X. 509
description: Opisuje pojęcia związane z używaniem zaświadczania o certyfikatach X. 509 z usługą Device Provisioning Service (DPS) i IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 911f819343f675ebe0a2604d912e6e26aa646eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533063"
---
# <a name="x509-certificate-attestation"></a>Zaświadczanie certyfikatu X.509

Ten artykuł zawiera omówienie pojęć związanych z aprowizacji urządzeń przy użyciu zaświadczania o certyfikatach X. 509. Ten artykuł dotyczy wszystkich osób związanych z przygotowaniem urządzenia do wdrożenia.

Certyfikaty X. 509 mogą być przechowywane w sprzętowym module zabezpieczeń modułu HSM.

> [!TIP]
> Zdecydowanie zalecamy używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych, takich jak certyfikat X. 509, na urządzeniach w środowisku produkcyjnym.


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