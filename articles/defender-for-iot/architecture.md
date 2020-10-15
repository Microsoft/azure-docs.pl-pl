---
title: Architektura usługi Azure Defender for IoT
description: Dowiedz się więcej o architekturze i przepływie informacji usługi Azure Defender.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: ea62592ed155215b14666d0d56e09dbb1a83ed6e
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093467"
---
# <a name="azure-defender-for-iot-architecture"></a>Architektura usługi Azure Defender for IoT

W tym artykule opisano architekturę systemu funkcjonalnego rozwiązania Defender for IoT.

## <a name="defender-for-iot-components"></a>Usługa Defender dla składników IoT

Usługa Defender for IoT nawiązuje połączenie zarówno z chmurą platformy Azure, jak i ze składnikami lokalnymi. Rozwiązanie jest przeznaczone do skalowalności w dużych i geograficznie rozproszonych środowiskach z wieloma lokalizacjami zdalnymi. To rozwiązanie umożliwia rozproszoną architekturę wielowarstwową według kraju, regionu, jednostki biznesowej lub strefy. 

Usługa Azure Defender for IoT obejmuje następujące składniki: 
- Usługa Azure Defender dla czujników IoT
- Konsole zarządzania:
    - Konsola czujnika
    - Lokalna Konsola zarządzania
    - Azure Portal
- Osadzony Agent zabezpieczeń (moduł zabezpieczeń i zestaw IoT SDK)

