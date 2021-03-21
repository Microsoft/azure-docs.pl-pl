---
title: Struktura zabezpieczeń — Azure IoT Edge | Microsoft Docs
description: Dowiedz się więcej na temat standardów zabezpieczeń, uwierzytelniania i autoryzacji, które zostały użyte do opracowania Azure IoT Edge i które należy wziąć pod uwagę podczas projektowania rozwiązania
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ff154eee6c6174a8f1a3aa7bea37ef62273bb1a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489506"
---
# <a name="security-standards-for-azure-iot-edge"></a>Standardy zabezpieczeń Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge odnosi się do ryzyka, które są związane podczas przesuwania danych i analiz do inteligentnej krawędzi. IoT Edge standardy zabezpieczeń zapewniają elastyczność dla różnych scenariuszy wdrażania z ochroną oczekiwaną przez wszystkie usługi platformy Azure.

IoT Edge działa na różnych urządzeniach i modelach sprzętu, obsługuje kilka systemów operacyjnych i ma zastosowanie do różnorodnych scenariuszy wdrażania. Zamiast oferować specjalne rozwiązania dla konkretnych scenariuszy, IoT Edge to rozszerzalna platforma zabezpieczeń oparta na zasadach, które są przeznaczone do skalowania. Ryzyko scenariusza wdrażania zależy od wielu czynników, takich jak:

* Własność rozwiązania
* Lokalizacja geograficzna wdrożenia
* Czułość danych
* Prywatność
* Aplikacja w pionie
* Wymagania prawne

