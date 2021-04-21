---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — integracja App Service z Application Gateway | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — integracja App Service z Application Gateway
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
ms.openlocfilehash: d30cc27fc3c546619e85bb9aabd0b31c10102e96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787813"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrowanie App Service z Application Gateway przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy Azure App Service internetową, usługę Azure Virtual Network i Application Gateway. Następnie ogranicza ruch dla aplikacji internetowej tak, aby pochodził tylko z Application Gateway podsieci.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń do tworzenia grupy zasobów, aplikacji usługi App Service, bazy danych Cosmos DB i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az network vnet create`](/cli/azure/network/vnet#az_network_vnet_create) | Tworzy sieć wirtualną. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az_network_public_ip_create) | Tworzy publiczny adres IP. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az_network_public_ip_show) | Pokaż szczegóły publicznego adresu IP. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Tworzy App Service internetową. |
| [`az webapp show`](/cli/azure/webapp#az_webapp_show) | Pokazywanie szczegółów App Service internetowej. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | Dodaje ograniczenie dostępu do App Service internetowej. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az_network_application_gateway_create) | Tworzy Application Gateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az_network-application-gateway-http_settings_update) | Aktualizacje Application Gateway ustawienia http. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
