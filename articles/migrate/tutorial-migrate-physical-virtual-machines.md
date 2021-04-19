---
title: Migrowanie maszyn jako serwerów fizycznych na platformę Azure przy Azure Migrate.
description: W tym artykule opisano sposób migrowania maszyn fizycznych na platformę Azure przy użyciu Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: 1563543dec0a27094c00e446a205e94535e54229
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713530"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrowanie maszyn jako serwerów fizycznych na platformę Azure

W tym artykule pokazano, jak przeprowadzić migrację maszyn jako serwerów fizycznych na platformę Azure przy użyciu narzędzia Azure Migrate: Server Migration. Migrowanie maszyn przez traktowanie ich jako serwerów fizycznych jest przydatne w wielu scenariuszach:

- Migrowanie lokalnych serwerów fizycznych.
- Migrowanie maszyn wirtualnych zwirtualizowanych według platform, takich jak Xen, KVM.
- Migruj maszyny wirtualne funkcji [Hyper-V](tutorial-migrate-hyper-v.md)lub VMware, jeśli z jakiegoś powodu nie możesz użyć standardowego procesu migracji dla funkcji Hyper-V lub [migracji VMware.](server-migrate-overview.md)
- Migrowanie maszyn wirtualnych działających w chmurach prywatnych.
- Migrowanie maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP).


Ten samouczek jest trzecim z serii, który pokazuje, jak oceniać i migrować serwery fizyczne na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowanie do korzystania z platformy Azure z Azure Migrate: Migracja serwera.
> * Sprawdź wymagania dotyczące maszyn, które chcesz migrować, i przygotuj maszynę dla urządzenia Azure Migrate replikacji, które jest używane do odnajdywania i migrowania maszyn na platformę Azure.
> * Dodaj narzędzie Azure Migrate: Server Migration w centrum Azure Migrate serwera.
> * Skonfiguruj urządzenie replikacji.
> * Zainstaluj usługa mobilności na maszynach, które chcesz migrować.
> * Włącz replikację.
> * Uruchom migrację testów, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z instrukcjami dotyczącymi Azure Migrate.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

[Przejrzyj](./agent-based-migration-architecture.md) architekturę migracji.

## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Przygotowanie platformy Azure do migracji za pomocą Azure Migrate: Migracja serwera.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu usługi Azure Migrate** | Aby utworzyć nowy projekt, Twoje konto platformy Azure musi mieć uprawnienia Współautor [lub Właściciel.](./create-manage-projects.md)
**Weryfikowanie uprawnień dla konta platformy Azure** | Konto platformy Azure musi mieć uprawnienia do tworzenia maszyny wirtualnej i zapisu na dysku zarządzanym platformy Azure.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure Portal otwórz daną subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W **sprawdź dostęp**, znajdź odpowiednie konto, a następnie kliknij go, aby wyświetlić uprawnienia.
3. Musisz mieć uprawnienia **Współautor** **lub** Właściciel.
    - Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, poproś właściciela o przypisanie roli.


### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę Współautor maszyny wirtualnej do konta platformy Azure. Zapewnia to uprawnienia do:

- Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
- Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
- Zapis na dysku zarządzanym platformy Azure. 

### <a name="create-an-azure-network"></a>Tworzenie sieci platformy Azure

