---
title: 'Szybki Start: Tworzenie strategii przy użyciu programu PowerShell'
description: W tym przewodniku szybki start używasz planów platformy Azure do tworzenia, definiowania i wdrażania artefaktów przy użyciu programu PowerShell.
ms.date: 08/27/2020
ms.topic: quickstart
ms.openlocfilehash: 339cd4628cda5f469a783db02c10f86259c93941
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051512"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Szybki Start: Definiowanie i przypisywanie Azure Blueprint przy użyciu programu PowerShell

Informacje na temat tworzenia i przypisywania planów umożliwiają definiowanie wspólnych wzorców w celu opracowania wielokrotnego i szybkiego wdrożenia konfiguracji na podstawie szablonów Azure Resource Manager (szablony ARM), zasad, zabezpieczeń i innych. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Blueprints wykonywać niektóre typowe zadania związane z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji, takie jak:

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

Jeśli nie jest jeszcze zainstalowana, postępuj zgodnie z instrukcjami w temacie [Dodawanie modułu AZ. plan](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) w celu zainstalowania i sprawdzenia poprawności modułu **AZ. plan** z poziomu Galeria programu PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. Utworzymy strategię o nazwie „MyBlueprint” służącą do konfigurowania przypisań ról i zasad dla subskrypcji. Następnie dodamy grupę zasobów, szablon ARM i przypisanie roli do grupy zasobów.

> [!NOTE]
> W przypadku _korzystania z programu PowerShell, obiekt planu_ jest tworzony jako pierwszy. Dla każdego _artefaktu_ zawierającego parametry, który ma zostać dodany, parametry _strategii_ początkowej muszą zostać zdefiniowane wcześniej.

