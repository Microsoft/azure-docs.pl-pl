---
title: Automatyzowanie odzyskiwania po awarii StorSimple z użyciem Azure Site Recovery | Microsoft Docs
description: Zawiera opis kroków i najlepszych rozwiązań dotyczących tworzenia rozwiązania do odzyskiwania po awarii dla udziałów plików hostowanych w usłudze Microsoft Azure StorSimple Storage.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: 6d98f1a1dfb575f4af8b666d173f9354b5eeac6b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966262"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatyczne rozwiązanie do odzyskiwania po awarii wykorzystujące Azure Site Recovery dla udziałów plików hostowanych w usłudze StorSimple

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Microsoft Azure StorSimple to hybrydowe rozwiązanie do magazynowania w chmurze, które eliminuje złożoność danych bez struktury często skojarzonych z udziałami plików. Usługa StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie warstwuje dane w magazynach lokalnych i w chmurze. Zintegrowana ochrona danych z migawkami lokalnymi i w chmurze eliminuje potrzebę infrastruktury magazynu sprawling.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) to usługa oparta na platformie Azure, która zapewnia możliwości odzyskiwania po awarii (Dr) przez organizowanie replikacji, trybu failover i odzyskiwania maszyn wirtualnych. Azure Site Recovery obsługuje wiele technologii replikacji, które umożliwiają spójną replikację, ochronę i bezproblemowe przełączanie maszyn wirtualnych i aplikacji do chmur prywatnych/publicznych lub hostowanych.

Korzystając z Azure Site Recovery, replikacji maszyny wirtualnej i możliwości migawek w chmurze StorSimple, można chronić całe środowisko serwera plików. W przypadku przerwania możesz użyć jednego kliknięcia, aby przenieść udziały plików w tryb online na platformie Azure w ciągu zaledwie kilku minut.

W tym dokumencie wyjaśniono szczegółowo, jak utworzyć rozwiązanie do odzyskiwania po awarii dla udziałów plików hostowanych w magazynie StorSimple oraz wykonać zaplanowane, niezaplanowane i testowe przełączenie w tryb failover przy użyciu planu odzyskiwania jednego kliknięcia. W tym temacie pokazano, jak można zmodyfikować plan odzyskiwania w magazynie Azure Site Recovery, aby umożliwić StorSimple trybu failover w ramach scenariuszy awarii. Ponadto opisano obsługiwane konfiguracje i wymagania wstępne. W tym dokumencie przyjęto założenie, że znasz podstawowe informacje dotyczące architektur Azure Site Recovery i StorSimple.

## <a name="supported-azure-site-recovery-deployment-options"></a>Obsługiwane Azure Site Recovery opcje wdrażania
Klienci mogą wdrażać serwery plików jako serwery fizyczne lub maszyny wirtualne uruchomione w funkcji Hyper-V lub VMware, a następnie tworzyć udziały plików z woluminów używać miejsca z magazynu StorSimple. Azure Site Recovery może chronić wdrożenia fizyczne i wirtualne w lokacji dodatkowej lub na platformie Azure. Ten dokument zawiera szczegółowe informacje o rozwiązaniu DR na platformie Azure jako lokacji odzyskiwania dla maszyny wirtualnej serwera plików hostowanej w funkcji Hyper-V i z udziałami plików w magazynie StorSimple. Inne scenariusze, w których maszyna wirtualna serwera plików znajduje się na maszynie wirtualnej VMware lub na komputerze fizycznym, można zaimplementować w podobny sposób.

## <a name="prerequisites"></a>Wymagania wstępne
Wdrożenie jednego kliknięcia rozwiązania do odzyskiwania po awarii, które używa Azure Site Recovery do udziałów plików hostowanych w magazynie StorSimple, ma następujące wymagania wstępne:

   - Lokalna maszyna wirtualna serwera plików z systemem Windows Server 2012 R2 hostowana w funkcji Hyper-V lub VMware lub na komputerze fizycznym
   - Lokalne urządzenie magazynujące StorSimple zarejestrowane w usłudze Azure StorSimple Manager
   - Urządzenie w chmurze StorSimple utworzone w usłudze Azure StorSimple Manager. Urządzenie może być utrzymywane w stanie zamknięcia.
   - Udziały plików hostowane na woluminach skonfigurowanych na urządzeniu magazynującym StorSimple
   - [Magazyn usług Azure Site Recovery Services](/azure/storsimple/hyper-v-vmm-disaster-recovery) utworzony w ramach subskrypcji Microsoft Azure

