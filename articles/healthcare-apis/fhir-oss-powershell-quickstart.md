---
title: 'PowerShell: Wdrażanie serwera FHIR na platformie Azure — Azure API for FHIR'
description: Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć serwer Microsoft Open Source FHIR przy użyciu programu PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817956"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Szybki Start: Wdrażanie serwera FHIR Open Source przy użyciu programu PowerShell

W tym przewodniku szybki start dowiesz się, jak wdrożyć serwer FHIR firmy Microsoft dla platformy Azure przy użyciu programu PowerShell.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wybierz nazwę grupy zasobów, która będzie zawierać zasoby, których zainicjowano, i utwórz ją:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Wdrażanie szablonu serwera FHIR

Repozytorium Microsoft FHIR Server for Azure [GitHub](https://github.com/Microsoft/fhir-server) zawiera szablon, który będzie wdrażał wszystkie niezbędne zasoby. Proces wdrażania trwa kilka minut, po utworzeniu i skonfigurowaniu wymaganych zasobów platformy Azure. Wdróż przy użyciu:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> Jeśli jeszcze nie zalogowano się, uruchom następujące polecenia jako pierwsze.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

Aby użyć istniejącej grupy zasobów, należy zmienić $rg wartości w wierszu definicji zmiennej $rg i w wierszu polecenia wdrożenia szablonu Azure ARM, jak pokazano w kodzie.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Sprawdź, czy serwer FHIR jest uruchomiony

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Potrwa to chwilę, zanim serwer zacznie reagować po raz pierwszy.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń grupę zasobów, wykonując następujące czynności:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono serwer Microsoft Open Source FHIR dla platformy Azure w ramach subskrypcji. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API FHIR przy użyciu programu Poster, zapoznaj się z samouczkiem dotyczącym programu.
 
>[!div class="nextstepaction"]
>[Dostęp do interfejsu API FHIR za pomocą programu Poster](access-fhir-postman-tutorial.md)
