---
title: Konfigurowanie grupy dostępności zawsze włączone na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell | Microsoft Docs
description: W tym samouczku użyto zasobów utworzonych przy użyciu klasycznego modelu wdrażania. Program PowerShell umożliwia utworzenie zawsze włączonych grup dostępności na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 7f20d79ea353830b41290c7b91d8d1de2b1b3abe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014863"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurowanie grupy dostępności zawsze włączone na maszynie wirtualnej platformy Azure przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Klasyczny: interfejs użytkownika](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasyczny: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Przed rozpoczęciem należy zastanowić się, że możesz teraz wykonać to zadanie w modelu usługi Azure Resource Manager. Zalecamy model usługi Azure Resource Manager dla nowych wdrożeń. [SQL Server zobacz zawsze włączone grupy dostępności na maszynach wirtualnych platformy Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).

> [!IMPORTANT]
> Zalecamy, aby większość nowych wdrożeń korzystała z modelu Menedżer zasobów. Platforma Azure ma dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [Resource Manager i model klasyczny](../../../azure-resource-manager/management/deployment-models.md). Ten artykuł dotyczy klasycznego modelu wdrożenia.

Usługa Azure Virtual Machines może pomóc administratorom baz danych obniżyć koszty systemu SQL Server o wysokiej dostępności. W tym samouczku pokazano, jak zaimplementować grupę dostępności przy użyciu programu SQL Server zawsze na kompleksowym poziomie w środowisku platformy Azure. Na końcu samouczka SQL Server zawsze włączone rozwiązanie na platformie Azure będzie zawierać następujące elementy:

* Sieć wirtualna, która zawiera wiele podsieci, w tym fronton i podsieć zaplecza.
* Kontroler domeny z domeną Active Directory.
* Dwie SQL Server maszyny wirtualne wdrożone w podsieci zaplecza i przyłączone do domeny Active Directory.
* Klaster trybu failover systemu Windows z trzema węzłami z modelem kworum większość węzłów.
* Grupa dostępności z dwiema replikami zatwierdzania synchronicznego bazy danych dostępności.

Ten scenariusz jest dobrym rozwiązaniem dla uproszczenia na platformie Azure, a nie pod względem kosztów i innych czynników. Można na przykład zminimalizować liczbę maszyn wirtualnych dla grupy dostępności z dwiema replikami, aby zaoszczędzić w godzinach obliczeniowych na platformie Azure przy użyciu kontrolera domeny jako monitora udostępniania plików kworum w klastrze trybu failover z dwoma węzłami. Ta metoda zmniejsza liczbę maszyn wirtualnych o jedną z powyższej konfiguracji.

Ten samouczek jest przeznaczony do wyświetlania czynności, które są wymagane do skonfigurowania opisanego powyżej rozwiązania, bez opracowywania szczegółowych informacji dotyczących poszczególnych kroków. W związku z tym zamiast przedstawiać kroki konfiguracji graficznego interfejsu użytkownika, program korzysta z skryptów programu PowerShell, aby szybko wykonać każdy krok. W tym samouczku założono następujące kwestie:

