---
title: Tworzenie certyfikatu zaświadczania platformy Azure przy użyciu szablonu Azure Resource Manager
description: Dowiedz się, jak utworzyć certyfikat zaświadczania platformy Azure przy użyciu szablonu Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144977"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Szybki Start: Tworzenie dostawcy zaświadczania platformy Azure przy użyciu szablonu ARM

[Zaświadczanie Microsoft Azure](overview.md) to rozwiązanie służące do zaświadczania zaufanych środowisk wykonywania (TEEs). Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia zasad zaświadczania Microsoft Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Zasoby platformy Azure zdefiniowane w szablonie:

- Microsoft. zaświadczanie/attestationProviders

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon.

    [![Wdróż na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Wybierz lub wprowadź następujące wartości.

    O ile nie zostanie on określony, użyj wartości domyślnej, aby utworzyć dostawcę zaświadczania.

    - **Nazwa dostawcy zaświadczania**: wybierz nazwę swojego dostawcy zaświadczania platformy Azure.
    - **Lokalizacja**: Wybierz lokalizację. Na przykład **Środkowe stany USA**.
    - **Znaczniki**: Wybierz lokalizację. Na przykład **Środkowe stany USA**.

1. Wybierz pozycję **Kup**. Po pomyślnym wdrożeniu zasobu zaświadczania otrzymujesz powiadomienie.

Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal, aby sprawdzić zasób zaświadczania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inna kompilacja zaświadczania na platformie Azure na tym przewodniku Szybki Start. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie zasobu zaświadczania. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

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

W tym przewodniku szybki start utworzono zasób zaświadczania przy użyciu szablonu usługi ARM i zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat zaświadczania platformy Azure, zobacz [Omówienie zaświadczania platformy Azure](overview.md).
