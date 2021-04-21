---
title: Tworzenie aplikacji funkcji w planie App Service — interfejs wiersza polecenia platformy Azure
description: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie aplikacji funkcji w ramach planu usługi App Service
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ea368714ded56d77a10e24b853144bdf1dd42dfe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786337"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Tworzenie aplikacji funkcji w ramach planu usługi App Service

Ten przykładowy skrypt usługi Azure Functions tworzy aplikację funkcji, która jest kontenerem dla funkcji. Tworzona aplikacja funkcji używa dedykowanego planu usługi App Service, co oznacza, że zasoby serwera są zawsze włączone.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten skrypt umożliwia utworzenie aplikacji funkcji platformy Azure używającej dedykowanego [planu usługi App Service](../dedicated-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Każde polecenie w tabeli stanowi link do dokumentacji polecenia. W tym skrypcie użyto następujących poleceń:

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Tworzy konto usługi Azure Storage. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az_functionapp_plan_create) | Tworzy plan Premium. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Tworzy aplikację funkcji w planie usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
