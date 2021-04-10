---
title: Szybki Start — Tworzenie zadania Azure Stream Analytics według szablonu Azure Resource Manager
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia zadania Azure Stream Analytics przy użyciu szablonu Azure Resource Manager.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 2e8e7890adcb6b91d79bbf4e119488e365410d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020081"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-an-arm-template"></a>Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM) do utworzenia zadania Azure Stream Analytics. Po utworzeniu zadania można sprawdzić poprawność wdrożenia.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, należy wykonać następujące czynności:

* Masz subskrypcję platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-streamanalytics-create/).

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json":::

Zasób platformy Azure zdefiniowany w szablonie to [Microsoft. StreamAnalytics/StreamingJobs](/azure/templates/microsoft.streamanalytics/streamingjobs): create an Azure Stream Analytics Job.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

W tej sekcji utworzysz zadanie Azure Stream Analytics przy użyciu szablonu ARM.

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy zadanie Azure Stream Analytics.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Podaj wymagane wartości, aby utworzyć zadanie Azure Stream Analytics.

   ![Tworzenie zadania Azure Stream Analytics przy użyciu szablonu Azure Resource Manager](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Tworzenie zadania Azure Stream Analytics przy użyciu szablonu Azure Resource Manager")

   Podaj następujące wartości:

   |Właściwość  |Opis  |
   |---------|---------|
   |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
   |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md). |
   |**Region**     | Wybierz pozycję **Wschodnie stany USA**. Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).        |
   |**Nazwa zadania Stream Analytics**     | Podaj nazwę dla zadania Stream Analytics.      |
   |**Liczba jednostek przesyłania strumieniowego**     |  Wybierz liczbę potrzebnych jednostek przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Opis i Dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).       |

3. Wybierz pozycję **Przeglądanie + tworzenie**, a następnie pozycję **Utwórz**.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal, aby sprawdzić zadanie Azure Stream Analytics lub użyć następującego interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby wyświetlić listę zasobów.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi samouczkami, możesz pozostawić te zasoby na miejscu. Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie Azure Stream Analytics zadania. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zadanie Azure Stream Analytics przy użyciu szablonu usługi ARM i zweryfikowano wdrożenie. Przejdź do następnego artykułu, aby dowiedzieć się, jak wyeksportować szablon ARM dla istniejącego zadania przy użyciu VS Code.

> [!div class="nextstepaction"]
> [Eksportowanie szablonu ARM zadania Azure Stream Analytics](resource-manager-export.md)
