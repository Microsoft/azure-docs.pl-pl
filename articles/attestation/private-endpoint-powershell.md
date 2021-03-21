---
title: Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell
description: Tworzenie prywatnego punktu końcowego dla zaświadczania platformy Azure przy użyciu Azure PowerShell
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 92080f119567d185ca6a5bccf0e89d8d10213f3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732313"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell

Zacznij korzystać z prywatnego linku platformy Azure przy użyciu prywatnego punktu końcowego, aby bezpiecznie połączyć się z zaświadczeniem platformy Azure.

W tym przewodniku szybki start utworzysz prywatny punkt końcowy dla zaświadczania platformy Azure i wdrożono maszynę wirtualną w celu przetestowania połączenia prywatnego.  

> [!NOTE]
> Bieżąca implementacja obejmuje tylko opcję automatycznego zatwierdzania. Aby można było kontynuować tworzenie prywatnych punktów końcowych, subskrypcja musi być białym listą. Przed wykonaniem poniższych kroków skontaktuj się z zespołem usługi lub Prześlij żądanie pomocy technicznej platformy Azure na [stronie pomocy technicznej](https://azure.microsoft.com/support/options/) systemu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Dowiedz się więcej o [usłudze Azure Private link](../private-link/private-link-overview.md)
* [Konfigurowanie zaświadczania platformy Azure za pomocą Azure PowerShell](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów przy użyciu elementu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription “mySubscription”
$rg = “CreateAttestationPrivateLinkTutorial-rg”
$loc= "eastus”
New-AzResourceGroup -Name $rg -Location $loc
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Tworzenie sieci wirtualnej i hosta bastionu

W tej sekcji utworzysz sieć wirtualną, podsieć i hosta bastionu. 

Host bastionu zostanie użyty do nawiązania bezpiecznego połączenia z maszyną wirtualną w celu przetestowania prywatnego punktu końcowego.

Utwórz sieć wirtualną i hosta bastionu przy użyciu:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
```

Wdrożenie hosta usługi Azure bastionu może potrwać kilka minut.

## <a name="create-test-virtual-machine"></a>Utwórz testową maszynę wirtualną

W tej sekcji utworzysz maszynę wirtualną, która będzie używana do testowania prywatnego punktu końcowego.

Utwórz maszynę wirtualną przy użyciu:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>Tworzenie dostawcy zaświadczania

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id

## Access the attestation provider from local machine ##
Enter nslookup <provider-name>.attest.azure.net. Replace <provider-name> with the name of the attestation provider instance you created in the previous steps. 

You'll receive a message similar to what is displayed below:

## PowerShell copy. ##
nslookup myattestationprovider.eus.attest.azure.net

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net
```

## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

W tej sekcji utworzysz prywatny punkt końcowy i połączenie za pomocą polecenia:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
 $vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>Skonfiguruj prywatną strefę DNS

W tej sekcji utworzysz i skonfigurujesz prywatną strefę DNS przy użyciu:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>Testowanie łączności z prywatnym punktem końcowym

W tej sekcji użyjesz maszyny wirtualnej utworzonej w poprzednim kroku, aby nawiązać połączenie z programem SQL Server w ramach prywatnego punktu końcowego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com). 
 
2. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów** .

3. Wybierz pozycję **CreateAttestationPrivateLinkTutorial-RG**.

4. Wybierz pozycję **myVM**.

5. Na stronie Przegląd dla **myVM** wybierz pozycję **Połącz** , a następnie **bastionu**.

6. Wybierz przycisk **bastionu Użyj** niebieska.

7. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

8. Po nawiązaniu połączenia Otwórz program Windows PowerShell na serwerze.

9. Wprowadź `nslookup <provider-name>.attest.azure.net`. Zamień **\<provider-name>** na nazwę wystąpienia dostawcy zaświadczania utworzonego w poprzednich krokach. Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell

    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1

    cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    ```
