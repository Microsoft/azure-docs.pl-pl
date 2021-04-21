---
title: Tworzenie certyfikatu Azure Attestation przy użyciu Azure Resource Manager szablonu
description: Dowiedz się, jak utworzyć certyfikat Azure Attestation przy użyciu Azure Resource Manager szablonu.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 7d70f9ebd071d6699412f56e9dca1abcebb38105
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834250"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Szybki start: tworzenie dostawcy Azure Attestation za pomocą szablonu usługi ARM

[Microsoft Azure atest to](overview.md) rozwiązanie do zaświadczenia zaufanych środowisk wykonawczego (TEE). Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu usługi Azure Resource Manager (arm) w celu utworzenia zasad Microsoft Azure zaświadczenia.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Zasoby platformy Azure zdefiniowane w szablonie:

- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon.

    [![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości.

    Jeśli nie zostanie określony, użyj wartości domyślnej, aby utworzyć dostawcę zaświadczenia.

    - **Nazwa dostawcy zaświadczenia:** wybierz nazwę dostawcy Azure Attestation aplikacji.
    - **Lokalizacja:** wybierz lokalizację. Na przykład **Środkowe stany USA**.
    - **Tagi:** wybierz lokalizację. Na przykład **Środkowe stany USA**.

1. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu zasobu zaświadczenia otrzymasz powiadomienie.

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz interfejsu Azure Portal można również użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Aby sprawdzić zasób Azure Portal, możesz użyć konsoli.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne Azure Attestation z tego przewodnika Szybki start. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie zasobu zaświadczenia. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono zasób zaświadczenia przy użyciu szablonu usługi ARM i zweryfikowano wdrożenie. Aby dowiedzieć się więcej o Azure Attestation, zobacz [Overview of Azure Attestation](overview.md)(Omówienie Azure Attestation ).
