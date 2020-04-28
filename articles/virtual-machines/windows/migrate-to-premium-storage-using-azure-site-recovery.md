---
title: Migrowanie maszyn wirtualnych z systemem Windows do usługi Azure Premium Storage za pomocą usługi Azure Site Recovery
description: Migruj istniejące maszyny wirtualne do usługi Azure Premium Storage przy użyciu usługi Site Recovery. Magazyn w wersji Premium oferuje wysokowydajną obsługę dysku o niskim opóźnieniu dla obciążeń intensywnie korzystających z we/wy uruchomionych na maszynach wirtualnych platformy Azure.
author: luywang
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 071596557de00c9dfb2afaa5751d9331c21ada99
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866272"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrowanie do magazynu w wersji Premium przy użyciu usługi Azure Site Recovery

[Dyski SSD w wersji Premium platformy Azure](disks-types.md) zapewniają wysoką wydajność obsługi dysków o niskim opóźnieniu dla maszyn wirtualnych (VM), które są uruchomione obciążenia intensywnie korzystające z we/wy. Ten przewodnik ułatwia migrację dysków maszyn wirtualnych ze standardowego konta magazynu na konto magazynu w wersji Premium przy użyciu [usługi Azure Site Recovery.](../../site-recovery/site-recovery-overview.md)

Usługa Site Recovery to usługa platformy Azure, która przyczynia się do strategii ciągłości działania i odzyskiwania po awarii, organizując replikację lokalnych serwerów fizycznych i maszyn wirtualnych w chmurze (Azure) lub w pomocniczym centrum danych. Gdy wystąpią awarie w lokalizacji głównej, można przejść w stan fail over do lokalizacji dodatkowej, aby zachować aplikacje i obciążenia dostępne. Powrót po awarii do lokalizacji podstawowej po powrocie do normalnego działania. 

Odzyskiwanie lokacji zapewnia testowanie pracy awaryjnej do obsługi ćwiczeń odzyskiwania po awarii bez wpływu na środowiska produkcyjne. Można uruchomić pracę awaryjną przy minimalnej utracie danych (w zależności od częstotliwości replikacji) w przypadku nieoczekiwanych awarii. W scenariuszu migracji do magazynu w ramach programu Premium można użyć [pracy awaryjnej w układzie Site Recovery](../../site-recovery/site-recovery-failover.md) do migracji dysków docelowych na konto magazynu w wersji premium.

Zaleca się migrację do magazynu w wersji Premium przy użyciu usługi Site Recovery, ponieważ ta opcja zapewnia minimalne przestoje. Ta opcja pozwala również uniknąć ręcznego wykonywania kopiowania dysków i tworzenia nowych maszyn wirtualnych. Funkcja Site Recovery będzie systematycznie kopiować dyski i tworzyć nowe maszyny wirtualne podczas pracy awaryjnej. 

Odzyskiwanie witryny obsługuje wiele typów pracy awaryjnej przy minimalnym lub żadnym przestoju. Aby zaplanować przestoje i oszacować utratę danych, zobacz [typy pracy awaryjnej w układzie Site Recovery](../../site-recovery/site-recovery-failover.md). Jeśli [przygotowujesz się do łączenia się z maszynami wirtualnymi platformy Azure po przełączeniu w stan failover,](../../site-recovery/vmware-walkthrough-overview.md)powinieneś mieć możliwość nawiązania połączenia z maszyną wirtualną platformy Azure przy użyciu protokołu RDP po przełączeniu w stan failover.

![Diagram odzyskiwania po awarii][1]

## <a name="azure-site-recovery-components"></a>Składniki usługi Azure Site Recovery

Te składniki odzyskiwania witryny są istotne dla tego scenariusza migracji:

* **Serwer konfiguracji** to maszyna wirtualna platformy Azure, która koordynuje komunikację i zarządza procesami replikacji i odzyskiwania danych. Na tej maszynie wirtualnej można uruchomić pojedynczy plik instalacyjny w celu zainstalowania serwera konfiguracji i dodatkowy składnik, zwany serwerem przetwarzania, jako bramę replikacji. Przeczytaj o [wymaganiach wstępnych serwera konfiguracji](../../site-recovery/vmware-walkthrough-overview.md). Serwer konfiguracji można skonfigurować tylko raz i można go używać do wszystkich migracji do tego samego regionu.

