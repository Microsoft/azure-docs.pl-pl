---
title: Tworzenie prywatnego punktu końcowego platformy Azure przy użyciu Azure PowerShell | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure Private link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 0c6fc36be101679cea3a770f311005f63c3f0d66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737380"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell
Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. 

W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną na platformie Azure Virtual Network, logicznym serwerze SQL z prywatnym punktem końcowym platformy Azure przy użyciu Azure PowerShell. Następnie możesz bezpiecznie uzyskać dostęp do SQL Database z maszyny wirtualnej.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem zasobów należy utworzyć grupę zasobów, która będzie hostować Virtual Network i prywatny punkt końcowy z poleceniem [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *zachodniej* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć. Następnie należy skojarzyć podsieć z Virtual Network.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną dla prywatnego punktu końcowego za pomocą elementu [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy Virtual Network o nazwie *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Dodawanie podsieci

Platforma Azure wdraża zasoby w podsieci w ramach Virtual Network, dlatego należy utworzyć podsieć. Utwórz konfigurację podsieci o nazwie Moja *podsieć* z [dodatkiem Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Poniższy przykład tworzy podsieć o nazwie Moja *podsieć* z flagą zasad sieci prywatnego punktu końcowego ustawioną na wartość **wyłączone**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

> [!CAUTION]
> Można łatwo pomylić `PrivateEndpointNetworkPoliciesFlag` parametr z inną dostępną flagą, `PrivateLinkServiceNetworkPoliciesFlag` ponieważ są one długimi wyrazami i mają podobny wygląd.  Upewnij się, że używasz jednej z nich `PrivateEndpointNetworkPoliciesFlag` .

### <a name="associate-the-subnet-to-the-virtual-network"></a>Skojarz podsieć z Virtual Network

Konfigurację podsieci można zapisać do Virtual Network przy użyciu [opcji Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). To polecenie tworzy podsieć:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną w Virtual Network przy użyciu elementu [New-AzVM](/powershell/module/az.compute/new-azvm). Po uruchomieniu następnego polecenia zostanie wyświetlony monit o poświadczenia. Wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

Opcja `-AsJob` tworzy maszynę wirtualną w tle. Możesz przejść do następnego kroku.

Kiedy platforma Azure zacznie tworzyć maszynę wirtualną w tle, otrzymasz wynik podobny do poniższego:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-logical-sql-server"></a>Tworzenie logicznego serwera SQL 

Utwórz logiczny serwer SQL za pomocą polecenia New-AzSqlServer. Należy pamiętać, że nazwa serwera musi być unikatowa w obrębie platformy Azure, więc Zastąp wartość symbolu zastępczego w nawiasach własnym unikatowymi wartościami:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Prywatny punkt końcowy dla serwera w Virtual Network z [dodatkiem New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet `
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS 
Utwórz strefę Prywatna strefa DNS dla SQL Database domeny, Utwórz link skojarzenia z Virtual Network i Utwórz grupę stref DNS w celu skojarzenia prywatnego punktu końcowego ze strefą Prywatna strefa DNS.

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  

$config = New-AzPrivateDnsZoneConfig -Name "privatelink.database.windows.net" -PrivateDnsZoneId $zone.ResourceId

$privateDnsZoneGroup = New-AzPrivateDnsZoneGroup -ResourceGroupName "myResourceGroup" `
 -PrivateEndpointName "myPrivateEndpoint" -name "MyZoneGroup" -PrivateDnsZoneConfig $config
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress), aby uzyskać publiczny adres IP maszyny wirtualnej. Ten przykład zwraca publiczny adres IP maszyny wirtualnej *myVM* :

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Otwórz wiersz polecenia na komputerze lokalnym. Uruchom polecenie mstsc. Zastąp ciąg <publicIpAddress> publicznym adresem IP zwróconym w ostatnim kroku: 


> [!NOTE]
> Jeśli te polecenia były uruchamiane w wierszu polecenia programu PowerShell na komputerze lokalnym i używasz modułu Az PowerShell w wersji 1.0 lub nowszej, możesz kontynuować pracę w tym interfejsie.
```
mstsc /v:<publicIpAddress>
```

1. Po wyświetleniu monitu wybierz pozycję **Połącz**. 
2. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.
  > [!NOTE]
  > Może być konieczne wybranie pozycji więcej opcji, > użyć innego konta, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
  
3. Kliknij przycisk **OK**. 
4. Może zostać wyświetlone ostrzeżenie o certyfikacie. W takim przypadku wybierz pozycję **Tak** lub **Kontynuuj**. 

## <a name="access-sql-database-privately-from-the-vm"></a>Dostęp SQL Database prywatnie z maszyny wirtualnej

1. Na pulpicie zdalnym maszyny myVm otwórz program PowerShell.
2. Wprowadź `nslookup myserver.database.windows.net`. Pamiętaj, aby zamienić na `myserver` nazwę programu SQL Server.

    Zostanie wyświetlony komunikat podobny do tego:
    
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
    
3. Zainstaluj [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).
4. W obszarze **Połącz z serwerem**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | --- | --- |
    | Typ serwera | Aparat bazy danych |
    | Nazwa serwera | myserver.database.windows.net |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika podaną podczas tworzenia |
    | Hasło | Wprowadź hasło podane podczas tworzenia |
    | Zapamiętaj hasło | Tak |
    
5. Wybierz pozycję **Połącz**.
6. Przeglądaj **bazy danych** z menu po lewej stronie. 
7. Zdefiniować Utwórz lub zapytaj informacje z bazy danych.
8. Zamknij połączenie pulpitu zdalnego z *myVM*. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, SQL Database i maszyny wirtualnej, użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , aby usunąć grupę zasobów i wszystkie jej zasoby:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym łączu platformy Azure](private-link-overview.md)
