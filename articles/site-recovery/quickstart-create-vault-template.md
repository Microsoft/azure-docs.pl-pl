---
title: Przewodnik Szybki start tworzenia magazynu usługi Azure Recovery Services przy użyciu Azure Resource Manager szablonu.
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć magazyn usługi Azure Recovery Services przy użyciu szablonu usługi Azure Resource Manager (szablonu usługi ARM).
ms.date: 04/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 11a88b5485ad970802a65af31daccdb30a1c86df
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533255"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Szybki start: tworzenie magazynu usługi Recovery Services przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano sposób skonfigurowania magazynu usługi Recovery Services przy użyciu Azure Resource Manager usługi (szablonu USŁUGI ARM). Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR, business continuity and disaster recovery), dzięki czemu aplikacje biznesowe pozostają w trybie online podczas planowanych i nieplanowanych przestojów. Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, w tym replikacją, trybem failover i odzyskiwaniem.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

W szablonie zdefiniowano dwa zasoby platformy Azure:

- [Magazyny Microsoft.RecoveryServices:](/azure/templates/microsoft.recoveryservices/vaults)tworzy magazyn.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig:](/rest/api/backup/backupresourcestorageconfigs)konfiguruje ustawienia nadmiarowości kopii zapasowych magazynu.

Szablon zawiera opcjonalne parametry konfiguracji kopii zapasowej magazynu. Ustawienia nadmiarowości magazynu to magazyn lokalnie nadmiarowy (LRS) lub magazyn geograficznie nadmiarowy (GRS). Aby uzyskać więcej informacji, zobacz [Ustawianie nadmiarowości magazynu.](../backup/backup-create-rs-vault.md#set-storage-redundancy)

Aby uzyskać więcej szablonów usługi Azure Recovery Services, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Do wdrożenia szablonu wymagane **są pola Subskrypcja,** **Grupa zasobów** i **Nazwa magazynu.**

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz obraz **Deploy to Azure (Wd wdrażaj na platformie Azure).**

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Szablon do tworzenia magazynu usługi Recovery Services.":::

   - **Subskrypcja:** wybierz swoją subskrypcję platformy Azure.
   - **Grupa zasobów:** wybierz istniejącą grupę lub wybierz **pozycję Utwórz nową,** aby dodać grupę.
   - **Lokalizacja:** wartość domyślna to lokalizacja grupy zasobów i staje się niedostępna po wybraniu grupy zasobów.
   - **Nazwa magazynu:** podaj nazwę magazynu.
   - **Zmień typ magazynu:** wartość domyślna to **false.** Wybierz **wartość true** tylko wtedy, gdy musisz zmienić typ magazynu.
   - **Typ magazynu magazynu:** wartość domyślna to **GlobalRedundant.** Jeśli typ magazynu został ustawiony na **wartość true,** wybierz pozycję **LocallyRedundant**.
   - **Lokalizacja**: funkcja `[resourceGroup().location]` domyślnie jest lokalizacją grupy zasobów. Aby zmienić lokalizację, wprowadź wartość, taką jak **westus**.
   - Zaznacz pole wyboru **Wyrażam zgodę na powyższe warunki i postanowienia.**

1. Aby rozpocząć wdrażanie magazynu, wybierz **przycisk** Kup. Po pomyślnym wdrożeniu zostanie wyświetlone powiadomienie.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Wdrożenie magazynu powiodło się.":::

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Aby potwierdzić, że magazyn został utworzony, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

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

Jeśli planujesz używać nowych zasobów, nie są wymagane żadne działania. W przeciwnym razie możesz usunąć grupę zasobów i magazyn, które zostały utworzone w tym przewodniku Szybki start. Aby usunąć grupę zasobów i jej zasoby, użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.

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

W tym przewodniku Szybki start utworzono magazyn usługi Recovery Services. Aby dowiedzieć się więcej na temat odzyskiwania po awarii, przejdź do następnego artykułu Szybki start.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii](azure-to-azure-quickstart.md)
