---
title: Zbieranie danych w Azure Security Center | Microsoft Docs
description: W tym artykule opisano sposób instalowania agenta Log Analytics i ustawiania obszaru roboczego Log Analytics, w którym będą przechowywane zebrane dane.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/27/2020
ms.author: memildin
ms.openlocfilehash: 92c73fed84910e525378aa18e02456960acf9911
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91447217"
---
# <a name="data-collection-in-azure-security-center"></a>Zbieranie danych w usłudze Azure Security Center
Security Center zbiera dane z maszyn wirtualnych platformy Azure, zestawów skalowania maszyn wirtualnych, kontenerów IaaS oraz innych niż platformy Azure (w tym komputerów lokalnych) do monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu agenta Log Analytics, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. Przykładami takich danych są: typ i wersja systemu operacyjnego, Dzienniki systemu operacyjnego (dzienniki zdarzeń systemu Windows), uruchomione procesy, Nazwa maszyny, adresy IP i zalogowany użytkownik.

Zbieranie danych jest wymagane w celu zapewnienia wglądu w brakujące aktualizacje, nieprawidłowej konfiguracji ustawień zabezpieczeń systemu operacyjnego, stanu programu Endpoint Protection oraz ochrony kondycji i zagrożeń. 

W tym artykule opisano sposób instalowania agenta Log Analytics i ustawiania obszaru roboczego Log Analytics, w którym będą przechowywane zebrane dane. Obie operacje są wymagane do włączenia zbierania danych. 

> [!NOTE]
> - Zbieranie danych jest wymagana tylko w przypadku zasobów obliczeniowych (maszyn wirtualnych, zestawów skalowania maszyn wirtualnych, kontenerów IaaS i komputerów spoza platformy Azure). Możesz skorzystać z Azure Security Center, nawet jeśli nie zainicjujesz agentów. jednak będziesz mieć ograniczone zabezpieczenia i możliwości wymienione powyżej nie są obsługiwane.  
> - Listę obsługiwanych platform można znaleźć [w temacie obsługiwane platformy w Azure Security Center](security-center-os-coverage.md).
> - Przechowywanie danych w Log Analytics, bez względu na to, czy używasz nowego, czy istniejącego obszaru roboczego, może nawiązać dodatkowe opłaty za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Włącz automatyczną obsługę administracyjną agenta Log Analytics <a name="auto-provision-mma"></a>

Aby zebrać dane z maszyn, należy zainstalować agenta Log Analytics. Instalację agenta można wykonać automatycznie (zalecane) lub ręcznie zainstalować agenta. Automatyczne Inicjowanie obsługi jest domyślnie wyłączone.

