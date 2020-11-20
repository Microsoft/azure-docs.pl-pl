---
title: Azure IoT Hub Device Provisioning Service — zaświadczanie certyfikatu X. 509
description: Opisuje pojęcia związane z używaniem zaświadczania o certyfikatach X. 509 z usługą Device Provisioning Service (DPS) i IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9eee315aac28847710662b463add7d6e68d8d505
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967299"
---
# <a name="x509-certificate-attestation"></a>Zaświadczanie certyfikatu X.509

Ten artykuł zawiera omówienie koncepcji usługi Device Provisioning (DPS) związanych z zaświadczeniem urządzeń przy użyciu certyfikatu X. 509. Ten artykuł dotyczy wszystkich osób związanych z przygotowaniem urządzenia do wdrożenia.

Certyfikaty X. 509 mogą być przechowywane w sprzętowym module zabezpieczeń modułu HSM.

> [!TIP]
> Zdecydowanie zalecamy używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych, takich jak certyfikat X. 509, na urządzeniach w środowisku produkcyjnym.


## <a name="x509-certificates"></a>Certyfikaty X. 509

Używanie certyfikatów X. 509 jako mechanizmu zaświadczania jest doskonałym sposobem na skalowanie produkcji i uproszczenie aprowizacji urządzeń. Certyfikaty X. 509 są zwykle uporządkowane w łańcuchu certyfikatów zaufania, w którym każdy certyfikat w łańcuchu jest podpisany przez klucz prywatny następnego wyższego certyfikatu itd., kończy się w certyfikacie głównym z podpisem własnym. To rozmieszczenie nawiązuje delegowany łańcuch zaufania z certyfikatu głównego wygenerowanego przez zaufany główny urząd certyfikacji (CA) przez każdy pośredni urząd certyfikacji do certyfikatu typu "liść" jednostki końcowej zainstalowanego na urządzeniu. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md). 

Często łańcuch certyfikatów reprezentuje niepewną logiczną lub fizyczną hierarchię skojarzoną z urządzeniami. Na przykład producent może:
- wystawianie certyfikatu głównego urzędu certyfikacji z podpisem własnym
- Użyj certyfikatu głównego, aby wygenerować unikatowy certyfikat pośredniego urzędu certyfikacji dla każdej fabryki
- Użyj certyfikatu każdej fabryki, aby wygenerować unikatowy certyfikat pośredniego urzędu certyfikacji dla każdej linii produkcyjnej w zakładzie
- i na koniec Użyj certyfikatu wiersza produkcji, aby wygenerować unikatowy certyfikat urządzenia (klasy końcowej) dla każdego urządzenia wygenerowanego w wierszu. 

Aby dowiedzieć się więcej, zobacz temat [pojęcia dotyczące pojęć związanych z certyfikatami urzędu certyfikacji X. 509 w branży IoT](../iot-hub/iot-hub-x509ca-concept.md). 

### <a name="root-certificate"></a>Certyfikat główny

