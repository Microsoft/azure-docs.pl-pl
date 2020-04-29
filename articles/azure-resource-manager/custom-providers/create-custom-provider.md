---
title: Utwórz dostawcę zasobów
description: Opisuje sposób tworzenia dostawcy zasobów i wdrażania jego niestandardowych typów zasobów.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75649869"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Szybki Start: Tworzenie niestandardowego dostawcy i wdrażanie zasobów niestandardowych

W tym przewodniku szybki start utworzysz własnego dostawcę zasobów i wdrożono niestandardowe typy zasobów dla tego dostawcy zasobów. Aby uzyskać więcej informacji o dostawcach niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku Szybki Start, należy wywołać operacje REST. Istnieją [różne sposoby wysyłania żądań REST](/rest/api/azure/). Jeśli nie masz jeszcze narzędzia dla operacji REST, zainstaluj [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie wiersza polecenia Open Source, które upraszcza wywoływanie interfejsu API Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Wdróż dostawcę niestandardowego

Aby skonfigurować niestandardowego dostawcę, wdróż [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) w subskrypcji platformy Azure.

Po wdrożeniu szablonu subskrypcja obejmuje następujące zasoby:

* Aplikacja funkcji z operacjami dotyczącymi zasobów i akcji.
* Konto magazynu do przechowywania użytkowników utworzonych za pomocą dostawcy niestandardowego.
* Niestandardowy dostawca definiujący niestandardowe typy zasobów i akcje. Używa punktu końcowego aplikacji funkcji do wysyłania żądań.
* Zasób niestandardowy od niestandardowego dostawcy.

Aby wdrożyć niestandardowego dostawcę przy użyciu programu PowerShell, użyj polecenia:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Można też wdrożyć rozwiązanie przy użyciu następującego przycisku:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Wyświetlanie niestandardowego dostawcy i zasobu

W portalu Dostawca niestandardowy jest ukrytym typem zasobu. Aby upewnić się, że dostawca zasobów został wdrożony, przejdź do grupy zasobów. Wybierz opcję **wyświetlania typów ukrytych**.

![Pokaż ukryte typy zasobów](./media/create-custom-provider/show-hidden.png)

Aby wyświetlić niestandardowy typ zasobu, który został wdrożony, użyj operacji GET dla typu zasobu.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Za pomocą ARMClient:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Otrzymasz odpowiedź:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Akcja wywołania

Dostawca niestandardowy ma również akcję o nazwie **ping**. Kod, który przetwarza żądanie, jest implementowany w aplikacji funkcji. Akcja ping odpowiada za pomocą powitania.

Aby wysłać żądanie ping, należy użyć operacji POST dla dostawcy niestandardowego.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Za pomocą ARMClient:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Otrzymasz odpowiedź:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Utwórz typ zasobu

Aby utworzyć niestandardowy typ zasobu, można wdrożyć zasób w szablonie. To podejście jest wyświetlane w szablonie wdrożonym w tym przewodniku Szybki Start. Możesz również wysłać żądanie PUT dla typu zasobu.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Za pomocą ARMClient:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Otrzymasz odpowiedź:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](overview.md).
