---
title: Jak zresetować interfejs sieciowy dla maszyny wirtualnej z systemem Windows Azure | Microsoft Docs
description: Pokazuje, jak zresetować interfejs sieciowy dla maszyny wirtualnej platformy Azure z systemem Windows
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022908"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Jak zresetować interfejs sieciowy dla maszyny wirtualnej z systemem Windows na platformie Azure 

W tym artykule opisano sposób resetowania interfejsu sieciowego dla maszyny wirtualnej platformy Azure z systemem Windows w celu rozwiązywania problemów, gdy nie można nawiązać połączenia z maszyną wirtualną z systemem Microsoft Azure Windows (VM) po:

* Należy wyłączyć domyślny interfejs sieciowy (NIC). 
* Należy ręcznie ustawić statyczny adres IP dla karty sieciowej. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Zresetuj interfejs sieciowy

### <a name="for-vms-deployed-in-resource-group-model"></a>W przypadku maszyn wirtualnych wdrożonych w modelu grupy zasobów

1.  Przejdź do witryny [Azure Portal](https://ms.portal.azure.com).
2.  Wybierz zaatakowaną maszynę wirtualną.
3.  Wybierz pozycję **Sieć** , a następnie wybierz interfejs sieciowy maszyny wirtualnej.

    ![Lokalizacja interfejsu sieciowego](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Wybierz pozycję **konfiguracje adresów IP**.
5.  Wybierz adres IP. 
6.  Jeśli **przypisanie prywatnego adresu IP**  nie jest  **statyczne**, zmień je na **static**.
7.  Zmień **adres IP** na inny adres IP, który jest dostępny w podsieci.
8. Maszyna wirtualna zostanie ponownie uruchomiona w celu zainicjowania nowej karty sieciowej w systemie.
9.  Spróbuj nawiązać połączenie RDP z maszyną. Jeśli chcesz, możesz zmienić prywatny adres IP z powrotem na oryginalny. W przeciwnym razie możesz ją zachować. 

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz zainstalowaną [najnowszą Azure PowerShell](/powershell/azure/)
2. Otwórz sesję Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Spróbuj nawiązać połączenie RDP z maszyną.  Jeśli chcesz, możesz zmienić prywatny adres IP z powrotem na oryginalny. W przeciwnym razie możesz ją zachować.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Aby zresetować interfejs sieciowy, wykonaj następujące kroki:

#### <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

1.  Przejdź do witryny [Azure Portal]( https://ms.portal.azure.com).
2.  Wybierz **Virtual Machines (klasyczny)**.
3.  Wybierz zaatakowaną maszynę wirtualną.
4.  Wybierz pozycję **adresy IP**.
5.  Jeśli **przypisanie prywatnego adresu IP**  nie jest  **statyczne**, zmień je na **static**.
6.  Zmień **adres IP** na inny adres IP, który jest dostępny w podsieci.
7.  Wybierz pozycję **Zapisz**.
8.  Maszyna wirtualna zostanie ponownie uruchomiona w celu zainicjowania nowej karty sieciowej w systemie.
9.  Spróbuj nawiązać połączenie RDP z maszyną. Jeśli to się powiedzie, możesz wybrać przywrócenie prywatnego adresu IP z powrotem do oryginalnego.  

#### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. Upewnij się, że masz zainstalowaną [najnowszą Azure PowerShell](/powershell/azure/) .
2. Otwórz sesję Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Spróbuj nawiązać połączenie RDP z maszyną. Jeśli chcesz, możesz zmienić prywatny adres IP z powrotem na oryginalny. W przeciwnym razie możesz ją zachować. 

## <a name="delete-the-unavailable-nics"></a>Usuń niedostępne karty sieciowe
Po dodaniu pulpitu zdalnego do komputera należy usunąć stare karty sieciowe, aby uniknąć potencjalnego problemu:

1.  Otwórz Menedżera urządzeń.
2.  Wybierz pozycję **Wyświetl**  >  **Pokaż ukryte urządzenia**.
3.  Wybierz pozycję **karty sieciowe**. 
4.  Sprawdź karty o nazwie "Microsoft Hyper-V karcie sieciowej".
5.  Może zostać wyświetlona niedostępna karta, która jest wyszarzona. Kliknij prawym przyciskiem myszy kartę, a następnie wybierz pozycję Odinstaluj.

    ![obraz karty sieciowej](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Odinstaluj tylko niedostępne karty o nazwie "Microsoft Hyper-V karcie sieciowej". W przypadku odinstalowania dowolnej z pozostałych kart ukrytych może dojść do dodatkowych problemów.
    >
    >

6.  Teraz wszystkie niedostępne karty powinny zostać wyczyszczone w systemie.
