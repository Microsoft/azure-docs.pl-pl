---
title: Migrowanie maszyny wirtualnej VMware bez agenta Azure Migrate migracji serwera
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware bez wykorzystania agentów przy użyciu Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 86f24b7fdfee30c182419023e4ed33f6228b3711
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509316"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (bez wykorzystania agentów)

W tym artykule opisano sposób migrowania lokalnych maszyn wirtualnych programu VMware na platformę Azure przy użyciu migracji bez wykorzystania agentów za pomocą narzędzia migracji Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz wystąpień maszyn wirtualnych AWS/GCP na platformę Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ten samouczek jest trzecią częścią serii, która pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure przy użyciu funkcji oceny i migracji serwera Azure Migrate. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj maszyny wirtualne do migracji.
> * Dodaj narzędzie migracji serwera migracji platformy Azure.
> * Odnajdź maszyny wirtualne, które chcesz zmigrować.
> * Rozpocznij replikację maszyn wirtualnych.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) .

## <a name="migration-methods"></a>Metody migracji

Maszyny wirtualne programu VMware można migrować do platformy Azure za pomocą narzędzia migracji Azure Migrate Server. To narzędzie oferuje kilka opcji migracji maszyn wirtualnych VMware:

- Migracja przy użyciu replikacji bez wykorzystania agentów. Migrowanie maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.
- Migracja z agentem na potrzeby replikacji. Zainstaluj agenta na maszynie wirtualnej na potrzeby replikacji.

Aby zdecydować, czy chcesz korzystać z migracji opartej na agencie, czy na agencie, zapoznaj się z następującymi artykułami:

