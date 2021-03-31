---
title: Szybki Start — Menedżer zasobów szablonu kopia zapasowa maszyny wirtualnej
description: Dowiedz się, jak utworzyć kopię zapasową maszyn wirtualnych przy użyciu szablonu Azure Resource Manager
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88826535"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Szybki Start: Tworzenie kopii zapasowej maszyny wirtualnej na platformie Azure przy użyciu szablonu ARM

[Azure Backup](backup-overview.md) tworzyć kopie zapasowe maszyn i aplikacji lokalnych oraz maszyn wirtualnych platformy Azure. W tym artykule opisano sposób tworzenia kopii zapasowej maszyny wirtualnej platformy Azure z szablonem Azure Resource Manager (szablon ARM) i Azure PowerShell. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu ARM w celu utworzenia magazynu Recovery Servicesowego. Aby uzyskać więcej informacji na temat opracowywania szablonów ARM, zapoznaj się z [dokumentacją Azure Resource Manager](../azure-resource-manager/index.yml) i [odwołaniem do szablonu](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) jest kontenerem logicznym, który przechowuje dane kopii zapasowej chronionych zasobów, takich jak maszyny wirtualne platformy Azure. Po uruchomieniu zadania tworzenia kopii zapasowej tworzy punkt odzyskiwania w magazynie Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie. Alternatywnie można utworzyć kopię zapasową maszyny wirtualnej przy użyciu [Azure PowerShell](./quick-backup-vm-powershell.md), [interfejsu wiersza polecenia platformy Azure](quick-backup-vm-cli.md)lub [Azure Portal](quick-backup-vm-portal.md).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Ten szablon pozwala wdrożyć prostą maszynę wirtualną z systemem Windows i magazyn Recovery Services skonfigurowany przy użyciu DefaultPolicy na potrzeby ochrony.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

Zasoby zdefiniowane w szablonie są następujące:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. COMPUTE/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. RecoveryServices/magazyny**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby wdrożyć szablon, wybierz opcję **Wypróbuj** , aby otworzyć Azure Cloud Shell, a następnie wklej następujący skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell służy do wdrażania szablonu ARM w tym przewodniku Szybki Start. Do wdrażania szablonów można także używać [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), interfejsu [wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)i [interfejsów API REST](../azure-resource-manager/templates/deploy-rest.md) .

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

### <a name="start-a-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej

Szablon tworzy maszynę wirtualną i włącza ją z powrotem na maszynie wirtualnej. Po wdrożeniu szablonu należy uruchomić zadanie tworzenia kopii zapasowej. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorowanie zadania tworzenia kopii zapasowej

Aby monitorować zadanie tworzenia kopii zapasowej, zobacz [monitorowanie zadania tworzenia kopii zapasowej](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

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
- [Dowiedz się, jak](../azure-resource-manager/templates/template-tutorial-create-first-template.md) tworzyć szablony ARM.
