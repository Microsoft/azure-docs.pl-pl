---
title: Azure Security Center bezpłatna usługa vs Azure Defender
description: Dowiedz się więcej o zaletach włączania usługi Azure Defender pod kątem ochrony obciążeń w chmurze w Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: 1825f5be8a4f8a8ddfba931dfbc7e77186b4331f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889454"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center bezpłatna usługa vs Azure Defender
Usługa Azure Defender jest bezpłatna przez pierwsze 30 dni. Po upływie 30 dni, jeśli chcesz kontynuować korzystanie z usługi, automatycznie zaczniemy naliczać opłaty za użycie.

Możesz przeprowadzić uaktualnienie ze strony **ustawień & cenowych** , zgodnie z opisem w [przewodniku szybki start: Włączanie usługi Azure Defender](enable-azure-defender.md). Aby uzyskać szczegółowe informacje o cenach w wybranej walucie i według regionu, zobacz [Security Center Cennik](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Jakie korzyści wiążą się z włączeniem usługi Azure Defender?

Security Center jest oferowane w dwóch trybach:

- **Usługa Azure Defender wyłączona** (bezpłatna) — Security Center bez usługi Azure Defender nie jest dostępna bezpłatnie w przypadku wszystkich subskrypcji platformy Azure, gdy po raz pierwszy zostanie odwiedzany pulpit nawigacyjny Azure Security Center w Azure Portal lub jeśli włączono programowo za pośrednictwem interfejsu API. Użycie tego trybu bezpłatnego zapewnia zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń z możliwością podejmowania działań, które ułatwiają ochronę zasobów platformy Azure.

- **Usługa Azure Defender włączona** — włączenie usługi Azure Defender rozszerza możliwości trybu wolnego do obciążeń działających w prywatnych i innych chmurach publicznych, zapewniając ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Niektóre główne funkcje usługi Azure Defender:

    - **Microsoft Defender for Endpoint** — usługa Azure Defender dla serwerów zawiera [program Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) do kompleksowego wykrywania i reagowania punktów końcowych (EDR). Dowiedz się więcej o zaletach korzystania z usługi Microsoft Defender dla punktów końcowych wraz z usługą Azure Defender w [użyciu zintegrowanego rozwiązania EDR Security Center](security-center-wdatp.md).
    - **Skanowanie luk w zabezpieczeniach maszyn wirtualnych i rejestrów kontenerów** — łatwo Wdrażaj skaner na wszystkich maszynach wirtualnych, które zapewniają najbardziej zaawansowane rozwiązanie do zarządzania lukami w zabezpieczeniach. Wyświetlaj, badaj i Koryguj wyniki bezpośrednio w Security Center. 
    - **Bezpieczeństwo hybrydowe** — Uzyskaj ujednolicony widok zabezpieczeń we wszystkich obciążeniach lokalnych i w chmurze. Stosuj zasady zabezpieczeń i stale oceniaj bezpieczeństwo obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z wielu źródeł, w tym zapór i innych rozwiązań partnerskich.
    - **Alerty ochrony przed zagrożeniami** — Zaawansowana analiza zachowań i Microsoft Intelligent Security Graph zapewniają krawędź nad rozwijającym się atakami cybernetycznymi. Wbudowana analiza behawioralna i uczenie maszynowe mogą identyfikować ataki i wypróbować programy wykorzystujące zero dni. Monitoruj sieci, maszyny i usługi w chmurze pod kątem ataków przychodzących i działań po naruszeniu. Usprawnij badanie przy użyciu interaktywnych narzędzi i kontekstowej analizy zagrożeń.
    - **Kontrola dostępu i aplikacji** (AAC) — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie zaleceń z obsługą uczenia maszynowego przystosowanych do określonych obciążeń do tworzenia list dozwolonych i zablokowanych. Ogranicz obszar ataków sieci z dostępem just-in-Time do portów zarządzania na maszynach wirtualnych platformy Azure. AAC radykalnie zmniejsza narażenie na rozżycie i inne ataki sieciowe.
    - **Funkcje zabezpieczeń kontenerów** — Skorzystaj z funkcji zarządzania lukami w zabezpieczeniach i ochrony przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów. Włączenie **usługi Azure Defender dla rejestrów kontenerów** może zająć do 12 godzin do momentu włączenia wszystkich funkcji. Opłaty są oparte na liczbie unikatowych obrazów kontenerów, które zostały wypchnięte do dołączonego rejestru. Po przeskanowaniu obrazu do tego momentu nie zostanie naliczona opłata za niego, chyba że zostanie on zmodyfikowany i wypychany jeszcze raz.
    - **Ochrona przed zagrożeniami dla zasobów połączonych ze środowiskiem platformy Azure** — usługa Azure Defender obejmuje usługę Azure — natywną ochronę przed zagrożeniami dla usług platformy Azure wspólnych dla wszystkich zasobów: Azure Resource Manager, Azure DNS, warstwy sieci platformy azure i Azure Key Vault. Usługa Azure Defender ma unikatowy wgląd w warstwę zarządzania platformy Azure i warstwę Azure DNS i w związku z tym może chronić zasoby w chmurze połączone z tymi warstwami.


## <a name="faq---pricing-and-billing"></a>Często zadawane pytania — Cennik i rozliczenia 

- [Jak mogę śledzić, kto w organizacji włączył zmiany w usłudze Azure Defender w Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Jakie są plany oferowane przez Security Center?](#what-are-the-plans-offered-by-security-center)
- [Jak mogę włączyć usługę Azure Defender dla mojej subskrypcji?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Czy mogę włączyć usługę Azure Defender dla podzbioru serwerów w mojej subskrypcji?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Jeśli mam już licencję usługi Microsoft Defender for Endpoint dla punktu końcowego, możesz uzyskać rabat za usługę Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Moja subskrypcja ma włączoną usługę Azure Defender dla serwerów, czy płacisz za niedziałającymi serwerami?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Czy zostaną naliczone opłaty za maszyny, na których nie zainstalowano agenta Log Analytics?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Jeśli Agent Log Analytics jest raportowany do wielu obszarów roboczych, czy zostanie naliczona dwa razy?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Jeśli Agent Log Analytics jest zgłaszany do wielu obszarów roboczych, to 500 MB wolnego miejsca do pozyskiwania danych?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Czy 500 w przypadku całego obszaru roboczego lub w odniesieniu do maszyny?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Jakie typy danych są uwzględniane w dziennym wykorzystaniu 500 MB danych?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Jak mogę śledzić, kto w organizacji włączył zmiany w usłudze Azure Defender w Security Center?
Subskrypcje platformy Azure mogą mieć wielu administratorów z uprawnieniami do zmiany ustawień cen. Aby dowiedzieć się, który użytkownik wprowadził zmianę, użyj dziennika aktywności platformy Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Dziennik aktywności platformy Azure przedstawiający zdarzenie zmiany cennika":::

Jeśli informacje o użytkowniku nie są wymienione w kolumnie **zdarzenia zainicjowane przez** , Przejrzyj kod JSON zdarzenia w celu uzyskania odpowiednich informacji.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Eksplorator operacji JSON usługi Azure log":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Jakie są plany oferowane przez Security Center? 
Security Center ma dwie oferty: 

- Bezpłatna usługa Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Jak mogę włączyć usługę Azure Defender dla mojej subskrypcji? 
Aby włączyć usługę Azure Defender dla Twojej subskrypcji, można użyć dowolnego z poniższych sposobów: 

| Metoda                                          | Instrukcje                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center strony Azure Portal | [Włączanie usługi Azure Defender](enable-azure-defender.md)                                                                                                  |
| Interfejs API REST                                        | [Interfejs API cen](/rest/api/securitycenter/pricings)                                                                                                  |
| Interfejs wiersza polecenia platformy Azure                                       | [AZ Security Cennik](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Ceny pakietu](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Czy mogę włączyć usługę Azure Defender dla podzbioru serwerów w mojej subskrypcji?
Nie. Po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji wszystkie serwery w subskrypcji będą chronione za pomocą usługi Azure Defender. 

Alternatywą jest włączenie usługi Azure Defender dla serwerów na poziomie obszaru roboczego Log Analytics. W takim przypadku tylko serwery raportowane w tym obszarze roboczym będą chronione i rozliczane. Jednak niektóre funkcje będą niedostępne. Obejmują one dostęp do maszyn wirtualnych just-in-Time, wykrywanie sieci, zgodność z przepisami, adaptacyjną ochronę sieci, adaptacyjną kontrolę aplikacji i wiele więcej. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Jeśli mam już licencję usługi Microsoft Defender for Endpoint dla punktu końcowego, możesz uzyskać rabat za usługę Azure Defender?
Jeśli masz już licencję na usługę Microsoft Defender for Endpoint, nie musisz uiszczać tej części licencji usługi Azure Defender.

Aby potwierdzić swój rabat, skontaktuj się z zespołem pomocy technicznej Security Center i podaj odpowiedni identyfikator obszaru roboczego, region i informacje o licencji dla każdej odpowiedniej licencji.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Moja subskrypcja ma włączoną usługę Azure Defender dla serwerów, czy płacisz za niedziałającymi serwerami? 
Nie. Po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji nie będą naliczane opłaty za maszyny, które są w stanie niealokowanym. Maszyny są rozliczane zgodnie z ich stanem mocy, jak pokazano w poniższej tabeli:

| Stan        | Opis                                                                                                                                      | Użycie wystąpienia rozliczane |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Uruchamianie     | Maszyna wirtualna jest uruchamiana.                                                                                                                               | Bez opłat            |
| Uruchomienie      | Normalny stan roboczy dla maszyny wirtualnej                                                                                                                    | Rozliczane                |
| Zatrzymywanie     | Jest to stan przejściowy. Po zakończeniu będzie wyświetlana jako zatrzymana.                                                                           | Rozliczane                |
| Zatrzymano      | Maszyna wirtualna została zamknięta z poziomu systemu operacyjnego gościa lub przy użyciu interfejsów API wyłączenie. Sprzęt jest nadal przydzielony do maszyny wirtualnej i pozostaje na hoście. | Rozliczane                |
| Cofanie przydziału | Stan przejściowy. Po zakończeniu maszyna wirtualna będzie wyświetlana jako cofnięta alokacja.                                                                             | Bez opłat            |
| Cofnięto przydział  | Maszyna wirtualna została pomyślnie zatrzymana i usunięta z hosta.                                                                                  | Bez opłat            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Virtual Machines platformy Azure z wyświetloną cofniętą maszyną":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Czy zostaną naliczone opłaty za maszyny, na których nie zainstalowano agenta Log Analytics?
Tak. Po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) w ramach subskrypcji maszyny w tej subskrypcji uzyskują zakres ochrony, nawet jeśli nie zainstalowano agenta log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Jeśli Agent Log Analytics jest raportowany do wielu obszarów roboczych, czy zostanie naliczona dwa razy? 
Tak. Jeśli skonfigurowano agenta Log Analytics w celu wysyłania danych do co najmniej dwóch różnych Log Analytics obszarów roboczych (multihostingu), zostanie naliczona opłata za każdy obszar roboczy z zainstalowanymi rozwiązaniami "Security" lub "chroniącymi przed złośliwym oprogramowaniem". 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Jeśli Agent Log Analytics jest zgłaszany do wielu obszarów roboczych, to 500 MB wolnego miejsca do pozyskiwania danych?
Tak. Jeśli skonfigurowano agenta Log Analytics, aby wysyłał dane do co najmniej dwóch różnych Log Analytics obszarów roboczych (multihostingu), uzyskasz bezpłatne pozyskiwanie danych 500-MB. Jest on obliczany na węzeł, na zgłoszone obszary robocze, dziennie i dostępne dla każdego obszaru roboczego, w którym zainstalowano rozwiązania "Security" lub "chroniące przed złośliwym oprogramowaniem". Zostanie naliczona opłata za dane pozyskiwane w 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Czy 500 w przypadku całego obszaru roboczego lub w odniesieniu do maszyny?
W przypadku każdej maszyny połączonej z obszarem roboczym będzie można korzystać z 500-MB bezpłatnego pozyskiwania danych dziennie. W odniesieniu do typów danych zabezpieczeń bezpośrednio zbieranych przez Azure Security Center.

Przyjmowana jest średnia dzienna ilość danych dla wszystkich węzłów. Nawet jeśli niektóre komputery wysyłają 100 MB, a inne wysyłają 800-MB, jeśli łączna liczba nie przekracza limitów **[liczba maszyn] x 500-MB** , opłata nie zostanie naliczona.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Jakie typy danych są uwzględniane w dziennym wykorzystaniu 500 MB danych?

Rozliczanie Security Center jest ściśle powiązane z rozliczeniami Log Analytics. Security Center zapewnia alokację 500 MB/węzeł/dzień dla następującego podzestawu [typów danych zabezpieczeń](/azure/azure-monitor/reference/tables/tables-category.md#security):
- WindowsEvent
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Aktualizowanie i UpdateSummary typów danych, gdy rozwiązanie Update Management nie jest uruchomione w obszarze roboczym lub jest włączona funkcja określania wartości docelowej rozwiązania

Jeśli obszar roboczy znajduje się w starszej warstwie cenowej węzła, alokacje Security Center i Log Analytics są łączone i stosowane wspólnie do wszystkich danych pozyskanych z rozliczeniami.

## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono opcje cenowe Security Center. W przypadku pokrewnego materiału Zobacz:

- [Jak zoptymalizować koszty obciążeń platformy Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Szczegóły cennika w wybranej walucie i zgodnie z Twoim regionem](https://azure.microsoft.com/pricing/details/security-center/)
- Możesz chcieć zarządzać kosztami i ograniczyć ilość danych zbieranych dla rozwiązania, ograniczając je do określonego zestawu agentów. [Określanie celu rozwiązania](../azure-monitor/insights/solution-targeting.md) pozwala zastosować zakres do rozwiązania i określić podzestaw komputerów w obszarze roboczym. Jeśli używasz funkcji określania celu rozwiązania, Security Center wyświetli obszar roboczy jako niemający rozwiązania.