- [Dowiedz się, jak](server-migrate-overview.md) działa migracja bez agentów i [PORÓWNAJ metody migracji](server-migrate-overview.md#compare-migration-methods).
- [Przeczytaj ten artykuł](tutorial-migrate-vmware-agent.md) , jeśli chcesz użyć metody opartej na agencie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Wykonaj pierwszy samouczek](tutorial-prepare-vmware.md) z tej serii, aby skonfigurować platformę Azure i oprogramowanie VMware do migracji. W tym samouczku należy:
    - [Przygotuj platformę Azure](tutorial-prepare-vmware.md#prepare-azure) do migracji.
    - [Przygotuj środowisko lokalne](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) do migracji.
    
2. Zalecamy, aby przed migracją na platformę Azure spróbować ocenić maszyny wirtualne VMware z oceną serwera Azure Migrate. Aby skonfigurować ocenę, [Wykonaj drugi samouczek](tutorial-assess-vmware.md) z tej serii. Jeśli nie chcesz oceniać maszyn wirtualnych, możesz pominąć ten samouczek. Chociaż zalecamy wypróbowanie oceny, ale nie trzeba uruchamiać oceny przed rozpoczęciem migracji.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Dodawanie narzędzia migracji Azure Migrate Server

Dodaj Azure Migrate: Narzędzia migracji serwera.

- Jeśli przeszedł drugi samouczek dotyczący [oceny maszyn wirtualnych VMware](tutorial-assess-vmware.md), możesz przejść do niego i dodać narzędzie.
- Jeśli nie korzystasz z drugiego samouczka, [postępuj zgodnie z poniższymi instrukcjami](how-to-add-tool-first-time.md) , aby skonfigurować projekt Azure Migrate.  Podczas tworzenia projektu należy dodać Azure Migrate: Narzędzie migracji serwera.

Jeśli masz skonfigurowany projekt, Dodaj narzędzie w następujący sposób:

1. W projekcie Azure Migrate kliknij pozycję **Przegląd**. 
2. W obszarze **odnajdywanie, ocenianie i**Migrowanie serwerów kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

     ![Ocenianie i Migrowanie serwerów](./media/tutorial-migrate-vmware/assess-migrate.png)

3. W obszarze **Narzędzia migracji**wybierz **pozycję kliknij tutaj, aby dodać narzędzie do migracji, gdy wszystko jest gotowe do migracji**.

    ![Wybierz narzędzie](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Na liście Narzędzia wybierz pozycję **Azure Migrate: serwer** > —**Dodawanie narzędzia** do migracji

    ![Narzędzie do migracji serwera](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia Azure Migrate

Migracja serwera Azure Migrate uruchamia lekkie urządzenie maszyny wirtualnej VMware. Urządzenie wykonuje odnajdywanie maszyn wirtualnych i wysyła metadane maszyny wirtualnej oraz dane wydajności do Azure Migrate: migracji serwera. To samo urządzenie jest również używane przez Azure Migrate: Narzędzie do oceny serwera, aby przeprowadzić migrację maszyn wirtualnych VMware bez agentów.

- Jeśli wykonano [samouczek w celu oceny maszyn wirtualnych VMware](tutorial-assess-vmware.md), urządzenie zostało już skonfigurowane w tym samouczku.
- Jeśli nie korzystasz z tego samouczka, możesz skonfigurować urządzenie teraz, korzystając z jednej z następujących metod:
    - [Skonfiguruj](how-to-set-up-appliance-vmware.md) na maszynie wirtualnej VMware przy użyciu pobranego szablonu komórki jajowe.
    - Skonfiguruj na maszynie wirtualnej VMware lub na komputerze fizycznym za pomocą skryptu Instalatora programu PowerShell. [Tej metody](deploy-appliance-script.md) należy użyć, jeśli nie można skonfigurować maszyny wirtualnej przy użyciu szablonu komórki jajowe lub w przypadku korzystania z platformy Azure dla instytucji rządowych.

Po utworzeniu urządzenia sprawdź, czy może nawiązać połączenie z Azure Migrate: Ocena serwera, skonfiguruj ją po raz pierwszy i zarejestruj ją za pomocą projektu Azure Migrate.


## <a name="prepare-vms-for-migration"></a>Przygotowywanie maszyn wirtualnych do migracji

Azure Migrate wymaga wprowadzenia zmian w maszynie wirtualnej w celu zapewnienia, że maszyny wirtualne można migrować do platformy Azure.

- W niektórych systemach operacyjnych Azure Migrate automatycznie wprowadza te zmiany. [Dowiedz się więcej](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- W przypadku migrowania maszyny wirtualnej, która nie ma jednego z tych systemów operacyjnych, postępuj zgodnie z instrukcjami, aby przygotować maszynę wirtualną.
- Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure.
- Zmiany konfiguracji wprowadzone w lokalnych maszynach wirtualnych są replikowane na platformę Azure po włączeniu replikacji dla maszyny wirtualnej. Aby upewnić się, że zmiany są replikowane, należy się upewnić, że migrowany punkt odzyskiwania jest późniejszy niż czas, w którym zmiany konfiguracji zostały wprowadzone lokalnie.


### <a name="prepare-windows-server-vms"></a>Przygotowywanie maszyn wirtualnych z systemem Windows Server

**Akcja** | **Szczegóły** | **Instrukcje**
--- | --- | ---
Upewnij się, że woluminy systemu Windows na maszynie wirtualnej platformy Azure używają tych samych przypisań liter dysku co lokalna maszyna wirtualna. | Skonfiguruj zasady sieci SAN jako wszystkie. | 1. Zaloguj się do maszyny wirtualnej przy użyciu konta administratora, a następnie otwórz okno polecenia.<br/> 2. Wpisz polecenie **diskpart** , aby uruchomić narzędzie Diskpart.<br/> 3. Wpisz **zasady sieci San = OnlineAll**<br/> 4. wpisz exit, aby opuścić program DiskPart, i Zamknij wiersz polecenia.
Włączanie konsoli dostępu szeregowego platformy Azure dla maszyny wirtualnej platformy Azure | Ułatwia to rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure rozpocznie rozruch przy użyciu obrazu dysku, co jest równoznaczne z ponownym uruchomieniem nowej maszyny wirtualnej. | Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) , aby włączyć.
Zainstaluj integrację gościa funkcji Hyper-V | W przypadku migrowania maszyn z systemem Windows Server 2003 należy zainstalować usługi integracji gościa funkcji Hyper-V w systemie operacyjnym maszyny wirtualnej. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Pulpit zdalny | Włącz Pulpit zdalny na maszynie wirtualnej i sprawdź, czy Zapora systemu Windows nie blokuje dostępu Pulpit zdalny do żadnych profilów sieciowych. | [Dowiedz się więcej](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Przygotowywanie maszyn wirtualnych z systemem Linux

**Akcja** | **Szczegóły** 
--- | --- | ---
Zainstaluj usługi integracji funkcji Hyper-V w systemie Linux | Większość nowych wersji dystrybucji systemu Linux jest domyślnie uwzględnionych w tym systemie.
Ponowne kompilowanie obrazu init systemu Linux w celu uwzględnienia wymaganych sterowników funkcji Hyper-V | Dzięki temu maszyna wirtualna zostanie uruchomiona na platformie Azure i będzie wymagana tylko w przypadku niektórych dystrybucji.
Włącz rejestrowanie w konsoli szeregowej platformy Azure | Ułatwia to rozwiązywanie problemów. Nie ma potrzeby ponownego uruchamiania maszyny wirtualnej. Maszyna wirtualna platformy Azure rozpocznie rozruch przy użyciu obrazu dysku, co jest równoznaczne z ponownym uruchomieniem nowej maszyny wirtualnej.<br/> Postępuj zgodnie z [tymi instrukcjami](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) , aby włączyć.
Zaktualizuj plik mapy urządzeń | Zaktualizuj plik mapy urządzeń zawierający nazwę urządzenia do skojarzenia woluminu, aby użyć trwałych identyfikatorów urządzeń
Aktualizowanie wpisów fstab | Aktualizowanie wpisów w celu używania identyfikatorów woluminów trwałych.
Usuń regułę udev | Usuń wszystkie reguły udev, które rezerwują nazwy interfejsów na podstawie adresu MAC itp.
Aktualizowanie interfejsów sieciowych | Zaktualizuj interfejsy sieciowe, aby odbierać adresy IP na podstawie protokołu DHCP.
Włączanie protokołu SSH | Upewnij się, że protokół SSH jest włączony i że usługa SSHD jest uruchomiona automatycznie po ponownym uruchomieniu.<br/> Upewnij się, że przychodzące żądania połączenia SSH nie są blokowane przez zaporę systemu operacyjnego ani reguły obsługujące skrypty.

[Postępuj zgodnie z tym artykułem](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) , który omawia te kroki w celu uruchomienia maszyny wirtualnej z systemem Linux na platformie Azure, i Dołącz instrukcje dotyczące niektórych popularnych dystrybucji systemu Linux.  


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Po ukończeniu odnajdywania można rozpocząć replikację maszyn wirtualnych VMware na platformę Azure. 

> [!NOTE]
> Można replikować do 10 maszyn jednocześnie. Jeśli chcesz replikować więcej, a następnie Replikuj je jednocześnie w partiach 10. W przypadku migracji bez agentów można uruchomić maksymalnie 100 równoczesnych replikacji.

1. Na **serwerach**Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-replicate.png)

2. W obszarze **Replikacja** > **Ustawienia źródła** > **Czy maszyny są zwirtualizowane** wybierz pozycję **Tak, z funkcją VMware vSphere Hypervisor**.
3. W obszarze **Urządzenie lokalne** wybierz nazwę skonfigurowanego urządzenia usługi Azure Migrate > przycisk **OK**. 

    ![Ustawienia źródła](./media/tutorial-migrate-vmware/source-settings.png)

    - W tym kroku zakłada się, że urządzenie zostało już skonfigurowane po zakończeniu pracy z samouczkiem.
    - Jeśli nie skonfigurowano urządzenia, postępuj zgodnie z instrukcjami w [tym artykule](how-to-set-up-appliance-vmware.md).

4. W obszarze **Maszyny wirtualne** wybierz maszyny wirtualne, które mają być replikowane.
    - Jeśli przeprowadzasz ocenę dla maszyn wirtualnych, możesz zastosować rekomendacje dotyczące rozmiarów maszyn wirtualnych i typów dysków (Premium/standardowy) w wynikach oceny. W tym celu w obszarze **Zaimportować ustawienia migracji z oceny usługi Azure Migrate?** wybierz opcję **Tak**.
    - Jeśli nie uruchomiono oceny lub nie chcesz używać ustawień oceny, wybierz opcję **Nie**.
    - W przypadku wybrania opcji korzystania z oceny wybierz grupę maszyn wirtualnych i nazwę oceny.

    ![Wybieranie oceny](./media/tutorial-migrate-vmware/select-assessment.png)

5. W obszarze **Maszyny wirtualne** wyszukaj potrzebne maszyny wirtualne i sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-vmware/select-vms.png)

6. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
7. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

8. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: Jeśli korzystasz z zaleceń dotyczących oceny, lista rozwijana rozmiaru maszyny wirtualnej będzie zawierać zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności**: Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeniowe maszyny wirtualnej](./media/tutorial-migrate-vmware/compute-settings.png)

9. W obszarze **Dyski** określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowe dyski SSD/dyski twarde lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Dyski](./media/tutorial-migrate-vmware/disks.png)

10. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji w obszarze **Zarządzanie** > **maszynami replikowanymi**. Ustawień nie można zmienić po rozpoczęciu replikacji.

### <a name="provisioning-for-the-first-time"></a>Inicjowanie obsługi po raz pierwszy

Jeśli jest to pierwsza maszyna wirtualna, która jest replikowana w projekcie Azure Migrate, Azure Migrate migracji serwera automatycznie zainicjuje te zasoby w tej samej grupie zasobów co projekt.

- **Service Bus**: migracja serwera Azure Migrate przy użyciu usługi Service Bus wysyła do urządzenia komunikaty aranżacji replikacji.
- **Konto magazynu bramy**: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników**: urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych repliki.
- **Magazyn kluczy**: urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji. Należy skonfigurować uprawnienia, które Magazyn kluczy musi uzyskać dostęp do konta magazynu podczas przygotowywania. [Przejrzyj te uprawnienia](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault).   


## <a name="track-and-monitor"></a>Śledzenie i monitorowanie


- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej. Dane dysku z migawki są replikowane do dysków zarządzanych repliki na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze**serwery** >  **celów** > migracji**Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-vmware/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W Azure Migrate **serwery** > > Project**Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-vmware/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie** > **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych**wybierz pozycję **tak** > **OK**.
    - Domyślnie usługa Azure Migrate zamyka lokalną maszynę wirtualną i uruchamia replikację na żądanie, aby zsynchronizować wszystkie zmiany maszyny wirtualnej, które wystąpiły od momentu ostatniej replikacji. Gwarantuje to brak utraty danych.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną, > **zatrzymać replikację**. Spowoduje to zatrzymanie replikacji maszyny lokalnej i oczyszczenie informacji o stanie replikacji dla maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) lub [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Wdróż usługę [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
