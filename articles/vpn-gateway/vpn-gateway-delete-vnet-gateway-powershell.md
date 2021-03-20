---
title: 'Azure VPN Gateway: usuwanie bramy: PowerShell'
description: Usuń bramę sieci wirtualnej przy użyciu programu PowerShell w modelu wdrażania Menedżer zasobów.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/03/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 48e0998719ea19c0b360b50d8c9171d2b448ad19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440766"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Istnieje kilka różnych metod, które można wykonać, aby usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystko i zacząć od początku, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Jest to metoda zalecana tylko wtedy, gdy nie chcesz zachować żadnych zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilku zasobów przy użyciu tego podejścia.

- Jeśli chcesz zachować część zasobów w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby zależne od bramy. Kroki, które należy wykonać, zależą od typu utworzonych połączeń i zasobów zależnych dla każdego połączenia.

## <a name="before-beginning"></a>Przed rozpoczęciem



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Pobierz najnowsze polecenia cmdlet programu PowerShell w programie Azure Resource Manager.

Pobierz i zainstaluj najnowszą wersję Azure Resource Manager poleceń cmdlet programu PowerShell. Aby uzyskać więcej informacji na temat pobierania i instalowania poleceń cmdlet programu PowerShell, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/).

### <a name="2-connect-to-your-azure-account"></a>2. Nawiąż połączenie z kontem platformy Azure.

Otwórz konsolę programu PowerShell i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Connect-AzAccount
```

Sprawdź subskrypcje dostępne na koncie.

```powershell
Get-AzSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Usuwanie bramy sieci VPN typu lokacja-lokacja

Aby usunąć bramę sieci wirtualnej dla konfiguracji S2S, należy najpierw usunąć wszystkie zasoby odnoszące się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z poniższymi przykładami należy określić niektóre wartości, a inne wartości są wynikami wyjściowymi. W przykładach w celach demonstracyjnych używamy następujących konkretnych wartości:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: wartość gw1<br>

Poniższe kroki dotyczą modelu wdrażania Menedżer zasobów.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, która ma zostać usunięta.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy Brama sieci wirtualnej ma jakieś połączenia.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Usuń wszystkie połączenia.

Może zostać wyświetlony monit o potwierdzenie usunięcia każdego z tych połączeń.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Usuń bramę sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy. Jeśli do tej sieci wirtualnej istnieje konfiguracja P2S, to usunięcie bramy sieci wirtualnej spowoduje automatyczne odłączenie wszystkich klientów P2S bez ostrzeżenia.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie Brama sieci wirtualnej została usunięta. Poniższe kroki służą do usuwania wszelkich zasobów, które nie są już używane.

### <a name="5-delete-the-local-network-gateways"></a>5. Usuń bramy sieci lokalnej.

Pobierz listę odpowiednich bram sieci lokalnej.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Usuń bramy sieci lokalnej. Może zostać wyświetlony monit o potwierdzenie usunięcia każdej bramy sieci lokalnej.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Usuń zasoby publicznego adresu IP.

Pobierz konfiguracje protokołu IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów publicznych adresów IP używanych dla tej bramy sieci wirtualnej. Jeśli Brama sieci wirtualnej była aktywna — zostaną wyświetlone dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń zasoby publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy i Ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Usuwanie bramy sieci VPN typu sieć wirtualna-sieć wirtualna

Aby usunąć bramę sieci wirtualnej dla konfiguracji V2V, należy najpierw usunąć wszystkie zasoby odnoszące się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z poniższymi przykładami należy określić niektóre wartości, a inne wartości są wynikami wyjściowymi. W przykładach w celach demonstracyjnych używamy następujących konkretnych wartości:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: wartość gw1<br>

Poniższe kroki dotyczą modelu wdrażania Menedżer zasobów.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, która ma zostać usunięta.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Sprawdź, czy Brama sieci wirtualnej ma jakieś połączenia.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Mogą istnieć inne połączenia z bramą sieci wirtualnej, która jest częścią innej grupy zasobów. Sprawdź dodatkowe połączenia w każdej dodatkowej grupie zasobów. W tym przykładzie Sprawdzamy połączenia z RG2. Uruchom to dla każdej grupy zasobów, która może mieć połączenie z bramą sieci wirtualnej.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Pobierz listę połączeń w obu kierunkach.

