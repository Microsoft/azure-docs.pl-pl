---
title: Przewodnik Szybki Start dotyczący tworzenia magazynu usługi Azure Recovery Services przy użyciu szablonu Azure Resource Manager.
description: W tym przewodniku szybki start dowiesz się, jak utworzyć magazyn usługi Azure Recovery Services przy użyciu szablonu Azure Resource Manager (szablon ARM).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/29/2020
ms.openlocfilehash: c1c6d625c67130a4e3608cee6134b8c81404c01b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745885"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Szybki Start: Tworzenie magazynu Recovery Services przy użyciu szablonu ARM

W tym przewodniku szybki start opisano sposób konfigurowania magazynu Recovery Services przy użyciu szablonu Azure Resource Manager (szablon ARM). Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR), dzięki czemu aplikacje biznesowe pozostają w trybie online podczas planowanych i nieplanowanych przestojów. Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, w tym replikacji, trybu failover i odzyskiwania.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz aktywnej subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

- [Magazyny Microsoft. RecoveryServices](/azure/templates/microsoft.recoveryservices/vaults): tworzy magazyn.
- [Microsoft. RecoveryServices/magazyny/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): konfiguruje ustawienia nadmiarowości kopii zapasowej magazynu.

Szablon zawiera opcjonalne parametry konfiguracji kopii zapasowej magazynu. Ustawienia nadmiarowości magazynu to magazyn lokalnie nadmiarowy (LRS) lub magazyn Geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji, zobacz [Ustawianie nadmiarowości magazynu](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Aby uzyskać więcej szablonów Recovery Services platformy Azure, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon, wymagana jest **subskrypcja** , **Grupa zasobów** i **Nazwa magazynu** .

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz pozycję **Wdróż na platformie Azure** .

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Szablon służący do tworzenia magazynu Recovery Services.":::

   - **Subskrypcja** : wybierz subskrypcję platformy Azure.
   - **Grupa zasobów** : wybierz istniejącą grupę lub wybierz pozycję **Utwórz nową** , aby dodać grupę.
   - **Lokalizacja** : wartość domyślna lokalizacji grupy zasobów i jest niedostępna po wybraniu grupy zasobów.
   - **Nazwa magazynu** : Podaj nazwę magazynu.
   - **Zmień typ magazynu** : wartość domyślna to **false** . Wybierz **wartość true** tylko wtedy, gdy musisz zmienić typ magazynu dla magazynu.
   - **Typ magazynu** magazynu: wartość domyślna to **GloballyRedundant** . Jeśli typ magazynu został ustawiony na **wartość true** , wybierz pozycję **LocallyRedundant** .
   - **Lokalizacja** : funkcja `[resourceGroup().location]` Domyślnie jest lokalizacją grupy zasobów. Aby zmienić lokalizację, wprowadź wartość taką jak **zachodnie** .
   - Zaznacz pole wyboru **Akceptuję warunki i postanowienia podane powyżej** .

1. Aby rozpocząć wdrażanie magazynu, wybierz przycisk **Kup** . Po pomyślnym wdrożeniu zostanie wyświetlone powiadomienie.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Szablon służący do tworzenia magazynu Recovery Services.":::

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby upewnić się, że magazyn został utworzony, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

Następujące dane wyjściowe to fragment informacji o magazynie:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz korzystanie z nowych zasobów, nie jest wymagana żadna akcja. W przeciwnym razie można usunąć grupę zasobów i magazyn, które zostały utworzone w tym przewodniku Szybki Start. Aby usunąć grupę zasobów i jej zasoby, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono magazyn Recovery Services. Aby dowiedzieć się więcej o odzyskiwaniu po awarii, przejdź do następnego artykułu Szybki Start.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii](azure-to-azure-quickstart.md)
