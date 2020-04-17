---
title: Migracja bezagentowej migracji serwera azure na maszyny Wirtualne VMware
description: Dowiedz się, jak uruchomić bezagentową migrację maszyn wirtualnych VMware za pomocą usługi Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 4612c9b0ea2ef8d53b0c04f47628f3789705d833
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535319"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (bez agenta)

W tym artykule pokazano, jak migrować lokalne maszyny wirtualne VMware na platformę Azure przy użyciu migracji bez agenta za pomocą narzędzia migracji serwera migracji usługi Azure.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdowania, oceny i migracji lokalnych aplikacji i obciążeń oraz wystąpień maszyn wirtualnych AWS/GCP na platformie Azure. Centrum udostępnia narzędzia migracji platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania innych firm (ISV).

Ten samouczek jest trzecim z serii, który pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure przy użyciu oceny i migracji serwera migracji usługi Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowanie maszyn wirtualnych do migracji.
> * Dodaj narzędzie migracji do serwera migracji platformy Azure.
> * Odkryj maszyny wirtualne, które chcesz przeprowadzić migrację.
> * Rozpocznij replikowanie maszyn wirtualnych.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację maszyny Wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.

## <a name="migration-methods"></a>Metody migracji

Maszyny wirtualne VMware można migrować na platformę Azure za pomocą narzędzia migracji serwera migracji platformy Azure. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez agenta. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich niczego.
- Migracja z agentem replikacji. Zainstaluj agenta na maszynie wirtualnej do replikacji.

Aby zdecydować, czy chcesz używać migracji bez agenta czy na podstawie agenta, zapoznaj się z tymi artykułami:

