---
title: Zalety najważniejszych
description: Dowiedz się więcej na temat podstawowych informacji o usłudze Defender for IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: cb984ca1a74d8b3838b857f2f5679264d1445187
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508612"
---
# <a name="basic-concepts"></a>Podstawowe pojęcia 

W tym artykule opisano najważniejsze zalety usługi Azure Defender dla IoT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Szybkie wdrażanie nieinwazyjne i pasywne monitorowanie

Usługa Defender for IoT służy do łączenia się z portami przełączeń i sieci, a następnie od razu zaczyna zbierać ruch sieciowy usługi ICS za pośrednictwem pasywnego monitorowania (bez agentów). Głębokiej inspekcji pakietów (DPI) używa się do DISSECT ruchu z urządzeń sieciowych z kontrolką zarówno serial i Ethernet. Usługa Defender for IoT nie ma wpływu na nieaktywne sieci, ponieważ nie jest umieszczana w ścieżce danych i nie skanuje urządzeń. 

Aby zapewnić błyskawiczne migawki szczegółowych informacji o urządzeniu z systemem Windows, można skonfigurować usługę Defender for IoT sensor do uzupełniania pasywnego monitorowania z opcjonalnym aktywnym składnikiem. Ten składnik używa bezpiecznych poleceń zatwierdzonych przez dostawcę do wykonywania zapytań dotyczących urządzeń z systemem Windows w celu uzyskania szczegółowych informacji na temat urządzeń, tak często, jak to jest często.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Osadzona znajomość protokołów, urządzeń i aplikacji usługi ICS

Sama wartość DPI jest zbyt mała, aby identyfikować anomalie protokołu i identyfikować urządzenia na poziomie szczegółowości. Czujnik usługi Defender for IoT dotyczy niektórych największych i najbardziej złożonych środowisk. Przeanalizowane ponad 1 300 zarejestrowanych sieci we wszystkich sektorach przemysłowych.

## <a name="analytics-and-self-learning-engines"></a>Analizy i aparaty do uczenia się samodzielnego

Aparaty identyfikują problemy z zabezpieczeniami za pośrednictwem ciągłego monitorowania i pięciu aparatów analitycznych, które obejmują samouczenie się, aby wyeliminować konieczność aktualizowania podpisów lub definiowania reguł. Aparaty wykorzystują analizy behawioralne specyficzne dla usługi ICS i analizują dane, aby stale analizować ruch sieciowy pod kątem anomalii. Pięć aparatów to:

- **Wykrywanie naruszenia protokołu**: Określa użycie struktur pakietów i wartości pól, które naruszają specyfikacje protokołu ICS.

- **Wykrywanie naruszenia zasad**: identyfikuje naruszenia zasad, takie jak nieautoryzowane użycie kodów funkcji, dostęp do określonych obiektów lub zmiany konfiguracji urządzeń.

- **Wykrywanie złośliwego oprogramowania w przemyśle**: identyfikuje zachowania wskazujące obecność znanego złośliwego oprogramowania, takiego jak robak, czarna energia, Havex, atak wannacry i NotPetya.

- **Wykrywanie anomalii**: wykrywanie nietypowej komunikacji i zachowań między maszynami (M2M). Przez modelowanie sieci ICS jako deterministycznych sekwencji stanów i przejść aparat używa metody opatentowanej o nazwie przemysłowe skończone Stany Modeling (IFSM). Rozwiązanie wymaga krótszego okresu uczenia się niż ogólne podejścia matematyczne lub analizy, które pierwotnie zostały opracowane dla nich, a nie jako osobne. Program wykrywa również anomalie szybciej, z minimalnymi fałszywymi dodatnimi wynikami.

- **Wykrywanie zdarzeń operacyjnych**: identyfikuje problemy operacyjne, takie jak sporadyczne połączenia, które mogą wskazywać wczesne objawy awarii sprzętu.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Analiza ruchu sieciowego w celu oceny ryzyka i luk w zabezpieczeniach

