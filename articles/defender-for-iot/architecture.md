---
title: Architektura usługi Azure Defender for IoT
description: Dowiedz się więcej o architekturze i przepływie informacji usługi Azure Defender.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/13/2021
ms.author: shhazam
ms.openlocfilehash: 66b960bf874cc46985230e488c749663eff0b835
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621101"
---
# <a name="azure-defender-for-iot-architecture"></a>Architektura usługi Azure Defender for IoT

W tym artykule opisano architekturę systemu funkcjonalnego rozwiązania Defender for IoT. Usługa Azure Defender for IoT oferuje dwa zestawy funkcji, które odpowiadają wymaganiom środowiska, rozwiązanie bez agenta dla organizacji i rozwiązanie oparte na agentach dla konstruktorów urządzeń.

## <a name="agentless-solution-for-organizations"></a>Rozwiązanie bez agenta dla organizacji
### <a name="defender-for-iot-components"></a>Usługa Defender dla składników IoT

Usługa Defender for IoT nawiązuje połączenie zarówno z chmurą platformy Azure, jak i ze składnikami lokalnymi. Rozwiązanie jest przeznaczone do skalowalności w dużych i geograficznie rozproszonych środowiskach z wieloma lokalizacjami zdalnymi. To rozwiązanie umożliwia rozproszoną architekturę wielowarstwową według kraju, regionu, jednostki biznesowej lub strefy. 

Usługa Azure Defender for IoT obejmuje następujące składniki: 

**Wdrożenia połączone z chmurą**

- Usługa Azure Defender dla maszyny wirtualnej lub urządzenia czujnika IoT
- Azure Portal do zarządzania chmurą i integracji z platformą Azure — wskaźnikiem
- Lokalna Konsola zarządzania dla lokalnego zarządzania lokacją
- Osadzony Agent zabezpieczeń (opcjonalnie)

**Wdrożenia Air-gapped (offline)**

- Usługa Azure Defender dla maszyny wirtualnej lub urządzenia czujnika IoT
- Lokalna Konsola zarządzania dla lokalnego zarządzania lokacją

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="Architektura usługi Defender for IoT.":::

### <a name="azure-defender-for-iot-sensors"></a>Usługa Azure Defender dla czujników IoT

Usługa Defender for IoT umożliwia odnajdywanie i ciągłe monitorowanie urządzeń sieciowych. Czujniki zbierają ruch sieciowy usługi ICS przy użyciu pasywnego (bezagentowego) monitorowania na urządzeniach IoT i niezależnych. 
 
Technologia bez wykorzystania agentów zapewnia szeroką widoczność w zakresie rzeczy i niezwiązanych z innymi sieciami, a w ciągu kilku minut jest podłączonych do sieci. Nie ma to wpływu na wydajność sieci i urządzeń sieciowych z powodu nieinwazyjnej metody analizy ruchu sieciowego (NTA). 
 
Korzystanie z opatentowanych, IoT i nieświadomej analizy zachowań oraz funkcji dokładnej inspekcji pakietów (DPI) w warstwie 7 umożliwia analizowanie ponad tradycyjnymi rozwiązaniami opartymi na sygnaturach w celu natychmiastowego wykrywania zaawansowanych rzeczy i nieautoryzowanych zagrożeń (takich jak złośliwe oprogramowanie bez plików). 
  
Usługa Defender for IoT służy do łączenia się z portem SPAN lub siecią NACISKania i natychmiast rozpoczyna wykonywanie wartości DPI na IoT i Nieruch sieciowy. 
 
Zbieranie danych, przetwarzanie, analiza i alerty odbywają się bezpośrednio na czujniku. Jest to idealne rozwiązanie w przypadku lokalizacji z niską przepustowością lub łącznością o dużej opóźnieniu, ponieważ tylko metadane są przesyłane do konsoli zarządzania.

Czujnik obejmuje pięć aparatów wykrywania analiz. Aparaty wyzwalają alerty na podstawie analizy zarówno ruchu w czasie rzeczywistym, jak i ze wstępnie zarejestrowanym ruchem. Dostępne są następujące aparaty: 

#### <a name="protocol-violation-detection-engine"></a>Aparat wykrywania naruszenia protokołu
Aparat wykrywania naruszenia protokołu określa użycie struktur pakietów i wartości pól, które naruszają specyfikacje protokołu ICS, na przykład: wyjątek Modbus i Inicjowanie przestarzałych alertów dotyczących kodu funkcji.