Ponieważ jest to konfiguracja między sieciami wirtualnymi, potrzebna jest lista połączeń w obu kierunkach.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
W tym przykładzie Sprawdzamy połączenia z RG2. Uruchom to dla każdej grupy zasobów, która może mieć połączenie z bramą sieci wirtualnej.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Usuń wszystkie połączenia.

Może zostać wyświetlony monit o potwierdzenie usunięcia każdego z tych połączeń.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Usuń bramę sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej. Jeśli P2S konfiguracje sieci wirtualnych oprócz konfiguracji V2V, usunięcie bram sieci wirtualnej spowoduje automatyczne rozłączenie wszystkich klientów P2S bez ostrzeżenia.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie Brama sieci wirtualnej została usunięta. Poniższe kroki służą do usuwania wszelkich zasobów, które nie są już używane.

### <a name="6-delete-the-public-ip-address-resources"></a>6. usuwanie zasobów publicznego adresu IP

Pobierz konfiguracje protokołu IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę zasobów publicznych adresów IP używanych dla tej bramy sieci wirtualnej. Jeśli Brama sieci wirtualnej była aktywna — zostaną wyświetlone dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń zasoby publicznego adresu IP. Może zostać wyświetlony monit o potwierdzenie usunięcia publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Usuń podsieć bramy i Ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Usuwanie bramy sieci VPN typu punkt-lokacja

Aby usunąć bramę sieci wirtualnej dla konfiguracji P2S, należy najpierw usunąć wszystkie zasoby odnoszące się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności. Podczas pracy z poniższymi przykładami należy określić niektóre wartości, a inne wartości są wynikami wyjściowymi. W przykładach w celach demonstracyjnych używamy następujących konkretnych wartości:

Nazwa sieci wirtualnej: VNet1<br>
Nazwa grupy zasobów: RG1<br>
Nazwa bramy sieci wirtualnej: wartość gw1<br>

Poniższe kroki dotyczą modelu wdrażania Menedżer zasobów.


>[!NOTE]
> Po usunięciu bramy sieci VPN wszystkie połączone klienci będą odłączane od sieci wirtualnej bez ostrzeżenia.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Pobierz bramę sieci wirtualnej, która ma zostać usunięta.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Usuń bramę sieci wirtualnej.

Może zostać wyświetlony monit o potwierdzenie usunięcia bramy sieci wirtualnej.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

W tym momencie Brama sieci wirtualnej została usunięta. Poniższe kroki służą do usuwania wszelkich zasobów, które nie są już używane.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Usuń zasoby publicznego adresu IP

Pobierz konfiguracje protokołu IP bramy sieci wirtualnej.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Pobierz listę publicznych adresów IP używanych dla tej bramy sieci wirtualnej. Jeśli Brama sieci wirtualnej była aktywna — zostaną wyświetlone dwa publiczne adresy IP.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Usuń publiczne adresy IP. Może zostać wyświetlony monit o potwierdzenie usunięcia publicznego adresu IP.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Usuń podsieć bramy i Ustaw konfigurację.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie chcesz przechowywać żadnych zasobów w grupie zasobów i chcesz zacząć od razu, możesz usunąć całą grupę zasobów. Jest to szybka metoda usuwania wszystkiego. Poniższe kroki dotyczą tylko modelu wdrażania Menedżer zasobów.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Pobierz listę wszystkich grup zasobów w ramach subskrypcji.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Zlokalizuj grupę zasobów, którą chcesz usunąć.

Znajdź grupę zasobów, którą chcesz usunąć, i Wyświetl listę zasobów w tej grupie zasobów. W przykładzie nazwa grupy zasobów to RG1. Zmodyfikuj przykład, aby pobrać listę wszystkich zasobów.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Sprawdź zasoby na liście.

Po zwróceniu listy Przejrzyj ją, aby upewnić się, że chcesz usunąć wszystkie zasoby w grupie zasobów, a także samą grupę zasobów. Jeśli chcesz zachować część zasobów w grupie zasobów, wykonaj kroki opisane w poprzednich sekcjach tego artykułu, aby usunąć bramę.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Usuń grupę zasobów i zasoby.

Aby usunąć grupę zasobów i wszystkie zasoby zawarte w grupie zasobów, należy zmodyfikować przykład i uruchomić polecenie.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Sprawdź stan.

Usunięcie wszystkich zasobów przez platformę Azure zajmuje trochę czasu. Stan grupy zasobów można sprawdzić za pomocą tego polecenia cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Zwrócony wynik zawiera wartość "powodzenie".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
