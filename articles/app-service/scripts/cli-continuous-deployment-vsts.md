---
title: Ciągłe wdrażanie z Azure Repos
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do automatyzowania wdrażania aplikacji App Service zarządzania nimi. W tym przykładzie pokazano, jak skonfigurować ciszę/Azure Repos.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: cfc9ffff21db92eef4a3a544cb042394078a4e6d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788029"
---
# <a name="create-an-app-service-app-with-continuous-deployment-using-azure-cli"></a>Tworzenie aplikacji usługi App Service z ciągłym wdrażaniem przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt tworzy aplikację w App Service z powiązanymi zasobami, a następnie konfiguruje ciągłe wdrażanie z Azure DevOps repozytorium. Do pracy z tym przykładem potrzebne są:

* Repozytorium Azure DevOps z kodem aplikacji, do których masz uprawnienia administracyjne.
* [Osobisty token dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) organizacji pakietu Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Create an app with continuous deployment from Azure DevOps")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Tworzy aplikację usługi App Service. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Kojarzy aplikację usługi App Service z repozytorium Git lub Mercurial. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
