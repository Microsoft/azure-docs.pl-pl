---
title: Szybki Start — Menedżer zasobów szablonu kopia zapasowa maszyny wirtualnej
description: Dowiedz się, jak utworzyć kopię zapasową maszyn wirtualnych przy użyciu szablonu Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81458849"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure przy użyciu szablonu Menedżer zasobów

[Azure Backup](backup-overview.md) tworzyć kopie zapasowe maszyn i aplikacji lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule opisano sposób tworzenia kopii zapasowej maszyny wirtualnej platformy Azure przy użyciu szablonu Menedżer zasobów i Azure PowerShell. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Menedżer zasobów w celu utworzenia magazynu usługi Recovery Services. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](/azure/azure-resource-manager/) [szablonu](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Alternatywnie można utworzyć kopię zapasową maszyny wirtualnej przy użyciu [Azure PowerShell](./quick-backup-vm-powershell.md), [interfejsu wiersza polecenia platformy Azure](quick-backup-vm-cli.md)lub [Azure Portal](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Tworzenie maszyny wirtualnej i magazynu Recovery Services

[Magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, który przechowuje dane kopii zapasowej chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ten szablon pozwala wdrożyć prostą maszynę wirtualną z systemem Windows i magazyn Recovery Services skonfigurowany przy użyciu DefaultPolicy na potrzeby ochrony.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

Zasoby zdefiniowane w szablonie są następujące:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. COMPUTE/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. RecoveryServices/magazyny**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby wdrożyć szablon, wybierz opcję **Wypróbuj** , aby otworzyć Azure Cloud Shell, a następnie wklej następujący skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

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

Azure PowerShell służy do wdrażania szablonu Menedżer zasobów w tym przewodniku Szybki Start. Do wdrażania szablonów można także używać [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), interfejsu [wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)i [interfejsów API REST](../azure-resource-manager/templates/deploy-rest.md) .

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Szablon tworzy maszynę wirtualną i włącza ją z powrotem na maszynie wirtualnej. Po wdrożeniu szablonu należy uruchomić zadanie tworzenia kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Aby monitorować zadanie tworzenia kopii zapasowej, zobacz [monitorowanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Wyczyść wdrożenie

Jeśli kopia zapasowa maszyny wirtualnej nie jest już potrzebna, można ją wyczyścić.

- Jeśli chcesz wypróbować przywracanie maszyny wirtualnej, Pomiń oczyszczanie.
- Jeśli użyto istniejącej maszyny wirtualnej, możesz pominąć końcowe polecenie cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) , aby pozostawić grupę zasobów i maszynę wirtualną.

Wyłącz ochronę, Usuń punkty przywracania i magazyn. Następnie Usuń grupę zasobów i skojarzone z nią zasoby maszyn wirtualnych w następujący sposób:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services, włączono ochronę maszyny wirtualnej i utworzono początkowy punkt odzyskiwania.

- [Dowiedz się, jak](tutorial-backup-vm-at-scale.md) utworzyć kopię zapasową maszyn wirtualnych w Azure Portal.
- [Dowiedz się, jak](tutorial-restore-disk.md) szybko przywrócić maszynę wirtualną
- [Dowiedz się, jak](../azure-resource-manager/templates/template-tutorial-create-first-template.md) tworzyć szablony Menedżer zasobów.
