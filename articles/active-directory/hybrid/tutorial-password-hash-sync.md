---
title: 'Samouczek: Integrowanie pojedynczego lasu usługi AD z platformą Azure przy użyciu PHS'
description: W tym samouczku pokazano, jak skonfigurować środowisko tożsamości hybrydowej przy użyciu funkcji synchronizacji skrótów haseł.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96176124"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Samouczek: Integrowanie pojedynczego lasu usługi AD przy użyciu funkcji synchronizacji skrótów haseł (PHS)

![Utwórz](media/tutorial-password-hash-sync/diagram.png)

Poniższy samouczek przeprowadzi Cię przez proces tworzenia hybrydowego środowiska tożsamości przy użyciu funkcji synchronizacji skrótów haseł.  Tego środowiska można następnie użyć do testowania lub w celu uzyskania bardziej znajomości sposobu działania tożsamości hybrydowej.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne niezbędne do wykonania kroków tego samouczka
- Komputer z zainstalowaną [funkcją Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview).  Zaleca się wykonanie tych czynności na komputerze z systemem [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) lub [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- [Zewnętrzna karta sieciowa](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) umożliwiająca maszynie wirtualnej komunikację z Internetem.
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free)
- Kopia systemu Windows Server 2016

> [!NOTE]
> W tym samouczku używane są skrypty programu PowerShell w celu jak najszybszego utworzenia środowiska samouczka.  W każdym ze skryptów są używane zmienne zadeklarowane na początku skryptu.  Można i należy zmienić te zmienne na zgodne z używanym środowiskiem.
>
>Używane skrypty tworzą ogólne środowisko usługi Active Directory przed zainstalowaniem narzędzia Azure AD Connect.  Są one odpowiednie dla wszystkich samouczków.
>
> Kopie skryptów programu PowerShell używanych w tym samouczku są dostępne w usłudze GitHub — [tutaj](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Pierwszą rzeczą, jaką należy zrobić, aby skonfigurować i uruchomić środowisko tożsamości hybrydowej, jest utworzenie maszyny wirtualnej, która będzie służyć jako lokalny serwer usługi Active Directory.  Wykonaj następujące czynności:

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
Maszyna wirtualna jest gotowa. Przed zainstalowaniem usługi Active Directory należy wykonać jeszcze kilka czynności.  Należy zmienić nazwę maszyny wirtualnej, ustawić statyczny adres IP i informacje DNS oraz zainstalować narzędzia administracji zdalnej serwera.   Wykonaj następujące czynności:

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
Maszyna wirtualna jest już utworzona, ma już właściwą nazwę i adres statyczny. Czas zainstalować i skonfigurować usługi Active Directory Domain Services.  Wykonaj następujące czynności:

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
Środowisko usługi Active Directory jest już gotowe. Potrzebujemy konta testowego.  To konto zostanie utworzone w lokalnym środowisku usługi AD, a następnie zsynchronizowane z usługą Azure AD.  Wykonaj następujące czynności:

1. Otwórz program PowerShell ISE jako administrator.
2. Uruchom poniższy skrypt.

```powershell 
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
Teraz należy utworzyć dzierżawę usługi Azure AD, aby umożliwić synchronizowanie użytkowników z chmurą.  Aby utworzyć nową dzierżawę usługi Azure AD, wykonaj następujące czynności.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta, które ma subskrypcję platformy Azure.
2. Wybierz **ikonę plusa (+)** i wyszukaj pozycję **Azure Active Directory**.
3. W wynikach wyszukiwania wybierz pozycję **Azure Active Directory**.
4. Wybierz przycisk **Utwórz**.</br>
![Zrzut ekranu pokazujący sposób tworzenia dzierżawy usługi Azure AD.](media/tutorial-password-hash-sync/create1.png)</br>
5. Podaj **nazwę organizacji** wraz z **początkową nazwą domeny**. Następnie wybierz pozycję **Utwórz**. Spowoduje to utworzenie katalogu.
6. Po zakończeniu kliknij link **tutaj**, aby zarządzać katalogiem.

## <a name="create-a-global-administrator-in-azure-ad"></a>Tworzenie administratora globalnego w usłudze Azure AD
Dzierżawa usługi Azure AD jest już gotowa. Utworzymy konto administratora globalnego.  To konto posłuży do utworzenia konta Azure AD Connector podczas instalacji programu Azure AD Connect.  Konto łącznika usługi Azure AD służy do zapisywania informacji w usłudze Azure AD.   Aby utworzyć konto administratora globalnego, wykonaj następujące czynności.

1.  W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.</br>
![Zrzut ekranu pokazujący opcję użytkownika wybraną w sekcji Zarządzanie, w której tworzysz administratora globalnego w usłudze Azure AD.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Wybierz pozycję **Wszyscy użytkownicy**, a następnie pozycję **+ Nowy użytkownik**.
3.  Podaj nazwę i nazwę użytkownika dla tego użytkownika. Będzie to administrator globalny dzierżawy. Zmień też **rolę Katalog** na **Administrator globalny**. Możesz również wyświetlić hasło tymczasowe. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.</br>
![Zrzut ekranu pokazujący przycisk tworzenia wybierany podczas tworzenia administratora globalnego w usłudze Azure AD.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Po zakończeniu otwórz nowe okno przeglądarki internetowej i zaloguj się na stronie myapps.microsoft.com przy użyciu nowego konta administratora globalnego oraz hasła tymczasowego.
5. Zmień hasło administratora globalnego na inne, które zapamiętasz.

## <a name="download-and-install-azure-ad-connect"></a>Pobieranie i instalowanie programu Azure AD Connect
Nadszedł czas, aby pobrać i zainstalować program Azure AD Connect.  Po zainstalowaniu go przejdziemy przez konfigurację ekspresową.  Wykonaj następujące czynności:

1. Pobierz [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Przejdź do pozycji **AzureADConnect.msi** i kliknij ją dwukrotnie.
3. Na ekranie powitalnym zaznacz pole wyrażenia zgody na warunki licencji i kliknij pozycję **Kontynuuj**.  
4. Na ekranie Ustawienia ekspresowe kliknij polecenie **Użyj ustawień ekspresowych**.</br>  
![Zrzut ekranu, na którym jest wyświetlany ekran Ustawienia ekspresowe i przycisk Użyj ustawień ekspresowych.](media/tutorial-password-hash-sync/express1.png)</br>
5. Na ekranie Łączenie z usługą Azure AD podaj nazwę użytkownika i hasło administratora globalnego usługi Azure AD. Kliknij przycisk **Dalej**.  
6. Na ekranie Łączenie z usługami AD DS wprowadź nazwę użytkownika i hasło konta administratora przedsiębiorstwa. Kliknij przycisk **Dalej**.  
7. Na ekranie Wszystko gotowe do skonfigurowania kliknij pozycję **Zainstaluj**.
8. Po zakończeniu instalacji kliknij przycisk **Zakończ**.
9. Po zakończeniu instalacji wyloguj się, a następnie zaloguj się ponownie przed użyciem narzędzia Synchronization Service Manager lub Synchronization Rule Editor.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Weryfikowanie utworzenia użytkowników i przeprowadzania synchronizacji
Teraz potwierdzimy, że użytkownicy znajdujący się w katalogu lokalnym zostali zsynchronizowani i istnieją teraz w zewnętrznej dzierżawie usługi Azure AD.  Ukończenie tego procesu może potrwać kilka godzin.  Aby potwierdzić, że użytkownicy zostali zsynchronizowani, wykonaj następujące czynności.


1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta, które ma subskrypcję platformy Azure.
2. W obszarze po lewej stronie wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzanie** wybierz pozycję **Użytkownicy**.
4. Zweryfikuj, czy nowi użytkownicy są widoczni w dzierżawie</br>
![Synchronizacja](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testowanie logowania się przy użyciu jednego z kont użytkowników

1. Przejdź do [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Zaloguj się przy użyciu konta użytkownika utworzonego w nowej dzierżawie.  Należy zalogować się przy użyciu następującego formatu: (user@domain.onmicrosoft.com). Użyj tego samego hasła, za pomocą którego użytkownik loguje się lokalnie.</br>
   ![Weryfikacja](media/tutorial-password-hash-sync/verify1.png)</br>

W ten sposób pomyślnie skonfigurowano środowisko tożsamości hybrydowej, które można wykorzystać do testowania lub do bliższego zapoznania się z możliwościami platformy Azure.

## <a name="next-steps"></a>Następne kroki


- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)|