Certyfikat główny to certyfikat X. 509 z podpisem własnym reprezentujący urząd certyfikacji (CA). Jest to Terminus lub zakotwiczenie zaufania w łańcuchu certyfikatów. Certyfikaty główne mogą być wystawiane przez organizację lub zakupione z głównego urzędu certyfikacji. Aby dowiedzieć się więcej, zobacz [pobieranie certyfikatów urzędu certyfikacji X. 509](../iot-hub/iot-hub-security-x509-get-started.md#get-x509-ca-certificates). Certyfikat główny może być również określony jako certyfikat głównego urzędu certyfikacji.

### <a name="intermediate-certificate"></a>Certyfikat pośredni

Certyfikat pośredni to certyfikat X. 509, który został podpisany przez certyfikat główny (lub inny certyfikat pośredni z certyfikatem głównym w łańcuchu). Ostatni certyfikat pośredni w łańcuchu jest używany do podpisywania certyfikatu liścia. Certyfikat pośredni może być również określany jako certyfikat pośredniego urzędu certyfikacji.

##### <a name="why-are-intermediate-certs-useful"></a>Dlaczego certyfikaty pośrednie są przydatne?
Certyfikaty pośrednie są używane na wiele sposobów. Na przykład certyfikaty pośrednie mogą służyć do grupowania urządzeń według linii produktów, klientów kupowania urządzeń, działów firmy lub fabryk. 

Załóżmy, że firma Contoso jest wielką firmą z własną infrastrukturą kluczy publicznych (PKI) przy użyciu certyfikatu głównego o nazwie *ContosoRootCert*. Każdy oddział firmy Contoso ma swój własny certyfikat pośredni podpisany przez *ContosoRootCert*. Każdy z tych podmiotów zależnych będzie używał certyfikatu pośredniego w celu podpisania certyfikatów liści dla każdego urządzenia. W tym scenariuszu firma Contoso może używać jednego wystąpienia punktu dystrybucji, w którym zweryfikowano *ContosoRootCert* z [dowodem posiadania](./how-to-verify-certificates.md). Mogą mieć grupę rejestracji dla każdego podmiotu zależnego. W ten sposób poszczególne osoby zależne nie będą musieli martwić się o weryfikowanie certyfikatów.


### <a name="end-entity-leaf-certificate"></a>Certyfikat typu "liść" jednostki końcowej

Certyfikat liścia lub certyfikat jednostki końcowej identyfikuje właściciela certyfikatu. Ma certyfikat główny w łańcuchu certyfikatów, a także zero lub więcej certyfikatów pośrednich. Certyfikat liścia nie jest używany do podpisywania innych certyfikatów. Jednoznacznie identyfikuje urządzenie w usłudze aprowizacji i jest czasami określane jako certyfikat urządzenia. Podczas uwierzytelniania urządzenie używa klucza prywatnego skojarzonego z tym certyfikatem, aby odpowiedzieć na potwierdzenie istnienia usługi.

Certyfikaty liści używane z [indywidualnym](./concepts-service.md#individual-enrollment) wpisem rejestracji mają wymaganie, aby **nazwa podmiotu** była ustawiona na identyfikator rejestracji indywidualnego wpisu rejestracji. Certyfikaty liści używane z wpisem [grupy rejestracji](./concepts-service.md#enrollment-group) powinny mieć ustawioną **nazwę podmiotu** na żądany identyfikator urządzenia, który będzie wyświetlany w **rekordach rejestracji** dla uwierzytelnionego urządzenia w grupie rejestracji.

Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń podpisanych za pomocą certyfikatów X. 509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrolowanie dostępu urządzenia do usługi aprowizacji za pomocą certyfikatów X. 509

Usługa aprowizacji udostępnia dwa typy rejestracji, których można użyć do sterowania dostępem urządzeń za pomocą mechanizmu zaświadczania X. 509:  

- [Poszczególne wpisy rejestracji](./concepts-service.md#individual-enrollment) są konfigurowane przy użyciu certyfikatu urządzenia skojarzonego z określonym urządzeniem. Te wpisy kontrolują rejestracje dla konkretnych urządzeń.
- Wpisy [grupy rejestracji](./concepts-service.md#enrollment-group) są skojarzone z określonym pośrednim lub głównym certyfikatem urzędu certyfikacji. Te wpisy kontrolują rejestracje dla wszystkich urządzeń, które mają ten certyfikat pośredni lub główny w łańcuchu certyfikatów. 

#### <a name="dps-device-chain-requirements"></a>Dział DPS — wymagania dotyczące łańcucha urządzeń

Gdy urządzenie próbuje przeprowadzić rejestrację za pośrednictwem usługi DPS przy użyciu grupy rejestracji, urządzenie musi wysłać łańcuch certyfikatów z certyfikatu liścia do certyfikatu zweryfikowanego za pomocą [dowodu posiadania](how-to-verify-certificates.md). W przeciwnym razie uwierzytelnianie nie powiedzie się.

Jeśli na przykład tylko certyfikat główny zostanie zweryfikowany, a certyfikat pośredni zostanie przekazany do grupy rejestracji, urządzenie powinno przedstawić łańcuch certyfikatów od certyfikatu liścia do zweryfikowanego certyfikatu głównego. Ten łańcuch certyfikatów zawiera wszystkie certyfikaty pośrednie z przedziału między. Uwierzytelnianie nie powiedzie się, jeśli usługa DPS nie będzie mogła przepływać łańcucha certyfikatów do zweryfikowanego certyfikatu.

Rozważmy na przykład firmę korzystającą z następującego łańcucha urządzeń dla urządzenia.

![Przykładowy łańcuch certyfikatów urządzeń](./media/concepts-x509-attestation/example-device-cert-chain.png) 

Weryfikowany jest tylko certyfikat główny, a certyfikat *intermediate2* jest przekazywany do grupy rejestracji.

![Przykład zweryfikowanej głównej](./media/concepts-x509-attestation/example-root-verified.png) 

Jeśli urządzenie wysyła tylko następujący łańcuch urządzeń podczas aprowizacji, uwierzytelnianie zakończy się niepowodzeniem. Ponieważ usługa DPS nie może próbować uwierzytelniania przy założeniu, że ważność certyfikatu *intermediate1*

![Przykład niepowodzenia łańcucha certyfikatów](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

Jeśli urządzenie wyśle pełny łańcuch urządzeń w następujący sposób podczas aprowizacji, usługa DPS może próbować uwierzytelniać urządzenie.

![Przykładowy łańcuch certyfikatów urządzeń](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> Certyfikaty pośrednie można także zweryfikować przy użyciu [dowodu posiadania](how-to-verify-certificates.md).


#### <a name="dps-order-of-operations-with-certificates"></a>Kolejność operacji w usłudze DPS przy użyciu certyfikatów
Gdy urządzenie nawiązuje połączenie z usługą aprowizacji, usługa ustala priorytet bardziej szczegółowych wpisów rejestracji przy użyciu mniej szczegółowych wpisów rejestracji. Oznacza to, że jeśli istnieje indywidualna Rejestracja urządzenia, usługa aprowizacji stosuje ten wpis. Jeśli nie istnieje indywidualna Rejestracja dla danego urządzenia, a grupa rejestracji pierwszego certyfikatu pośredniego w łańcuchu certyfikatów urządzenia istnieje, usługa zastosuje ten wpis i tak dalej, w dół łańcucha do katalogu głównego. Usługa stosuje pierwszy stosowny wpis, który znajdzie, tak że:

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