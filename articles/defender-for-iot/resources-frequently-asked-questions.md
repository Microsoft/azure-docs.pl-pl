---
title: Usługa Defender for IoT — często zadawane pytania
description: Znajdź odpowiedzi na najczęściej zadawane pytania dotyczące funkcji i usługi Azure Defender dla IoT.
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
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 678b50ffb2df2cd16312548109b93f6f1a7f08ff
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247469"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Usługa Azure Defender dla IoT — często zadawane pytania

Ten artykuł zawiera listę często zadawanych pytań i odpowiedzi dotyczących usługi Defender for IoT.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Co to jest wyjątkowa wartość na platformie Azure dla zabezpieczeń IoT?

Usługa Defender for IoT umożliwia przedsiębiorstwom rozszeranie istniejącego widoku zabezpieczeń cybernetycznymi na całe rozwiązanie IoT. Platforma Azure zapewnia kompleksowy wgląd w Twoje rozwiązanie biznesowe, umożliwiając podejmowanie działań i decyzji związanych z prowadzoną działalnością w oparciu o stan zabezpieczeń przedsiębiorstwa i zebrane dane. Połączone zabezpieczenia za pomocą usługi Azure IoT, Azure IoT Edge i Azure Security Center umożliwiają tworzenie potrzebnych rozwiązań przy użyciu żądanych zabezpieczeń.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>Nasza organizacja używa własnych, niestandardowych protokołów przemysłowych. Czy są one obsługiwane? 

Usługa Azure Defender for IoT oferuje kompleksową obsługę protokołów. Oprócz obsługi protokołu osadzonego można zabezpieczyć urządzenia IoT i OT z własnymi i niestandardowymi protokołami lub protokoły, które odbiegają od dowolnego standardu. Korzystając z zestawu SDK open Development Environment (Node), deweloperzy mogą tworzyć wtyczki rozsektorowe, które dekodują ruch sieciowy na podstawie zdefiniowanych protokołów. Ruch jest analizowany przez usługi, aby zapewnić pełne monitorowanie, alerty i raportowanie. Użyj zakresu od do:
- Rozwiń widoczność i kontrolę bez konieczności uaktualniania do nowych wersji.
- Zabezpieczanie informacji własnościowych przez tworzenie w lokacji jako wtyczki zewnętrznej. 
- Lokalizowanie tekstu dla alertów, zdarzeń i parametrów protokołu.

To unikatowe rozwiązanie do tworzenia protokołów jako wtyczek, nie wymaga dedykowanych zespołów deweloperów ani wersji do obsługi nowego protokołu. Deweloperzy, partnerzy i klienci mogą bezpiecznie opracowywać protokoły i dzielić się informacjami i wiedzą przy użyciu horyzontu. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Czy muszę kupić urządzenia sprzętowe od partnerów firmy Microsoft?
Usługa Azure Defender for IoT sensor działa na określonych specyfikacjach sprzętu zgodnie z opisem w [przewodniku dotyczącym specyfikacji sprzętowych](./how-to-identify-required-appliances.md), klienci mogą zakupić Certyfikowany sprzęt od partnerów firmy Microsoft lub użyć dostarczonych materiałów (BOM) i zakupić je samodzielnie. 

Certyfikowany sprzęt został przetestowany w naszym laboratorium pod kątem stabilności sterowników, porzucania pakietów i ustalania rozmiarów sieci.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>Regulacja nie pozwala nam na łączenie systemu z Internetem. Czy nadal możemy używać usługi Defender for IoT?

Tak, możesz! Rozwiązanie "Azure Defender for IoT platform on-premises" jest wdrażane jako fizyczne lub wirtualne urządzenie czujnika, które w sposób pasywny pozyskuje ruch sieciowy (za pośrednictwem zakresów, RSPAN lub TAP) w celu analizowania, odnajdywania i ciągłego monitorowania sieci IT, rzeczy i IoT. W przypadku większych przedsiębiorstw wiele czujników może agregować swoje dane w lokalnej konsoli zarządzania.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>Gdzie w sieci należy połączyć porty monitorowania?

