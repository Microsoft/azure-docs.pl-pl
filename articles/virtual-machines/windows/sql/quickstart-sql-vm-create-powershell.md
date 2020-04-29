---
title: Tworzenie maszyny wirtualnej systemu Windows z programem SQL Server przy użyciu usługi Azure PowerShell | Microsoft Docs
description: W tym samouczku pokazano sposób tworzenia maszyny wirtualnej z programem SQL Server 2017 i systemem Windows przy użyciu usługi Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8994079cf18a9af5f5e1368761015bbd8b836bd9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74790907"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Szybki start: tworzenie maszyny wirtualnej z programem SQL Server i systemem Windows przy użyciu usługi Azure PowerShell

Ten przewodnik Szybki start przeprowadza użytkownika przez proces tworzenia maszyny wirtualnej z programem SQL Server przy użyciu usługi Azure PowerShell.

> [!TIP]
> - W tym przewodniku Szybki start przedstawiono ścieżkę szybkiego aprowizowania maszyny wirtualnej SQL i nawiązywania z nią połączenia. Aby uzyskać więcej informacji na temat innych opcji programu Azure PowerShell dotyczących tworzenia maszyn wirtualnych SQL, zobacz [Provisioning guide for SQL Server VMs with Azure PowerShell](virtual-machines-windows-ps-sql-create.md) (Przewodnik aprowizowania dla maszyn wirtualnych z programem SQL Server za pomocą programu Azure PowerShell).
> - Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Uzyskaj subskrypcję platformy Azure

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .


## <a name="get-azure-powershell"></a><a id="powershell"></a> Uzyskiwanie programu Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurowanie programu PowerShell

1. Otwórz program PowerShell i nawiąż połączenie z kontem platformy Azure, uruchamiając polecenie **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Powinien zostać wyświetlony ekran z monitem o podanie poświadczeń. Użyj tego samego adresu e-mail i hasła, którego używasz do logowania w witrynie Azure Portal.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Zdefiniuj zmienną z unikatową nazwą grupy zasobów. Aby uprościć pozostałą część poradnika Szybki start, reszta poleceń będzie używać tej nazwy jako podstawy dla innych nazw zasobów.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Zdefiniuj lokalizację docelowego regionu świadczenia usługi Azure dla wszystkich zasobów maszyn wirtualnych.

   ```powershell
   $Location = "East US"
   ```

1. Utwórz grupę zasobów.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Konfigurowanie ustawień sieciowych

1. Utwórz sieć wirtualną, podsieć i publiczny adres IP. Te zasoby są używane do zapewniania łączności sieciowej z maszyną wirtualną i nawiązywania połączenia z Internetem.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Utwórz sieciową grupę zabezpieczeń. Skonfiguruj reguły, aby zezwolić na połączenia między pulpitem zdalnym (RDP) i programem SQL Server.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Utwórz interfejs sieciowy.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Tworzenie maszyny wirtualnej z programem SQL

1. Wprowadź swoje poświadczenia, aby zalogować się do maszyny wirtualnej. Nazwa użytkownika to „azureadmin”. Przed uruchomieniem polecenia pamiętaj o zmianie hasła (\<password>).

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Utwórz obiekt konfiguracji maszyny wirtualnej, a następnie utwórz maszynę wirtualną. Poniższe polecenie utworzy maszynę wirtualną programu SQL Server 2017 Developer Edition w systemie Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Utworzenie maszyny wirtualnej zajmuje kilka minut.

## <a name="install-the-sql-iaas-agent"></a>Instalacja agenta SQL IaaS

Aby uzyskać integrację portalu z funkcjami maszyny wirtualnej programu SQL, musisz zainstalować [rozszerzenie agenta IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Aby zainstalować agenta na nowej maszynie wirtualnej, uruchom następujące polecenie po utworzeniu maszyny wirtualnej.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "2.0" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Łączenie pulpitu zdalnego z maszyną wirtualną

1. Użyj następującego polecenia, aby pobrać publiczny adres IP dla nowej maszyny wirtualnej.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Przekaż zwrócony adres IP jako parametr wiersza polecenia do elementu **mstsc**, aby rozpocząć sesję pulpitu zdalnego w nowej maszynie wirtualnej.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Po wyświetleniu monitu o poświadczenia wybierz opcję podania poświadczeń dla innego konta. Wprowadź nazwę użytkownika poprzedzoną ukośnikiem odwrotnym (na przykład `\azureadmin`) oraz hasło ustawione wcześniej w tym poradniku Szybki start.

## <a name="connect-to-sql-server"></a>Ustanawianie połączenia z programem SQL Server

1. Po zalogowaniu w sesji pulpitu zdalnego uruchom program **SQL Server Management Studio 2017** z menu start.

1. W oknie dialogowym **Połącz z serwerem** zachowaj ustawienia domyślne. Nazwa serwera to nazwa maszyny wirtualnej. Uwierzytelnianie jest ustawione na opcję **Uwierzytelnianie systemu Windows**. Wybierz przycisk **Połącz**.

Masz teraz lokalne połączenie z programem SQL Server. Jeśli chcesz nawiązać połączenie zdalne, musisz [skonfigurować łączność](virtual-machines-windows-sql-connect.md) z poziomu portalu lub ręcznie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli maszyna wirtualna nie musi działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Następujące polecenie zatrzyma maszynę wirtualną, ale pozostawi ją dostępną do użycia w przyszłości.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, korzystając z polecenia **Remove-AzResourceGroup**. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą.

## <a name="next-steps"></a>Następne kroki

W tym samouczku Szybki start utworzono maszynę wirtualną z programem SQL Server 2017 przy użyciu usługi Azure PowerShell. Aby dowiedzieć się więcej o sposobie przeprowadzania migracji danych do nowego serwera SQL Server, zobacz następujący artykuł.

> [!div class="nextstepaction"]
> [Migrowanie bazy danych do maszyny wirtualnej SQL](virtual-machines-windows-migrate-sql.md)
