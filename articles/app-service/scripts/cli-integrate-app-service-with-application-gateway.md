---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Integruj App Service z Application Gateway | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Integruj App Service z Application Gateway
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3133d5f17643eeb1a0e9f77f7d0af6532c30592d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561446"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrowanie App Service z Application Gateway przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy aplikację sieci Web Azure App Service, Virtual Network platformy Azure i Application Gateway. Następnie ogranicza ruch aplikacji sieci Web wyłącznie z podsieci Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga wersji 2.0.74 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń do tworzenia grupy zasobów, aplikacji usługi App Service, bazy danych Cosmos DB i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az network vnet create`](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) | Tworzy sieć wirtualną. |
| [`az network public-ip create`](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) | Tworzy publiczny adres IP. |
| [`az network public-ip show`](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) | Pokaż szczegóły publicznego adresu IP. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację sieci Web App Service. |
| [`az webapp show`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-show) | Pokaż szczegóły aplikacji sieci Web App Service. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest#az-webapp-config-access-restriction-add) | Dodaje ograniczenie dostępu do aplikacji sieci Web App Service. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-create) | Tworzy Application Gateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings?view=azure-cli-latest#az-network-application-gateway-http-settings-update) | Aktualizuje Application Gateway ustawienia protokołu HTTP. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).