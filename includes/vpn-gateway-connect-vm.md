---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8d1b27fc040e6aed0bdeeb86b2e6c4df13f87c3
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "92540905"
---
Z maszyną wirtualną, która jest wdrażana w sieci wirtualnej, można się połączyć, tworząc połączenie pulpitu zdalnego z tą maszyną. Najlepszym sposobem na zweryfikowanie, czy można połączyć się z maszyną wirtualną, jest połączenie się z nią za pomocą jej prywatnego adresu IP, a nie nazwy komputera. W ten sposób można przetestować możliwość połączenia się, a nie poprawność skonfigurowania rozpoznawania nazw.

1. Zlokalizuj prywatny adres IP. Prywatny adres IP maszyny wirtualnej można znaleźć, zaglądając do właściwości maszyny wirtualnej w witrynie Azure Portal lub używając programu PowerShell.

   * Witryna Azure Portal — zlokalizuj swoją maszynę wirtualną w witrynie Azure Portal. Wyświetl właściwości maszyny wirtualnej. Zostanie wyświetlony prywatny adres IP.

   * Program PowerShell — skorzystaj z przykładu, aby wyświetlić listę maszyn wirtualnych i prywatnych adresów IP z grup zasobów. Przed użyciem tego przykładu nie trzeba go modyfikować.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. Sprawdź, czy masz połączenie z siecią wirtualną przez połączenie sieci VPN punkt-lokacja.
1. Otwórz program **Podłączanie pulpitu zdalnego** , wpisując „RDP” lub „Podłączanie pulpitu zdalnego” w polu wyszukiwania na pasku zadań, a następnie wybierając pozycję Podłączanie pulpitu zdalnego. Program Podłączanie pulpitu zdalnego możesz także otworzyć za pomocą polecenia „mstsc” w programie PowerShell. 
1. W programie Podłączanie pulpitu zdalnego wprowadź prywatny adres IP maszyny wirtualnej. Możesz kliknąć pozycję „Pokaż opcje”, aby dostosować dodatkowe ustawienia. Następnie nawiąż połączenie.

**Rozwiązywanie problemów z połączeniem**

Jeśli masz problemy z łączeniem się z maszyną wirtualną za pośrednictwem połączenia sieci VPN, sprawdź następujące rzeczy:

* Sprawdź, czy połączenie sieci VPN zostało pomyślnie nawiązane.

* Sprawdź, czy łączysz się z prywatnym adresem IP maszyny wirtualnej.

* Jeśli możesz połączyć się z maszyną wirtualną za pomocą prywatnego adresu IP, ale nie za pomocą nazwy komputera, sprawdź, czy usługa DNS została prawidłowo skonfigurowana. Aby uzyskać więcej informacji na temat tego, jak działa rozpoznawanie nazw dla maszyn wirtualnych, zobacz [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) (Rozpoznawanie nazw dla maszyn wirtualnych).

* Aby uzyskać więcej informacji na temat połączeń pulpitu zdalnego, zobacz [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).