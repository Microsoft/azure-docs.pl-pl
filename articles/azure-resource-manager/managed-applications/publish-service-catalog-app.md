---
title: Publikowanie aplikacji zarządzanej katalogu usług
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: b255cafb9040f87c902fe6c094c3e0db3d461e4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093644"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Szybki start: Tworzenie i publikowanie definicji aplikacji zarządzanej

Ten przewodnik Szybki Start zawiera wprowadzenie do pracy z [Azure Managed Applications](overview.md). Można utworzyć i opublikować zarządzaną aplikację, która jest przeznaczona dla członków organizacji.

Aby opublikować zarządzaną aplikację w katalogu usług, musisz:

* Utwórz szablon określający zasoby wdrażane wraz z aplikacją zarządzaną.
* Zdefiniuj elementy interfejsu użytkownika portalu, stosowane podczas wdrażania aplikacji zarządzanej.
* Utwórz pakiet _. zip_ zawierający wymagane pliki szablonów.
* Wybierz użytkowników, grupy lub aplikacje, dla których jest wymagany dostęp do grupy zasobów w ramach subskrypcji użytkownika.
* Utwórz definicję aplikacji zarządzanej, która wskazuje na pakiet _. zip_ i żąda dostępu do tożsamości.

## <a name="create-the-arm-template"></a>Tworzenie szablonu usługi ARM

Każda definicja aplikacji zarządzanej zawiera plik o nazwie _mainTemplate.json_. W tym pliku należy zdefiniować zasoby platformy Azure, które zostaną wdrożone. Szablon nie różni się od zwykłego szablonu ARM.

Utwórz plik o nazwie _mainTemplate.json_. W nazwie jest rozróżniana wielkość liter.

Dodaj do tego pliku następujący kod JSON. Definiuje on parametry wymagane podczas tworzenia konta magazynu oraz określa właściwości tego konta magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

Zapisz _mainTemplate.js_ pliku.

## <a name="define-your-create-experience"></a>Definiowanie środowiska tworzenia

Jako wydawca zdefiniujesz środowisko portalu do tworzenia aplikacji zarządzanej. _createUiDefinition.jsw_ pliku generuje interfejs portalu. Definiujesz sposób, w jaki użytkownicy dostarczają dane wejściowe dla każdego parametru za pomocą [elementów kontrolki](create-uidefinition-elements.md) , takich jak listy rozwijane, pola tekstowe i hasła.

Utwórz plik o nazwie _createUiDefinition.jsna_ (Ta nazwa jest uwzględniana wielkość liter)

Dodaj następujący początkowy kod JSON do pliku i Zapisz go.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

