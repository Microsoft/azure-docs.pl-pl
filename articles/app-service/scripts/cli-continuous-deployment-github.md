---
title: 'Interfejs wiersza polecenia: ciągłe wdrażanie z usługi GitHub'
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do automatyzowania wdrażania aplikacji App Service zarządzania nimi. W tym przykładzie pokazano, jak utworzyć aplikację przy użyciu usługi Ci/CD z usługi GitHub.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 6e1759e0279c99025d0975dd207ca56b7679d5f6
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482313"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Tworzenie aplikacji usługi App Service z ciągłym wdrażaniem z repozytorium GitHub za pomocą interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy aplikację w usłudze App Service z powiązanymi zasobami, a następnie konfiguruje ciągłe wdrażanie z repozytorium GitHub. Aby uzyskać więcej informacji na temat wdrożenia z repozytorium GitHub bez ciągłego wdrażania, zobacz [Create an app and deploy code from GitHub](cli-deploy-github.md) (Tworzenie aplikacji i wdrażanie kodu z repozytorium GitHub). Do pracy z tym przykładem potrzebne są:

* Repozytorium GitHub zawierające kod aplikacji, w którym masz uprawnienia administracyjne. Aby uzyskać automatyczne kompilacje, należy utworzyć strukturę repozytorium zgodnie z [tabelą Prepare your repository](../deploy-continuous-deployment.md#prepare-your-repository) (Przygotowywanie repozytorium).
* [Osobisty token dostępu](https://help.github.com/articles/creating-an-access-token-for-command-line-use) do konta w witrynie GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Tworzy aplikację usługi App Service. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config) | Kojarzy aplikację usługi App Service z repozytorium Git lub Mercurial. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
