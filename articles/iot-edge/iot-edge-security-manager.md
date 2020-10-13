---
title: Azure IoT Edge Security Manager — Azure IoT Edge
description: Zarządza IoT Edge zasobów zakresie zabezpieczeń urządzeń i integralnością usług zabezpieczeń.
services: iot-edge
keywords: zabezpieczenia, zabezpieczanie elementu, enklawy, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76548700"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge Security Manager

Azure IoT Edge Security Manager to dobrze ograniczone podstawowe zabezpieczenia na potrzeby ochrony urządzeń IoT Edge i wszystkich jej składników przez abstrakcję bezpiecznego sprzętu krzemowego. Menedżer zabezpieczeń jest punktem ogniskowym zabezpieczeń i zapewnia integrację technologii z producentem oryginalnego sprzętu (OEM).

![Azure IoT Edge Security Manager](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge Security Manager chce chronić integralność urządzenia IoT Edge i wszystkich nieodłącznych operacji oprogramowania. Usługa Security Manager przechodzi zaufania z bazowego sprzętu głównego sprzętu zaufania (jeśli jest dostępny) do uruchamiania IoT Edge środowiska uruchomieniowego i monitorowania bieżących operacji.  IoT Edge Security Manager to oprogramowanie działające wraz z bezpiecznym sprzętem krzemu (jeśli jest dostępny) w celu zapewnienia najwyższej możliwej gwarancji zabezpieczeń.  

Obowiązki programu IoT Edge Security Manager obejmują, ale nie są ograniczone do:

* Bezpieczne i mierzone uruchamianie urządzenia Azure IoT Edge.
* Inicjowanie obsługi tożsamości urządzeń i przejście zaufania, jeśli ma to zastosowanie.
* Hostowanie i ochrona składników urządzeń usług w chmurze, takich jak usługa Device Provisioning.
* Bezpiecznie Zainicjuj obsługę modułów IoT Edge przy użyciu unikatowych tożsamości.
* Strażnik do sprzętowego katalogu głównego zaufania za poorednictwem usług notarialnych.
* Monitoruj integralność operacji IoT Edge w czasie wykonywania.

Program IoT Edge Security Manager zawiera trzy składniki:

* IoT Edge demona zabezpieczeń.
* Warstwa abstrakcji platformy sprzętowego modułu zabezpieczeń (HSM PAL).
* Opcjonalny, ale wysoce zalecany sprzętowy korzeń modelu krzemu zaufania lub modułu HSM.

## <a name="the-iot-edge-security-daemon"></a>Demon zabezpieczeń IoT Edge

Demon zabezpieczeń IoT Edge jest odpowiedzialny za operacje logiczne programu IoT Edge Security Manager. Reprezentuje znaczną część zaufanej bazy obliczeniowej na urządzeniu IoT Edge.

### <a name="design-principles"></a>Zasady projektowania

Demon zabezpieczeń IoT Edge jest zgodny z dwoma podstawowymi zasadami: Maksymalizuj integralność operacyjną i Minimalizuj przeładowanie i zmiany.

#### <a name="maximize-operational-integrity"></a>Maksymalizuj integralność operacyjną

Demon IoT Edge Security jest w stanie zapewnić najwyższą integralność w ramach funkcji obrony każdego danego elementu głównego sprzętu zaufania. Dzięki właściwej integracji certyfikat główny zaufania i monitoruje demona zabezpieczeń statycznie i w czasie wykonywania w celu odporności na manipulacje.

Dostęp fizyczny jest zawsze zagrożeniem dla urządzeń IoT. Katalog główny sprzętu zaufania odgrywa ważną rolę w zabezpieczaniu integralności IoT Edge demona zabezpieczeń.  Katalog główny sprzętu zaufania ma dwie odmiany:

* Zabezpieczanie elementów do ochrony poufnych informacji, takich jak wpisy tajne i klucze kryptograficzne.
* Zabezpiecz enclaves do ochrony kluczy tajnych, takich jak klucze, i wrażliwych obciążeń, takich jak pomiar i rozliczenia.

Istnieją dwa rodzaje środowisk wykonawczych do użycia głównego katalogu sprzętowego zaufania:

* Standardowe lub bogate środowisko wykonawcze (REE), które polega na użyciu bezpiecznych elementów do ochrony poufnych informacji.
* Środowisko TEE (Trusted Execution Environment), które polega na użyciu bezpiecznej technologii enklawy do ochrony poufnych informacji i zapewniania ochrony przed wykonaniem oprogramowania.

W przypadku urządzeń korzystających z bezpiecznego enclaves jako katalogu głównego sprzętu zaufania poufna logika w ramach IoT Edge Security DAEMON powinna znajdować się wewnątrz enklawy.  Niewrażliwe fragmenty demona zabezpieczeń mogą być poza TEEem.  W każdym przypadku pierwotni producenci projektu (ODM) i producenci oryginalnego sprzętu (OEM) powinni rozciągnąć zaufanie od ich modułu HSM, aby mierzyć i chronić integralność IoT Edge demona zabezpieczeń w przypadku rozruchu i środowiska uruchomieniowego.

#### <a name="minimize-bloat-and-churn"></a>Minimalizacja przeładowanie i zmian

Inną podstawową zasadą dla demona IoT Edge Security jest zminimalizowanie zmian.  W przypadku najwyższego poziomu zaufania demon IoT Edge Security może ściśle współpracować z głównym katalogiem sprzętowym zaufania i działać jako kod natywny.  Jest to typowy dla tych typów realizacji, aby zaktualizować oprogramowanie demona za pomocą głównej ścieżki do sprzętu zaufanej aktualizacji (w przeciwieństwie do mechanizmów systemu operacyjnego dostarczonych przez system operacyjny), które mogą być trudne w niektórych scenariuszach.  Podczas gdy w przypadku urządzeń IoT zaleca się odnowienie zabezpieczeń, nadmierne wymagania dotyczące aktualizacji lub duże ładunki aktualizacji mogą rozszerzać powierzchnię zagrożeń na wiele sposobów.  Przykłady obejmują Pomijanie aktualizacji w celu zmaksymalizowania dostępności operacyjnej lub głównego sprzętu zaufania zbyt ograniczonego do przetwarzania dużych ładunków aktualizacji.  W związku z tym projekt demona IoT Edge Security jest zwięzły, aby zapewnić, że rozmiary i zaufanych obliczeń są niewielkie i zminimalizować wymagania dotyczące aktualizacji.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architektura demona IoT Edge Security

![Demon zabezpieczeń Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Demon zabezpieczeń IoT Edge korzysta z dowolnego dostępnego głównego sprzętu technologii zaufania na potrzeby zabezpieczania zabezpieczeń.  Umożliwia również dzielenie operacji na całym świecie między standardowym/rozbudowanym środowiskiem wykonywania (REE) a zaufanym środowiskiem wykonywania (TEE), gdy technologie sprzętowe oferują zaufane środowiska wykonawcze. Interfejsy specyficzne dla ról umożliwiają główne składniki IoT Edge, aby zapewnić integralność urządzenia IoT Edge i jego operacji.

#### <a name="cloud-interface"></a>Interfejs chmury

Interfejs chmury umożliwia demona IoT Edge Security, aby uzyskać dostęp do usług w chmurze, takich jak chmura odniesień do zabezpieczeń urządzeń, takich jak odnowienie zabezpieczeń.  Na przykład demon zabezpieczeń IoT Edge obecnie używa tego interfejsu do uzyskiwania dostępu do usługi Azure IoT Hub [Device Provisioning](https://docs.microsoft.com/azure/iot-dps/) w celu zarządzania cyklem życia tożsamości urządzeń.  

#### <a name="management-api"></a>Interfejs API zarządzania

IoT Edge Security DAEMON oferuje interfejs API zarządzania, który jest wywoływany przez agenta IoT Edge podczas tworzenia/uruchamiania/zatrzymywania/usuwania modułu IoT Edge. Demon zabezpieczeń zapisuje "rejestracje" dla wszystkich aktywnych modułów. Te rejestracje mapują tożsamość modułu na niektóre właściwości modułu. Przykładowo te właściwości modułów obejmują identyfikator procesu (PID) procesu działającego w kontenerze i skrót zawartości kontenera Docker.

Te właściwości są używane przez interfejs API obciążenia (opisany poniżej) w celu sprawdzenia, czy obiekt wywołujący jest autoryzowany do działania.

Interfejs API zarządzania to uprzywilejowany interfejs API, który można wywołać tylko z agenta IoT Edge.  Ponieważ IoT Edge Bootstrap demona zabezpieczeń i uruchamia agenta IoT Edge, sprawdza, czy Agent IoT Edge nie został naruszony, a następnie może utworzyć niejawną rejestrację dla agenta IoT Edge. Ten sam proces zaświadczania, którego używa interfejs API obciążenia, ogranicza dostęp do interfejsu API zarządzania tylko do agenta IoT Edge.

#### <a name="container-api"></a>Interfejs API kontenera

Interfejs API kontenera współdziała z systemem kontenerów używanym do zarządzania modułami, takimi jak Moby lub Docker.

#### <a name="workload-api"></a>Interfejs API obciążenia

Interfejs API obciążenia jest dostępny dla wszystkich modułów. Zapewnia potwierdzenie tożsamości, jako certyfikat z podpisem własnym w formacie modułu HSM lub certyfikatu x509 oraz odpowiedni pakiet zaufania do modułu. Pakiet zaufania zawiera certyfikaty urzędu certyfikacji dla wszystkich pozostałych serwerów, które powinny być zaufane w modułach.

Demon zabezpieczeń IoT Edge używa procesu zaświadczania do zabezpieczenia tego interfejsu API. Gdy moduł wywołuje ten interfejs API, Demon zabezpieczeń próbuje znaleźć rejestrację dla tożsamości. Jeśli to się powiedzie, używa właściwości rejestracji do mierzenia modułu. Jeśli wynik procesu pomiaru jest zgodny z rejestracją, zostanie wygenerowana Nowa weryfikacja tożsamości. Odpowiednie certyfikaty urzędu certyfikacji (pakiet zaufania) są zwracane do modułu.  Moduł używa tego certyfikatu do łączenia się z IoT Hub, innymi modułami lub uruchamianiem serwera. Gdy podpisany token lub certyfikat zbliża się do wygaśnięcia, odpowiedzialność za moduł żąda nowego certyfikatu.

### <a name="integration-and-maintenance"></a>Integracja i konserwacja

Firma Microsoft utrzymuje główną bazę kodu dla [demona IoT Edge Security w serwisie GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Instalacja i aktualizacje

Instalacja i aktualizacje demona IoT Edge Security DAEMON są zarządzane za pomocą system zarządzania pakietami systemu operacyjnego. Urządzenia IoT Edge z certyfikatem głównym sprzętu zaufania powinny zapewniać dodatkową ochronę przed integralnością demona przez Zarządzanie cyklem życia za pomocą systemów bezpiecznego rozruchu i zarządzania aktualizacjami. Twórcy urządzeń powinni poznać te drogi na podstawie ich możliwości urządzeń.

#### <a name="versioning"></a>Obsługa wersji

Środowisko uruchomieniowe IoT Edge śledzi i raportuje wersję demona IoT Edge Security. Wersja jest raportowana jako atrybut *Runtime. platform. Version* dla właściwości "module agenta IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Warstwa abstrakcji platformy sprzętowego modułu zabezpieczeń (HSM PAL)

Moduł HSM PAL dzieli cały rdzeń sprzętu zaufania, aby odizolować dewelopera lub użytkownika IoT Edge z ich złożoności.  Obejmuje on kombinację procedur komunikacji między interfejsem programowania aplikacji (API) i międzydomenowych, na przykład komunikację między standardowym środowiskiem wykonywania a bezpieczną enklawy.  Rzeczywista implementacja modułu HSM PAL zależy od określonego bezpiecznego sprzętu w użyciu. Jego istnienie umożliwia korzystanie z praktycznie dowolnego bezpiecznego sprzętu krzemu.

## <a name="secure-silicon-root-of-trust-hardware"></a>Bezpieczny katalog główny krzemu sprzętu zaufania

W celu zakotwiczenia zaufania wewnątrz sprzętowego urządzenia IoT Edge należy zabezpieczyć krzem.  Zabezpieczanie krzemu polega na uwzględnieniu moduł TPM (TPM), osadzonego bezpiecznego elementu (eSE), ARM TrustZone, Intel SGX i niestandardowych technologii krzemu.  Korzystanie z bezpiecznego modelu krzemu zaufania na urządzeniach jest zalecane z uwzględnieniem zagrożeń związanych z fizycznym dostępnością urządzeń IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge integracja i Konserwacja programu Security Manager

IoT Edge Security Manager chce identyfikować i izolować składniki, które chronią zabezpieczenia i integralność platformy Azure IoT Edge w celu zapewnienia niestandardowej funkcjonalności. Osoby trzecie, takie jak twórcy urządzeń, powinny korzystać z niestandardowych funkcji zabezpieczeń dostępnych na urządzeniach.  Zobacz sekcję następne kroki, aby poznać linki, które pokazują, jak zabezpieczyć usługę Azure IoT Security Manager za pomocą moduł TPM (TPM) na platformach Linux i Windows. Te przykłady używają oprogramowania lub wirtualnej moduły TPM, ale bezpośrednio mają zastosowanie do korzystania z dyskretnych urządzeń TPM.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z blogiem dotyczącym [zabezpieczania inteligentnej krawędzi](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Utwórz i Udostępnij [urządzenie IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md).

Utwórz i Udostępnij [urządzenie IoT Edge przy użyciu symulowanego modułu TPM w systemie Windows](how-to-auto-provision-simulated-device-windows.md).
