---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych (klasycznych) — Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych (klasycznych) przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e798f81102017b4d34c4b1b219a9f23035b0359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222840"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej (klasycznej) przy użyciu programu PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Można także [zarządzać statycznym prywatnym adresem IP w modelu wdrażania Menedżer zasobów](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Przykładowe polecenia programu PowerShell poniżej oczekują, że zostało już utworzone proste środowisko. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe opisane w temacie [Tworzenie sieci wirtualnej](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak sprawdzić, czy określony adres IP jest dostępny
Aby sprawdzić, czy adres IP *192.168.1.101* jest dostępny w sieci wirtualnej o nazwie *TestVNet*, uruchom następujące polecenie programu PowerShell i sprawdź wartość parametru *IsAvailable*:

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

Oczekiwane dane wyjściowe:

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak określić statyczny prywatny adres IP podczas tworzenia maszyny wirtualnej
Poniższy skrypt programu PowerShell tworzy nową usługę w chmurze o nazwie *TestService*, a następnie pobiera obraz z platformy Azure, tworzy maszynę wirtualną o nazwie *DNS01* w nowej usłudze w chmurze przy użyciu pobranego obrazu, ustawia maszynę wirtualną jako podsieć o nazwie *fronton* i ustawia *192.168.1.7* jako statyczny prywatny adres IP dla maszyny wirtualnej:

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

Oczekiwane dane wyjściowe:

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać informacje o statycznym prywatnym adresie IP dla maszyny wirtualnej
Aby wyświetlić informacje o statycznym prywatnym adresie IP dla maszyny wirtualnej utworzonej za pomocą powyższego skryptu, uruchom następujące polecenie programu PowerShell i obserwuj wartości dla *adresu IP*:

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

Oczekiwane dane wyjściowe:

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
InstanceUpgradeDomain       : 0
InstanceSize                : Small
HostName                    : rsR2-797
AvailabilitySetName         : 
DNSName                     : http://testservice000.cloudapp.net/
Status                      : Provisioning
GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
PublicIPAddress             : 
PublicIPName                : 
NetworkInterfaces           : {}
ServiceName                 : TestService
OperationDescription        : Get-AzureVM
OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
OperationStatus             : OK
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny wirtualnej
Aby usunąć statyczny prywatny adres IP dodany do maszyny wirtualnej w powyższym skrypcie, uruchom następujące polecenie programu PowerShell:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

Oczekiwane dane wyjściowe:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny wirtualnej
Aby dodać statyczny prywatny adres IP do maszyny wirtualnej utworzonej przy użyciu skryptu powyżej, uruchom następujące polecenie:

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

Oczekiwane dane wyjściowe:

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustaw adresy IP w ramach systemu operacyjnego

Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile nie jest to konieczne. Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do maszyny wirtualnej platformy Azure, lub możesz utracić łączność z maszyną wirtualną. Nigdy nie należy ręcznie przypisywać publicznego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zarezerwowanych publicznych adresów IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .
* Dowiedz się więcej o publicznych adresach [IP na poziomie wystąpienia (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Zapoznaj się z [interfejsami API REST zastrzeżony adres IP](/previous-versions/azure/reference/dn722420(v=azure.100)).