Gdy automatyczne Inicjowanie obsługi jest włączone, Security Center wdraża agenta Log Analytics na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Zalecana jest Automatyczna obsługa administracyjna, ale w razie potrzeby można zainstalować agenta ręcznie (zobacz [Ręczne instalowanie agenta log Analytics](#manual-agent)).



Aby włączyć automatyczną obsługę administracyjną agenta Log Analytics:

1. W menu Security Center wybierz pozycję **cennik & ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Na stronie **zbieranie danych** ustaw opcję **samoobsługowego udostępniania** na wartość **włączone**.
1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Włączanie obsługi administracyjnej agenta Log Analytics":::

>[!TIP]
> Jeśli konieczne jest zainicjowanie obszaru roboczego, Instalacja agenta może trwać do 25 minut.

Dzięki agentowi wdrożonemu na maszynach Security Center mogą zapewnić dodatkowe zalecenia dotyczące stanu aktualizacji systemu, konfiguracji zabezpieczeń systemu operacyjnego, programu Endpoint Protection, a także generować dodatkowe alerty zabezpieczeń.

>[!NOTE]
> Ustawienie opcji autozastrzeganie na **wyłączone** nie powoduje usunięcia agenta log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został już zainicjowany. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.

>[!NOTE]
> - Aby uzyskać instrukcje dotyczące inicjowania obsługi istniejącej instalacji, zobacz [Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta](#preexisting).
> - Aby uzyskać instrukcje dotyczące ręcznego inicjowania obsługi, zobacz [Ręczne instalowanie rozszerzenia agenta log Analytics](#manual-agent).
> - Aby uzyskać instrukcje dotyczące wyłączania automatycznej aprowizacji, zobacz [wyłączanie automatycznej aprowizacji](#offprovisioning).
> - Aby uzyskać instrukcje na temat sposobu dołączania Security Center przy użyciu programu PowerShell, zobacz Automatyzowanie dołączania [Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguracja obszaru roboczego
Dane zebrane przez usługę Security Center są przechowywane w obszarach roboczych usługi Log Analytics. Dane można zbierać z maszyn wirtualnych platformy Azure przechowywanych w obszarach roboczych utworzonych przez Security Center lub w istniejącym utworzonym obszarze roboczym. 

Konfiguracja obszaru roboczego jest ustawiana na subskrypcję, a wiele subskrypcji może korzystać z tego samego obszaru roboczego.

### <a name="using-a-workspace-created-by-security-center"></a>Używanie obszaru roboczego utworzonego przez Security Center

Centrum zabezpieczeń może automatycznie utworzyć domyślny obszar roboczy, w którym będą przechowywane dane. 

Aby wybrać obszar roboczy utworzony przez Security Center:

1. W obszarze **Domyślna konfiguracja obszaru roboczego**wybierz pozycję Użyj obszarów roboczych utworzonych przez Centrum zabezpieczeń.
    :::image type="content" source="./media/security-center-enable-data-collection/workspace-selection.png" alt-text="Włączanie obsługi administracyjnej agenta Log Analytics"::: 

1. Kliknij pozycję **Zapisz**.<br>
    Security Center tworzy nową grupę zasobów i domyślny obszar roboczy w tej geolokalizacji i łączy agenta z tym obszarem roboczym. Konwencja nazewnictwa obszaru roboczego i grupy zasobów to:<br>
   **Obszar roboczy: DefaultWorkspace-[Identyfikator subskrypcji]-[geograficzna] <br> Grupa zasobów: DefaultResourceGroup-[Geo]**

   Jeśli subskrypcja zawiera maszyny wirtualne z wielu lokalizacji, wówczas Security Center tworzy wiele obszarów roboczych. Utworzono wiele obszarów roboczych, aby zachować reguły ochrony prywatności danych.
1. Security Center automatycznie włączy Security Center rozwiązanie w obszarze roboczym zgodnie z warstwą cenową ustawioną dla subskrypcji. 

> [!NOTE]
> Log Analytics warstwa cenowa obszarów roboczych utworzonych przez Security Center nie ma wpływu na rozliczenia Security Center. Rozliczenie usługi Security Center zawsze zależy od zasad zabezpieczeń usługi Security Center i rozwiązań zainstalowanych w obszarze roboczym. W przypadku subskrypcji bez usługi Azure Defender Security Center włącza rozwiązanie *SecurityCenterFree* w domyślnym obszarze roboczym. W przypadku subskrypcji w usłudze Azure Defender Security Center włącza rozwiązanie *zabezpieczeń* w domyślnym obszarze roboczym.
> Przechowywanie danych w Log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

Aby uzyskać więcej informacji na temat istniejących kont usługi log Analytics, zobacz [istniejących klientów usługi log Analytics](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Korzystanie z istniejącego obszaru roboczego

Jeśli masz już istniejący obszar roboczy Log Analytics, możesz chcieć użyć tego samego obszaru roboczego.

Aby korzystać z istniejącego obszaru roboczego Log Analytics, musisz mieć uprawnienia do odczytu i zapisu w obszarze roboczym.

> [!NOTE]
> Rozwiązania włączone w istniejącym obszarze roboczym zostaną zastosowane do maszyn wirtualnych platformy Azure, które są z nim połączone. W przypadku płatnych rozwiązań może to spowodować naliczenie dodatkowych opłat. W przypadku zagadnień dotyczących prywatności danych upewnij się, że wybrany obszar roboczy znajduje się w odpowiednim regionie geograficznym.
> Przechowywanie danych w usłudze log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).

Aby wybrać istniejący obszar roboczy Log Analytics:

1. W obszarze **Domyślna konfiguracja obszaru roboczego**wybierz pozycję **Użyj innego obszaru roboczego**.
    :::image type="content" source="./media/security-center-enable-data-collection/use-another-workspace.png" alt-text="Włączanie obsługi administracyjnej agenta Log Analytics"::: 

2. Z menu rozwijanego wybierz obszar roboczy, w którym mają być przechowywane zebrane dane.

   > [!NOTE]
   > W menu rozwijanym są dostępne wszystkie obszary robocze dla wszystkich subskrypcji. Aby uzyskać więcej informacji, zobacz [obszar roboczy Cross Subscription](security-center-enable-data-collection.md#cross-subscription-workspace-selection) . Musisz mieć uprawnienia dostępu do obszaru roboczego.
   >
   >

3. Wybierz pozycję **Zapisz**.
4. Po wybraniu opcji **Zapisz**zostanie wyświetlony monit z prośbą o ponowne skonfigurowanie monitorowanych maszyn wirtualnych, które zostały wcześniej połączone z domyślnym obszarem roboczym.

   - Wybierz pozycję **nie** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były stosowane tylko do nowych maszyn wirtualnych. Nowe ustawienia obszaru roboczego mają zastosowanie tylko do nowych instalacji agenta; nowo odnalezione maszyny wirtualne, na których nie zainstalowano agenta Log Analytics.
   - Wybierz pozycję **tak** , jeśli chcesz, aby nowe ustawienia obszaru roboczego były stosowane do wszystkich maszyn wirtualnych. Ponadto wszystkie maszyny wirtualne połączone z Security Center utworzonym obszarem roboczym są ponownie połączone z nowym docelowym obszarem roboczym.

   > [!NOTE]
   > W przypadku wybrania opcji tak nie należy usuwać obszarów roboczych utworzonych przez Security Center, dopóki wszystkie maszyny wirtualne nie zostaną ponownie połączone z nowym docelowym obszarem roboczym. Ta operacja kończy się niepowodzeniem, jeśli obszar roboczy zostanie zbyt wcześnie usunięty.
   >
   >

   - Aby anulować operację, wybierz pozycję **Anuluj**.

     ![Przejrzyj opcje, aby ponownie skonfigurować monitorowane maszyny wirtualne][3]

5. Wybierz, czy w obszarze roboczym będzie włączona usługa Azure Defender.

    Aby użyć istniejącego obszaru roboczego, ustaw warstwę cenową obszaru roboczego. Spowoduje to zainstalowanie rozwiązania Security Center w obszarze roboczym, jeśli jeszcze go nie ma.

    1. W menu głównym Security Center wybierz pozycję **cennik & ustawienia**.
     
    1. Wybierz obszar roboczy, do którego chcesz połączyć agenta.

    1. Wybierz pozycję **Azure Defender on** lub **Azure Defender off**.

   
   >[!NOTE]
   >Jeśli w obszarze roboczym jest już włączone rozwiązanie **Security** lub **SecurityCenterFree** , Cennik zostanie ustawiony automatycznie. 


## <a name="cross-subscription-workspace-selection"></a>Wybór obszaru roboczego między subskrypcjami
Po wybraniu obszaru roboczego, w którym będą przechowywane dane, są dostępne wszystkie obszary robocze we wszystkich subskrypcjach. Możliwość wybrania obszaru roboczego z innej subskrypcji pozwala na zbieranie danych z maszyn wirtualnych działających w różnych subskrypcjach i przechowywanie ich w wybranym obszarze roboczym. Ten wybór jest przydatny, jeśli w swojej organizacji używasz scentralizowanego obszaru roboczego i chcesz używać go do zbierania danych dotyczących zabezpieczeń. Aby uzyskać więcej informacji na temat zarządzania obszarami roboczymi, zobacz [Zarządzanie dostępem do obszaru roboczego](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).



## <a name="data-collection-tier"></a>Warstwa zbierania danych
Wybranie warstwy zbierania danych w usłudze Azure Security Center ma wpływ tylko na przechowywanie zdarzeń zabezpieczeń w obszarze roboczym usługi Log Analytics. Agent Log Analytics będzie nadal zbierać i analizować zdarzenia zabezpieczeń wymagane do ochrony przed zagrożeniami Azure Security Center, niezależnie od tego, która warstwa zdarzeń zabezpieczeń została wybrana do przechowywania w obszarze roboczym Log Analytics (jeśli istnieje). Jeśli postanowisz przechowywać zdarzenia zabezpieczeń w obszarze roboczym, spowoduje to aktywowanie badania, wyszukiwania i inspekcji tych zdarzeń w obszarze roboczym. 
> [!NOTE]
> Przechowywanie danych w usłudze log Analytics może wiązać się z dodatkowymi opłatami za przechowywanie danych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Można wybrać odpowiednie zasady filtrowania dla subskrypcji i obszarów roboczych z czterech zestawów zdarzeń, które mają być przechowywane w obszarze roboczym: 

- **Brak** — Wyłącz magazyn zdarzeń zabezpieczeń. Jest to ustawienie domyślne.
- **Minimalny** — mniejszy zestaw zdarzeń dla klientów, którzy chcą zminimalizować wolumin zdarzeń.
- **Wspólne** — jest to zestaw zdarzeń, które spełniają większość klientów i umożliwiają im pełny dziennik inspekcji.
- **Wszystkie zdarzenia** — dla klientów, którzy chcą mieć pewność, że wszystkie zdarzenia są przechowywane.


> [!NOTE]
> Te zestawy zdarzeń zabezpieczeń są dostępne tylko w usłudze Azure Defender. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
Te zestawy zostały zaprojektowane w celu rozwiązywania typowych scenariuszy. Upewnij się, że należy obliczyć, który z nich odpowiada Twoim potrzebom przed wdrożeniem.
>
>

Aby określić zdarzenia, które będą należeć do **wspólnych** i **minimalnych** zestawów zdarzeń, firma Microsoft współpracuje z klientami i standardami branżowymi, aby dowiedzieć się więcej o niefiltrowanej częstotliwości poszczególnych zdarzeń i ich użyciu. W tym procesie użyto następujących wytycznych:

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
> - W przypadku używania obiektu zasady grupy (GPO) zaleca się włączenie zdarzenia dotyczącego tworzenia procesu zasad inspekcji 4688 i pola *wiersza polecenia* wewnątrz zdarzenia 4688. Aby uzyskać więcej informacji o zdarzeniu tworzenia procesu 4688, zobacz [często zadawane pytania](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)dotyczące Security Center. Aby uzyskać więcej informacji na temat tych zasad inspekcji, zobacz [zalecenia dotyczące zasad inspekcji](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Aby włączyć zbieranie danych na potrzeby [adaptacyjnego sterowania aplikacjami](security-center-adaptive-application.md), Security Center konfiguruje lokalne zasady funkcji AppLocker w trybie inspekcji, aby zezwolić na wszystkie aplikacje. Spowoduje to generowanie przez funkcję AppLocker zdarzeń, które są następnie zbierane i wykorzystywane przez Security Center. Należy pamiętać, że te zasady nie zostaną skonfigurowane na wszystkich komputerach, na których już skonfigurowano zasady funkcji AppLocker. 
> - Aby zbierać zdarzenia platformy filtrowania systemu Windows o [identyfikatorze 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), należy włączyć funkcję [filtrowania inspekcji dla platformy](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (auditpol/Set/Subcategory: "Filtering Platform Connection"/Success: Enable)
>

Aby wybrać zasady filtrowania:
1. Na stronie **zbieranie danych** wybierz zasady filtrowania w obszarze **przechowuj dodatkowe dane pierwotne — zdarzenia zabezpieczeń systemu Windows**.
 
1. Wybierz pozycję **Zapisz**.
    :::image type="content" source="./media/security-center-enable-data-collection/data-collection-tiers.png" alt-text="Włączanie obsługi administracyjnej agenta Log Analytics":::

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatyczne Inicjowanie obsługi w przypadku istniejącej instalacji agenta <a name="preexisting"></a> 

W następujących przypadkach użycia określono, jak funkcja automatycznego udostępniania będzie działać w przypadkach, gdy jest już zainstalowany agent lub rozszerzenie. 

- Agent Log Analytics jest zainstalowany na komputerze, ale nie jako rozszerzenie (Agent bezpośredni)<br>
Jeśli Agent Log Analytics jest instalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), Security Center zainstaluje rozszerzenie agenta Log Analytics i może uaktualnić agenta Log Analytics do najnowszej wersji.
Zainstalowany agent będzie kontynuował raportowanie do już skonfigurowanych obszarów roboczych, a ponadto przeprowadzi raport do obszaru roboczego skonfigurowanego w Security Center (wiele multihostingu jest obsługiwana na maszynach z systemem Windows).
Jeśli skonfigurowany obszar roboczy jest obszarem roboczym użytkownika (nie Security Center domyślnym obszarem roboczym), należy zainstalować na nim rozwiązanie "Security/" securityFree "dla Security Center, aby rozpocząć przetwarzanie zdarzeń z maszyn wirtualnych i komputerów zgłaszanych do tego obszaru roboczego.<br>
<br>
W przypadku maszyn z systemem Linux Agent multihostingu nie jest jeszcze obsługiwany — w związku z tym jeśli zostanie wykryta istniejąca instalacja agenta, automatyczne Inicjowanie obsługi nie zostanie wykonane i konfiguracja maszyny nie zostanie zmieniona.
<br>
W przypadku istniejących maszyn w ramach subskrypcji do Security Center przed 17 marca 2019, gdy zostanie wykryty istniejący Agent, rozszerzenie agenta Log Analytics nie zostanie zainstalowane i nie wpłynie to na komputer. W przypadku tych maszyn Zobacz zalecenia dotyczące rozwiązywania problemów z kondycją agenta monitorowania na swoich komputerach, aby rozwiązać problemy z instalacją agenta na tych komputerach.

  
- Agent System Center Operations Manager jest zainstalowany na maszynie<br>
Program Security Center zainstaluje rozszerzenie agenta Log Analytics obok istniejących Operations Manager. Istniejący Agent Operations Manager będzie kontynuował raportowanie do serwera Operations Manager w normalny sposób. Agent Operations Manager i Agent Log Analytics mają wspólne biblioteki uruchomieniowe, które zostaną zaktualizowane do najnowszej wersji w trakcie tego procesu. Jeśli zainstalowano agenta **Operations Manager w wersji 2012, nie należy** włączać automatycznej aprowizacji.<br>

- Istnieje już istniejące rozszerzenie maszyny wirtualnej<br>
    - Gdy Agent monitorowania jest zainstalowany jako rozszerzenie, konfiguracja rozszerzenia umożliwia raportowanie tylko jednego obszaru roboczego. Security Center nie przesłania istniejących połączeń z obszarami roboczymi użytkowników. Security Center będą przechowywać dane zabezpieczeń z maszyny wirtualnej w już podłączonym obszarze roboczym, pod warunkiem, że zostało na nim zainstalowane rozwiązanie "Security" lub "securityFree". Security Center może uaktualnić wersję rozszerzenia do najnowszej wersji w tym procesie.  
    - Aby zobaczyć, w którym obszarze roboczym jest wysyłane dane rozszerzenie, Uruchom test w celu [sprawdzenia łączności z Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Alternatywnie możesz otworzyć obszary robocze Log Analytics, wybrać obszar roboczy, wybrać maszynę wirtualną i sprawdzić połączenie z agentem Log Analytics. 
    - Jeśli masz środowisko, w którym jest zainstalowany agent Log Analytics na stacjach roboczych klienta i raportowanie do istniejącego Log Analytics obszaru roboczego, przejrzyj listę [systemów operacyjnych obsługiwanych przez Azure Security Center](security-center-os-coverage.md) , aby upewnić się, że system operacyjny jest obsługiwany. Aby uzyskać więcej informacji, zobacz [istniejących klientów usługi log Analytics](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Wyłącz automatyczne Inicjowanie obsługi <a name="offprovisioning"></a>
Aby wyłączyć automatyczne Inicjowanie obsługi agenta Log Analytics:

1. Z poziomu menu Security Center w portalu wybierz pozycję **cennik & ustawienia**.
2. Wybierz odpowiednią subskrypcję.

    :::image type="content" source="./media/security-center-enable-data-collection/select-subscription.png" alt-text="Włączanie obsługi administracyjnej agenta Log Analytics":::

3. Wybierz pozycję **zbieranie danych**.
4. W obszarze **Automatyczna obsługa administracyjna**wybierz pozycję **Wyłącz** , aby wyłączyć automatyczne Inicjowanie obsługi.
5. Wybierz pozycję **Zapisz**. 


Gdy funkcja automatycznej aprowizacji jest wyłączona (wyłączona), domyślna sekcja konfiguracji obszaru roboczego nie zostanie wyświetlona.

Jeśli wyłączysz funkcję autozastrzeganie, gdy wcześniej jej Agent nie zostanie zainicjowany na nowych maszynach wirtualnych.

 
> [!NOTE]
>  Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Aby uzyskać informacje dotyczące usuwania rozszerzenia pakietu OMS, zobacz [Jak mogę usuwania rozszerzeń pakietu OMS zainstalowanych przez Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Ręczna obsługa agentów <a name="manual-agent"></a>
 
Istnieje kilka sposobów ręcznego instalowania agenta Log Analytics. W przypadku ręcznego instalowania należy wyłączyć funkcję autoaprowizacji.

### <a name="operations-management-suite-vm-extension-deployment"></a>Wdrożenie rozszerzenia maszyny wirtualnej pakietu Operations Management Suite 

Można ręcznie zainstalować agenta Log Analytics, aby Security Center mógł zbierać dane dotyczące zabezpieczeń z maszyn wirtualnych i udostępniać zalecenia i alerty.

1. Wyłącz funkcję autoaprowizacji.

1. Opcjonalnie możesz utworzyć obszar roboczy.

1. Włącz usługę Azure Defender w obszarze roboczym, na którym instalujesz agenta Log Analytics:

    1. W menu Security Center wybierz pozycję **cennik & ustawienia**.

    1. Ustaw obszar roboczy, w którym jest instalowany Agent. Upewnij się, że obszar roboczy znajduje się w tej samej subskrypcji, której używasz w Security Center i że masz uprawnienia do odczytu/zapisu w obszarze roboczym.

    1. Ustaw usługę Azure Defender na wartość włączone, a następnie wybierz pozycję **Zapisz**.

       >[!NOTE]
       >Jeśli w obszarze roboczym jest już włączone rozwiązanie **Security** lub **SecurityCenterFree** , Cennik zostanie ustawiony automatycznie. 

1. Jeśli chcesz wdrożyć agentów na nowych maszynach wirtualnych przy użyciu szablonu Menedżer zasobów, Zainstaluj agenta Log Analytics:

   a.  [Zainstaluj agenta Log Analytics dla systemu Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Zainstaluj agenta Log Analytics dla systemu Linux](../virtual-machines/extensions/oms-linux.md)

1. Aby wdrożyć rozszerzenia na istniejących maszynach wirtualnych, postępuj zgodnie z instrukcjami w [temacie zbieranie danych o platformie Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Sekcja **zbiera dane zdarzeń i wydajności,** które są opcjonalne.
   >

1. Aby wdrożyć rozszerzenie przy użyciu programu PowerShell, Skorzystaj z instrukcji z dokumentacji dotyczącej maszyn wirtualnych:

    - [Dla maszyn z systemem Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#powershell-deployment)

    - [Dla maszyn z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-linux?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#azure-cli-deployment)



> [!NOTE]
> Aby uzyskać instrukcje na temat sposobu dołączania Security Center przy użyciu programu PowerShell, zobacz Automatyzowanie dołączania [Azure Security Center przy użyciu programu PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

-   Aby zidentyfikować problemy z instalacją automatycznej obsługi administracyjnej, zobacz [monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).

-  Aby zidentyfikować wymagania sieciowe agenta monitorowania, zobacz [Rozwiązywanie problemów z wymaganiami sieci agenta monitorowania](security-center-troubleshooting-guide.md#mon-network-req).
-   Aby identyfikować ręczne problemy z dołączaniem, zobacz [Jak rozwiązywać problemy z dołączaniem do pakietu Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Aby zidentyfikować problemy dotyczące niemonitorowanych maszyn wirtualnych i komputerów:

    Maszyna wirtualna lub komputer nie jest monitorowany przez Security Center, jeśli na komputerze nie jest uruchomione rozszerzenie agenta Log Analytics. Na komputerze może być już zainstalowany agent lokalny, na przykład Agent usługi OMS Direct lub Agent System Center Operations Manager. Maszyny z tymi agentami są identyfikowane jako niemonitorowane, ponieważ nie są w pełni obsługiwane w Security Center. Aby w pełni korzystać ze wszystkich możliwości Security Center, wymagane jest rozszerzenie agenta Log Analytics.

    Aby uzyskać więcej informacji na temat przyczyn, Security Center nie może pomyślnie monitorować maszyn wirtualnych i komputerów zainicjowanych do automatycznej aprowizacji, zobacz [monitorowanie problemów z kondycją agenta](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak działa zbieranie danych i automatyczne Inicjowanie obsługi w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące strony:

* [Centrum zabezpieczeń Azure — często zadawane pytania](faq-general.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.



<!--Image references-->
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