* Masz już konto platformy Azure z subskrypcją maszyny wirtualnej.
* Zainstalowano [polecenia cmdlet Azure PowerShell](/powershell/azure/overview).
* Masz już pełną wiedzę na temat zawsze dostępnych grup dostępności dla rozwiązań lokalnych. Aby uzyskać więcej informacji, zobacz [Always On Availability groups (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Nawiązywanie połączenia z subskrypcją platformy Azure i tworzenie sieci wirtualnej
1. W oknie programu PowerShell na komputerze lokalnym zaimportuj moduł platformy Azure, Pobierz plik ustawień publikowania na swoją maszynę i Połącz sesję programu PowerShell z subskrypcją platformy Azure, importując pobrane ustawienia publikowania.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Polecenie **Get-AzurePublishSettingsFile** automatycznie generuje certyfikat zarządzania z platformą Azure i pobiera go na komputer. Przeglądarka zostanie automatycznie otwarta i zostanie wyświetlony monit o wprowadzenie poświadczeń konto Microsoft dla subskrypcji platformy Azure. Pobrany plik **publishsettings** zawiera wszystkie informacje potrzebne do zarządzania subskrypcją platformy Azure. Po zapisaniu tego pliku w katalogu lokalnym zaimportuj go za pomocą polecenia **Import-AzurePublishSettingsFile** .

   > [!NOTE]
   > Plik. publishsettings zawiera Twoje poświadczenia (niekodowane), które są używane do administrowania subskrypcjami i usługami platformy Azure. Najlepszym rozwiązaniem w zakresie zabezpieczeń dla tego pliku jest przechowywanie go tymczasowo poza katalogami źródłowymi (na przykład w folderze Libraries\Documents), a następnie usunięcie go po zakończeniu importowania. Złośliwy użytkownik, który uzyskuje dostęp do pliku. publishsettings, może edytować, tworzyć i usuwać usługi platformy Azure.

2. Zdefiniuj serię zmiennych, które będą używane do tworzenia infrastruktury IT w chmurze.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Zwróć uwagę na następujące kwestie, aby upewnić się, że polecenia zakończą się później:

   * Zmienne **$storageAccountName** i **$dcServiceName** muszą być unikatowe, ponieważ są używane do identyfikacji konta magazynu w chmurze i serwera w chmurze odpowiednio w Internecie.
   * Nazwy określone dla zmiennych **$affinityGroupName** i **$virtualNetworkName** są konfigurowane w dokumencie konfiguracji sieci wirtualnej, który będzie później używany.
   * **$sqlImageName** określa zaktualizowaną nazwę obrazu maszyny wirtualnej, który zawiera SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Dla uproszczenia **firma Contoso! 000** to to samo hasło, które jest używane w całym samouczku.

3. Utwórz grupę koligacji.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Utwórz sieć wirtualną przez zaimportowanie pliku konfiguracji.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Plik konfiguracji zawiera następujący dokument XML. W skrócie określa sieć wirtualną o nazwie **ContosoNET** w grupie koligacji o nazwie **ContosoAG**. Ma przestrzeń adresową **10.10.0.0/16** i ma dwie podsieci, **10.10.1.0/24** i **10.10.2.0/24**, które są odpowiednio podsiecią i podsiecią wsteczną. Podsieć z podsiecią to miejsce, w którym można umieścić aplikacje klienckie, takie jak program Microsoft SharePoint. Podsieć jest miejscem, w którym zostaną umieszczone SQL Server maszyny wirtualne. Jeśli zmienisz zmienne **$affinityGroupName** i **$virtualNetworkName** wcześniej, musisz również zmienić odpowiednie nazwy poniżej.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Utwórz konto magazynu skojarzone z utworzoną grupą koligacji i ustaw je jako bieżące konto magazynu w ramach subskrypcji.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Utwórz serwer kontrolera domeny w nowej usłudze w chmurze i zestawie dostępności.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Te polecenia potokowe wykonują następujące czynności:

   * **New-AzureVMConfig** tworzy konfigurację maszyny wirtualnej.
   * Polecenie **Add-AzureProvisioningConfig** zapewnia parametry konfiguracji autonomicznego serwera systemu Windows.
   * Polecenie **Add-AzureDataDisk** dodaje dysk danych, który będzie używany do przechowywania danych Active Directory, z opcją buforowania ustawionym na none.
   * **New-AzureVM** tworzy nową usługę w chmurze i tworzy nową maszynę wirtualną platformy Azure w nowej usłudze w chmurze.

7. Poczekaj, aż Nowa maszyna wirtualna będzie w pełni zainicjowana, i Pobierz plik pulpitu zdalnego do katalogu roboczego. Ponieważ nowa maszyna wirtualna platformy Azure zajmuje dużo czasu, `while` Pętla kontynuuje sondowanie nowej maszyny wirtualnej, dopóki nie będzie gotowa do użycia.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Pomyślnie zainicjowano teraz serwer kontrolera domeny. Następnie skonfigurujesz domenę Active Directory na tym serwerze kontrolera domeny. Pozostaw otwarte okno programu PowerShell na komputerze lokalnym. Użyjesz go ponownie później, aby utworzyć dwie SQL Server maszyny wirtualne.

## <a name="configure-the-domain-controller"></a>Konfiguruj kontroler domeny
1. Połącz się z serwerem kontrolera domeny przez uruchomienie pliku pulpitu zdalnego. Użyj nazwy użytkownika i hasła administratora komputera **contoso! 000**, która została określona podczas tworzenia nowej maszyny wirtualnej.
2. Otwórz okno programu PowerShell w trybie administratora.
3. Uruchom następujące polecenie **DCPROMO.EXE** , aby skonfigurować domenę **Corp.contoso.com** z katalogami danych na dysku M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Po zakończeniu wykonywania polecenia maszyna wirtualna zostanie automatycznie uruchomiona ponownie.

4. Ponownie nawiąż połączenie z serwerem kontrolera domeny, uruchamiając plik pulpitu zdalnego. Tym razem Zaloguj się jako **CORP\Administrator.**.
5. Otwórz okno programu PowerShell w trybie administratora i zaimportuj moduł Active Directory PowerShell przy użyciu następującego polecenia:

        Import-Module ActiveDirectory

6. Uruchom następujące polecenia, aby dodać trzech użytkowników do domeny.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** służy do konfigurowania wszystkich elementów związanych z wystąpieniami usługi SQL Server, klastrem trybu failover i grupą dostępności. **CORP\SQLSvc1** i **CORP\SQLSvc2** są używane jako konta usługi SQL Server dla dwóch maszyn wirtualnych SQL Server.
7. Następnie uruchom następujące polecenia, aby dać **CORP\Install** uprawnienia do tworzenia obiektów komputerów w domenie.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Określony powyżej identyfikator GUID jest identyfikatorem GUID dla typu obiektu komputera. Konto **CORP\Install** wymaga uprawnienia **Odczyt wszystkich właściwości** i **Tworzenie obiektów komputerów** do tworzenia aktywnych obiektów bezpośrednich dla klastra trybu failover. Uprawnienie **Odczyt wszystkich właściwości** jest już domyślnie przyznawane CORP\Install, więc nie musisz przyznawać go jawnie. Aby uzyskać więcej informacji dotyczących uprawnień, które są konieczne do utworzenia klastra trybu failover, zobacz [klaster trybu failover — przewodnik krok po kroku: Konfigurowanie kont w Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Po zakończeniu konfigurowania Active Directory i obiektów użytkownika utworzysz dwie SQL Server maszyny wirtualne i Dołącz je do tej domeny.

## <a name="create-the-sql-server-vms"></a>Tworzenie maszyn wirtualnych SQL Server
1. Kontynuuj korzystanie z okna programu PowerShell, które jest otwarte na komputerze lokalnym. Zdefiniuj następujące dodatkowe zmienne:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    Adres IP **10.10.0.4** jest zwykle przypisywany do pierwszej maszyny wirtualnej, która została utworzona w podsieci **10.10.0.0/16** sieci wirtualnej platformy Azure. Należy sprawdzić, czy jest to adres serwera kontrolera domeny, uruchamiając **polecenie ipconfig**.
2. Uruchom następujące polecenia potokowe, aby utworzyć pierwszą maszynę wirtualną w klastrze trybu failover o nazwie **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Zwróć uwagę na następujące polecenie:

   * **New-AzureVMConfig** tworzy konfigurację maszyny wirtualnej o żądanej nazwie zestawu dostępności. Kolejne maszyny wirtualne zostaną utworzone z taką samą nazwą zestawu dostępności, aby były one przyłączone do tego samego zestawu dostępności.
   * **Dodatek Add-AzureProvisioningConfig** łączy maszynę wirtualną z utworzoną domeną Active Directory.
   * **Set-AzureSubnet** umieszcza maszynę wirtualną w podsieci zaplecza.
   * **New-AzureVM** tworzy nową usługę w chmurze i tworzy nową maszynę wirtualną platformy Azure w nowej usłudze w chmurze. Parametr **DnsSettings** określa, że serwer DNS dla serwerów w nowej usłudze w chmurze ma adres IP **10.10.0.4**. Jest to adres IP serwera kontrolera domeny. Ten parametr jest niezbędny do włączenia nowych maszyn wirtualnych w usłudze w chmurze w celu pomyślnego dołączenia do domeny Active Directory. Bez tego parametru należy ręcznie ustawić ustawienia protokołu IPv4 na maszynie wirtualnej tak, aby używały serwera kontrolera domeny jako podstawowego serwera DNS po aprowizacji maszyny wirtualnej, a następnie dołączyć maszynę wirtualną do domeny Active Directory.
3. Uruchom następujące polecenia potokowe, aby utworzyć SQL Server maszyny wirtualne o nazwach **ContosoSQL1** i **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Należy pamiętać o następujących kwestiach dotyczących poleceń:

   * Polecenie **New-AzureVMConfig** używa tej samej nazwy zestawu dostępności co serwer kontrolera domeny i używa obrazu SQL Server 2012 Service Pack 1 Enterprise Edition w galerii maszyn wirtualnych. Ustawia również dysk systemu operacyjnego na tylko do odczytu (bez buforowania zapisu). Zalecamy migrowanie plików bazy danych na osobny dysk z danymi, który został dołączony do maszyny wirtualnej, i skonfigurowanie go bez buforowania odczytu lub zapisu. Następnym najlepszym rozwiązaniem jest usunięcie buforowania zapisu na dysku systemu operacyjnego, ponieważ nie można usunąć buforowania odczytu na dysku systemu operacyjnego.
   * **Dodatek Add-AzureProvisioningConfig** łączy maszynę wirtualną z utworzoną domeną Active Directory.
   * **Set-AzureSubnet** umieszcza maszynę wirtualną w podsieci zaplecza.
   * **Add-AzureEndpoint** dodaje punkty końcowe dostępu, dzięki czemu aplikacje klienckie mogą uzyskiwać dostęp do tych wystąpień SQL Server usług w Internecie. Różne porty są przekazywane do ContosoSQL1 i ContosoSQL2.
   * **New-AzureVM** tworzy nową maszynę wirtualną SQL Server w tej samej usłudze w chmurze co ContosoQuorum. Należy umieścić maszyny wirtualne w tej samej usłudze w chmurze, jeśli chcesz, aby były one w tym samym zestawie dostępności.
4. Poczekaj, aż wszystkie maszyny wirtualne zostaną w pełni zainicjowane, a każda maszyna wirtualna pobierze plik pulpitu zdalnego do katalogu roboczego. `for`Pętla przechodzi przez trzy nowe maszyny wirtualne i wykonuje polecenia wewnątrz nawiasów klamrowych najwyższego poziomu dla każdej z nich.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Maszyny wirtualne SQL Server są teraz inicjowane i uruchamiane, ale są instalowane z SQL Server z opcjami domyślnymi.

## <a name="initialize-the-failover-cluster-vms"></a>Inicjowanie maszyn wirtualnych klastra trybu failover
W tej sekcji należy zmodyfikować trzy serwery, które będą używane w klastrze trybu failover i instalacji SQL Server. W szczególności:

* Wszystkie serwery: należy zainstalować funkcję **klaster trybu failover** .
* Wszystkie serwery: należy dodać **CORP\Install** jako **administratora**komputera.
* Tylko ContosoSQL1 i ContosoSQL2: należy dodać **CORP\Install** jako rolę **sysadmin** w domyślnej bazie danych.
* Tylko ContosoSQL1 i ContosoSQL2: należy dodać **NT NT\SYSTEM** jako logowanie przy użyciu następujących uprawnień:

  * Zmień dowolną grupę dostępności
  * Połącz SQL
  * Wyświetl stan serwera
* Tylko ContosoSQL1 i ContosoSQL2: protokół **TCP** jest już włączony na maszynie wirtualnej SQL Server. Jednak nadal trzeba otworzyć Zaporę w celu uzyskania dostępu zdalnego SQL Server.

Teraz wszystko jest gotowe do rozpoczęcia. Począwszy od **ContosoQuorum**, wykonaj następujące kroki:

1. Połącz się z usługą **ContosoQuorum** , uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika **AzureAdmin** oraz hasła **contoso! 000**, które zostały określone podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie dołączone do **Corp.contoso.com**.
3. Przed kontynuowaniem Zaczekaj na zakończenie instalacji SQL Server.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję Klaster trybu failover systemu Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Install** jako administratora lokalnego.

        net localgroup administrators "CORP\Install" /Add
7. Wyloguj się z ContosoQuorum. Ten serwer jest teraz używany.

        logoff.exe

Następnie zainicjuj **ContosoSQL1** i **ContosoSQL2**. Wykonaj poniższe kroki, które są identyczne dla obu maszyn wirtualnych SQL Server.

1. Połącz się z dwoma maszynami wirtualnymi SQL Server, uruchamiając pliki pulpitu zdalnego. Użyj nazwy użytkownika **AzureAdmin** oraz hasła **contoso! 000**, które zostały określone podczas tworzenia maszyn wirtualnych.
2. Sprawdź, czy komputery zostały pomyślnie dołączone do **Corp.contoso.com**.
3. Przed kontynuowaniem Zaczekaj na zakończenie instalacji SQL Server.
4. Otwórz okno programu PowerShell w trybie administratora.
5. Zainstaluj funkcję Klaster trybu failover systemu Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Dodaj **CORP\Install** jako administratora lokalnego.

        net localgroup administrators "CORP\Install" /Add
7. Zaimportuj dostawcę SQL Server PowerShell.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Dodaj **CORP\Install** jako rolę sysadmin dla domyślnego wystąpienia SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Dodaj rolę **NT NT\SYSTEM** jako logowanie przy użyciu trzech opisanych powyżej uprawnień.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otwórz zaporę, aby uzyskać dostęp zdalny do SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Wyloguj się z obu maszyn wirtualnych.

         logoff.exe

Na koniec możesz skonfigurować grupę dostępności. Użyjesz dostawcy SQL Server PowerShell, aby wykonać wszystkie prace w witrynie **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurowanie grupy dostępności
1. Ponownie nawiąż połączenie z usługą **ContosoSQL1** , uruchamiając pliki pulpitu zdalnego. Zamiast logować się przy użyciu konta komputera, zaloguj się przy użyciu **CORP\Install**.
2. Otwórz okno programu PowerShell w trybie administratora.
3. Zdefiniuj następujące zmienne:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Zaimportuj dostawcę SQL Server PowerShell.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Zmień konto usługi SQL Server na ContosoSQL1 na CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Zmień konto usługi SQL Server na ContosoSQL2 na CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Pobierz **CreateAzureFailoverCluster.ps1** z [tworzenia klastra trybu failover dla zawsze dostępnych grup dostępności na maszynie wirtualnej platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do lokalnego katalogu roboczego. Ten skrypt służy do tworzenia funkcjonalnego klastra trybu failover. Aby uzyskać ważne informacje dotyczące sposobu, w jaki klaster trybu failover systemu Windows współdziała z siecią platformy Azure, zobacz [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie azure Virtual Machines](../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Przejdź do katalogu roboczego i Utwórz klaster trybu failover z pobranym skryptem.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Włącz zawsze włączone grupy dostępności dla domyślnych wystąpień SQL Server w **ContosoSQL1** i **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Utwórz katalog kopii zapasowych i Udziel uprawnień dla kont usługi SQL Server. Ten katalog będzie używany do przygotowywania bazy danych dostępności w replice pomocniczej.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Utwórz bazę danych na **ContosoSQL1** o nazwie **MyDB1**, wykonaj pełną kopię zapasową i kopię zapasową dziennika, a następnie Przywróć je na **ContosoSQL2** z opcją **WITH NORECOVERY** .

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Utwórz punkty końcowe grupy dostępności na maszynach wirtualnych SQL Server i ustaw odpowiednie uprawnienia dla punktów końcowych.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Utwórz repliki dostępności.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Na koniec Utwórz grupę dostępności i Dołącz do niej replikę pomocniczą.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Następne kroki
Teraz pomyślnie zaimplementowano SQL Server zawsze włączone przez utworzenie grupy dostępności na platformie Azure. Aby skonfigurować odbiornik dla tej grupy dostępności, zobacz [Konfigurowanie odbiornika ILB dla zawsze włączonych grup dostępności na platformie Azure](../classic/ps-sql-int-listener.md).

Aby uzyskać więcej informacji na temat korzystania z SQL Server na platformie Azure, zobacz [SQL Server w usłudze Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).