1. Utwórz obiekt _strategii_ początkowej. Parametr **BlueprintFile** pobiera plik JSON, który zawiera właściwości strategii, wszystkie grupy zasobów do utworzenia i wszystkie parametry poziomu planu. Parametry są określane podczas przypisywania i używane przez artefakty dodane w kolejnych krokach.

   - Plik JSON — blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
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
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Użyj _blueprint.js_ nazwy pliku podczas programistycznego tworzenia definicji planu.
     > Ta nazwa pliku jest używana podczas wywoływania metody [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Obiekt planu jest domyślnie tworzony w domyślnej subskrypcji. Aby określić grupę zarządzania, należy użyć parametru **ManagementGroupId**. Aby określić subskrypcję, użyj parametru identyfikator **subskrypcji**.

1. Dodaj przypisanie roli w subskrypcji. **ArtifactFile** definiuje _rodzaj_ artefaktu, właściwości są wyrównane do identyfikatora definicji roli, a tożsamości podmiotu zabezpieczeń są przenoszone jako tablica wartości. W poniższym przykładzie tożsamości podmiotu zabezpieczeń, którym przyznano określoną rolę, są konfigurowane za pomocą parametru określonego podczas przypisywania strategii. W tym przykładzie użyto wbudowanej roli _Współautor_ o identyfikatorze GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - Plik JSON — \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Dodaj przypisanie zasad w subskrypcji. **ArtifactFile** definiuje _rodzaj_ artefaktu, właściwości, które są wyrównane do definicji zasad lub inicjatywy, i konfiguruje przypisanie zasad tak, aby korzystało ze zdefiniowanych parametrów strategii do konfigurowania podczas przypisywania planu. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Plik JSON — \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Dodaj kolejne przypisanie zasad dla tagu magazynu (używając ponownie parametru _storageAccountType_) w subskrypcji. Ten dodatkowy artefakt przypisania zasad pokazuje, że parametr zdefiniowany w strategii może być używany przez więcej niż jeden artefakt. W tym przykładzie parametr **storageAccountType** służy do określania tagu w grupie zasobów. Ta wartość zawiera informacje o koncie magazynu, które zostanie tworzone w następnym kroku. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Plik JSON — \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Dodaj szablon w grupie zasobów. **TemplateFile** dla szablonu ARM zawiera normalny składnik JSON szablonu. Szablon używa również wielokrotnie parametrów strategii **storageAccountType**, **tagName** i **tagValue**, przekazując każdy z nich do szablonu. Parametry planu są dostępne dla szablonu za pomocą parametru **TemplateParameterFile** i wewnątrz kodu JSON szablonu, którego para klucz-wartość służy do iniekcji wartości. Nazwy planów i parametrów szablonu mogą być takie same.

   - Plik szablonu ARM JSON — \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Plik parametru szablonu ARM w formacie JSON — \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Dodaj przypisanie roli w grupie zasobów. Podobnie jak w poprzednim wpisie przypisania roli, w poniższym przykładzie użyto identyfikatora definicji dla roli **Właściciel** i podano mu inny parametr ze strategii. W tym przykładzie użyto wbudowanej roli _Właściciel_ o identyfikatorze GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - Plik JSON — \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu artefaktów do strategii czas na jej opublikowanie. Opublikowanie strategii umożliwia przypisanie jej do subskrypcji.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Wartość zmiennej `{BlueprintVersion}` jest ciągiem liter, cyfr i łączników (bez spacji czy innych znaków specjalnych) o maksymalnej długości 20 znaków. Używaj wartości, które są unikatowe i informacyjne, na przykład **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu planu przy użyciu programu PowerShell można go przypisać do subskrypcji. Przypisz utworzoną przez siebie strategię do jednej z subskrypcji w Twojej hierarchii grup zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji. Parametr **strategii określa plan do** przypisania. Aby podać nazwę, lokalizację, tożsamość, blokadę i parametry planu, użyj pasujących parametrów programu PowerShell w `New-AzBlueprintAssignment` poleceniu cmdlet lub podaj je w pliku JSON parametrów **AssignmentFile** .

1. Uruchom wdrażanie strategii, przypisując ją do subskrypcji. Ponieważ parametry **współautorów** i **właścicieli** wymagają tablicy obiektów objectid, aby otrzymać przypisanie roli, użyj [Azure Active Directory interfejs API programu Graph](../../active-directory/develop/active-directory-graph-api.md) do zbierania obiektów objectid do użycia w **AssignmentFile** dla własnych użytkowników, grup lub jednostek usługi.

   - Plik JSON — blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Polecenie programu PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Tożsamość zarządzana przypisana przez użytkownika

     W przypisaniu strategii można również użyć [tożsamości zarządzanej przypisanej przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md).
     W takim przypadku części **Identity** pliku przypisania JSON zmienia się w następujący sposób. Zastąp wartości `{tenantId}` ,, `{subscriptionId}` `{yourRG}` i `{userIdentity}` tenantId, Identyfikator subskrypcji, nazwę grupy zasobów i nazwę tożsamości zarządzanej przypisanej przez użytkownika odpowiednio.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **Tożsamość zarządzana przypisana przez użytkownika** może należeć do dowolnej subskrypcji i grupy zasobów, do której użytkownik przypisujący strategię ma uprawnienia.

     > [!IMPORTANT]
     > Plany platformy Azure nie zarządzają tożsamości zarządzanej przypisanej przez użytkownika. Użytkownicy są odpowiedzialni za przypisywanie wystarczających ról i uprawnień — w przeciwnym razie przypisanie strategii kończy się niepowodzeniem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Strategię można usunąć z subskrypcji. Usunięcie często przeprowadza się, gdy zasoby artefaktu przestają być potrzebne. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie planu, należy użyć `Remove-AzBlueprintAssignment` polecenia cmdlet:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono, przypisano i usunięto plan przy użyciu programu PowerShell. Aby dowiedzieć się więcej na temat planów platformy Azure, przejdź do artykułu dotyczącego cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](./concepts/lifecycle.md)