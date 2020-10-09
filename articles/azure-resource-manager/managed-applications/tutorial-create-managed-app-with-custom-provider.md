---
title: Samouczek — akcje niestandardowe & zasoby
description: W tym samouczku opisano sposób tworzenia aplikacji zarządzanej przez platformę Azure za pomocą niestandardowego dostawcy platformy Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e8824f534f573d97353cc86d2a1b112b1acdb211
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494506"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Samouczek: Tworzenie aplikacji zarządzanej z niestandardowymi akcjami i zasobami

W tym samouczku utworzysz własną zarządzaną aplikację z niestandardowymi akcjami i zasobami. Aplikacja zarządzana będzie zawierać akcję niestandardową na `Overview` stronie, niestandardowy typ zasobu wyświetlany jako oddzielny element menu w `Table of Content` i niestandardowa Akcja kontekstowa na stronie zasobów niestandardowych.

Ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Utwórz plik definicji interfejsu użytkownika na potrzeby tworzenia wystąpienia aplikacji zarządzanej
> * Tworzenie szablonu wdrożenia przy użyciu [dostawcy niestandardowego platformy Azure](../custom-providers/overview.md), konta usługi Azure Storage i funkcji platformy Azure
> * Tworzenie artefaktu definicji widoku z niestandardowymi akcjami i zasobami
> * Wdróż definicję aplikacji zarządzanej
> * Wdrażanie wystąpienia aplikacji zarządzanej
> * Wykonywanie akcji niestandardowych i tworzenie zasobów niestandardowych

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz znać:

* Jak [utworzyć i opublikować definicję aplikacji zarządzanej](publish-service-catalog-app.md).
* Jak [wdrożyć aplikację katalogu usług za Azure Portal](deploy-service-catalog-quickstart.md).
* Jak [utworzyć Azure Portal interfejs użytkownika dla aplikacji zarządzanej](create-uidefinition-overview.md).
* [Wyświetl możliwości artefaktów definicji](concepts-view-definition.md) .
* Możliwości [niestandardowe dostawcy platformy Azure](../custom-providers/overview.md) .

## <a name="user-interface-definition"></a>Definicja interfejsu użytkownika

W tym samouczku utworzysz zarządzaną aplikację, a jej zarządzana Grupa zasobów będzie zawierać niestandardowe wystąpienie dostawcy, konto magazynu i funkcję. Funkcja platformy Azure używana w tym przykładzie implementuje interfejs API obsługujący niestandardowe operacje dostawcy dla akcji i zasobów. Konto usługi Azure Storage jest używane jako magazyn podstawowy dla niestandardowych zasobów dostawcy.

Definicja interfejsu użytkownika służąca do tworzenia wystąpienia aplikacji zarządzanej `funcname` zawiera `storagename` elementy i. Nazwa konta magazynu i nazwa funkcji musi być globalnie unikatowa. Domyślnie pliki funkcji zostaną wdrożone z [przykładowego pakietu funkcji](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), ale można je zmienić przez dodanie elementu wejściowego dla linku do pakietu w *createUIDefinition.jsna*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

