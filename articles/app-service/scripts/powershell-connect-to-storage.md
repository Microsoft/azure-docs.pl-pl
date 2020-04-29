---
title: 'PowerShell: Nawiązywanie połączenia z kontem magazynu'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak połączyć aplikację z kontem magazynu.
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: d1c8951445c0be6a3d4b9c4763d719fc7d79d2d5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74685334"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>Łączenie aplikacji usługi App Service z kontem magazynu

Z tego scenariusza dowiesz się, jak utworzyć konto magazynu platformy Azure i aplikację usługi App Service. Następnie połączysz konto magazynu z aplikacją przy użyciu ustawień aplikacji.

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure/overview), a następnie `Connect-AzAccount` Uruchom polecenie, aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację usługi App Service i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację usługi App Service. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Tworzy konto magazynu. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Pobiera klucze dostępu dla konta usługi Azure Storage. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modyfikuje konfigurację aplikacji usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
