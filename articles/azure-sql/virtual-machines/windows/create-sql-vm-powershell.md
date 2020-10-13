---
title: Przewodnik po użyciu Azure PowerShell do aprowizacji SQL Server na maszynie wirtualnej platformy Azure
description: Zawiera kroki i polecenia programu PowerShell służące do tworzenia maszyny wirtualnej platformy Azure przy użyciu obrazów galerii maszyn wirtualnych SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bb3fd9136f78a332a22f973d211dec748c4fb260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317076"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Jak za pomocą Azure PowerShell zainicjować obsługę administracyjną SQL Server na platformie Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym przewodniku opisano opcje korzystania z programu PowerShell w celu aprowizacji SQL Server na platformie Azure Virtual Machines. Aby zapoznać się z ulepszonym Azure PowerShell przykładem, który opiera się na wartościach domyślnych, zapoznaj się z [przewodnikiem Szybki Start dla Azure PowerShell SQL](sql-vm-create-powershell-quickstart.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurowanie subskrypcji

1. Otwórz program PowerShell i nawiąż połączenie z kontem platformy Azure, uruchamiając polecenie **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Po wyświetleniu monitu wprowadź poświadczenia. Użyj tego samego adresu e-mail i hasła, którego używasz do logowania w witrynie Azure Portal.

## <a name="define-image-variables"></a>Definiuj zmienne obrazu

Aby ponownie użyć wartości i uprościć tworzenie skryptów, Zacznij od zdefiniowania kilku zmiennych. Zmień wartości parametrów zgodnie z potrzebami, ale pamiętaj o ograniczeniach nazewnictwa związanych z długościami nazw i znakami specjalnymi podczas modyfikowania dostarczonych wartości.

### <a name="location-and-resource-group"></a>Lokalizacja i Grupa zasobów

Zdefiniuj obszar danych i grupę zasobów, w której chcesz utworzyć inne zasoby maszyn wirtualnych.

Zmodyfikuj odpowiednie działanie, a następnie Uruchom te polecenia cmdlet, aby zainicjować te zmienne.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Właściwości magazynu

Zdefiniuj konto magazynu i typ magazynu, który ma być używany przez maszynę wirtualną.

Zmodyfikuj odpowiednie polecenie, a następnie uruchom następujące polecenie cmdlet, aby zainicjować te zmienne. Zalecamy używanie [dysków SSD Premium](../../../virtual-machines/disks-types.md#premium-ssd) w przypadku obciążeń produkcyjnych.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Właściwości sieci

Zdefiniuj właściwości, które mają być używane przez sieć na maszynie wirtualnej. 

- Interfejs sieciowy
- Metoda alokacji TCP/IP
- Nazwa sieci wirtualnej
- Nazwa podsieci wirtualnej
- Zakres adresów IP dla sieci wirtualnej
- Zakres adresów IP podsieci
- Etykieta nazwy domeny publicznej

Zmodyfikuj odpowiednie działanie, a następnie Uruchom to polecenie cmdlet, aby zainicjować te zmienne.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Właściwości maszyny wirtualnej

Zdefiniuj następujące właściwości:

- Nazwa maszyny wirtualnej
- Nazwa komputera
- Rozmiar maszyny wirtualnej
- Nazwa dysku systemu operacyjnego dla maszyny wirtualnej

Zmodyfikuj odpowiednie działanie, a następnie Uruchom to polecenie cmdlet, aby zainicjować te zmienne.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Wybieranie obrazu SQL Server

Użyj następujących zmiennych, aby zdefiniować obraz SQL Server do użycia dla maszyny wirtualnej. 

1. Najpierw utwórz listę ofert obrazu SQL Server za pomocą `Get-AzVMImageOffer` polecenia. To polecenie wyświetla listę bieżących obrazów, które są dostępne w Azure Portal, a także starszych obrazów, które można zainstalować tylko przy użyciu programu PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. W tym samouczku Użyj następujących zmiennych, aby określić SQL Server 2017 w systemie Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Następnie Wyświetl listę dostępnych wersji oferty.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Na potrzeby tego samouczka Użyj SQL Server 2017 Developer Edition (**SQLDEV**). Wersja Developer jest swobodnie licencjonowana do testowania i programowania, a opłaty są nanoszone tylko za koszt działania maszyny wirtualnej.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W modelu wdrażania Menedżer zasobów pierwszy tworzony obiekt jest grupą zasobów. Użyj polecenia cmdlet [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) , aby utworzyć grupę zasobów platformy Azure i jej zasoby. Określ zmienne, które wcześniej zainicjowano dla nazwy i lokalizacji grupy zasobów.

Uruchom to polecenie cmdlet, aby utworzyć nową grupę zasobów.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Maszyna wirtualna wymaga zasobów magazynu dla dysku systemu operacyjnego oraz plików danych i dzienników SQL Server. Dla uproszczenia utworzysz pojedynczy dysk dla obu tych dysków. Później możesz dołączyć dodatkowe dyski przy użyciu polecenia cmdlet [Add-Azure Disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) , aby umieścić dane SQL Server i pliki dziennika na dedykowanych dyskach. Użyj polecenia cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) , aby utworzyć konto magazynu w warstwie Standardowa w nowej grupie zasobów. Określ zmienne, które wcześniej zainicjowano dla nazwy konta magazynu, nazwy jednostki SKU magazynu i lokalizacji.

