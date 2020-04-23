---
title: Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
description: Dowiedz się, jak zmaksymalizować korzyści związane z pakietem Windows Software Assurance, aby przenieść licencje lokalne na platformę Azure
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: f84d4fcd85f1e718f414e63bbe76fd29fa32427d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869564"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Korzyść użycia hybrydowego platformy Azure dla systemu Windows Server
W przypadku klientów korzystających z pakietu Software Assurance usługa Azure Hybrid Benefit dla systemu Windows Server umożliwia korzystanie z lokalnych licencji systemu Windows Server i uruchamianie maszyn wirtualnych systemu Windows na platformie Azure po obniżonych kosztach. Korzyści hybrydowe platformy Azure dla systemu Windows Server umożliwiają wdrażanie nowych maszyn wirtualnych w systemie operacyjnym Windows. W tym artykule opisano kroki dotyczące wdrażania nowych maszyn wirtualnych z korzyścią hybrydową platformy Azure dla systemu Windows Server i sposobu aktualizowania istniejących uruchomionych maszyn wirtualnych. Aby uzyskać więcej informacji na temat korzyści hybrydowych platformy Azure dla systemu Windows Server i oszczędności kosztów, zobacz [stronę Licencjonowania hybrydowego platformy Azure dla systemu Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Każda licencja 2-procesorowa lub każdy zestaw licencji 16-rdzeniowych ma prawo do dwóch wystąpień do 8 rdzeni lub jednego wystąpienia do 16 rdzeni. Licencji usługi Azure Hybrid Benefit for Standard Edition można używać tylko raz w środowisku lokalnym lub na platformie Azure. Korzyści z usługi Datacenter Edition umożliwiają jednoczesne użycie zarówno lokalnie, jak i na platformie Azure.

Korzystanie z usługi Azure Hybrid Benefit dla systemu Windows Server z dowolnymi maszynami wirtualnymi z systemem operacyjnym Windows Server są teraz obsługiwane we wszystkich regionach, w tym na maszynach wirtualnych z dodatkowym oprogramowaniem, takim jak SQL Server lub oprogramowanie marketplace innych firm. 


## <a name="classic-vms"></a>Klasyczne maszyny wirtualne

W przypadku klasycznych maszyn wirtualnych obsługiwane jest tylko wdrażanie nowych maszyn wirtualnych z lokalnych obrazów niestandardowych. Aby skorzystać z możliwości obsługiwanych w tym artykule, należy najpierw przeprowadzić migrację klasycznych maszyn wirtualnych do modelu Menedżera zasobów.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Sposoby korzystania z korzyści hybrydowych platformy Azure dla systemu Windows Server
Istnieje kilka sposobów korzystania z maszyn wirtualnych systemu Windows z korzyścią hybrydową platformy Azure:

1. Maszyny wirtualne można wdrażać z jednego z obrazów systemu Windows Server w portalu Azure Marketplace
2. Niestandardową maszynę wirtualną można przekazać i wdrożyć przy użyciu szablonu usługi Resource Manager lub programu Azure PowerShell
3. Można przełączać i konwertować istniejącą maszynę wirtualną między uruchamianiem z korzyścią hybrydową platformy Azure lub płacić na żądanie dla systemu Windows Server
4. Korzyści hybrydowe platformy Azure dla systemu Windows Server można również zastosować w zestawie skalowania maszyny wirtualnej


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Tworzenie maszyny Wirtualnej z korzyścią hybrydową platformy Azure dla systemu Windows Server
Wszystkie obrazy oparte na systemie Operacyjnym Windows Server są obsługiwane dla korzyści hybrydowych platformy Azure dla systemu Windows Server. Można użyć obrazów pomocy technicznej platformy Azure lub przekazać własne niestandardowe obrazy systemu Windows Server. 

### <a name="portal"></a>Portal
Aby utworzyć maszynę wirtualną z korzyścią hybrydową platformy Azure dla systemu Windows Server, użyj przełącznika w sekcji "Oszczędzaj pieniądze".

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Szablon
W szablonach Menedżera zasobów należy `licenseType` określić dodatkowy parametr. Możesz dowiedzieć się więcej o [tworzenie szablonów usługi Azure Resource Manager](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Konwertowanie istniejącej maszyny Wirtualnej przy użyciu korzyści hybrydowej platformy Azure dla systemu Windows Server
Jeśli masz istniejącą maszynę wirtualną, którą chcesz przekonwertować, aby skorzystać z korzyści hybrydowych platformy Azure dla systemu Windows Server, możesz zaktualizować typ licencji maszyny Wirtualnej, postępując zgodnie z poniższymi instrukcjami.

> [!NOTE]
> Zmiana typu licencji na maszynie wirtualnej nie powoduje ponownego uruchomienia systemu ani nie powoduje połączenia usługi.  Jest to po prostu aktualizacja flagi metadanych.
> 

### <a name="portal"></a>Portal
Z bloku maszyny Wirtualnej portalu możesz zaktualizować maszynę wirtualną, aby korzystała z korzyści hybrydowych platformy Azure, wybierając opcję "Konfiguracja" i przełączając opcję "Korzyści hybrydowe platformy Azure"

### <a name="powershell"></a>PowerShell
- Konwertowanie istniejących maszyn wirtualnych z systemem Windows Server na korzyść hybrydową platformy Azure dla systemu Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Konwertowanie maszyn wirtualnych z systemem Windows Server z korzyścią z powrotem do płatności zgodnie z rzeczywistymi oczekiwaniami

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>Interfejs wiersza polecenia
- Konwertowanie istniejących maszyn wirtualnych z systemem Windows Server na korzyść hybrydową platformy Azure dla systemu Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Jak sprawdzić, czy maszyna wirtualna korzysta z korzyści wynikających z licencjonowania
Po wdrożeniu maszyny wirtualnej za pośrednictwem szablonu programu PowerShell, Menedżera zasobów lub portalu można zweryfikować to ustawienie w następujących metodach.

### <a name="portal"></a>Portal
Z bloku maszyny Wirtualnej portalu możesz wyświetlić przełącznik dla korzyści hybrydowych platformy Azure dla systemu Windows Server, wybierając kartę "Konfiguracja".

### <a name="powershell"></a>PowerShell
W poniższym przykładzie przedstawiono typ licencji dla pojedynczej maszyny Wirtualnej
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Dane wyjściowe:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ten wynik danych kontrastuje z następującą maszyną wirtualną wdrożoną bez korzystania z usługi Azure Hybrid Benefit dla systemu Windows Server:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Zmiana typu licencji na maszynie wirtualnej nie powoduje ponownego uruchomienia systemu ani nie powoduje połączenia usługi. Jest to tylko flaga licencjonowania metadanych.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Wyświetlanie listy wszystkich maszyn wirtualnych z korzyścią hybrydową platformy Azure dla systemu Windows Server w ramach subskrypcji
Aby wyświetlić i policzyć wszystkie maszyny wirtualne wdrożone przy za pomocą usługi Azure Hybrid Benefit dla systemu Windows Server, można uruchomić następujące polecenie z subskrypcji:

### <a name="portal"></a>Portal
W bloku zasobów zestawów zasobów grupy typu maszyn wirtualnych lub maszyn wirtualnych można wyświetlić listę wszystkich maszyn wirtualnych i typu licencjonowania, konfigurując kolumnę tabeli w celu uwzględnienia "korzyści hybrydowe platformy Azure". Ustawienie maszyny Wirtualnej może być w stanie "Włączone", "Nie włączone" lub "Nie obsługiwane".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Wdrażanie zestawu skalowania maszyny wirtualnej z korzyścią hybrydową platformy Azure dla systemu Windows Server
W ramach szablonów Menedżera zasobów zestawu skalowania maszyny wirtualnej należy określić dodatkowy parametr `licenseType` we właściwości VirtualMachineProfile. Można to zrobić podczas tworzenia lub aktualizowania dla zestawu skalowania za pomocą szablonu ARM, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub REST.

W poniższym przykładzie użyto szablonu ARM z obrazem centrum danych systemu Windows Server 2016:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Możesz również dowiedzieć się więcej o tym, jak [zmodyfikować zestaw skalowania maszyny wirtualnej,](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) aby uzyskać więcej sposobów aktualizowania zestawu skalowania.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [oszczędzaniu pieniędzy dzięki korzyści hybrydowej platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Dowiedz się więcej o [często zadawanych pytaniach dotyczących korzyści hybrydowych platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Dowiedz się więcej o [korzyściach z licencjonowania hybrydowego platformy Azure dla systemu Windows Server — szczegółowe wskazówki](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Dowiedz się więcej o [korzyściach hybrydowych platformy Azure dla systemów Windows Server i usługi Azure Site Recovery, dzięki że migracja aplikacji na platformę Azure](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/) jest jeszcze bardziej opłacalna
- Dowiedz się więcej o [systemie Windows 10 na platformie Azure dzięki prawu do hostingu wielodostępnego](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Dowiedz się więcej o [używaniu szablonów Menedżera zasobów](../../azure-resource-manager/management/overview.md)
