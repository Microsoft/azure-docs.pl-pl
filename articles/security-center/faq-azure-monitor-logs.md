---
title: Azure Security Center często zadawane pytania — pytania dotyczące istniejących agentów Log Analytics
description: Często zadawane pytania są odpowiedzi na pytania dla klientów korzystających już z agenta Log Analytics i biorąc pod uwagę Azure Security Center, produkt, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701964"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Często zadawane pytania dotyczące klientów korzystających już z dzienników Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Czy Security Center zastąpić wszystkie istniejące połączenia między maszynami wirtualnymi i obszarami roboczymi?

Jeśli na maszynie wirtualnej jest już zainstalowany agent Log Analytics jako rozszerzenie platformy Azure, Security Center nie zastąpi istniejącego połączenia z obszarem roboczym. Zamiast tego Security Center używa istniejącego obszaru roboczego. Maszyna wirtualna będzie chroniona pod warunkiem, że rozwiązanie "Security" lub "SecurityCenterFree" zostało zainstalowane w obszarze roboczym, w którym jest raportowane. 

Rozwiązanie Security Center jest instalowane w obszarze roboczym wybranym na ekranie zbierania danych, jeśli nie istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych. Dodanie rozwiązania jest automatycznie wdrażane domyślnie dla wszystkich agentów systemu Windows i Linux połączonych z obszarem roboczym Log Analytics. [Określanie celu rozwiązania](../azure-monitor/insights/solution-targeting.md) pozwala zastosować zakres do rozwiązań.

> [!TIP]
> Jeśli Agent Log Analytics jest instalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center nie zainstaluje agenta Log Analytics i monitorowanie zabezpieczeń jest ograniczone.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Czy Security Center instalować rozwiązania na moich istniejących Log Analytics obszarach roboczych? Jakie są implikacje rozliczeń?
Gdy Security Center określa, że maszyna wirtualna jest już połączona z utworzonym obszarem roboczym, Security Center umożliwia korzystanie z rozwiązań w tym obszarze roboczym zgodnie z konfiguracją cennika. Rozwiązania są stosowane tylko do odpowiednich maszyn wirtualnych platformy Azure, za pośrednictwem [określania wartości docelowej rozwiązania](../azure-monitor/insights/solution-targeting.md), więc rozliczenia pozostają takie same.

- **Usługa Azure Defender wyłączona** — Security Center instaluje rozwiązanie "SecurityCenterFree" w obszarze roboczym. Opłata nie zostanie naliczona.
- **Azure Defender on** — Security Center instaluje rozwiązanie "zabezpieczenia" w obszarze roboczym.

   ![Rozwiązania w domyślnym obszarze roboczym](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Mam już obszary robocze w moim środowisku, czy mogę ich używać do zbierania danych zabezpieczeń?
Jeśli na maszynie wirtualnej jest już zainstalowany agent Log Analytics jako rozszerzenie platformy Azure, Security Center używa istniejącego połączonego obszaru roboczego. Rozwiązanie Security Center jest zainstalowane w obszarze roboczym, jeśli nie istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych za pośrednictwem [określania celu rozwiązania](../azure-monitor/insights/solution-targeting.md).

Gdy Security Center instaluje agenta Log Analytics na maszynach wirtualnych, używa domyślnych obszarów roboczych utworzonych przez Security Center, jeśli Security Center nie zostanie wskazany istniejącym obszarem roboczym.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Mam już rozwiązanie zabezpieczeń w obszarze Moje obszary robocze. Jakie są implikacje rozliczeń?
Rozwiązanie Security & Audit służy do włączania **usługi Azure Defender dla serwerów**. Jeśli rozwiązanie Security & Audit jest już zainstalowane w obszarze roboczym, Security Center używa istniejącego rozwiązania. Nie wprowadzono zmian w rozliczeniach.