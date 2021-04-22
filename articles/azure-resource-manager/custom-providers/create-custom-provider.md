---
title: Tworzenie dostawcy zasobów
description: Opisuje sposób tworzenia dostawcy zasobów i wdrażania jego niestandardowych typów zasobów.
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 915856e3f9875c0a3318bc19138eaae8742bf72f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871745"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>Szybki start: tworzenie niestandardowego dostawcy i wdrażanie zasobów niestandardowych

W tym przewodniku Szybki start utworzysz własnego dostawcę zasobów i wdrożysz niestandardowe typy zasobów dla tego dostawcy zasobów. Aby uzyskać więcej informacji na temat dostawców niestandardowych, zobacz Omówienie usługi [Azure Custom Providers (wersja zapoznawcza)](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aby wykonać kroki opisane w tym przewodniku Szybki start, należy wywołać `REST` operacje. Istnieją różne [sposoby wysyłania żądań REST.](/rest/api/azure/)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przygotowywanie środowiska dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Przykłady interfejsu wiersza polecenia platformy Azure `az rest` są przykładowe dla `REST` żądań. Aby uzyskać więcej informacji, zobacz [az rest](/cli/azure/reference-index#az_rest).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

- Polecenia programu PowerShell są uruchamiane lokalnie przy użyciu programu PowerShell 7 lub nowszego, a Azure PowerShell modułów. Aby uzyskać więcej informacji, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps).
- Jeśli nie masz jeszcze narzędzia do operacji, zainstaluj `REST` pakiet [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie wiersza polecenia typu open source, które upraszcza wywołania interfejsu API Azure Resource Manager API.
- Po **zainstalowaniu programu ARMClient** można wyświetlić informacje o użyciu z wiersza polecenia programu PowerShell, wpisując: `armclient.exe` . Możesz też przejść do strony [typu wiki ARMClient](https://github.com/projectkudu/ARMClient/wiki).

---

## <a name="deploy-custom-provider"></a>Wdrażanie dostawcy niestandardowego

Aby skonfigurować niestandardowego dostawcę, wd wdrażaj [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) w subskrypcji platformy Azure.

Po wdrożeniu szablonu subskrypcja ma następujące zasoby:

- Aplikacja funkcji z operacjami na zasobach i akcjach.
- Konto magazynu do przechowywania użytkowników utworzonych za pośrednictwem dostawcy niestandardowego.
- Dostawca niestandardowy, który definiuje niestandardowe typy zasobów i akcje. Używa ona punktu końcowego aplikacji funkcji do wysyłania żądań.
- Zasób niestandardowy od dostawcy niestandardowego.

Aby wdrożyć niestandardowego dostawcę, użyj interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W tym przykładzie zostanie wyświetlony monit o wprowadzenie grupy zasobów, lokalizacji i nazwy aplikacji funkcji dostawcy. Nazwy są przechowywane w zmiennych, które są używane w innych poleceniach. Polecenia [az group create](/cli/azure/group#az_group_create) i az deployment group [create](/cli/azure/deployment/group#az_deployment_group_create) wdrażają zasoby.

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

W tym przykładzie zostanie wyświetlony monit o wprowadzenie grupy zasobów, lokalizacji i nazwy aplikacji funkcji dostawcy. Nazwy są przechowywane w zmiennych, które są używane w innych poleceniach. Polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) i [New-AzResourceGroupDeployment wdrażają](/powershell/module/az.resources/new-azresourcegroupdeployment) zasoby.

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

Możesz również wdrożyć rozwiązanie z Azure Portal. Wybierz przycisk **Deploy to Azure (Wd** wdrażaj na platformie Azure), aby otworzyć szablon w Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>Wyświetlanie niestandardowego dostawcy i zasobu

W portalu dostawca niestandardowy jest ukrytym typem zasobu. Aby potwierdzić, że dostawca zasobów został wdrożony, przejdź do grupy zasobów. Wybierz opcję Pokaż **ukryte typy**.

![Wyświetlanie ukrytych typów zasobów](./media/create-custom-provider/show-hidden.png)

Aby wyświetlić wdrożony typ zasobu niestandardowego, użyj `GET` operacji na typie zasobu.

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

Twój dostawca niestandardowy ma również akcję o nazwie `ping` . Kod, który przetwarza żądanie, jest implementowane w aplikacji funkcji. Akcja `ping` odpowiada powitaniami.

Aby `ping` wysłać żądanie, użyj `POST` operacji na dostawcy niestandardowym.

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

Aby utworzyć niestandardowy typ zasobu, możesz wdrożyć zasób w szablonie. Takie podejście przedstawiono w szablonie wdrożonym w tym przewodniku Szybki start. Możesz również wysłać `PUT` żądanie dotyczące typu zasobu.

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

## <a name="custom-resource-provider-commands"></a>Polecenia niestandardowego dostawcy zasobów

Użyj poleceń [dostawców niestandardowych,](/cli/azure/custom-providers/resource-provider) aby pracować z niestandardowym dostawcą zasobów.

### <a name="list-custom-resource-providers"></a>Lista niestandardowych dostawców zasobów

Użyj polecenia `list` , aby wyświetlić wszystkich niestandardowych dostawców zasobów w subskrypcji. Wartość domyślna wyświetla listę dostawców zasobów niestandardowych bieżącej subskrypcji lub można określić `--subscription` parametr . Aby wyświetlić listę dla grupy zasobów, użyj `--resource-group` parametru .

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

### <a name="show-the-properties"></a>Wyświetlanie właściwości

Użyj polecenia `show` , aby wyświetlić właściwości niestandardowego dostawcy zasobów. Format danych wyjściowych przypomina dane `list` wyjściowe.

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>Tworzenie nowego zasobu

Użyj polecenia `create` , aby utworzyć lub zaktualizować niestandardowego dostawcę zasobów. Ten przykład aktualizuje i `actions` `resourceTypes` .

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

Polecenie `update` aktualizuje tylko tagi dla niestandardowego dostawcy zasobów. W Azure Portal aplikacja dostawcy zasobów niestandardowych wyświetla tag.

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>Usuwanie niestandardowego dostawcy zasobów

Polecenie `delete` wyświetli monit i usunie tylko niestandardowego dostawcę zasobów. Konto magazynu, usługa App Service i plan usługi App Service nie są usuwane. Po usunięciu dostawcy wrócisz do wiersza polecenia.

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wprowadzenie do dostawców niestandardowych, zobacz następujący artykuł:

> [!div class="nextstepaction"]
> [Omówienie usługi Azure Custom Providers (wersja zapoznawcza)](overview.md)
