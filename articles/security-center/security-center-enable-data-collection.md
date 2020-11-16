---
title: Wdróż ponownie agentów dla Azure Security Center | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania autoaprowizacji agenta Log Analytics i innych agentów używanych przez Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 7d96b03598f90b45b7ecf88027be7408d8f161ea
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638771"
---
# <a name="auto-provisioning-agents-and-extensions-from-azure-security-center"></a>Inicjowanie obsługi administracyjnej agentów i rozszerzeń z Azure Security Center

Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz innych niż platformy Azure (w tym lokalnych) maszyn do monitorowania luk w zabezpieczeniach i zagrożeń. 

Zbieranie danych jest wymagane w celu zapewnienia wglądu w brakujące aktualizacje, nieprawidłowej konfiguracji ustawień zabezpieczeń systemu operacyjnego, stanu programu Endpoint Protection oraz ochrony kondycji i zagrożeń. Zbieranie danych jest wymagana tylko w przypadku zasobów obliczeniowych (maszyn wirtualnych, zestawów skalowania maszyn wirtualnych, kontenerów IaaS i komputerów spoza platformy Azure). Możesz skorzystać z Azure Security Center, nawet jeśli nie zainicjujesz agentów. jednak będziesz mieć ograniczone zabezpieczenia i możliwości wymienione powyżej nie są obsługiwane.  

Dane są zbierane przy użyciu:

- **Agent log Analytics** , który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. Przykładami takich danych są: typ i wersja systemu operacyjnego, Dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, Nazwa maszyny, adresy IP i zalogowany użytkownik.
- **Rozszerzenia zabezpieczeń** , takie jak [dodatek Azure Policy dla Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), które mogą również dostarczać dane do Security Center dotyczących wyspecjalizowanych typów zasobów.

> [!TIP]
> Jak Security Center, typy zasobów, które mogą być monitorowane, również zostały nahodowane. Liczba rozszerzeń została również osiągnięta. Funkcja autoaprowizacji została rozszerzona o obsługę dodatkowych typów zasobów, wykorzystując możliwości Azure Policy.

:::image type="content" source="./media/security-center-enable-data-collection/auto-provisioning-options.png" alt-text="Strona ustawień autoaprowizacji Security Center":::


