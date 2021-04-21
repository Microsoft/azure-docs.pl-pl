---
title: Stosowanie licencji systemu Windows do maszyn wirtualnych hosta sesji — Azure
description: Opisuje sposób stosowania licencji systemu Windows dla Windows Virtual Desktop wirtualnych.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: fa3c9f82e99536b07a27656e0143d6b2fcc89a44
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833872"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Stosowanie licencji systemu Windows do maszyn wirtualnych hosta sesji

Klienci, którzy mają odpowiednie licencje na uruchamianie obciążeń Windows Virtual Desktop, są uprawnieni do zastosowania licencji systemu Windows do maszyn wirtualnych hosta sesji i uruchamiania ich bez płacenia za inną licencję. Aby uzyskać więcej informacji, zobacz [Windows Virtual Desktop cennika](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Sposoby korzystania z licencji Windows Virtual Desktop licencji
Windows Virtual Desktop licencjonowania umożliwia zastosowanie licencji do dowolnej maszyny wirtualnej z systemem Windows lub Windows Server, która jest zarejestrowana jako host sesji w puli hostów i odbiera połączenia użytkowników. Ta licencja nie ma zastosowania do maszyn wirtualnych, które są uruchomione jako serwery udziałów plików, kontrolery domeny i tak dalej.

Istnieje kilka sposobów używania licencji Windows Virtual Desktop licencji:
- Pulę hostów i jej maszyny wirtualne hosta sesji można utworzyć przy [użyciu Azure Marketplace usługi](./create-host-pools-azure-marketplace.md). Maszyny wirtualne utworzone w ten sposób automatycznie mają zastosowaną licencję.
- Pulę hostów i jej maszyny wirtualne hosta sesji można utworzyć przy użyciu szablonu usługi [GitHub Azure Resource Manager usługi](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). Maszyny wirtualne utworzone w ten sposób automatycznie mają zastosowaną licencję.
- Możesz zastosować licencję do istniejącej maszyny wirtualnej hosta sesji. W tym celu najpierw postępuj zgodnie z instrukcjami w artykule Tworzenie puli hostów za pomocą programu [PowerShell,](./create-host-pools-powershell.md) aby utworzyć pulę hostów i skojarzone maszyny wirtualne, a następnie wróć do tego artykułu, aby dowiedzieć się, jak zastosować licencję.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Stosowanie licencji systemu Windows do maszyny wirtualnej hosta sesji
Upewnij się, że [zainstalowano i skonfigurowano najnowszą wersję Azure PowerShell](/powershell/azure/). Uruchom następujące polecenie cmdlet programu PowerShell, aby zastosować licencję systemu Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna hosta sesji korzysta z korzyści licencjonowania
Po wdrożeniu maszyny wirtualnej uruchom to polecenie cmdlet, aby sprawdzić typ licencji:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Maszyna wirtualna hosta sesji z zastosowaną licencją systemu Windows będzie wyglądać podobnie do tego:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Maszyny wirtualne bez zastosowanej licencji systemu Windows będą wyświetlać informacje podobne do tych:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Uruchom następujące polecenie cmdlet, aby wyświetlić listę wszystkich maszyn wirtualnych hosta sesji, które mają licencję systemu Windows zastosowaną w subskrypcji platformy Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Wymagania dotyczące wdrażania systemu Windows Server Usługi pulpitu zdalnego

W przypadku wdrażania systemu Windows Server 2019, 2016 lub 2012 R2 jako hostów Windows Virtual Desktop we wdrożeniu serwer licencji programu Usługi pulpitu zdalnego musi być dostępny z tych maszyn wirtualnych. Serwer Usługi pulpitu zdalnego licencji może być zlokalizowany lokalnie lub na platformie Azure. Aby uzyskać więcej informacji, [zobacz Aktywowanie Usługi pulpitu zdalnego licencji usługi](/windows-server/remote/remote-desktop-services/rds-activate-license-server).
