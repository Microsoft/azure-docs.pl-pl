---
title: Szybki start — szablon menedżera zasobów Kopia zapasowa maszyny Wirtualnej
description: Dowiedz się, jak zrobić kopii zapasowej maszyn wirtualnych za pomocą szablonu usługi Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458849"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Finansuj utworzenie kopii zapasowej maszyny wirtualnej na platformie Azure za pomocą szablonu Usługi Resource Manager

[Usługa Azure Backup](backup-overview.md) tworzą kopię zapasową maszyn i aplikacji lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule pokazano, jak zrobić z nim projekt kopii zapasowej maszyny Wirtualnej platformy Azure za pomocą szablonu usługi Resource Manager i programu Azure PowerShell. Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu Menedżera zasobów w celu utworzenia magazynu recover services. Aby uzyskać więcej informacji na temat tworzenia szablonów Menedżera zasobów, zobacz [Dokumentacja Menedżera zasobów](/azure/azure-resource-manager/) i odwołanie do [szablonu](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Alternatywnie można wywrzeć zapas maszynę wirtualną przy użyciu [usługi Azure PowerShell](./quick-backup-vm-powershell.md), [interfejsu wiersza polecenia platformy Azure](quick-backup-vm-cli.md)lub w [witrynie Azure portal.](quick-backup-vm-portal.md)

## <a name="create-a-vm-and-recovery-services-vault"></a>Tworzenie magazynu usług wirtualnych i odzyskiwania

[Magazyn usług odzyskiwania](backup-azure-recovery-services-vault-overview.md) to kontener logiczny, który przechowuje dane kopii zapasowej dla chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania kopii zapasowej tworzy punkt odzyskiwania wewnątrz magazynu usług odzyskiwania. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ten szablon umożliwia wdrożenie prostej usługi Windows VM i Recovery Services Vault skonfigurowane za pomocą defaultpolicy for protection.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

Zasoby zdefiniowane w szablonie to:

- [**Microsoft.Storage/storageKondyje**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Sieć Microsoft.Network/virtual Sieci**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMacyny**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoverySługs/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby wdrożyć szablon, wybierz pozycję **Wypróbuj,** aby otworzyć usługę Azure Cloud Shell, a następnie wklej następujący skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Program Azure PowerShell jest używany do wdrażania szablonu Menedżera zasobów w tym przewodniku Szybki start. [Witryna Azure portal](../azure-resource-manager/templates/deploy-portal.md), [interfejs wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)i interfejs API [odpoczynku](../azure-resource-manager/templates/deploy-rest.md) mogą być również używane do wdrażania szablonów.

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Szablon tworzy maszynę wirtualną i włącza z powrotem na maszynie Wirtualnej. Po wdrożeniu szablonu należy rozpocząć zadanie tworzenia kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Aby monitorować zadanie tworzenia kopii zapasowej, zobacz [Monitorowanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Oczyszczanie wdrożenia

Jeśli nie trzeba już wykonać kopii zapasowej maszyny Wirtualnej, można ją wyczyścić.

- Jeśli chcesz wypróbować przywracanie maszyny Wirtualnej, pomiń oczyszczanie.
- Jeśli użyto istniejącej maszyny Wirtualnej, można pominąć końcowe polecenie cmdlet [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby pozostawić grupę zasobów i maszynę wirtualną na miejscu.

Wyłącz ochronę, usuń punkty przywracania i przechowalnię. Then delete the resource group and associated VM resources, as follows:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania.

- [Dowiedz się, jak](tutorial-backup-vm-at-scale.md) zrobić kopii zapasowej maszyn wirtualnych w witrynie Azure portal.
- [Dowiedz się, jak](tutorial-restore-disk.md) szybko przywrócić maszynę wirtualną
- [Dowiedz się, jak](../azure-resource-manager/templates/template-tutorial-create-first-template.md) tworzyć szablony Menedżera zasobów.
