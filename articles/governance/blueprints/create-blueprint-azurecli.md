---
title: 'Szybki Start: Tworzenie strategii przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start używasz planów platformy Azure do tworzenia, definiowania i wdrażania artefaktów przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 88ccd3fe6271a79d201007954e7997b68d2dc69b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920563"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Szybki Start: Definiowanie i przypisywanie Azure Blueprint przy użyciu interfejsu wiersza polecenia platformy Azure

Informacje na temat tworzenia i przypisywania planów umożliwiają definiowanie wspólnych wzorców w celu opracowania wielokrotnego i szybkiego wdrożenia konfiguracji na podstawie szablonów Azure Resource Manager (szablony ARM), zasad, zabezpieczeń i innych. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Blueprints wykonywać niektóre typowe zadania związane z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji, takie jak:

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Dodaj rozszerzenie planu

Aby włączyć interfejs wiersza polecenia platformy Azure do zarządzania definicjami i przypisaniami planu, należy dodać rozszerzenie.
To rozszerzenie działa wszędzie tam, gdzie interfejs wiersza polecenia platformy Azure może być używany, w tym w [funkcji bash w systemie Windows 10](/windows/wsl/install-win10), usłudze [Cloud Shell](https://shell.azure.com) (autonomicznej i wewnątrz portalu), [obrazie platformy Docker interfejsu wiersza polecenia platformy Azure](https://hub.docker.com/_/microsoft-azure-cli), lub zainstalowany lokalnie.

1. Sprawdź, czy jest zainstalowany najnowszy interfejs wiersza polecenia platformy Azure (co najmniej **2.0.76**). Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/cli/azure/install-azure-cli-windows).

