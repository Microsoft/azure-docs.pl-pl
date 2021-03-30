---
title: Stosowanie licencji systemu Windows do maszyn wirtualnych hosta sesji — Azure
description: Zawiera opis sposobu zastosowania licencji systemu Windows dla maszyn wirtualnych z systemem Windows dla komputerów stacjonarnych.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010144"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Zastosuj licencję systemu Windows na maszyny wirtualne hosta sesji

Klienci, którzy są prawidłowo licencjonowani do uruchamiania obciążeń pulpitu wirtualnego systemu Windows, mogą stosować licencję systemu Windows na maszynach wirtualnych hosta sesji i uruchamiać je bez płacenia za inną licencję. Aby uzyskać więcej informacji, zobacz [Cennik pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Sposoby korzystania z licencji pulpitu wirtualnego systemu Windows
Licencjonowanie usług pulpitu wirtualnego systemu Windows umożliwia zastosowanie licencji do dowolnej maszyny wirtualnej z systemem Windows lub Windows Server, która jest zarejestrowana jako Host sesji w puli hostów i odbiera połączenia użytkownika. Ta licencja nie ma zastosowania do maszyn wirtualnych, które działają jako serwery udziałów plików, kontrolery domeny i tak dalej.

Istnieje kilka sposobów używania licencji pulpitu wirtualnego systemu Windows:
- Można utworzyć pulę hostów i jej maszyny wirtualne hosta sesji za pomocą [oferty portalu Azure Marketplace](./create-host-pools-azure-marketplace.md). Maszyny wirtualne utworzone w ten sposób automatycznie mają zastosowana licencję.
- Pulę hostów i jej maszyny wirtualne hosta sesji można utworzyć przy użyciu [szablonu Azure Resource Manager GitHub](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). Maszyny wirtualne utworzone w ten sposób automatycznie mają zastosowana licencję.
- Możesz zastosować licencję do istniejącej maszyny wirtualnej hosta sesji. Aby to zrobić, najpierw postępuj zgodnie z instrukcjami w temacie [Tworzenie puli hostów przy użyciu programu PowerShell](./create-host-pools-powershell.md) , aby utworzyć pulę hostów i skojarzone maszyny wirtualne, a następnie wróć do tego artykułu, aby dowiedzieć się, jak zastosować licencję.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Stosowanie licencji systemu Windows na maszynę wirtualną hosta sesji
Upewnij się, że [zainstalowano i skonfigurowano najnowszą Azure PowerShell](/powershell/azure/). Uruchom następujące polecenie cmdlet programu PowerShell, aby zastosować licencję systemu Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna hosta sesji korzysta z korzyści licencjonowania
Po wdrożeniu maszyny wirtualnej Uruchom to polecenie cmdlet, aby zweryfikować typ licencji:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Na maszynie wirtualnej hosta sesji z założoną licencją systemu Windows zostanie wyświetlony następujący element:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

W przypadku maszyn wirtualnych bez zastosowanej licencji systemu Windows zostanie wyświetlony następujący element:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Uruchom następujące polecenie cmdlet, aby wyświetlić listę wszystkich maszyn wirtualnych hosta sesji z licencją systemu Windows zastosowanej w ramach subskrypcji platformy Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
