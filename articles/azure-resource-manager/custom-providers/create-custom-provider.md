---
title: Utwórz dostawcę zasobów
description: Opisuje sposób tworzenia dostawcy zasobów i wdrażania jego niestandardowych typów zasobów.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 541d140716e52b4fe1db4bc999682914a380a5f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85368111"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Szybki Start: Tworzenie niestandardowego dostawcy i wdrażanie zasobów niestandardowych

W tym przewodniku szybki start utworzysz własnego dostawcę zasobów i wdrożono niestandardowe typy zasobów dla tego dostawcy zasobów. Aby uzyskać więcej informacji o dostawcach niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby wykonać kroki opisane w tym przewodniku Szybki Start, należy wywołać `REST` operacje. Istnieją [różne sposoby wysyłania żądań REST](/rest/api/azure/).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

- Polecenia [niestandardowych dostawców](/cli/azure/ext/custom-providers/custom-providers/resource-provider) wymagają rozszerzenia. Aby uzyskać więcej informacji, zobacz [Korzystanie z rozszerzeń przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).
- Przykłady użycia interfejsu wiersza polecenia platformy Azure `az rest` do `REST` żądania. Aby uzyskać więcej informacji, zobacz [AZ REST](/cli/azure/reference-index#az-rest).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

- Polecenia programu PowerShell są uruchamiane lokalnie przy użyciu programu PowerShell 7 lub nowszego oraz modułów Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).
- Jeśli nie masz jeszcze narzędzia do `REST` wykonywania operacji, zainstaluj [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie wiersza polecenia Open Source, które upraszcza wywoływanie interfejsu API Azure Resource Manager.
- Po zainstalowaniu **ARMClient** można wyświetlić informacje o użyciu z wiersza polecenia programu PowerShell, wpisując: `armclient.exe` . Możesz też przejść do [witryny typu wiki ARMClient](https://github.com/projectkudu/ARMClient/wiki).

---

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-custom-provider"></a>Wdróż dostawcę niestandardowego

Aby skonfigurować niestandardowego dostawcę, wdróż [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) w subskrypcji platformy Azure.

Po wdrożeniu szablonu subskrypcja obejmuje następujące zasoby:

- Aplikacja funkcji z operacjami dotyczącymi zasobów i akcji.
- Konto magazynu do przechowywania użytkowników utworzonych za pomocą dostawcy niestandardowego.
- Niestandardowy dostawca definiujący niestandardowe typy zasobów i akcje. Używa punktu końcowego aplikacji funkcji do wysyłania żądań.
- Zasób niestandardowy od niestandardowego dostawcy.

Aby wdrożyć dostawcę niestandardowego, użyj interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W tym przykładzie zostanie wyświetlony komunikat z prośbą o wprowadzenie nazwy grupy zasobów, lokalizacji i aplikacji dostawcy. Nazwy są przechowywane w zmiennych, które są używane w innych poleceniach. Polecenia [AZ Group Create](/cli/azure/group#az-group-create) i [AZ Deployment Group](/cli/azure/deployment/group#az-deployment-group-create) Create wdrażają zasoby.

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W tym przykładzie zostanie wyświetlony komunikat z prośbą o wprowadzenie nazwy grupy zasobów, lokalizacji i aplikacji dostawcy. Nazwy są przechowywane w zmiennych, które są używane w innych poleceniach. Polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) i [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) wdrażają zasoby.

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Możesz również wdrożyć rozwiązanie z poziomu Azure Portal. Wybierz przycisk **Wdróż na platformie Azure** , aby otworzyć szablon w Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Wyświetlanie niestandardowego dostawcy i zasobu

W portalu Dostawca niestandardowy jest ukrytym typem zasobu. Aby upewnić się, że dostawca zasobów został wdrożony, przejdź do grupy zasobów. Wybierz opcję **wyświetlania typów ukrytych**.

![Pokaż ukryte typy zasobów](./media/create-custom-provider/show-hidden.png)

Aby wyświetlić niestandardowy typ zasobu, który został wdrożony, należy użyć `GET` operacji dla typu zasobu.

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

Otrzymasz odpowiedź:

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

---

## <a name="call-action"></a>Akcja wywołania

Dostawca niestandardowy ma również akcję o nazwie `ping` . Kod, który przetwarza żądanie, jest implementowany w aplikacji funkcji. `ping`Akcja odpowiada za pomocą powitania.

Aby wysłać `ping` żądanie, użyj `POST` operacji na swoim dostawcy niestandardowym.

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

Otrzymasz odpowiedź:

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

---

## <a name="create-a-resource-type"></a>Tworzenie typu zasobu

Aby utworzyć niestandardowy typ zasobu, można wdrożyć zasób w szablonie. To podejście jest wyświetlane w szablonie wdrożonym w tym przewodniku Szybki Start. Możesz również wysłać `PUT` żądanie dla typu zasobu.

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

Otrzymasz odpowiedź:

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

---

## <a name="custom-resource-provider-commands"></a>Niestandardowe polecenia dostawcy zasobów

Użyj poleceń [dostawców niestandardowych](/cli/azure/ext/custom-providers/custom-providers/resource-provider) do pracy z niestandardowym dostawcą zasobów.

### <a name="list-custom-resource-providers"></a>Utwórz listę niestandardowych dostawców zasobów

Użyj `list` polecenia, aby wyświetlić wszystkich niestandardowych dostawców zasobów w ramach subskrypcji. Domyślnie zawiera listę niestandardowych dostawców zasobów bieżącej subskrypcji lub można określić `--subscription` parametr. Aby wyświetlić listę dla grupy zasobów, użyj `--resource-group` parametru.

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>Pokaż właściwości

Użyj `show` polecenia, aby wyświetlić właściwości niestandardowego dostawcy zasobów. Format danych wyjściowych przypomina `list` dane wyjściowe.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Użyj `create` polecenia, aby utworzyć lub zaktualizować niestandardowego dostawcę zasobów. Ten przykład aktualizuje `actions` i `resourceTypes` .

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>Aktualizowanie tagów dostawcy

`update`Polecenie aktualizuje tylko Tagi dla niestandardowego dostawcy zasobów. W Azure Portal usługa App Provider niestandardowego dostawcy zasobów wyświetla tag.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Usuwanie niestandardowego dostawcy zasobów

`delete`Polecenie powoduje wybranie i usunięcie tylko niestandardowego dostawcy zasobów. Nie usunięto konta magazynu, usługi App Service i planu usługi App Service. Po usunięciu dostawcy zostanie wyświetlony wiersz polecenia.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Przegląd dla dostawców niestandardowych platformy Azure — omówienie](overview.md)
