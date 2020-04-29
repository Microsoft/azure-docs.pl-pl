---
title: Migrowanie serwerów z systemem Windows Server 2008 do platformy Azure za pomocą Azure Site Recovery
description: W tym artykule opisano sposób migrowania maszyn lokalnych z systemem Windows Server 2008 na platformę Azure przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 20fe29a6588891c35520db01ac0403fb5b3a85d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73936137"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrowanie serwerów z systemem Windows Server 2008 na platformę Azure

W tym samouczku pokazano, jak przeprowadzić migrację lokalnych serwerów z systemem Windows Server 2008 lub 2008 R2 na platformę Azure przy użyciu usługi Azure Site Recovery. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowanie środowiska lokalnego do migracji
> * Konfigurowanie środowiska docelowego
> * Konfigurowanie zasad replikacji
> * Włączanie replikacji
> * Uruchamianie testowania migracji w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami
> * Tryb failover na platformie Azure i kończenie migracji

Sekcja Ograniczenia i znane problemy zawiera niektóre ograniczenia i obejścia znanych problemów, które można napotkać podczas migracji maszyn z systemem Windows Server 2008 na platformę Azure. 

> [!NOTE]
> Teraz można migrować z lokalizacji lokalnej na platformę Azure przy użyciu usługi Azure Migrate. [Dowiedz się więcej](../migrate/migrate-services-overview.md).


## <a name="supported-operating-systems-and-environments"></a>Obsługiwane systemy operacyjne i środowiska