* **Serwer przetwarzania** jest bramą replikacji, która: 

  1. Odbiera dane replikacji ze źródłowych maszyn wirtualnych.
  2. Optymalizuje dane za pomocą buforowania, kompresji i szyfrowania.
  3. Wysyła dane do konta magazynu. 

  Obsługuje również instalację wypychaną usługi mobilności do źródła maszyn wirtualnych i wykonuje automatyczne odnajdowanie źródłowych maszyn wirtualnych. Domyślny serwer przetwarzania jest zainstalowany na serwerze konfiguracji. Można wdrożyć dodatkowe autonomiczne serwery procesów, aby skalować wdrożenie. Przeczytaj o [najlepszych rozwiązaniach dotyczących wdrażania serwerów procesów](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) i [wdrażania dodatkowych serwerów procesów](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Serwer przetwarzania został skonfigurowany tylko raz i można go używać do wszystkich migracji do tego samego regionu.

* **Usługa mobilności** jest składnikiem, który jest wdrażany na każdej standardowej maszynie wirtualnej, którą chcesz replikować. Przechwytuje zapisy danych na standardowej maszynie wirtualnej i przekazuje je do serwera przetwarzania. Przeczytaj o [wymaganiach wstępnych replikacji maszyny](../../site-recovery/vmware-walkthrough-overview.md).

Ta grafika pokazuje, jak te składniki współdziałają:

![Interakcja składników odzyskiwania witryny][15]

> [!NOTE]
> Funkcja Odzysk lokacji nie obsługuje migracji dysków miejsca do magazynowania.

Aby uzyskać dodatkowe składniki dla innych scenariuszy, zobacz [architektura scenariusza](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Podstawowe informacje o platformie Azure

Oto wymagania platformy Azure dla tego scenariusza migracji:

* Subskrypcja platformy Azure.
* Konto magazynu w wersji Premium platformy Azure do przechowywania replikowanych danych.
* Sieć wirtualna platformy Azure, z którą maszyny wirtualne będą łączyć się podczas ich tworzenia w trybie failover. Sieć wirtualna platformy Azure musi znajdować się w tym samym regionie, w którym jest uruchamiana usługa Site Recovery.
* Konto magazynu standardowego platformy Azure do przechowywania dzienników replikacji. Może to być to samo konto magazynu dla dysków maszyn wirtualnych, które są migrowane.

## <a name="prerequisites"></a>Wymagania wstępne

* Opis odpowiednich składników scenariusza migracji w poprzedniej sekcji.
* Zaplanuj swój przestój, poznając informacje o [pracy awaryjnej w układzie Site Recovery](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Kroki konfiguracji i migracji

Za pomocą usługi Site Recovery można migrować maszyny wirtualne usługi Azure IaaS między regionami lub w tym samym regionie. Poniższe instrukcje są dostosowane do tego scenariusza migracji z artykułu [Replikuj maszyny wirtualne VMware lub serwery fizyczne na platformie Azure.](../../site-recovery/vmware-walkthrough-overview.md) Skorzystaj z linków, aby uzyskać szczegółowe kroki oprócz instrukcji w tym artykule.

### <a name="step-1-create-a-recovery-services-vault"></a>Krok 1: Tworzenie magazynu usług odzyskiwania

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Wybierz **pozycję Utwórz** > kopię zapasową**zarządzania zasobami** > **i odzyskiwanie witryn (OMS)**. Alternatywnie można wybrać **opcję Przeglądaj** > **dodatek do przechowalni** > usług odzyskiwania **.**
   >[!NOTE]
   >Kopia zapasowa i odzyskiwanie witryny było wcześniej częścią [pakietu OMS](/azure/azure-monitor/terminology#april-2018---retirement-of-operations-management-suite-brand).
1. Określ region, do który będą replikowane maszyny wirtualne. Na potrzeby migracji w tym samym regionie wybierz region, w którym znajdują się źródłowe maszyny wirtualne i źródłowe konta magazynu. 

### <a name="step-2-choose-your-protection-goals"></a>Krok 2: Wybierz swoje cele ochrony 

1. Na maszynie Wirtualnej, na której chcesz zainstalować serwer konfiguracji, otwórz [witrynę Azure portal](https://portal.azure.com).
2. Przejdź do sekcji Ustawienia magazynu **usług odzyskiwania** > **Lokacja** > **recovery** > Krok**1: Przygotuj** > **cel ochrony**infrastruktury .

   ![Przeglądanie okienka celu Ochrona][2]

3. W obszarze **Cel Ochrony**na pierwszej liście rozwijanej wybierz pozycję **Na platformie Azure**. Na drugiej liście rozwijanej wybierz pozycję **Nie zwirtualizowany / Inne**, a następnie wybierz przycisk **OK**.

   ![Okienko celu ochrony z wypełnionymi polami][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Krok 3: Konfigurowanie środowiska źródłowego (serwer konfiguracji)

1. Pobierz **ujednoliconą instalację usługi Azure Site Recovery** i klucz rejestracji magazynu, przechodząc do okienek Przygotowywanie źródła**Dodawania serwera** w **infrastrukturze** > **Przygotowywania.** >  
 
   Do uruchomienia ujednoliconej konfiguracji potrzebny jest klucz rejestracji magazynu. Klucz jest ważny przez pięć dni po jego wygenerowaniu.

   ![Przeglądanie okienka Dodawanie serwera][4]

2. W okienku **Dodawanie serwera** dodaj serwer konfiguracji.

   ![Okienko Dodawanie serwera z zaznaczonym serwerem konfiguracji][5]

3. Na maszynie Wirtualnej używanej jako serwer konfiguracji uruchom Instalatora ujednoliconego, aby zainstalować serwer konfiguracji i serwer przetwarzania. Możesz [przejść przez zrzuty ekranu,](../../site-recovery/vmware-walkthrough-overview.md) aby zakończyć instalację. Można zapoznać się z poniższymi zrzutami ekranu, aby uzyskać kroki określone dla tego scenariusza migracji.

   1. W **przed rozpoczęciem**wybierz pozycję Zainstaluj serwer konfiguracji i serwer **przetwarzania**.

      ![Przed rozpoczęciem strony][6]

   2. W **obszarze Rejestracja**przeglądaj i wybierz klucz rejestracji pobrany z przechowalni.

      ![Strona rejestracji][7]

   3. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. W tym scenariuszu migracji wybierz pozycję **Nie**.

      ![Strona Szczegóły środowiska][8]

4. Po zakończeniu instalacji wykonaj następujące czynności w oknie **Serwera konfiguracji odzyskiwania witryny platformy Microsoft Azure:**
 
   1. Karta **Zarządzanie kontami** służy do tworzenia konta, którego program Site Recovery może używać do automatycznego odnajdowania. (W scenariuszu ochrony komputerów fizycznych konfigurowanie konta nie jest istotne, ale potrzebujesz co najmniej jednego konta, aby włączyć jeden z następujących kroków. W takim przypadku można nazwać konto i hasło jako dowolny.) 
   2. Karta **Rejestracja przechowalni** służy do przekazywania pliku poświadczeń przechowalni.

      ![Karta Rejestracja przechowalni][9]

### <a name="step-4-set-up-the-target-environment"></a>Krok 4: Konfigurowanie środowiska docelowego

Wybierz **opcję Przygotuj obiekt docelowy infrastruktury** > **Target**i określ model wdrażania, którego chcesz użyć dla maszyn wirtualnych po przeżycieniu awaryjnym. W zależności od scenariusza można wybrać **klasyczne** lub **Menedżer zasobów.**

![Okienko docelowe][10]

Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure. 

> [!NOTE]
> Jeśli używasz konta magazynu w wersji premium dla replikowanych danych, musisz skonfigurować dodatkowe standardowe konto magazynu do przechowywania dzienników replikacji.

### <a name="step-5-set-up-replication-settings"></a>Krok 5: Konfigurowanie ustawień replikacji

Aby sprawdzić, czy serwer konfiguracji został pomyślnie skojarzony z utworzonymi zasadami replikacji, wykonaj polecenie [Konfiguruj ustawienia replikacji](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>Krok 6: Pojemność planu

1. Użyj [planisty pojemności,](../../site-recovery/site-recovery-capacity-planner.md) aby dokładnie oszacować przepustowość sieci, pamięć masową i inne wymagania, aby spełnić twoje potrzeby związane z replikacją. 
2. Po zakończeniu wybierz pozycję **Tak, zrobiłem to** w obszarze **Czy ukończyłeś planowanie pojemności?**.

   ![Pole do potwierdzenia ukończenia planowania zdolności produkcyjnych][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Krok 7: Zainstaluj usługę mobilności i włącz replikację

1. Można [wypchnąć instalację](../../site-recovery/vmware-walkthrough-overview.md) do źródłowych maszyn wirtualnych lub [ręcznie zainstalować usługę mobilności](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) na źródłowych maszynach wirtualnych. Wymagania dotyczące pchania instalacji i ścieżki instalatora ręcznego można znaleźć w podanym łączu. Jeśli wykonujesz instalację ręczną, może być konieczne użycie wewnętrznego adresu IP w celu znalezienia serwera konfiguracji.

   ![Strona Szczegóły serwera konfiguracji][12]

   Maszyna wirtualna awaryjna będzie miała dwa dyski tymczasowe: jeden z podstawowej maszyny Wirtualnej, a drugi utworzony podczas inicjowania obsługi administracyjnej maszyny Wirtualnej w regionie odzyskiwania. Aby wykluczyć dysk tymczasowy przed replikacją, zainstaluj usługę mobilności przed włączeniem replikacji. Aby dowiedzieć się więcej o wykluczaniu dysku tymczasowego, zobacz [Wykluczanie dysków z replikacji](../../site-recovery/vmware-walkthrough-overview.md).

2. Aby włączyć replikację:
   1. Wybierz opcję **Replikuj** > **źródło**aplikacji . Po włączeniu replikacji po raz pierwszy wybierz **+Replikuj** w przechowalni, aby włączyć replikację dla dodatkowych maszyn.
   2. W kroku 1 **skonfiguruj źródło** jako serwer przetwarzania.
   3. W kroku 2 określ model wdrażania po pracy awaryjnej, konto magazynu w wersji premium do migracji, standardowe konto magazynu do zapisywania dzienników i sieć wirtualną, która nie powiedzie się.
   4. W kroku 3 dodaj chronione maszyny wirtualne według adresu IP. (Aby go znaleźć, może być potrzebny wewnętrzny adres IP).
   5. W kroku 4 skonfiguruj właściwości, wybierając konta, które zostały wcześniej skonfigurowane na serwerze przetwarzania.
   6. W kroku 5 wybierz zasadę replikacji utworzoną wcześniej w sekcji "Krok 5: Konfigurowanie ustawień replikacji".
   7. Kliknij przycisk **OK**.

   > [!NOTE]
   > Gdy maszyna wirtualna platformy Azure jest cofnięta i uruchomiona ponownie, nie ma żadnej gwarancji, że otrzyma ten sam adres IP. Jeśli adres IP serwera konfiguracji/serwera przetwarzania lub chronionych maszyn wirtualnych platformy Azure ulegnie zmianie, replikacja w tym scenariuszu może nie działać poprawnie.

   ![Włącz okienko replikacji z zaznaczoną wybraną wybraną wybraną wybraną][13]

Podczas projektowania środowiska usługi Azure Storage zaleca się użycie oddzielnych kont magazynu dla każdej maszyny Wirtualnej w zestawie dostępności. Zaleca się przestrzeganie najlepszych rozwiązań w warstwie magazynu, aby [używać wielu kont magazynu dla każdego zestawu dostępności](../linux/manage-availability.md). Dystrybucja dysków maszyn wirtualnych na wiele kont magazynu pomaga zwiększyć dostępność magazynu i dystrybuuje we/wy w infrastrukturze magazynu platformy Azure.

Jeśli maszyny wirtualne są w zestawie dostępności, zamiast replikowania dysków wszystkich maszyn wirtualnych do jednego konta magazynu, zdecydowanie zaleca się wielokrotne migrowanie wielu maszyn wirtualnych. W ten sposób maszyny wirtualne w tym samym zestawie dostępności nie współużytkują jedno konto magazynu. Okienko **Włącz replikację** służy do konfigurowania docelowego konta magazynu dla każdej maszyny Wirtualnej po jednym naraz.
 
Model wdrażania po pracy awaryjnej można wybrać zgodnie z potrzebami. Jeśli wybierzesz usługę Azure Resource Manager jako model wdrażania po pracy awaryjnej, można w trybie failmover maszyny Wirtualnej (Resource Manager) do maszyny Wirtualnej (Menedżer zasobów) lub można awaryjnie maszyny Wirtualnej (klasycznej) do maszyny wirtualnej (Menedżer zasobów).

### <a name="step-8-run-a-test-failover"></a>Krok 8: Uruchamianie testu po awarii

Aby sprawdzić, czy replikacja jest zakończona, wybierz wystąpienie odzyskiwania witryny, a następnie wybierz pozycję **Ustawienia** > **elementów replikowanych**. Zostanie wyświetlony stan i procent procesu replikacji. 

Po zakończeniu replikacji początkowej uruchom test pracy awaryjnej, aby sprawdzić poprawność strategii replikacji. Aby uzyskać szczegółowe kroki pracy awaryjnej testu, zobacz [Uruchamianie testowej pracy awaryjnej w odzyskiwaniu lokacji.](../../site-recovery/vmware-walkthrough-overview.md) 

> [!NOTE]
> Przed uruchomieniem pracy awaryjnej upewnij się, że maszyny wirtualne i strategia replikacji spełniają wymagania. Aby uzyskać więcej informacji na temat uruchamiania pracy awaryjnej testu, zobacz [Testowanie pracy awaryjnej na platformie Azure w usłudze Site Recovery.](../../site-recovery/site-recovery-test-failover-to-azure.md)

Stan pracy awaryjnej testu można sprawdzić w*YOUR_FAILOVER_PLAN_NAME***zadania** >  **ustawień** > . W okienku można zobaczyć podział kroków i wyniki sukcesu/niepowodzenia. Jeśli test pracy awaryjnej nie powiedzie się w dowolnym kroku, wybierz krok, aby sprawdzić komunikat o błędzie. 

### <a name="step-9-run-a-failover"></a>Krok 9: Uruchamianie trybu failover

Po zakończeniu pracy awaryjnej testu uruchom przemijenie awaryjne, aby przeprowadzić migrację dysków do magazynu w wersji Premium i zreplikować wystąpienia maszyny Wirtualnej. Postępuj zgodnie ze szczegółowymi instrukcjami w [sprawie Uruchamianie trybu failover](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Pamiętaj, aby wybrać **opcję Zamknij maszyny wirtualne i zsynchronizować najnowsze dane**. Ta opcja określa, że usługa Site Recovery powinna próbować zamknąć chronione maszyny wirtualne i zsynchronizować dane, tak aby najnowsza wersja danych została przejęta awaryjnie. Jeśli nie wybierzesz tej opcji lub próba nie powiedzie się, przewijanie awaryjne będzie pochodzić z najnowszego dostępnego punktu odzyskiwania maszyny Wirtualnej. 

Usługa Site Recovery utworzy wystąpienie maszyny Wirtualnej, którego typ jest taki sam jak maszyna wirtualna obsługująca pamięć wirtualną w ramach magazynu w jakości Premium. Wydajność i cenę różnych wystąpień maszyn wirtualnych można sprawdzić, przechodząc do [ceny maszyn wirtualnych systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) lub maszyn [wirtualnych systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Kroki po migracji

1. **Skonfiguruj replikowane maszyny wirtualne do zestawu dostępności, jeśli ma to zastosowanie.** Usługa Site Recovery nie obsługuje migracji maszyn wirtualnych wraz z zestawem dostępności. W zależności od wdrożenia replikowanej maszyny Wirtualnej wykonaj jedną z następujących czynności:
   * Dla maszyny Wirtualnej utworzonej za pomocą klasycznego modelu wdrażania: Dodaj maszynę wirtualną do dostępności ustawionej w witrynie Azure portal. Aby uzyskać szczegółowe kroki, przejdź do [dodawania istniejącej maszyny wirtualnej do zestawu dostępności](../linux/classic/configure-availability-classic.md).
   * Dla maszyny Wirtualnej utworzonej za pomocą modelu wdrażania Menedżera zasobów: Zapisz konfigurację maszyny wirtualnej, a następnie usuń i ponownie utwórz maszyny wirtualne w zestawie dostępności. Aby to zrobić, użyj skryptu w [set Azure Resource Manager Zestaw dostępności maszyny Wirtualnej](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Przed uruchomieniem tego skryptu sprawdź jego ograniczenia i zaplanuj przestoje.

2. **Usuń stare maszyny wirtualne i dyski**. Upewnij się, że dyski premium są zgodne z dyskami źródłowymi i że nowe maszyny wirtualne pełnią tę samą funkcję co źródłowe maszyny wirtualne. Usuń maszynę wirtualną i usuń dyski z kont magazynu źródłowego w witrynie Azure portal. Jeśli występuje problem, w którym dysk nie jest usuwany, nawet jeśli maszyna wirtualna została usunięta, zobacz [Rozwiązywanie problemów z błędami usuwania zasobów magazynu](storage-resource-deletion-errors.md).

3. **Czyszczenie infrastruktury odzyskiwania witryny platformy Azure**. Jeśli odzysk lokacji nie jest już potrzebny, można wyczyścić jego infrastrukturę. Usuń elementy replikowane, serwer konfiguracji i zasady odzyskiwania, a następnie usuń magazyn usługi Azure Site Recovery.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Monitorowanie i rozwiązywanie problemów z ochroną maszyn wirtualnych i serwerów fizycznych](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Forum odzyskiwania witryny platformy Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Następne kroki

W przypadku określonych scenariuszy migracji maszyn wirtualnych zobacz następujące zasoby:

* [Migrowanie maszyn wirtualnych platformy Azure między kontami magazynu](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Utwórz dysk VHD systemu Windows Server i przekaż go do platformy Azure](upload-generalized-managed.md)
* [Migrowanie maszyn wirtualnych z usług Amazon AWS na platformę Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Ponadto zobacz następujące zasoby, aby dowiedzieć się więcej o usłudze Azure Storage i maszynach wirtualnych platformy Azure:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Maszyny wirtualne platformy Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)

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
