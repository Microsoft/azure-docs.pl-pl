---
title: Media Services szablon ARM konta: Azure Media Services opis: w tym artykule przedstawiono sposób użycia szablonu usługi ARM do utworzenia konta usług Media Services.
usługi: Media-Services documentationcenter: "" Author: IngridAtMicrosoft Manager: femila Editor: ""

MS. Service: Media-Services MS. obciążenie: MS. Subject: Szybki Start MS. Date: 03/23/2021 MS. Author: inhenkel MS. Custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Szybki Start: szablon ARM konta Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule pokazano, jak utworzyć konto usługi Media Services przy użyciu szablonu Azure Resource Manager (szablon ARM).

## <a name="introduction"></a>Wprowadzenie

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Czytelnicy znający szablony ARM mogą przejść do [sekcji Wdrażanie](#deploy-the-template).

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli szablon ARM nie został wcześniej wdrożony, warto zapoznać się z [szablonami usługi Azure ARM](../../azure-resource-manager/templates/index.yml) i przejść przez [samouczek](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-media-services-create/).

Składnia ogranicznika kodu JSON jest następująca:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Trzy typy zasobów platformy Azure są zdefiniowane w szablonie:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Tworzenie konta magazynu
- [Microsoft. Media/MediaServices](/azure/templates/microsoft.media/mediaservices): tworzenie konta Media Services

## <a name="set-the-account"></a>Ustawianie konta

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Przypisywanie zmiennej do pliku wdrożenia

Dla wygody utwórz zmienną, w której będzie przechowywana ścieżka do pliku szablonu. Ta zmienna ułatwia uruchamianie poleceń wdrażania, ponieważ nie trzeba ponownie wpisywać ścieżki przy każdym wdrożeniu.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zostanie wyświetlony monit o wprowadzenie nazwy konta usługi Media Services.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Powinna zostać wyświetlona odpowiedź JSON podobna do poniższego:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

W Azure Portal upewnij się, że zasoby zostały utworzone.

![utworzono zasoby szybkiego startu](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz używania właśnie utworzonych zasobów, możesz usunąć grupę zasobów.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o korzystaniu z szablonu ARM przez następujący proces tworzenia szablonu z parametrami, zmiennymi i innymi, spróbuj

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)