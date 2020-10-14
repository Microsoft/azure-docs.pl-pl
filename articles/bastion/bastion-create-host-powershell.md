---
title: Tworzenie hosta bastionu przy użyciu Azure PowerShell | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: f0e7a66ef7d6947306f1b2ffec54b8e9d12737d7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018597"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Tworzenie hosta usługi Azure bastionu za pomocą Azure PowerShell

W tym artykule opisano sposób tworzenia hosta usługi Azure bastionu przy użyciu programu PowerShell. Po wdrożeniu bastionu można nawiązać połączenie z maszyną wirtualną za pośrednictwem swojego prywatnego adresu IP za pośrednictwem przeglądarki za pomocą Azure Portal. Maszyna wirtualna nie potrzebuje publicznego adresu IP, dodatkowego klienta lub specjalnego oprogramowania. Wdrożenie usługi Azure bastionu odbywa się na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną. Bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej.

Te instrukcje dotyczą wdrażania programu PowerShell. Możesz również utworzyć hosta usługi Azure bastionu za pomocą [Azure Portal](tutorial-create-host-portal.md) lub [interfejsu wiersza polecenia platformy Azure](create-host-cli.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu usługi Azure bastionu dla sieci wirtualnej przy użyciu Azure PowerShell.

1. Utwórz sieć wirtualną i podsieć usługi Azure bastionu. Należy utworzyć podsieć usługi Azure bastionu przy użyciu wartości Name **AzureBastionSubnet**. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci z co najmniej/27 lub większą podsiecią (/27,/26 itd.). Utwórz **AzureBastionSubnet** bez żadnych tabel tras ani delegowania. Jeśli używasz sieciowych grup zabezpieczeń w **AzureBastionSubnet**, zapoznaj się z artykułem [Pracuj z sieciowych grup zabezpieczeń](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Utwórz publiczny adres IP dla usługi Azure bastionu. Publiczny adres IP jest publicznym adresem IP, do którego zostanie uzyskany dostęp do protokołu RDP/SSH (za pośrednictwem portu 443). Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Utwórz nowy zasób platformy Azure bastionu w AzureBastionSubnet sieci wirtualnej. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Następne kroki

* Nawiąż połączenie z maszyną wirtualną.
   * [Maszyna wirtualna z systemem Linux](bastion-connect-vm-ssh.md)
   * [Maszyna wirtualna z systemem Windows](bastion-connect-vm-rdp.md)
