---
title: Skalowanie odzyskiwania vmware/fizycznego odzyskiwania po awarii za pomocą usługi Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla dużej liczby lokalnych maszyn wirtualnych VMware lub serwerów fizycznych za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409769"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Konfigurowanie odzyskiwania po awarii na dużą skalę dla maszyn wirtualnych VMware/serwerów fizycznych

W tym artykule opisano sposób konfigurowania odzyskiwania po awarii na platformie Azure dla dużej liczby (> 1000) lokalnych maszyn wirtualnych VMware lub serwerów fizycznych w środowisku produkcyjnym przy użyciu usługi [Azure Site Recovery.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Zdefiniuj swoją strategię BCDR

W ramach strategii ciągłości biznesowej i odzyskiwania po awarii (BCDR) definiujesz cele punktu odzyskiwania (RPO) i cele czasu odzyskiwania (RTO) dla aplikacji biznesowych i obciążeń. RTO mierzy czas trwania czasu i poziomu usług, w którym aplikacja biznesowa lub proces muszą zostać przywrócone i dostępne, aby uniknąć problemów z ciągłością.
- Usługa Site Recovery zapewnia ciągłą replikację maszyn wirtualnych VMware i serwerów fizycznych oraz umowę [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) dla obiektu RTO.
- Planowanie odzyskiwania po awarii na dużą skalę dla maszyn wirtualnych VMware i wymyślenie potrzebnych zasobów platformy Azure można określić wartość rto, która będzie używana do obliczania zdolności produkcyjnych.


## <a name="best-practices"></a>Najlepsze rozwiązania

Niektóre ogólne najlepsze rozwiązania dotyczące odzyskiwania po awarii na dużą skalę. Te najlepsze rozwiązania zostały omówione bardziej szczegółowo w następnych sekcjach dokumentu.

- **Identyfikowanie wymagań docelowych:** Przed skonfigurowaniem odzyskiwania po awarii należy określić zapotrzebowanie na pojemność i zasoby na platformie Azure.
- **Planowanie składników odzyskiwania witryny:** Dowiedz się, jakie składniki odzyskiwania witryny (serwer konfiguracji, serwery przetwarzania) są potrzebne do osiągnięcia szacowanej pojemności.
- **Skonfiguruj co najmniej jeden serwer procesów skalowanych w poziomie:** nie używaj serwera przetwarzania, który jest domyślnie uruchomiony na serwerze konfiguracji. 
- **Uruchom najnowsze aktualizacje:** Zespół odzyskiwania witryny regularnie wydaje nowe wersje składników usługi Site Recovery i upewnij się, że korzystasz z najnowszych wersji. Aby w tym pomóc, śledź [nowości](site-recovery-whats-new.md) w aktualizacjach oraz [włączaj i instaluj aktualizacje](service-updates-how-to.md) w miarę ich wydawania.
- **Monitoruj proaktywnie:** Po uruchomieniu odzyskiwania po awarii należy aktywnie monitorować stan i kondycję zreplikowanych maszyn i zasobów infrastruktury.
- **Ćwiczenia odzyskiwania po awarii:** Należy regularnie uruchamiać ćwiczenia odzyskiwania po awarii. Nie mają one wpływu na środowisko produkcyjne, ale pomagają zapewnić, że praca awaryjna na platformie Azure będzie działać zgodnie z oczekiwaniami, gdy jest to potrzebne.



## <a name="gather-capacity-planning-information"></a>Zbieranie informacji o planowaniu pojemności

Zbieraj informacje o środowisku lokalnym, aby pomóc w ocenie i oszacowaniu potrzeb w zakresie pojemności docelowej (Azure).
- W przypadku oprogramowania VMware uruchom planer wdrażania maszyn wirtualnych VMware, aby to zrobić.
- W przypadku serwerów fizycznych należy zbierać informacje ręcznie.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Uruchamianie planisty wdrażania maszyn wirtualnych VMware

Planer wdrażania pomaga zbierać informacje o środowisku lokalnym VMware.

- Uruchom Planer wdrażania w okresie, który reprezentuje typowe zmiany dla maszyn wirtualnych. Spowoduje to wygenerowanie dokładniejszych szacunków i zaleceń.
- Zaleca się uruchomienie planisty wdrażania na komputerze serwera konfiguracji, ponieważ planista oblicza przepływność z serwera, na którym jest uruchomiony. [Dowiedz się więcej](site-recovery-vmware-deployment-planner-run.md#get-throughput) o pomiarze przepustowości.
- Jeśli nie masz jeszcze skonfigurowanego serwera konfiguracji:
    - [Zapoznaj się](vmware-physical-azure-config-process-server-overview.md) ze składnikami odzyskiwania witryny.
    - [Skonfiguruj serwer konfiguracji,](vmware-azure-deploy-configuration-server.md)aby uruchomić na nim Planek wdrażania.

Następnie uruchom planistę w następujący sposób:

1. [Dowiedz się więcej o](site-recovery-deployment-planner.md) Planście wdrażania. Najnowszą wersję można pobrać z portalu lub [pobrać bezpośrednio.](https://aka.ms/asr-deployment-planner)
2. Przejrzyj [wymagania wstępne](site-recovery-deployment-planner.md#prerequisites) i [najnowsze aktualizacje](site-recovery-deployment-planner-history.md) dla Planisty wdrażania i [pobierz i wyodrębnij](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) narzędzie.
3. [Uruchom planista wdrażania](site-recovery-vmware-deployment-planner-run.md) na serwerze konfiguracji.
4. [Generowanie raportu w](site-recovery-vmware-deployment-planner-run.md#generate-report) celu podsumowania szacunków i zaleceń.
5. Analizuj [zalecenia raportu](site-recovery-vmware-deployment-planner-analyze-report.md) i [szacunki kosztów](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Domyślnie narzędzie jest skonfigurowane do profilu i generuje raport dla maksymalnie 1000 maszyn wirtualnych. Ten limit można zmienić, zwiększając wartość klucza MaxVMsSupported w pliku ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Wymagania i pojemność obiektu docelowego planu (Azure)

Korzystając ze zgromadzonych szacunków i zaleceń, można zaplanować zasoby docelowe i zdolności produkcyjne. Jeśli uruchomiono Planer wdrażania dla maszyn wirtualnych VMware, możesz użyć wielu [zaleceń raportu,](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) aby ci pomóc.

- **Kompatybilne maszyny wirtualne:** Użyj tego numeru, aby zidentyfikować liczbę maszyn wirtualnych, które są gotowe do odzyskiwania po awarii na platformie Azure. Zalecenia dotyczące przepustowości sieci i rdzeni platformy Azure są oparte na tym numerze.
- **Wymagana przepustowość sieci:** Zwróć uwagę na przepustowość potrzebną do replikacji różnicowej zgodnych maszyn wirtualnych. 
    - Po uruchomieniu planisty określasz żądany rpo w ciągu kilku minut. Zalecenia pokazują przepustowość potrzebną do spełnienia tego RPO 100% i 90% czasu. 
    - Zalecenia dotyczące przepustowości sieci uwzględniają przepustowość potrzebną do całkowitej liczby serwerów konfiguracyjnych i serwerów przetwarzania zalecanych w aplikacji Planner.
- **Wymagane rdzenie platformy Azure:** Należy zwrócić uwagę na liczbę rdzeni, których potrzebujesz w docelowym regionie platformy Azure, na podstawie liczby zgodnych maszyn wirtualnych. Jeśli nie masz wystarczającej liczby rdzeni, w usłudze failover site recovery nie będzie można utworzyć wymaganych maszyn wirtualnych platformy Azure.
- **Zalecany rozmiar partii maszyny Wirtualnej:** Zalecany rozmiar partii jest oparty na możliwości zakończenia replikacji początkowej dla partii domyślnie w ciągu 72 godzin, podczas gdy spełnianie celu celowego 100%. Wartość godziny można zmodyfikować.

Za pomocą tych zaleceń można zaplanować zasoby platformy Azure, przepustowość sieci i przetwarzanie wsadowe maszyn wirtualnych.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planowanie subskrypcji i przydziałów platformy Azure

Chcemy upewnić się, że dostępne przydziały w subskrypcji docelowej są wystarczające do obsługi pracy awaryjnej.

**Zadanie** | **Szczegóły** | **Akcja**
--- | --- | ---
**Sprawdź rdzenie** | Jeśli rdzenie w dostępnym przydziałze nie są równe lub przekraczają całkowitą liczbę docelową w czasie pracy awaryjnej, przewijanie awaryjne zakończy się niepowodzeniem. | W przypadku maszyn wirtualnych VMware sprawdź, czy masz wystarczającą liczbę rdzeni w subskrypcji docelowej, aby spełnić podstawowe zalecenie programu Deployment Planner.<br/><br/> W przypadku serwerów fizycznych sprawdź, czy rdzenie platformy Azure spełniają twoje ręczne szacunki.<br/><br/> Aby sprawdzić przydziały, w witrynie Azure portal > **Subscription**kliknij pozycję **Użycie + przydziały**.<br/><br/> [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) o zwiększaniu przydziałów.
**Sprawdzanie limitów pracy awaryjnej** | Liczba failover nie może przekraczać limitów pracy awaryjnej odzyskiwania lokacji. |  Jeśli przekroczenie trybu failover przekracza limity, można dodać subskrypcje i przejść w stan failover do wielu subskrypcji lub zwiększyć przydział dla subskrypcji. 


### <a name="failover-limits"></a>Limity trybu failover

Limity wskazują liczbę pracy awaryjnej, które są obsługiwane przez odzyskiwanie lokacji w ciągu jednej godziny, przy założeniu, że trzy dyski na komputer.

Co oznacza przestrzeganie? Aby uruchomić maszynę wirtualną platformy Azure, platforma Azure wymaga, aby niektóre sterowniki były w stanie rozruchu, a usługi takie jak DHCP mają być uruchamiane automatycznie.
- Maszyny, które spełniają wymagania, będą już miały te ustawienia.
- W przypadku komputerów z systemem Windows można proaktywnie sprawdzać zgodność i w razie potrzeby zapewniać ich zgodność. [Dowiedz się więcej](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Maszyny z systemem Linux są wprowadzane do zgodności tylko w czasie pracy awaryjnej.

**Maszyna jest zgodna z platformą Azure?** | **Limity maszyn wirtualnych platformy Azure (przebłaj dysków zarządzanych)**
--- | --- 
Tak | 2000
Nie | 1000

- Limity zakładają, że minimalne inne zadania są w toku w regionie docelowym dla subskrypcji.
- Niektóre regiony platformy Azure są mniejsze i mogą mieć nieco niższe limity.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planowanie infrastruktury i łączności maszyn wirtualnych

Po przejściu w stan failover do platformy Azure trzeba obciążeń do pracy w środowisku lokalnym i umożliwić użytkownikom dostęp do obciążeń uruchomionych na maszynach wirtualnych platformy Azure.

- [Dowiedz się więcej](site-recovery-active-directory.md#test-failover-considerations) o awarii infrastruktury lokalnej usługi Active Directory lub DNS na platformie Azure.
- [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) o przygotowywaniu do nawiązania połączenia z maszynami wirtualnymi platformy Azure po przełączeniu w stan failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Planowanie zdolności wytwórczych i wymagań

Ważne jest, aby mieć wystarczające serwery konfiguracji i serwery procesów skalowanych w poziomie, aby spełnić wymagania dotyczące pojemności. Po rozpoczęciu wdrażania na dużą skalę zacznij od jednego serwera konfiguracji i pojedynczego serwera procesów skalowanych w poziomie. Po osiągnięciu zalecanych limitów dodaj dodatkowe serwery.

>[!NOTE]
> W przypadku maszyn wirtualnych VMware planista wdrażania zawiera pewne zalecenia dotyczące potrzebnych serwerów konfiguracji i przetwarzania. Zaleca się używanie tabel zawartych w poniższych procedurach, zamiast korzystać z zalecenia Planisty wdrażania. 


## <a name="set-up-a-configuration-server"></a>Konfigurowanie serwera konfiguracji
 
Na pojemność serwera konfiguracji ma wpływ liczba replikacji maszyn, a nie szybkość zmiany danych. Aby dowiedzieć się, czy potrzebujesz dodatkowych serwerów konfiguracji, użyj tych zdefiniowanych limitów maszyn wirtualnych.

**Procesora** | **Pamięci** | **Dysk pamięci podręcznej** | **Limit maszyny replikowanej**
 --- | --- | --- | ---
8 procesorów wirtualnych<br> 2 gniazda * 4 rdzenie @ 2,5 GDN | 16 GB | 600 GB | Do 550 maszyn<br> Zakłada, że każdy komputer ma trzy dyski po 100 GB każdy.

- Limity te są oparte na serwerze konfiguracji skonfigurowanym przy użyciu szablonu OVF.
- Limity zakładają, że nie używasz serwera przetwarzania, który jest uruchomiony domyślnie na serwerze konfiguracji.

Jeśli chcesz dodać nowy serwer konfiguracji, postępuj zgodnie z poniższymi instrukcjami:

- [Skonfiguruj serwer konfiguracji](vmware-azure-deploy-configuration-server.md) odzyskiwania po awarii VMware VM, używając szablonu OVF.
- [Skonfiguruj serwer konfiguracji](physical-azure-set-up-source.md) ręcznie dla serwerów fizycznych lub wdrożeń VMware, które nie mogą używać szablonu OVF.

Podczas konfigurowania serwera konfiguracji należy pamiętać, że:

- Podczas konfigurowania serwera konfiguracji, należy wziąć pod uwagę subskrypcji i magazynu, w którym znajduje się, ponieważ nie powinny być zmieniane po instalacji. Jeśli trzeba zmienić przechowalnię, należy odłączyć serwer konfiguracji od repozytorium i ponownie go zarejestrować. Zatrzymuje replikację maszyn wirtualnych w przechowalni.
- Jeśli chcesz skonfigurować serwer konfiguracji z wieloma kartami sieciowymi, należy to zrobić podczas konfigurowania. Nie można tego zrobić po zarejestrowaniu serwera konfiguracji w przechowalni.

## <a name="set-up-a-process-server"></a>Konfigurowanie serwera przetwarzania

Na pojemność serwera przetwarzania mają wpływ współczynniki zmian danych, a nie liczba maszyn włączonych do replikacji.

- W przypadku dużych wdrożeń zawsze powinien mieć co najmniej jeden serwer procesów skalowanych w poziomie.
- Aby dowiedzieć się, czy potrzebujesz dodatkowych serwerów, skorzystaj z poniższej tabeli.
- Zaleca się dodanie serwera o najwyższej specyfikacji. 


**Procesora** | **Pamięci** | **Dysk pamięci podręcznej** | **Współczynnik zmian**
 --- | --- | --- | --- 
12 procesorów wirtualnych<br> 2 gniazda* 6 rdzeni @ 2,5 GDN | 24 GB | 1 GB | Do 2 TB dziennie

Skonfiguruj serwer przetwarzania w następujący sposób:

1. Przejrzyj [wymagania wstępne](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Zainstaluj serwer w [portalu](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)lub z [wiersza polecenia](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Skonfiguruj zreplikowane maszyny do korzystania z nowego serwera. Jeśli masz już maszyny replikujące:
    - Całe obciążenie serwera przetwarzania można [przenieść](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) na nowy serwer przetwarzania.
    - Alternatywnie można [przenieść](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) określonych maszyn wirtualnych do nowego serwera przetwarzania.



## <a name="enable-large-scale-replication"></a>Włączanie replikacji na dużą skalę

Po zaplanowaniu zdolności produkcyjnych i wdrożeniu wymaganych składników i infrastruktury włącz replikację dla dużej liczby maszyn wirtualnych.

1. Sortuj maszyny w partiach. Włącz replikację maszyn wirtualnych w partii, a następnie przejść do następnej partii.

    - W przypadku maszyn wirtualnych VMware można użyć [zalecanego rozmiaru partii maszyny Wirtualnej](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) w raporcie Planer wdrażania.
    - W przypadku komputerów fizycznych zaleca się identyfikowanie partii na podstawie maszyn o podobnym rozmiarze i ilości danych oraz na dostępnej przepustowości sieci. Celem jest wsadowe maszyny, które mogą zakończyć swoją początkową replikację w mniej więcej tym samym czasie.
    
2. Jeśli zmiany dysku dla komputera jest wysoki lub przekracza limity w wdrożenie thePlanner, można przenieść pliki niekrytyczne, które nie trzeba replikować (takie jak zrzuty dziennika lub pliki tymczasowe) z komputera. W przypadku maszyn wirtualnych VMware można przenieść te pliki na oddzielny dysk, a następnie [wykluczyć ten dysk](vmware-azure-exclude-disk.md) z replikacji.
3. Przed włączeniem replikacji sprawdź, czy maszyny spełniają [wymagania dotyczące replikacji](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfigurowanie zasad replikacji maszyn [wirtualnych VMware](vmware-azure-set-up-replication.md#create-a-policy) lub [serwerów fizycznych](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Włącz replikację maszyn [wirtualnych VMware](vmware-azure-enable-replication.md) lub [serwerów fizycznych](physical-azure-disaster-recovery.md#enable-replication). Spowoduje to rozpoczęcie replikacji początkowej dla wybranych maszyn.

## <a name="monitor-your-deployment"></a>Monitorowanie wdrożenia

Po rozpoczęciu replikacji dla pierwszej partii maszyn wirtualnych należy rozpocząć monitorowanie wdrożenia w następujący sposób:  

1. Przypisz administratora odzyskiwania po awarii do monitorowania stanu kondycji zreplikowanych komputerów.
2. [Monitoruj zdarzenia](site-recovery-monitor-and-troubleshoot.md) dla zreplikowanych elementów i infrastruktury.
3. [Monitoruj kondycję](vmware-physical-azure-monitor-process-server.md) serwerów procesów skalowania w poziomie.
4. Zarejestruj się, aby otrzymywać [powiadomienia e-mail](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) o wydarzeniach, aby ułatwić monitorowanie.
5. Przeprowadzaj regularne [ćwiczenia odzyskiwania po awarii,](site-recovery-test-failover-to-azure.md)aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.


## <a name="plan-for-large-scale-failovers"></a>Planowanie pracy awaryjnej na dużą skalę

W przypadku awarii może być konieczne przebłajenie dużej liczby maszyn/obciążeń na platformie Azure. Przygotuj się do tego typu zdarzenia w następujący sposób.

Możesz przygotować się z wyprzedzeniem do pracy awaryjnej w następujący sposób:

- [Przygotuj infrastrukturę i maszyny wirtualne,](#plan-infrastructure-and-vm-connectivity) aby twoje obciążenia były dostępne po przemijania awaryjnego i aby użytkownicy mogli uzyskiwać dostęp do maszyn wirtualnych platformy Azure.
- Należy zauważyć [limity pracy awaryjnej](#failover-limits) wcześniej w tym dokumencie. Upewnij się, że przewijasz awaryjne mieszczą się w tych granicach.
- Uruchom regularne [ćwiczenia odzyskiwania po awarii](site-recovery-test-failover-to-azure.md). Ćwiczenia pomagają:
    - Znajdź luki we wdrożeniu przed przełączeniem awaryjnym.
    - Oszacuj kompleksowe rto dla aplikacji.
    - Oszacuj kompleksowy cel ochrony konta końcowego dla obciążeń.
    - Identyfikowanie konfliktów zakresu adresów IP.
    - Podczas wykonywania ćwiczeń zaleca się, aby nie używać sieci produkcyjnych dla ćwiczeń, unikać używania tych samych nazw podsieci w sieciach produkcyjnych i testowych oraz czyścić testowe prace awaryjne po każdym ćwiczeniu.

Aby uruchomić tryb failover na dużą skalę, zaleca się następujące czynności:

1. Tworzenie planów odzyskiwania dla pracy awaryjnej obciążenia.
    - Każdy plan odzyskiwania może wywołać przewijalnie w stan failover maksymalnie 100 maszyn.
    - [Dowiedz się więcej](recovery-plan-overview.md) o planach odzyskiwania.
2. Dodaj skrypty runbook usługi Azure Automation do planów odzyskiwania, aby zautomatyzować wszelkie zadania ręczne na platformie Azure. Typowe zadania obejmują konfigurowanie modułów równoważenia obciążenia, aktualizowanie DNS itp. [Dowiedz się więcej](site-recovery-runbook-automation.md)
2. Przed przełączeniem awaryjnym przygotuj maszyny z systemem Windows, aby były zgodne ze środowiskiem platformy Azure. [Limity pracy awaryjnej](#plan-azure-subscriptions-and-quotas) są wyższe dla maszyn, które są zgodne. [Dowiedz się więcej](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) o uruchomieniu kamieczkach.
4.  Wyzwalanie pracy awaryjnej z polecenia cmdlet [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell wraz z planem odzyskiwania.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Monitorowanie usługi Site Recovery](site-recovery-monitor-and-troubleshoot.md)