![Architektura usługi Defender for IoT](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>Usługa Azure Defender dla czujników IoT

Usługa Defender dla czujników IoT odnajduje i stale monitoruje urządzenia sieciowe. Czujniki zbierają ruch sieciowy usługi ICS przy użyciu pasywnego (bezagentowego) monitorowania na urządzeniach IoT i niezależnych. 
 
Technologia bez wykorzystania agentów zapewnia szeroką widoczność w zakresie rzeczy i niezwiązanych z innymi sieciami, a w ciągu kilku minut jest podłączonych do sieci. Nie ma to wpływu na wydajność sieci i urządzeń sieciowych z powodu nieinwazyjnej metody analizy ruchu sieciowego (NTA). 
 
Korzystanie z opatentowanych, IoT i nieświadomej analizy zachowań oraz funkcji dokładnej inspekcji pakietów (DPI) w warstwie 7 umożliwia analizowanie ponad tradycyjnymi rozwiązaniami opartymi na sygnaturach w celu natychmiastowego wykrywania zaawansowanych rzeczy i nieautoryzowanych zagrożeń (takich jak złośliwe oprogramowanie bez plików). 
  
Usługa Defender dla czujników IoT nawiązuje połączenie z portem SPAN lub z siecią, a następnie natychmiast rozpoczyna wykonywanie wartości DPI na IoT i Nieruch sieciowy. 
 
Zbieranie danych, przetwarzanie, analiza i alerty odbywają się bezpośrednio na czujniku. Jest to idealne rozwiązanie w przypadku lokalizacji z niską przepustowością lub łącznością o dużej opóźnieniu, ponieważ tylko metadane są przesyłane do konsoli zarządzania.

Czujnik obejmuje pięć aparatów wykrywania analiz. Aparaty wyzwalają alerty na podstawie analizy zarówno ruchu w czasie rzeczywistym, jak i ze wstępnie zarejestrowanym ruchem. Dostępne są następujące aparaty: 

#### <a name="protocol-violation-detection-engine"></a>Aparat wykrywania naruszenia protokołu
Aparat wykrywania naruszenia protokołu określa użycie struktur pakietów i wartości pól, które naruszają specyfikacje protokołu ICS, na przykład: wyjątek Modbus i Inicjowanie przestarzałych alertów dotyczących kodu funkcji.

#### <a name="policy-violation-detection-engine"></a>Aparat wykrywania naruszenia zasad
Za pomocą uczenia maszynowego aparat wykrywania naruszeń zasad ostrzega użytkowników o jakimkolwiek odróżnieniu od zachowań linii bazowej, takich jak nieautoryzowane użycie określonych kodów funkcji, dostęp do określonych obiektów lub zmiany konfiguracji urządzeń. Na przykład: DeltaV wersja oprogramowania została zmieniona i nieautoryzowane alerty dotyczące programowania PLC. Aparat naruszenia zasad modeluje sieci ICS jako deterministyczne sekwencje stanów i przejść — przy użyciu techniki opatentowanej o nazwie przemysłowe skończone Stany Modeling (IFSM). Aparat wykrywania naruszenia zasad ustanawia linię bazową sieci ICS, dzięki czemu platforma wymaga krótszego okresu szkoleniowego, aby utworzyć linię bazową sieci niż ogólne podejścia matematyczne lub analizy, które zostały pierwotnie opracowane dla nich, a nie w sieciach.

#### <a name="industrial-malware-detection-engine"></a>Aparat wykrywania złośliwego oprogramowania w przemyśle
Aparat wykrywania złośliwego oprogramowania określa zachowania wskazujące obecność znanego złośliwego oprogramowania, takiego jak robak, czerń Energy, Havex, atak wannacry, NotPetya i Triton. 

#### <a name="anomaly-detection-engine"></a>Aparat wykrywania anomalii
Aparat wykrywania anomalii wykrywa nietypową komunikację i zachowania między maszynami (M2M). Przez modelowanie sieci ICS jako deterministycznych sekwencji stanów i przejść platforma wymaga krótszego okresu uczenia niż ogólne podejścia matematyczne lub analiza pierwotnie opracowana dla niego, a nie jako. Program wykrywa również anomalie szybciej, z minimalnymi fałszywymi dodatnimi wynikami. Alerty aparatu wykrywania anomalii obejmują nadmierne próby zalogowania do protokołu SMB i wykryto alerty dotyczące skanowania PLC.

#### <a name="operational-incident-detection"></a>Wykrywanie incydentów operacyjnych
Wykrywanie incydentu operacyjnego wykrywa problemy operacyjne, takie jak sporadyczne połączenia, które mogą wskazywać wczesne objawy awarii sprzętu. Na przykład urządzenie jest podejrzane o odłączenie (nieodpowiadanie), a polecenie Siemens S7 zatrzymał PLC do wysłania alertów.


### <a name="management-consoles"></a>Konsole zarządzania
Zarządzanie usługą Azure Defender dla IoT w środowiskach hybrydowych odbywa się za pośrednictwem dwóch portali zarządzania: 
- Konsola czujnika
- Lokalna Konsola zarządzania
- Witryna Azure Portal

#### <a name="sensor-console"></a>Konsola czujnika
Wykrywanie czujników są wyświetlane w konsoli czujników, gdzie można je przeglądać, badać i analizować w mapie sieci, spisie zasobów i w szerokim zakresie raportów, na przykład raporty oceny ryzyka, zapytania wyszukiwania danych i wektory ataków. Konsoli programu można także używać do wyświetlania i obsługi zagrożeń wykrytych przez aparaty czujników, przekazywania informacji do systemów innych firm, zarządzania użytkownikami i innych.

![Usługa Defender for IoT — konsola czujnika](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>Lokalna Konsola zarządzania
Lokalna Konsola zarządzania umożliwia operatorom usługi Security Operations Center (SOC) Zarządzanie alertami zagregowanymi z wielu czujników na jednym pulpicie nawigacyjnym i zapewnia ogólny wgląd w kondycję nieobsługiwanych sieci.

Ta architektura zapewnia kompleksowy, ujednolicony widok sieci na poziomie SOC, zoptymalizowaną obsługę alertów oraz kontrolę nad zabezpieczeniami sieci operacyjnej, dzięki czemu podejmowanie decyzji i zarządzania ryzykiem pozostaje nieskazitelnych.

Oprócz wielu dzierżawców, monitorowania, analizy danych i scentralizowanej kontroli zdalnej, konsola zarządzania oferuje dodatkowe narzędzia do konserwacji systemu (takie jak wykluczenie alertów) i w pełni dostosowane funkcje raportowania dla każdego zdalnego urządzenia. Ta skalowalna architektura obsługuje zarządzanie lokalne na poziomie witryny, na poziomie strefy i globalnym zarządzaniu w ramach SOC.

Konsolę zarządzania można wdrożyć w celu skonfigurowania wysokiej dostępności, która udostępnia konsolę zapasową, która okresowo otrzymuje kopie zapasowe wszystkich plików konfiguracji wymaganych do odzyskania. Jeśli konsola główna nie powiedzie się, urządzenia zarządzania lokacją lokalną zostaną automatycznie przełączone w tryb failover w celu synchronizacji z konsolą kopii zapasowej w celu zapewnienia dostępności bez przerw w działaniu.

#### <a name="azure-portal"></a>Azure Portal

Portal Defender for IoT na platformie Azure jest używany w celu ułatwienia: ·   Kup urządzenia rozwiązania ·   Instalowanie i aktualizowanie oprogramowania ·   Dołączanie czujników do platformy Azure ·   Aktualizowanie pakietów analizy zagrożeń

## <a name="embedded-security-agent-built-in-mode"></a>Osadzony Agent zabezpieczeń: tryb wbudowany

W trybie **wbudowanym** usługa Defender for IoT jest włączona po wybraniu opcji Włącz opcję **zabezpieczenia** w IoT Hub. Zapewnianie monitorowania w czasie rzeczywistym, zaleceń i alertów, wbudowany tryb oferuje jednoetapową widoczność urządzenia i niedopasowane zabezpieczenia. Tryb kompilacji nie wymaga instalacji agenta na żadnym urządzeniu i używa zaawansowanej analizy na zarejestrowanych działaniach w celu przeanalizowania i ochrony urządzenia Field i centrum IoT Hub.

## <a name="embedded-security-agent-enhanced-mode"></a>Osadzony Agent zabezpieczeń: Tryb rozszerzony

W trybie **rozszerzonym** po włączeniu opcji **zabezpieczeń** w IoT Hub i zainstalowaniu agentów usługi Defender dla IoT na urządzeniach Agenci zbierają, agregują i analizują surowe zdarzenia zabezpieczeń z urządzeń. Surowe zdarzenia zabezpieczeń mogą obejmować połączenia IP, tworzenie procesów, logowania użytkowników i inne informacje związane z zabezpieczeniami. Usługa Defender for IoT Agents obsługuje również agregację zdarzeń, co pozwala uniknąć dużej przepływności sieci. Agenci są w wysokim stopniu dostosowywany, co pozwala na korzystanie z nich do określonych zadań, takich jak wysyłanie tylko ważnych informacji z najszybszą umową SLA lub agregowanie szczegółowych informacji o zabezpieczeniach i kontekstu w dużych segmentach, co pozwala uniknąć wyższych kosztów usługi.

Agenci urządzeń i inne aplikacje używają **zestawu SDK komunikatów zabezpieczeń platformy Azure** do wysyłania informacji o zabezpieczeniach do usługi Azure IoT Hub. IoT Hub pobiera te informacje i przekazuje je do usługi Defender for IoT.

Po włączeniu usługi Defender for IoT (oprócz danych przekazywanych dalej) IoT Hub wysyła także wszystkie dane wewnętrzne do analizy przez usługę Defender for IoT. Te dane obejmują dzienniki operacji w chmurze urządzenia, tożsamości urządzeń i konfigurację centrum. Wszystkie te informacje ułatwiają tworzenie usługi Defender dla potoku analizy IoT.

Usługa Defender dla potoku usługi IoT Analytics odbiera również dodatkowe strumienie analizy zagrożeń z różnych źródeł w ramach firmy Microsoft i partnerów firmy Microsoft. Usługa Defender for IoT cała potoku analizy współpracuje z każdą konfiguracją klienta dokonaną w usłudze (taką jak niestandardowe alerty i użycie zestawu SDK komunikatów o zabezpieczeniach wysyłania).

Za pomocą potoku analizy usługa Defender for IoT łączy wszystkie strumienie informacji w celu generowania zaleceń i alertów z możliwością wykonania akcji. Potok zawiera zarówno reguły niestandardowe utworzone przez badaczy zabezpieczeń, jak i ekspertów, jak również modele uczenia maszynowego poszukujące odchyleń od standardowych zachowań urządzeń i analizy ryzyka.

Zalecenia i alerty usługi Defender for IoT (dane wyjściowe potoku analiz) są zapisywane w obszarze roboczym Log Analytics każdego klienta. Uwzględnianie nieprzetworzonych zdarzeń w obszarze roboczym, a także alertów i zaleceń pozwala na głębokie badanie szczegółowe oraz zapytania wykorzystujące dokładne szczegóły wykrytych podejrzanych działań.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o podstawowej architekturze i przepływie pracy usługi Defender for IoT. Aby dowiedzieć się więcej o wymaganiach wstępnych, sposobach rozpoczynania i włączania rozwiązania zabezpieczeń w IoT Hub, zobacz następujące artykuły:

- [Wymagania wstępne usługi](service-prerequisites.md)
- [Rozpoczęcie pracy](getting-started.md)
- [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
- [Włącz zabezpieczenia w IoT Hub](quickstart-onboard-iot-hub.md)
- [Defender — często zadawane pytania dotyczące usługi IoT](resources-frequently-asked-questions.md)
- [Alerty zabezpieczeń usługi Defender dla IoT](concept-security-alerts.md)
