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
ms.date: 11/24/2020
ms.author: memildin
ms.openlocfilehash: 6c0e92c566dd69cdea14c9c7d346a5ec65f892bf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487953"
---
# <a name="pricing-of-azure-security-center"></a>Cennik Azure Security Center
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. Zapewnia widoczność i kontrolę nad obciążeniami w chmurze hybrydowej, aktywną obroną, która zmniejsza narażenie na zagrożenia oraz Inteligentne wykrywanie, które ułatwiają szybkie rozwijanie zagrożeń cybernetycznymi.


## <a name="free-option-vs-azure-defender-enabled"></a>Opcja bezpłatna a usługa Azure Defender włączona

Security Center jest oferowane w dwóch trybach:

- **Usługa Azure Defender wyłączona** (bezpłatna) — Security Center bez usługi Azure Defender nie jest dostępna bezpłatnie w przypadku wszystkich subskrypcji platformy Azure, gdy po raz pierwszy zostanie odwiedzany pulpit nawigacyjny Azure Security Center w Azure Portal lub jeśli włączono programowo za pośrednictwem interfejsu API. Użycie tego trybu bezpłatnego zapewnia zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń z możliwością podejmowania działań, które ułatwiają ochronę zasobów platformy Azure.

- **Usługa Azure Defender włączona** — włączenie usługi Azure Defender rozszerza możliwości trybu wolnego do obciążeń działających w prywatnych i innych chmurach publicznych, zapewniając ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Niektóre główne funkcje usługi Azure Defender:

    - **Microsoft Defender for Endpoint** — usługa Azure Defender dla serwerów zawiera [program Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) do kompleksowego wykrywania i reagowania punktów końcowych (EDR). Dowiedz się więcej o zaletach korzystania z usługi Microsoft Defender dla punktów końcowych wraz z usługą Azure Defender w [użyciu zintegrowanego rozwiązania EDR Security Center](security-center-wdatp.md).
    - **Skanowanie luk w zabezpieczeniach maszyn wirtualnych i rejestrów kontenerów** — łatwo Wdrażaj skaner na wszystkich maszynach wirtualnych, które zapewniają najbardziej zaawansowane rozwiązanie do zarządzania lukami w zabezpieczeniach. Wyświetlaj, badaj i Koryguj wyniki bezpośrednio w Security Center. 
    - **Bezpieczeństwo hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i w chmurze. Stosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z wielu źródeł, w tym zapór i innych rozwiązań partnerskich.
    - **Alerty ochrony przed zagrożeniami** — Zaawansowana analiza zachowań i Microsoft Intelligent Security Graph zapewniają krawędź nad rozwijającym się atakami cybernetycznymi. Wbudowana analiza behawioralna i uczenie maszynowe mogą identyfikować ataki i wypróbować programy wykorzystujące zero dni. Monitoruj sieci, maszyny i usługi w chmurze pod kątem ataków przychodzących i działań po naruszeniu. Usprawnij badanie przy użyciu interaktywnych narzędzi i kontekstowej analizy zagrożeń.
    - **Kontrola dostępu i aplikacji** (AAC) — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie zaleceń z obsługą uczenia maszynowego przystosowanych do określonych obciążeń do tworzenia list dozwolonych i zablokowanych. Ogranicz obszar ataków sieci z dostępem just-in-Time do portów zarządzania na maszynach wirtualnych platformy Azure. AAC radykalnie zmniejsza narażenie na rozżycie i inne ataki sieciowe.
    - **Funkcje zabezpieczeń kontenerów** — Skorzystaj z funkcji zarządzania lukami w zabezpieczeniach i ochrony przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów. Włączenie **usługi Azure Defender dla rejestrów kontenerów** może zająć do 12 godzin do momentu włączenia wszystkich funkcji. Opłaty są oparte na liczbie unikatowych obrazów kontenerów, które zostały wypchnięte do dołączonego rejestru. Po przeskanowaniu obrazu do tego momentu nie zostanie naliczona opłata za niego, chyba że zostanie on zmodyfikowany i wypychany jeszcze raz. 

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


## <a name="faq---pricing-and-billing"></a>Często zadawane pytania — Cennik i rozliczenia 

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-azure-security-center"></a>Jak mogę śledzić, kto w mojej organizacji włączył zmiany w usłudze Azure Defender w Azure Security Center
Subskrypcje platformy Azure mogą mieć wielu administratorów z uprawnieniami do zmiany ustawień cen. Aby dowiedzieć się, który użytkownik wprowadził zmianę, użyj dziennika aktywności platformy Azure.