Unikatowa w branży usługa Defender for IoT używa własnościowe algorytmy analizy ruchu sieciowego (NTA) w celu pasywnego identyfikowania wszystkich luk w zabezpieczeniach sieci i punktu końcowego, takich jak:

- Nieautoryzowane połączenia dostępu zdalnego
- Nieautoryzowane lub nieudokumentowane urządzenia
- Słabe uwierzytelnianie
- Zagrożone urządzenia (na podstawie nieCVEsch poprawek)
- Nieautoryzowane mostki między podsieciami
- Słabe reguły zapory

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Wyszukiwanie danych w celu badania, dowodowych i łowiectwa zagrożeń

Platforma zapewnia intuicyjny interfejs wyszukiwania danych, który umożliwia szczegółowe wyszukiwanie historycznych danych w całym odpowiednim wymiarze. Przykładami mogą być okresy, adresy IP, adresy MAC i porty. Można również tworzyć zapytania specyficzne dla protokołu na podstawie kodów funkcji, usług protokołów i modułów. PCAPs z pełną wiernośćą są dostępne w celu przeprowadzenia dalszej analizy przechodzenia do szczegółów.

## <a name="sensor-cloud-management-mode"></a>Tryb zarządzania chmurą czujnika

Tryb zarządzania chmurą czujników określa, gdzie są wyświetlane urządzenia, alerty i inne informacje wykryte przez czujnik.

W przypadku **czujników połączonych z chmurą** informacje wykrywane przez czujnik są wyświetlane w konsoli czujnika. Informacje o alertach są dostarczane przez Centrum IoT Hub i mogą być udostępniane innym usługom platformy Azure, na przykład na platformie Azure.

W przypadku **czujników połączonych lokalnie** informacje wykrywane przez czujnik są wyświetlane w konsoli czujnika. Informacje o wykrywaniu są również udostępniane w lokalnej konsoli zarządzania, jeśli czujnik jest podłączony do niego.

## <a name="air-gapped-networks"></a>Sieci gapped powietrze

Jeśli pracujesz w środowisku gappedu, lokalna Konsola zarządzania w usłudze Defender for IoT oferuje w czasie rzeczywistym widok najważniejszych rzeczy i wskaźników ryzyka oraz alerty dla wszystkich Twoich obiektów. Ścisła integracja z przepływami pracy i elementami Runbook programu SOC umożliwia łatwe określanie priorytetów działań zaradczych oraz korelację zagrożeń między lokacjami.  

Usługa Defender for IoT oferuje skonsolidowany widok wszystkich urządzeń. Zawiera również krytyczne informacje o urządzeniach, takie jak Type (PLC, RTU, DC i więcej), producent, model i poziom poprawki oprogramowania układowego, a także informacje o alertach.  

Usługa Defender for IoT umożliwia efektywne zarządzanie wieloma wdrożeniami oraz kompleksowy wgląd w sieć. Usługa Defender for IoT optymalizuje obsługę alertów i kontroluje zabezpieczenia sieci operacyjnej.

Lokalna Konsola zarządzania jest platformą administracyjną opartą na sieci Web, która umożliwia monitorowanie i kontrolowanie działań instalacji czujnika globalnego. Oprócz zarządzania danymi otrzymanymi od wdrożonych czujników, lokalna Konsola zarządzania bezproblemowo integruje dane z różnych zasobów firmy: CMDB, DNS, zapory, interfejsy API sieci Web i inne.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Wyświetlana jest lokalna Konsola zarządzania.":::

Zalecamy zapoznanie się z pojęciami, funkcjami i funkcjami dostępnymi dla czujników przed rozpoczęciem pracy z lokalną konsolą zarządzania.

## <a name="integrations"></a>Integracje

