---
title: Nawiązywanie połączenia z konsolą maszyny wirtualnej na urządzeniu z systemem Azure Stack Edge — procesor GPU
description: Zawiera opis sposobu nawiązywania połączenia z konsolą maszyny wirtualnej na maszynie wirtualnej działającej na urządzeniu z systemem Azure Stack Edge z procesorem GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962692"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Nawiązywanie połączenia z konsolą maszyny wirtualnej na urządzeniu z systemem Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Rozwiązanie procesora GPU w Azure Stack Edge jest uruchamiane za pośrednictwem maszyn wirtualnych. W tym artykule opisano sposób nawiązywania połączenia z konsolą maszyny wirtualnej wdrożoną na urządzeniu. 

Konsola maszyny wirtualnej umożliwia dostęp do maszyn wirtualnych za pomocą funkcji klawiatury, myszy i ekranu przy użyciu powszechnie dostępnych narzędzi pulpitu zdalnego. Podczas wdrażania maszyny wirtualnej na urządzeniu można uzyskać dostęp do konsoli programu i rozwiązać wszelkie problemy. Można nawiązać połączenie z konsolą maszyny wirtualnej, nawet jeśli nie można zainicjować obsługi administracyjnej maszyny wirtualnej.


## <a name="workflow"></a>Przepływ pracy

Przepływ pracy wysokiego poziomu obejmuje następujące kroki:

1. Nawiąż połączenie z interfejsem programu PowerShell na urządzeniu.
1. Umożliwia dostęp do maszyny wirtualnej za pomocą konsoli.
1. Nawiąż połączenie z maszyną wirtualną za pomocą protokołu RDP (Remote Desktop Protocol).
1. Odwołaj dostęp do konsoli do maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania wstępne:

#### <a name="for-your-device"></a>Dla urządzenia

Należy mieć dostęp do aktywowanego urządzenia GPU z systemem Azure Stack Edge. Na urządzeniu musi być wdrożona co najmniej jedna maszyna wirtualna. Maszyny wirtualne można wdrażać za pośrednictwem Azure PowerShell, za pomocą szablonów lub za pośrednictwem Azure Portal.

#### <a name="for-client-accessing-the-device"></a>Do uzyskiwania dostępu do urządzenia przez klienta

Upewnij się, że masz dostęp do systemu klienckiego, który:

- Może uzyskać dostęp do interfejsu programu PowerShell urządzenia. Klient korzysta z [obsługiwanego systemu operacyjnego](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
- Klient korzysta z programu PowerShell 7,0 lub nowszego. Ta wersja programu PowerShell działa na klientach z systemami Windows, Mac i Linux. Zapoznaj się z instrukcjami dotyczącymi [instalowania programu PowerShell 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Ma możliwości pulpitu zdalnego. W zależności od tego, czy używasz systemu Windows, macOS lub Linux, należy zainstalować jednego z tych [klientów usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Ten artykuł zawiera instrukcje dotyczące [systemów Windows pulpit zdalny](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) i [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Łączenie z konsolą maszyny wirtualnej

Wykonaj następujące kroki, aby nawiązać połączenie z konsolą maszyny wirtualnej na urządzeniu.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Nawiązywanie połączenia z interfejsem programu PowerShell na urządzeniu

Pierwszym krokiem jest [nawiązanie połączenia z interfejsem programu PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) urządzenia. 

### <a name="enable-console-access-to-the-vm"></a>Włączanie dostępu konsolowego do maszyny wirtualnej

1.  W interfejsie programu PowerShell uruchom następujące polecenie, aby włączyć dostęp do konsoli maszyny wirtualnej.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. W przykładowych danych wyjściowych Zanotuj identyfikator maszyny wirtualnej. Będzie to potrzebne do późniejszego kroku.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Teraz możesz użyć klienta Pulpit zdalny, aby nawiązać połączenie z konsolą maszyny wirtualnej.

#### <a name="use-windows-remote-desktop"></a>Użyj Pulpit zdalny systemu Windows

1. Utwórz nowy plik tekstowy i wprowadź następujący tekst.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Zapisz plik jako **. rdp* w systemie klienta. Ten profil zostanie użyty do nawiązania połączenia z maszyną wirtualną.
1. Kliknij dwukrotnie profil, aby nawiązać połączenie z maszyną wirtualną. Podaj następujące poświadczenia:

    - **Nazwa użytkownika**: Zaloguj się jako EdgeARMUser.
    - **Hasło**: Podaj hasło lokalnego Azure Resource Manager dla Twojego urządzenia. Jeśli zapomnisz hasła, [zresetuj Azure Resource Manager hasło za pośrednictwem Azure Portal](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Użyj FreeRDP

Jeśli używasz FreeRDP na kliencie z systemem Linux, uruchom następujące polecenie: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Odwołaj dostęp do konsoli maszyny wirtualnej

Aby odwołać dostęp do konsoli maszyny wirtualnej, Wróć do interfejsu programu PowerShell urządzenia. Uruchom następujące polecenie:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Oto przykładowe dane wyjściowe:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Zalecamy, aby po zakończeniu korzystania z konsoli maszyny wirtualnej odwołać dostęp lub zamknąć okno programu PowerShell, aby zakończyć sesję. 

## <a name="next-steps"></a>Następne kroki

- Rozwiązywanie problemów z [Azure Stack EDGE Pro](azure-stack-edge-gpu-troubleshoot.md) w Azure Portal.