i dane wyjściowe w *createUIDefinition.jsna*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Pełną *createUIDefinition.jsna* przykład można znaleźć w temacie [Reference: elementy interfejsu użytkownika artefakty](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Szablon z niestandardowym dostawcą

Aby utworzyć wystąpienie aplikacji zarządzanej z dostawcą niestandardowym, należy zdefiniować zasób niestandardowego dostawcy o nazwie **Public** i wpisać w **mainTemplate.js**wartość **Microsoft. CustomProviders/resourceProviders** . W tym zasobie należy zdefiniować typy zasobów i akcje dla usługi. Aby wdrożyć funkcje platformy Azure i wystąpienia konta usługi Azure Storage, zdefiniuj zasoby typu `Microsoft.Web/sites` i `Microsoft.Storage/storageAccounts` odpowiednio.

W tym samouczku utworzysz jeden `users` Typ zasobu, `ping` akcję niestandardową i `users/contextAction` akcję niestandardową, która zostanie wykonana w kontekście `users` niestandardowego zasobu. Dla każdego typu zasobu i akcji Podaj punkt końcowy wskazujący funkcję o nazwie podanej w [createUIDefinition.json](#user-interface-definition). Określ typ **routingtype** jako `Proxy,Cache` dla typów zasobów i `Proxy` akcji:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Pełną *mainTemplate.jsna* przykład można znaleźć w temacie [Reference: artefakt szablonu wdrożenia](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Wyświetlanie artefaktu definicji

Aby zdefiniować interfejs użytkownika, który zawiera niestandardowe akcje i zasoby niestandardowe w aplikacji zarządzanej, należy utworzyć **viewDefinition.jsna** artefaktie. Aby uzyskać więcej informacji na temat artefaktu definicji widoku, zobacz temat [artefakt definicji widoku w Azure Managed Applications](concepts-view-definition.md).

W tym samouczku zdefiniujesz:
* Strona *przeglądu* z przyciskiem paska narzędzi, który reprezentuje akcję niestandardową `TestAction` z podstawowymi danymi wejściowymi.
* Strona *użytkowników* reprezentująca niestandardowy typ zasobu `users` .
* Niestandardowa Akcja zasobu `users/contextAction` na stronie *Użytkownicy* , która zostanie wykonana w kontekście niestandardowego zasobu typu `users` .

W poniższym przykładzie przedstawiono konfigurację widoku na stronie "przegląd":

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Poniższy przykład obejmuje konfigurację strony zasobów "Użytkownicy" z akcją zasobów niestandardowych:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Pełny *viewDefinition.jsna* przykład można znaleźć w temacie [Reference: artefakt definicji widoku](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definicja aplikacji zarządzanej

Spakuj następujące artefakty aplikacji zarządzanych do archiwum zip i przekaż je do magazynu:

* createUiDefinition.jsna
* mainTemplate.jsna
* viewDefinition.jsna

Wszystkie pliki muszą znajdować się na poziomie głównym. Pakiet z artefaktami może być przechowywany w dowolnym magazynie, na przykład w usłudze GitHub BLOB lub na koncie BLOB usługi Azure Storage. Oto skrypt przekazywania pakietu aplikacji do konta magazynu: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Uruchom Poniższy skrypt interfejsu wiersza polecenia platformy Azure lub postępuj zgodnie z instrukcjami w Azure Portal, aby wdrożyć definicję aplikacji zarządzanej katalogu usług:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz i wybierz pozycję **zarządzane aplikacje centrum**.
2. W **centrum zarządzane aplikacje**wybierz pozycję **Definicja aplikacji katalogu usług** i kliknij przycisk **Dodaj**. 
    
    ![Dodaj katalog usług](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Podaj wartości do utworzenia definicji katalogu usług:

    * Podaj unikatową **nazwę** definicji katalogu usług, **nazwę wyświetlaną** i *Opis*(opcjonalnie).
    * Wybierz **subskrypcję**, **grupę zasobów**i **lokalizację** , w której zostanie utworzona definicja aplikacji. Możesz użyć tej samej grupy zasobów, która jest używana przez pakiet ZIP lub utworzyć nową grupę zasobów.
    * W polu **Identyfikator URI pliku pakietu**podaj ścieżkę do pliku zip utworzonego w poprzednim kroku.

    ![Podaj wartości](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Po wyświetleniu sekcji uwierzytelnianie i blokowanie poziomu wybierz pozycję **Dodaj autoryzację**.

    ![Dodaj autoryzację](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Wybierz grupę Azure Active Directory, aby zarządzać zasobami, a następnie wybierz **przycisk OK**.

   ![Dodaj grupę autoryzacji](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Po podaniu wszystkich wartości wybierz pozycję **Utwórz**.

   ![Utwórz definicję aplikacji zarządzanej](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Zarządzane wystąpienie aplikacji

Po wdrożeniu definicji aplikacji zarządzanej uruchom poniższy skrypt lub postępuj zgodnie z instrukcjami w Azure Portal, aby wdrożyć wystąpienie aplikacji zarządzanej przy użyciu dostawcy niestandardowego:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz i wybierz pozycję **zarządzane aplikacje centrum**.
2. W **centrum zarządzane aplikacje**wybierz pozycję **aplikacje katalogu usług** i kliknij przycisk **Dodaj**. 

    ![Dodaj zarządzaną aplikację](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Na stronie **aplikacje katalogu usług** wpisz nazwę wyświetlaną definicji katalogu usług w polu wyszukiwania. Wybierz definicję utworzoną w poprzednim kroku, a następnie kliknij pozycję **Utwórz**.

    ![Wybieranie katalogu usług](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Podaj wartości do utworzenia wystąpienia aplikacji zarządzanej z definicji katalogu usług:

    * Wybierz **subskrypcję**, **grupę zasobów**i **lokalizację** , w której zostanie utworzone wystąpienie aplikacji.
    * Podaj unikatową nazwę funkcji platformy Azure i nazwę konta usługi Azure Storage.

    ![Ustawienia aplikacji](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Po przekazaniu walidacji kliknij przycisk **OK** , aby wdrożyć wystąpienie aplikacji zarządzanej. 
    
    ![Wdróż aplikację zarządzaną](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Niestandardowe akcje i zasoby

Po wdrożeniu wystąpienia aplikacji wykazu usług masz dwie nowe grupy zasobów. Pierwsza grupa zasobów `applicationGroup` zawiera wystąpienie aplikacji zarządzanej. Druga grupa zasobów `managedResourceGroup` przechowuje zasoby dla aplikacji zarządzanej, w tym **dostawcę niestandardowego**.

![Grupy zasobów aplikacji](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Możesz przejść do wystąpienia aplikacji zarządzanej i wykonać **akcję niestandardową** na stronie "przegląd", utworzyć zasób niestandardowy **Użytkownicy** na stronie "Użytkownicy" i uruchomić **akcję niestandardowego kontekstu** w zasobie niestandardowym.

* Przejdź do strony "przegląd" i kliknij przycisk "Akcja ping":

![Wykonaj akcję niestandardową](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Przejdź do strony "Użytkownicy" i kliknij przycisk "Dodaj". Podaj dane wejściowe do utworzenia zasobu i Prześlij formularz:

![Utwórz zasób niestandardowy](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Przejdź do strony "Użytkownicy", wybierz zasób "Użytkownicy" i kliknij pozycję "Akcja kontekstu niestandardowego":

![Utwórz zasób niestandardowy](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące Azure Managed Applications, spróbuj zadać pytanie na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobne pytanie mogło zostać już zgłoszone i nie udzielono odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag, `azure-managedapps` Aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

Aby opublikować aplikację zarządzaną w witrynie Azure Marketplace, zobacz [Aplikacje zarządzane platformy Azure w witrynie Marketplace](publish-marketplace-app.md).

Dowiedz się więcej o [dostawcach niestandardowych platformy Azure](../custom-providers/overview.md).
