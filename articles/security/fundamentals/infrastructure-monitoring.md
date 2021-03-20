---
title: Monitorowanie infrastruktury platformy Azure
description: Dowiedz się więcej o aspektach monitorowania infrastruktury w sieci produkcyjnej platformy Azure, takich jak skanowanie luk w zabezpieczeniach.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87542946"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorowanie infrastruktury platformy Azure   

## <a name="configuration-and-change-management"></a>Konfiguracja i zarządzanie zmianami
Platforma Azure sprawdza i aktualizuje ustawienia konfiguracji oraz konfiguracje linii bazowej sprzętu, oprogramowania i urządzeń sieciowych. Zmiany są opracowywane, testowane i zatwierdzane przed wprowadzeniem środowiska produkcyjnego ze środowiska deweloperskiego i/lub testowego.

Konfiguracje bazowe wymagane dla usług opartych na systemie Azure są analizowane przez zespół ds. zabezpieczeń i zgodności platformy Azure oraz zespoły usług. Przegląd zespołu usługi jest częścią testów, które są wykonywane przed wdrożeniem ich usługi produkcyjnej.

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach
Zarządzanie aktualizacjami zabezpieczeń pomaga chronić systemy przed znanymi lukami w zabezpieczeniach. Platforma Azure używa zintegrowanych systemów wdrażania do zarządzania dystrybucją i instalacją aktualizacji zabezpieczeń oprogramowania firmy Microsoft. Platforma Azure umożliwia również rysowanie zasobów centrum Microsoft Security Response Center (MSRC). Centrum MSRC identyfikuje, monitoruje, reaguje na i rozwiązuje problemy związane z bezpieczeństwem oraz luki w zabezpieczeniach w chmurze dotyczące zegara, każdego dnia roku.

## <a name="vulnerability-scanning"></a>Skanowanie pod kątem luk w zabezpieczeniach
Skanowanie luk w zabezpieczeniach odbywa się w systemach operacyjnych serwera, bazach danych i urządzeniach sieciowych. Skanowanie w poszukiwaniu luk w zabezpieczeniach jest wykonywane co kwartał co najmniej. Kontrakty platformy Azure z niezależnymi ocenami umożliwiają przeprowadzenie testów penetracji granicy platformy Azure. Ćwiczenia z czerwonym zespołem są również wykonywane rutynowo, a wyniki są używane do ulepszania zabezpieczeń.

## <a name="protective-monitoring"></a>Monitorowanie ochronne
Zabezpieczenia platformy Azure mają zdefiniowane wymagania dotyczące aktywnego monitorowania. Zespoły usług konfigurują aktywne narzędzia monitorowania zgodnie z tymi wymaganiami. Aktywne narzędzia monitorowania obejmują Microsoft Monitoring Agent (MMA) i System Center Operations Manager. Te narzędzia są skonfigurowane do udostępniania alertów czasowych dla pracowników zabezpieczeń platformy Azure w sytuacjach, które wymagają natychmiastowego działania.

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Firma Microsoft implementuje proces zarządzania zdarzeniami zabezpieczeń, aby ułatwić skoordynowane reagowanie na zdarzenia.

Jeśli firma Microsoft będzie wiedzieć, że nieautoryzowany dostęp do danych klienta, które są przechowywane w jego sprzęcie lub w jego obiektach, lub ma świadomość nieautoryzowany dostęp do tego sprzętu lub udogodnień, co spowodowało utratę, ujawnienie lub zmianę danych klienta, firma Microsoft podejmuje następujące działania:

- Bezzwłocznie powiadamia klienta o zdarzeniu zabezpieczeń.
- Program natychmiast bada zdarzenie związane z zabezpieczeniami i dostarcza klientom szczegółowe informacje o zdarzeniu zabezpieczeń.
- Podejmuje odpowiednie czynności i monity, aby ograniczyć efekty i zminimalizować wszelkie szkody wynikające ze zdarzenia zabezpieczeń.

Została ustanowiona struktura zarządzania zdarzeniami, która definiuje role i przydziela obowiązki. Zespół zarządzania zdarzeniami zabezpieczeń platformy Azure jest odpowiedzialny za zarządzanie zdarzeniami bezpieczeństwa, w tym eskalacją i zapewnianie zaangażowania zespołów wyspecjalizowanych w razie potrzeby. Menedżerowie operacji platformy Azure są odpowiedzialni za nadzorowanie badań i rozwiązywania problemów dotyczących zabezpieczeń i ochrony prywatności.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sposobu zabezpieczania infrastruktury platformy Azure przez firmę Microsoft, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
