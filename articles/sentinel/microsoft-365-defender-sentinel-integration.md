---
title: Integracja usługi Microsoft 365 Defender z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, w jaki sposób integracja usługi Microsoft 365 Defender z platformą Azure — wskaźnikiem, umożliwia korzystanie z platformy Azure jako kolejki zdarzeń uniwersalnych przy zachowaniu siły M365D's, aby pomóc w badaniu zdarzeń zabezpieczeń M365, a także jak pozyskiwanie zaawansowanych danych polowania składników usługi Defender do badania na platformie Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747000"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Integracja Microsoft 365 Defender z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik Microsoft 365 Defender jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

> [!IMPORTANT]
>
> Usługa **Microsoft 365 Defender** była znana wcześniej jako **Microsoft Threat Protection** lub **MTP**.
>
> Usługa **Microsoft Defender dla punktu końcowego** była znana wcześniej jako usługa **Microsoft Defender Advanced Threat Protection** lub **MDATP**.
>
> W danym czasie nadal mogą być widoczne stare nazwy.

## <a name="incident-integration"></a>Integracja zdarzeń

Integracja zdarzeń Microsoft 365 usługi Azure wskaźnikowej [(M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) to usługa, która umożliwia przesyłanie strumieniowe wszystkich zdarzeń M365D do usługi Azure wskaźnikowej i utrzymywanie synchronizacji między obydwoma portalami. Zdarzenia z usługi M365D (dawniej znany jako Microsoft Threat Protection lub MTP) obejmują wszystkie skojarzone alerty, jednostki i istotne informacje, dzięki czemu można wykonać Klasyfikacja i wstępną badanie na platformie Azure. Gdy dane są wskazywane przez program, Zdarzenia nadal będą dwukierunkowe zsynchronizowane z usługą M365D, co pozwala korzystać z zalet obu portali w badaniu zdarzeń.

Integracja ta zapewnia Microsoft 365 incydentów zabezpieczeń, które mają być zarządzane z poziomu platformy Azure, jako część głównej kolejki zdarzeń w całej organizacji, dzięki czemu możesz zobaczyć, jak i skorelować zdarzenia M365 ze wszystkimi innymi systemami w chmurze i lokalnymi. W tym samym czasie umożliwia korzystanie z unikatowych mocnych i funkcji M365D w celu uzyskania szczegółowych badań i środowiska specyficznego dla M365 w całym ekosystemie M365. Usługa M365 Defender wzbogaca i grupuje alerty z wielu produktów M365, zmniejszając jednocześnie rozmiar kolejki zdarzeń SOC i skrócić czas do rozwiązania. Usługi składowe, które są częścią stosu M365 Defender, to:

- **Microsoft Defender dla punktu końcowego** (MDE, dawniej MDATP)
- **Microsoft Defender for Identity** (MDI, dawniej AATP)
- **Microsoft Defender dla Office 365** (MDO, dawniej O365ATP)
- **Microsoft Cloud App Security** (MCAS)

Oprócz zbierania alertów z tych składników usługa M365 Defender generuje alerty. Tworzy zdarzenia ze wszystkich tych alertów i wysyła je do usługi Azure wskaźnikowej.

### <a name="common-use-cases-and-scenarios"></a>Typowe przypadki użycia i scenariusze

- Jedno kliknięcie łączenie zdarzeń usługi M365 Defender, w tym wszystkich alertów i jednostek ze składników M365 Defender, do platformy Azure.

- Dwukierunkowa synchronizacja między zdarzeniami wskaźnikowymi i M365D na stanie, właściciela i przyczyny zamknięcia.

- Korzystaj z możliwości grupowania i wzbogacania alertów programu M365 Defender na platformie Azure, co skraca czas do rozwiązania.

- Szczegółowy link w kontekście między zdarzeniem wskaźnikowym platformy Azure a zdarzeniem równoległym usługi M365 Defender, aby ułatwić badanie w obu portalach.

### <a name="connecting-to-microsoft-365-defender"></a>Nawiązywanie połączenia z usługą Microsoft 365 Defender

Po włączeniu łącznika danych programu Microsoft 365 Defender w celu [zbierania zdarzeń i alertów](connect-microsoft-365-defender.md)zdarzenia M365D będą wyświetlane w kolejce zdarzeń wskaźnikowych platformy Azure z **Microsoft 365 Defender** w polu **Nazwa produktu** wkrótce po wygenerowaniu w usłudze M365 Defender.
- Może upłynąć do 10 minut od momentu wygenerowania zdarzenia w usłudze M365 Defender do momentu, w którym pojawia się on na platformie Azure.

- Zdarzenia będą pozyskiwane i synchronizowane bez dodatkowych kosztów.

Po powiązaniu integracji z programem M365 Defender wszystkie łączniki alertów składników (MDE, MDI, MDO, MCAS) zostaną automatycznie połączone w tle, jeśli nie zostały jeszcze zainstalowane. Jeśli jakiekolwiek licencje składników zostały zakupione po nawiązaniu połączenia z usługą M365 Defender, alerty i zdarzenia z nowego produktu będą nadal przepływać do zespołu wskaźnikowego platformy Azure bez dodatkowej konfiguracji ani opłat.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>Zdarzenia M365 Defender i reguły tworzenia zdarzeń firmy Microsoft

- Zdarzenia generowane przez usługę M365 Defender, na podstawie alertów pochodzących z produktów zabezpieczeń M365, są tworzone przy użyciu niestandardowej logiki M365.

- Reguły tworzenia zdarzeń firmy Microsoft w witrynie Azure, które również tworzą zdarzenia z tych samych alertów, używają (innej) niestandardowej logiki wskaźnikowej platformy Azure.

- Używanie obu mechanizmów razem jest w pełni obsługiwane i ta konfiguracja może służyć do ułatwienia przejścia do nowej logiki tworzenia zdarzenia M365 usługi Defender. Spowoduje to jednak utworzenie **zduplikowanych zdarzeń** dla tych samych alertów.

- Aby uniknąć tworzenia zduplikowanych zdarzeń dla tych samych alertów, firma Microsoft zaleca, aby klienci mogli wyłączyć wszystkie **reguły tworzenia zdarzeń firmy Microsoft** dla produktów M365 (MDE, MDI i MDO-patrz MCAS poniżej) podczas nawiązywania połączenia z usługą M365 Defender. Można to zrobić, zaznaczając odpowiednie pole wyboru na stronie łącznik. Należy pamiętać, że jeśli to zrobisz, wszystkie filtry, które zostały zastosowane przez reguły tworzenia zdarzeń, nie zostaną zastosowane do integracji zdarzeń M365 Defender.

- W przypadku alertów Microsoft Cloud App Security (MCAS) nie wszystkie typy alertów są obecnie dołączone do usługi M365 Defender. Aby upewnić się, że nadal trwają zdarzenia dotyczące wszystkich alertów MCAS, należy zachować lub utworzyć **reguły tworzenia zdarzeń firmy Microsoft** dla typów alertów, które *nie zostały* dołączone do usługi M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Praca ze zdarzeniami M365 Defender w systemie Azure — wskaźnikiem i synchronizacją dwukierunkową

Zdarzenia M365 Defender będą wyświetlane w kolejce zdarzeń wskaźnikowych platformy Azure przy użyciu nazwy produktu **Microsoft 365 Defender** i z podobnymi szczegółami i funkcjami do innych zdarzeń związanych ze wskaźnikami kontrolnymi. Każde zdarzenie zawiera łącze z powrotem do zdarzenia równoległego w portalu M365 Defender.

W miarę rozwoju zdarzenia w usłudze M365 Defender i dodawania do niego większej liczby alertów lub jednostek zostanie odpowiednio zaktualizowane zdarzenie dotyczące platformy Azure.

Zmiany wprowadzone do stanu, przyczyny zamknięcia lub przypisania zdarzenia M365, w M365D lub na platformie Azure, są odpowiednio aktualizowane w kolejce zdarzeń innych. Synchronizacja będzie odbywać się w obu portalach natychmiast po zastosowaniu zmiany zdarzenia, bez opóźnień. Aby wyświetlić najnowsze zmiany, może być wymagane odświeżenie.

W programie M365 Defender wszystkie alerty z jednego incydentu można przenieść do innego, co spowodowało scalenie zdarzeń. W takim przypadku incydenty wskaźnikowe platformy Azure będą odzwierciedlać zmiany. Jedno zdarzenie będzie zawierać wszystkie alerty z oryginalnego zdarzenia, a inne zdarzenie zostanie automatycznie zamknięte, a zostanie dodany tag "przekierowane".

> [!NOTE]
> Zdarzenia na platformie Azure (Wskaźnikowanie) mogą zawierać maksymalnie 150 alertów. Zdarzenia M365D mogą mieć więcej niż to możliwe. Jeśli zdarzenie M365D z ponad 150 alertami zostanie zsynchronizowane z usługą Azure wskaźnikowego, zdarzenie wskaźnikowe będzie widoczne jako "150 +" i będzie zawierać link do zdarzenia równoległego w M365D, gdzie zobaczysz pełny zestaw alertów.

## <a name="advanced-hunting-event-collection"></a>Zaawansowane zbieranie zdarzeń polowania

Łącznik usługi Microsoft 365 Defender umożliwia również strumieniowe przesyłanie **zaawansowanych zdarzeń polowania** — typu nieprzetworzonych danych zdarzeń — od Microsoft 365 Defender i usług składowych do platformy Azure. Obecnie można zbierać [Zaawansowane zdarzenia łowiectwa](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) usługi [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) i przesyłać je strumieniowo bezpośrednio do tabel opartych na celu w obszarze roboczym wskaźnikowego platformy Azure. Tabele te są oparte na tym samym schemacie, który jest używany w portalu Microsoft 365 Defender, co zapewnia pełny dostęp do pełnego zestawu zaawansowanych zdarzeń polowania i umożliwia wykonywanie następujących czynności:

- Łatwo Kopiuj istniejące zaawansowane zapytania polowania usługi Microsoft Defender for Endpoint do platformy Azure.

- Za pomocą nieprzetworzonych dzienników zdarzeń można uzyskać dodatkowe informacje dotyczące alertów, polowań i badań oraz skorelować te zdarzenia z innymi osobami z dodatkowych źródeł danych na platformie Azure.

- Przechowuj dzienniki z zwiększonym przechowywaniem, poza programem Microsoft Defender dla punktów końcowych lub Microsoft 365 usługi Defender — domyślnie zatrzymywanie 30 dni. Można to zrobić przez skonfigurowanie przechowywania obszaru roboczego lub skonfigurowanie przechowywania poszczególnych tabel w Log Analytics.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie wyjaśniono, jak korzystać z usługi Microsoft 365 Defender razem z platformą Azure, korzystając z łącznika Microsoft 365 Defender.

- Uzyskaj instrukcje dotyczące [włączania łącznika usługi Microsoft 365 Defender](connect-microsoft-365-defender.md).
- Twórz [alerty niestandardowe](tutorial-detect-threats-custom.md) i [Badaj zdarzenia](tutorial-investigate-cases.md).
