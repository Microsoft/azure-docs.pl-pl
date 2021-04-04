---
title: Wdrażanie zapory platformy Azure z wieloma publicznymi adresami IP przy użyciu programu PowerShell
description: W tym artykule dowiesz się, jak wdrożyć zaporę platformy Azure z wieloma publicznymi adresami IP przy użyciu Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023673"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Wdrażanie usługi Azure Firewall z wieloma publicznymi adresami IP przy użyciu programu Azure PowerShell

Ta funkcja włącza następujące scenariusze:

- **DNAT** — wiele standardowych wystąpień portów można przetłumaczyć na serwery zaplecza. Jeśli na przykład masz dwa publiczne adresy IP, możesz wykonać translację portu TCP 3389 (RDP) dla obu adresów IP.
- Reportcy **adresów sieciowych** — dodatkowe porty są dostępne dla wychodzących połączeń z reportem adresów sieciowych, co zmniejsza prawdopodobieństwo wyczerpania portów dla tego elementu. W tej chwili Zapora platformy Azure losowo wybiera źródłowy publiczny adres IP, który ma być używany w połączeniu. Jeśli w sieci występuje jakiekolwiek filtrowanie w kierunku do klienta, musisz zezwolić na wszystkie publiczne adresy IP skojarzone z zaporą. Rozważ użycie [prefiksu publicznego adresu IP](../virtual-network/public-ip-address-prefix.md) , aby uprościć tę konfigurację.
 
Zapora platformy Azure z wieloma publicznymi adresami IP jest dostępna za pośrednictwem Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure, REST i szablonów. Można wdrożyć zaporę platformy Azure z maksymalnie 250 publicznymi adresami IP.

Poniższe Azure PowerShell przykłady pokazują, jak można konfigurować, dodawać i usuwać publiczne adresy IP dla zapory platformy Azure.

> [!NOTE]
> Nie można usunąć pierwszego elementu ipConfiguration z strony konfiguracji publicznego adresu IP zapory platformy Azure. Jeśli chcesz zmodyfikować adres IP, możesz użyć Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Tworzenie zapory z co najmniej dwoma publicznymi adresami IP

Ten przykład umożliwia utworzenie zapory dołączonej *do sieci wirtualnej* z dwoma publicznymi adresami IP.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Dodawanie publicznego adresu IP do istniejącej zapory

W tym przykładzie publiczny adres IP *azFwPublicIp1* jest podłączony do zapory.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Usuwanie publicznego adresu IP z istniejącej zapory

W tym przykładzie publiczny adres IP *azFwPublicIp1* jest odłączony od zapory.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie zapory platformy Azure z wieloma publicznymi adresami IP — szablon Menedżer zasobów](quick-create-multiple-ip-template.md)
