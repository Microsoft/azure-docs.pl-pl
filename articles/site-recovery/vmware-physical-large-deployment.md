---
title: Skalowanie oprogramowania VMware/fizycznego odzyskiwania po awarii za pomocą Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla dużej liczby lokalnych maszyn wirtualnych VMware lub serwerów fizycznych przy użyciu Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cc87429f269fba5083b87e2c328f0e21de9707ff
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454351"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Konfigurowanie odzyskiwania po awarii na dużą skalę dla maszyn wirtualnych VMware/serwerów fizycznych

W tym artykule opisano sposób konfigurowania odzyskiwania po awarii na platformie Azure dla dużych liczb (> 1000) lokalnych maszyn wirtualnych VMware lub serwerów fizycznych w środowisku produkcyjnym przy użyciu usługi [Azure Site Recovery](site-recovery-overview.md) .


## <a name="define-your-bcdr-strategy"></a>Zdefiniuj strategię BCDR

W ramach strategii ciągłości działania i odzyskiwania po awarii (BCDR) można definiować cele punktu odzyskiwania (RPO) i cele czasu odzyskiwania (RTO) dla aplikacji i obciążeń firmy. RTO mierzy czas trwania i poziom usług, w których należy przywrócić i udostępnić aplikację biznesową lub proces, aby uniknąć problemów z ciągłością.
- Site Recovery zapewnia ciągłą replikację maszyn wirtualnych VMware i serwerów fizycznych oraz umowę [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) dla RTO.
- Zgodnie z planem odzyskiwania danych na dużą skalę na potrzeby maszyn wirtualnych VMware i ustalania potrzebnych zasobów platformy Azure można określić wartość RTO, która będzie używana na potrzeby obliczeń pojemności.


## <a name="best-practices"></a>Najlepsze rozwiązania

Niektóre ogólne najlepsze rozwiązania dotyczące odzyskiwania po awarii na dużą skalę. Te najlepsze rozwiązania zostały omówione bardziej szczegółowo w następnych sekcjach dokumentu.

- **Określenie wymagań docelowych**: oszacowanie wydajności i potrzeb zasobów na platformie Azure przed rozpoczęciem konfigurowania odzyskiwania po awarii.
- **Zaplanuj składniki Site Recovery**: Ustal, jakie składniki Site Recovery (serwer konfiguracji, serwery przetwarzania) muszą spełniać szacunkową pojemność.
- **Skonfiguruj co najmniej jeden serwer przetwarzania skalowalny** w poziomie: nie używaj serwera przetwarzania, który jest domyślnie uruchomiony na serwerze konfiguracji. 
- **Uruchom najnowsze aktualizacje**: zespół site Recoverya regularnie zwalnia nowe wersje składników Site Recovery i upewnij się, że korzystasz z najnowszych wersji. Aby to ułatwić, śledź [nowości](site-recovery-whats-new.md) dotyczące aktualizacji oraz [włączaj i instaluj aktualizacje](service-updates-how-to.md) podczas ich wydawania.
- **Monitoruj proaktywne**: w przypadku odzyskiwania po awarii i uruchamiania, należy aktywnie monitorować stan i kondycję replikowanych maszyn oraz zasobów infrastruktury.
- **Przechodzenie do odzyskiwania po awarii**: należy regularnie uruchamiać funkcję odzyskiwania po awarii. Nie mają one wpływu na środowisko produkcyjne, ale pomagają zapewnić, że tryb failover na platformie Azure będzie działał zgodnie z oczekiwaniami w razie potrzeby.



## <a name="gather-capacity-planning-information"></a>Zbierz informacje o planowaniu pojemności

Zbierz informacje o środowisku lokalnym, aby ułatwić ocenę i oszacowanie potrzeb związanych z pojemnością platformy Target.
- W przypadku oprogramowania VMware Uruchom Planista wdrażania dla maszyn wirtualnych VMware, aby to zrobić.
- W przypadku serwerów fizycznych Zbierz informacje ręcznie.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Uruchamianie Planista wdrażania dla maszyn wirtualnych VMware

Planista wdrażania ułatwia zbieranie informacji o środowisku lokalnym programu VMware.