## <a name="why-use-auto-provisioning"></a>Dlaczego warto używać autoaprowizacji?
Dowolny Agent i rozszerzenia opisane na tej stronie *można* zainstalować ręcznie (zobacz [ręczne Instalowanie agenta log Analytics](#manual-agent)). Jednak **Funkcja autoaprowizacji** zmniejsza obciążenie związane z zarządzaniem przez zainstalowanie wszystkich wymaganych agentów i rozszerzeń na istniejących i nowych maszynach w celu zapewnienia szybszego pokrycia zabezpieczeń dla wszystkich obsługiwanych zasobów. 

Zalecamy włączenie obsługi autoaprowizacji, ale jest ona domyślnie wyłączona.

## <a name="how-does-auto-provisioning-work"></a>Jak działa funkcja autoaprowizacji?
Ustawienia autoaprowizacji Security Center są dostępne dla każdego typu obsługiwanego rozszerzenia. Po włączeniu autoaprowizacji rozszerzenia należy przypisać odpowiednie zasady **wdrażania, jeśli nie istnieje** , aby upewnić się, że rozszerzenie jest obsługiwane we wszystkich istniejących i przyszłych zasobach tego typu.

> [!TIP]
> Dowiedz się więcej na temat Azure Policy efektów, w tym do wdrożenia, jeśli nie istnieje w temacie [Azure Policy efekty](../governance/policy/concepts/effects.md).

## <a name="enable-auto-provisioning-of-the-log-analytics-agent"></a>Włącz funkcję autoaprowizacji agenta Log Analytics <a name="auto-provision-mma"></a>
Gdy automatyczne Inicjowanie obsługi jest włączone dla agenta Log Analytics, Security Center wdraża agenta na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Listę obsługiwanych platform można znaleźć [w temacie obsługiwane platformy w Azure Security Center](security-center-os-coverage.md).

Aby włączyć funkcję autoaprowizacji agenta Log Analytics:

1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Na stronie **autozastrzeganie** Ustaw stan agenta na wartość **włączone**.
1. W okienku Opcje konfiguracji Zdefiniuj obszar roboczy, który ma być używany.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Opcje konfiguracji automatycznej aprowizacji Log Analytics agentów na maszynach wirtualnych" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Połącz maszyny wirtualne platformy Azure z domyślnymi obszarami roboczymi utworzonymi przez Security Center** — Security Center tworzy nową grupę zasobów i domyślny obszar roboczy w tej samej lokalizacji geolokalizacyjnej i łączy agenta z tym obszarem roboczym. Jeśli subskrypcja zawiera maszyny wirtualne z wielu lokalizacji, Security Center tworzy wiele obszarów roboczych, aby zapewnić zgodność z wymaganiami dotyczącymi ochrony prywatności danych.

        Konwencja nazewnictwa obszaru roboczego i grupy zasobów to: 
        - Obszar roboczy: DefaultWorkspace-[identyfikator-subskrypcji]-[lokalizacja-geograficzna] 
        - Grupa zasobów: DefaultResourceGroup-[Geo] 

        Security Center automatycznie włącza Security Center rozwiązanie w obszarze roboczym zgodnie z warstwą cenową ustawioną dla subskrypcji. 

        > [!TIP]
        > Pytania dotyczące domyślnych obszarów roboczych można znaleźć w temacie:
        >
        > - [Czy są naliczane opłaty za dzienniki Azure Monitor w obszarach roboczych utworzonych przez Security Center?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Gdzie jest tworzony domyślny obszar roboczy Log Analytics?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Czy mogę usunąć domyślne obszary robocze utworzone przez Security Center?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Łączenie maszyn wirtualnych platformy Azure z innym obszarem roboczym** — z listy rozwijanej wybierz obszar roboczy, w którym mają być przechowywane zebrane dane. Lista rozwijana zawiera wszystkie obszary robocze we wszystkich Twoich subskrypcjach. Za pomocą tej opcji można zbierać dane z maszyn wirtualnych działających w różnych subskrypcjach i przechować je w wybranym obszarze roboczym.  

        Jeśli masz już istniejący obszar roboczy Log Analytics, możesz chcieć użyć tego samego obszaru roboczego (wymagane są uprawnienia do odczytu i zapisu w obszarze roboczym). Ta opcja jest przydatna, jeśli używasz scentralizowanego obszaru roboczego w organizacji i chcesz używać go do zbierania danych zabezpieczeń. Dowiedz się więcej w temacie [Zarządzanie dostępem do danych dziennika i obszarów roboczych w programie Azure monitor](../azure-monitor/platform/manage-access.md).

        Jeśli w wybranym obszarze roboczym jest już włączone rozwiązanie Security lub SecurityCenterFree, Cennik zostanie ustawiony automatycznie. Jeśli nie, Zainstaluj rozwiązanie Security Center w obszarze roboczym:

        1. W menu Security Center Otwórz **& ustawienia cennika**.
        1. Wybierz obszar roboczy, do którego chcesz połączyć agentów.
        1. Wybierz pozycję **Azure Defender on** lub **Azure Defender off**.

1. W obszarze Konfiguracja **zdarzeń zabezpieczeń systemu Windows** wybierz ilość danych nieprzetworzonych zdarzeń do zapisania:
    - **Brak** — Wyłącz magazyn zdarzeń zabezpieczeń. Jest to ustawienie domyślne.
    - **Minimalny** — mały zestaw zdarzeń do zminimalizowania ilości zdarzeń.
    - **Common** — zestaw zdarzeń, który spełnia większość klientów i zawiera pełny dziennik inspekcji.
    - **Wszystkie zdarzenia** — dla klientów, którzy chcą mieć pewność, że wszystkie zdarzenia są przechowywane.

    > [!TIP]
    > Aby ustawić te opcje na poziomie obszaru roboczego, zobacz [Ustawianie opcji zdarzenia zabezpieczeń na poziomie obszaru roboczego](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Aby uzyskać więcej informacji na temat tych opcji, zobacz [Opcje zdarzeń zabezpieczeń systemu Windows dla agenta log Analytics](#data-collection-tier).

1. Wybierz pozycję **Zastosuj** w okienku Konfiguracja.

1. Wybierz pozycję **Zapisz**. Jeśli konieczne jest zainicjowanie obszaru roboczego, Instalacja agenta może trwać do 25 minut.

1. Zostanie wyświetlony monit, czy chcesz ponownie skonfigurować monitorowane maszyny wirtualne, które zostały wcześniej połączone z domyślnym obszarem roboczym:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Przejrzyj opcje, aby ponownie skonfigurować monitorowane maszyny wirtualne":::

    - **Nie** — nowe ustawienia obszaru roboczego zostaną zastosowane tylko do nowo odnalezionych maszyn wirtualnych, na których nie zainstalowano agenta log Analytics.
    - **Tak** — nowe ustawienia obszaru roboczego zostaną zastosowane do wszystkich maszyn wirtualnych, a każda maszyna wirtualna aktualnie połączona z Security Centerm utworzonym obszarem roboczym zostanie ponownie nawiązane połączenie z nowym docelowym obszarem roboczym.

   > [!NOTE]
   > W przypadku wybrania opcji **tak** nie usuwaj obszarów roboczych utworzonych przez Security Center, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja kończy się niepowodzeniem, jeśli obszar roboczy zostanie zbyt wcześnie usunięty.


## <a name="enable-auto-provisioning-of-extensions"></a>Włącz funkcję autoaprowizacji rozszerzeń

Aby włączyć automatyczną obsługę rozszerzenia innego niż Agent Log Analytics: 

1. W Azure Portal z menu Security Center wybierz pozycję **Cennik ustawienia &**.
1. Wybierz odpowiednią subskrypcję.
1. Wybierz pozycję **autoinicjowanie obsługi**.
1. Jeśli włączysz funkcję autoaprowizacji dla programu Microsoft Dependency Agent, upewnij się, że Agent Log Analytics jest ustawiony na wartość autodeploy. 
1. Przełącz stan na wartość **włączone** dla odpowiedniego rozszerzenia.

    :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Przełącz, aby włączyć funkcję autoaprowizacji dla dodatku zasad K8s":::

1. Wybierz pozycję **Zapisz**. Zasady platformy Azure są przypisane i tworzone jest zadanie korygowania.

    |Rozszerzenie  |Zasady  |
    |---------|---------|
    |Dodatek zasad dla Kubernetes|[Wdrażanie dodatku Azure Policy w klastrach usługi Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
    |Microsoft Dependency Agent (wersja zapoznawcza) (maszyny wirtualne z systemem Windows)|[Wdróż agenta zależności dla maszyn wirtualnych z systemem Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
    |Microsoft Dependency Agent (wersja zapoznawcza) (maszyny wirtualne z systemem Linux)|[Wdróż agenta zależności dla maszyn wirtualnych z systemem Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|



## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Opcje zdarzeń zabezpieczeń systemu Windows dla agenta Log Analytics <a name="data-collection-tier"></a> 

Wybranie warstwy zbierania danych w Azure Security Center ma wpływ tylko na *Magazyn* zdarzeń zabezpieczeń w obszarze roboczym log Analytics. Agent Log Analytics będzie nadal zbierać i analizować zdarzenia zabezpieczeń wymagane do ochrony przed zagrożeniami Security Center, niezależnie od poziomu zdarzeń zabezpieczeń wybranych do przechowywania w obszarze roboczym. Wybranie opcji przechowywania zdarzeń zabezpieczeń umożliwia badanie, wyszukiwanie i inspekcję tych zdarzeń w obszarze roboczym.

### <a name="requirements"></a>Wymagania 
Usługa Azure Defender jest wymagana do przechowywania danych zdarzeń zabezpieczeń systemu Windows. [Dowiedz się więcej o usłudze Azure Defender](azure-defender.md).

Przechowywanie danych w Log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Informacje dla użytkowników wskaźnikowych platformy Azure 
Użytkownicy platformy Azure — należy pamiętać, że zbieranie zdarzeń zabezpieczeń w kontekście jednego obszaru roboczego można skonfigurować z poziomu Azure Security Center lub platformy Azure, ale nie obu. Jeśli planujesz dodać wskaźnik platformy Azure do obszaru roboczego, który już otrzymuje alerty usługi Azure Defender z Azure Security Center i jest ustawiony na zbieranie zdarzeń zabezpieczeń, masz dwie opcje:
- Pozostaw zbieranie zdarzeń zabezpieczeń w Azure Security Center. Będzie można wykonywać zapytania i analizować te zdarzenia na platformie Azure, a także w usłudze Azure Defender. Nie będzie jednak można monitorować stanu łączności łącznika ani zmieniać jego konfiguracji na platformie Azure — wskaźnik. Jeśli jest to ważne dla Ciebie, weź pod uwagę drugą opcję.
- Wyłącz zbieranie zdarzeń zabezpieczeń w Azure Security Center (przez ustawienie **zdarzeń zabezpieczeń systemu Windows** na **Brak** w konfiguracji agenta log Analytics). Następnie Dodaj łącznik zdarzeń zabezpieczeń na platformie Azure — wskaźnik. Podobnie jak w przypadku pierwszej opcji, można wysyłać zapytania i analizować zdarzenia zarówno z platformy Azure, jak i usługi Azure Defender/ASC, ale teraz będzie można monitorować stan łączności łącznika lub zmieniać jego konfigurację w systemach i tylko na platformie Azure.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Jakie typy zdarzeń są przechowywane dla "Common" i "minimalny"?
Te zestawy zostały zaprojektowane w celu rozwiązywania typowych scenariuszy. Upewnij się, że należy obliczyć, który z nich odpowiada Twoim potrzebom przed wdrożeniem.

Aby określić zdarzenia dotyczące **typowych** i **minimalnych** opcji, pracujemy z klientami i standardami branżowymi, aby dowiedzieć się więcej o niefiltrowanych częstotliwości poszczególnych zdarzeń i ich użyciu. W tym procesie użyto następujących wytycznych:

- **Minimalny** — upewnij się, że ten zestaw obejmuje tylko zdarzenia, które mogą wskazywać na pomyślne naruszenie i ważne zdarzenia, które mają bardzo niski wolumin. Na przykład ten zestaw zawiera pomyślne i nieudane Logowanie użytkownika (identyfikatory zdarzeń 4624, 4625), ale nie zawiera wylogowania, co jest ważne w przypadku inspekcji, ale nie ma znaczenia dla wykrywania i ma stosunkowo dużą ilość danych. Większość ilości danych tego zestawu to zdarzenia logowania i zdarzenia tworzenia procesu (Identyfikator zdarzenia 4688).
- **Common** — Podaj pełny dziennik inspekcji użytkownika w tym zestawie. Na przykład ten zestaw zawiera identyfikatory logowania użytkowników i wylogowania użytkowników (Identyfikator zdarzenia 4634). Obejmuje to akcje inspekcji, takie jak zmiany grupy zabezpieczeń, kluczowego kontrolera domeny i inne zdarzenia, które są zalecane przez organizacje branżowe.

Zdarzenia, które mają bardzo niski wolumin, zostały uwzględnione w wspólnym zestawie jako główna motywacja do wyboru nad wszystkimi zdarzeniami polega na zmniejszeniu ilości i braku filtrowania określonych zdarzeń.

Poniżej znajduje się kompletny podział identyfikatorów zdarzeń związanych z zabezpieczeniami i blokowaniem aplikacji dla każdego zestawu:

| Warstwa danych | Zebrane wskaźniki zdarzeń |
| --- | --- |
| Minimalny | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Wspólne | 1, 413, 300, 324, 340, 403, 404, 410, 411, 412,, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893,: 4898, 4902 |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - W przypadku używania obiektu zasady grupy (GPO) zaleca się włączenie zdarzenia dotyczącego tworzenia procesu zasad inspekcji 4688 i pola *wiersza polecenia* wewnątrz zdarzenia 4688. Aby uzyskać więcej informacji o zdarzeniu tworzenia procesu 4688, zobacz [często zadawane pytania](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)dotyczące Security Center. Aby uzyskać więcej informacji na temat tych zasad inspekcji, zobacz [zalecenia dotyczące zasad inspekcji](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Aby włączyć zbieranie danych na potrzeby [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. Spowoduje to generowanie przez funkcję AppLocker zdarzeń, które są następnie zbierane i wykorzystywane przez Security Center. Należy pamiętać, że te zasady nie zostaną skonfigurowane na wszystkich komputerach, na których już skonfigurowano zasady funkcji AppLocker. 
> - Aby zbierać zdarzenia platformy filtrowania systemu Windows o [identyfikatorze 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), należy włączyć funkcję [filtrowania inspekcji dla platformy](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (auditpol/Set/Subcategory: "Filtering Platform Connection"/Success: Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Ustawianie opcji zdarzenia zabezpieczeń na poziomie obszaru roboczego

Można zdefiniować poziom danych zdarzeń zabezpieczeń do przechowywania na poziomie obszaru roboczego.

1. W Azure Portal z menu Security Center wybierz pozycję **Cennik ustawienia &**.
1. Wybierz odpowiedni obszar roboczy. Jedynymi zdarzeniami zbierania danych dla obszaru roboczego są zdarzenia zabezpieczeń systemu Windows opisane na tej stronie.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Ustawianie danych zdarzeń zabezpieczeń do przechowywania w obszarze roboczym":::

1. Wybierz ilość danych nieprzetworzonych zdarzenia do zapisania i wybierz pozycję **Zapisz**.

## <a name="manual-agent-provisioning"></a>Ręczna obsługa agentów <a name="manual-agent"></a>
 
Aby ręcznie zainstalować agenta Log Analytics:

1. Wyłącz funkcję autoaprowizacji.

1. Opcjonalnie możesz utworzyć obszar roboczy.

1. Włącz usługę Azure Defender w obszarze roboczym, na którym instalujesz agenta Log Analytics:

    1. W menu Security Center wybierz pozycję **cennik & ustawienia**.

    1. Ustaw obszar roboczy, w którym jest instalowany Agent. Upewnij się, że obszar roboczy znajduje się w tej samej subskrypcji, której używasz w Security Center i że masz uprawnienia do odczytu/zapisu dla obszaru roboczego.

    1. Wybierz pozycję **Azure Defender on** i **Zapisz**.

       >[!NOTE]
       >Jeśli w obszarze roboczym jest już włączone rozwiązanie **Security** lub **SecurityCenterFree** , Cennik zostanie ustawiony automatycznie. 

1. Aby wdrożyć agentów na nowych maszynach wirtualnych przy użyciu szablonu Menedżer zasobów, Zainstaluj agenta Log Analytics:

   - [Zainstaluj agenta Log Analytics dla systemu Windows](../virtual-machines/extensions/oms-windows.md)
   - [Zainstaluj agenta Log Analytics dla systemu Linux](../virtual-machines/extensions/oms-linux.md)

1. Aby wdrożyć agentów na istniejących maszynach wirtualnych, postępuj zgodnie z instrukcjami podanymi w temacie [zbieranie danych dotyczących platformy Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md) (sekcja **zbieranie danych dotyczących zdarzeń i wydajności** jest opcjonalne).

1. Aby wdrożyć agentów przy użyciu programu PowerShell, Skorzystaj z instrukcji z dokumentacji dotyczącej maszyn wirtualnych:

    - [Dla maszyn z systemem Windows](../virtual-machines/extensions/oms-windows.md?toc=%252fazure%252fazure-monitor%252ftoc.json#powershell-deployment)
    - [Dla maszyn z systemem Linux](../virtual-machines/extensions/oms-linux.md?toc=%252fazure%252fazure-monitor%252ftoc.json#azure-cli-deployment)

> [!TIP]
> Aby uzyskać instrukcje na temat sposobu dołączania Security Center przy użyciu programu PowerShell, zobacz Automatyzowanie dołączania [Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta <a name="preexisting"></a> 

W następujących przypadkach użycia określono, jak funkcja automatycznego udostępniania będzie działać w przypadkach, gdy jest już zainstalowany agent lub rozszerzenie. 

- **Agent log Analytics jest zainstalowany na komputerze, ale nie jako rozszerzenie (Agent bezpośredni)** — jeśli Agent log Analytics jest instalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center zainstaluje rozszerzenie agenta log Analytics i może uaktualnić agenta log Analytics do najnowszej wersji.
Zainstalowany agent będzie kontynuował raportowanie do już skonfigurowanych obszarów roboczych, a ponadto przeprowadzi raport do obszaru roboczego skonfigurowanego w Security Center (wiele multihostingu jest obsługiwana na maszynach z systemem Windows).
Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie Security Center domyślnym obszarem roboczym), należy zainstalować na nim rozwiązanie "Security/" securityFree "dla Security Center, aby rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów zgłaszanych do tego obszaru roboczego.

    W przypadku maszyn z systemem Linux Agent multihostingu nie jest jeszcze obsługiwany — w związku z tym jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne Inicjowanie obsługi nie zostanie wykonane i konfiguracja maszyny nie zostanie zmieniona.

    W przypadku istniejących maszyn w ramach subskrypcji, które zostały dołączone do Security Center przed 17 marca 2019, gdy zostanie wykryty istniejący Agent, rozszerzenie agenta Log Analytics nie zostanie zainstalowane i nie będzie to miało takiego oddziaływania. W przypadku tych maszyn Zobacz zalecenia dotyczące rozwiązywania problemów z kondycją agenta monitorowania na swoich komputerach, aby rozwiązać problemy z instalacją agenta na tych komputerach.
  
- **Agent System Center Operations Manager jest zainstalowany na komputerze** — Centrum zabezpieczeń zainstaluje rozszerzenie agenta log Analytics obok istniejącej Operations Manager. Istniejący Agent Operations Manager będzie kontynuował raportowanie do serwera Operations Manager w normalny sposób. Agent Operations Manager i Agent Log Analytics mają wspólne biblioteki uruchomieniowe, które zostaną zaktualizowane do najnowszej wersji w trakcie tego procesu. Jeśli zainstalowano agenta **Operations Manager w wersji 2012, nie należy** włączać automatycznej aprowizacji.

- **Istnieje już istniejące rozszerzenie maszyny wirtualnej** :
    - Gdy Agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko jednego obszaru roboczego. Security Center nie przesłania istniejących połączeń z obszarami roboczymi użytkowników. Security Center będą przechowywać dane zabezpieczeń z maszyny wirtualnej w już podłączonym obszarze roboczym, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "securityFree". Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.  
    - Aby zobaczyć, w którym obszarze roboczym jest wysyłane dane rozszerzenie, Uruchom test w celu [sprawdzenia łączności z Azure Security Center](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). Alternatywnie możesz otworzyć obszary robocze Log Analytics, wybrać obszar roboczy, wybrać maszynę wirtualną i sprawdzić połączenie z agentem Log Analytics. 
    - Jeśli masz środowisko, w którym jest zainstalowany agent Log Analytics na stacjach roboczych klienta i raportowanie do istniejącego Log Analytics obszaru roboczego, przejrzyj listę [systemów operacyjnych obsługiwanych przez Azure Security Center](security-center-os-coverage.md) , aby upewnić się, że system operacyjny jest obsługiwany. Aby uzyskać więcej informacji, zobacz [istniejących klientów usługi log Analytics](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Wyłącz funkcję autoaprowizacji <a name="offprovisioning"></a>

Po wyłączeniu obsługi autoaprowizacji agenci nie będą obsługiwani na nowych maszynach wirtualnych.

Aby wyłączyć automatyczne Inicjowanie obsługi agenta:

1. Z poziomu menu Security Center w portalu wybierz pozycję **cennik & ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Wybierz pozycję **autoinicjowanie obsługi**.
1. Przełącz stan na **wyłączony** dla odpowiedniego agenta.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Włącza lub wyłącza funkcję autoaprowizacji na typ agenta":::

1. Wybierz pozycję **Zapisz**. Gdy funkcja automatycznej aprowizacji jest wyłączona, domyślna sekcja konfiguracji obszaru roboczego nie jest wyświetlana:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="Gdy funkcja automatycznej aprowizacji jest wyłączona, komórka konfiguracji jest pusta":::


> [!NOTE]
>  Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Aby uzyskać informacje dotyczące usuwania rozszerzenia pakietu OMS, zobacz [Jak mogę usuwania rozszerzeń pakietu OMS zainstalowanych przez Security Center](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Rozwiązywanie problemów

-   Aby zidentyfikować problemy z instalacją automatycznej obsługi administracyjnej, zobacz [monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Aby zidentyfikować wymagania sieciowe agenta monitorowania, zobacz [Rozwiązywanie problemów z wymaganiami sieci agenta monitorowania](security-center-troubleshooting-guide.md#mon-network-req).
-   Aby identyfikować ręczne problemy z dołączaniem, zobacz [Jak rozwiązywać problemy z dołączaniem do pakietu Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Aby zidentyfikować problemy dotyczące niemonitorowanych maszyn wirtualnych i komputerów:

    Maszyna wirtualna lub komputer nie jest monitorowany przez Security Center, jeśli na komputerze nie jest uruchomione rozszerzenie agenta Log Analytics. Na komputerze może być już zainstalowany agent lokalny, na przykład Agent usługi OMS Direct lub Agent System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ nie są w pełni obsługiwane w Security Center. Aby w pełni skorzystać ze wszystkich funkcji usługi Security Center, wymagane jest rozszerzenie agenta usługi Log Analytics.

    Aby uzyskać więcej informacji na temat przyczyn, Security Center nie może pomyślnie monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji, zobacz [monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).




## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak działa zbieranie danych i automatyczne Inicjowanie obsługi w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące strony:

- [Centrum zabezpieczeń Azure — często zadawane pytania](faq-general.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
- [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.

W tym artykule opisano sposób instalowania agenta Log Analytics i ustawiania obszaru roboczego Log Analytics, w którym będą przechowywane zebrane dane. Obie operacje są wymagane do włączenia zbierania danych. Przechowywanie danych w Log Analytics, bez względu na to, czy używasz nowego, czy istniejącego obszaru roboczego, może nawiązać dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

