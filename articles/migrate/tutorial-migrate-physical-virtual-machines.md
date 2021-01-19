---
title: Migrowanie maszyn jako serwera fizycznego na platformę Azure za pomocą Azure Migrate.
description: W tym artykule opisano sposób migrowania maszyn fizycznych na platformę Azure przy użyciu Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: c0f4f1d7f0213ad24d25d8d34235475bbdb0316a
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567106"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrowanie maszyn jako serwerów fizycznych na platformę Azure

W tym artykule opisano sposób migrowania maszyn jako serwerów fizycznych na platformę Azure przy użyciu narzędzia do migracji Azure Migrate: Server. Migrowanie maszyn przez traktowanie ich jako serwerów fizycznych jest przydatne w wielu scenariuszach:

- Migruj lokalne serwery fizyczne.
- Migrowanie maszyn wirtualnych zwirtualizowanych przez platformy takie jak Xen, KVM.
- Migrowanie maszyn wirtualnych funkcji Hyper-V lub oprogramowania VMware, jeśli z jakiegoś powodu nie można użyć standardowego procesu migracji dla [funkcji Hyper-V](tutorial-migrate-hyper-v.md)lub migracji [VMware](server-migrate-overview.md) .
- Migrowanie maszyn wirtualnych działających w chmurach prywatnych.
- Migrowanie maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP).


Ten samouczek jest trzecią częścią serii, która pokazuje, jak oceniać i migrować serwery fizyczne na platformę Azure. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Przygotowanie do korzystania z platformy Azure z Azure Migrate: Migracja serwera.
> * Sprawdź wymagania dotyczące maszyn, które mają zostać poddane migracji, i przygotuj maszynę do Azure Migrate urządzenia do replikacji, które służy do odnajdywania i migrowania maszyn na platformę Azure.
> * Dodaj narzędzie do migracji Azure Migrate Server w centrum Azure Migrate.
> * Skonfiguruj urządzenie do replikacji.
> * Zainstaluj usługę mobilności na maszynach, które chcesz zmigrować.
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z tematem Porady dotyczące Azure Migrate.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

[Przejrzyj](./agent-based-migration-architecture.md) architekturę migracji.

## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Przygotuj platformę Azure do migracji przy użyciu migracji serwera.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu usługi Azure Migrate** | Twoje konto platformy Azure wymaga uprawnień współautora lub właściciela, aby [utworzyć nowy projekt](https://docs.microsoft.com/azure/migrate/create-manage-projects).
**Weryfikowanie uprawnień do konta platformy Azure** | Twoje konto platformy Azure wymaga uprawnień do utworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure Portal otwórz daną subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp** Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, poproś właściciela o przypisanie roli.


### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę współautora maszyny wirtualnej do konta platformy Azure. Zapewnia to następujące uprawnienia:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapisz na dysku zarządzanym platformy Azure. 

### <a name="create-an-azure-network"></a>Tworzenie sieci platformy Azure

