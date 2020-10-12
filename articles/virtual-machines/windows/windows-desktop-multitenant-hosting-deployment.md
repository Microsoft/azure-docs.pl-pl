---
title: Jak wdrożyć system Windows 10 na platformie Azure z wielodostępnymi prawami hostingu
description: Dowiedz się, jak zmaksymalizować korzyści z pakietu Software Assurance systemu Windows, aby zapewnić lokalne licencje na platformę Azure z wielodostępnymi prawami hostingu.
author: xujing
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: ceb8b8b31963317ccbbd1aee9f1b2606afc5a5db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279029"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Jak wdrożyć system Windows 10 na platformie Azure z wielodostępnymi prawami hostingu 
W przypadku klientów z systemem Windows 10 Enterprise E3/E5 na użytkownika lub dostęp do pulpitu wirtualnego systemu Windows dla użytkownika (licencje subskrypcyjne użytkownika lub licencje subskrypcyjne użytkownika), wielodostępne prawa hostingu dla systemu Windows 10 umożliwiają przenoszenie licencji systemu Windows 10 do chmury i uruchamianie systemu Windows 10 Virtual Machines na platformie Azure bez płacenia za inną licencję. Aby uzyskać więcej informacji, zobacz [Obsługa wielu dzierżawców dla systemu Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> W tym artykule pokazano, jak zaimplementować zalety licencjonowania dla obrazów komputerów stacjonarnych z systemem Windows 10 Pro w witrynie Azure Marketplace.
> - W przypadku obrazów systemu Windows 7, 8,1, 10 Enterprise (x64) w witrynie Azure Marketplace dla subskrypcji MSDN zapoznaj się z [klientem systemu Windows na platformie Azure na potrzeby scenariuszy tworzenia i testowania](client-images.md)
> - W przypadku korzyści z licencjonowania systemu Windows Server zapoznaj się z artykułem [korzyści z używania hybrydowej platformy Azure dla obrazów systemu Windows Server](hybrid-use-benefit-licensing.md).
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Wdrażanie obrazu systemu Windows 10 z witryny Azure Marketplace 
W przypadku wdrożeń programu PowerShell, interfejsu wiersza polecenia i szablonów Azure Resource Manager można znaleźć obraz systemu Windows 10 z następującym wydawcą, ofertą i jednostką SKU.

| System operacyjny  |      PublisherName      |  Oferta | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | System Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | System Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | System Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | System Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Przekazywanie wirtualnego dysku twardego z systemem Windows 10 na platformę Azure
Jeśli przekazujesz uogólniony wirtualny dysk twardy z systemem Windows 10, Uwaga dla systemu Windows 10 nie włączono domyślnie wbudowanego konta administratora. Aby włączyć wbudowane konto administratora, należy uwzględnić następujące polecenie jako część niestandardowego rozszerzenia skryptu.

```powershell
Net user <username> /active:yes
```

Poniższy fragment kodu programu PowerShell oznacza, że wszystkie konta administratorów są oznaczone jako aktywne, w tym wbudowanego administratora. Ten przykład jest przydatny, jeśli wbudowana nazwa użytkownika administratora jest nieznana.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Więcej informacji: 
* [Jak przekazać dysk VHD na platformę Azure](upload-generalized-managed.md)
* [Jak przygotować wirtualny dysk twardy systemu Windows do przekazania do platformy Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Wdrażanie systemu Windows 10 z wielodostępnymi prawami hostingu
Upewnij się, że [zainstalowano i skonfigurowano najnowszą Azure PowerShell](/powershell/azure/). Po przygotowaniu wirtualnego dysku twardego Przekaż wirtualny dysk twardy do konta usługi Azure Storage za pomocą `Add-AzVhd` polecenia cmdlet w następujący sposób:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Wdrażanie przy użyciu wdrożenia szablonu Azure Resource Manager** W szablonach Menedżer zasobów można określić dodatkowy parametr dla `licenseType` . Więcej informacji na temat [tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Po przekazaniu wirtualnego dysku twardego na platformę Azure Edytuj szablon Menedżer zasobów, aby uwzględnić typ licencji w ramach dostawcy obliczeń i wdrożyć szablon w zwykły sposób:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Wdrażanie za pomocą programu PowerShell** W przypadku wdrażania maszyny wirtualnej z systemem Windows Server za pośrednictwem programu PowerShell masz dodatkowy parametr dla programu `-LicenseType` . Po przekazaniu wirtualnego dysku twardego na platformę Azure należy utworzyć maszynę wirtualną przy użyciu `New-AzVM` i określić typ licencjonowania w następujący sposób:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Sprawdź, czy maszyna wirtualna korzysta z korzyści licencjonowania
Po wdrożeniu maszyny wirtualnej za pomocą metody wdrażania programu PowerShell lub Menedżer zasobów należy sprawdzić typ licencji w następujący sposób `Get-AzVM` :
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Dane wyjściowe są podobne do następującego przykładu dla systemu Windows 10 z prawidłowym typem licencji:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ten wynik różni się od następującej maszyny wirtualnej wdrożonej bez licencjonowania korzyści z używania hybrydowej platformy Azure, takich jak maszyna wirtualna wdrożona bezpośrednio z galerii platformy Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Dodatkowe informacje na temat dołączania do usługi Azure AD
>[!NOTE]
>Platforma Azure obsługuje wszystkie maszyny wirtualne z systemem Windows przy użyciu wbudowanego konta administratora, które nie może zostać użyte do przyłączenia do usługi AAD. Na przykład *ustawienia > konta > dostęp do pracy lub szkoły > + Connect* nie będą działały. Aby ręcznie dołączyć do usługi Azure AD, należy utworzyć i zalogować się jako drugie konto administratora. Możesz również skonfigurować usługę Azure AD przy użyciu pakietu aprowizacji. Aby dowiedzieć się więcej, Użyj linku do sekcji *następne kroki* .
>
>

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [konfigurowaniu VDA dla systemu Windows 10](/windows/deployment/vda-subscription-activation)
- Dowiedz się więcej o [hostingu wielu dzierżawców dla systemu Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