Aby dowiedzieć się więcej, zobacz Rozpoczynanie [pracy z usługą CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Pakowanie plików

Dodaj dwa pliki do pliku _zip_ o nazwie _app.zip_. Te dwa pliki muszą znajdować się na poziomie głównym pliku _zip_ . Jeśli zostaną umieszczone w folderze, podczas tworzenia definicji aplikacji zarządzanej otrzymasz komunikat o błędzie, informujący o braku wymaganych plików.

Przekaż pakiet do dostępnej lokalizacji, w której może być używany. Musisz podać unikatową nazwę konta magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Tworzenie grupy użytkowników aplikacji usługi Azure Active Directory

Następnym krokiem jest wybranie grupy użytkowników, użytkownika lub aplikacji w celu zarządzania zasobami dla klienta. Ta tożsamość ma uprawnienia w zarządzanej grupie zasobów zgodne z przypisaną rolą. Rolą może być dowolna wbudowana rola platformy Azure, taka jak właściciel lub współautor. Jeśli chcesz utworzyć nową grupę użytkowników usługi Active Directory, zobacz [Create a group and add members in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) (Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory).

Aby umożliwić zarządzanie zasobami, potrzebujesz identyfikatora obiektu grupy użytkowników.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Uzyskiwanie identyfikatora definicji roli

Następnie potrzebujesz identyfikatora definicji roli wbudowanej roli platformy Azure, do której chcesz udzielić dostępu użytkownikowi, grupie użytkowników lub aplikacji. Najczęściej używana jest rola Właściciel, Współautor lub Czytelnik. W następującym poleceniu przedstawiono sposób uzyskiwania identyfikatora definicji dla roli Właściciel:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

Jeśli nie istnieje jeszcze grupa zasobów do przechowywania definicji aplikacji zarządzanej, utwórz ją:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Teraz utwórz zasób definicji aplikacji zarządzanej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Gdy wykonywanie polecenia zakończy się, będziesz mieć w swojej grupie zasobów definicję aplikacji zarządzanej.

Niektóre z parametrów użytych w powyższym przykładzie to:

* **Grupa zasobów**: Nazwa grupy zasobów, w której utworzono definicję aplikacji zarządzanej.
* **poziom blokady**: typ blokady umieszczonej w zarządzanej grupie zasobów. Uniemożliwia klientowi wykonywanie niepożądanych operacji na tej grupie zasobów. Obecnie jedynym obsługiwanym poziomem blokady jest ReadOnly. Gdy zostanie określony poziom ReadOnly, klient może jedynie odczytywać zasoby obecne w zarządzanej grupie zasobów. Tożsamości wydawcy, które mają dostęp do zarządzanej grupy zasobów, są wykluczone z blokady.
* **authorizations**: opisuje identyfikator podmiotu zabezpieczeń i identyfikator definicji roli, przy użyciu których udzielany jest dostęp do zarządzanej grupy zasobów. Określa się go w formacie `<principalId>:<roleDefinitionId>`. Jeśli wymagana jest więcej niż jedna wartość, należy podać wartości w formacie `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. Wartości są oddzielone przecinkami.
* **Identyfikator URI pliku pakietu**: lokalizacja pakietu _. zip_ , który zawiera wymagane pliki.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Przenoszenie własnego magazynu dla definicji aplikacji zarządzanej

Możesz wybrać przechowywanie definicji aplikacji zarządzanej w ramach konta magazynu dostarczonego przez użytkownika podczas tworzenia, tak aby jego lokalizacja i dostęp mogły być w pełni zarządzane przez użytkownika zgodnie z wymaganiami prawnymi.

> [!NOTE]
> Korzystanie z własnego magazynu jest obsługiwane tylko przy użyciu szablonu ARM lub wdrożenia interfejsu API REST dla definicji aplikacji zarządzanej.

### <a name="select-your-storage-account"></a>Wybierz swoje konto magazynu

Należy [utworzyć konto magazynu](../../storage/common/storage-account-create.md) , aby zawierało definicję aplikacji zarządzanej do użycia z katalogiem usług.

Skopiuj identyfikator zasobu konta magazynu. Będzie on używany później podczas wdrażania definicji.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Ustaw przypisanie roli dla "dostawca zasobów urządzenia" na koncie magazynu

Aby można było wdrożyć definicję aplikacji zarządzanej na koncie magazynu, należy nadać uprawnienia współautora roli **dostawcy zasobów urządzenia** , aby można było napisać pliki definicji do kontenera konta magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego konta magazynu.
1. Wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **współautor** .
1. W polu **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. W obszarze **Wybierz** Wyszukaj rolę **dostawca zasobów urządzenia** i wybierz ją.
1. Zapisz przypisanie roli.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Wdróż definicję aplikacji zarządzanej przy użyciu szablonu ARM

Użyj następującego szablonu usługi ARM, aby wdrożyć spakowaną aplikację zarządzaną jako nową definicję aplikacji zarządzanej w katalogu usług, której pliki definicji są przechowywane i utrzymywane na Twoim koncie magazynu:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

Dodaliśmy nową właściwość o nazwie `storageAccountId` do `applicationDefinitions` właściwości i podaj identyfikator konta magazynu, dla którego chcesz przechowywać swoją definicję jako wartość:

Można sprawdzić, czy pliki definicji aplikacji są zapisywane na podanym koncie magazynu w kontenerze zatytułowanym `applicationDefinitions` .

> [!NOTE]
> W celu zwiększenia bezpieczeństwa można utworzyć magazyn definicji zarządzanych aplikacji w [obiekcie blob konta usługi Azure Storage, w którym jest włączone szyfrowanie](../../storage/common/storage-service-encryption.md). Zawartość definicji jest zaszyfrowana przy użyciu opcji szyfrowania konta magazynu. Tylko użytkownicy z uprawnieniami do pliku mogą zobaczyć definicję w katalogu usług.

## <a name="make-sure-users-can-see-your-definition"></a>Upewnij się, że użytkownicy będą mogli zobaczyć definicję

Masz dostęp do definicji aplikacji zarządzanej, ale chcesz mieć pewność, że inni użytkownicy w Twojej organizacji również mają do niej dostęp. Z definicji przyznaj im co najmniej rolę czytelnika. Użytkownicy mogą odziedziczyć ten poziom dostępu z subskrypcji lub grupy zasobów. Aby sprawdzić, kto ma dostęp do definicji i dodać użytkowników lub grupy, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

Opublikowano definicję aplikacji zarządzanej. Teraz możesz dowiedzieć się, jak wdrożyć wystąpienie tej definicji.

> [!div class="nextstepaction"]
> [Quickstart: Deploy service catalog app (Szybki start: wdrażanie aplikacji katalogu usług)](deploy-service-catalog-quickstart.md)