Możesz rozszerzyć możliwości usługi Defender for IoT, udostępniając zarówno informacje o urządzeniu, jak i alercie z systemami partnerskimi. Integracja pomaga przedsiębiorstwom w wykorzystaniu wcześniej silosowych rozwiązań w zakresie zabezpieczeń, aby znacząco ulepszyć widoczność urządzeń i analizę zagrożeń. Integracje pomagają również przedsiębiorstwom przyspieszyć reagowanie na całe systemy i zmniejszyć ryzyko. 

Integracja zmniejsza złożoność i eliminuje jej silosy poprzez integrację z istniejącymi przepływami pracy SOC i stosem zabezpieczeń. Na przykład:

- Rozwiązań Siem, takie jak IBM QRadar, Splunk, ArcSight, LogRhythm i RSA — monitor

- Aranżacja zabezpieczeń i systemy biletówki, takie jak usługi ServiceNow i IBM odporne na błędy

- Bezpieczne rozwiązania dostępu zdalnego, takie jak CyberArk Privileged Session Manager (PSM) i BeyondTrust

- Bezpieczne systemy kontroli dostępu do sieci (NAC), takie jak Aruba ClearPass i Forescout

- Zapory, takie jak Fortinet i Check Point

## <a name="complete-protocol-support"></a>Ukończ obsługę protokołów

Oprócz obsługi protokołu osadzonego można zabezpieczyć urządzenia IoT i ICS z własnymi i niestandardowymi protokołami lub protokoły odbiegające od dowolnego standardu. Korzystając z zestawu SDK open Development Environment (Node), deweloperzy mogą tworzyć wtyczki do rozsektorów, które dekodują ruch sieciowy na podstawie określonych protokołów. Usługi analizują ruch, aby zapewnić pełne monitorowanie, alerty i raportowanie. Użyj zakresu od do:

- Rozwiń widoczność i kontrolę bez konieczności uaktualniania do nowych wersji.

- Zabezpieczanie informacji własnościowych przez Tworzenie lokacji jako wtyczki zewnętrznej.

- Lokalizowanie tekstu dla alertów, zdarzeń i parametrów protokołu.

Ponadto można używać zastrzeżonych alertów protokołu do przekazywania informacji:

- Informacje o wykrywaniu ruchu w oparciu o protokoły i protokoły bazowe w ramach wtyczki własnościowego horyzontu.

- Informacje o kombinacji pól protokołu ze wszystkich warstw protokołu. Na przykład w środowisku z systemem MODBUS może być konieczne wygenerowanie alertu, gdy czujnik wykryje polecenie zapisu w rejestrze pamięci na określonym adresie IP i miejscu docelowym sieci Ethernet. Lub można generować alert, gdy dowolny dostęp zostanie przeprowadzony do określonego adresu IP.

Alerty są wyzwalane, gdy zostaną spełnione warunki reguły alertu dotyczącego horyzontu.

Ponadto praca z alertami niestandardowymi w usłudze Horizon umożliwia pisanie własnych tytułów i komunikatów alertów. Rozpoznane pola protokołu i wartości mogą być osadzone w tekście komunikatu alertu.

Korzystanie z niestandardowych, wyzwalania alertów opartych na warunkach i komunikatów ułatwia identyfikowanie określonych aktywności sieciowych oraz efektywne aktualizowanie zabezpieczeń, IT i zespołów operacyjnych.


## <a name="high-availability"></a>Wysoka dostępność

Zwiększenie odporności wdrożenia usługi Defender for IoT przez zainstalowanie urządzenia wysokiej dostępności w lokalnej konsoli zarządzania. Wdrożenia o wysokiej dostępności zapewniają, że zarządzane czujniki będą stale raportować do aktywnej lokalnej konsoli zarządzania.

To wdrożenie jest implementowane za pomocą pary lokalnej konsoli zarządzania, która obejmuje urządzenie podstawowe i pomocnicze.

## <a name="localization"></a>Lokalizacja

Wiele funkcji konsoli obsługuje szeroką gamę języków.

## <a name="next-step"></a>Następny krok

[Wprowadzenie do usługi Defender for IoT](getting-started.md)