Usługa Azure Defender for IoT sensor nawiązuje połączenie z portem SPAN lub z siecią, a następnie natychmiast rozpoczyna zbieranie ruchu sieciowego usługi ICS za pośrednictwem pasywnego monitorowania (bez agentów). Nie ma ona wpływu na sieci, ponieważ nie jest umieszczana w ścieżce danych i aktywnie nie skanuje urządzeń.

Na przykład:
- Pojedyncze urządzenie (wirtualne) może znajdować się w warstwie DMZ w sklepie produkcyjnym, dzięki czemu cały ruch z komórkowego jest kierowany do tej warstwy.
- Alternatywnie możesz odszukać małe czujniki w każdej komórce warsztatowej z chmurą lub lokalnym zarządzaniem, które będą znajdować się w warstwie DMZ piętra. Inne urządzenie (wirtualne lub fizyczne) może monitorować ruch w warstwie DMZ w sklepie produkcyjnym (w przypadku SCADA, Historian lub MES).

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Jak usługa Defender for IoT jest porównywana z konkursem?

Inne rozwiązania zapewniają zestaw funkcji, które umożliwiają klientom tworzenie własnych rozwiązań, usługa Defender for IoT zapewnia unikatowe kompleksowe rozwiązanie do obsługi zabezpieczeń IoT, które zapewnia szeroki wgląd w zabezpieczenia wszystkich powiązanych zasobów platformy Azure. System Azure umożliwia szybkie wdrażanie i pełną integrację z usługą IoT Hub module bliźniaczych reprezentacji w celu zapewnienia łatwej integracji z istniejącymi narzędziami do zarządzania urządzeniami.


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Czy muszę być klientem usługi Azure IoT?

Tak. W przypadku wdrożeń związanych z chmurą usługa Azure Defender dla IoT korzysta z łączności i infrastruktury Azure IoT.
## <a name="can-i-create-my-own-alerts"></a>Czy mogę tworzyć własne alerty?

Tak. Możesz ustawić dostosowany alert dla wstępnie ustalonego zestawu zachowań, takich jak adres IP i otwarte porty. Zobacz [Tworzenie niestandardowych alertów](quickstart-create-custom-alerts.md) , aby dowiedzieć się więcej o alertach niestandardowych i sposobach ich tworzenia.

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Gdzie mogę zobaczyć dzienniki? Czy mogę dostosować dzienniki?

- Wyświetlanie alertów i zaleceń przy użyciu połączonego obszaru roboczego Log Analytics. Skonfiguruj rozmiar i czas trwania magazynu w obszarze roboczym.

- Dane pierwotne z agenta zabezpieczeń mogą być również przechowywane na koncie Log Analytics. Przed zmianą konfiguracji tej opcji należy wziąć pod uwagę rozmiar, czas trwania, wymagania dotyczące magazynu i powiązane koszty.



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Co się stanie, gdy połączenie internetowe przestanie działać?

Czujniki i agenci kontynuują uruchamianie i przechowywanie danych, o ile urządzenie jest uruchomione. Dane są przechowywane w pamięci podręcznej komunikatów zabezpieczeń zgodnie z konfiguracją rozmiaru. Gdy urządzenie odzyska łączność, komunikaty zabezpieczeń są wznawiane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rozpoczynania pracy z usługą Defender for IoT, zobacz następujące artykuły:

- Zapoznaj się z [omówieniem](overview.md) usługi Defender for IoT
- Sprawdzanie [wymagań wstępnych systemu](quickstart-system-prerequisites.md)
- Dowiedz się więcej na temat [rozpoczynania pracy z usługą Defender for IoT](getting-started.md)
- Informacje [o alertach zabezpieczeń usługi Defender for IoT](concept-security-alerts.md)