---
title: Migrowanie maszyn jako serwera fizycznego na platformę Azure za pomocą usługi Azure Migrate.
description: W tym artykule opisano sposób migracji komputerów fizycznych na platformę Azure za pomocą usługi Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 1824fc6c7cbc0fd0390770027f4a15d9130139de
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535387"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Migrowanie komputerów jako serwerów fizycznych na platformę Azure

W tym artykule pokazano, jak migrować maszyny jako serwery fizyczne na platformę Azure przy użyciu narzędzia Migracji:Migracja serwera platformy Azure. Migrowanie maszyn przez traktowanie ich jako serwerów fizycznych jest przydatne w wielu scenariuszach:

- Migrowanie lokalnych serwerów fizycznych.
- Migrowanie maszyn wirtualnych zwirtualizowanych przez platformy takie jak Xen, KVM.
- Migrowanie maszyn wirtualnych z programem Hyper-V lub VMware, jeśli z jakiegoś powodu nie można użyć standardowego procesu migracji do migracji [funkcji Hyper-V](tutorial-migrate-hyper-v.md)lub [VMware.](server-migrate-overview.md)
- Migrowanie maszyn wirtualnych działających w chmurach prywatnych.
- Migrowanie maszyn wirtualnych działających w chmurach publicznych, takich jak Amazon Web Services (AWS) lub Google Cloud Platform (GCP).


Ten samouczek jest trzecim z serii, który pokazuje, jak ocenić i migrować serwery fizyczne na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotuj się do korzystania z platformy Azure z migracją usługi Azure:Migracja serwera.
> * Sprawdź wymagania dotyczące maszyn, które chcesz przeprowadzić migrację, i przygotuj komputer dla urządzenia replikacji migracji platformy Azure, które jest używane do odnajdywać i migrować maszyny na platformę Azure.
> * Dodaj narzędzie migracji serwera migracji platformy Azure w centrum migracji platformy Azure.
> * Konfigurowanie urządzenia replikacji.
> * Zainstaluj usługę mobilności na komputerach, które chcesz przeprowadzić migrację.
> * Włącz replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z instrukcjami dotyczącymi migracji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

[Przejrzyj](migrate-architecture.md) architekturę migracji.




## <a name="prepare-azure"></a>Przygotowywanie platformy Azure

Przygotuj platformę Azure do migracji za pomocą migracji serwera.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu migracji platformy Azure** | Twoje konto platformy Azure wymaga uprawnień programu Contributer lub Owner do utworzenia projektu.
**Weryfikowanie uprawnień dla konta platformy Azure** | Twoje konto platformy Azure potrzebuje uprawnień do tworzenia maszyny Wirtualnej i zapisu na dysku zarządzanym platformy Azure.


### <a name="assign-permissions-to-create-project"></a>Przypisywanie uprawnień do tworzenia projektu

1. W witrynie Azure portal otwórz subskrypcję i wybierz **pozycję Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Powinieneś mieć uprawnienia **współautora** lub **właściciela.**
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, współpracuj z właścicielem, aby przypisać tę rolę.


### <a name="assign-azure-account-permissions"></a>Przypisywanie uprawnień konta platformy Azure

Przypisz rolę współautora maszyny wirtualnej do konta platformy Azure. Zapewnia to uprawnienia do:

    - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
    - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
    - Zapis na dysku zarządzanym platformy Azure. 

### <a name="create-an-azure-network"></a>Tworzenie sieci platformy Azure