[Skonfiguruj](../virtual-network/manage-virtual-network.md#create-a-virtual-network) sieć wirtualną platformy Azure. Podczas replikowania na platformę Azure maszyny wirtualne platformy Azure są tworzone i łączone do sieci wirtualnej platformy Azure, która została określona podczas konfigurowania migracji.

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

Aby przygotować się do migracji serwera fizycznego, należy sprawdzić ustawienia serwera fizycznego i przygotować urządzenie do replikacji.

### <a name="check-machine-requirements-for-migration"></a>Sprawdź wymagania dotyczące maszyn pod kątem migracji

Upewnij się, że maszyny są zgodne z wymaganiami dotyczącymi migracji na platformę Azure. 

> [!NOTE]
> Podczas migrowania maszyn fizycznych Azure Migrate: Migracja serwera korzysta z tej samej architektury replikacji co w przypadku odzyskiwania po awarii opartego na agencie w usłudze Azure Site Recovery, a niektóre składniki mają tę samą bazę kodu. Część zawartości może zostać połączona z dokumentacją Site Recovery.

1. [Sprawdź](migrate-support-matrix-physical-migration.md#physical-server-requirements) wymagania dotyczące serwera fizycznego.
2. Sprawdź, czy maszyny lokalne replikowane na platformę Azure są zgodne z [wymaganiami maszyny wirtualnej platformy Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).
3. Przed przeprowadzeniem migracji na platformę Azure potrzebne są pewne zmiany na maszynach wirtualnych.
    - W niektórych systemach operacyjnych Azure Migrate automatycznie wprowadza te zmiany. 
    - Ważne jest, aby wprowadzić te zmiany przed rozpoczęciem migracji. W przypadku migrowania maszyny wirtualnej przed wprowadzeniem zmiany, maszyna wirtualna może nie zostać uruchomiona na platformie Azure. Przejrzyj zmiany w [systemach Windows](prepare-for-migration.md#windows-machines) i [Linux](prepare-for-migration.md#linux-machines) , które należy wprowadzić.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotuj komputer do urządzenia replikacji

Azure Migrate: Migracja serwera używa urządzenia replikacji do replikowania maszyn na platformę Azure. Urządzenie replikacji uruchamia następujące składniki.

- **Serwer konfiguracji**: serwer konfiguracji koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania**: serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je przy użyciu pamięci podręcznej, kompresji i szyfrowania, a następnie wysyła je do konta magazynu pamięci podręcznej na platformie Azure. 

Przygotuj się do wdrożenia urządzenia w następujący sposób:

- Przygotuj komputer do hostowania urządzenia replikacji. [Zapoznaj](migrate-replication-appliance.md#appliance-requirements) się z wymaganiami dotyczącymi maszyn.
- Urządzenie do replikacji używa programu MySQL. Zapoznaj się z [opcjami](migrate-replication-appliance.md#mysql-installation) instalacji bazy danych MySQL na urządzeniu.
- Przejrzyj adresy URL platformy Azure wymagane przez urządzenie replikacji, aby uzyskać dostęp do chmur [publicznych](migrate-replication-appliance.md#url-access) i [instytucji rządowych](migrate-replication-appliance.md#azure-government-url-access) .
- Przejrzyj wymagania dotyczące dostępu do [portów](migrate-replication-appliance.md#port-access) dla urządzenia replikacji.

> [!NOTE]
> Urządzenia replikacji nie należy instalować na maszynie źródłowej, która ma zostać zreplikowana lub na urządzeniu odnajdywania Azure Migrate i ocenie, które zainstalowano wcześniej.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia do replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia do replikacji. Aby skonfigurować urządzenie do migracji serwera fizycznego, należy pobrać plik Instalatora dla urządzenia, a następnie uruchomić go na [przygotowanej maszynie](#prepare-a-machine-for-the-replication-appliance). Po zainstalowaniu urządzenia należy zarejestrować je na Azure Migrate migracji serwera.


### <a name="download-the-replication-appliance-installer"></a>Pobierz Instalatora urządzenia replikacji

1. Na **serwerach** Azure Migrate Project >, w **Azure Migrate: Migracja serwera**, kliknij przycisk **odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. W obszarze **odnajdywanie** maszyn  >  **są zwirtualizowane maszyny?** kliknij pozycję **niezwirtualizowane/inne**.
4. W **obszarze region docelowy** wybierz region świadczenia usługi Azure, do którego chcesz przeprowadzić migrację maszyn.
5. Wybierz pozycję **Potwierdź, że docelowy region migracji to nazwa regionu**.
6. Kliknij pozycję **Utwórz zasoby**. Spowoduje to utworzenie magazynu Azure Site Recovery w tle.
    - Jeśli już skonfigurowano migrację z Azure Migrate migracji do serwera, nie można skonfigurować opcji docelowej, ponieważ zasoby zostały wcześniej skonfigurowane.    
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje znajdują się w tym regionie.

7. W **czy chcesz zainstalować nowe urządzenie do replikacji?** wybierz pozycję **Zainstaluj urządzenie replikacji**.
9. W obszarze **pobieranie i instalowanie oprogramowania do replikacji urządzenia** Pobierz Instalatora urządzenia i klucz rejestracji. Aby zarejestrować urządzenie, musisz być kluczem. Klucz jest ważny przez pięć dni od jego pobrania.

    ![Pobierz dostawcę](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Skopiuj plik Instalatora urządzenia i plik klucza do komputera z systemem Windows Server 2016, który został utworzony dla urządzenia.
11. Po zakończeniu instalacji Kreator konfiguracji urządzenia zostanie uruchomiony automatycznie (można również uruchomić Kreatora ręcznie przy użyciu skrótu cspsconfigtool utworzonego na pulpicie urządzenia). Za pomocą karty Zarządzanie kontami kreatora można dodać szczegóły konta do użycia podczas instalacji wypychanej usługi mobilności. W tym samouczku będziemy ręcznie instalować usługę mobilności na źródłowych maszynach wirtualnych do replikacji, więc Utwórz fikcyjne konto w tym kroku i przejdź dalej. Poniżej przedstawiono szczegółowe informacje na temat tworzenia fikcyjnego konta "Gość" jako przyjaznej nazwy, "username" jako nazwy użytkownika i hasła do konta. Będziesz używać tego fikcyjnego konta na etapie włączania replikacji. 

12. Po ponownym uruchomieniu urządzenia po zakończeniu instalacji w obszarze **odnajdywanie maszyn** wybierz nowe urządzenie na liście **Wybierz serwer konfiguracji**, a następnie kliknij pozycję **finalizowanie rejestracji**. Finalizowanie rejestracji wykonuje kilka zadań końcowych w celu przygotowania urządzenia do replikacji.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Po zakończeniu rejestracji może upłynąć trochę czasu, dopóki odnalezione maszyny nie pojawią się w ramach migracji na serwer Azure Migrate. Jako że maszyny wirtualne są odnajdywane, wzrasta liczba **odnalezionych serwerów** .

![Odnalezione serwery](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności

Na maszynach, które mają zostać zmigrowane, należy zainstalować agenta usługi mobilności. Instalatory agentów są dostępne na urządzeniu replikacji. Możesz znaleźć odpowiedni Instalator i zainstalować agenta na każdej maszynie, która ma zostać zmigrowana. W tym celu wykonaj następujące czynności:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Znajdź Instalatora dla systemu operacyjnego i wersji maszyny. Przejrzyj [obsługiwane systemy operacyjne](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines). 
4. Skopiuj plik Instalatora na maszynę, którą chcesz zmigrować.
5. Upewnij się, że hasło zostało wygenerowane podczas wdrażania urządzenia.
    - Zapisz plik w tymczasowym pliku tekstowym na komputerze.
    - Hasło można uzyskać na urządzeniu replikacji. W wierszu polecenia Uruchom polecenie **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** , aby wyświetlić bieżące hasło.
    - Nie generuj ponownie hasła. Spowoduje to przerwanie łączności i konieczne będzie ponowne zarejestrowanie urządzenia replikacji.

> [!NOTE]
> W parametrze */platform* należy określić program *VMware* w przypadku migrowania maszyn wirtualnych VMware lub komputerów fizycznych.

### <a name="install-on-windows"></a>Instalowanie w systemie Windows

1. Wyodrębnij zawartość pliku Instalatora do folderu lokalnego (na przykład C:\Temp) na komputerze w następujący sposób:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Uruchom Instalatora usługi mobilności:
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalowanie w systemie Linux

1. Wyodrębnij zawartość Instalatora plik tar do folderu lokalnego (na przykład/tmp/MobSvcInstaller) na komputerze w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Uruchom skrypt Instalatora:
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikowanie maszyn

Teraz wybierz maszyny do migracji. 

> [!NOTE]
> Można replikować do 10 maszyn jednocześnie. Jeśli chcesz replikować więcej, a następnie Replikuj je jednocześnie w partiach 10.

1. Na **serwerach** Azure Migrate project > **Azure Migrate: Migracja serwera**, kliknij przycisk **replikacja**.

    ![Zrzut ekranu przedstawiający ekran Azure Migrate serwerów z przyciskiem replikacja wybrana w Azure Migrate: Migracja serwera w obszarze Narzędzia migracji.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. W obszarze **replikacja**> **Ustawienia źródła**  >  **są zwirtualizowane na maszynach?** wybierz opcję **niezwirtualizowane/inne**.
3. W **urządzeniu lokalnym** wybierz nazwę skonfigurowanego urządzenia Azure Migrate.
4. W obszarze **serwer przetwarzania** wybierz nazwę urządzenia replikacji.
6. W obszarze **poświadczenia gościa** wybierz konto fikcyjne utworzone wcześniej podczas [instalacji Instalatora replikacji](#download-the-replication-appliance-installer) , aby ręcznie zainstalować usługę mobilności (instalacja wypychana nie jest obsługiwana). Następnie kliknij przycisk **Dalej: maszyny wirtualne**.   

    ![Zrzut ekranu przedstawiający kartę Ustawienia źródła na ekranie replikacja z wyróżnionym polem poświadczenia gościa.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. W **Virtual Machines** w **ustawieniach migracji importu z oceny?** pozostaw ustawienie domyślne **nie, określ ustawienia migracji ręcznie**.
8. Sprawdź wszystkie maszyny wirtualne, które chcesz zmigrować. Następnie kliknij przycisk **Dalej: ustawienia docelowe**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
10. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Opcje dostępności** wybierz pozycję:
    -  Strefa dostępności służąca do przypinania zmigrowanej maszyny do określonej strefy dostępności w regionie. Użyj tej opcji, aby dystrybuować serwery tworzące wielowęzłową warstwę aplikacji między Strefy dostępności. W przypadku wybrania tej opcji należy określić strefę dostępności, która ma być używana dla każdej z wybranych maszyn na karcie obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności umożliwiający umieszczenie zmigrowanej maszyny w zestawie dostępności. Docelowa Grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Nie jest wymagana opcja nadmiarowości infrastruktury, jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla zmigrowanych maszyn.
    
12. W obszarze **typ szyfrowania dysku** wybierz:
    - Szyfrowanie — w spoczynku z kluczem zarządzanym przez platformę
    - Szyfrowanie — w spoczynku z kluczem zarządzanym przez klienta
    - Podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i klienta

   > [!NOTE]
   > Aby replikować maszyny wirtualne za pomocą CMK, należy [utworzyć zestaw szyfrowanie dysków](https://go.microsoft.com/fwlink/?linkid=2151800) w docelowej grupie zasobów. Obiekt zestawu szyfrowania dysku mapuje Managed Disks do Key Vault, który zawiera CMK do użycia na potrzeby SSE.
  
13. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

14. W obszarze **obliczenia** Sprawdź nazwę maszyny wirtualnej, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności (w przypadku wybrania w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej**: Jeśli korzystasz z zaleceń dotyczących oceny, rozmiar maszyny wirtualnej zostanie wyświetlony na liście rozwijanej rozmiar zalecane. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**.
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (Boot) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego.
    - **Strefa dostępności**: Określ strefę dostępności, która ma zostać użyta.
    - **Zestaw dostępności**: Określ zestaw dostępności, który ma być używany.

![Ustawienia obliczeń](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. W obszarze **dyski** Określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, a następnie wybierz typ dysku (standardowy dysk SSD lub dyski w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można aktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie**  >  **maszynami replikowanymi**. Ustawień nie można zmienić po rozpoczęciu replikacji.

## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po kliknięciu przycisku **Replikuj** rozpocznie się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.


Stan zadania można śledzić w powiadomieniach portalu.

Aby monitorować stan replikacji, należy kliknąć opcję **replikowanie serwerów** w **Azure Migrate: Migracja serwera**.
![Monitorowanie replikacji](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy wykonanie tej czynności co najmniej raz na każdym komputerze, przed przeprowadzeniem migracji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają w działaniu i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W obszarze serwery **celów migracji**  >    >  **Azure Migrate: Migracja serwera**, kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W Azure Migrate serwery > Project   >  **Azure Migrate: Migracja serwera**, kliknij przycisk **replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W obszarze **Migrowanie**  >  **Zamknij maszyny wirtualne i przeprowadź planowaną migrację bez utraty danych** wybierz pozycję **tak**  >  **OK**.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
    
    Uwaga: w przypadku migracji serwera fizycznego zaleca się przełączenie aplikacji w ramach okna migracji (nie Zezwalaj aplikacjom na akceptowanie żadnych połączeń), a następnie zainicjowanie migracji (serwer musi być uruchomiony, aby można było zsynchronizować pozostałe zmiany) przed ukończeniem migracji.

4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Wyłącza replikację maszyny lokalnej.
    - Usuwa maszynę z liczby **serwerów replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji maszyny.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
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
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](../security-center/security-center-just-in-time.md).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż migracji w chmurze](/azure/architecture/cloud-adoption/getting-started/migrate) w strukturze wdrażania w chmurze platformy Azure.
