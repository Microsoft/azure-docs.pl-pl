---
title: 'Szybki Start: Tworzenie strategii przy użyciu interfejsu API REST'
description: W tym przewodniku szybki start używasz planów platformy Azure do tworzenia, definiowania i wdrażania artefaktów przy użyciu interfejsu API REST.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: eaf6dbb2ff14106ba8d2798d86a8f093855de85e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915645"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Szybki Start: Definiowanie i przypisywanie Azure Blueprint przy użyciu interfejsu API REST

Informacje na temat tworzenia i przypisywania planów umożliwiają definiowanie wspólnych wzorców w celu opracowania wielokrotnego i szybkiego wdrożenia konfiguracji na podstawie szablonów Azure Resource Manager (szablony ARM), zasad, zabezpieczeń i innych. Z tego samouczka dowiesz się, jak za pomocą usługi Azure Blueprints wykonywać niektóre typowe zadania związane z tworzeniem, publikowaniem i przypisywaniem strategii w organizacji, takie jak:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).
- Zarejestruj `Microsoft.Blueprint` dostawcę zasobów. Aby uzyskać instrukcje, zobacz [dostawcy zasobów i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Wprowadzenie do interfejsu API REST

Jeśli jeszcze nie znasz interfejsu API REST, zacznij od przejrzenia [dokumentacji interfejsu API REST platformy Azure](/rest/api/azure/), aby uzyskać ogólny opis interfejsu API REST, a w szczególności identyfikatora URI żądania i treści żądania. W tym artykule te pojęcia służą do podawania wskazówek dotyczących pracy z usługą Azure Blueprints, dlatego praktyczna wiedza na ich temat jest niezbędna. Narzędzia, między innymi takie jak [ARMClient](https://github.com/projectkudu/ARMClient), mogą automatycznie obsługiwać autoryzację i są zalecane dla początkujących.

Aby poznać specyfikacje planów platformy Azure, zobacz [interfejs API REST usługi Azure Plans](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>Interfejs API REST i program PowerShell

Jeśli jeszcze nie masz narzędzia do wykonywania wywołań interfejsu API REST, rozważ wykonywanie tych instrukcji w programie PowerShell. Poniżej przedstawiono przykładowy nagłówek umożliwiający uwierzytelnianie za pomocą platformy Azure. Wygeneruj nagłówek uwierzytelniania, czasami nazywany **tokenem elementu nośnego**, i podaj identyfikator URI interfejsu API REST, z którym chcesz nawiązać połączenie, za pomocą dowolnych parametrów lub **treści żądania**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Zastąp parametr `{subscriptionId}` w zmiennej **$restUri** powyżej, aby uzyskać informacje na temat Twojej subskrypcji. Zmienna $response zawiera wynik działania polecenia cmdlet `Invoke-RestMethod`, który można przeanalizować za pomocą poleceń cmdlet, takich jak [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Jeśli punkt końcowy usługi interfejsu API REST oczekuje **treści żądania**, podaj zmienną w formacie JSON dla parametru `-Body` polecenia `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Tworzenie strategii

Pierwszym krokiem podczas definiowania standardowego wzorca zgodności jest utworzenie strategii z dostępnych zasobów. Utworzymy strategię o nazwie „MyBlueprint” służącą do konfigurowania przypisań ról i zasad dla subskrypcji. Następnie dodamy grupę zasobów, szablon ARM i przypisanie roli do grupy zasobów.

> [!NOTE]
> Gdy używasz interfejsu API REST, w pierwszej kolejności jest tworzony obiekt _strategii_. Dla każdego _artefaktu_ zawierającego parametry, który ma zostać dodany, parametry _strategii_ początkowej muszą zostać zdefiniowane wcześniej.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{YourMG}` — zastąp identyfikatorem swojej grupy zarządzania
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

> [!NOTE]
> Plany mogą być również tworzone na poziomie subskrypcji. Aby zapoznać się z przykładem, zobacz [Tworzenie strategii na przykład subskrypcji](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint).

1. Utwórz obiekt _strategii_ początkowej. **Treść żądania** zawiera właściwości strategii, wszystkie grupy zasobów, które mają zostać utworzone, oraz wszystkie parametry poziomu strategii. Parametry są określane podczas przypisywania i używane przez artefakty dodane w kolejnych krokach.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Treść żądania

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. Dodaj przypisanie roli w subskrypcji. **Treść żądania** definiuje _rodzaj_ artefaktu, właściwości dostosowują się do identyfikatora definicji roli, a tożsamości podmiotu zabezpieczeń są przekazywane w postaci tablicy wartości. W poniższym przykładzie tożsamości podmiotu zabezpieczeń, którym przyznano określoną rolę, są konfigurowane za pomocą parametru określonego podczas przypisywania strategii. W tym przykładzie użyto wbudowanej roli _Współautor_ o identyfikatorze GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Dodaj przypisanie zasad w subskrypcji. **Treść żądania** definiuje _rodzaj_ artefaktu, właściwości, które dostosowują się do definicji inicjatywy lub zasad, oraz konfiguruje przypisanie zasad tak, aby używało zdefiniowanych parametrów strategii, które zostaną skonfigurowane podczas przypisywania strategii. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Dodaj kolejne przypisanie zasad dla tagu magazynu (używając ponownie parametru _storageAccountType_) w subskrypcji. Ten dodatkowy artefakt przypisania zasad pokazuje, że parametr zdefiniowany w strategii może być używany przez więcej niż jeden artefakt. W tym przykładzie parametr **storageAccountType** służy do określania tagu w grupie zasobów. Ta wartość zawiera informacje o koncie magazynu, które zostanie tworzone w następnym kroku. W tym przykładzie użyto wbudowanych zasad _Zastosuj tag i jego wartość domyślną do grup zasobów_ o identyfikatorze GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Dodaj szablon w grupie zasobów. **Treść żądania** dla szablonu ARM zawiera normalny składnik JSON szablonu i definiuje docelową grupę zasobów z **właściwościami.** Szablon używa również wielokrotnie parametrów strategii **storageAccountType**, **tagName** i **tagValue**, przekazując każdy z nich do szablonu. Parametry strategii są udostępniane w szablonie dzięki zdefiniowaniu wartości **properties.parameters**, a w pliku JSON szablonu ta para klucz-wartość służy do iniekcji wartości. Nazwy parametrów strategii i szablonu mogą być takie same, ale wprowadziliśmy inne, aby lepiej zilustrować sposób ich przekazywania ze strategii do artefaktu szablonu.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Treść żądania

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. Dodaj przypisanie roli w grupie zasobów. Podobnie jak w poprzednim wpisie przypisania roli, w poniższym przykładzie użyto identyfikatora definicji dla roli **Właściciel** i podano mu inny parametr ze strategii. W tym przykładzie użyto wbudowanej roli _Właściciel_ o identyfikatorze GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Treść żądania

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

## <a name="publish-a-blueprint"></a>Publikowanie strategii

Po dodaniu artefaktów do strategii czas na jej opublikowanie. Opublikowanie strategii umożliwia przypisanie jej do subskrypcji.

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

Wartość zmiennej `{BlueprintVersion}` jest ciągiem liter, cyfr i łączników (bez spacji czy innych znaków specjalnych) o maksymalnej długości 20 znaków. Używaj wartości, które są unikatowe i informacyjne, na przykład **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Przypisywanie strategii

Po opublikowaniu strategii przy użyciu interfejsu API REST można przypisać ją do subskrypcji. Przypisz utworzoną przez siebie strategię do jednej z subskrypcji w Twojej hierarchii grup zarządzania. Jeśli strategia została zapisana w subskrypcji, można ją przypisać tylko do tej subskrypcji. **Treść żądania** określa strategię, która ma zostać przypisana, dostarcza nazwę i lokalizację do wszystkich grup zasobów w definicji strategii oraz podaje wszystkie parametry zdefiniowane w strategii i używane przez co najmniej jeden dołączony artefakt.

Każdy identyfikator URI interfejsu API REST zawiera używane zmienne, które musisz zastąpić własnymi wartościami:

- `{tenantId}` — zastąp identyfikatorem swojej dzierżawy
- `{YourMG}` — zastąp identyfikatorem swojej grupy zarządzania
- `{subscriptionId}` — zastąp swoim identyfikatorem subskrypcji

1. Podaj jednostce usługi Azure Blueprint rolę **Właściciel** w subskrypcji docelowej. Identyfikator AppId jest statyczny ( `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ), ale nazwa główna usługi różni się w zależności od dzierżawy. Szczegółowych informacji na temat dzierżawy można żądać, używając poniższego interfejsu API REST. Używa [interfejs API programu Graph Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md), które ma inną autoryzację.

   - Identyfikator URI interfejsu API REST

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Uruchom wdrażanie strategii, przypisując ją do subskrypcji. Ponieważ parametry **contributors** i **owners** wymagają, aby tablica identyfikatorów obiektów podmiotów zabezpieczeń miała przypisaną rolę, skorzystaj z [interfejsu API programu Grpah usługi Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) do zbierania identyfikatorów obiektów, które będą używane w **treści żądania** na potrzeby Twoich własnych użytkowników, grup lub jednostek usługi.

   - Identyfikator URI interfejsu API REST

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Treść żądania

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

   - Tożsamość zarządzana przypisana przez użytkownika

     W przypisaniu strategii można również użyć [tożsamości zarządzanej przypisanej przez użytkownika](../../active-directory/managed-identities-azure-resources/overview.md).
     W tym przypadku część dotycząca **tożsamości** w treści żądania zmienia się w poniższy sposób. Zastąp elementy `{yourRG}` i `{userIdentity}` odpowiednio nazwą grupy zasobów i nazwą tożsamości zarządzanej przypisanej przez użytkownika.

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

Strategię można usunąć z subskrypcji. Usunięcie często przeprowadza się, gdy zasoby artefaktu przestają być potrzebne. Po usunięciu strategii artefakty przypisane w jej ramach są pozostawiane. Aby usunąć przypisanie strategii, wykonaj następującą operację interfejsu API REST:

- Identyfikator URI interfejsu API REST

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Usuwanie strategii

Aby usunąć samą strategię, wykonaj następującą operację interfejsu API REST:

- Identyfikator URI interfejsu API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono, przypisano i usunięto plan z interfejsem API REST. Aby dowiedzieć się więcej na temat planów platformy Azure, przejdź do artykułu dotyczącego cyklu życia planu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cyklu życia planu](./concepts/lifecycle.md)