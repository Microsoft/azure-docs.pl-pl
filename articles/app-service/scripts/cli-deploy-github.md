---
title: 'Interfejs wiersza polecenia: wdrażanie aplikacji z usługi GitHub'
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do automatyzowania wdrażania aplikacji App Service zarządzania nimi. W tym przykładzie pokazano, jak wdrożyć aplikację z usługi GitHub.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 655c98e5249b66b114dfbe1d88cf951001d07af2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787878"
---
# <a name="create-an-app-service-app-with-deployment-from-github-using-azure-cli"></a>Tworzenie aplikacji usługi App Service wraz wdrożeniem z usługi GitHub przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt służy do tworzenia aplikacji w usłudze App Service wraz z jej powiązanymi zasobami. Następnie za jego pomocą kod aplikacji jest wdrażany z publicznego repozytorium GitHub (bez ciągłego wdrażania). Aby uzyskać więcej informacji na temat wdrożenia z usługi GitHub z ciągłym wdrażaniem, zobacz [Tworzenie aplikacji z ciągłym wdrażaniem z usługi GitHub](cli-continuous-deployment-github.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create an app with deployment from GitHub")]

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
