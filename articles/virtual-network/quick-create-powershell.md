---
title: Tworzenie sieci wirtualnej — Szybki start — Azure PowerShell
titlesuffix: Azure Virtual Network
description: W tym przewodniku szybki start utworzysz sieć wirtualną przy użyciu Azure Portal. Dzięki sieci wirtualnej zasoby platformy Azure komunikują się ze sobą i z Internetem.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93e459df96d444e71f4b6a15668f80e9d77db5fd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89077893"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu programu PowerShell

Dzięki sieci wirtualnej zasoby platformy Azure, takie jak maszyny wirtualne (VM), mogą komunikować się prywatnie ze sobą i z Internetem. W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną. Po utworzeniu sieci wirtualnej, wdrożysz w niej dwie maszyny wirtualne. Następnie nawiążesz połączenie z jedną z maszyn wirtualnych z Internetu i rozpoczniesz prywatną komunikację przez sieć wirtualną.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zamiast tego postanowisz zainstalować program PowerShell i używać go lokalnie, ten przewodnik Szybki start wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Aby uzyskać informacje na temat instalacji i uaktualniania, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

Ponadto, jeśli używasz programu PowerShell lokalnie, musisz uruchomić polecenie `Connect-AzAccount`. To polecenie tworzy połączenie z platformą Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej

Aby skonfigurować grupę zasobów i sieć wirtualną, należy wykonać kilka czynności.

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów, która będzie hostowała tę sieć wirtualną. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Ten przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). W tym przykładzie opisano tworzenie domyślnej sieci wirtualnej o nazwie *myVirtualNetwork* w lokalizacji *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Dodawanie podsieci

Zasoby platformy Azure są wdrażane w podsieci sieci wirtualnej, dlatego należy utworzyć podsieć. Utwórz konfigurację podsieci o nazwie *domyślna* za pomocą polecenia [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Kojarzenie podsieci z siecią wirtualną

Konfigurację podsieci możesz zapisać w sieci wirtualnej za pomocą polecenia [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). To polecenie tworzy podsieć:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz pierwszą maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Po uruchomieniu następnego polecenia zostanie wyświetlony monit o poświadczenia. Wprowadź nazwę użytkownika i hasło dla maszyny wirtualnej:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Opcja `-AsJob` tworzy maszynę wirtualną w tle. Możesz przejść do następnego kroku.

Kiedy platforma Azure zacznie tworzyć maszynę wirtualną w tle, otrzymasz wynik podobny do poniższego:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Utwórz drugą maszynę wirtualną za pomocą następującego polecenia:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Trzeba będzie utworzyć innego użytkownika i hasło. Utworzenie maszyny wirtualnej na platformie Azure trwa kilka minut.

> [!IMPORTANT]
> Nie przechodź do następnego kroku, dopóki platforma Azure nie ukończy tego procesu.  Będziesz wiedzieć, że wszystko jest już gotowe, gdy do programu PowerShell zostaną zwrócone dane wyjściowe.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W poniższym przykładzie zwracany jest publiczny adres IP maszyny wirtualnej o nazwie *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Otwórz wiersz polecenia na komputerze lokalnym. Uruchom polecenie `mstsc`. Zastąp ciąg `<publicIpAddress>` publicznym adresem IP zwróconym w ostatnim kroku:

> [!NOTE]
> Jeśli te polecenia były uruchamiane w wierszu polecenia programu PowerShell na komputerze lokalnym i używasz modułu Az PowerShell w wersji 1.0 lub nowszej, możesz kontynuować pracę w tym interfejsie.

```cmd
mstsc /v:<publicIpAddress>
```
1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

    > [!NOTE]
    > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Może zostać wyświetlone ostrzeżenie o certyfikacie. W takim przypadku wybierz pozycję **Tak** lub **Kontynuuj**.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. Na pulpicie zdalnym maszyny *myVm1* otwórz program PowerShell.

1. Wprowadź `ping myVm2`.

    Zostanie wyświetlony komunikat podobny do następującego:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Polecenie ping kończy się niepowodzeniem, ponieważ używa protokołu ICMP (Internet Control Message Protocol). Domyślnie protokół ICMP jest blokowany przez zaporę Windows.

1. Aby umożliwić wykonanie polecenia ping na maszynie *myVm2* w celu komunikacji z maszyną *myVm1* w późniejszym kroku, wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    To polecenie zezwala przechodzenie przez zaporę Windows ruchu przychodzącego przy użyciu protokołu ICMP.

1. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm1*.

1. Powtórz kroki opisane w sekcji [Nawiązywanie połączenia z maszyną wirtualną z Internetu](#connect-to-a-vm-from-the-internet). Tym razem połączysz się z maszyną wirtualną *myVm2*.

1. W wierszu polecenia na maszynie wirtualnej *myVm2* wprowadź polecenie `ping myvm1`.

    Zostanie wyświetlony komunikat podobny do następującego:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Z maszyny wirtualnej *myVm1* zostanie odebrana odpowiedź, ponieważ w poprzednim kroku umożliwiono przekazywanie ruchu protokołu ICMP przez zaporę systemu Windows na maszynie wirtualnej *myVm1*.

1. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm2*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu korzystania z sieci wirtualnej i maszyn wirtualnych użyj polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), aby usunąć grupę zasobów i wszystkie zasoby, które zawiera:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. Z Internetu nawiązano połączenie z jedną z maszyn wirtualnych, a następnie rozpoczęto prywatną komunikację między tymi dwiema maszynami wirtualnymi.
Azure umożliwia nieograniczoną komunikację prywatną między maszynami wirtualnymi. Azure domyślnie zezwala tylko na połączenia przychodzące pulpitu zdalnego z maszynami wirtualnymi Windows z Internetu. Przejdź do następnego artykułu, aby dowiedzieć się więcej o konfigurowaniu różnych typów komunikacji sieciowej maszyny wirtualnej:
> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md)