- [Dowiedz się, jak](server-migrate-overview.md) działa migracja bez agenta, i [porównaj metody migracji.](server-migrate-overview.md#compare-migration-methods)
- [Przeczytaj ten artykuł,](tutorial-migrate-vmware-agent.md) jeśli chcesz użyć metody opartej na agencie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Ukończ pierwszy samouczek](tutorial-prepare-vmware.md) z tej serii, aby skonfigurować platformę Azure i VMware do migracji. W szczególności, w tym samouczku trzeba:
    - [Przygotuj platformę Azure](tutorial-prepare-vmware.md#prepare-azure) do migracji.
    - [Przygotuj środowisko lokalne](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) do migracji.
    
2. Zaleca się, aby spróbować oceny maszyn wirtualnych VMware z usługi Azure Migrate Server Assessment przed migracją ich do platformy Azure. Aby skonfigurować ocenę, [wykonaj drugi samouczek](tutorial-assess-vmware.md) z tej serii. Jeśli nie chcesz oceniać maszyn wirtualnych, możesz pominąć ten samouczek. Chociaż zaleca się wypróbowanie oceny, ale nie trzeba uruchamiać oceny przed wypróbowaniem migracji.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji serwera migracji platformy Azure

Dodaj narzędzie Migracji platformy Azure:Migracja serwera.

- Jeśli po drugim samouczku do [oceny maszyn wirtualnych VMware](/tutorial-assess-vmware.md), można śmiało i dodać narzędzie.
- Jeśli nie po drugim samouczku, [postępuj zgodnie z tymi instrukcjami,](how-to-add-tool-first-time.md) aby skonfigurować projekt migracji platformy Azure.  Podczas tworzenia projektu dodajesz narzędzie Migracji azure:Migracja serwera.

Jeśli masz skonfigurowany projekt, dodaj narzędzie w następujący sposób:

1. W projekcie migracji platformy Azure kliknij pozycję **Przegląd**. 
2. W **przypadku serwerów Discover oceniaj i migruj**kliknij pozycję **Oceń i migruj serwery**.

     ![Ocenianie i migrowanie serwerów](./media/tutorial-migrate-vmware/assess-migrate.png)

3. W **obszarze Narzędzia migracji**wybierz pozycję Kliknij **tutaj, aby dodać narzędzie do migracji, gdy jesteś gotowy do migracji**.

    ![Wybieranie narzędzia](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na liście narzędzi wybierz narzędzie **Azure Migrate: Server Migration** > **Add tool**

    ![Narzędzie do migracji serwera](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia migracji platformy Azure

Migracja serwera migracji usługi Azure uruchamia lekkie urządzenie VMware VM. Urządzenie wykonuje odnajdowanie maszyn wirtualnych i wysyła metadane maszyny Wirtualnej i dane o wydajności do migracji usługi Azure Migrate:Server. To samo urządzenie jest również używane przez narzędzie Azure Migrate:Server Assessment do wykonywania bezagentowej migracji maszyn wirtualnych VMware.

- Jeśli po [tutorialu do oceny maszyn wirtualnych VMware](tutorial-assess-vmware.md), już skonfigurować urządzenie podczas tego samouczka.
- Jeśli nie po tym samouczku, można skonfigurować urządzenie teraz, przy użyciu jednej z następujących metod:
    - [Skonfiguruj](how-to-set-up-appliance-vmware.md) na maszynie wirtualnej VMware przy użyciu pobranego szablonu OVA.
    - Skonfiguruj na maszynie wirtualnej VMware lub na komputerze fizycznym za pomocą skryptu instalatora programu PowerShell. [Ta metoda](deploy-appliance-script.md) powinna być używana, jeśli nie można skonfigurować maszyny Wirtualnej przy użyciu szablonu OVA lub jeśli jesteś w usłudze Azure government.

Po utworzeniu urządzenia można sprawdzić, czy można połączyć się z oceną migracji:serwera platformy Azure, skonfigurować go po raz pierwszy i zarejestrować go w projekcie migracji platformy Azure.


## <a name="prepare-vms-for-migration"></a>Przygotowanie maszyn wirtualnych do migracji

Usługa Azure Migrate wymaga pewnych zmian w maszynach wirtualnych, aby upewnić się, że maszyny wirtualne mogą być migrowane na platformę Azure.

- W przypadku niektórych systemów operacyjnych usługa Azure Migrate wprowadza te zmiany automatycznie. [Dowiedz się więcej](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Jeśli przeprowadzasz migrację maszyny Wirtualnej, która nie ma jednego z tych systemów operacyjnych, postępuj zgodnie z instrukcjami, aby przygotować maszynę wirtualną.
- Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. Jeśli przed dokonaniem zmiany zostanie migrowane maszyny Wirtualnej, maszyna wirtualna może nie zostać uruchomiony na platformie Azure.
- Zmiany konfiguracji wprowadzone na lokalnych maszynach wirtualnych są replikowane na platformie Azure po włączeniu replikacji dla maszyny Wirtualnej. Aby upewnić się, że zmiany są replikowane, upewnij się, że punkt odzyskiwania, do którego przeprowadzasz migrację, jest późniejszy niż czas, w którym zmiany konfiguracji zostały wprowadzone lokalnie.


### <a name="prepare-windows-server-vms"></a>Przygotowywanie maszyn wirtualnych z systemem Windows Server

**Akcja** | **Szczegóły** | **Instrukcje**
--- | --- | ---
Upewnij się, że woluminy systemu Windows w maszynie Wirtualnej platformy Azure używają tych samych przypisań liter dysku, co lokalna maszyna wirtualna. | Skonfiguruj zasady sieci SAN jako wszystko w trybie online. | 1. Zaloguj się do maszyny Wirtualnej za pomocą konta administratora i otwórz okno polecenia.<br/> 2. Wpisz **diskpart,** aby uruchomić narzędzie Diskpart.<br/> 3. Wpisz **SAN POLICY=OnlineAll**<br/> 4. Wpisz exit, aby opuścić diskpart, i zamknij wiersz polecenia.
Włączanie konsoli dostępu szeregowego platformy Azure dla maszyny Wirtualnej platformy Azure | Pomaga to w rozwiązywaniu problemów. Nie trzeba ponownie uruchomić maszyny Wirtualnej. Maszyna wirtualna platformy Azure uruchomi się przy użyciu obrazu dysku, a to jest równoważne ponownemu uruchomieniu nowej maszyny Wirtualnej. | Postępuj zgodnie [z tymi instrukcjami,](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) aby włączyć.
Instalowanie integracji gościa funkcji Hyper-V | W przypadku migracji komputerów z systemem Windows Server 2003 należy zainstalować usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny wirtualnej. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Pulpit zdalny | Włącz pulpit zdalny na maszynie wirtualnej i sprawdź, czy Zapora systemu Windows nie blokuje dostępu pulpitu zdalnego w żadnych profilach sieciowych. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Przygotowywanie maszyn wirtualnych z systemem Linux

**Akcja** | **Szczegóły** 
--- | --- | ---
Instalowanie usług integracji systemu Hyper-V linux | Większość nowych wersji dystrybucji Linuksa ma to domyślnie uwzględnione.
Odbuduj obraz linuxowy, aby zawierał niezbędne sterowniki Hyper-V | Gwarantuje to, że maszyna wirtualna zostanie uruchomiony na platformie Azure i jest wymagane tylko w niektórych dystrybucjach.
Włączanie rejestrowania konsoli szeregowych platformy Azure | Pomaga to w rozwiązywaniu problemów. Nie trzeba ponownie uruchomić maszyny Wirtualnej. Maszyna wirtualna platformy Azure uruchomi się przy użyciu obrazu dysku, a to jest równoważne ponownemu uruchomieniu nowej maszyny Wirtualnej.<br/> Postępuj zgodnie [z tymi instrukcjami,](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) aby włączyć.
Aktualizowanie pliku mapy urządzenia | Aktualizowanie pliku mapy urządzenia, który ma nazwę urządzenia do skojarzeń woluminów, aby używać trwałych identyfikatorów urządzeń
Aktualizowanie wpisów fstab | Aktualizowanie wpisów w celu używania trwałych identyfikatorów woluminów.
Usuń regułę udev | Usuń wszystkie reguły udev, które rezerwują nazwy interfejsów na podstawie adresu mac itp.
Aktualizowanie interfejsów sieciowych | Aktualizowanie interfejsów sieciowych w celu odbierania adresu IP na podstawie usługi DHCP.
Włącz ssh | Upewnij się, że ssh jest włączona, a usługa sshd jest ustawiona na automatyczne uruchamianie po ponownym uruchomieniu komputera.<br/> Upewnij się, że przychodzące żądania połączenia ssh nie są blokowane przez zaporę systemu operacyjnego lub reguły skryptowe.

[W tym artykule](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) omówiono te kroki dotyczące uruchamiania maszyny Wirtualnej z systemem Linux na platformie Azure i dołącz instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.  


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Po ukończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych VMware na platformę Azure. 

> [!NOTE]
> Można replikować do 10 maszyn razem. Jeśli chcesz replikować więcej, a następnie replikuj je jednocześnie w partiach po 10. W przypadku migracji bez agenta można uruchomić do 100 jednoczesnych replikacji.

1. W projekcie migracji platformy Azure > **serwery**, **Migracja platformy Azure: Migracja serwera**, kliknij przycisk **Replikuj**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W obszarze **Urządzenie lokalne** wybierz nazwę skonfigurowanego urządzenia usługi Azure Migrate > przycisk **OK**. 

    ![Ustawienia źródła](./media/tutorial-migrate-vmware/source-settings.png)

    - Ten krok zakłada, że urządzenie zostało już skonfigurowane po zakończeniu samouczka.
    - Jeśli urządzenie nie zostało skonfigurowane, postępuj zgodnie z instrukcjami zawartymi w [tym artykule](how-to-set-up-appliance-vmware.md).

4. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

    ![Wybieranie oceny](./media/tutorial-migrate-vmware/select-assessment.png)

5. W obszarze **Maszyny wirtualne** wyszukaj potrzebne maszyny wirtualne i sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij **przycisk Dalej: Ustawienia celu**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-vms.png)

6. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
7. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

8. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny Wirtualnej:** Jeśli używasz zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny Wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) maszyny Wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności:** Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeniowe maszyny Wirtualnej](./media/tutorial-migrate-vmware/compute-settings.png)

9. W obszarze **Dyski** określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowe dyski SSD/dyski twarde lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-vmware/disks.png)

10. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem replikacji w obszarze **Zarządzanie maszynami** > **replikacji**. Ustawień nie można zmienić po rozpoczęciu replikacji.

### <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi administracyjnej po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, którą replikujesz w projekcie migracji platformy Azure, migracja serwera migracji platformy Azure automatycznie aprowiuje te zasoby w tej samej grupie zasobów co projekt.

- **Magistrala usługi:** Migracja serwera migracji usługi Azure używa magistrali usług do wysyłania komunikatów aranżacji replikacji do urządzenia.
- **Konto magazynu bramy:** Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dziennika:** urządzenie migracji platformy Azure przekazuje dzienniki replikacji maszyn wirtualnych do konta magazynu dziennika. Usługa Azure Migrate stosuje informacje o replikacji do dysków zarządzanych repliki.
- **Magazyn kluczy:** urządzenie migracji platformy Azure używa magazynu kluczy do zarządzania ciągami połączeń dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji. Należy skonfigurować uprawnienia, które magazyn kluczy musi uzyskać dostęp do konta magazynu podczas przygotowywania. [Przejrzyj te uprawnienia](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault).   


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po **kliknięciu przycisku Replikacja** rozpoczyna się zadanie replikacji początkowej. 
- Po pomyślnym zakończeniu zadania replikacji początkowej maszyny rozpoczynają replikację początkową na platformie Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny Wirtualnej. Dane dysku z migawki są replikowane do repliki dysków zarządzanych na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja delta. Przyrostowe zmiany na dyskach lokalnych są okresowo replikowane na dyski repliki na platformie Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Stan replikacji można monitorować, klikając opcję **Replikowanie serwerów** w **programie Azure Migrate: Server Migration**.
![Monitorowanie replikacji](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można uruchomić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, aby zrobić to co najmniej raz dla każdego komputera, przed migracją.

- Uruchomienie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają operacyjne i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migrując do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Za pomocą replikowanego testu maszyny Wirtualnej platformy Azure można sprawdzić poprawność migracji, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W **celach** > migracji**Serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po sprawdzeniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację komputerów lokalnych.

1. W projekcie migracji platformy Azure > **serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij pozycję **Replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W **obszarze Migracja** > Zamknij**maszyny wirtualne i wykonaj planowaną migrację bez utraty danych**, wybierz tak **OK** > **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby zsynchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Gwarantuje to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj replikację**. Zatrzymuje replikację komputera lokalnego i czyści informacje o stanie replikacji dla maszyny Wirtualnej.
2. Zainstaluj agenta systemu [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) platformy Azure na zmigrowanych komputerach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Przetnij ruch do zmigrowanego wystąpienia maszyny Wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze praktyki po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Blokowanie i ograniczanie dostępu do ruchu przychodzącego za pomocą [usługi Azure Security Center — zarządzanie tylko w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [proces migracji](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) do chmury w ramach wdrażania chmury azure.
