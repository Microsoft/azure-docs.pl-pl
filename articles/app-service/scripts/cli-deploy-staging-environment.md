---
title: 'Interfejs wiersza polecenia: wdrażanie do miejsca przejściowego'
description: Dowiedz się, jak zautomatyzować wdrażanie aplikacji App Service i zarządzanie nią za pomocą interfejsu wiersza polecenia platformy Azure. Ten przykład pokazuje, jak wdrożyć kod w miejscu przejściowym.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 6a84b6df5a47604cbceeb172597e955c478fe75f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88959803"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>Tworzenie aplikacji usługi App Service i wdrażanie kodu w środowisku przejściowym przy użyciu interfejsu wiersza polecenia platformy Azure

Ten przykładowy skrypt tworzy aplikację w usłudze App Service z dodatkowym miejscem wdrożenia o nazwie „staging”, a następnie wdraża przykładową aplikację w tym miejscu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create an app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację usługi App Service. |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az-webapp-deployment-slot-create) | Tworzy miejsce wdrożenia. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Kojarzy aplikację usługi App Service z repozytorium Git lub Mercurial. |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot?view=azure-cli-latest#az-webapp-deployment-slot-swap) | Zamienia określone miejsce wdrożenia na środowisko produkcyjne. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).