[Konfigurowanie sieci](../virtual-network/manage-virtual-network.md#create-a-virtual-network) wirtualnej platformy Azure. Podczas replikacji na platformę Azure maszyny wirtualne platformy Azure są tworzone i przyłączone do sieci wirtualnej platformy Azure, która jest określana podczas konfigurowanie migracji.

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

Aby przygotować się do migracji serwera fizycznego, należy zweryfikować ustawienia serwera fizycznego i przygotować się do wdrożenia urządzenia replikacji.

### <a name="check-machine-requirements-for-migration"></a>Sprawdzanie wymagań dotyczących maszyny na potrzeby migracji

Upewnij się, że maszyny spełniają wymagania dotyczące migracji na platformę Azure. 

> [!NOTE]
> Podczas migrowania maszyn fizycznych program Azure Migrate: migracja serwera używa tej samej architektury replikacji co odzyskiwanie po awarii oparte na agentach w usłudze Azure Site Recovery, a niektóre składniki współużytkują tę samą bazę kodu. Część zawartości może zawierać link do Site Recovery dokumentacji.

1. [Sprawdź](migrate-support-matrix-physical-migration.md#physical-server-requirements) wymagania dotyczące serwera fizycznego.
2. Sprawdź, czy maszyny lokalne replikowane na platformę Azure są zgodne z wymaganiami [maszyny wirtualnej platformy Azure.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)
3. Przed migracją maszyn wirtualnych na platformę Azure należy wprowadzić pewne zmiany.
    - W przypadku niektórych systemów operacyjnych Azure Migrate te zmiany są automatycznie wprowadzane. 
    - Przed rozpoczęciem migracji należy wprowadzić te zmiany. W przypadku migrowania maszyny wirtualnej przed zmianą maszyna wirtualna może nie zostać uruchomiony na platformie Azure. Przejrzyj [zmiany w](prepare-for-migration.md#windows-machines) [systemach Windows i Linux,](prepare-for-migration.md#linux-machines) które należy wprowadzić.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotowywanie maszyny dla urządzenia replikacji

Azure Migrate: migracja serwera używa urządzenia replikacji do replikowania maszyn na platformę Azure. Na urządzeniu replikacji są uruchamiane następujące składniki.

- **Serwer konfiguracji:** serwer konfiguracji koordynuje komunikację między środowiskiem lokalnym i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania:** serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje je za pomocą buforowania, kompresji i szyfrowania oraz wysyła je do konta magazynu pamięci podręcznej na platformie Azure. 

Przygotuj się do wdrożenia urządzenia w następujący sposób:

- Należy przygotować maszynę do hostowania urządzenia replikacji. [Przejrzyj](migrate-replication-appliance.md#appliance-requirements) wymagania dotyczące maszyny.
- Urządzenie replikacji używa programu MySQL. Zapoznaj się [z opcjami](migrate-replication-appliance.md#mysql-installation) instalowania programu MySQL na urządzeniu.
- Przejrzyj adresy URL platformy Azure wymagane przez urządzenie replikacji do uzyskiwania dostępu do [chmur publicznych](migrate-replication-appliance.md#url-access) [i](migrate-replication-appliance.md#azure-government-url-access) dla instytucji rządowych.
- Przejrzyj [wymagania dotyczące](migrate-replication-appliance.md#port-access) dostępu do portów dla urządzenia replikacji.

> [!NOTE]
> Urządzenia replikacji nie należy instalować na maszynie źródłowej, którą chcesz replikować, ani na urządzeniu Azure Migrate odnajdywania i oceny, które być może wcześniej zainstalowano.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia replikacji. Aby skonfigurować urządzenie do migracji serwera fizycznego, pobierz plik instalatora dla urządzenia, a następnie uruchom go na [przygotowanej maszynie.](#prepare-a-machine-for-the-replication-appliance) Po zainstalowaniu urządzenia należy je zarejestrować za pomocą Azure Migrate: Migracja serwera.


### <a name="download-the-replication-appliance-installer"></a>Pobieranie instalatora urządzenia replikacji

1. W projekcie Azure Migrate serwery > **,** w **Azure Migrate: Migracja serwera** kliknij pozycję **Odnajdz.**

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. Na **stronie Odnajdywanie maszyn** Czy maszyny są  >  **zwirtualizowane?** kliknij pozycję Nie **zwirtualizowane/inne.**
3. W **obszarze Region docelowy** wybierz region platformy Azure, do którego chcesz migrować maszyny.
4. Wybierz **pozycję Potwierdź, że region docelowy migracji ma nazwę regionu**.
5. Kliknij **pozycję Utwórz zasoby.** Powoduje to utworzenie Azure Site Recovery magazynu danych w tle.
    - Jeśli już skonfigurowano migrację za pomocą Azure Migrate: Migracja serwera, nie można skonfigurować opcji docelowej, ponieważ zasoby zostały wcześniej skonfigurowane.    
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje są do tego regionu. 
    > [!NOTE]
    > W przypadku wyboru prywatnego punktu końcowego jako metody łączności dla projektu Azure Migrate podczas jego tworzenia magazyn usługi Recovery Services zostanie również skonfigurowany do łączności z prywatnym punktem końcowym. Upewnij się, że prywatne punkty końcowe są dostępne z urządzenia replikacji. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. Na **stronie Czy chcesz zainstalować nowe urządzenie replikacji?** wybierz pozycję Zainstaluj urządzenie **replikacji.**
7. W **chmurze Pobierz i zainstaluj oprogramowanie urządzenia replikacji** pobierz instalator urządzenia oraz klucz rejestracji. Klucz jest potrzebny do zarejestrowania urządzenia. Klucz jest ważny przez pięć dni po jego pobraniu.

    ![Dostawca pobierania](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Skopiuj plik instalacyjny urządzenia i plik klucza na maszynę z systemem Windows Server 2016 utworzoną dla tego urządzenia.
9. Po zakończeniu instalacji kreator konfiguracji urządzenia zostanie uruchomiony automatycznie (możesz również uruchomić kreatora ręcznie przy użyciu skrótu cspsconfigtool utworzonego na pulpicie urządzenia). Użyj karty Zarządzaj kontami kreatora, aby dodać szczegóły konta do użycia na użytek instalacji wypychaowej usługa mobilności. W tym samouczku ręcznie zainstalujemy usługę Mobility Service na źródłowych maszyn wirtualnych do replikacji, więc w tym kroku utworzymy fikcyjne konto i kontynuujemy. Możesz podać następujące szczegóły dotyczące tworzenia fikcyjnego konta — "gość" jako przyjazną nazwę, "username" jako nazwę użytkownika i "hasło" jako hasło do konta. To fikcyjne konto będzie na etapie Włączania replikacji. 

10. Po ponownym uruchomieniu urządzenia po instalacji w opcji Odnajdowanie maszyn wybierz nowe urządzenie w chmurze **Wybierz** serwer konfiguracji, a następnie kliknij pozycję **Finalizuj rejestrację.** Finalizuj rejestrację wykonuje kilka końcowych zadań w celu przygotowania urządzenia replikacji.

    ![Finalizuj rejestrację](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Po sfinalizowaniu rejestracji może miej trochę czasu, aż odnalezione maszyny pojawią się w Azure Migrate: Migracja serwera. W przypadku odnalezionych maszyn wirtualnych liczba **odnalezionych** serwerów rośnie.

![Odnalezione serwery](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności

Na maszynach, które mają zostać zmigrowane, należy zainstalować usługa mobilności agenta. Instalatory agenta są dostępne na urządzeniu replikacji. Znajdź odpowiedniego instalatora i zainstaluj agenta na każdej maszynie, którą chcesz zmigrować. W tym celu wykonaj następujące czynności:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **folderu %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.**
3. Znajdź instalatora dla systemu operacyjnego i wersji komputera. Zapoznaj się [z obsługiwanymi systemami operacyjnymi.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) 
4. Skopiuj plik instalatora na maszynę, którą chcesz zmigrować.
5. Upewnij się, że masz hasło, które zostało wygenerowane podczas wdrażania urządzenia.
    - Przechowaj plik w tymczasowym pliku tekstowym na maszynie.
    - Hasło można uzyskać na urządzeniu replikacji. W wierszu polecenia uruchom **polecenieC:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v,** aby wyświetlić bieżące hasło.
    - Nie wygeneruj ponownie hasła. Spowoduje to przerwę w łączności i będzie trzeba ponownie zarejestrować urządzenie replikacji.

> [!NOTE]
> W *parametrze /Platform* należy określić program *VMware* w przypadku migrowania maszyn wirtualnych VMware lub maszyn fizycznych.

### <a name="install-on-windows"></a>Instalowanie w systemie Windows

1. Wyodrębnij zawartość pliku instalatora do folderu lokalnego (na przykład C:\Temp) na maszynie w następujący sposób:

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

1. Wyodrębnij zawartość pliku tarball instalatora do folderu lokalnego (na przykład /tmp/MobSvcInstaller) na maszynie w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Uruchom skrypt instalatora:
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
> Można replikować maksymalnie 10 maszyn jednocześnie. Jeśli musisz replikować więcej, replikuj je jednocześnie w partiach po 10.

1. W projekcie Azure Migrate serwery **>,** **Azure Migrate: Migracja serwera** kliknij pozycję **Replikuj.**

    ![Zrzut ekranu Azure Migrate — Serwery przedstawiający przycisk Replikuj wybrany w obszarze Azure Migrate: Migracja serwera w obszarze Narzędzia migracji.](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Na **stronie Replikuj**> **źródłową**  >  **Czy maszyny są zwirtualizowane?** wybierz pozycję Nie **zwirtualizowane/inne.**
3. W **chmurze Urządzenie** lokalne wybierz nazwę Azure Migrate, które zostało przez Ciebie ustawione.
4. Na **serwerze przetwarzania** wybierz nazwę urządzenia replikacji.
5. W **opcji Poświadczenia gościa** wybierz fikcyjne konto [](#download-the-replication-appliance-installer) utworzone wcześniej podczas konfigurowania instalatora replikacji, aby ręcznie zainstalować usługa mobilności (instalacja wypychana nie jest obsługiwana). Następnie kliknij przycisk **Dalej: Maszyny wirtualne.**   

    ![Zrzut ekranu przedstawiający kartę Ustawienia źródła na ekranie Replikowanie z wyróżniowanym polem Poświadczenia gościa.](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

6. W **Virtual Machines**, w opcji Importuj ustawienia migracji z **oceny?** pozostaw ustawienie domyślne Nie. Określę ustawienia migracji **ręcznie.**
7. Sprawdź każdą maszynę wirtualną, którą chcesz zmigrować. Następnie kliknij przycisk **Dalej: Ustawienia docelowe.**

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


8. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
9. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.   
10. Na  **stronie Konto magazynu pamięci** podręcznej zachowaj domyślną opcję używania konta magazynu pamięci podręcznej, które jest automatycznie tworzone dla projektu. Użyj listy rozwijanej, jeśli chcesz określić inne konto magazynu do użycia jako konto magazynu pamięci podręcznej na użytek replikacji. <br/> 
    > [!NOTE]
    >
    > - Jeśli jako metodę łączności dla projektu usługi Azure Migrate wybrano prywatny punkt końcowy, przyznaj magazynowi usługi Recovery Services dostęp do konta magazynu pamięci podręcznej. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Aby replikować przy użyciu usługi ExpressRoute z prywatną komunikacji równorzędnej, utwórz prywatny punkt końcowy dla konta magazynu pamięci podręcznej. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
  
11. W **opcje dostępności**, wybierz:
    -  Strefa dostępności do przypiętych migrowanych maszyn do określonej strefy dostępności w regionie. Ta opcja umożliwia dystrybucję serwerów tworzących warstwę aplikacji z wieloma węzłami między Strefy dostępności. Jeśli wybierzesz tę opcję, musisz określić strefę dostępności do użycia dla każdej wybranej maszyny na karcie Obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności, aby umieścić zmigrowany komputer w zestawie dostępności. Docelowa grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla migrowanych maszyn, nie jest wymagana żadna z tych opcji nadmiarowości infrastruktury.
    
12. W **typie Szyfrowanie dysków** wybierz pozycję:
    - Szyfrowanie danych spoczynku przy użyciu klucza zarządzanego przez platformę
    - Szyfrowanie danych spoczynku przy użyciu klucza zarządzanego przez klienta
    - Podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i zarządzanych przez klienta

   > [!NOTE]
   > Aby replikować maszyny wirtualne za pomocą klucza cmk, należy utworzyć zestaw szyfrowania dysków [w](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) docelowej grupie zasobów. Obiekt zestawu szyfrowania dysków mapuje Dyski zarządzane na Key Vault, który zawiera klucz CMK do użycia na użytek funkcji SSE.
  
13. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

14. W **części Obliczenia** przejrzyj nazwę, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności maszyny wirtualnej (jeśli wybrano w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej:** jeśli używasz zaleceń dotyczących oceny, na liście rozwijanej Rozmiar maszyny wirtualnej jest przedstawiany zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**.
    - **Dysk systemu operacyjnego:** określ dysk systemu operacyjnego (rozruchowy) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego.
    - **Strefa dostępności:** określ strefę dostępności do użycia.
    - **Zestaw dostępności:** określ zestaw dostępności do użycia.

![Ustawienia obliczeniowe](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. W **witrynie** Dyski określ, czy dyski maszyny wirtualnej mają być replikowane do platformy Azure, i wybierz typ dysku (dyski SSD/HDD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem replikacji. Zarządzanie  >  **replikowaniami maszyn.** Ustawień nie można zmienić po rozpoczęciu replikacji.

## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po kliknięciu **przycisku Replikuj** rozpoczyna się zadanie uruchamiania replikacji. 
- Po pomyślnym zakończeniu zadania uruchamiania replikacji maszyny rozpoczynają replikację początkową na platformę Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany na dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.


Stan zadania można śledzić w powiadomieniach portalu.

Stan replikacji można monitorować, klikając pozycję **Replikowanie serwerów** **w Azure Migrate: Migracja serwera.**
![Monitorowanie replikacji](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po uruchomieniu replikacji różnicowej można uruchomić migrację testowa dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się przeprowadzenie tej migracji co najmniej raz dla każdej maszyny przed jej migracją.

- Uruchomienie testu migracji sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które nadal działają, i kontynuować replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zazwyczaj migrując do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Replikowanej testowej maszyny wirtualnej platformy Azure można użyć do weryfikowania migracji, testowania aplikacji i rozwiązania wszelkich problemów przed pełną migracją.

Przetestuj migrację w następujący sposób:


1. W **Cele migracji**  >  **serwery**  >  **Azure Migrate: migracja serwera**, kliknij przycisk Test **zmigrowanych serwerów.**

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po sprawdzeniu, czy migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację maszyn lokalnych.

1. W projekcie Azure Migrate serwery > Azure Migrate: migracja  >  **serwera kliknij** pozycję **Replikowanie serwerów.**

    ![Replikowanie serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. Na stronie Migruj Zamknij maszyny wirtualne i   >  **wykonaj planowaną migrację bez utraty danych** wybierz **pozycję Tak**  >  **OK.**
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**
    
    Uwaga: W przypadku migracji serwera fizycznego zaleca się, aby aplikacja nie działała w ramach okna migracji (nie pozwól aplikacjom akceptować żadnych połączeń), a następnie zainicjować migrację (serwer musi być uruchomiony, aby pozostałe zmiany można było zsynchronizować) przed zakończeniem migracji.

4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną, aby > **zatrzymać migrację.** Spowoduje to wykonanie następujących czynności:
    - Zatrzymuje replikację maszyny lokalnej.
    - Usuwa maszynę z liczby serwerów **replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji dla maszyny.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure z systemem [Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Przetnij ruch do zmigrowanych wystąpień maszyn wirtualnych platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i ogranicz dostęp do ruchu przychodzącego za [pomocą Azure Security Center — administrowanie dokładnie na czas.](../security-center/security-center-just-in-time.md)
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż po migracji do](/azure/architecture/cloud-adoption/getting-started/migrate) chmury w usłudze Azure Cloud Adoption Framework.
