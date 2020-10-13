---
title: Migrowanie maszyn wirtualnych z systemem Windows do usługi Azure Premium Storage przy użyciu Azure Site Recovery
description: Dowiedz się, jak przeprowadzić migrację dysków maszyny wirtualnej z konta magazynu w warstwie Standardowa do konta magazynu w warstwie Premium przy użyciu Azure Site Recovery.
author: luywang
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 58d4459e1869a9d1f7ccb8234c0356ac486a950c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975556"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrowanie do Premium Storage przy użyciu Azure Site Recovery

[Usługa Azure Premium dysków SSD](../disks-types.md) zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych, na których działają duże obciążenia we/wy. Ten przewodnik ułatwia Migrowanie dysków maszyn wirtualnych z konta magazynu w warstwie Standardowa do konta magazynu w warstwie Premium przy użyciu [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery to usługa platformy Azure, która przyczynia się do strategii zapewniania ciągłości działalności biznesowej i odzyskiwania po awarii przez organizowanie replikacji lokalnych serwerów fizycznych i maszyn wirtualnych do chmury (Azure) lub dodatkowego centrum danych. Gdy wystąpi awaria w lokalizacji głównej, nastąpi przełączenie w tryb failover do lokalizacji dodatkowej, aby zachować dostęp do aplikacji i obciążeń. Powrót po awarii do lokalizacji podstawowej powraca do normalnego działania. 

Site Recovery zapewnia test pracy w trybie failover w celu obsługi testów odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Można uruchomić tryb failover z minimalną utratą danych (zależnie od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. W scenariuszu migrowania do Premium Storage można użyć [trybu failover w Site Recovery](../../site-recovery/site-recovery-failover.md) do migrowania dysków docelowych do konta magazynu w warstwie Premium.

Zalecamy przeprowadzenie migracji do Premium Storage przy użyciu Site Recovery, ponieważ ta opcja zapewnia minimalny czas przestoju. Ta opcja zapobiega również ręcznym wykonywaniu kopiowania dysków i tworzeniu nowych maszyn wirtualnych. Site Recovery będzie systematycznie kopiować dyski i tworzyć nowe maszyny wirtualne podczas pracy w trybie failover. 

Site Recovery obsługuje wiele typów trybu failover z minimalnym czasem przestoju lub bez niego. Aby zaplanować przestoje i oszacować utratę danych, zobacz [typy trybu failover w Site Recovery](../../site-recovery/site-recovery-failover.md). Jeśli [przygotowuje się do łączenia się z maszynami wirtualnymi platformy Azure po](../../site-recovery/vmware-azure-tutorial.md)przejściu w tryb failover, po przejściu do trybu failover powinno być możliwe nawiązanie połączenia z maszyną wirtualną Azure

![Diagram odzyskiwania po awarii][1]

## <a name="azure-site-recovery-components"></a>Składniki Azure Site Recovery

Poniższe składniki Site Recovery są istotne dla tego scenariusza migracji:

* **Serwer konfiguracji** to maszyna wirtualna platformy Azure, która koordynuje komunikację i zarządza procesami replikacji i odzyskiwania danych. Na tej maszynie wirtualnej uruchamiasz jeden plik Instalatora, aby zainstalować serwer konfiguracji i dodatkowy składnik nazywany serwerem przetwarzania jako bramę replikacji. Przeczytaj o [wymaganiach wstępnych dotyczących serwera konfiguracji](../../site-recovery/vmware-azure-tutorial.md). Serwer konfiguracji można skonfigurować tylko raz i można go użyć do wszystkich migracji do tego samego regionu.

* **Serwer przetwarzania** to brama replikacji, która: 

  1. Odbiera dane replikacji z źródłowych maszyn wirtualnych.
  2. Optymalizuje dane z pamięci podręcznej, kompresji i szyfrowania.
  3. Wysyła dane do konta magazynu. 

  Obsługuje ona również instalację wypychaną usługi mobilności na źródłowych maszynach wirtualnych i przeprowadza automatyczne odnajdowanie źródłowych maszyn wirtualnych. Domyślny serwer przetwarzania jest zainstalowany na serwerze konfiguracji. Aby skalować wdrożenie, można wdrożyć dodatkowe autonomiczne serwery procesów. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wdrażania serwera przetwarzania](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) i [wdrażania dodatkowych serwerów przetwarzania](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Serwer przetwarzania można skonfigurować tylko raz i można go użyć do wszystkich migracji do tego samego regionu.

* **Usługa mobilności** to składnik wdrożony na każdej standardowej maszynie wirtualnej, która ma być replikowana. Przechwytuje dane zapisu na standardowej maszynie wirtualnej i przekazuje je do serwera przetwarzania. Przeczytaj o [wymaganiach wstępnych dotyczących replikowanych maszyn](../../site-recovery/vmware-azure-tutorial.md).

Ta ilustracja pokazuje, jak te składniki współdziałają:

![Interakcja składników Site Recovery][15]

> [!NOTE]
> Site Recovery nie obsługuje migracji dysków funkcji miejsca do magazynowania.

Aby uzyskać dodatkowe składniki dla innych scenariuszy, zobacz [Architektura scenariusza](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Podstawowe informacje dotyczące platformy Azure

Oto wymagania dotyczące platformy Azure dotyczące tego scenariusza migracji:

* Subskrypcja platformy Azure.
* Konto usługi Azure Premium Storage do przechowywania replikowanych danych.
* Sieć wirtualna platformy Azure, z którą maszyny wirtualne będą łączyć się podczas tworzenia w trybie failover. Sieć wirtualna platformy Azure musi znajdować się w tym samym regionie, w którym działa Site Recovery.
* Konto magazynu w warstwie Standardowa na potrzeby przechowywania dzienników replikacji. Może to być to samo konto magazynu dla migrowanych dysków maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne

* Zapoznaj się z odpowiednimi składnikami scenariusza migracji w poprzedniej sekcji.
* Zaplanuj przestój, przepoznając [się z trybem failover w Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Kroki instalacji i migracji

Za pomocą Site Recovery można migrować maszyny wirtualne IaaS platformy Azure między regionami lub w tym samym regionie. Poniższe instrukcje są dostosowane do tego scenariusza migracji z artykułu [replikowanie maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure](../../site-recovery/vmware-azure-tutorial.md). Aby zapoznać się ze szczegółowymi instrukcjami zawartymi w tym artykule, Skorzystaj z linków.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1. Tworzenie magazynu Recovery Services

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób**  >  **Management**  >  **Backup and Site Recovery (OMS)** zarządzania. Alternatywnie możesz wybrać pozycję **Przeglądaj**  >  **Recovery Services magazyn**  >  **Dodaj**.
   >[!NOTE]
   >Kopia zapasowa i Site Recovery była wcześniej częścią [pakietu OMS](../../azure-monitor/terminology.md#april-2018---retirement-of-operations-management-suite-brand).
1. Określ region, do którego zostaną zreplikowane maszyny wirtualne. Na potrzeby migracji w tym samym regionie wybierz region, w którym źródłowe maszyny wirtualne i źródłowe konta magazynu są. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2. Wybieranie celów ochrony 

1. Na maszynie wirtualnej, na której chcesz zainstalować serwer konfiguracji, Otwórz [Azure Portal](https://portal.azure.com).
2. Przejdź do obszaru **Recovery Services ustawienia magazynów**  >  **Settings**  >  **Site Recovery**  >  **krok 1: przygotowanie**  >  **celu ochrony**infrastruktury.

   ![Przeglądanie okienka cel ochrony][2]

3. W obszarze **cel ochrony**z pierwszej listy rozwijanej wybierz pozycję **na platformie Azure**. Z drugiej listy rozwijanej wybierz pozycję **niezwirtualizowane/inne**, a następnie wybierz przycisk **OK**.

   ![Okienko cel ochrony z wypełnionymi polami][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3. Konfigurowanie środowiska źródłowego (serwer konfiguracji)

1. Pobierz **Azure Site Recovery ujednoliconą konfigurację** i klucz rejestracji magazynu, przechodząc do okienka **Przygotowanie infrastruktury**  >  **Przygotuj Źródło**  >  **Dodaj serwer** . 
 
   Do uruchomienia ujednoliconej konfiguracji potrzebny będzie klucz rejestracji magazynu. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Przeglądanie w okienku Dodawanie serwera][4]

2. W okienku **Dodawanie serwera** Dodaj serwer konfiguracji.

   ![Wybrano okienko Dodaj serwer z wybranym serwerem konfiguracji][5]

3. Na maszynie wirtualnej, która jest używana jako serwer konfiguracji, uruchom ujednoliconą konfigurację, aby zainstalować serwer konfiguracji i serwer przetwarzania. Aby ukończyć instalację, można [przewinąć zrzuty ekranu](../../site-recovery/vmware-azure-tutorial.md) . Aby zapoznać się z krokami opisanymi w tym scenariuszu migracji, można odwołać się do poniższych zrzutów ekranu.

   1. W obszarze **przed rozpoczęciem**wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.

      ![Przed rozpoczęciem][6]

   2. W obszarze **rejestracja**Przeglądaj i wybierz klucz rejestracji pobrany z magazynu.

      ![Strona rejestracji][7]

   3. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. W tym scenariuszu migracji wybierz pozycję **nie**.

      ![Strona szczegółów środowiska][8]

4. Po zakończeniu instalacji wykonaj następujące czynności w oknie **Microsoft Azure Site Recovery Configuration Server** :
 
   1. Karta **Zarządzanie kontami** służy do tworzenia konta, za pomocą którego Site Recovery mogą być odnajdywane automatycznie. (W scenariuszu dotyczącym ochrony komputerów fizycznych skonfigurowanie konta nie ma znaczenia, ale musisz mieć co najmniej jedno konto, aby włączyć jeden z poniższych kroków. W takim przypadku można nazwać konto i hasło jako dowolne. 
   2. Za pomocą karty **rejestracja w magazynie** Przekaż plik poświadczeń magazynu.

      ![Karta Rejestracja w magazynie][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4. Konfigurowanie środowiska docelowego

Wybierz pozycję **Przygotuj**  >  **miejsce docelowe**infrastruktury i określ model wdrażania, który ma być używany dla maszyn wirtualnych po przejściu w tryb failover. W zależności od danego scenariusza możesz wybrać opcję **klasyczne** lub **Menedżer zasobów**.

![Okienko docelowe][10]

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. 

> [!NOTE]
> Jeśli używasz konta usługi Premium Storage dla replikowanych danych, musisz skonfigurować dodatkowe konto magazynu w warstwie Standardowa do przechowywania dzienników replikacji.

### <a name="step-5-set-up-replication-settings"></a>Krok 5. Konfigurowanie ustawień replikacji

Aby sprawdzić, czy serwer konfiguracji został pomyślnie skojarzony z zasadami replikacji, które tworzysz, postępuj zgodnie z [ustawieniami konfiguracji replikacji](../../site-recovery/vmware-azure-tutorial.md).

### <a name="step-6-plan-capacity"></a>Krok 6. Planowanie pojemności

1. Użyj [planisty wydajności](../../site-recovery/site-recovery-capacity-planner.md) , aby dokładnie oszacować przepustowość sieci, magazyn i inne wymagania w celu spełnienia wymagań dotyczących replikacji. 
2. Gdy wszystko będzie gotowe, wybierz pozycję **tak. czy zostało to zrobione** w ramach **planowania pojemności?**.

   ![Pole umożliwiające potwierdzenie ukończenia planowania pojemności][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7. Instalowanie usługi mobilności i włączanie replikacji

1. Możesz wybrać [wypychanie instalacji](../../site-recovery/vmware-azure-tutorial.md) do źródłowych maszyn wirtualnych lub [ręcznie zainstalować usługę mobilności](../../site-recovery/vmware-azure-install-mobility-service.md) na źródłowych maszynach wirtualnych. Wymaganie wypychania instalacji i ścieżki Instalatora ręcznego można znaleźć w podanym łączu. W przypadku przeprowadzania ręcznej instalacji może być konieczne użycie wewnętrznego adresu IP w celu znalezienia serwera konfiguracji.

   ![Strona szczegółów serwera konfiguracji][12]

   Maszyna wirtualna w trybie failover będzie miała dwa dyski tymczasowe: jedną z podstawowej maszyny wirtualnej i drugą utworzoną podczas aprowizacji maszyny wirtualnej w regionie odzyskiwania. Aby wykluczyć dysk tymczasowy przed replikacją, należy zainstalować usługę mobilności przed włączeniem replikacji. Aby dowiedzieć się więcej na temat wykluczania dysku tymczasowego, zobacz temat [wykluczanie dysków z replikacji](../../site-recovery/vmware-azure-tutorial.md).

2. Aby włączyć replikację:
   1. Wybierz pozycję **Replikuj**  >  **Źródło**aplikacji. Po włączeniu replikacji po raz pierwszy wybierz pozycję **+ Replikuj** w magazynie, aby włączyć replikację dla dodatkowych maszyn.
   2. W kroku 1 Skonfiguruj **Źródło** jako serwer przetwarzania.
   3. W kroku 2 Określ model wdrożenia po zakończeniu pracy w trybie failover, konto magazynu w warstwie Premium, do którego chcesz przeprowadzić migrację, konto magazynu w warstwie Standardowa w celu zapisania dzienników i niepowodzenie sieci wirtualnej.
   4. W kroku 3 Dodaj chronione maszyny wirtualne według adresu IP. (Może być potrzebny wewnętrzny adres IP, aby je znaleźć).
   5. W kroku 4 skonfiguruj właściwości, wybierając konta, które zostały wcześniej skonfigurowane na serwerze przetwarzania.
   6. W kroku 5 wybierz zasady replikacji utworzone wcześniej w sekcji "krok 5. Konfigurowanie ustawień replikacji".
   7. Wybierz przycisk **OK**.

   > [!NOTE]
   > Po cofnięciu przydziału i ponownym uruchomieniu maszyny wirtualnej platformy Azure nie ma gwarancji, że będzie ona mieć ten sam adres IP. Jeśli adres IP serwera konfiguracji/serwera przetwarzania lub chronionych maszyn wirtualnych platformy Azure ulegnie zmianie, replikacja w tym scenariuszu może nie funkcjonować prawidłowo.

   ![Włącz okienko replikacji z wybranym źródłem][13]

Podczas projektowania środowiska usługi Azure Storage zalecamy używanie osobnych kont magazynu dla każdej maszyny wirtualnej w zestawie dostępności. Zalecamy przestrzeganie najlepszych rozwiązań z warstwy magazynowania w celu [użycia wielu kont magazynu dla każdego zestawu dostępności](../manage-availability.md). Dystrybuowanie dysków maszyn wirtualnych do wielu kont magazynu pomaga w zwiększeniu dostępności magazynu i rozproszeniu we/wy między infrastrukturą usługi Azure Storage.

Jeśli maszyny wirtualne znajdują się w zestawie dostępności, a nie replikowania dysków wszystkich maszyn wirtualnych na jedno konto magazynu, zdecydowanie zalecamy Migrowanie wielu maszyn wirtualnych wiele razy. Dzięki temu maszyny wirtualne w tym samym zestawie dostępności nie współużytkują jednego konta magazynu. Za pomocą okienka **Włączanie replikacji** można skonfigurować docelowe konto magazynu dla każdej maszyny wirtualnej, po jednej naraz.
 
Możesz wybrać model wdrożenia po zakończeniu pracy w trybie failover zgodnie z potrzebami. W przypadku wybrania Azure Resource Manager jako modelu wdrożenia po zakończeniu pracy w trybie failover można przełączyć maszynę wirtualną (Menedżer zasobów) do maszyny wirtualnej (Menedżer zasobów) lub przełączyć ją w tryb failover na maszynę wirtualną (Menedżer zasobów).

### <a name="step-8-run-a-test-failover"></a>Krok 8. Uruchamianie testu pracy w trybie failover

Aby sprawdzić, czy replikacja została ukończona, wybierz wystąpienie Site Recovery a następnie wybierz pozycję **Ustawienia**  >  **zreplikowane elementy**. Zobaczysz stan i procent procesu replikacji. 

Po zakończeniu replikacji początkowej Uruchom test pracy w trybie failover, aby zweryfikować strategię replikacji. Aby zapoznać się ze szczegółowymi krokami testowego przełączania do trybu failover, zobacz [Uruchamianie testowej pracy w trybie failover w Site Recovery](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> Przed uruchomieniem dowolnego trybu failover upewnij się, że maszyny wirtualne i strategia replikacji spełniają wymagania. Aby uzyskać więcej informacji o uruchamianiu testowej pracy w trybie failover, zobacz [test pracy w trybie failover na platformie Azure w Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Stan testu pracy w trybie failover można sprawdzić w obszarze **Ustawienia**  >  **zadania**  >  *YOUR_FAILOVER_PLAN_NAME*. W okienku można zobaczyć podział kroków i wyników powodzeń/niepowodzeń. Jeśli test pracy w trybie failover nie powiedzie się w dowolnym kroku, wybierz krok, aby sprawdzić komunikat o błędzie. 

### <a name="step-9-run-a-failover"></a>Krok 9. Uruchamianie trybu failover

Po zakończeniu testowej pracy w trybie failover uruchom tryb failover, aby zmigrować dyski do Premium Storage i zreplikować wystąpienia maszyn wirtualnych. Postępuj zgodnie ze szczegółowymi instrukcjami w temacie [Uruchamianie trybu failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Upewnij się, że wybrano opcję **Zamknij maszyny wirtualne i zsynchronizuj najnowsze dane**. Ta opcja określa, że Site Recovery powinna próbować zamknąć chronione maszyny wirtualne i zsynchronizować dane, aby Najnowsza wersja danych została przełączona w tryb failover. Jeśli ta opcja nie zostanie wybrana lub próba nie powiedzie się, przełączenie w tryb failover będzie możliwe z najnowszego dostępnego punktu odzyskiwania dla maszyny wirtualnej. 

Site Recovery utworzy wystąpienie maszyny wirtualnej, którego typ jest taki sam jak lub podobny do maszyny wirtualnej obsługującej Premium Storage. Możesz sprawdzić wydajność i cenę różnych wystąpień maszyn wirtualnych, przechodząc do [Windows Virtual Machines cenach](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub [Linux Virtual Machines cenniku](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Kroki do wykonania po migracji

1. **Skonfiguruj zreplikowane maszyny wirtualne do zestawu dostępności, jeśli ma to zastosowanie**. Site Recovery nie obsługuje migrowania maszyn wirtualnych wraz z zestawem dostępności. W zależności od wdrożenia zreplikowanej maszyny wirtualnej wykonaj jedną z następujących czynności:
   * W przypadku maszyny wirtualnej utworzonej za pomocą klasycznego modelu wdrażania: Dodaj maszynę wirtualną do zestawu dostępności w Azure Portal. Aby uzyskać szczegółowe instrukcje, przejdź do sekcji [Dodawanie istniejącej maszyny wirtualnej do zestawu dostępności](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * W przypadku maszyny wirtualnej utworzonej za pomocą Menedżer zasobów model wdrażania: Zapisz konfigurację maszyny wirtualnej, a następnie usuń i ponownie utwórz maszyny wirtualne w zestawie dostępności. Aby to zrobić, Użyj skryptu w [ustawieniu Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Przed uruchomieniem tego skryptu sprawdź jego ograniczenia i Zaplanuj przestoje.

2. **Usuń stare maszyny wirtualne i dyski**. Upewnij się, że dyski w warstwie Premium są spójne z dyskami źródłowymi i że nowe maszyny wirtualne wykonują tę samą funkcję co źródłowe maszyny wirtualne. Usuń maszynę wirtualną i Usuń dyski z kont magazynu źródłowego w Azure Portal. Jeśli wystąpi problem polegający na tym, że dysk nie został usunięty nawet po usunięciu maszyny wirtualnej, zobacz [Rozwiązywanie problemów z usuwaniem zasobów magazynu](../troubleshooting/storage-resource-deletion-errors.md).

3. **Wyczyść infrastrukturę Azure Site Recovery**. Jeśli Site Recovery nie jest już potrzebne, można oczyścić jej infrastrukturę. Usuń zreplikowane elementy, serwer konfiguracji i zasady odzyskiwania, a następnie usuń Magazyn Azure Site Recovery.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Monitorowanie i rozwiązywanie problemów z ochroną maszyn wirtualnych i serwerów fizycznych](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [&Strona pytania dla Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z określonymi scenariuszami migrowania maszyn wirtualnych, zobacz następujące zasoby:

* [Migrowanie Virtual Machines platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Utwórz dysk VHD systemu Windows Server i przekaż go do platformy Azure](upload-generalized-managed.md)
* [Migrowanie Virtual Machines z usługi Amazon AWS do Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Zobacz też następujące zasoby, aby dowiedzieć się więcej na temat usługi Azure Storage i platformy Azure Virtual Machines:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png