Jeśli informacje o użytkowniku nie są wymienione w kolumnie **zdarzenie zainicjowane przez** , należy zapoznać się z informacjami dotyczącymi tego zdarzenia.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Dziennik zdarzeń platformy Azure przedstawiający zdarzenie zmiany cennika":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Jakie są plany oferowane przez Security Center? 
Security Center ma dwie oferty: 

- Bezpłatna usługa Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Jak mogę włączyć usługę Azure Defender dla mojej subskrypcji? 
Aby włączyć usługę Azure Defender dla Twojej subskrypcji, można użyć dowolnego z poniższych sposobów: 

|Metoda  |Instrukcje  |
|---------|---------|
|Azure Security Center strony Azure Portal|[Włączanie usługi Azure Defender](#enable-azure-defender)|
|Interfejs API REST|[Interfejs API cen](/rest/api/securitycenter/pricings)|
|Interfejs wiersza polecenia platformy Azure|[AZ Security Cennik](/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[Ceny pakietu](https://github.com/Azure/Azure-Security-Center/tree/master/Pricing%20%26%20Settings/Azure%20Policy%20definitions/Bundle%20Pricings)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Czy mogę włączyć usługę Azure Defender dla podzbioru serwerów w mojej subskrypcji?
Nie. Po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji wszystkie serwery w subskrypcji będą chronione za pomocą usługi Azure Defender. 

Alternatywą jest włączenie usługi Azure Defender dla serwerów na poziomie obszaru roboczego Log Analytics. W takim przypadku tylko serwery raportowane w tym obszarze roboczym będą chronione i rozliczane. Jednak niektóre funkcje będą niedostępne. Obejmują one dostęp do maszyn wirtualnych just-in-Time, wykrywanie sieci, zgodność z przepisami, adaptacyjną ochronę sieci, adaptacyjną kontrolę aplikacji i wiele więcej. 


### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Moja subskrypcja ma włączoną usługę Azure Defender dla serwerów, czy płacisz za niedziałającymi serwerami? 
Nie. Po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji będzie naliczana stawka godzinowa tylko dla uruchomionych serwerów. Nie zostanie naliczona opłata za żaden serwer, który jest wyłączony, w czasie jego wyłączenia. 

> [!TIP]
> Dotyczy to również innych typów zasobów chronionych przez Security Center. 

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Czy zostaną naliczone opłaty za maszyny, na których nie zainstalowano agenta Log Analytics?
Tak. Po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji maszyny w tej subskrypcji uzyskują zakres ochrony, nawet jeśli nie zainstalowano agenta log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Jeśli Agent Log Analytics jest raportowany do wielu obszarów roboczych, czy zostanie naliczona dwa razy? 
Tak. Jeśli skonfigurowano agenta Log Analytics w celu wysyłania danych do co najmniej dwóch różnych Log Analytics obszarów roboczych (multihostingu), zostanie naliczona opłata za każdy obszar roboczy z zainstalowanymi rozwiązaniami "Security" lub "chroniącymi przed złośliwym oprogramowaniem". 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Jeśli Agent Log Analytics jest zgłaszany do wielu obszarów roboczych, to 500 MB wolnego miejsca do pozyskiwania danych?
Tak. Jeśli skonfigurowano agenta Log Analytics, aby wysyłał dane do co najmniej dwóch różnych Log Analytics obszarów roboczych (multihostingu), uzyskasz bezpłatne pozyskiwanie danych 500-MB. Jest on obliczany na węzeł, na zgłoszone obszary robocze, dziennie i dostępne dla każdego obszaru roboczego, w którym zainstalowano rozwiązania "Security" lub "chroniące przed złośliwym oprogramowaniem". Zostanie naliczona opłata za dane pozyskiwane w 500 MB.


## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono opcje cenowe Security Center. W przypadku pokrewnego materiału Zobacz:

- [Jak zoptymalizować koszty obciążeń platformy Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Szczegóły cennika w wybranej walucie i zgodnie z Twoim regionem](https://azure.microsoft.com/pricing/details/security-center/)
- Możesz chcieć zarządzać kosztami i ograniczyć ilość danych zbieranych dla rozwiązania, ograniczając je do określonego zestawu agentów. [Określanie celu rozwiązania](../azure-monitor/insights/solution-targeting.md) pozwala zastosować zakres do rozwiązania i określić podzestaw komputerów w obszarze roboczym. Jeśli używasz funkcji określania celu rozwiązania, Security Center wyświetli obszar roboczy jako niemający rozwiązania.