#### <a name="policy-violation-detection-engine"></a>Aparat wykrywania naruszenia zasad
Za pomocą uczenia maszynowego aparat wykrywania naruszeń zasad ostrzega użytkowników o jakimkolwiek odróżnieniu od zachowań linii bazowej, takich jak nieautoryzowane użycie określonych kodów funkcji, dostęp do określonych obiektów lub zmiany konfiguracji urządzeń. Na przykład: DeltaV wersja oprogramowania została zmieniona i nieautoryzowane alerty dotyczące programowania PLC. Aparat naruszenia zasad modeluje sieci ICS jako deterministyczne sekwencje stanów i przejść — przy użyciu techniki opatentowanej o nazwie przemysłowe skończone Stany Modeling (IFSM). Aparat wykrywania naruszenia zasad ustanawia linię bazową sieci ICS, dzięki czemu platforma wymaga krótszego okresu szkoleniowego, aby utworzyć linię bazową sieci niż ogólne podejścia matematyczne lub analizy, które zostały pierwotnie opracowane dla nich, a nie w sieciach.

#### <a name="industrial-malware-detection-engine"></a>Aparat wykrywania złośliwego oprogramowania w przemyśle
Aparat wykrywania złośliwego oprogramowania określa zachowania wskazujące obecność znanego złośliwego oprogramowania, takiego jak robak, czerń Energy, Havex, atak wannacry, NotPetya i Triton. 

#### <a name="anomaly-detection-engine"></a>Aparat wykrywania anomalii
Aparat wykrywania anomalii wykrywa nietypową komunikację i zachowania między maszynami (M2M). Przez modelowanie sieci ICS jako deterministycznych sekwencji stanów i przejść platforma wymaga krótszego okresu uczenia niż ogólne podejścia matematyczne lub analiza pierwotnie opracowana dla niego, a nie jako. Program wykrywa również anomalie szybciej, z minimalnymi fałszywymi dodatnimi wynikami. Alerty aparatu wykrywania anomalii obejmują nadmierne próby logowania przy użyciu protokołu SMB i wykrywanie alertów PLC.

#### <a name="operational-incident-detection"></a>Wykrywanie incydentów operacyjnych
Wykrywanie incydentu operacyjnego wykrywa problemy operacyjne, takie jak sporadyczne połączenia, które mogą wskazywać wczesne objawy awarii sprzętu. Na przykład urządzenie jest uważane za odłączone (nieodpowiadające), a firma Siemens S7 zakończyła wysyłanie alertów.

### <a name="management-consoles"></a>Konsole zarządzania
Zarządzanie usługą Azure Defender dla IoT w środowiskach hybrydowych odbywa się za pośrednictwem dwóch portali zarządzania: 
- Konsola czujnika
- Lokalna Konsola zarządzania
- Witryna Azure Portal

### <a name="sensor-console"></a>Konsola czujnika
Wykrywanie czujników są wyświetlane w konsoli czujników, gdzie można je przeglądać, badać i analizować na mapie sieci, spisie urządzeń i w szerokim zakresie raportów, na przykład raporty oceny ryzyka, zapytania wyszukiwania danych i wektory ataków. Konsoli programu można także używać do wyświetlania i obsługi zagrożeń wykrytych przez aparaty czujników, przekazywania informacji do systemów partnerskich, zarządzania użytkownikami i innych.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Usługa Defender for IoT — konsola czujnika":::

### <a name="on-premises-management-console"></a>Lokalna Konsola zarządzania
Lokalna Konsola zarządzania umożliwia operatorom usługi Security Operations Center (SOC) Zarządzanie alertami zagregowanymi z wielu czujników na jednym pulpicie nawigacyjnym i zapewnia ogólny wgląd w kondycję nieobsługiwanych sieci.

Ta architektura zapewnia kompleksowy, ujednolicony widok sieci na poziomie SOC, zoptymalizowaną obsługę alertów oraz kontrolę nad zabezpieczeniami sieci operacyjnej, dzięki czemu podejmowanie decyzji i zarządzania ryzykiem pozostaje nieskazitelnych.

Oprócz wielu dzierżawców, monitorowania, analizy danych i scentralizowanej kontroli zdalnej, konsola zarządzania oferuje dodatkowe narzędzia do konserwacji systemu (takie jak wykluczenie alertów) i w pełni dostosowane funkcje raportowania dla każdego zdalnego urządzenia. Ta skalowalna architektura obsługuje zarządzanie lokalne na poziomie witryny, na poziomie strefy i globalnym zarządzaniu w ramach SOC.

Konsolę zarządzania można wdrożyć w celu skonfigurowania wysokiej dostępności, która udostępnia konsolę zapasową, która okresowo otrzymuje kopie zapasowe wszystkich plików konfiguracji wymaganych do odzyskania. Jeśli konsola podstawowa ulegnie awarii, urządzenia zarządzania lokacjami lokalnymi zostaną automatycznie przełączone w tryb failover w celu synchronizacji z konsolą kopii zapasowej w celu zapewnienia dostępności bez przerw w działaniu.

Ścisła integracja z przepływami pracy SOC i uruchamianie książek umożliwia łatwe określanie priorytetów działań zaradczych i korelację między lokacjami.

- Kompleksowe — zmniejszenie złożoności dzięki pojedynczej ujednoliconej platformie na potrzeby zarządzania urządzeniami, ryzyka i luk w zabezpieczeniach, a także monitorowania zagrożeń z odpowiedzią na zdarzenia.

