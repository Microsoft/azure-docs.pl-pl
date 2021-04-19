---
title: Odnajdywanie, ocenianie i migrowanie Google Cloud Platform maszyn wirtualnych na platformę Azure
description: W tym artykule opisano sposób migrowania maszyn wirtualnych GCP na platformę Azure przy użyciu Azure Migrate.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 9965557115206cd512450d3411a70390f2249153
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713521"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Odnajdywanie, ocenianie i migrowanie Google Cloud Platform wirtualnych na platformę Azure

W tym samouczku pokazano, jak odnajdywać, oceniać i migrować maszyny wirtualne platformy Google Cloud Platform (GCP) do maszyn wirtualnych platformy Azure przy użyciu narzędzi Azure Migrate: Server Assessment i Azure Migrate: Narzędzia migracji serwera.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Sprawdź wymagania wstępne migracji.
> * Przygotowywanie zasobów platformy Azure za pomocą Azure Migrate: Migracja serwera. Skonfiguruj uprawnienia dla konta platformy Azure i zasobów do pracy z Azure Migrate.
> * Przygotowywanie wystąpień maszyn wirtualnych platformy GCP do migracji.
> * Dodaj narzędzie Azure Migrate: Server Migration w centrum Azure Migrate serwera.
> * Skonfiguruj urządzenie replikacji i wd wdrażaj serwer konfiguracji.
> * Zainstaluj usługa mobilności na maszyn wirtualnych GCP, które chcesz migrować.
> * Włączanie replikacji maszyn wirtualnych
> * Śledzenie i monitorowanie stanu replikacji. 
> * Uruchom migrację testów, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację na platformę Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="discover-and-assess"></a>Odnajdywanie i ocenianie

Przed przeprowadzeniem migracji na platformę Azure zalecamy przeprowadzenie oceny odnajdywania i migracji maszyn wirtualnych. Ta ocena pomaga w odpowiednim rozmiarze maszyn wirtualnych GCP do migracji na platformę Azure i oszacowanie potencjalnych kosztów uruchomienia platformy Azure.

Skonfiguruj ocenę w następujący sposób:

1. Postępuj zgodnie z [samouczkiem,](./tutorial-discover-gcp.md) aby skonfigurować platformę Azure i przygotować maszyny wirtualne platformy GCP do oceny. Należy pamiętać, że:

    - Azure Migrate używa uwierzytelniania za pomocą hasła podczas odnajdywania wystąpień maszyn wirtualnych platformy GCP. Wystąpienia GCP nie obsługują domyślnie uwierzytelniania za pomocą hasła. Przed odnajdywaniem należy włączyć uwierzytelnianie za pomocą hasła.
        - W przypadku maszyn z systemem Windows zezwalaj na port WinRM 5985 (HTTP). Umożliwia to zdalne wywołania usługi WMI.
        - W przypadku maszyn z systemem Linux:
            1. Zaloguj się do każdej maszyny z systemem Linux.
            2. Otwórz plik sshd_config: vi /etc/ssh/sshd_config
            3. W pliku znajdź wiersz **PasswordAuthentication** i zmień wartość na **tak.**
            4. Zapisz plik i zamknij go. Uruchom ponownie usługę SSH.
    - Jeśli do odnajdywania maszyn wirtualnych z systemem Linux używasz użytkownika głównego, upewnij się, że na tych maszynach wirtualnych jest dozwolone logowanie do konta root.
        1. Zaloguj się do każdej maszyny z systemem Linux
        2. Otwórz plik sshd_config: vi /etc/ssh/sshd_config
        3. W pliku znajdź wiersz **PermitRootLogin** i zmień wartość na **tak.**
        4. Zapisz plik i zamknij go. Uruchom ponownie usługę SSH.

2. Następnie wykonaj czynności z tego [samouczka,](./tutorial-assess-gcp.md) aby skonfigurować Azure Migrate projektu i urządzenia w celu odnajdywania i oceniania maszyn wirtualnych GCP.

Mimo że zalecamy wypróbowanie oceny, przeprowadzenie oceny nie jest krokiem obowiązkowym, aby można było migrować maszyny wirtualne.



## <a name="prerequisites"></a>Wymagania wstępne 