1. W wybranym środowisku interfejsu wiersza polecenia platformy Azure zaimportuj rozszerzenie za pomocą następującego polecenia:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Sprawdź, czy rozszerzenie zostało zainstalowane i czy jest to oczekiwana wersja (co najmniej **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. Utworzymy strategię o nazwie „MyBlueprint” służącą do konfigurowania przypisań ról i zasad dla subskrypcji. Następnie dodamy grupę zasobów, szablon ARM i przypisanie roli do grupy zasobów.

> [!NOTE]
> W przypadku korzystania z interfejsu wiersza polecenia platformy Azure najpierw tworzony jest obiekt _Plan_ . Dla każdego _artefaktu_ zawierającego parametry, który ma zostać dodany, parametry _strategii_ początkowej muszą zostać zdefiniowane wcześniej.

1. Utwórz obiekt _strategii_ początkowej. Parametr **Parameters** przyjmuje plik JSON, który zawiera wszystkie parametry poziomu planu. Parametry są określane podczas przypisywania i używane przez artefakty dodane w kolejnych krokach.

   - Plik JSON — blueprintparms.json

     ```json
     {
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
     }
     ```

   - Polecenie interfejsu wiersza polecenia platformy Azure

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Użyj nazwy pliku _blueprint.js_ podczas importowania definicji planu.
     > Ta nazwa pliku jest używana podczas wywoływania [AZ planu import](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import).

     Obiekt planu jest domyślnie tworzony w domyślnej subskrypcji. Aby określić grupę zarządzania, należy użyć grupy **zarządzania** parametrami. Aby określić subskrypcję, użyj **subskrypcji** parametru.

1. Dodaj grupę zasobów dla artefaktów magazynu do definicji.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Dodaj przypisanie roli w subskrypcji. W poniższym przykładzie tożsamości podmiotu zabezpieczeń, którym przyznano określoną rolę, są konfigurowane za pomocą parametru określonego podczas przypisywania strategii. W tym przykładzie użyto wbudowanej roli _Współautor_ o identyfikatorze GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Dodaj przypisanie zasad w subskrypcji. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Plik JSON — artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Polecenie interfejsu wiersza polecenia platformy Azure

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. Dodaj kolejne przypisanie zasad dla tagu magazynu (używając ponownie parametru _storageAccountType_) w subskrypcji. Ten dodatkowy artefakt przypisania zasad pokazuje, że parametr zdefiniowany w strategii może być używany przez więcej niż jeden artefakt. W tym przykładzie parametr **storageAccountType** służy do określania tagu w grupie zasobów. Ta wartość zawiera informacje o koncie magazynu, które zostanie tworzone w następnym kroku. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Plik JSON — artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Polecenie interfejsu wiersza polecenia platformy Azure

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. Dodaj szablon w grupie zasobów. Parametr **szablonu** dla szablonu ARM zawiera normalne składniki JSON szablonu. Szablon używa również wielokrotnie parametrów strategii **storageAccountType**, **tagName** i **tagValue**, przekazując każdy z nich do szablonu. Parametry planu są dostępne dla szablonu za pomocą **parametrów** parametru i w formacie JSON szablonu, którego para klucz-wartość służy do iniekcji wartości. Nazwy planów i parametrów szablonu mogą być takie same.

   - Plik szablonu ARM JSON — artifacts\templateStorage.json

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

   - Plik parametru szablonu ARM w formacie JSON — artifacts\templateStorageParams.json

     ```json
     {
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
     ```

   - Polecenie interfejsu wiersza polecenia platformy Azure

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. Dodaj przypisanie roli w grupie zasobów. Podobnie jak w poprzednim wpisie przypisania roli, w poniższym przykładzie użyto identyfikatora definicji dla roli **Właściciel** i podano mu inny parametr ze strategii. W tym przykładzie użyto wbudowanej roli _Właściciel_ o identyfikatorze GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu artefaktów do strategii czas na jej opublikowanie. Opublikowanie strategii umożliwia przypisanie jej do subskrypcji.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

Wartość zmiennej `{BlueprintVersion}` jest ciągiem liter, cyfr i łączników (bez spacji czy innych znaków specjalnych) o maksymalnej długości 20 znaków. Korzystaj z unikatowych i informacyjnych, takich jak **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu planu przy użyciu interfejsu wiersza polecenia platformy Azure można go przypisać do subskrypcji. Przypisz utworzoną przez siebie strategię do jednej z subskrypcji w Twojej hierarchii grup zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji. Parametr strategii **-name** określa plan do przypisania. Aby podać nazwę, lokalizację, tożsamość, blokadę i parametry planu, użyj pasujących parametrów interfejsu wiersza polecenia platformy Azure w `az blueprint assignment create` poleceniu lub podaj je w pliku JSON **parametrów** .

1. Uruchom wdrażanie strategii, przypisując ją do subskrypcji. Ponieważ parametry **współautorów** i **właścicieli** wymagają tablicy obiektów objectid, aby otrzymać przypisanie roli, użyj [Azure Active Directory interfejs API programu Graph](../../active-directory/develop/active-directory-graph-api.md) do zbierania obiektów objectid do użycia w **parametrach** dla własnych użytkowników, grup lub jednostek usługi.

   - Plik JSON — blueprintAssignment.json

     ```json
     {
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
     ```

   - Polecenie interfejsu wiersza polecenia platformy Azure

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Tożsamość zarządzana przypisana przez użytkownika

     W przypisaniu strategii można również użyć [tożsamości zarządzanej przypisanej przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md).
     W takim przypadku parametr **Identity-Type** jest ustawiony na wartość _UserAssigned_ , a parametr tożsamości **przypisanych przez użytkownika** Określa tożsamość. Zamień `{userIdentity}` na nazwę tożsamości zarządzanej przypisanej przez użytkownika.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **Tożsamość zarządzana przypisana przez użytkownika** może należeć do dowolnej subskrypcji i grupy zasobów, do której użytkownik przypisujący strategię ma uprawnienia.

     > [!IMPORTANT]
     > Plany platformy Azure nie zarządzają tożsamości zarządzanej przypisanej przez użytkownika. Użytkownicy są odpowiedzialni za przypisywanie wystarczających ról i uprawnień — w przeciwnym razie przypisanie strategii kończy się niepowodzeniem.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="unassign-a-blueprint"></a>Cofanie przypisania strategii

Strategię można usunąć z subskrypcji. Usunięcie często przeprowadza się, gdy zasoby artefaktu przestają być potrzebne. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie planu, użyj `az blueprint assignment delete` polecenia:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono, przypisano i usunięto plan przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej na temat planów platformy Azure, przejdź do artykułu dotyczącego cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](./concepts/lifecycle.md)