[Konfigurowanie](../virtual-network/manage-virtual-network.md#create-a-virtual-network) sieci wirtualnej platformy Azure (sieci wirtualnej). Podczas replikowania na platformie Azure maszyny wirtualne platformy Azure są tworzone i dołączane do sieci wirtualnej platformy Azure, które określisz podczas konfigurowania migracji.

## <a name="prepare-for-migration"></a>Przygotowanie do migracji

Aby przygotować się do migracji serwera fizycznego, należy zweryfikować ustawienia serwera fizycznego i przygotować się do wdrożenia urządzenia replikacji.

### <a name="check-machine-requirements-for-migration"></a>Sprawdzanie wymagań maszyny dla migracji

Upewnij się, że maszyny są zgodne z wymaganiami dotyczącymi migracji na platformę Azure. 

> [!NOTE]
> Podczas migracji komputerów fizycznych usługa Azure Migrate:Server Migration używa tej samej architektury replikacji co odzyskiwanie po awarii oparte na agentach w usłudze Azure Site Recovery, a niektóre składniki mają tę samą bazę kodu. Niektóre treści mogą zawierać łącze do dokumentacji odzyskiwania witryny.

1. [Sprawdź](migrate-support-matrix-physical-migration.md#physical-server-requirements) wymagania serwera fizycznego.
2. Sprawdź, czy maszyny lokalne, które replikujesz na platformie Azure, są zgodne z [wymaganiami dotyczącymi maszyny wirtualnej platformy Azure.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotowanie urządzenia do replikacji

Migracja usługi Azure:Migracja serwera używa urządzenia replikacji do replikacji maszyn na platformę Azure. Urządzenie replikacji uruchamia następujące składniki.

- **Serwer konfiguracji:** Serwer konfiguracji koordynuje komunikację między lokalną a platformą Azure i zarządza replikacją danych.
- **Serwer przetwarzania:** Serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji; optymalizuje go za pomocą buforowania, kompresji i szyfrowania i wysyła go do konta magazynu pamięci podręcznej na platformie Azure. 

Przygotuj się do wdrożenia urządzenia w następujący sposób:

- Należy przygotować komputer do obsługi urządzenia replikacji. [Przejrzyj](migrate-replication-appliance.md#appliance-requirements) wymagania maszyny. Urządzenie nie powinno być instalowane na komputerze źródłowym, który ma zostać zreplikowany.
- Urządzenie replikacji używa MySQL. Przejrzyj [opcje](migrate-replication-appliance.md#mysql-installation) instalacji MySQL na urządzeniu.
- Przejrzyj adresy URL platformy Azure wymagane dla urządzenia replikacji, aby uzyskać dostęp do chmur [publicznych](migrate-replication-appliance.md#url-access) i [rządowych.](migrate-replication-appliance.md#azure-government-url-access)
- Przejrzyj wymagania dotyczące dostępu [port] (migrate-replication-appliance.md#port-access) dla urządzenia replikacji.

## <a name="add-the-server-migration-tool"></a>Dodawanie narzędzia Migracja serwera

Skonfiguruj projekt migracji platformy Azure, a następnie dodaj do niego narzędzie Migracji serwera.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **Odnajdowanie oceniaj i migruj serwery**kliknij pozycję **Oceń i migruj serwery**.

    ![Odkrywanie i ocenianie serwerów](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
6. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
7. W obszarze **Szczegóły projektu** określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt, a następnie kliknij przycisk **Dalej**. Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. W **obszarze Wybierz narzędzie oceny**wybierz pozycję **Pomiń dodanie narzędzia oceny na razie** > **Dalej**.
9. W **narzędziu Wybierz migrację**wybierz pozycję **Migracja platformy Azure: Migracja** > serwera**dalej**.
10. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**
11. Po dodaniu narzędzia pojawia się w projekcie migracji platformy Azure >**narzędzia migracji** **serwerów** > .

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia replikacji. Aby skonfigurować urządzenie do migracji serwera fizycznego, należy pobrać plik instalatora urządzenia, a następnie uruchomić go na [przygotowanym komputerze](#prepare-a-machine-for-the-replication-appliance). Po zainstalowaniu urządzenia można zarejestrować go w usłudze Azure Migrate Server Migration.


### <a name="download-the-replication-appliance-installer"></a>Pobierz instalator urządzenia replikacji

1. W projekcie migracji platformy Azure > **serwery**w **programie Azure Migrate: Migracja serwera**kliknij przycisk **Odkryj**.

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij nie **zwirtualizowany/Inne**.
4. W **regionie docelowym**wybierz region platformy Azure, do którego chcesz przeprowadzić migrację komputerów.
5. Wybierz **pozycję Potwierdź, że regionem docelowym migracji jest nazwa regionu**.
6. Kliknij **pozycję Utwórz zasoby**. Spowoduje to utworzenie magazynu usługi Azure Site Recovery w tle.
    - Jeśli migracja została już skonfigurowana za pomocą migracji z usługą Azure Migrate Server, nie można skonfigurować opcji docelowej, ponieważ zasoby zostały skonfigurowane wcześniej.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Wszystkie kolejne migracje są do tego regionu.

7. W **obszarze Czy chcesz zainstalować nowe urządzenie replikacji?** wybierz pozycję **Instalowanie urządzenia replikacji**.
9. W **aplikacji Pobierz i zainstaluj oprogramowanie urządzenia replikacji**pobierz instalator urządzenia i klucz rejestracji. Aby zarejestrować urządzenie, należy wpisać klucz. Klucz jest ważny przez pięć dni po pobraniu.

    ![Dostawca pobierania](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Skopiuj plik instalacyjny urządzenia i plik klucza do komputera z systemem Windows Server 2016 utworzonego dla urządzenia.
11. Uruchom plik instalacyjny urządzenia replikacji, zgodnie z opisem w następnej procedurze. Po zakończeniu instalacji kreator konfiguracji urządzenia zostanie uruchomiony automatycznie (Kreator można również uruchomić ręcznie za pomocą skrótu cspsconfigtool utworzonego na pulpicie urządzenia). Karta Zarządzanie kontami kreatora służy do dodawania szczegółów konta do instalacji wypychanej usługi mobilności. W tym samouczku będziemy ręcznie instalować usługę mobilności na komputerach do replikacji, więc utwórz fikcyjne konto w tym kroku i kontynuuj.

12. Po ponownym uruchomieniu urządzenia po skonfigurowaniu w obszarze **Discover maszyny**wybierz nowe urządzenie w obszarze Wybierz **serwer konfiguracji**i kliknij przycisk **Finalizuj rejestrację**. Finalizowanie rejestracji wykonuje kilka zadań końcowych w celu przygotowania urządzenia replikacji.

    ![Finalizowanie rejestracji](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Może upłynąć trochę czasu po sfinalizowaniu rejestracji, dopóki nie pojawią się wykryte maszyny w migracji serwera migracji programu Azure Migrate Server. Po wykryciu maszyn wirtualnych liczba **wykrytych serwerów** rośnie.

![Odnalezione serwery](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności

Na komputerach, które chcesz przeprowadzić migrację, należy zainstalować agenta usługi mobilności. Instalatorzy agenta są dostępni na urządzeniu replikacji. Znajdziesz odpowiedniego instalatora i zainstaluj agenta na każdym komputerze, który chcesz przeprowadzić migrację. W tym celu wykonaj następujące czynności:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **pozycji %ProgramData%\ASR\home\svsystems\pushinstallsvc\repozytorium**.
3. Znajdź instalator dla systemu operacyjnego i wersji komputera. Przejrzyj [obsługiwane systemy operacyjne](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Skopiuj plik instalatora na komputer, który chcesz przeprowadzić migrację.
5. Upewnij się, że masz hasło, które zostało wygenerowane podczas wdrażania urządzenia.
    - Przechowuj plik w tymczasowym pliku tekstowym na komputerze.
    - Hasło można uzyskać na urządzeniu replikacji. W wierszu polecenia uruchom polecenie **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v,** aby wyświetlić bieżące hasło.
    - Nie regeneruj hasła. Spowoduje to przerwanie łączności i trzeba będzie ponownie zarejestrować urządzenie replikacji.


### <a name="install-on-windows"></a>Instalowanie w systemie Windows

1. Wyodrębnij zawartość pliku instalatora do folderu lokalnego (na przykład C:\Temp) na komputerze w następujący sposób:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Uruchom Instalator usługi mobilności:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Zarejestruj agenta przy za pomocą urządzenia replikacji:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Instalowanie w systemie Linux

1. Wyodrębnij zawartość tarball instalatora do folderu lokalnego (na przykład /tmp/MobSvcInstaller) na komputerze, w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Uruchom skrypt instalatora:
    ```
    sudo ./install -r MS -q
    ```
3. Zarejestruj agenta przy za pomocą urządzenia replikacji:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Replikowanie maszyn

Teraz wybierz maszyny do migracji. 

> [!NOTE]
> Można replikować do 10 maszyn razem. Jeśli chcesz replikować więcej, a następnie replikuj je jednocześnie w partiach po 10.

1. W projekcie migracji platformy Azure > **serwery**, **Migracja platformy Azure: Migracja serwera**, kliknij przycisk **Replikuj**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. W **obszarze Replika**, > **ustawienia** > źródła**Czy twoje maszyny są zwirtualizowane?**, wybierz opcję Nie **zwirtualizowany/Inne**.
3. W **urządzeniu lokalnym**wybierz nazwę skonfigurowanego urządzenia migracji platformy Azure.
4. W **obszarze Serwer przetwarzania**wybierz nazwę urządzenia replikacji.
6. W **poświadczeń gościa**można określić fikcyjne konto, które będzie używane do ręcznego instalowania usługi mobilności (instalacja wypychania nie jest obsługiwana w języku fizycznym). Następnie kliknij **przycisk Dalej: Maszyny wirtualne**.

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. W **obszarze Maszyny wirtualne**w obszarze **Importowanie ustawień migracji z oceny?**, pozostaw ustawienie domyślne **Nie, określę ustawienia migracji ręcznie**.
8. Sprawdź każdą maszynę wirtualną, którą chcesz przeprowadzić migrację. Następnie kliknij **przycisk Dalej: Ustawienia celu**.

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
10. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.
11. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. W obszarze **Obliczenia** sprawdź nazwę, rozmiar, typ dysku systemu operacyjnego i zestaw dostępności maszyny wirtualnej. Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny Wirtualnej:** Domyślnie migracja serwera migracji platformy Azure wybiera rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**. 
    - **Dysk systemu operacyjnego**: Określ dysk systemu operacyjnego (rozruchowego) maszyny Wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. 
    - **Zestaw dostępności:** Jeśli maszyna wirtualna powinna znajdować się w zestawie dostępności platformy Azure po migracji, określ zestaw. Zestaw musi znajdować się w docelowej grupie zasobów określonej dla migracji.

    ![Ustawienia obliczeń](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. W **obszarze Dyski**określ, czy dyski maszyn wirtualnych mają być replikowane na platformę Azure, i wybierz typ dysku (standardowy dysk SSD/HDD lub dyski zarządzane w wersji premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem replikacji, **Zarządzanie maszynami** > **replikacji**. Ustawień nie można zmienić po rozpoczęciu replikacji.



## <a name="track-and-monitor"></a>Śledzenie i monitorowanie

- Po **kliknięciu przycisku Replikacja** rozpoczyna się zadanie replikacji początkowej. 
- Po pomyślnym zakończeniu zadania replikacji początkowej maszyny rozpoczynają replikację początkową na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja delta. Przyrostowe zmiany na dyskach lokalnych są okresowo replikowane na dyski repliki na platformie Azure.


Stan zadania można śledzić w powiadomieniach portalu.

Stan replikacji można monitorować, klikając opcję **Replikowanie serwerów** w **programie Azure Migrate: Server Migration**.
![Monitorowanie replikacji](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej


Po rozpoczęciu replikacji różnicowej można uruchomić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zaleca się, aby zrobić to co najmniej raz dla każdego komputera, przed migracją.

- Uruchomienie migracji testowej sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny lokalne, które pozostają operacyjne i kontynuują replikację. 
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu replikowanych danych (zwykle migrując do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Za pomocą replikowanego testu maszyny Wirtualnej platformy Azure można sprawdzić poprawność migracji, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wykonaj migrację testową w następujący sposób:


1. W **celach** > migracji**Serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij przycisk **Testuj zmigrowane serwery**.

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po sprawdzeniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację komputerów lokalnych.

1. W projekcie migracji platformy Azure > **serwery** > **Usługi Azure Migrate: Migracja serwera**kliknij pozycję **Replikowanie serwerów**.

    ![Replikowanie serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. W **obszarze Migracja** > Zamknij**maszyny wirtualne i wykonaj planowaną migrację bez utraty danych**, wybierz tak **OK** > **OK**.
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie**

    Uwaga: W przypadku migracji serwera fizycznego zaleca się, aby aplikacja została wyłączona w oknie migracji (nie pozwól, aby aplikacje akceptowały żadne połączenia), a następnie zainicjować migrację (serwer musi być uruchomiony, aby pozostałe zmiany mogły zostać zsynchronizowane) przed zakończeniem migracji.

4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Śledź zadanie w powiadomieniach platformy Azure.
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie **Maszyny wirtualne**.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną > **Zatrzymaj migrację**. Spowoduje to wykonanie następujących czynności:
    - Zatrzymuje replikację komputera lokalnego.
    - Usuwa komputer z licznika **serwerów replikacji** w usłudze Azure Migrate: Server Migration.
    - Czyści informacje o stanie replikacji dla urządzenia.
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
    - Rozważ wdrożenie usługi [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview), aby monitorować użycie zasobu i wydatki.


## <a name="next-steps"></a>Następne kroki

Zbadaj [proces migracji](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) do chmury w ramach wdrażania chmury azure.
