---
title: 'PowerShell: przekazywanie plików przy użyciu protokołu FTP'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak przekazywać pliki do aplikacji przy użyciu protokołu FTP.
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 3b0eb74e49cbc2e5e5e0cc84e998796c85b73925
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88960075"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>Przekazywanie plików do aplikacji internetowej za pomocą protokołu FTP

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service wraz z jej powiązanymi zasobami, a następnie wdraża kod aplikacji internetowej przy użyciu protokołu FTP (za pośrednictwem funkcji [WebClient.UploadFile()](/dotnet/api/system.net.webclient.uploadfile?view=netcore-3.1)).

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie, `Connect-AzAccount` Aby utworzyć połączenie z platformą Azure.

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

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
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | Pobierz profil publikowania aplikacji internetowej. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).