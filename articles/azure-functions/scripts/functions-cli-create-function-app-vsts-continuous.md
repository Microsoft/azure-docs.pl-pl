---
title: Tworzenie aplikacji funkcji przy użyciu wdrożenia DevOps — interfejs wiersza polecenia platformy Azure
description: Tworzenie aplikacji funkcji i wdrażanie kodu funkcji z usługi Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: f89da9fc146d753442f2a8c8aa38861e66c9a3d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934380"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Tworzenie funkcji na platformie Azure wdrożonej z usługi Azure DevOps

W tym temacie pokazano, jak za pomocą programu Azure Functions utworzyć aplikację funkcji [bezserwerowej](https://azure.microsoft.com/solutions/serverless/) przy użyciu [planu zużycia](../consumption-plan.md). Aplikacja funkcji, która jest kontenerem dla funkcji, jest ciągle wdrażana z repozytorium usługi Azure DevOps. 

Do wykonania instrukcji w tym temacie potrzebne są:

* Repozytorium usługi Azure DevOps zawierające projekt aplikacji funkcji, w którym masz uprawnienia administracyjne.
* [Osobisty token dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) do repozytorium usługi Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt umożliwia utworzenie aplikacji funkcji platformy Azure i wdrożenie kodu funkcji z usługi Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia, służące do utworzenia grupy zasobów, konta magazynu, aplikacji funkcji i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Tworzy konto magazynu wymagane przez aplikację funkcji. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Tworzy aplikację funkcji w [planie zużycia](../consumption-plan.md)bezserwerowego. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Kojarzy aplikację funkcji z repozytorium Git lub Mercurial. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).
