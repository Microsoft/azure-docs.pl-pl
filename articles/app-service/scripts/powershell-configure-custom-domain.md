---
title: 'PowerShell: przypisywanie domeny niestandardowej'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak przypisać domenę niestandardową do aplikacji.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 4629157142c17122c0aa6c74c1918c1d01d796c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80044738"
---
# <a name="assign-a-custom-domain-to-a-web-app-using-powershell"></a>Przypisywanie domeny niestandardowej do aplikacji internetowej przy użyciu programu PowerShell

Ten przykładowy skrypt służy do tworzenia aplikacji internetowej w usłudze App Service wraz z jej powiązanymi zasobami, a następnie mapowania domeny `www.<yourdomain>` na tę aplikację. 

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure/overview), a następnie `Connect-AzAccount` Uruchom polecenie, aby utworzyć połączenie z platformą Azure. Musisz też mieć dostęp do strony konfiguracji usługi DNS rejestratora domen.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Assign a custom domain to a web app")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację internetową. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modyfikuje plan usługi App Service, aby zmienić jej warstwę cenową. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modyfikuje konfigurację aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