- Agregacja i korelacja — wyświetlanie, agregowanie i analizowanie danych i alertów zebranych ze wszystkich lokacji.

- Kontroluj wszystkie czujniki — Skonfiguruj i Monitoruj wszystkie czujniki z pojedynczej lokalizacji.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="Zarządzaj wszystkimi alertami i informacjami.":::

### <a name="azure-portal"></a>Azure Portal

Portal Defender for IoT na platformie Azure umożliwia:

- Kup urządzenia rozwiązania

- Instalowanie i aktualizowanie oprogramowania
- Dołączanie czujników do platformy Azure
- Aktualizowanie pakietów analizy zagrożeń

## <a name="agent-based-solution-for-device-builders"></a>Rozwiązanie oparte na agencie dla konstruktorów urządzeń

### <a name="embedded-security-agent-built-in-mode"></a>Osadzony Agent zabezpieczeń: tryb wbudowany

W trybie **wbudowanym** usługa Defender for IoT jest włączona po wybraniu opcji Włącz opcję **zabezpieczenia** w centrum IoT. Zapewnianie monitorowania w czasie rzeczywistym, zaleceń i alertów, wbudowany tryb oferuje jednoetapową widoczność urządzenia i niedopasowane zabezpieczenia. Tryb kompilacji nie wymaga instalacji agenta na żadnym urządzeniu i używa zaawansowanej analizy na zarejestrowanych działaniach w celu przeanalizowania i ochrony urządzenia Field i centrum IoT Hub.

### <a name="embedded-security-agent-enhanced-mode"></a>Osadzony Agent zabezpieczeń: Tryb rozszerzony

W trybie **rozszerzonym** po włączeniu opcji **zabezpieczeń** w centrum IoT Hub i zainstalowaniu agenta usługi Defender dla usługi IoT na urządzeniach Agenci zbierają, agregują i analizują surowe zdarzenia zabezpieczeń z urządzeń. Surowe zdarzenia zabezpieczeń mogą obejmować połączenia IP, tworzenie procesów, logowania użytkowników i inne informacje związane z zabezpieczeniami. Usługa Defender dla agentów urządzeń IoT obsługuje również agregację zdarzeń, co pozwala uniknąć dużej przepływności sieci. Agenci są w wysokim stopniu dostosowywany, co pozwala na korzystanie z nich do określonych zadań, takich jak wysyłanie tylko ważnych informacji z najszybszą umową SLA lub agregowanie szczegółowych informacji o zabezpieczeniach i kontekstu w dużych segmentach, co pozwala uniknąć wyższych kosztów usługi.

Agenci urządzeń i inne aplikacje używają **zestawu SDK komunikatów zabezpieczeń platformy Azure** do wysyłania informacji o zabezpieczeniach do usługi Azure IoT Hub. Usługa IoT Hub pobiera te informacje i przekazuje je do usługi Defender for IoT.

Po włączeniu usługi Defender for IoT oprócz przesyłanych dalej danych usługa IoT Hub wysyła także wszystkie dane wewnętrzne do analizy za pomocą programu Defender for IoT. Te dane obejmują dzienniki operacji w chmurze urządzenia, tożsamości urządzeń i konfigurację centrum. Wszystkie te informacje ułatwiają tworzenie usługi Defender dla potoku analizy IoT.

Usługa Defender dla potoku usługi IoT Analytics odbiera również dodatkowe strumienie analizy zagrożeń z różnych źródeł w ramach firmy Microsoft i partnerów firmy Microsoft. Usługa Defender for IoT cała potoku analizy współpracuje z każdą konfiguracją klienta dokonaną w usłudze (taką jak niestandardowe alerty i użycie zestawu SDK komunikatów o zabezpieczeniach wysyłania).

Za pomocą potoku analizy usługa Defender for IoT łączy wszystkie strumienie informacji w celu generowania zaleceń i alertów z możliwością wykonania akcji. Potok zawiera zarówno reguły niestandardowe utworzone przez badaczy zabezpieczeń, jak i ekspertów, jak również modele uczenia maszynowego poszukujące odchyleń od standardowych zachowań urządzeń i analizy ryzyka.

Zalecenia i alerty usługi Defender for IoT (dane wyjściowe potoku analiz) są zapisywane w obszarze roboczym Log Analytics każdego klienta. Uwzględnianie nieprzetworzonych zdarzeń w obszarze roboczym, a także alertów i zaleceń pozwala na głębokie badanie szczegółowe oraz zapytania wykorzystujące dokładne szczegóły wykrytych podejrzanych działań.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Architektura Micro Agent.":::

## <a name="see-also"></a>Zobacz też

[Defender — często zadawane pytania dotyczące usługi IoT](resources-frequently-asked-questions.md)

[Wymagania wstępne systemu](quickstart-system-prerequisites.md)