- Uruchom Planista wdrażania w okresie, który reprezentuje typowe zmiany dla maszyn wirtualnych. Spowoduje to wygenerowanie dokładniejszych oszacowań i zaleceń.
- Zalecamy uruchomienie Planista wdrażania na komputerze z serwerem konfiguracji, ponieważ planista oblicza przepływność z serwera, na którym jest uruchomiony. [Dowiedz się więcej](site-recovery-vmware-deployment-planner-run.md#get-throughput) na temat mierzenia przepływności.
- Jeśli nie masz jeszcze skonfigurowanego serwera konfiguracji:
    - [Zapoznaj się z omówieniem](vmware-physical-azure-config-process-server-overview.md) składników Site Recovery.
    - [Skonfiguruj serwer konfiguracji](vmware-azure-deploy-configuration-server.md), aby można było uruchomić na nim planista wdrażania.

Następnie uruchom Planistę w następujący sposób:

1. [Dowiedz się więcej na temat](site-recovery-deployment-planner.md) planista wdrażania. Możesz pobrać najnowszą wersję z portalu lub [pobrać ją bezpośrednio](https://aka.ms/asr-deployment-planner).
2. Zapoznaj się z [wymaganiami wstępnymi](site-recovery-deployment-planner.md#prerequisites) i [najnowszymi aktualizacjami](site-recovery-deployment-planner-history.md) planista wdrażania, a następnie [Pobierz i Wyodrębnij](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) narzędzie.
3. [Uruchom planista wdrażania](site-recovery-vmware-deployment-planner-run.md) na serwerze konfiguracji.
4. [Generuj raport](site-recovery-vmware-deployment-planner-run.md#generate-report) podsumowujący oszacowania i zalecenia.
5. Analizuj [zalecenia dotyczące raportu](site-recovery-vmware-deployment-planner-analyze-report.md) i [oszacowania kosztów](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Domyślnie narzędzie jest skonfigurowane do profilowania i generowania raportu dla maksymalnie 1000 maszyn wirtualnych. Można zmienić ten limit poprzez zwiększenie wartości klucza MaxVMsSupported w pliku ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Wymagania i pojemność planu docelowego (platforma Azure)

Korzystając z zebranych szacunków i zaleceń, można zaplanować zasoby docelowe i pojemność. Jeśli uruchomiono Planista wdrażania dla maszyn wirtualnych VMware, możesz użyć szeregu [zaleceń dotyczących raportów](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) , aby pomóc.

- **Zgodne maszyny wirtualne**: Użyj tego numeru, aby określić liczbę maszyn wirtualnych, które są gotowe do odzyskiwania po awarii na platformie Azure. Zalecenia dotyczące przepustowości sieci i rdzeni platformy Azure są oparte na tej liczbie.
- **Wymagana przepustowość sieci**: należy zwrócić uwagę na przepustowość potrzebną w przypadku replikacji różnicowej zgodnych maszyn wirtualnych. 
    - Po uruchomieniu planisty należy określić żądany cel punktu odzyskiwania w minutach. Zalecenia pokazują, że przepustowość wymagana do osiągnięcia tego celu punktu odzyskiwania 100% i 90% czasu. 
    - W zaleceniach dotyczących przepustowości sieci należy wziąć pod uwagę przepustowość wymaganą dla łącznej liczby serwerów konfiguracji i serwerów przetwarzania zalecanych w programie Planner.
- **Wymagane rdzenie platformy Azure**: Zwróć uwagę na liczbę rdzeni, których potrzebujesz w docelowym regionie platformy Azure, na podstawie liczby zgodnych maszyn wirtualnych. Jeśli nie masz wystarczającej liczby rdzeni, w trybie failover Site Recovery nie będzie można utworzyć wymaganych maszyn wirtualnych platformy Azure.
- **Zalecany rozmiar partii maszyn wirtualnych**: zalecany rozmiar wsadu bazuje na możliwości zakończenia replikacji początkowej dla partii w ciągu 72 godzin domyślnie, jednocześnie spełniając cel punktu odzyskiwania 100%. Wartość godzinowa może być modyfikowana.

Te zalecenia umożliwiają planowanie zasobów platformy Azure, przepustowości sieci i partii maszyn wirtualnych.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planowanie subskrypcji i przydziałów platformy Azure

Chcemy upewnić się, że dostępne przydziały w subskrypcji docelowej są wystarczające do obsługi trybu failover.

**Zadanie** | **Szczegóły** | **Akcja**
--- | --- | ---
**Sprawdź rdzenie** | Jeśli rdzenie w dostępnym limicie przydziału nie są równe ani przekraczają łączną liczbę elementów docelowych w momencie przejścia w tryb failover, tryb failover zakończy się niepowodzeniem. | W przypadku maszyn wirtualnych VMware Sprawdź, czy masz wystarczającą liczbę rdzeni w subskrypcji docelowej, aby spełnić zalecenia dotyczące Planista wdrażania Core.<br/><br/> W przypadku serwerów fizycznych Sprawdź, czy rdzenie platformy Azure są zgodne z ręcznymi oszacowaniami.<br/><br/> Aby sprawdzić przydziały, w Azure Portal > **subskrypcji** kliknij pozycję **użycie i limity przydziału**.<br/><br/> [Dowiedz się więcej](../azure-portal/supportability/resource-manager-core-quotas-request.md) o zwiększaniu przydziałów.
**Sprawdzanie limitów trybu failover** | Liczba nie może trybu failover przekracza limity Site Recovery trybu failover. |  Jeśli przełączenie w tryb failover przekracza limity, możesz dodać subskrypcje i przełączyć się w tryb pracy awaryjnej do wielu subskrypcji lub zwiększyć przydział dla subskrypcji. 


### <a name="failover-limits"></a>Limity trybu failover

Limity wskazują liczbę przełączeń w tryb failover, które są obsługiwane przez Site Recovery w ciągu jednej godziny, przy założeniu, że trzy dyski na maszynę.

Co oznacza zgodność? Aby uruchomić maszynę wirtualną platformy Azure, platforma Azure wymaga, aby niektóre sterowniki były w stanie uruchomienia rozruchu, a usługi, takie jak DHCP, były uruchamiane automatycznie.
- Na maszynach, które są zgodne, będą już stosowane te ustawienia.
- W przypadku maszyn z systemem Windows można aktywnie sprawdzić zgodność i udostępnić je w razie potrzeby. [Dowiedz się więcej](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Komputery z systemem Linux są zgodne tylko w momencie przejścia w tryb failover.

**Maszyna jest zgodna z platformą Azure?** | **Limity maszyn wirtualnych platformy Azure (tryb failover dysku zarządzanego)**
--- | --- 
Tak | 2000
Nie | 1000

- Limity zakładają, że minimalne inne zadania są w toku w regionie docelowym dla subskrypcji.
- Niektóre regiony platformy Azure są mniejsze i mogą mieć nieco niższe limity.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planowanie łączności z infrastrukturą i maszyną wirtualną

Po przejściu w tryb failover na platformę Azure potrzebujesz obciążeń, które będą działać w sposób lokalny, i umożliwienie użytkownikom dostępu do obciążeń uruchomionych na maszynach wirtualnych platformy Azure.

- [Dowiedz się więcej](site-recovery-active-directory.md#test-failover-considerations) o przełączaniu infrastruktury lokalnej na platformę Azure za pośrednictwem usługi Active Directory lub systemu DNS.
- [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) na temat przygotowywania do łączenia się z maszynami wirtualnymi platformy Azure po przejściu



## <a name="plan-for-source-capacity-and-requirements"></a>Planowanie pojemności i wymagań źródłowych

Ważne jest posiadanie wystarczającej liczby serwerów konfiguracji i skalowalnych w poziomie serwerów procesów w celu spełnienia wymagań dotyczących pojemności. Po rozpoczęciu wdrażania na dużą skalę Rozpocznij pracę z jednym serwerem konfiguracji i jednym serwerem przetwarzania skalowalnym w poziomie. Po osiągnięciu określonych limitów Dodaj kolejne serwery.

>[!NOTE]
> W przypadku maszyn wirtualnych VMware Planista wdrażania wykonuje pewne zalecenia dotyczące serwerów konfiguracji i procesów, które są potrzebne. Zalecamy używanie tabel uwzględnionych w poniższych procedurach, a nie zgodnie z zaleceniem Planista wdrażania. 


## <a name="set-up-a-configuration-server"></a>Konfigurowanie serwera konfiguracji
 
Wydajność serwera konfiguracji ma wpływ na liczbę maszyn replikowanych, a nie przez współczynnik zmian danych. Aby ustalić, czy potrzebujesz dodatkowych serwerów konfiguracji, Użyj tych zdefiniowanych limitów maszyn wirtualnych.

**Procesor CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Limit zreplikowanych maszyn**
 --- | --- | --- | ---
8 procesorów wirtualnych vCPU<br> 2 gniazda * 4 rdzenie o częstotliwości 2,5 GHz | 16 GB | 600 GB | Do 550 maszyn<br> Przyjęto założenie, że każdy komputer ma trzy dyski o pojemności 100 GB każdego z nich.

- Limity te są oparte na serwerze konfiguracji skonfigurowanym za pomocą szablonu OVF.
- Limity założono, że nie używasz serwera przetwarzania, który jest domyślnie uruchomiony na serwerze konfiguracji.

Jeśli musisz dodać nowy serwer konfiguracji, wykonaj następujące instrukcje:

- [Skonfiguruj serwer konfiguracji](vmware-azure-deploy-configuration-server.md) na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware przy użyciu szablonu OVF.
- [Skonfiguruj ręcznie serwer konfiguracji](physical-azure-set-up-source.md) dla serwerów fizycznych lub dla wdrożeń VMware, które nie mogą używać szablonu OVF.

Podczas konfigurowania serwera konfiguracji należy pamiętać, że:

- Po skonfigurowaniu serwera konfiguracji należy wziąć pod uwagę subskrypcję i magazyn, w której znajduje się ten element, ponieważ nie należy ich zmieniać po zakończeniu instalacji. Jeśli musisz zmienić magazyn, musisz usunąć skojarzenie serwera konfiguracji z magazynem i ponownie go zarejestrować. Spowoduje to zatrzymanie replikacji maszyn wirtualnych w magazynie.
- Jeśli chcesz skonfigurować serwer konfiguracji z wieloma kartami sieciowymi, należy to zrobić podczas konfigurowania. Nie można wykonać tej czynności po zarejestrowaniu serwera konfiguracji w magazynie.

## <a name="set-up-a-process-server"></a>Konfigurowanie serwera przetwarzania

Wydajności serwera przetwarzania mają wpływ szybkości zmian danych, a nie przez liczbę maszyn włączonych do replikacji.

- W przypadku dużych wdrożeń należy zawsze mieć co najmniej jeden serwer przetwarzania skalowalny w poziomie.
- Aby ustalić, czy potrzebujesz dodatkowych serwerów, Skorzystaj z poniższej tabeli.
- Zalecamy dodanie serwera o najwyższej specyfikacji. 


**Procesor CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Współczynnik zmian**
 --- | --- | --- | --- 
12 procesorów wirtualnych vCPU<br> 2 gniazda * 6 rdzenie, 2,5 GHz | 24 GB | 1 GB | Do 2 TB dziennie

Skonfiguruj serwer przetwarzania w następujący sposób:

1. Zapoznaj się z [wymaganiami wstępnymi](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Zainstaluj serwer w [portalu](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)lub z [wiersza polecenia](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Skonfiguruj zreplikowane maszyny do korzystania z nowego serwera. Jeśli masz już replikację maszyn:
    - Całe obciążenie serwera przetwarzania można [przenieść](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) na nowy serwer przetwarzania.
    - Alternatywnie można [przenieść](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) określone maszyny wirtualne do nowego serwera przetwarzania.



## <a name="enable-large-scale-replication"></a>Włącz replikację na dużą skalę

Po zaplanowaniu wydajności i wdrożeniu wymaganych składników i infrastruktury Włącz replikację dla dużej liczby maszyn wirtualnych.

1. Sortuj maszyny do partii. Należy włączyć replikację dla maszyn wirtualnych w ramach partii, a następnie przejść do następnej partii.

    - W przypadku maszyn wirtualnych VMware można użyć [zalecanego rozmiaru partii maszyn wirtualnych](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) w raporcie planista wdrażania.
    - W przypadku maszyn fizycznych zalecamy zidentyfikowanie partii na podstawie maszyn o podobnym rozmiarze i ilości danych oraz dostępnej przepływności sieci. Celem jest przetwarzanie wsadowe maszyn, które prawdopodobnie zakończą replikację początkową w tym samym czasie.
    
2. Jeśli zmiany dysku dla maszyny są wysokie lub przekraczają limity w thePlanner wdrożenia, można przenieść niekrytyczne pliki, które nie są potrzebne do replikacji (na przykład zrzutów dzienników lub plików tymczasowych) poza maszyną. W przypadku maszyn wirtualnych VMware można przenieść te pliki na osobny dysk, a następnie [wykluczyć ten dysk](vmware-azure-exclude-disk.md) z replikacji.
3. Przed włączeniem replikacji Sprawdź, czy maszyny spełniają [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Skonfiguruj zasady replikacji dla [maszyn wirtualnych VMware](vmware-azure-set-up-replication.md#create-a-policy) lub [serwerów fizycznych](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Włącz replikację dla [maszyn wirtualnych VMware](vmware-azure-enable-replication.md) lub [serwerów fizycznych](physical-azure-disaster-recovery.md#enable-replication). Spowoduje to rozpoczęcie replikacji początkowej dla wybranych maszyn.

## <a name="monitor-your-deployment"></a>Monitorowanie wdrożenia

Po rozpoczęciu replikacji pierwszej partii maszyn wirtualnych Rozpocznij monitorowanie wdrożenia w następujący sposób:  

1. Do monitorowania stanu kondycji replikowanych maszyn należy przypisać administratora odzyskiwania po awarii.
2. [Monitoruj zdarzenia](site-recovery-monitor-and-troubleshoot.md) dla zreplikowanych elementów i infrastruktury.
3. [Monitoruj kondycję](vmware-physical-azure-monitor-process-server.md) serwerów procesów skalowalnych w poziomie.
4. Zarejestruj się, aby otrzymywać [powiadomienia e-mail](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) o zdarzeniach w celu łatwiejszego monitorowania.
5. Przeprowadź regularne [Przechodzenie do odzyskiwania po awarii](site-recovery-test-failover-to-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="plan-for-large-scale-failovers"></a>Planowanie trybu failover w dużej skali

W przypadku awarii może zajść potrzeba przełączenia dużej liczby maszyn/obciążeń na platformę Azure. Przygotuj się dla tego typu zdarzenia w następujący sposób.

Przed przejściem do trybu failover można przygotować się w następujący sposób:

- [Przygotuj infrastrukturę i maszyny wirtualne](#plan-infrastructure-and-vm-connectivity) , aby Twoje obciążenia będą dostępne po przejściu w tryb failover, a użytkownicy mogą uzyskiwać dostęp do maszyn wirtualnych platformy Azure.
- Zanotuj [limity trybu failover](#failover-limits) wcześniej w tym dokumencie. Upewnij się, że przełączenie w tryb failover będzie należeć do tych limitów.
- Uruchom przechodzenie do regularnego [odzyskiwania po awarii](site-recovery-test-failover-to-azure.md). Przechodzenie do szczegółów pomocy:
    - Znajdź luki w wdrożeniu przed przejściem w tryb failover.
    - Oszacuj kompleksowe RTO dla aplikacji.
    - Oszacowanie kompleksowego celu punktu odzyskiwania dla obciążeń.
    - Identyfikuj konflikty zakresów adresów IP.
    - Podczas przeprowadzania testów zaleca się, aby nie używać sieci produkcyjnych do przechodzenia do szczegółów i czyścić test pracy w trybie failover po każdym przejściu do szczegółów.

Aby uruchomić tryb failover o dużej skali, zalecamy wykonanie następujących czynności:

1. Utwórz plany odzyskiwania dla pracy w trybie failover.
    - Każdy plan odzyskiwania może wyzwolić tryb failover do 100 maszyn.
    - [Dowiedz się więcej](recovery-plan-overview.md) o planach odzyskiwania.
2. Dodaj Azure Automation skrypty elementu Runbook do planów odzyskiwania, aby zautomatyzować zadania wykonywane ręcznie na platformie Azure. Typowe zadania obejmują Konfigurowanie modułów równoważenia obciążenia, aktualizowanie systemu DNS itd. [Dowiedz się więcej](site-recovery-runbook-automation.md)
2. Przed przejściem w tryb failover Przygotuj maszyny z systemem Windows tak, aby były one zgodne ze środowiskiem platformy Azure. [Limity trybu failover](#plan-azure-subscriptions-and-quotas) są większe dla maszyn, które są zgodne. [Dowiedz się więcej](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) o elementach Runbook.
4.  Wyzwól tryb failover za pomocą polecenia cmdlet [Start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) środowiska PowerShell wraz z planem odzyskiwania.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie usługi Site Recovery](site-recovery-monitor-and-troubleshoot.md)
