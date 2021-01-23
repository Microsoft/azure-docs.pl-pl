---
title: Resetowanie Usługi pulpitu zdalnego lub jego hasła administratora na maszynie wirtualnej z systemem Windows | Microsoft Docs
description: Informacje na temat resetowania hasła konta lub Usługi pulpitu zdalnego na maszynie wirtualnej z systemem Windows przy użyciu Azure Portal lub Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 720d25079e1350315c9f403a8215f650db49ceb7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743087"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Resetowanie Usługi pulpitu zdalnego lub jego hasła administratora na maszynie wirtualnej z systemem Windows
Jeśli nie można nawiązać połączenia z maszyną wirtualną z systemem Windows, możesz zresetować hasło administratora lokalnego lub zresetować konfigurację Usługi pulpitu zdalnego (nieobsługiwane przez kontrolery domeny systemu Windows). Możesz zresetować hasło w witrynie Azure Portal lub za pośrednictwem rozszerzenia VM Access w programie Azure PowerShell. Po zalogowaniu się na maszynie wirtualnej zresetuj hasło administratora lokalnego.  
Jeśli używasz programu PowerShell, upewnij się, że masz [zainstalowany i skonfigurowany najnowszy moduł programu PowerShell](/powershell/azure/) oraz że zalogowano się do subskrypcji platformy Azure. Możesz również [wykonać te kroki w przypadku maszyn wirtualnych utworzonych za pomocą klasycznego modelu wdrożenia](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp).

Możesz zresetować usługi pulpitu zdalnego oraz  poświadczenia, korzystając z następujących metod:

- [Zresetuj przy użyciu Azure Portal](#reset-by-using-the-azure-portal)

- [Resetowanie przy użyciu rozszerzenia VMAccess i programu PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Zresetuj przy użyciu Azure Portal

Najpierw Zaloguj się do [Azure Portal](https://portal.azure.com) a następnie wybierz pozycję **maszyny wirtualne** w menu po lewej stronie. 

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło do konta administratora lokalnego**

1. Wybierz maszynę wirtualną z systemem Windows, a następnie wybierz pozycję **zresetuj hasło** w obszarze **Pomoc techniczna i rozwiązywanie problemów**. Zostanie wyświetlone okno **Resetowanie hasła** .

2. Wybierz pozycję **zresetuj hasło**, wprowadź nazwę użytkownika i hasło, a następnie wybierz pozycję **Aktualizuj**. 

3. Spróbuj ponownie nawiązać połączenie z maszyną wirtualną.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Zresetuj konfigurację Usługi pulpitu zdalnego**

Ten proces spowoduje włączenie usługi Pulpit zdalny na maszynie wirtualnej i utworzenie reguły zapory dla domyślnego portu RDP 3389.

1. Wybierz maszynę wirtualną z systemem Windows, a następnie wybierz pozycję **zresetuj hasło** w obszarze **Pomoc techniczna i rozwiązywanie problemów**. Zostanie wyświetlone okno **Resetowanie hasła** . 

2. Wybierz pozycję **Resetuj konfigurację** , a następnie wybierz pozycję **Aktualizuj**. 

3. Spróbuj ponownie nawiązać połączenie z maszyną wirtualną.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Resetowanie przy użyciu rozszerzenia VMAccess i programu PowerShell

Najpierw upewnij się, że masz [zainstalowany i skonfigurowany najnowszy moduł programu PowerShell](/powershell/azure/) oraz że są one zalogowane do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="reset-the-local-administrator-account-password"></a>**Zresetuj hasło do konta administratora lokalnego**

- Zresetuj hasło administratora lub nazwę użytkownika przy użyciu polecenia cmdlet [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) programu PowerShell. `typeHandlerVersion`Ustawienie musi mieć wartość 2,0 lub większą, ponieważ wersja 1 jest przestarzała. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Jeśli wprowadzisz inną nazwę niż bieżące konto administratora lokalnego na maszynie wirtualnej, rozszerzenie VMAccess doda konto administratora lokalnego o tej nazwie i przypisze określone hasło do tego konta. Jeśli konto administratora lokalnego na maszynie wirtualnej istnieje, rozszerzenie VMAccess zresetuje hasło. Jeśli konto jest wyłączone, rozszerzenie VMAccess zostanie włączone.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Zresetuj konfigurację Usługi pulpitu zdalnego**

1. Zresetuj dostęp zdalny do maszyny wirtualnej za pomocą polecenia cmdlet [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) programu PowerShell. Poniższy przykład resetuje rozszerzenie dostępu o nazwie w `myVMAccess` maszynie wirtualnej o nazwie `myVM` w `myResourceGroup` grupie zasobów:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > W dowolnym momencie maszyna wirtualna może mieć tylko jednego agenta dostępu do maszyny wirtualnej. Aby ustawić właściwości agenta dostępu do maszyny wirtualnej, użyj `-ForceRerun` opcji. Jeśli używasz `-ForceRerun` programu, upewnij się, że używasz tej samej nazwy dla agenta dostępu do maszyny wirtualnej, który może zostać użyty w poprzednich poleceniach.

1. Jeśli nadal nie można połączyć się zdalnie z maszyną wirtualną, zobacz [Rozwiązywanie problemów pulpit zdalny połączenia z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md)z systemem Windows. Jeśli nastąpi utrata połączenia z kontrolerem domeny systemu Windows, musisz przywrócić go z kopii zapasowej kontrolera domeny.

## <a name="next-steps"></a>Następne kroki


- Jeśli instalacja rozszerzenia dostępu do maszyny wirtualnej platformy Azure nie powiedzie się, można [rozwiązać problemy z rozszerzeniem maszyny wirtualnej](../extensions/troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- Jeśli nie możesz zresetować hasła przy użyciu rozszerzenia dostępu do maszyny wirtualnej, możesz [zresetować lokalne hasło systemu Windows do trybu offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ta metoda jest bardziej zaawansowana i wymaga połączenia wirtualnego dysku twardego problematycznej maszyny wirtualnej z inną maszyną wirtualną. Wykonaj najpierw kroki opisane w tym artykule, a następnie spróbuj użyć metody resetowania hasła w trybie offline tylko wtedy, gdy te kroki nie działają.

- [Dowiedz się więcej o rozszerzeniach i funkcjach maszyn wirtualnych platformy Azure](../extensions/features-windows.md).

- [Połącz się z maszyną wirtualną platformy Azure przy użyciu protokołu RDP lub SSH](/previous-versions/azure/dn535788(v=azure.100)).


- [Rozwiązywanie problemów z połączeniami pulpit zdalny z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)z systemem Windows.
