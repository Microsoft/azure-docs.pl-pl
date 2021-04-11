---
title: Tworzenie nowego przełącznika wirtualnego w Azure Stack Edge za pośrednictwem programu PowerShell
description: Opisuje sposób tworzenia przełącznika wirtualnego na urządzeniu brzegowym Azure Stack przy użyciu programu PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556254"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Tworzenie nowego przełącznika wirtualnego w Azure Stack Edge procesor GPU przy użyciu programu PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób tworzenia nowego przełącznika wirtualnego na urządzeniu z systemem Azure Stack Edge. Można na przykład utworzyć nowy przełącznik wirtualny, jeśli chcesz, aby maszyny wirtualne łączyły się za pomocą innego fizycznego portu sieciowego.

## <a name="vm-deployment-workflow"></a>Przepływ pracy wdrożenia maszyny wirtualnej

1. Nawiąż połączenie z interfejsem programu PowerShell na urządzeniu.
2. Zbadaj dostępne interfejsy sieci fizycznej.
3. Utwórz przełącznik wirtualny.
4. Sprawdź, czy sieć wirtualna i podsieć są tworzone automatycznie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

- Masz dostęp do komputera klienckiego, który może uzyskać dostęp do interfejsu programu PowerShell urządzenia. Zobacz [nawiązywanie połączenia z interfejsem programu PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    Na komputerze klienckim musi być uruchomiony [obsługiwany system operacyjny](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).

- Użyj lokalnego interfejsu użytkownika, aby włączyć obliczenia na jednym z fizycznych interfejsów sieciowych na urządzeniu zgodnie z instrukcjami w temacie [Włączanie sieci obliczeniowej](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) na urządzeniu. 


## <a name="connect-to-the-powershell-interface"></a>Nawiązywanie połączenia z interfejsem programu PowerShell

[Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Zapytanie o dostępne interfejsy sieciowe

1. Użyj poniższego polecenia, aby wyświetlić listę interfejsów sieci fizycznych, na których można utworzyć nowy przełącznik wirtualny. Wybierz jeden z tych interfejsów sieciowych.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Oto przykładowe dane wyjściowe:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Wybierz interfejs sieciowy:

    - W stanie **up** . 
    - Nieużywane przez żadne istniejące przełączniki wirtualne. Obecnie można skonfigurować tylko jeden przełącznik vSwitch dla każdego interfejsu sieciowego. 
    
    Aby sprawdzić istniejący przełącznik wirtualny i skojarzenie interfejsu sieciowego, uruchom `Get-HcsExternalVirtualSwitch` polecenie.
 
    Oto przykładowe dane wyjściowe.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    W tym przypadku port 2 jest skojarzony z istniejącym przełącznikiem wirtualnym i nie powinien być używany.

## <a name="create-a-virtual-switch"></a>Tworzenie wirtualnego przełącznika

Użyj poniższego polecenia cmdlet, aby utworzyć nowy przełącznik wirtualny w określonym interfejsie sieciowym. Po zakończeniu tej operacji wystąpienia obliczeniowe mogą używać nowej sieci wirtualnej.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Użyj `Get-HcsExternalVirtualSwitch` polecenia, aby zidentyfikować nowo utworzony przełącznik. Nowy utworzony przełącznik ma nazwę `vswitch-<InterfaceAlias>` . 

Oto przykładowe dane wyjściowe:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Weryfikuj sieć, podsieć 

Po utworzeniu nowego przełącznika wirtualnego Azure Stack Edge procesor GPU automatycznie utworzy sieć wirtualną i podsieć, która odpowiada. Tej sieci wirtualnej można użyć podczas tworzenia maszyn wirtualnych.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Następne kroki

- [Wdrażaj maszyny wirtualne na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU Pro Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Wdrażaj maszyny wirtualne na urządzeniu z systemem Azure Stack Edge przy użyciu procesora GPU Pro Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