Ponadto jeśli platforma Azure jest witryną odzyskiwania, uruchom narzędzie do [oceny gotowości maszyn wirtualnych platformy Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi platformy Azure i usługami Azure Site Recovery.

Aby uniknąć problemów z opóźnieniami (co może skutkować zwiększeniem kosztów), należy się upewnić, że tworzysz urządzenie w chmurze StorSimple, konto usługi Automation i konto magazynu w tym samym regionie.

## <a name="enable-dr-for-storsimple-file-shares"></a>Włącz udziały plików DR for StorSimple
Aby zapewnić pełną replikację i odzyskiwanie, każdy składnik środowiska lokalnego musi być chroniony. W tej sekcji opisano, jak:
    
   - Konfigurowanie Active Directory i replikacji DNS (opcjonalnie)
   - Użyj Azure Site Recovery, aby włączyć ochronę maszyny wirtualnej serwera plików
   - Włącz ochronę woluminów StorSimple
   - Konfigurowanie sieci

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Konfigurowanie Active Directory i replikacji DNS (opcjonalnie)
Jeśli chcesz chronić maszyny z systemami Active Directory i DNS, aby były dostępne w lokacji odzyskiwania po awarii, należy je jawnie chronić (dzięki czemu serwery plików są dostępne po przejściu w tryb failover z uwierzytelnianiem). Istnieją dwie zalecane opcje oparte na złożoności środowiska lokalnego klienta.

#### <a name="option-1"></a>Opcja 1
Jeśli klient ma niewielką liczbę aplikacji, pojedynczy kontroler domeny dla całej lokacji lokalnej i przestanie działać przez całą lokację, zalecamy użycie replikacji Azure Site Recovery w celu replikowania maszyny kontrolera domeny do lokacji dodatkowej (dotyczy to zarówno lokacji typu lokacja-lokacja, jak i do platformy Azure).

#### <a name="option-2"></a>Opcja 2
Jeśli klient ma dużą liczbę aplikacji, działa w lesie Active Directory i będzie działać w wielu aplikacjach jednocześnie, zalecamy skonfigurowanie dodatkowego kontrolera domeny w witrynie odzyskiwania po awarii (lokacji dodatkowej lub na platformie Azure).

Zapoznaj się z [automatycznym rozwiązaniem odzyskiwania po awarii dla Active Directory i DNS, używając Azure Site Recovery,](../site-recovery/site-recovery-active-directory.md) Aby uzyskać instrukcje dotyczące tworzenia kontrolera domeny dostępnego w witrynie Dr. W pozostałej części tego dokumentu przyjęto, że kontroler domeny jest dostępny w witrynie odzyskiwania po awarii.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Użyj Azure Site Recovery, aby włączyć ochronę maszyny wirtualnej serwera plików
Ten krok wymaga przygotowania lokalnego środowiska serwera plików, utworzenia i przygotowania magazynu Azure Site Recovery oraz włączenia ochrony plików maszyny wirtualnej.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Aby przygotować lokalne środowisko serwera plików
1. Ustaw **kontrolę konta użytkownika** na **nigdy nie Powiadamiaj**. Jest to wymagane, aby można było użyć skryptów usługi Azure Automation do łączenia obiektów docelowych iSCSI po przejściu w tryb failover przez Azure Site Recovery.
   
   1. Naciśnij klawisz Windows + Q i Wyszukaj pozycję **UAC**.  
   1. Wybierz pozycję **Zmień ustawienia kontroli konta użytkownika**.  
   1. Przeciągnij pasek na dół do strony bez **powiadomienia**.  
   1. Kliknij przycisk **OK** , a następnie wybierz opcję **tak** po wyświetleniu monitu.  
   
      ![Ustawienia kontroli konta użytkownika](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Zainstaluj agenta maszyny wirtualnej na każdej maszynie wirtualnej serwera plików. Jest to wymagane, aby można było uruchamiać skrypty usługi Azure Automation na maszynach wirtualnych w trybie failover.
   
   1. [Pobierz agenta](https://aka.ms/vmagentwin) do programu `C:\\Users\\<username>\\Downloads` .
   1. Otwórz program Windows PowerShell w trybie administratora (Uruchom jako administrator), a następnie wprowadź następujące polecenie, aby przejść do lokalizacji pobierania:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Nazwa pliku może ulec zmianie w zależności od wersji.
      
1. Kliknij przycisk **Dalej**.
1. Zaakceptuj **warunki umowy** , a następnie kliknij przycisk **dalej**.
1. Kliknij przycisk **Finish** (Zakończ).
1. Utwórz udziały plików przy użyciu woluminów używać miejsca z magazynu StorSimple. Aby uzyskać więcej informacji, zobacz [Używanie usługi StorSimple Manager do zarządzania woluminami](./index.yml).
   
   1. Na lokalnych maszynach wirtualnych naciśnij klawisz Windows + Q i Wyszukaj **iSCSI**.
   1. Wybierz **inicjatora iSCSI**.
   1. Wybierz kartę **Konfiguracja** i skopiuj nazwę inicjatora.
   1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
   1. Wybierz kartę **StorSimple** , a następnie wybierz usługę StorSimple Manager, która zawiera urządzenie fizyczne.
   1. Utwórz kontenery woluminów, a następnie utwórz woluminy. (Te woluminy są przeznaczone dla udziałów plików na maszynach wirtualnych serwera plików). Skopiuj nazwę inicjatora i nadaj jej nazwę Access Control podczas tworzenia woluminów.
   1. Wybierz kartę **Konfiguracja** i Zanotuj adres IP urządzenia.
   1. Na lokalnych maszynach wirtualnych przejdź ponownie do **inicjatora iSCSI** , a następnie wprowadź adres IP w sekcji Szybkie łączenie. Kliknij pozycję **szybkie łączenie** (urządzenie powinno być teraz połączone).
   1. Otwórz Azure Portal i wybierz kartę **woluminy i urządzenia** . Kliknij pozycję **Konfiguruj automatycznie**. Utworzony wolumin powinien zostać wyświetlony.
   1. W portalu wybierz kartę **urządzenia** , a następnie wybierz pozycję **Utwórz nowe urządzenie wirtualne.** (To urządzenie wirtualne zostanie użyte w przypadku przejścia w tryb failover). To nowe urządzenie wirtualne można zachować w trybie offline, aby uniknąć dodatkowych kosztów. Aby przełączyć urządzenie wirtualne w tryb offline, przejdź do sekcji **Virtual Machines** w portalu i zamknij go.
   1. Wróć do lokalnych maszyn wirtualnych i Otwórz przystawkę Zarządzanie dyskami (naciśnij klawisz Windows + X i wybierz pozycję **Zarządzanie dyskami**).
   1. Zauważysz kilka dodatkowych dysków (w zależności od liczby utworzonych woluminów). Kliknij prawym przyciskiem myszy pierwszy, wybierz opcję **zainicjuj dysk**, a następnie wybierz przycisk **OK**. Kliknij prawym przyciskiem myszy **przydzieloną** sekcję, wybierz pozycję **Nowy wolumin prosty**, przypisz do niej literę dysku i Zakończ pracę kreatora.
   1. Powtórz krok l dla wszystkich dysków. Teraz możesz zobaczyć wszystkie dyski na **tym komputerze** w Eksploratorze Windows.
   1. Użyj roli usługi plików i magazynowania, aby utworzyć udziały plików na tych woluminach.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Aby utworzyć i przygotować Magazyn Azure Site Recovery
Zapoznaj się z [dokumentacją Azure Site Recovery](/azure/storsimple/hyper-v-azure-tutorial) , aby rozpocząć pracę z usługą Azure Site Recovery przed ochroną maszyny wirtualnej serwera plików.

#### <a name="to-enable-protection"></a>Aby włączyć ochronę
1. Odłącz obiekty docelowe iSCSI od lokalnych maszyn wirtualnych, które mają być chronione za pośrednictwem Azure Site Recovery:
   
   1. Naciśnij klawisze Windows + Q i Wyszukaj **iSCSI**.
   1. Wybierz pozycję **Konfigurowanie inicjatora iSCSI**.
   1. Odłącz wcześniej połączone urządzenie StorSimple. Alternatywnie można wyłączyć serwer plików przez kilka minut podczas włączania ochrony.
      
   > [!NOTE]
   > Spowoduje to, że udziały plików będą tymczasowo niedostępne.
   
1. [Włącz ochronę maszyny wirtualnej](/azure/storsimple/hyper-v-azure-tutorial) serwera plików z poziomu portalu Azure Site Recovery.
1. Po rozpoczęciu początkowej synchronizacji można ponownie połączyć się z miejscem docelowym. Przejdź do inicjatora iSCSI, wybierz urządzenie StorSimple, a następnie kliknij przycisk **Połącz**.
1. Gdy synchronizacja zostanie zakończona, a stan maszyny wirtualnej jest **chroniony**, wybierz maszynę wirtualną, wybierz kartę **Konfiguracja** i odpowiednio zaktualizuj sieć maszyny wirtualnej (jest to sieć, do której będzie częścią maszyn wirtualnych w trybie failover). Jeśli sieć nie zostanie wyświetlona, oznacza to, że synchronizacja nadal trwa.

### <a name="enable-protection-of-storsimple-volumes"></a>Włącz ochronę woluminów StorSimple
Jeśli nie wybrano opcji **Włącz domyślną kopię zapasową dla tego woluminu** dla woluminów StorSimple, przejdź do **zasad tworzenia kopii zapasowych** w usłudze StorSimple Manager i Utwórz odpowiednie zasady tworzenia kopii zapasowych dla wszystkich woluminów. Zalecamy ustawienie częstotliwości wykonywania kopii zapasowych na cel punktu odzyskiwania (RPO), który ma być wyświetlany dla aplikacji.

### <a name="configure-the-network"></a>Konfigurowanie sieci
W przypadku maszyny wirtualnej serwera plików Skonfiguruj ustawienia sieciowe w Azure Site Recovery tak, aby sieci maszyn wirtualnych były dołączone do odpowiedniej sieci DR po przejściu w tryb failover.

Możesz wybrać maszynę wirtualną na karcie **zreplikowane elementy** , aby skonfigurować ustawienia sieci, jak pokazano na poniższej ilustracji.

![Obliczenia i sieć](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Możesz utworzyć plan odzyskiwania w usłudze ASR, aby zautomatyzować proces trybu failover udziałów plików. Jeśli wystąpi zakłócenie, można przenieść udziały plików w ciągu kilku minut za pomocą jednego kliknięcia. Aby włączyć tę automatyzację, konieczne będzie konto usługi Azure Automation.

#### <a name="to-create-an-automation-account"></a>Aby utworzyć konto usługi Automation
1. Przejdź do &gt; sekcji **automatyzacja** Azure Portal.
1. Kliknij przycisk **+ Dodaj** przycisk, otwiera poniżej bloku.
   
   ![Dodawanie konta usługi Automation](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Nazwa — wprowadź nowe konto usługi Automation
   - Subskrypcja — Wybieranie subskrypcji
   - Grupa zasobów — Utwórz nową/Wybierz istniejącą grupę zasobów
   - Lokalizacja — wybierz lokalizację, Zachowaj ją w tym samym georegionie/regionie, w którym zostały utworzone konta w chmurze StorSimple i urządzenia magazynu.
   - Utwórz konto Uruchom jako platformy Azure — wybierz opcję **tak** .
   
1. Przejdź do konta usługi Automation, kliknij pozycję **elementy Runbook** &gt; **Przeglądaj Galerię** , aby zaimportować wszystkie wymagane elementy Runbook do konta usługi Automation.
1. Dodaj następujące elementy Runbook, wyszukując tag **odzyskiwania po awarii** w galerii:
   
   - Oczyść woluminy StorSimple po testowaniu pracy w trybie failover (suma)
   - Kontenery woluminów StorSimple trybu failover
   - Zainstaluj woluminy na urządzeniu StorSimple po przejściu w tryb failover
   - Odinstalowywanie niestandardowego rozszerzenia skryptu na maszynie wirtualnej platformy Azure
   - Uruchom urządzenie wirtualne StorSimple
   
      ![Przeglądaj Galerię](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Opublikuj wszystkie skrypty, wybierając element Runbook na koncie usługi Automation, a następnie kliknij pozycję **Edytuj** &gt; **Publikowanie** , a następnie przycisk **tak** w komunikacie weryfikacyjnym. Po wykonaniu tego kroku karta **elementy Runbook** zostanie wyświetlona w następujący sposób:
   
   ![Elementy Runbook](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Na koncie usługi Automation kliknij pozycję **zmienne** &gt; **Dodaj zmienną** i Dodaj następujące zmienne. Można zdecydować się na szyfrowanie tych zasobów. Te zmienne są specyficzne dla planu odzyskiwania. Jeśli plan odzyskiwania, który zostanie utworzony w następnym kroku, ma nazwę plan testu, wówczas zmienne powinny być plan testu-StorSimRegKey, plan testu-AzureSubscriptionName i tak dalej.

   - **Baseurl**: adres URL Menedżer zasobów dla chmury platformy Azure. Pobierz przy użyciu polecenia cmdlet **Get-AzEnvironment | Select-Object Name, ResourceManagerUrl** .
   - _RecoveryPlanName_**-ResourceGroupName**: Grupa Menedżer zasobów, która ma zasób StorSimple.
   - _RecoveryPlanName_**-ManagerName**: zasób StorSimple z urządzeniem StorSimple.
   - _RecoveryPlanName_**-DeviceName**: Urządzenie StorSimple, które musi zostać przełączone w tryb failover.
   - _RecoveryPlanName_**-DeviceIpAddress**: adres IP urządzenia (można go znaleźć na karcie **urządzenia** w obszarze StorSimple Menedżer urządzeń sekcji &gt; **Ustawienia** &gt; **sieciowej** &gt; grupy **ustawień DNS** ).
   - _RecoveryPlanName_**-VolumeContainers**: rozdzielany przecinkami ciąg kontenerów woluminów znajdujących się na urządzeniu, które wymagają przełączenia w tryb failover; na przykład: volcon1, volcon2, volcon3.
   - _RecoveryPlanName_**-TargetDeviceName**: urządzenie w chmurze StorSimple, na którym znajdują się przełączenia w tryb failover.
   - _RecoveryPlanName_**-TARGETDEVICEIPADDRESS**: adres IP urządzenia docelowego (można go znaleźć na karcie sieci grupy ustawień sekcji **maszyny wirtualnej** &gt; **Settings** &gt; **Networking** ).
   - _RecoveryPlanName_**-StorageAccountName**: nazwa konta magazynu, w którym będzie przechowywany skrypt (który musi działać na maszynie wirtualnej w trybie failover). Może to być dowolne konto magazynu z ilością miejsca do tymczasowego przechowywania skryptu.
   - _RecoveryPlanName_**-StorageAccountKey**: klucz dostępu dla powyższego konta magazynu.
   - _RecoveryPlanName_**-VMGUIDS**: w przypadku ochrony maszyny wirtualnej Azure Site Recovery przypisuje każdej maszynie wirtualnej unikatowy identyfikator, który zawiera szczegółowe informacje dotyczące maszyny wirtualnej przełączonej w tryb failover. Aby uzyskać VMGUID, wybierz kartę **Recovery Services** , a następnie kliknij pozycję **chronione** &gt; **grupy ochrony** elementów &gt; **Machines** &gt; **Właściwości** maszyny. Jeśli masz wiele maszyn wirtualnych, Dodaj identyfikatory GUID jako ciąg rozdzielony przecinkami.

     Na przykład jeśli nazwa planu odzyskiwania to fileServerpredayRP, wówczas **zmienne**, **połączenia** i **Certyfikaty** powinny być wyświetlane w następujący sposób po dodaniu wszystkich zasobów.

      ![Elementy zawartości](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Przekaż moduł Runbook serii StorSimple 8000 na koncie usługi Automation. Wykonaj poniższe kroki, aby dodać moduł:
   
   1. Otwórz program PowerShell, Utwórz nowy folder & zmienić katalog na folder.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Pobierz interfejs wiersza polecenia NuGet w tym samym folderze w krok 1.
      Różne wersje nuget.exe są dostępne na stronie [pobierania narzędzia NuGet](https://www.nuget.org/downloads). Każde łącze pobierania wskazuje bezpośrednio plik. exe, dlatego należy kliknąć prawym przyciskiem myszy i zapisać plik na komputerze zamiast uruchamiać go z przeglądarki.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Pobieranie zależnego zestawu SDK
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Utwórz moduł Azure Automation Runbook dla zarządzania urządzeniami z serii StorSimple 8000. Użyj poniższych poleceń, aby utworzyć plik zip modułu automatyzacji.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Zaimportuj plik zip modułu Azure Automation (Microsoft.Azure.Management.StorSimple8000Series.zip) utworzony w powyższym kroku. W tym celu należy wybrać konto usługi Automation, kliknąć pozycję **moduły** w obszarze zasoby udostępnione, a następnie kliknąć pozycję **Dodaj moduł**.
   
   Po zaimportowaniu modułu StorSimple 8000 Series karta **moduły** powinna wyglądać w następujący sposób:
   
      ![Moduły](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Przejdź do sekcji **Recovery Services** i wybierz utworzony wcześniej Magazyn Azure Site Recovery.
1. Wybierz opcję **plany odzyskiwania (Site Recovery)** z opcji **Zarządzaj** grupą i Utwórz nowy plan odzyskiwania w następujący sposób:
   
   - Kliknij przycisk **+ Odzyskaj plan** , Otwórz poniżej bloku.
      
      ![Tworzenie planu odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Wprowadź nazwę planu odzyskiwania, wybierz źródło, miejsce docelowe & wartości modelu wdrożenia.
   
   - Wybierz Maszyny wirtualne z grupy ochrony, które chcesz uwzględnić w planie odzyskiwania, a następnie kliknij przycisk **OK** .
   
   - Wybierz utworzony wcześniej plan odzyskiwania, kliknij przycisk **Dostosuj** , aby otworzyć widok dostosowania planu odzyskiwania.
   
   - Kliknij prawym przyciskiem myszy **wszystkie grupy** , a następnie kliknij pozycję **Dodaj akcję wstępną**.
   
   - Otwiera blok akcja Wstaw, wprowadź nazwę, wybierz opcję **podstawowa Strona** w obszarze gdzie można uruchomić, wybierz pozycję konto usługi Automation (w którym dodano elementy Runbook), a następnie wybierz element Runbook **trybu failover-StorSimple-Volume-Containers** .
   
   - Kliknij prawym przyciskiem myszy **grupę 1: Start** i kliknij opcję **Dodaj chronione elementy** , a następnie wybierz maszyny wirtualne, które mają być chronione w planie odzyskiwania, a następnie kliknij przycisk **OK** . Opcjonalne, jeśli są już wybrane maszyny wirtualne.
   
   - Kliknij prawym przyciskiem myszy **grupę 1: Start** i kliknij opcję **Ogłoś akcję** , a następnie Dodaj wszystkie następujące skrypty:  
      
      - Start-StorSimple-Virtual-urządzeniem Runbook  
      - Tryb failover w trybie failover — StorSimple-Volume-Containers  
      - Instalacja-woluminy — po zakończeniu pracy w trybie failover  
      - Uninstall-Custom-Script-Extension Runbook  
        
   - Dodaj akcję ręczną po powyższych 4 skryptach w tej samej **grupie 1: sekcja po wykonaniu kroków** . Ta akcja jest punktem, w którym można sprawdzić, czy wszystko działa prawidłowo. Ta akcja musi zostać dodana tylko jako część testowej pracy w trybie failover (dlatego należy zaznaczyć pole wyboru **test pracy w trybie failover** ).
    
   - Po wykonaniu akcji ręcznej Dodaj skrypt **czyszczący** , korzystając z tej samej procedury, która została użyta w innych elementach Runbook. **Zapisz** plan odzyskiwania.
    
   > [!NOTE]
   > Podczas przeprowadzania testu pracy w trybie failover należy sprawdzić wszystko w kroku akcji ręcznej, ponieważ woluminy StorSimple, które zostały sklonowane na urządzeniu docelowym, zostaną usunięte jako część oczyszczania po zakończeniu akcji ręcznej.
       
      ![Plan odzyskiwania](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Wykonaj test pracy w trybie failover
Zapoznaj się z przewodnikiem uzupełniania [rozwiązania Active Directory Dr](../site-recovery/site-recovery-active-directory.md) , aby uzyskać informacje dotyczące Active Directory podczas testu pracy w trybie failover. Konfiguracja lokalna nie jest zakłócana podczas testu pracy w trybie failover. Woluminy StorSimple dołączone do lokalnej maszyny wirtualnej są klonowane do urządzenia w chmurze StorSimple na platformie Azure. Maszyna wirtualna do celów testowych jest przenoszona na platformę Azure, a sklonowane woluminy są dołączone do maszyny wirtualnej.

#### <a name="to-perform-the-test-failover"></a>Aby przeprowadzić test pracy w trybie failover
1. W Azure Portal wybierz swój magazyn Site Recovery.
1. Kliknij plan odzyskiwania utworzony dla maszyny wirtualnej serwera plików.
1. Kliknij pozycję **Testuj tryb failover**.
1. Wybierz sieć wirtualną platformy Azure, z którą maszyny wirtualne platformy Azure zostaną połączone po przejściu w tryb failover.
   
   ![Uruchom tryb failover](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Kliknij przycisk **OK**, aby rozpocząć tryb failover. Postęp można śledzić, klikając maszynę wirtualną, aby otworzyć jej właściwości, lub w **zadaniu testowego trybu failover** w obszarze zadania nazwy magazynu &gt; **Jobs** &gt; **Site Recovery zadania**.
1. Po zakończeniu pracy w trybie failover należy również sprawdzić, czy replika maszyny Azure jest wyświetlana w &gt; **Virtual Machines** Azure Portal. Możesz wykonywać walidację.
1. Po zakończeniu walidacji kliknij pozycję **walidacje ukończone**. Spowoduje to usunięcie woluminów StorSimple i zamknięcie urządzenia w chmurze StorSimple.
1. Gdy skończysz, kliknij przycisk **Oczyść test pracy w trybie failover** w planie odzyskiwania. W obszarze Uwagi zarejestruj i zapisz wszelkie obserwacje związane z testem pracy w trybie failover. Spowoduje to usunięcie maszyny wirtualnej, która została utworzona podczas testu pracy w trybie failover.

## <a name="perform-a-planned-failover"></a>Wykonywanie planowanej pracy w trybie failover
   Podczas planowanego przejścia w tryb failover maszyna wirtualna lokalnego serwera plików jest zamykana prawidłowo i jest wykonywana migawka kopii zapasowej w chmurze woluminów na urządzeniu StorSimple. Woluminy StorSimple są przełączane do trybu failover na urządzeniu wirtualnym, maszyna wirtualna repliki jest przenoszona na platformę Azure, a woluminy są dołączone do maszyny wirtualnej.

#### <a name="to-perform-a-planned-failover"></a>Aby wykonać planowaną pracę w trybie failover
1. W Azure Portal wybierz pozycję **usługi Recovery Services** magazynu &gt; **Site Recovery** &gt; **recoveryplan_name** utworzonych dla maszyny wirtualnej serwera plików.
1. W bloku plan odzyskiwania kliknij pozycję **więcej** &gt; **planowanej pracy w trybie failover**.  

   ![Zrzut ekranu, który wyróżnia opcje planowanej pracy w trybie failover i trybu failover.](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. W bloku **Potwierdź planowane przejście w tryb failover** wybierz lokalizację źródłową i docelową, a następnie wybierz pozycję Sieć docelowa, a następnie kliknij ikonę Sprawdź ✓, aby rozpocząć proces przełączania do trybu failover.
1. Po utworzeniu maszyn wirtualnych repliki znajdują się one w stanie oczekiwania na zatwierdzenie. Kliknij przycisk **Zatwierdź** , aby zatwierdzić przejście w tryb failover.
1. Po zakończeniu replikacji maszyny wirtualne są uruchamiane w lokalizacji pomocniczej.

## <a name="perform-a-failover"></a>Przełączenie w tryb failover
W przypadku nieplanowanego przejścia w tryb failover woluminy StorSimple są przełączane do urządzenia wirtualnego, a maszyna wirtualna zostanie przełączona na platformę Azure, a woluminy zostaną dołączone do maszyny wirtualnej.

#### <a name="to-perform-a-failover"></a>Aby przeprowadzić przejście w tryb failover
1. W Azure Portal wybierz pozycję **usługi Recovery Services** magazynu &gt; **Site Recovery** &gt; **recoveryplan_name** utworzonych dla maszyny wirtualnej serwera plików.
1. W bloku plan odzyskiwania kliknij pozycję **więcej** &gt; **trybu failover**.  
1. W bloku **Potwierdź tryb failover** wybierz lokalizację źródłową i docelową.
1. Wybierz pozycję **Zamknij maszyny wirtualne i zsynchronizuj najnowsze dane** , aby określić, że Site Recovery powinna próbować zamknąć chronioną maszynę wirtualną i zsynchronizować dane, aby Najnowsza wersja danych została przełączona w tryb failover.
1. Po przejściu w tryb failover maszyny wirtualne znajdują się w stanie oczekiwania na zatwierdzenie. Kliknij przycisk **Zatwierdź** , aby zatwierdzić przejście w tryb failover.


## <a name="perform-a-failback"></a>Wykonywanie powrotu po awarii
Podczas powrotu po awarii kontenery woluminów StorSimple są przełączane w tryb failover z powrotem do urządzenia fizycznego po wykonaniu kopii zapasowej.

#### <a name="to-perform-a-failback"></a>Aby przeprowadzić powrót po awarii
1. W Azure Portal wybierz pozycję **usługi Recovery Services** magazynu &gt; **Site Recovery** &gt; **recoveryplan_name** utworzonych dla maszyny wirtualnej serwera plików.
1. W bloku plan odzyskiwania kliknij pozycję **więcej** &gt; **planowanej pracy w trybie failover**.  
1. Wybierz lokalizację źródłową i docelową, wybierz odpowiednie opcje synchronizacji danych i tworzenia maszyn wirtualnych.
1. Kliknij przycisk **OK** , aby uruchomić proces powrotu po awarii.
   
   ![Rozpocznij powrót po awarii](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Najlepsze rozwiązania
### <a name="capacity-planning-and-readiness-assessment"></a>Planowanie pojemności i Ocena gotowości
#### <a name="hyper-v-site"></a>Lokacja funkcji Hyper-V
Użyj [Narzędzia planista wydajności użytkownika](https://www.microsoft.com/download/details.aspx?id=39057) , aby zaprojektować infrastrukturę serwera, magazynu i sieci dla środowiska funkcji Hyper-V Replica.

#### <a name="azure"></a>Azure
[Narzędzie do oceny gotowości maszyn wirtualnych platformy Azure](https://azure.microsoft.com/downloads/vm-readiness-assessment/) można uruchomić na maszynach wirtualnych, aby upewnić się, że są one zgodne z maszynami wirtualnymi platformy Azure i usługami Azure Site Recovery. Narzędzie do oceny gotowości sprawdza konfiguracje maszyn wirtualnych i ostrzega, gdy konfiguracje nie są zgodne z platformą Azure. Na przykład generuje ostrzeżenie, jeśli dysk C: jest większy niż 127 GB.

Planowanie pojemności składa się z co najmniej dwóch ważnych procesów:

   - Mapowanie lokalnych maszyn wirtualnych funkcji Hyper-V na rozmiary maszyn wirtualnych platformy Azure (np. A6, A8, A9).
   - Określanie wymaganej przepustowości Internetu.

## <a name="limitations"></a>Ograniczenia
- Obecnie tylko 1 Urządzenie StorSimple można przełączyć w tryb failover (do jednego urządzenia w chmurze StorSimple). Scenariusz serwera plików, który obejmuje kilka urządzeń StorSimple, nie jest jeszcze obsługiwany.
- Jeśli podczas włączania ochrony maszyny wirtualnej wystąpi błąd, upewnij się, że obiekty docelowe iSCSI zostały rozłączone.
- Wszystkie kontenery woluminów, które zostały pogrupowane ze względu na zasady tworzenia kopii zapasowych obejmujące między kontenerami woluminów, zostaną przełączone w tryb failover.
- Wszystkie woluminy w wybranych kontenerach woluminów zostaną przełączone w tryb failover.
- Woluminy dodawane do ponad 64 TB nie mogą być przełączone w tryb failover, ponieważ maksymalna pojemność pojedynczego urządzenia w chmurze StorSimple wynosi 64 TB.
- Jeśli planowane/nieplanowane przejście w tryb failover zakończy się niepowodzeniem, a maszyny wirtualne zostaną utworzone na platformie Azure, nie należy czyścić maszyn wirtualnych. Zamiast tego należy przeprowadzić powrót po awarii. Po usunięciu maszyn wirtualnych nie można ponownie włączyć lokalnych maszyn wirtualnych.
- Jeśli nie widzisz woluminów w trybie failover, przejdź do maszyn wirtualnych, Otwórz przystawkę Zarządzanie dyskami, przeskanuj dyski ponownie, a następnie Przełącz je do trybu online.
- W niektórych przypadkach litery dysku w witrynie odzyskiwania po awarii mogą być inne niż litery w środowisku lokalnym. W takim przypadku konieczne będzie ręczne rozwiązanie problemu po zakończeniu pracy w trybie failover.
- Limit czasu zadania trybu failover: skrypt StorSimple będzie przekroczyć limit czasu, Jeśli przełączenie w tryb failover kontenerów woluminów trwa dłużej niż limit Azure Site Recovery na skrypt (obecnie 120 minut).
- Przekroczono limit czasu zadania tworzenia kopii zapasowej: skrypt StorSimple przekracza limit czasu, jeśli kopia zapasowa woluminów trwa dłużej niż wartość limitu Azure Site Recovery dla skryptu (obecnie 120 minut).
   
  > [!IMPORTANT]
  > Ręcznie uruchom kopię zapasową z Azure Portal a następnie ponownie uruchom plan odzyskiwania.
   
- Sklonowany limit czasu zadania: StorSimple skrypt przekracza limit czasu, jeśli klonowanie woluminów trwa dłużej niż wartość limitu Azure Site Recovery na skrypt (obecnie 120 minut).
- Błąd synchronizacji czasu: skrypty StorSimple wystąpiły błędy podczas wymawiania, że wykonywanie kopii zapasowych nie powiodło się, mimo że kopia zapasowa została zakończona pomyślnie w portalu. Możliwą przyczyną może być brak synchronizacji czasu urządzenia StorSimple z bieżącym czasem w strefie czasowej.
   
  > [!IMPORTANT]
  > Zsynchronizuj czas urządzenia z bieżącą godziną w strefie czasowej.
   
- Błąd trybu failover urządzenia: skrypt StorSimple może się nie powieść, jeśli jest dostępny tryb failover urządzenia, gdy plan odzyskiwania jest uruchomiony.
   
  > [!IMPORTANT]
  > Uruchom ponownie plan odzyskiwania po zakończeniu przełączania urządzenia w tryb failover.


## <a name="summary"></a>Podsumowanie
Za pomocą Azure Site Recovery można utworzyć kompletny zautomatyzowany plan odzyskiwania po awarii dla maszyny wirtualnej serwera plików z udziałami plików hostowanymi w magazynie StorSimple. Pracę w trybie failover można zainicjować w ciągu kilku sekund od dowolnego miejsca w przypadku przerwania działania i uruchomić aplikację w kilka minut.