|System operacyjny  | Środowisko lokalne  |
|---------|---------|
|Windows Server 2008 z dodatkiem SP2 — wersje 32-bitowe i 64-bitowe (IA-32 i x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     Maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne    |
|Windows Server 2008 R2 z dodatkiem SP1 — wersja 64-bitowa</br>- Standard</br>- Enterprise</br>- Datacenter     |     Maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne|

> [!WARNING]
> - Migracja serwerów z systemem Server Core nie jest obsługiwana.
> - Przed migracją upewnij się, że masz zainstalowany najnowszy dodatek Service Pack i aktualizacje systemu Windows.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem warto zapoznać się z architekturą usługi Azure Site Recovery dla [migracji serwera VMware i fizycznego](vmware-azure-architecture.md) lub [migracji maszyny wirtualnej funkcji Hyper-V](hyper-v-azure-architecture.md) 

Aby przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V z systemem Windows Server 2008 lub Windows Server 2008 R2, należy wykonać czynności opisane w samouczku [Migrowanie maszyn lokalnych na platformę Azure](migrate-tutorial-on-premises-azure.md).

Pozostała część tego samouczka pokazuje, jak możesz migrować lokalne maszyny wirtualne VMware i serwery fizyczne z systemem Windows Server 2008 lub 2008 R2.
> [!TIP]
> Szukasz bezagentowego sposobu migracji maszyn wirtualnych VMware na platformę Azure? [Kliknij tutaj](https://aka.ms/migrateVMs-signup)


## <a name="limitations-and-known-issues"></a>Ograniczenia i znane problemy

- Serwer konfiguracji, dodatkowe serwery przetwarzania i usługa mobilności używane do przeprowadzania migracji serwerów z systemem Windows Server 2008 z dodatkiem SP2 powinny mieć uruchomioną wersję 9.19.0.0 lub nowszą oprogramowania usługi Azure Site Recovery.

- Punkty odzyskiwania spójne na poziomie aplikacji i funkcja spójności wielu maszyn wirtualnych nie są obsługiwane dla replikacji serwerów z systemem Windows Server 2008 z dodatkiem SP2. Serwery z systemem Windows Server 2008 z dodatkiem SP2 powinny być migrowane do punktu odzyskiwania spójnego na poziomie awarii. Punkty odzyskiwania spójne na poziomie awarii są domyślnie generowane co 5 minut. Używanie zasad replikacji ze skonfigurowaną częstotliwością migawek spójnych na poziomie aplikacji spowoduje, że kondycja replikacji przejdzie w stan krytyczny ze względu na brak punktów odzyskiwania spójnych na poziomie aplikacji. W celu uniknięcia wyników fałszywie dodatnich ustaw częstotliwość migawek spójnych na poziomie aplikacji w zasadach replikacji na wartość „Wyłączone”.

- Migrowane serwery powinny mieć program .NET Framework 3.5 z dodatkiem Service Pack 1, aby działała usługa mobilności.

- Jeśli serwer ma dyski dynamiczne, w niektórych konfiguracjach możesz zauważyć, że te dyski na serwerze w trybie failover są oznaczone jako będące w trybie offline lub są przedstawiane jako dyski obce. Możesz także zauważyć, że stan dublowanego zestawu dla woluminów dublowanych na dynamicznych dyskach jest oznaczony jako „Niepowodzenie nadmiarowości”. Możesz naprawić ten problem z pliku diskmgmt.msc, ręcznie importując te dyski i ponownie je uaktywniając.

- Migrowane serwery powinny mieć sterownik vmstorfl.sys. Tryb failover może zakończyć się niepowodzeniem, jeśli sterownik nie występuje na migrowanym serwerze. 
  > [!TIP]
  >Sprawdź, czy sterownik znajduje się na ścieżce „C:\Windows\system32\drivers\vmstorfl.sys”. Jeśli sterownik nie zostanie znaleziony, możesz obejść ten problem, tworząc plik zastępczy w tym miejscu. 
  >
  > Otwórz wiersz polecenia (Uruchom > cmd) i uruchom następujące polecenie: „copy nul c:\Windows\system32\drivers\vmstorfl.sys”

- Możesz nie mieć możliwości nawiązania połączenia RDP z serwerami z systemem Windows Server 2008 z dodatkiem SP2 z 32-bitowym systemem operacyjnym natychmiast po przełączeniu w tryb failover lub tryb testowania pracy w trybie failover do platformy Azure. Uruchom ponownie maszynę wirtualną pracy awaryjnej z witryny Azure Portal i spróbuj ponownie nawiązać połączenie. Jeśli nadal nie możesz się połączyć, sprawdź, czy serwer jest skonfigurowany do zezwalania na połączenia pulpitu zdalnego i upewnij się, że nie istnieją żadne reguły zapory ani grupy zabezpieczeń sieci blokujące połączenie. 
  > [!TIP]
  > Zdecydowanie zalecamy przeprowadzenie testowania pracy w trybie failover przed przeprowadzeniem migracji serwerów. Upewnij się, że został wykonany co najmniej jeden udany test pracy w trybie failover na każdym migrowanym serwerze. W ramach testowania pracy w trybie failover nawiąż połączenie z maszyną pracy awaryjnej i upewnij się, że wszystko działa zgodnie z oczekiwaniami.
  >
  >Testowanie operacji trybu failover nie wprowadza zakłóceń i pomaga przetestować migracje dzięki tworzeniu maszyn wirtualnych w wybranej przez siebie izolowanej sieci. W odróżnieniu od operacji trybu failover podczas operacji testowania trybu failover replikacja danych w dalszym ciągu postępuje. Przed przystąpieniem do migracji możesz wykonać tyle testów pracy w trybie failover, ile chcesz. 
  >
  >


## <a name="getting-started"></a>Wprowadzenie

Wykonaj następujące zadania w celu przygotowania subskrypcji platformy Azure i lokalnego środowiska programu VMware/fizycznego:

1. [Przygotowywanie platformy Azure](tutorial-prepare-azure.md)
2. Przygotowywanie lokalnego programu [VMware](vmware-azure-tutorial-prepare-on-premises.md)


## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

1. Zaloguj się do Recovery Services [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Kliknij pozycję **Utwórz zasoby** > **Narzędzia** > do zarządzania zasobami**i Site Recovery**.
3. W polu **Nazwa** podaj przyjazną nazwę **W2K8-migration**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów **w2k8migrate**.
5. Określ region platformy Azure. Aby sprawdzić obsługiwane regiony, zobacz sekcję dotyczącą dostępności geograficznej w temacie [Szczegóły cennika usługi Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Aby szybko uzyskać dostęp do magazynu z pulpitu nawigacyjnego, kliknij pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**.

   ![Nowy magazyn](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Nowy magazyn zostanie dodany do sekcji **Pulpit nawigacyjny** w obszarze **Wszystkie zasoby** oraz pojawi się na stronie głównej **Magazyny usługi Recovery Services**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Przygotowanie środowiska lokalnego do migracji

- Aby zmigrować maszyny wirtualne z systemem Windows Server 2008 działające w programie VMware, [skonfiguruj lokalny serwer konfiguracji w programie VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Jeśli serwera konfiguracji nie można skonfigurować jako maszyny wirtualnej VMware, [skonfiguruj serwer konfiguracji na lokalnym serwerze fizycznym lub maszynie wirtualnej](physical-azure-disaster-recovery.md#set-up-the-source-environment).

## <a name="set-up-the-target-environment"></a>Konfigurowanie środowiska docelowego

Wybierz i zweryfikuj zasoby docelowe.

1. Kliknij pozycję **Przygotuj** > **miejsce docelowe**infrastruktury i wybierz subskrypcję platformy Azure, której chcesz użyć.
2. Określ model wdrażania usługi Resource Manager.
3. Usługa Site Recovery sprawdza, czy masz co najmniej jedno zgodne konto magazynu Azure i co najmniej jedną sieć platformy Azure.


## <a name="set-up-a-replication-policy"></a>Konfigurowanie zasad replikacji

1. Aby utworzyć nowe zasady replikacji, kliknij kolejno pozycje **Site Recovery infrastruktura** > **zasady** > replikacji **+ zasady replikacji**.
2. W obszarze **Tworzenie zasad replikacji** określ nazwę zasad.
3. W obszarze **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Przekroczenie tego limitu przez cel punktu odzyskiwania replikacji powoduje wygenerowanie alertu.
4. W obszarze **Przechowywanie punktu odzyskiwania** określ (w godzinach), jak długie jest okno przechowywania dla każdego punktu odzyskiwania. Replikowane serwery można odzyskać do dowolnego punktu w tym oknie. Przechowywanie do 24 godzin jest obsługiwane dla maszyn replikowanych do magazynu w warstwie Premium, zaś do 72 godzin dla magazynu w warstwie Standardowa.
5. W obszarze **Częstotliwość wykonywania migawek na poziomie aplikacji** określ wartość **Wyłączone**. Kliknij przycisk **OK**, aby utworzyć zasady.

Zasady zostaną automatycznie skojarzone z serwerem konfiguracji.

> [!WARNING]
> Upewnij się, że wartość **WYŁĄCZONE** została określona w ustawieniu częstotliwości wykonywania migawek spójnych na poziomie aplikacji w zasadach replikacji. Podczas replikowania serwerów z systemem Windows Server 2008 obsługiwane są tylko punkty odzyskiwania spójne na poziomie awarii. Określenie jakiejkolwiek innej wartości dla częstotliwości wykonywania migawek spójnych na poziomie aplikacji spowoduje fałszywe alerty przez przełączanie kondycji replikacji serwera w stan krytyczny z powodu braku punktów odzyskiwania spójnych na poziomie aplikacji.

   ![Tworzenie zasad replikacji](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Włączanie replikacji

[Włączanie replikacji](physical-azure-disaster-recovery.md#enable-replication) dla migrowanego serwera z systemem Windows Server 2008 z dodatkiem SP2 / Windows Server 2008 R2 z dodatkiem SP1.
   
   ![Dodawanie serwera fizycznego](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Włączanie replikacji](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Możesz przeprowadzić test pracy w trybie failover serwerów replikacji po ukończeniu replikacji początkowej i zmianie stanu serwera na **Chroniony**.

Uruchom [testowanie trybu failover](tutorial-dr-drill-azure.md), aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

   ![Testowanie pracy w trybie failover](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migracja na platformę Azure

Uruchom tryb failover dla maszyn, które chcesz migrować.

1. W obszarze **Ustawienia** > **zreplikowane elementy** kliknij maszynę > **tryb failover**.
2. W obszarze **tryb failover** wybierz **punkt odzyskiwania** , do którego ma zostać przełączona praca awaryjna. Wybierz najnowszy punkt odzyskiwania.
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery spróbuje wyłączyć serwer przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
4. Sprawdź, czy maszyna wirtualna Azure jest wyświetlana na platformie Azure zgodnie z oczekiwaniami.
5. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy serwer > **Zakończ migrację**. Spowoduje to wykonanie następujących czynności:

    - Powoduje zakończenie procesu migracji, zatrzymanie replikacji serwera oraz zatrzymanie naliczania opłat za usługę Site Recovery dla serwera.
    - W tym kroku oczyszczane są dane replikacji. Nie są jednak usuwane migrowane maszyny wirtualne.

   ![Kończenie migracji](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Nie Anuluj trybu failover w toku**: replikacja serwera została zatrzymana przed rozpoczęciem pracy w trybie failover. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale replikowanie serwera nie będzie kontynuowane.