Ten artykuł zawiera omówienie struktury zabezpieczeń IoT Edge. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie inteligentnej krawędzi](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>Standardy

Standardy mają na celu łatwość kontroli i łatwość wdrażania, z których są hallmarks zabezpieczenia. Rozwiązanie zabezpieczeń powinno należeć do kontroli pod względem oceny w celu utworzenia zaufania i nie powinno być nieprzewidziane dla wdrożenia. Projektowanie struktury do zabezpieczania Azure IoT Edge opiera się na przetestowaniu czasu i sprawdzonych przez branżowe protokoły zabezpieczeń, aby poznać ich znajomość i użycie.

## <a name="authentication"></a>Authentication

Podczas wdrażania rozwiązania IoT należy wiedzieć, że tylko zaufane aktory, urządzenia i moduły mają dostęp do Twojego rozwiązania. Uwierzytelnianie oparte na certyfikatach jest podstawowym mechanizmem uwierzytelniania dla platformy Azure IoT Edge. Ten mechanizm pochodzi od zestawu standardów dotyczących infrastruktury kluczy publicznych (PKiX) przez Internet Engineering Task Force (IETF).

Wszystkie urządzenia, moduły i aktory, które współpracują z urządzeniem Azure IoT Edge, powinny mieć unikatowe tożsamości certyfikatów. Te wskazówki odnoszą się do tego, czy interakcje są fizyczne, czy za pomocą połączenia sieciowego. Nie każdy scenariusz lub składnik może nawiązać się z uwierzytelnianiem opartym na certyfikatach, dzięki czemu rozszerzalność struktury zabezpieczeń oferuje bezpieczne alternatywy.

Aby uzyskać więcej informacji, zobacz [Azure IoT Edge użycie certyfikatu](iot-edge-certs.md).

## <a name="authorization"></a>Autoryzacja

Zasada najniższych uprawnień oznacza, że użytkownicy i składniki systemu powinny mieć dostęp tylko do minimalnego zestawu zasobów i danych wymaganych do wykonywania ich ról. Urządzenia, moduły i aktory powinny uzyskiwać dostęp tylko do zasobów i danych w ramach ich zakresu uprawnień i tylko wtedy, gdy jest to możliwe pod kątem architektury. Niektóre uprawnienia można konfigurować przy użyciu wystarczających uprawnień, a inne są wymuszane w architekturze. Na przykład niektóre moduły mogą być autoryzowane do łączenia się z usługą Azure IoT Hub. Nie istnieje jednak powód, dla którego moduł w jednym IoT Edge urządzenie ma dostęp do sznurka modułu w innym IoT Edge urządzeniu.

Inne schematy autoryzacji obejmują prawa do podpisywania certyfikatu oraz kontrolę dostępu opartą na rolach (RBAC).

## <a name="attestation"></a>Zaświadczanie

Zaświadczanie gwarantuje integralność bitów oprogramowania, co jest ważne w przypadku wykrywania i zapobiegania złośliwemu oprogramowaniu. Azure IoT Edge Framework zabezpieczeń klasyfikuje zaświadczanie w ramach trzech głównych kategorii:

* Zaświadczanie statyczne
* Zaświadczanie środowiska uruchomieniowego
* Zaświadczanie o oprogramowaniu

### <a name="static-attestation"></a>Zaświadczanie statyczne

Zaświadczanie statyczne weryfikuje integralność całego oprogramowania na urządzeniu, w tym system operacyjny, wszystkie środowiska uruchomieniowe i informacje o konfiguracji. Ze względu na to, że zaświadczanie statyczne występuje podczas włączania, często jest nazywane bezpiecznym rozruchem. Struktura zabezpieczeń dla urządzeń IoT Edge obejmuje producentów i zapewnia ochronę bezpiecznych funkcji sprzętowych, które zapewniają statyczne procesy zaświadczania. Procesy te obejmują bezpieczny rozruch i bezpieczne uaktualnienie oprogramowania układowego. Praca w pobliżu współpracy z dostawcami krzemu eliminuje zbędne warstwy oprogramowania układowego, dlatego minimalizuje powierzchnię zagrożenia.

### <a name="runtime-attestation"></a>Zaświadczanie środowiska uruchomieniowego

Gdy system ukończy bezpieczny proces rozruchu, dobrze zaprojektowane systemy powinny wykrywać próby iniekcji złośliwego oprogramowania i podejmować odpowiednie środki zaradcze. Ataki złośliwego oprogramowania mogą wskazywać porty i interfejsy systemu. Jeśli złośliwe podmioty mają fizyczny dostęp do urządzenia, mogą oni zmodyfikować się z samym urządzeniem lub wykorzystać ataki kanału bocznego w celu uzyskania dostępu. Takie malcontent, bez względu na to, czy złośliwe lub nieautoryzowane zmiany konfiguracji nie mogą być wykrywane przez zaświadczenie statyczne, ponieważ jest wstrzykiwane po rozpoczęciu procesu rozruchu. Środki zaradcze oferowane lub wymuszane przez sprzęt urządzenia nie mogą na zewnątrz takich zagrożeń. Struktura zabezpieczeń dla IoT Edge jawnie wywołuje rozszerzenia, które powodują zwalczanie zagrożeń środowiska uruchomieniowego.  

### <a name="software-attestation"></a>Zaświadczanie o oprogramowaniu

Wszystkie systemy w dobrej kondycji, w tym inteligentne systemy brzegowe, wymagają poprawek i uaktualnień. Zabezpieczenia są ważne w przypadku procesów aktualizacji, w przeciwnym razie mogą to być potencjalne wektory zagrożeń. Struktura zabezpieczeń dla IoT Edge wywołuje aktualizacje za pomocą pakietów mierzonych i podpisanych w celu zapewnienia integralności i uwierzytelniania źródła pakietów. Ten standard dotyczy wszystkich systemów operacyjnych i bitów oprogramowania aplikacji.

## <a name="hardware-root-of-trust"></a>Katalog główny sprzętu zaufania

W przypadku wielu inteligentnych urządzeń brzegowych, szczególnie urządzeń, które mogą być fizycznie dostępne przez potencjalną złośliwe podmioty, zabezpieczenia sprzętowe są ostatnią obroną ochrony. Sprzęt odporny na manipulacje jest decydujący dla takich wdrożeń. Azure IoT Edge zachęca dostawców sprzętu krzemowego do oferowania różnych rodzajów sprzętu głównego zaufania w celu uwzględnienia różnych profilów ryzyka i scenariuszy wdrażania. Zaufanie sprzętowe może pochodzić ze wspólnych standardów protokołu zabezpieczeń, takich jak Trusted Platform Module (ISO/IEC 11889) i Trusted Computing Group aparat kompozycji identyfikatora urządzenia (kość). Bezpieczne technologie enklawy, takie jak TrustZones i rozszerzenia funkcji Software Guard (SGX), zapewniają również zaufanie sprzętowe.

## <a name="certification"></a>Certyfikacja

Aby ułatwić klientom podejmowanie świadomych decyzji podczas Azure IoT Edge urządzeń w celu ich wdrożenia, struktura IoT Edge obejmuje wymagania dotyczące certyfikacji. Podstawą tych wymagań są certyfikaty odnoszące się do oświadczeń zabezpieczeń i certyfikacji odnoszących się do weryfikacji implementacji zabezpieczeń. Na przykład certyfikat dotyczący zabezpieczenia polega na tym, że urządzenie IoT Edge używa bezpiecznego sprzętu znanego na ataki z rozruchem. Certyfikat weryfikacji oznacza, że bezpieczny sprzęt został prawidłowo zaimplementowany, aby zaoferować tę wartość na urządzeniu. Zgodnie z zasadą prostoty, struktura próbuje zachować ciężar certyfikacji w minimalnym stopniu.

## <a name="extensibility"></a>Rozszerzalność

Dzięki technologii IoT prowadzącej do różnych rodzajów transformacji biznesowej zabezpieczenia należy rozwijać równolegle w celu rozwiązania powstających scenariuszy. Azure IoT Edge Framework zabezpieczeń rozpoczyna się od solidnej podstawy, na której kompiluje się w celu rozszerzenia do różnych wymiarów, aby uwzględnić:

* Usługi zabezpieczeń pierwszej firmy, takie jak usługa Device Provisioning dla platformy Azure IoT Hub.
* Usługi innych firm, takie jak zarządzane usługi zabezpieczeń dla różnych pionowych aplikacji (na przykład przemysłowych lub opieki zdrowotnej), lub technologie, takie jak monitorowanie zabezpieczeń w sieciach siatkowych lub usługi zaświadczania sprzętu krzemowego, za pomocą bogatej sieci partnerów.
* Starsze systemy obejmują możliwość modernizacji z alternatywnymi strategiami zabezpieczeń, takimi jak używanie bezpiecznych technologii innych niż certyfikaty do uwierzytelniania i zarządzania tożsamościami.
* Bezpieczny sprzęt na potrzeby wdrażania nowych technologii sprzętowych i wkładów partnerów krzemu.

Na koniec Zabezpieczanie inteligentnej krawędzi wymaga od członków współpracy z otwartą społecznością. Te wkłady mogą mieć formę bezpiecznych technologii lub usług. Usługa Azure IoT Edge Security Framework oferuje solidną podstawę dla zabezpieczeń, która jest rozszerzalna dla maksymalnego pokrycia, aby zaoferować ten sam poziom zaufania i integralności w inteligentnej krawędzi, jak w przypadku chmury platformy Azure.  

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat sposobu [zabezpieczania inteligentnej krawędzi](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)Azure IoT Edge.
