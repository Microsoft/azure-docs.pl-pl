---
title: Wdrażanie szablonów za pomocą Cloud Shell
description: Użyj Azure Resource Manager i Azure Cloud Shell do wdrożenia zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie Azure Resource Manager (szablon ARM).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028611"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Wdrażanie szablonów usługi ARM na podstawie Azure Cloud Shell

Za pomocą [Azure Cloud Shell](../../cloud-shell/overview.md) można wdrożyć szablon Azure Resource Manager (szablon ARM). Można wdrożyć szablon usługi ARM, który jest przechowywany zdalnie, lub szablon ARM przechowywany na lokalnym koncie magazynu dla Cloud Shell.

Można wdrożyć w dowolnym zakresie. W tym artykule przedstawiono wdrażanie do grupy zasobów.

## <a name="deploy-remote-template"></a>Wdróż zdalny szablon

Aby wdrożyć szablon zewnętrzny, podaj identyfikator URI szablonu dokładnie tak, jak w przypadku dowolnego wdrożenia zewnętrznego. Szablon zewnętrzny może znajdować się w repozytorium GitHub lub na zewnętrznym koncie magazynu.

1. Otwórz monit Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Otwieranie usługi Cloud Shell":::

1. Aby wdrożyć szablon, użyj następujących poleceń:

   # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Wdrażanie szablonu lokalnego

Aby wdrożyć szablon lokalny, należy najpierw przekazać szablon do konta magazynu, które jest połączone z sesją Cloud Shell.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz swoją grupę zasobów usługi Cloud Shell. Wzorzec nazwy to `cloud-shell-storage-<region>`.

   ![Wybieranie grupy zasobów](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Wybierz konto magazynu dla usługi Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Wybieranie konta magazynu":::

1. Wybierz **udziały plików**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Wybierz udziały plików":::

1. Wybierz domyślny udział plików dla Cloud Shell. Udział plików ma format nazwy `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Domyślny udział plików":::

1. Dodaj nowy katalog do przechowywania szablonów. Wybierz ten katalog.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Dodawanie katalogu":::

1. Wybierz pozycję **Przekaż**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Przekaż szablon":::

1. Znajdź i przekaż swój szablon.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Wybierz szablon":::

1. Otwórz monit Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Otwieranie usługi Cloud Shell":::

1. Przejdź do katalogu **CloudDrive** . Przejdź do katalogu, który został dodany do przechowywania szablonów.

1. Aby wdrożyć szablon, użyj następujących poleceń:

   # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o poleceniach wdrażania, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md) oraz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
- Aby wyświetlić podgląd zmian przed wdrożeniem szablonu, zobacz [Wdrażanie szablonu ARM](template-deploy-what-if.md).
