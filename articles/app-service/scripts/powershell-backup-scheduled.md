---
title: 'PowerShell: Tworzenie zaplanowanej kopii zapasowej'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak utworzyć zaplanowaną kopię zapasową dla aplikacji.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: c53872973dad3d438d0d948c4eaebaf0cb7ba4de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075900"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>Tworzenie zaplanowanej kopii zapasowej aplikacji internetowej przy użyciu programu PowerShell

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service wraz z jej powiązanymi zasobami, a następnie tworzy zaplanowaną kopię zapasową na potrzeby tej aplikacji. 

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

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
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Tworzy konto magazynu. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Tworzy kontener usługi Azure Storage. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Generuje token SAS dla kontenera usługi Azure Storage. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Tworzy aplikację internetową. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | Edytuje konfigurację kopii zapasowej dla aplikacji internetowej. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | Pobiera konfigurację kopii zapasowej dla aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
