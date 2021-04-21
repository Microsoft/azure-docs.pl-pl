---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — przekieruj ruch w celu wysokiej dostępności aplikacji | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — przekieruj ruch w celu wysokiej dostępności aplikacji
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 00964a49940b208144167e9fe1ff4248f46e35fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763203"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Przekieruj ruch w celu wysokiej dostępności aplikacji — interfejs wiersza polecenia platformy Azure

Ten skrypt tworzy grupę zasobów, dwa plany usługi App Service, dwie aplikacje internetowe, profil usługi Traffic Manager i dwa punkty końcowe usługi Traffic Manager. Traffic Manager kieruje ruch do aplikacji w jednym regionie jako region podstawowy i do regionu pomocniczego, gdy aplikacja w regionie podstawowym jest niedostępna. Przed wykonaniem skryptu należy zmienić wartości MyWebApp, MyWebAppL1 i MyWebAppL2 na unikatowe wartości na platformie Azure. Po uruchomieniu skryptu możesz uzyskać dostęp do aplikacji w regionie podstawowym przy użyciu adresu URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowego skryptu można użyć następującego polecenia, aby usunąć grupę zasobów, App Service aplikację i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń do utworzenia grupy zasobów, aplikacji internetowej, profilu usługi Traffic Manager i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az appservice plan create](/cli/azure/appservice/plan) | Tworzy plan usługi App Service. Przypomina to farmę serwerów dla aplikacji internetowej platformy Azure. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | Tworzy aplikację internetową platformy Azure w ramach App Service planu. |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Tworzy profil usługi Azure Traffic Manager. |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Dodaje punkt końcowy do profilu usługi Azure Traffic Manager. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe App Service skryptów interfejsu wiersza polecenia można znaleźć w Azure Networking [dokumentacji.](../cli-samples.md)