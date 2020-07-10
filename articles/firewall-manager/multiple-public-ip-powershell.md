---
title: Wdrażanie zapory platformy Azure z wieloma publicznymi adresami IP przy użyciu Azure PowerShell
description: Wdrażanie zapory z wieloma publicznymi adresami IP w celu ochrony koncentratora wirtualnego
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 87af7f0f9b446fb3a54a600f61409c2cfc1a2494
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189483"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Wdrażanie zapory platformy Azure z wieloma publicznymi adresami IP

Jeśli chcesz chronić koncentrator wirtualny przy użyciu zapory platformy Azure, możesz wdrożyć zaporę z wieloma publicznymi adresami IP przy użyciu Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Użyj poniższego przykładu Azure PowerShell, aby wdrożyć zaporę platformy Azure z wieloma publicznymi adresami IP w celu ochrony koncentratora wirtualnego.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="delete-a-public-ip-address"></a>Usuwanie publicznego adresu IP

Za pomocą Azure PowerShell można usunąć publiczny adres IP z zapory platformy Azure. Poniższy przykład usuwa jeden publiczny adres IP z zapory. Rozpoczyna się od trzech publicznych adresów IP.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Następne kroki

[Co to jest bezpieczny koncentrator wirtualny?](secured-virtual-hub.md)