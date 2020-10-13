---
title: Samouczek — podstawowe Active Directory środowiska lokalnego i usługi Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceffc928a0cf8313723ff6193d9a6a63f8465f90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91266313"
---
# <a name="tutorial-basic-active-directory-environment"></a>Samouczek: podstawowe środowisko Active Directory

Ten samouczek przeprowadzi Cię przez proces tworzenia podstawowego środowiska Active Directory. 

![Diagram przedstawiający podstawowe środowisko Azure A D.](media/tutorial-single-forest/diagram1.png)

Możesz użyć środowiska utworzonego w samouczku, aby przetestować różne aspekty scenariuszy tożsamości hybrydowych i będzie to konieczne w przypadku niektórych samouczków.  Jeśli masz już istniejące Środowisko Active Directory, możesz użyć go jako substytutu.  Te informacje są udostępniane osobom, które nie zaczynają się od niczego.

Ten samouczek składa się z
## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne niezbędne do wykonania kroków tego samouczka
- Komputer z zainstalowaną [funkcją Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview).  Zaleca się wykonanie tych czynności na komputerze z systemem [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) lub [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- [Zewnętrzna karta sieciowa](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) umożliwiająca maszynie wirtualnej komunikację z Internetem.
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free)
- Kopia systemu Windows Server 2016
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> W tym samouczku używane są skrypty programu PowerShell w celu jak najszybszego utworzenia środowiska samouczka.  W każdym ze skryptów są używane zmienne zadeklarowane na początku skryptu.  Można i należy zmienić te zmienne na zgodne z używanym środowiskiem.
>
>Skrypty używane do tworzenia ogólnego środowiska Active Directory przed zainstalowaniem agenta aprowizacji Azure AD Connect w chmurze.  Są one odpowiednie dla wszystkich samouczków.
>
> Kopie skryptów programu PowerShell używanych w tym samouczku są dostępne w usłudze GitHub — [tutaj](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
W pierwszej kolejności, w której należy wykonać nasze hybrydowe środowisko tożsamości, należy utworzyć maszynę wirtualną, która będzie używana jako serwer lokalnego Active Directory.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Finalizowanie wdrożenia systemu operacyjnego
Aby zakończyć tworzenie maszyny wirtualnej, należy zakończyć instalację systemu operacyjnego.

1. Menedżer funkcji Hyper-V: kliknij dwukrotnie maszynę wirtualną
2. Kliknij przycisk Uruchom.
3. Zostanie wyświetlony monit „Naciśnij dowolny klawisz, aby dokonać rozruchu z dysku CD lub DVD”. Zrób to.
4. Na ekranie uruchamiania systemu Windows Server wybierz język i kliknij przycisk **Dalej**.
5. Kliknij pozycję **Zainstaluj teraz**.
6. Wprowadź klucz licencji i kliknij przycisk **Dalej**.
7. Zaznacz opcję **Akceptuję postanowienia licencyjne, a następnie kliknij przycisk **Dalej**.
8. Wybierz **niestandardowe: tylko Zainstaluj system Windows (Zaawansowane)**
9. Kliknij przycisk **dalej** .
10. Po zakończeniu instalacji uruchom ponownie maszynę wirtualną, zaloguj się i uruchom aktualizacje systemu Windows, aby upewnić się, że maszyna wirtualna została zaktualizowana.  Zainstaluj najnowsze aktualizacje.

## <a name="install-active-directory-prerequisites"></a>Instalowanie wymagań wstępnych usługi Active Directory
Teraz, gdy masz już maszynę wirtualną, przed zainstalowaniem Active Directory należy wykonać kilka czynności.  Oznacza to, że należy zmienić nazwę maszyny wirtualnej, ustawić statyczny adres IP i informacje DNS oraz zainstalować narzędzia administracji zdalnej serwera.   Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Tworzenie środowiska usługi Windows Server AD
Teraz, gdy masz utworzoną maszynę wirtualną, a jej nazwa została zmieniona i ma statyczny adres IP, możesz go zainstalować i skonfigurować Active Directory Domain Services.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Tworzenie użytkownika usługi Windows Server AD
Teraz, gdy masz nasze środowisko Active Directory, musisz być kontem testowym.  To konto zostanie utworzone w lokalnym środowisku usługi AD, a następnie zsynchronizowane z usługą Azure AD.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Tworzenie dzierżawy usługi Azure AD
Teraz musisz utworzyć dzierżawę usługi Azure AD, aby umożliwić synchronizację naszych użytkowników z chmurą.  Aby utworzyć nową dzierżawę usługi Azure AD, wykonaj następujące czynności.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta, które ma subskrypcję platformy Azure.
2. Wybierz **ikonę plusa (+)** i wyszukaj pozycję **Azure Active Directory**.
3. W wynikach wyszukiwania wybierz pozycję **Azure Active Directory**.
4. Wybierz przycisk **Utwórz**.</br>
![Zrzut ekranu przedstawiający stronę Azure Active Directory w Azure Portal.](media/tutorial-single-forest/create1.png)</br>
5. Podaj **nazwę organizacji** wraz z **początkową nazwą domeny**. Następnie wybierz przycisk **Utwórz**. Spowoduje to utworzenie katalogu.
6. Po zakończeniu kliknij link **tutaj**, aby zarządzać katalogiem.

## <a name="create-a-global-administrator-in-azure-ad"></a>Tworzenie administratora globalnego w usłudze Azure AD
Teraz, gdy masz już dzierżawę usługi Azure AD, utworzysz konto administratora globalnego.  Aby utworzyć konto administratora globalnego, wykonaj następujące czynności.

1.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.</br>
![Zrzut ekranu pokazujący menu "przegląd" z wybranymi "użytkownikami".](media/tutorial-single-forest/administrator1.png)</br>
2.  Wybierz pozycję **Wszyscy użytkownicy**, a następnie pozycję **+ Nowy użytkownik**.
3.  Podaj nazwę i nazwę użytkownika dla tego użytkownika. Będzie to administrator globalny dzierżawy. Zmień też **rolę Katalog** na **Administrator globalny**. Możesz również wyświetlić hasło tymczasowe. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.</br>
![Tworzenie](media/tutorial-single-forest/administrator2.png)</br>
4. Po zakończeniu otwórz nowe okno przeglądarki internetowej i zaloguj się na stronie myapps.microsoft.com przy użyciu nowego konta administratora globalnego oraz hasła tymczasowego.
5. Zmień hasło administratora globalnego na inne, które zapamiętasz.

## <a name="optional--additional-server-and-forest"></a>Opcjonalnie: dodatkowy serwer i Las
Poniżej znajduje się sekcja opcjonalna, która zawiera instrukcje tworzenia dodatkowego serwera i lasu.  Ta wartość może być używana w niektórych bardziej zaawansowanych samouczkach, takich jak [pilotaż dla Azure AD Connect na potrzeby aprowizacji w chmurze](tutorial-pilot-aadc-aadccp.md).

Jeśli potrzebujesz tylko dodatkowego serwera, możesz zatrzymać po kroku — **Tworzenie maszyny wirtualnej** i dołączyć serwer do istniejącej domeny, która została utworzona powyżej.  

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Finalizowanie wdrożenia systemu operacyjnego
Aby zakończyć tworzenie maszyny wirtualnej, należy zakończyć instalację systemu operacyjnego.

1. Menedżer funkcji Hyper-V: kliknij dwukrotnie maszynę wirtualną
2. Kliknij przycisk Uruchom.
3. Zostanie wyświetlony monit „Naciśnij dowolny klawisz, aby dokonać rozruchu z dysku CD lub DVD”. Zrób to.
4. Na ekranie uruchamiania systemu Windows Server wybierz język i kliknij przycisk **Dalej**.
5. Kliknij pozycję **Zainstaluj teraz**.
6. Wprowadź klucz licencji i kliknij przycisk **Dalej**.
7. Zaznacz opcję **Akceptuję postanowienia licencyjne, a następnie kliknij przycisk **Dalej**.
8. Wybierz **niestandardowe: tylko Zainstaluj system Windows (Zaawansowane)**
9. Kliknij przycisk **dalej** .
10. Po zakończeniu instalacji uruchom ponownie maszynę wirtualną, zaloguj się i uruchom aktualizacje systemu Windows, aby upewnić się, że maszyna wirtualna została zaktualizowana.  Zainstaluj najnowsze aktualizacje.

### <a name="install-active-directory-prerequisites"></a>Instalowanie wymagań wstępnych usługi Active Directory
Teraz, gdy masz już maszynę wirtualną, przed zainstalowaniem Active Directory należy wykonać kilka czynności.  Oznacza to, że należy zmienić nazwę maszyny wirtualnej, ustawić statyczny adres IP i informacje DNS oraz zainstalować narzędzia administracji zdalnej serwera.   Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Tworzenie środowiska usługi Windows Server AD
Teraz, gdy masz utworzoną maszynę wirtualną, a jej nazwa została zmieniona i ma statyczny adres IP, możesz go zainstalować i skonfigurować Active Directory Domain Services.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Tworzenie użytkownika usługi Windows Server AD
Teraz, gdy masz nasze środowisko Active Directory, musisz być kontem testowym.  To konto zostanie utworzone w lokalnym środowisku usługi AD, a następnie zsynchronizowane z usługą Azure AD.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Podsumowanie
Teraz masz środowisko, którego można użyć do istniejących samouczków i przetestowania dodatkowych funkcji zapewniania obsługi administracyjnej w chmurze.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)