---
title: Kierowanie ruchu dla dużej liczby aplikacji — interfejs wiersza polecenia platformy Azure — Traffic Manager
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: 86151efdc6d2b17c9eef722f2dc3c6306d5aa1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400232"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure

Ten skrypt tworzy grupę zasobów, dwa plany usługi App Service, dwie aplikacje sieci Web, profil programu Traffic Manager i dwa punkty końcowe Menedżera ruchu. Traffic Manager kieruje ruch do aplikacji w jednym regionie jako region podstawowy i do regionu pomocniczego, gdy aplikacja w regionie podstawowym jest niedostępna. Przed wykonaniem skryptu należy zmienić wartości MyWebApp, MyWebAppL1 i MyWebAppL2 na unikatowe wartości na platformie Azure. Po uruchomieniu skryptu możesz uzyskać dostęp do aplikacji w regionie podstawowym przy użyciu adresu URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po uruchomieniu przykładowego skryptu można użyć poniższego polecenia, aby usunąć grupę zasobów, App Service aplikację i wszystkie powiązane zasoby.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń do utworzenia grupy zasobów, aplikacji internetowej, profilu usługi Traffic Manager i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Tworzy plan usługi App Service. Jest to podobne do farmy serwerów dla Twojej aplikacji sieci Web platformy Azure. |
| [AZ webapp Web Create](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Tworzy aplikację internetową platformy Azure w ramach planu App Service. |
| [AZ Network Traffic-Manager profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Tworzy profil usługi Azure Traffic Manager. |
| [AZ Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Dodaje punkt końcowy do profilu usługi Azure Traffic Manager. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia App Service można znaleźć w [dokumentacji dotyczącej sieci platformy Azure](../cli-samples.md).