Uruchom to polecenie cmdlet, aby utworzyć nowe konto magazynu.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Tworzenie konta magazynu może potrwać kilka minut.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Maszyna wirtualna wymaga wielu zasobów sieciowych na potrzeby łączności sieciowej.

* Każda maszyna wirtualna wymaga sieci wirtualnej.
* Sieć wirtualna musi mieć zdefiniowaną co najmniej jedną podsieć.
* Interfejs sieciowy musi być zdefiniowany za pomocą publicznego lub prywatnego adresu IP.

### <a name="create-a-virtual-network-subnet-configuration"></a>Utwórz konfigurację podsieci sieci wirtualnej

Zacznij od utworzenia konfiguracji podsieci dla sieci wirtualnej. Na potrzeby tego samouczka Utwórz domyślną podsieć za pomocą polecenia cmdlet [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . Określ zmienne, które wcześniej zainicjowano dla nazwy podsieci i prefiksu adresu.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości konfiguracji podsieci sieci wirtualnej za pomocą tego polecenia cmdlet, ale wykraczające poza zakres tego samouczka.

Uruchom to polecenie cmdlet, aby utworzyć konfigurację podsieci wirtualnej.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Następnie Utwórz sieć wirtualną w nowej grupie zasobów za pomocą polecenia cmdlet [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) . Określ zmienne, które wcześniej zainicjowano dla nazwy, lokalizacji i prefiksu adresu. Użyj konfiguracji podsieci zdefiniowanej w poprzednim kroku.

Uruchom to polecenie cmdlet, aby utworzyć sieć wirtualną.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP

Teraz, gdy sieć wirtualna jest zdefiniowana, należy skonfigurować adres IP na potrzeby łączności z maszyną wirtualną. Na potrzeby tego samouczka Utwórz publiczny adres IP przy użyciu dynamicznego adresowania IP, aby zapewnić obsługę łączności z Internetem. Użyj polecenia cmdlet [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) , aby utworzyć publiczny adres IP w nowej grupie zasobów. Określ zmienne, które wcześniej zainicjowano dla nazwy, lokalizacji, metody alokacji i etykiety nazwy domeny DNS.

> [!NOTE]
> Można zdefiniować dodatkowe właściwości publicznego adresu IP za pomocą tego polecenia cmdlet, ale wykraczające poza zakres tego początkowego samouczka. Można również utworzyć adres prywatny lub adres ze statycznym adresem, ale również poza zakresem tego samouczka.

Uruchom to polecenie cmdlet, aby utworzyć publiczny adres IP.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Utwórz sieciową grupę zabezpieczeń

Aby zabezpieczyć maszynę wirtualną i ruch SQL Server, Utwórz sieciową grupę zabezpieczeń.

1. Najpierw utwórz regułę sieciowej grupy zabezpieczeń dla pulpitu zdalnego (RDP), aby umożliwić połączenia RDP.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Skonfiguruj regułę sieciowej grupy zabezpieczeń, która zezwala na ruch na porcie TCP 1433. Dzięki temu połączenia mogą SQL Server przez Internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Utwórz sieciową grupę zabezpieczeń.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Utwórz interfejs sieciowy

Teraz możesz przystąpić do utworzenia interfejsu sieciowego dla maszyny wirtualnej. Użyj polecenia cmdlet [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) , aby utworzyć interfejs sieciowy w nowej grupie zasobów. Określ wcześniej zdefiniowaną nazwę, lokalizację, podsieć i publiczny adres IP.

Uruchom to polecenie cmdlet, aby utworzyć interfejs sieciowy.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurowanie obiektu maszyny wirtualnej

Teraz, gdy są zdefiniowane zasoby magazynu i sieci, można przystąpić do definiowania zasobów obliczeniowych dla maszyny wirtualnej.

- Określ rozmiar maszyny wirtualnej i różne właściwości systemu operacyjnego.
- Określ wcześniej utworzony interfejs sieciowy.
- Zdefiniuj magazyn obiektów BLOB.
- Określ dysk systemu operacyjnego.

### <a name="create-the-vm-object"></a>Tworzenie obiektu maszyny wirtualnej

Zacznij od określenia rozmiaru maszyny wirtualnej. Na potrzeby tego samouczka Określ DS13. Użyj polecenia cmdlet [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) , aby utworzyć konfigurowalny obiekt maszyny wirtualnej. Określ zmienne, które wcześniej zainicjowano dla nazwy i rozmiaru.

Uruchom to polecenie cmdlet, aby utworzyć obiekt maszyny wirtualnej.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Utwórz obiekt poświadczeń, aby pomieścić nazwę i hasło do poświadczeń administratora lokalnego

Aby można było ustawić właściwości systemu operacyjnego dla maszyny wirtualnej, należy podać poświadczenia dla konta administratora lokalnego jako bezpieczny ciąg. W tym celu należy użyć polecenia cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Uruchom następujące polecenie cmdlet. Musisz wpisać nazwę lokalnego administratora maszyny wirtualnej i hasło w oknie żądania poświadczeń programu PowerShell.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ustaw właściwości systemu operacyjnego dla maszyny wirtualnej

Teraz można przystąpić do ustawiania właściwości systemu operacyjnego maszyny wirtualnej za pomocą polecenia cmdlet [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) .

- Ustaw typ systemu operacyjnego jako Windows.
- Wymagaj instalacji [agenta maszyny wirtualnej](../../../virtual-machines/extensions/agent-windows.md) .
- Określ, że polecenie cmdlet włącza aktualizację AutoUpdate.
- Określ zmienne, które wcześniej zainicjowano dla nazwy maszyny wirtualnej, nazwy komputera i poświadczenia.

Uruchom to polecenie cmdlet, aby ustawić właściwości systemu operacyjnego dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Dodaj interfejs sieciowy do maszyny wirtualnej

Następnie użyj polecenia cmdlet [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) , aby dodać interfejs sieciowy przy użyciu zdefiniowanej wcześniej zmiennej.

Uruchom to polecenie cmdlet, aby ustawić interfejs sieciowy dla maszyny wirtualnej.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ustaw lokalizację magazynu obiektów BLOB dla dysku, który ma być używany przez maszynę wirtualną

Następnie Ustaw lokalizację magazynu obiektów BLOB dla dysku maszyny wirtualnej ze zdefiniowanymi wcześniej zmiennymi.

Uruchom to polecenie cmdlet, aby ustawić lokalizację magazynu obiektów BLOB.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ustaw właściwości dysku systemu operacyjnego dla maszyny wirtualnej

Następnie ustaw właściwości dysku systemu operacyjnego dla maszyny wirtualnej przy użyciu polecenia cmdlet [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) . 

- Określ, że system operacyjny dla maszyny wirtualnej będzie pochodzący z obrazu.
- Ustaw buforowanie na tylko do odczytu (ponieważ SQL Server jest instalowany na tym samym dysku).
- Określ zmienne, które wcześniej zainicjowano dla nazwy maszyny wirtualnej i dysku systemu operacyjnego.

Uruchom to polecenie cmdlet, aby ustawić właściwości dysku systemu operacyjnego dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Określ obraz platformy dla maszyny wirtualnej

Ostatnim krokiem konfiguracji jest określenie obrazu platformy dla maszyny wirtualnej. Na potrzeby tego samouczka Użyj najnowszego obrazu SQL Server 2016 CTP. Użyj polecenia cmdlet [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) , aby użyć tego obrazu ze zdefiniowanymi wcześniej zmiennymi.

Uruchom to polecenie cmdlet, aby określić obraz platformy dla maszyny wirtualnej.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Tworzenie maszyny wirtualnej z programem SQL

Po zakończeniu kroków konfiguracji można utworzyć maszynę wirtualną. Użyj polecenia cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) , aby utworzyć maszynę wirtualną przy użyciu zdefiniowanych zmiennych.

