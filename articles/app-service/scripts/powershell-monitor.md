---
title: 'PowerShell: monitorowanie za pomocą dzienników serwera sieci Web'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak monitorować aplikację za pomocą dzienników serwera sieci Web.
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: c9b3ab6f8115f68ead478c0de21e4661d3ca032c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80437929"
---
# <a name="monitor-a-web-appwith-web-server-logs"></a>Monitorowanie aplikacji internetowej za pomocą dzienników serwera internetowego

W tym scenariuszu utworzysz grupę zasobów, plan usługi App Service i aplikację internetową oraz skonfigurujesz aplikację internetową tak, aby włączyć dzienniki serwera internetowego. Następnie pobierzesz pliki dzienników do przejrzenia.

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure/overview), a następnie `Connect-AzAccount` Uruchom polecenie, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Monitor a web app with web server logs")]

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
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modyfikuje konfigurację aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
