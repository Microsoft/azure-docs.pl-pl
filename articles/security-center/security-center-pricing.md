---
title: Azure Security Center bezpłatne a Azure Defender włączone
description: Dowiedz się więcej o korzyściach związanych z włączaniem ochrony Azure Defender obciążeń w chmurze w Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/23/2021
ms.openlocfilehash: aa65989953f761ff915383fcb59da7f36ea98dab
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600486"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center bezpłatne a Azure Defender włączone
Azure Defender jest bezpłatna przez pierwsze 30 dni. Po 30 dniach, jeśli zdecydujesz się kontynuować korzystanie z usługi, automatycznie rozpoczniemy naliowanie opłat za użycie.

Uaktualnienie można uaktualnić na stronie **ustawień &** cenowych, zgodnie z opisem w przewodniku [Szybki start: włączanie Azure Defender.](enable-azure-defender.md) Aby uzyskać szczegółowe informacje o cenach w wybranej walucie i w zależności od regionu, [zobacz Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Jakie są zalety włączenia Azure Defender?

Security Center jest oferowany w dwóch trybach:

- **Azure Defender OFF** (bezpłatna) — usługa Security Center bez usługi Azure Defender jest włączona bezpłatnie we wszystkich twoich subskrypcjach platformy Azure podczas pierwszego odwiedzania pulpitu nawigacyjnego usługi Azure Security Center w usłudze Azure Portal lub jeśli jest włączona programowo za pośrednictwem interfejsu API. Korzystanie z tego trybu bezpłatnego zapewnia zasady zabezpieczeń, ciągłą ocenę zabezpieczeń i zalecenia dotyczące zabezpieczeń z akcjami, które ułatwiają ochronę zasobów platformy Azure.

- **Azure Defender WŁ.** — włączenie usługi Azure Defender rozszerza możliwości trybu wolnego na obciążenia działające w chmurach prywatnych i innych chmurach publicznych, zapewniając ujednolicone zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w obciążeniach chmury hybrydowej. Niektóre z głównych funkcji Azure Defender:

    - **Usługa Microsoft Defender dla punktu końcowego** — Azure Defender dla serwerów zawiera usługę [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) w celu kompleksowego wykrywania punktów końcowych i reagowania na nie (EDR). Dowiedz się więcej o korzyściach wynikających z używania usługi Microsoft Defender dla punktu końcowego razem z Azure Defender, Security Center korzystanie ze zintegrowanego rozwiązania [EDR](security-center-wdatp.md)firmy Security Center.
    - **Skanowanie luk w zabezpieczeniach** maszyn wirtualnych i rejestrów kontenerów — łatwe wdrażanie skanera na wszystkich maszynach wirtualnych, które zapewnia najbardziej zaawansowane w branży rozwiązanie do zarządzania lukami w zabezpieczeniach. Wyświetlaj, badaj i koryguj wyniki bezpośrednio w Security Center. 
    - **Zabezpieczenia hybrydowe** — uzyskaj ujednolicony widok zabezpieczeń dla wszystkich obciążeń lokalnych i w chmurze. Stosowanie zasad zabezpieczeń i ciągła ocena zabezpieczeń obciążeń chmury hybrydowej w celu zapewnienia zgodności ze standardami zabezpieczeń. Zbieranie, wyszukiwanie i analizowanie danych zabezpieczeń z wielu źródeł, w tym zapór i innych rozwiązań partnerskich.
    - **Alerty ochrony przed** zagrożeniami — zaawansowana analiza behawioralna i Microsoft Intelligent Security Graph zapewniają przewagę nad rozwijającymi się cyberatakami. Wbudowana analiza behawioralna i uczenie maszynowe mogą identyfikować ataki i luki w zabezpieczeniach typu zero-day. Monitoruj sieci, maszyny i usługi w chmurze pod zakresie ataków przychodzących i działań po naruszeniu zabezpieczeń. Usprawnij badanie za pomocą interaktywnych narzędzi i kontekstowej analizy zagrożeń.
    - **Kontrola dostępu i aplikacji** (AAC) — blokowanie złośliwego oprogramowania i innych niechcianych aplikacji przez zastosowanie rekomendacji opartych na uczeniu maszynowym dostosowanych do określonych obciążeń w celu tworzenia list zezwaceń i odmów. Zmniejsz powierzchnię ataku sieciowego dzięki kontrolowanemu dostępowi dokładnie na czas do portów zarządzania na maszyn wirtualnych platformy Azure. AAC znacząco zmniejsza narażenie na ataki siłowe i inne ataki sieciowe.
    - **Funkcje zabezpieczeń kontenerów** — skorzystaj z zarządzania lukami w zabezpieczeniach i ochrony przed zagrożeniami w czasie rzeczywistym w środowiskach konteneryzowanych. Po włączeniu **Azure Defender** dla rejestrów kontenerów może upłynieć do 12 godzin, dopóki wszystkie funkcje nie zostaną włączone. Opłaty są naliczane na podstawie liczby unikatowych obrazów kontenerów wypchnięć do połączonego rejestru. Po raz przeskanowany obraz nie zostanie ponownie naliczony, chyba że zostanie zmodyfikowany i wypchnięty ponownie.
    - **Ochrona** przed zagrożeniami w zakresie zasobów połączonych ze środowiskiem platformy Azure — usługa Azure Defender obejmuje natywną dla platformy Azure ochronę przed zagrożeniami dla usług platformy Azure wspólnych dla wszystkich zasobów: Azure Resource Manager, Azure DNS, warstwy sieci platformy Azure i Azure Key Vault. Azure Defender ma unikatowy wgląd w warstwę zarządzania platformy Azure i warstwę Azure DNS i może w związku z tym chronić zasoby w chmurze, które są połączone z tymi warstwami.


## <a name="faq---pricing-and-billing"></a>Często zadawane pytania — cennik i rozliczenia 

- [Jak mogę śledzić, kto w mojej organizacji włączył Azure Defender zmiany w Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Jakie plany są oferowane przez Security Center?](#what-are-the-plans-offered-by-security-center)
- [Jak mogę włączyć usługę Azure Defender dla mojej subskrypcji?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Czy mogę włączyć usługę Azure Defender dla podzbioru serwerów w mojej subskrypcji?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Jeśli mam już licencję usługi Microsoft Defender dla punktu końcowego, czy mogę uzyskać rabat na Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Moja subskrypcja ma Azure Defender włączone serwery, czy płacę za nie działające serwery?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Czy zostanie naliczona opłata za maszyny bez zainstalowanego agenta usługi Log Analytics?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Jeśli agent usługi Log Analytics zgłasza raporty do wielu obszarów roboczych, czy opłata zostanie naliczona dwukrotnie?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Jeśli agent usługi Log Analytics raportuje do wielu obszarów roboczych, czy pozyskiwanie danych o rozmiarze 500 MB jest dostępne dla wszystkich z nich?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [Czy pozyskiwanie danych o rozmiarze 500 MB jest obliczane dla całego obszaru roboczego, czy wyłącznie na maszynę?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Jakie typy danych są uwzględniane w dziennym limitie 500 MB danych?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Jak mogę śledzić, kto w mojej organizacji włączył Azure Defender zmiany w Security Center?
Subskrypcje platformy Azure mogą mieć wielu administratorów z uprawnieniami do zmiany ustawień cen. Aby dowiedzieć się, który użytkownik dokonał zmiany, użyj dziennika aktywności platformy Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Dziennik aktywności platformy Azure przedstawiający zdarzenie zmiany cen":::

Jeśli informacji o użytkowniku nie ma na liście w kolumnie Zdarzenie zainicjowane **przez,** zapoznaj się z JSON zdarzenia, aby uzyskać odpowiednie informacje.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Eksplorator JSON dziennika aktywności platformy Azure":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Jakie plany są oferowane przez Security Center? 
Security Center ma dwie oferty: 

- Bezpłatna usługa Azure Security Center 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Jak mogę włączyć usługę Azure Defender dla mojej subskrypcji? 
Aby włączyć obsługę Azure Defender subskrypcji, można użyć dowolnego z następujących sposobów: 

| Metoda                                          | Instrukcje                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Security Center strony Azure Portal | [Włączanie usługi Azure Defender](enable-azure-defender.md)                                                                                                  |
| Interfejs API REST                                        | [Interfejs API cennika](/rest/api/securitycenter/pricings)                                                                                                  |
| Interfejs wiersza polecenia platformy Azure                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Ceny pakietów](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Czy mogę włączyć usługę Azure Defender dla podzbioru serwerów w mojej subskrypcji?
Nie. Po włączeniu [Azure Defender dla](defender-for-servers-introduction.md) serwerów w ramach subskrypcji wszystkie serwery w subskrypcji będą chronione przez Azure Defender. 

Alternatywą jest włączenie Azure Defender dla serwerów na poziomie obszaru roboczego usługi Log Analytics. W takim przypadku chronione i rozliczane będą tylko serwery raportowane w tym obszarze roboczym. Jednak niektóre funkcje będą niedostępne. Obejmują one dostęp just in time do maszyny wirtualnej, wykrywanie sieci, zgodność z przepisami, adaptacyjne wzmacnianie zabezpieczeń sieci, adaptacyjną kontrolę aplikacji i nie tylko. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Jeśli mam już licencję usługi Microsoft Defender dla punktu końcowego, czy mogę uzyskać rabat na Azure Defender?
Jeśli masz już licencję usługi Microsoft Defender dla punktu końcowego, nie musisz płacić za część licencji Azure Defender końcowej.

Aby potwierdzić rabat, skontaktuj Security Center pomocy technicznej firmy i podaj odpowiedni identyfikator obszaru roboczego, region i informacje o licencji dla każdej odpowiedniej licencji.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Moja subskrypcja ma Azure Defender włączone serwery. Czy płacisz za nie działające serwery? 
Nie. Po włączeniu [Azure Defender](defender-for-servers-introduction.md) dla serwerów w ramach subskrypcji nie będą naliczane opłaty za maszyny, dla których cofono alokację zasilania, gdy są w tym stanie. Maszyny są rozliczane zgodnie ze stanem zasilania, jak pokazano w poniższej tabeli:

| Stan        | Opis                                                                                                                                      | Użycie wystąpienia rozliczane |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Uruchamianie     | Maszyna wirtualna jest uruchamiana.                                                                                                                               | Nie są naliczane rachunki            |
| Uruchomienie      | Normalny stan roboczy maszyny wirtualnej                                                                                                                    | Rozliczane                |
| Zatrzymywanie     | Jest to stan przejściowy. Po zakończeniu będzie ona pokazywana jako Zatrzymana.                                                                           | Rozliczane                |
| Zatrzymano      | Maszyna wirtualna została zamknięta z poziomu systemu operacyjnego gościa lub przy użyciu interfejsów API poweroff. Sprzęt jest nadal przydzielany do maszyny wirtualnej i pozostaje na hoście. | Rozliczane                |
| Cofanie przydziału | Stan przejściowy. Po zakończeniu maszyna wirtualna będzie pokazywana jako Cofniesz alokację.                                                                             | Nie są naliczane            |
| Cofnięto przydział  | Maszyna wirtualna została pomyślnie zatrzymana i usunięta z hosta.                                                                                  | Nie są naliczane            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Usługa Azure Virtual Machines pokazująca maszynę, dla których cofniesz alokację":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Czy zostanie naliczona opłata za maszyny bez zainstalowanego agenta usługi Log Analytics?
Tak. Po włączeniu [Azure Defender](defender-for-servers-introduction.md) dla serwerów w ramach subskrypcji maszyny w tej subskrypcji uzyskają szereg zabezpieczeń, nawet jeśli agent usługi Log Analytics nie został zainstalowany.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Jeśli agent usługi Log Analytics zgłasza raporty do wielu obszarów roboczych, czy opłata zostanie naliczona dwukrotnie? 
Tak. Jeśli skonfigurowano agenta usługi Log Analytics do wysyłania danych do co najmniej dwóch różnych obszarów roboczych usługi Log Analytics (z wieloma regionami), opłata zostanie naliczona za każdy obszar roboczy z zainstalowanymi rozwiązaniami "Security" lub "AntiMalware". 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Jeśli agent usługi Log Analytics raportuje do wielu obszarów roboczych, czy pozyskiwanie danych o rozmiarze 500 MB jest dostępne dla wszystkich z nich?
Tak. Jeśli skonfigurowano agenta usługi Log Analytics do wysyłania danych do co najmniej dwóch różnych obszarów roboczych usługi Log Analytics (z wieloma regionami), otrzymasz 500 MB bezpłatnego pozyskiwania danych. Jest on obliczany dla każdego węzła, dla zgłoszonego obszaru roboczego dziennie i jest dostępny dla każdego obszaru roboczego z zainstalowanymi rozwiązaniami "Security" lub "AntiMalware". Opłata zostanie naliczona za wszystkie dane pozysłane w rozmiarze powyżej 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>Czy pozyskiwanie danych o rozmiarze 500 MB jest obliczane dla całego obszaru roboczego, czy wyłącznie na maszynę?
Będziesz korzystać z bezpłatnego pozyskiwania danych o rozmiarze 500 MB dziennie dla każdej maszyny podłączonej do obszaru roboczego. W szczególności w przypadku typów danych zabezpieczeń zbieranych bezpośrednio przez Azure Security Center.

Przyjmowana jest średnia dzienna ilość danych dla wszystkich węzłów. Dlatego nawet jeśli niektóre maszyny wysyłają 100 MB, a inne wysyłają 800 MB, jeśli łączna liczba maszyn nie przekracza **limitu [liczba maszyn] x 500 MB** wolnego miejsca, nie zostaną naliczone dodatkowe opłaty.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Jakie typy danych są uwzględnione w dziennym limitie 500 MB danych?

Security Center jest ściśle powiązana z rozliczeniami usługi Log Analytics. Security Center zapewnia alokację 500 MB/węzeł/dzień względem następującego [podzbioru typów danych zabezpieczeń:](/azure/azure-monitor/reference/tables/tables-category#security)
- WindowsEvent
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection (SecurityDetection)
- SecurityEvent
- WindowsFirewall
- ZłośliweIPCommunication
- LinuxAuditLog
- SysmonEvent
- ProtectionStatus
- Zaktualizuj i zaktualizuj typy danych UpdateSummary, gdy Update Management nie jest uruchomione w obszarze roboczym lub włączono docelowych rozwiązań

Jeśli obszar roboczy znajduje się w starszej warstwie cenowej na węzeł, alokacje usług Security Center i Log Analytics są łączone i stosowane razem do wszystkich rozliczanych danych pozysowanych.

## <a name="next-steps"></a>Następne kroki
W tym artykule Security Center opcje cenowe. Aby uzyskać powiązane materiały, zobacz:

- [Jak zoptymalizować koszty obciążeń platformy Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Szczegóły cennika w wybranej walucie i w zależności od regionu](https://azure.microsoft.com/pricing/details/security-center/)
- Możesz chcieć zarządzać kosztami i ograniczać ilość danych zbieranych dla rozwiązania, ograniczając je do określonego zestawu agentów. [Opcja określania](../azure-monitor/insights/solution-targeting.md) wartości docelowej rozwiązania umożliwia zastosowanie zakresu do rozwiązania i zastosowanie podzestawu komputerów w obszarze roboczym. Jeśli używasz określania wartości docelowej rozwiązania, Security Center obszaru roboczego jako bez rozwiązania.
