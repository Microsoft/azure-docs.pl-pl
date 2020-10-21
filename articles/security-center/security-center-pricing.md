---
title: Cennik Azure Security Center
description: Azure Security Center jest oferowana w dwóch trybach z usługą Azure Defender i bez niej.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: d6c3fff3ed265cb910388a2c9456b8a3f9d63c8b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281199"
---
# <a name="pricing-of-azure-security-center"></a>Cennik Azure Security Center
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. Zapewnia widoczność i kontrolę nad obciążeniami w chmurze hybrydowej, aktywną obroną, która zmniejsza narażenie na zagrożenia oraz Inteligentne wykrywanie, które ułatwiają szybkie rozwijanie ataków cybernetycznymi.


## <a name="free-option-vs-azure-defender-enabled"></a>Opcja bezpłatna a usługa Azure Defender włączona

Security Center jest oferowane w dwóch trybach:

- **Usługa Azure Defender wyłączona** (bezpłatna) — Security Center bez usługi Azure Defender nie jest dostępna bezpłatnie w przypadku wszystkich subskrypcji platformy Azure, gdy po raz pierwszy zostanie odwiedzany pulpit nawigacyjny Azure Security Center w Azure Portal lub jeśli włączono programowo za pośrednictwem interfejsu API. Użycie tego trybu bezpłatnego zapewnia zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń z możliwością podejmowania działań, które ułatwiają ochronę zasobów platformy Azure.

- **Usługa Azure Defender włączona** — włączenie usługi Azure Defender rozszerza możliwości trybu wolnego do obciążeń działających w prywatnych i innych chmurach publicznych, zapewniając ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Niektóre główne funkcje usługi Azure Defender:

    - **Microsoft Defender for Endpoint** — usługa Azure Defender dla serwerów zawiera [program Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) do kompleksowego wykrywania i reagowania punktów końcowych (EDR). Dowiedz się więcej o zaletach korzystania z usługi Microsoft Defender dla punktów końcowych wraz z usługą Azure Defender w [użyciu zintegrowanego rozwiązania EDR Security Center](security-center-wdatp.md).
    - **Skanowanie luk w zabezpieczeniach maszyn wirtualnych i rejestrów kontenerów** — łatwo Wdrażaj skaner na wszystkich maszynach wirtualnych, które zapewniają najbardziej zaawansowane rozwiązanie do zarządzania lukami w zabezpieczeniach. Wyświetlaj, badaj i Koryguj wyniki bezpośrednio w Security Center. 
    - **Bezpieczeństwo hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i w chmurze. Stosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z wielu źródeł, w tym zapór i innych rozwiązań partnerskich.
    - **Alerty ochrony przed zagrożeniami** — Zaawansowana analiza zachowań i Microsoft Intelligent Security Graph zapewniają krawędź nad rozwijającym się atakami cybernetycznymi. Korzystaj z wbudowanej analizy behawioralnej i uczenia maszynowego, aby identyfikować ataki i wypróbować programy wykorzystujące zero dni. Monitoruj sieci, maszyny i usługi w chmurze pod kątem ataków przychodzących i działań po naruszeniu. Usprawnij badanie przy użyciu interaktywnych narzędzi i kontekstowej analizy zagrożeń.
    - **Kontrola dostępu i aplikacji** — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie zaleceń z obsługą uczenia maszynowego przystosowanych do określonych obciążeń do tworzenia list dozwolonych i zablokowanych. Ogranicz obszar ataków sieci z dostępem just-in-Time do portów zarządzania na maszynach wirtualnych platformy Azure. Radykalnie zmniejsza to narażenie na rozżycie i inne ataki sieciowe.
    - **Funkcje zabezpieczeń kontenerów** — Skorzystaj z funkcji zarządzania lukami w zabezpieczeniach i ochrony przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów. Po włączeniu **usługi Azure Defender dla rejestrów kontenerów**może upłynąć do 12hrs do momentu włączenia wszystkich funkcji. Opłaty są oparte na liczbie unikatowych obrazów kontenerów, które zostały wypchnięte do dołączonego rejestru. Po przeskanowaniu obrazu do tego momentu nie zostanie naliczona opłata za niego, chyba że zostanie on zmodyfikowany i wypychany jeszcze raz. 

## <a name="try-azure-defender-free-for-30-days"></a>Wypróbuj usługę Azure Defender bezpłatnie przez 30 dni

Usługa Azure Defender jest bezpłatna przez pierwsze 30 dni. Po upływie 30 dni, jeśli chcesz kontynuować korzystanie z usługi, automatycznie zaczniemy naliczać opłaty za użycie.

## <a name="enable-azure-defender"></a>Włączanie usługi Azure Defender

Możesz chronić całą subskrypcję platformy Azure za pomocą usługi Azure Defender, a ochrona będzie dziedziczona przez wszystkie zasoby w ramach subskrypcji.

Aby włączyć usługę Azure Defender:

1. W menu głównym Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz subskrypcję, którą chcesz uaktualnić.
1. Wybierz pozycję **Azure Defender on** , aby przeprowadzić uaktualnienie.
1. Wybierz pozycję **Zapisz**.

Poniżej znajduje się Strona z cennikiem przykładowej subskrypcji. Należy zauważyć, że każdy plan w usłudze Azure Defender jest naliczany osobno i może być ustawiony indywidualnie lub wyłączony.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Strona cennika Security Center w portalu":::

> [!NOTE]
> Aby włączyć wszystkie funkcje Security Center w tym funkcje ochrony przed zagrożeniami, należy włączyć usługę Azure Defender w ramach subskrypcji zawierającej odpowiednie obciążenia. Włączenie tej opcji na poziomie obszaru roboczego nie pozwala na dostęp do programu VM, adaptacyjne kontrolki aplikacji i wykrywanie sieci dla zasobów platformy Azure. Ponadto jedyne plany usługi Azure Defender dostępne na poziomie obszaru roboczego to usługa Azure Defender dla serwerów i usługi Azure Defender dla serwerów SQL na komputerach.
>
> **Usługę Azure Defender można włączyć dla kont magazynu** na poziomie subskrypcji lub poziomu zasobów.
> **Usługę Azure Defender dla programu SQL** można włączyć na poziomie subskrypcji lub na poziomie zasobu.
> Ochronę przed zagrożeniami dla **Azure Database for MariaDB/MySQL/PostgreSQL** można włączyć tylko na poziomie zasobów.


## <a name="next-steps"></a>Następne kroki
W tym artykule wprowadzono cenniki dotyczące Security Center. W przypadku pokrewnego materiału Zobacz:

- [Jak zoptymalizować koszty obciążeń platformy Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Szczegóły cennika w wybranej walucie i zgodnie z Twoim regionem](https://azure.microsoft.com/pricing/details/security-center/)
- Możesz chcieć zarządzać kosztami i ograniczyć ilość danych zbieranych dla rozwiązania, ograniczając je do określonego zestawu agentów. [Określanie celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres do rozwiązania i określić podzestaw komputerów w obszarze roboczym. W przypadku korzystania z funkcji określania wartości docelowej rozwiązania Security Center zawiera listę obszarów roboczych bez rozwiązania.