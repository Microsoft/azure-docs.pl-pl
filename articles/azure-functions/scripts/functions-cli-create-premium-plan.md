---
title: Tworzenie aplikacji funkcji w planie Premium — interfejs wiersza polecenia platformy Azure
description: Tworzenie aplikacji funkcji w skalowalnym planie Premium na platformie Azure przy użyciu interfejsu wiersza polecenia platformy Azure
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d9f72fa433364f8d71ba44207d570bb827cd243
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786216"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Tworzenie aplikacji funkcji w planie Premium — interfejs wiersza polecenia platformy Azure

Ten przykładowy skrypt usługi Azure Functions tworzy aplikację funkcji, która jest kontenerem dla funkcji. Utworzona aplikacja funkcji używa skalowalnego [planu Premium.](../functions-premium-plan.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt tworzy aplikację funkcji przy użyciu [planu Premium.](../functions-premium-plan.md)

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto usługi Azure Storage. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az_functionapp_plan_create) | Tworzy plan Premium w [określonej sku.](../functions-premium-plan.md#available-instance-skus) |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Tworzy aplikację funkcji w planie usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
