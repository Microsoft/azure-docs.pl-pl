---
title: 'PowerShell: stale Wdrażaj z usługi GitHub'
description: Dowiedz się, jak używać Azure PowerShell do automatyzowania wdrażania App Service i zarządzania nimi. Ten przykład pokazuje, jak utworzyć aplikację przy użyciu ciągłej integracji/ciągłego wdrażania w witrynie GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: eee6ac9f9c469f9e1a9344ab4a30626c219d7836
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74685166"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Tworzenie aplikacji internetowej z ciągłym wdrażaniem z repozytorium GitHub

Ten przykładowy skrypt tworzy aplikację internetową w App Service z jej powiązanymi zasobami, a następnie konfiguruje [ciągłe wdrażanie](../deploy-continuous-deployment.md) z repozytorium GitHub. Aby uzyskać więcej informacji na temat wdrożenia z repozytorium GitHub bez ciągłego wdrażania, zobacz [Create a web app and deploy code from GitHub (Tworzenie aplikacji internetowej i wdrażanie kodu z repozytorium GitHub)](powershell-deploy-github.md).

W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdującej się w [przewodniku Azure PowerShell](/powershell/azure/overview), a następnie `Connect-AzAccount` Uruchom polecenie, aby utworzyć połączenie z platformą Azure. Ponadto upewnij się, że:

- Kod aplikacji znajduje się w publicznym lub prywatnym repozytorium GitHub, którego jesteś właścicielem. Aby uzyskać kompilacje automatyczne, należy przeprowadzić strukturę repozytorium zgodnie z tabelą [Przygotowanie repozytorium](../deploy-continuous-deployment.md#prepare-your-repository) .
- [W Twoim koncie usługi GitHub został utworzony osobisty token dostępu](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line).

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

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
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modyfikuje zasób w grupie zasobów. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
