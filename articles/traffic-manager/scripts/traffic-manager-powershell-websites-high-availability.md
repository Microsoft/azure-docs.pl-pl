---
title: Kierowanie ruchu dla dużej liczby aplikacji — Azure PowerShell-Traffic Manager
description: Przykładowy skrypt Azure PowerShell — kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: 114041a6ad0b311a1e01a692b9e6c36b0a8fb900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185380"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Kierowanie ruchu w celu zapewnienia wysokiej dostępności aplikacji przy użyciu Azure PowerShell

Ten skrypt tworzy grupę zasobów, dwa plany usługi App Service, dwie aplikacje sieci Web, profil programu Traffic Manager i dwa punkty końcowe Menedżera ruchu. Traffic Manager kieruje ruch do aplikacji w jednym regionie jako region podstawowy i do regionu pomocniczego, gdy aplikacja w regionie podstawowym jest niedostępna. Przed wykonaniem skryptu należy zmienić wartości MyWebApp, MyWebAppL1 i MyWebAppL2 na unikatowe wartości na platformie Azure. Po uruchomieniu skryptu możesz uzyskać dostęp do aplikacji w regionie podstawowym przy użyciu adresu URL mywebapp.trafficmanager.net.

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure), a następnie uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń do utworzenia grupy zasobów, aplikacji internetowej, profilu usługi Traffic Manager i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. Jest to podobne do farmy serwerów dla Twojej aplikacji sieci Web platformy Azure. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację internetową platformy Azure w ramach planu App Service. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Tworzy aplikację internetową platformy Azure w ramach planu App Service. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Tworzy profil usługi Azure Traffic Manager. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Dodaje punkt końcowy do profilu usługi Azure Traffic Manager. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Dodatkowe przykłady skryptów programu PowerShell dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).