- Upewnij się, że na maszynach wirtualnych GCP, które chcesz migrować, jest uruchomiona obsługiwana wersja systemu operacyjnego. Na potrzeby migracji maszyny wirtualne GCP są traktowane jak maszyny fizyczne. Zapoznaj się z [obsługiwanymi systemami operacyjnymi i wersjami jądra dla](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) przepływu pracy migracji serwera fizycznego. Możesz użyć standardowych poleceń, takich *jak hostnamectl* lub *uname -a,* aby sprawdzić wersje systemu operacyjnego i jądra dla maszyn wirtualnych z systemem Linux.  Zalecamy przeprowadzenie migracji testowej w celu zweryfikowania, czy maszyna wirtualna działa zgodnie z oczekiwaniami, przed przystąpieniem do rzeczywistej migracji.
- Upewnij się, że maszyny wirtualne platformy GCP są zgodne z obsługiwanymi [konfiguracjami](./migrate-support-matrix-physical-migration.md#physical-server-requirements) migracji na platformę Azure.
- Sprawdź, czy maszyny wirtualne platformy GCP replikowane na platformie Azure są zgodne z wymaganiami [maszyny wirtualnej platformy Azure.](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)
- Przed migracją maszyn wirtualnych na platformę Azure należy wprowadzić pewne zmiany.
    - W przypadku niektórych systemów operacyjnych Azure Migrate automatycznie wprowadza te zmiany.
    - Przed rozpoczęciem migracji należy wprowadzić te zmiany. Jeśli migrujesz maszynę wirtualną przed zmianą, maszyna wirtualna może nie zostać uruchomiony na platformie Azure.
Przejrzyj [zmiany w](prepare-for-migration.md#windows-machines) [systemach Windows i Linux,](prepare-for-migration.md#linux-machines) które należy wprowadzić.

### <a name="prepare-azure-resources-for-migration"></a>Przygotowywanie zasobów platformy Azure do migracji

Przygotuj platformę Azure do migracji za Azure Migrate: narzędzie do migracji serwera.

**Zadanie** | **Szczegóły**
--- | ---
**Tworzenie projektu usługi Azure Migrate** | Twoje konto platformy Azure musi mieć uprawnienia Współautor lub [Właściciel, aby utworzyć nowy projekt.](./create-manage-projects.md)
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

[Konfigurowanie sieci](../virtual-network/manage-virtual-network.md#create-a-virtual-network) wirtualnej platformy Azure. Podczas replikacji na platformę Azure tworzone maszyny wirtualne platformy Azure są przyłączone do sieci wirtualnej platformy Azure, która jest określana podczas konfigurowanie migracji.

## <a name="prepare-gcp-instances-for-migration"></a>Przygotowywanie wystąpień GCP do migracji

Aby przygotować się do migracji platformy GCP na platformę Azure, należy przygotować i wdrożyć urządzenie replikacji do migracji.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Przygotowywanie maszyny dla urządzenia replikacji

Azure Migrate: migracja serwera używa urządzenia replikacji do replikowania maszyn na platformę Azure. Na urządzeniu replikacji są uruchamiane następujące składniki.

- **Serwer konfiguracji:** serwer konfiguracji koordynuje komunikację między maszynami wirtualnych platformy GCP i platformą Azure oraz zarządza replikacją danych.
- **Serwer przetwarzania:** serwer przetwarzania działa jako brama replikacji. Odbiera dane replikacji, optymalizuje je za pomocą buforowania, kompresji i szyfrowania oraz wysyła je do konta magazynu pamięci podręcznej na platformie Azure.

Przygotuj się do wdrożenia urządzenia w następujący sposób:

- Skonfiguruj oddzielną maszynę wirtualną GCP do hostować urządzenie replikacji. To wystąpienie musi mieć uruchomiony system Windows Server 2012 R2 lub Windows Server 2016. [Przejrzyj](./migrate-replication-appliance.md#appliance-requirements) wymagania dotyczące sprzętu, oprogramowania i sieci dla urządzenia.
- Urządzenia nie należy instalować na źródłowej maszynie wirtualnej, którą chcesz replikować, ani na urządzeniu Azure Migrate odnajdywania i oceny, które być może wcześniej zainstalowano. Należy ją wdrożyć na innej maszynie wirtualnej.
- Źródłowe maszyny wirtualne GCP, które mają być migrowane, powinny mieć połączenie sieciowe z urządzeniem replikacji. Skonfiguruj niezbędne reguły zapory, aby to umożliwić. Zaleca się wdrożenie urządzenia replikacji w tej samej sieci VPC co źródłowe maszyny wirtualne do zmigrowania. Jeśli urządzenie replikacji musi być w innej sieci VPN, sieci VPN muszą być połączone za pośrednictwem komunikacji równorzędnej VPC.
- Źródłowe maszyny wirtualne GCP komunikują się z urządzeniem replikacji na portach HTTPS 443 (aranżacja kanału sterowania) i TCP 9443 (transport danych) dla ruchu przychodzącego na temat zarządzania replikacją i transferu danych replikacji. Z kolei urządzenie replikacji aranżuje i wysyła dane replikacji na platformę Azure za pośrednictwem portu HTTPS 443 dla ruchu wychodzącego. Aby skonfigurować te reguły, edytuj reguły ruchu przychodzącego/wychodzącego grupy zabezpieczeń przy użyciu odpowiednich portów i źródłowych informacji o adresie IP.

   ![Reguły zapory GCP](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![Edytowanie reguł zapory](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- Urządzenie replikacji używa programu MySQL. Zapoznaj się [z opcjami](migrate-replication-appliance.md#mysql-installation) instalowania programu MySQL na urządzeniu.
- Przejrzyj adresy URL platformy Azure wymagane przez urządzenie replikacji w celu uzyskania dostępu do [chmur publicznych](migrate-replication-appliance.md#url-access) [i](migrate-replication-appliance.md#azure-government-url-access) dla instytucji rządowych.

## <a name="set-up-the-replication-appliance"></a>Konfigurowanie urządzenia replikacji

Pierwszym krokiem migracji jest skonfigurowanie urządzenia replikacji. Aby skonfigurować urządzenie do migracji maszyn wirtualnych GCP, należy pobrać plik instalatora dla urządzenia, a następnie uruchomić go na przygotowanej maszynie [wirtualnej.](#prepare-a-machine-for-the-replication-appliance)

### <a name="download-the-replication-appliance-installer"></a>Pobieranie instalatora urządzenia replikacji

1. W projekcie Azure Migrate serwery > **w** **Azure Migrate: Migracja serwera** kliknij pozycję **Odnajdz.**

    ![Odnajdywanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. Na **stronie Odnajdywanie maszyn** Czy maszyny są  >  **zwirtualizowane?** kliknij pozycję Nie **zwirtualizowane/inne.**
3. W **obszarze Region docelowy** wybierz region platformy Azure, do którego chcesz migrować maszyny.
4. Wybierz **pozycję Potwierdź, że region docelowy** migracji ma <nazwę regionu>.
5. Kliknij **pozycję Utwórz zasoby.** Powoduje to utworzenie Azure Site Recovery magazynu danych w tle.
    - Jeśli migracja została już skonfigurowana za pomocą Azure Migrate Server Migration, nie można skonfigurować opcji docelowej, ponieważ zasoby zostały wcześniej skonfigurowane.
    - Po kliknięciu tego przycisku nie można zmienić regionu docelowego dla tego projektu.
    - Aby przeprowadzić migrację maszyn wirtualnych do innego regionu, należy utworzyć nowy/inny Azure Migrate projekt. 
    > [!NOTE]
    > W przypadku wyboru prywatnego punktu końcowego jako metody łączności dla projektu Azure Migrate podczas jego tworzenia magazyn usługi Recovery Services zostanie również skonfigurowany do łączności z prywatnym punktem końcowym. Upewnij się, że prywatne punkty końcowe są dostępne z urządzenia replikacji: [ **Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)

6. Na **stronie Czy chcesz zainstalować nowe urządzenie replikacji?** wybierz pozycję Zainstaluj urządzenie **replikacji.**
7. W **chmurze Pobierz i zainstaluj oprogramowanie urządzenia replikacji** pobierz instalator urządzenia oraz klucz rejestracji. Klucz jest potrzebny do zarejestrowania urządzenia. Klucz jest ważny przez pięć dni po jego pobraniu.

    ![Dostawca pobierania](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Skopiuj plik instalacyjny urządzenia i plik klucza na maszynę wirtualną z systemem Windows Server 2016 lub Windows Server 2012 GCP utworzoną dla urządzenia replikacji.
9. Uruchom plik instalacyjny urządzenia replikacji zgodnie z opisem w następnej procedurze.  
    9.1. W obszarze **Przed rozpoczęciem** wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**, a następnie wybierz opcję **Dalej**.   
    9.2 **W** licencji na oprogramowanie innej firmy wybierz pozycję Akceptuję umowę licencyjną innej **firmy,** a następnie wybierz pozycję **Dalej.**   
    9.3 **W** rejestracji wybierz **pozycję Przeglądaj,** a następnie przejdź do miejsca, w którym umieścisz plik klucza rejestracji magazynu. Wybierz opcję **Dalej**.  
    9.4 W **ustawieniach internetowych** wybierz pozycję Połącz Azure Site Recovery bez serwera **proxy, a** następnie wybierz pozycję **Dalej.**  
    9.5 Na stronie **Sprawdzanie wymagań wstępnych** jest uruchamiane sprawdzanie kilku elementów. Po zakończeniu wybierz opcję **Dalej**.  
    9.6 W konfiguracji **programu MySQL** podaj hasło dla bazy danych MySQL, a następnie wybierz pozycję **Dalej.**  
    9.7 W **szczegółach środowiska** wybierz pozycję **Nie.** Nie musisz chronić maszyn wirtualnych. Następnie wybierz pozycję **Dalej**.  
    9.8 W **pozycji Lokalizacja instalacji** wybierz przycisk **Dalej,** aby zaakceptować wartość domyślną.  
    9.9 W **menu Wybór sieci** wybierz przycisk **Dalej,** aby zaakceptować wartość domyślną.  
    9.10 W **podsumowaniu wybierz** pozycję **Zainstaluj**.   
    9.11 **Postęp instalacji** zawiera informacje o procesie instalacji. Po zakończeniu wybierz opcję **Zakończ**. Zostanie wyświetlone okno z komunikatem o konieczności ponownego uruchomienia. Wybierz przycisk **OK**.   
    9.12 Następnie w oknie zostanie wyświetlony komunikat o hasło połączenia serwera konfiguracji. Skopiuj hasło do schowka i zapisz je w tymczasowym pliku tekstowym na źródłowych maszynach wirtualnych. To hasło będzie potrzebne później podczas procesu instalacji usługi mobilności.
10. Po zakończeniu instalacji zostanie automatycznie uruchomiony Kreator konfiguracji urządzenia (możesz również uruchomić kreatora ręcznie przy użyciu skrótu cspsconfigtool utworzonego na pulpicie urządzenia). Użyj karty Zarządzaj kontami kreatora, aby dodać szczegóły konta do użycia podczas instalacji wypychaowej usługa mobilności. W tym samouczku ręcznie zainstalujemy usługę Mobility Service na źródłowych maszyn wirtualnych do replikacji, dlatego w tym kroku utworzymy fikcyjne konto i kontynuujemy. Możesz podać następujące szczegóły dotyczące tworzenia fikcyjnego konta — "gość" jako przyjazną nazwę, "username" jako nazwę użytkownika i "hasło" jako hasło do konta. Będziesz używać tego fikcyjnego konta na etapie Włączania replikacji. 
11. Po ponownym uruchomieniu urządzenia po instalacji w chmurze **Odnajdywanie** maszyn wybierz nowe urządzenie w chmurze **Wybierz** serwer konfiguracji i kliknij przycisk **Zakończ rejestrację.** Finalizuj rejestrację wykonuje kilka końcowych zadań w celu przygotowania urządzenia replikacji.

    ![Finalizuj rejestrację](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>Instalowanie usługi mobilności

Agent usługa mobilności musi być zainstalowany na źródłowych maszyn wirtualnych GCP, które mają zostać zmigrowane. Instalatory agenta są dostępne na urządzeniu replikacji. Znajdź odpowiedniego instalatora i zainstaluj agenta na każdej maszynie, którą chcesz migrować. Wykonaj następujące czynności:

1. Zaloguj się do urządzenia replikacji.
2. Przejdź do **folderu %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.**
3. Znajdź instalatora źródłowego systemu operacyjnego i wersji maszyn wirtualnych GCP. Zapoznaj się [z obsługiwanymi systemami operacyjnymi.](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)
4. Skopiuj plik instalatora na źródłową maszynę wirtualną GCP, którą chcesz zmigrować.
5. Upewnij się, że masz zapisany plik tekstowy hasła, który został utworzony podczas instalacji urządzenia replikacji.
    - Jeśli nie pamiętasz zapisać hasła, w tym kroku możesz wyświetlić hasło na urządzeniu replikacji. W wierszu polecenia uruchom **polecenieC:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v,** aby wyświetlić bieżące hasło.
    - Teraz skopiuj to hasło do schowka i zapisz je w tymczasowym pliku tekstowym na źródłowych maszyn wirtualnych.

### <a name="installation-guide-for-windows-gcp-vms"></a>Przewodnik instalacji maszyn wirtualnych GCP z systemem Windows

1. Wyodrębnij zawartość pliku instalatora do folderu lokalnego (na przykład C:\Temp) na maszynie wirtualnej GCP w następujący sposób:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Uruchom instalator usługi mobilności:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-gcp-vms"></a>Przewodnik instalacji maszyn wirtualnych GCP z systemem Linux

1. Wyodrębnij zawartość pliku tarball instalatora do folderu lokalnego (na przykład /tmp/MobSvcInstaller) na maszynie wirtualnej GCP w następujący sposób:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Uruchom skrypt instalatora:
    ```
    sudo ./install -r MS -q
    ```  

3. Zarejestruj agenta przy użyciu urządzenia replikacji:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-gcp-vms"></a>Włączanie replikacji dla maszyn wirtualnych GCP

> [!NOTE]
> Za pośrednictwem portalu można jednocześnie dodać maksymalnie 10 maszyn wirtualnych do replikacji. Aby jednocześnie replikować więcej maszyn wirtualnych, możesz dodać je w partiach po 10.

1. W projekcie Azure Migrate serwery > **,** **Azure Migrate: Migracja serwera** kliknij pozycję **Replikuj.**

    ![Replikowanie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Na **stronie Replikuj**> **źródłową**  >  **czy maszyny są zwirtualizowane?** wybierz pozycję Nie **zwirtualizowane/inne.**
3. W **urządzeniu lokalnym** wybierz nazwę Azure Migrate, które zostało przez Ciebie ustawione.
4. Na **serwerze przetwarzania** wybierz nazwę urządzenia replikacji. 
5. W **poświadczeniach gościa** wybierz fikcyjne konto [](#download-the-replication-appliance-installer) utworzone wcześniej podczas konfigurowania instalatora replikacji, aby ręcznie zainstalować usługa mobilności (instalacja wypychana nie jest obsługiwana). Następnie kliknij przycisk **Dalej: Maszyny wirtualne.**   
 
    ![Ustawienia replikacji](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. W **Virtual Machines** opcji w opcji Importuj ustawienia migracji z **oceny?** pozostaw ustawienie domyślne Nie. Określę ustawienia migracji **ręcznie.**
7. Sprawdź każdą maszynę wirtualną, którą chcesz migrować. Następnie kliknij przycisk **Dalej: Ustawienia docelowe.**

    ![Wybieranie maszyn wirtualnych](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. W obszarze **Ustawienia elementu docelowego** wybierz subskrypcję i docelowy region migracji, a następnie określ grupę zasobów, w której będą znajdować się maszyny wirtualne platformy Azure po migracji.
9. W obszarze **Sieć wirtualna** wybierz sieć wirtualną/podsieć platformy Azure, do której zostaną dołączone maszyny wirtualne platformy Azure po migracji.  
10. Na  **stronie Konto magazynu pamięci podręcznej** zachowaj domyślną opcję używania konta magazynu pamięci podręcznej, które jest automatycznie tworzone dla projektu. Użyj listy rozwijanej, jeśli chcesz określić inne konto magazynu do użycia jako konto magazynu pamięci podręcznej na użytek replikacji. <br/> 
    > [!NOTE]
    >
    > - Jeśli jako metodę łączności dla projektu usługi Azure Migrate wybrano prywatny punkt końcowy, przyznaj magazynowi usługi Recovery Services dostęp do konta magazynu pamięci podręcznej. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Aby replikować przy użyciu usługi ExpressRoute z prywatną komunikacji równorzędnej, utwórz prywatny punkt końcowy dla konta magazynu pamięci podręcznej. [**Dowiedz się więcej**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
11. W **opcje dostępności**, wybierz:
    -  Strefa dostępności do przypiętych migrowanych maszyn do określonej strefy dostępności w regionie. Ta opcja umożliwia dystrybucję serwerów tworzących warstwę aplikacji z wieloma węzłami między Strefy dostępności. Jeśli wybierzesz tę opcję, musisz określić strefę dostępności do użycia dla każdej wybranej maszyny na karcie Obliczenia. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności
    -  Zestaw dostępności, aby umieścić zmigrowany komputer w zestawie dostępności. Docelowa grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji.
    - Jeśli nie potrzebujesz żadnej z tych konfiguracji dostępności dla migrowanych maszyn, nie jest wymagana żadna z tych opcji nadmiarowości infrastruktury.
12. W **typie Szyfrowanie dysków** wybierz:
    - Szyfrowanie w spoczynku za pomocą klucza zarządzanego przez platformę
    - Szyfrowanie danych w spoczynku przy użyciu klucza zarządzanego przez klienta
    - Podwójne szyfrowanie za pomocą kluczy zarządzanych przez platformę i zarządzanych przez klienta

   > [!NOTE]
   > Aby replikować maszyny wirtualne za pomocą klucza cmk, należy utworzyć zestaw szyfrowania dysków [w](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) docelowej grupie zasobów. Obiekt zestawu szyfrowania dysków mapuje Dyski zarządzane na Key Vault, który zawiera klucz CMK do użycia na użytek funkcji SSE.
  
13. W obszarze **Korzyść użycia hybrydowego platformy Azure**:

    - Wybierz pozycję **Nie**, jeśli nie chcesz stosować korzyści użycia hybrydowego platformy Azure. Następnie kliknij przycisk **Dalej**.
    - Wybierz opcję **Tak**, jeśli masz maszyny z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, i chcesz zastosować korzyść do migrowanych maszyn. Następnie kliknij przycisk **Dalej**.

    ![Ustawienia docelowe](./media/tutorial-migrate-vmware/target-settings.png)

14. W **jęz.** obliczeniowym przejrzyj nazwę maszyny wirtualnej, rozmiar, typ dysku systemu operacyjnego i konfigurację dostępności (jeśli wybrano w poprzednim kroku). Maszyny wirtualne muszą być zgodne z [wymaganiami platformy Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Rozmiar maszyny wirtualnej:** jeśli używasz zaleceń dotyczących oceny, lista rozwijana Rozmiar maszyny wirtualnej zawiera zalecany rozmiar. W przeciwnym razie usługa Azure Migrate wybierze rozmiar na podstawie najbliższego dopasowania w subskrypcji platformy Azure. Alternatywnie możesz wybrać rozmiar ręczny w obszarze **rozmiaru maszyny wirtualnej platformy Azure**.
    - **Dysk systemu operacyjnego:** określ dysk systemu operacyjnego (rozruchowy) dla maszyny wirtualnej. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego.
    - **Strefa dostępności:** określ strefę dostępności do użycia.
    - **Zestaw dostępności:** określ zestaw dostępności do użycia.

![Ustawienia obliczeniowe](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. W **witrynie Dyski** określ, czy dyski maszyny wirtualnej mają być replikowane na platformie Azure, i wybierz typ dysku (dyski ssd/HDD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) na platformie Azure. Następnie kliknij przycisk **Dalej**.
    - Dyski można wykluczyć z replikacji.
    - Jeśli wykluczysz dyski, nie będą one znajdować się na maszynie wirtualnej platformy Azure po migracji. 

    ![Ustawienia dysku](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. W obszarze **Przegląd i rozpoczynanie replikacji** sprawdź ustawienia, a następnie kliknij pozycję **Replikuj**, aby uruchomić replikację początkową dla serwerów.

> [!NOTE]
> Ustawienia replikacji można zaktualizować w dowolnym momencie przed rozpoczęciem **replikacji: Zarządzanie**  >  **replikami maszyn.** Ustawień nie można zmienić po rozpoczęciu replikacji.

## <a name="track-and-monitor-replication-status"></a>Śledzenie i monitorowanie stanu replikacji

- Po kliknięciu **przycisku Replikuj** rozpoczyna się zadanie uruchamiania replikacji.
- Po pomyślnym zakończeniu zadania rozpocznij replikację początkową maszyn wirtualnych na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany dysków maszyn wirtualnych platformy GCP są okresowo replikowane na dyski repliki na platformie Azure.

Stan zadania można śledzić w powiadomieniach portalu.

Stan replikacji można monitorować, klikając pozycję **Replikowanie serwerów** **w Azure Migrate: Migracja serwera.**  

![Monitorowanie replikacji](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Po uruchomieniu replikacji różnicowej można uruchomić migrację testowa dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Migracja testowa jest zdecydowanie zalecana i umożliwia wykrywanie potencjalnych problemów i ich rozwiązywanie przed kontynuowaniem rzeczywistej migracji. Zaleca się, aby wykonać tę migrację co najmniej raz dla każdej maszyny wirtualnej przed jej migracją.

- Uruchomienie testu migracji sprawdza, czy migracja będzie działać zgodnie z oczekiwaniami, bez wpływu na maszyny wirtualne GCP, które nadal działają i kontynuują replikację.
- Migracja testowa symuluje migrację przez utworzenie maszyny wirtualnej platformy Azure przy użyciu replikowanych danych (zazwyczaj jest to migracja do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanej testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Przetestuj migrację w następujący sposób:

1. W **cele migracji**  >  **serwerów**  >  **Azure Migrate: migracja serwera,** kliknij przycisk Test **zmigrowane serwery.**

     ![Serwery z przeprowadzoną migracją testową](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kliknij prawym przyciskiem myszy maszynę wirtualną do przetestowania, a następnie kliknij pozycję **Testuj migrację**.

    ![Testowanie migracji](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. W obszarze **Testowanie migracji** wybierz sieć wirtualną platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po migracji. Zalecamy użycie nieprodukcyjnej sieci wirtualnej.
4. Zostanie uruchomione zadanie **Testowanie migracji**. Monitoruj zadanie w powiadomieniach portalu.
5. Po zakończeniu migracji sprawdź zmigrowane maszyny wirtualne platformy Azure w obszarze **Maszyny wirtualne** w witrynie Azure Portal. Nazwa maszyny ma sufiks **-Test**.
6. Po zakończeniu testu kliknij prawym przyciskiem myszy maszynę wirtualną platformy Azure w obszarze **Replikowanie maszyn**, a następnie kliknij pozycję **Wyczyść migrację testową**.

    ![Czyszczenie migracji](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-gcp-vms"></a>Migrowanie maszyn wirtualnych GCP

Po sprawdzeniu, czy migracja testowa działa zgodnie z oczekiwaniami, możesz przeprowadzić migrację maszyn wirtualnych GCP.

1. W projekcie Azure Migrate serwery >: migracja serwera Azure Migrate kliknij pozycję  >   **Replikowanie serwerów.**

    ![Replikowanie serwerów](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. W obszarze **Replikowanie maszyn** kliknij prawym przyciskiem myszy maszynę wirtualną > **Migruj**.
3. Na **stronie**  >  **Migruj Zamknij** maszyny wirtualne i wykonaj planowaną migrację bez utraty danych wybierz **pozycję Tak**  >  **OK.**
    - Jeśli nie chcesz zamykać maszyny wirtualnej, wybierz pozycję **Nie.**
4. Zostanie uruchomione zadanie migracji maszyny wirtualnej. Stan zadania można wyświetlić, klikając ikonę dzwonka powiadomień w prawym górnym rogu strony portalu lub przechodząc do strony zadań narzędzia do migracji serwera (kliknij pozycję Przegląd na kafelku narzędzia > Wybierz zadania z menu po lewej stronie).
5. Po zakończeniu zadania możesz wyświetlić maszynę wirtualną i zarządzać nią na stronie Maszyny wirtualne.

### <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji kliknij prawym przyciskiem myszy maszynę wirtualną, aby > **zatrzymać migrację.** Spowoduje to wykonanie następujących czynności:
    - Zatrzymuje replikację maszyny wirtualnej platformy GCP.
    - Usuwa maszynę wirtualną GCP z liczby serwerów **replikowania** w Azure Migrate: Migracja serwera.
    - Czyści informacje o stanie replikacji dla maszyny wirtualnej.
2. Zainstaluj agenta maszyny wirtualnej platformy Azure z systemem [Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Przetnij ruch do zmigrowanych wystąpień maszyn wirtualnych platformy Azure.
6. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure. 




## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Blokowanie i ograniczanie dostępu do ruchu przychodzącego za [pomocą Azure Security Center — administrowanie just in time.](../security-center/security-center-just-in-time.md)
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
    - Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.



## <a name="troubleshooting--tips"></a>Rozwiązywanie problemów/porady

**Pytanie:** Nie widzę mojej maszyny wirtualnej platformy GCP na odnalezionej liście serwerów do migracji   
**Odpowiedź:** Sprawdź, czy urządzenie replikacji spełnia wymagania. Upewnij się, że agent mobilności jest zainstalowany na źródłowej maszynie wirtualnej, która ma zostać zmigrowana, i że jest zarejestrowany na serwerze konfiguracji. Sprawdź reguły zapory, aby włączyć ścieżkę sieciową między urządzeniem replikacji i źródłowymi maszynami wirtualnych GCP.  

**Pytanie:** Jak mogę, czy moja maszyna wirtualna została pomyślnie zmigrowana   
**Odpowiedź:** Po migracji możesz wyświetlić maszynę wirtualną i zarządzać Virtual Machines wirtualnej. Połącz się ze zmigrowana maszyną wirtualną w celu zweryfikowania.  

**Pytanie:** Nie mogę zaimportować maszyn wirtualnych do migracji z wcześniej utworzonych wyników narzędzia Server Assessment   
**Odpowiedź:** Obecnie nie obsługujemy importowania oceny dla tego przepływu pracy. Aby obejść ten problem, możesz wyeksportować ocenę, a następnie ręcznie wybrać zalecenie dotyczące maszyny wirtualnej w kroku Włączanie replikacji.
  
**Pytanie:** Podczas próby odnajdywania maszyn wirtualnych GCP występuje błąd "Nie można pobrać identyfikatora GUID systemu BIOS"   
**Odpowiedź:** Użyj głównego identyfikatora logowania do uwierzytelniania, a nie żadnego pseudou użytkownika. Jeśli nie możesz użyć użytkownika głównego, upewnij się, że wymagane funkcje zostały ustawione na użytkownika, zgodnie z instrukcjami podanymi w [macierzy obsługi](migrate-support-matrix-physical.md#physical-server-requirements). Zapoznaj się również z obsługiwanymi systemami operacyjnymi dla maszyn wirtualnych GCP.  

**Pytanie:** Mój stan replikacji nie postępuje   
**Odpowiedź:** Sprawdź, czy urządzenie replikacji spełnia wymagania. Upewnij się, że na urządzeniu replikacji włączono wymagane porty TCP 9443 i HTTPS 443 na potrzeby transportu danych. Upewnij się, że nie ma żadnych nieodświeżonych zduplikowanych wersji urządzenia replikacji podłączonych do tego samego projektu.   

**Pytanie:** Nie mogę odnaleźć wystąpień GCP przy użyciu Azure Migrate ze względu na kod stanu HTTP 504 ze zdalnej usługi zarządzania systemem Windows    
**Odpowiedź:** Zapoznaj się z wymaganiami dotyczącymi urządzenia usługi Azure Migrate i wymaganiami dotyczącymi dostępu do adresu URL. Upewnij się, że żadne ustawienia serwera proxy nie blokują rejestracji urządzenia.

**Pytanie:** Czy muszę wprowadzić jakiekolwiek zmiany przed migracją maszyn wirtualnych GCP na platformę Azure   
**Odpowiedź:** Przed migracją maszyn wirtualnych EC2 na platformę Azure może być trzeba wprowadzić następujące zmiany:

- Jeśli używasz narzędzia cloud-init do aprowizowania maszyny wirtualnej, warto wyłączyć cloud-init na maszynie wirtualnej przed jej replikacją na platformę Azure. Kroki aprowizowania wykonywane przez cloud-init na maszynie wirtualnej mogą być specyficzne dla platformy GCP i nie będą prawidłowe po migracji na platformę Azure.  
- Zapoznaj się z [sekcją wymagań wstępnych,](#prerequisites) aby ustalić, czy istnieją jakieś zmiany niezbędne dla systemu operacyjnego przed ich migracją na platformę Azure.
- Zawsze zalecamy uruchomienie migracji testowej przed ostateczną migracją.  

## <a name="next-steps"></a>Następne kroki

Zbadaj [podróż po migracji do](/azure/architecture/cloud-adoption/getting-started/migrate) chmury w usłudze Azure Cloud Adoption Framework.