> [!TIP]
> Uruchomienie maszyny wirtualnej może potrwać kilka minut.

Uruchom to polecenie cmdlet, aby utworzyć maszynę wirtualną.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Maszyna wirtualna została utworzona.

> [!NOTE]
> Jeśli wystąpi błąd dotyczący diagnostyki rozruchu, możesz go zignorować. Konto magazynu w warstwie Standardowa jest tworzone na potrzeby diagnostyki rozruchu, ponieważ określone konto magazynu dla dysku maszyny wirtualnej jest kontem magazynu w warstwie Premium.

## <a name="install-the-sql-iaas-agent"></a>Instalacja agenta SQL IaaS

SQL Server maszyny wirtualne obsługują zautomatyzowane funkcje zarządzania przy użyciu [rozszerzenia agenta SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md). Aby zainstalować agenta na nowej maszynie wirtualnej i zarejestrować go u dostawcy zasobów, uruchom polecenie [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) po utworzeniu maszyny wirtualnej. Określ typ licencji dla maszyny wirtualnej SQL Server, wybierając między opcją płatność zgodnie z rzeczywistym użyciem lub przeniesieniem własnych licencji za pośrednictwem [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/). Aby uzyskać więcej informacji o licencjonowaniu, zobacz [model licencjonowania](licensing-model-azure-hybrid-benefit-ahb-change.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Zatrzymywanie lub usuwanie maszyny wirtualnej

Jeśli maszyna wirtualna nie musi działać w sposób ciągły, możesz uniknąć niepotrzebnych opłat, zatrzymując ją, gdy jest nieużywana. Następujące polecenie zatrzyma maszynę wirtualną, ale pozostawi ją dostępną do użycia w przyszłości.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Możesz również trwale usunąć wszystkie zasoby skojarzone z maszyną wirtualną, korzystając z polecenia **Remove-AzResourceGroup**. Spowoduje to również trwałe usunięcie maszyny wirtualnej, dlatego tego polecenia należy używać z rozwagą.

## <a name="example-script"></a>Przykładowy skrypt

Poniższy skrypt zawiera kompletny skrypt programu PowerShell dla tego samouczka. Przyjęto założenie, że skonfigurowano już subskrypcję platformy Azure do użycia z poleceniami **Connect-AzAccount** i **SELECT-AzSubscription** .

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu maszyny wirtualnej można:

- Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP
- Skonfiguruj ustawienia SQL Server w portalu dla maszyny wirtualnej, w tym:
   - [Ustawienia magazynu](storage-configuration.md) 
   - [Zautomatyzowane zadania zarządzania](sql-server-iaas-agent-extension-automate-management.md)
- [Konfigurowanie łączności](ways-to-connect-to-sql.md)
- Łączenie klientów i aplikacji z nowym wystąpieniem SQL Server
