---
title: Programowe tworzenie zasad
description: W tym artykule opisano sposób programowego tworzenia zasad i zarządzania nimi dla Azure Policy za pomocą interfejsu wiersza polecenia platformy Azure, Azure PowerShell i API REST.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 4c3a503e8d48573441093879511490608437b1e1
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033867"
---
# <a name="programmatically-create-policies"></a>Programowe tworzenie zasad

Ten artykuł przeprowadzi Cię przez programowe tworzenie zasad i zarządzanie nimi. Definicje Azure Policy wymuszają różne reguły i efekty dotyczące zasobów. Wymuszanie gwarantuje, że zasoby pozostają zgodne ze standardami firmy i umowami dotyczącymi poziomu usług.

Aby uzyskać informacje na temat zgodności, zobacz [pobieranie danych zgodności](get-compliance-data.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania wstępne:

1. Jeśli ta czynność nie została jeszcze wykonana, zainstaluj klienta [ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie, które wysyła żądania HTTP do interfejsów API opartych na usłudze Azure Resource Manager.

1. Zaktualizuj moduł Azure PowerShell do najnowszej wersji. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje. Aby uzyskać więcej informacji na temat najnowszej wersji, zobacz [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Zarejestruj dostawcę zasobów usługi Azure Policy Insights za pomocą Azure PowerShell, aby sprawdzić, czy subskrypcja współpracuje z dostawcą zasobów. Aby zarejestrować dostawcę zasobów, musisz mieć uprawnienie do uruchamiania operacji Rejestruj akcję dla dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Aby uzyskać więcej informacji na temat rejestrowania i przeglądania dostawców zasobów, zobacz [dostawcy zasobów i ich typy](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Jeśli jeszcze tego nie zrobiono, zainstaluj interfejs wiersza polecenia platformy Azure. Najnowszą wersję można uzyskać w [instalacji interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Tworzenie i przypisywanie definicji zasad

Pierwszym krokiem w celu lepszego wglądu w zasoby jest utworzenie i przypisanie zasad do zasobów. Następnym krokiem jest zapoznanie się z sposobem programowego tworzenia i przypisywania zasad. Przykładowe zasady przeprowadzają inspekcję kont magazynu otwartych dla wszystkich sieci publicznych przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure i żądań HTTP.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Tworzenie i przypisywanie definicji zasad za pomocą programu PowerShell

1. Użyj poniższego fragmentu kodu JSON, aby utworzyć plik JSON o nazwie AuditStorageAccounts.jsna.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Aby uzyskać więcej informacji na temat tworzenia definicji zasad, zobacz [Azure Policy struktura definicji](../concepts/definition-structure.md).

1. Uruchom następujące polecenie, aby utworzyć definicję zasad przy użyciu AuditStorageAccounts.jspliku.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Polecenie tworzy definicję zasad o nazwie _konta magazynu inspekcji otwarte dla sieci publicznych_.
   Aby uzyskać więcej informacji na temat innych parametrów, których można użyć, zobacz [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Gdy jest wywoływana bez parametrów lokalizacji, `New-AzPolicyDefinition` domyślnie zapisuje definicję zasad w wybranej subskrypcji kontekstu sesji. Aby zapisać definicję w innej lokalizacji, użyj następujących parametrów:

   - Identyfikator **subskrypcji** — Zapisz w innej subskrypcji. Wymaga wartości _identyfikatora GUID_ .
   - **ManagementGroupName** — Zapisz w grupie zarządzania. Wymaga wartości _ciągu_ .

1. Po utworzeniu definicji zasad można utworzyć przypisanie zasad, uruchamiając następujące polecenia:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Zastąp _ContosoRG_ nazwą zamierzonej grupy zasobów.

   Parametr **SCOPE** w `New-AzPolicyAssignment` działaniu z grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem. Parametr używa pełnej ścieżki zasobu, która Właściwość **ResourceID** `Get-AzResourceGroup` zwraca wartość. Wzorzec dla **zakresu** dla każdego kontenera jest następujący:. Zastąp `{rName}` `{rgName}` `{subId}` `{mgName}` odpowiednio nazwę zasobu, nazwę grupy zasobów, Identyfikator subskrypcji i nazwę grupy zarządzania.
   `{rType}` zostanie zastąpiony **typem zasobu** zasobu, `Microsoft.Compute/virtualMachines` na przykład dla maszyny wirtualnej.

   - Zasoby `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupa zasobów — `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Ramach `/subscriptions/{subId}`
   - Grupa zarządzania — `/providers/Microsoft.Management/managementGroups/{mgName}`

Aby uzyskać więcej informacji na temat zarządzania zasadami zasobów przy użyciu modułu Menedżer zasobów PowerShell, zobacz [AZ. resources](/powershell/module/az.resources/#policy).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Tworzenie i przypisywanie definicji zasad za pomocą ARMClient

Aby utworzyć definicję zasad, należy wykonać poniższą procedurę.

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik JSON. Plik zostanie wywoływać w następnym kroku.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Utwórz definicję zasad przy użyciu jednego z następujących wywołań:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Zastąp poprzednią wartość {Subscription} IDENTYFIKATORem subskrypcji lub {managementGroupId} IDENTYFIKATORem [grupy zarządzania](../../management-groups/overview.md).

   Aby uzyskać więcej informacji na temat struktury zapytania, zobacz [definicje Azure Policy — Tworzenie lub aktualizowanie](/rest/api/resources/policydefinitions/createorupdate) oraz [definicje zasad — Tworzenie lub aktualizowanie w grupie zarządzania](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Aby utworzyć przypisanie zasad i przypisać definicję zasad na poziomie grupy zasobów, należy wykonać czynności opisane w poniższej procedurze.

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik przydziału zasad JSON. Zastąp przykładowe informacje w &lt; &gt; symbolami własnymi wartościami.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Utwórz przypisanie zasad, używając następującego wywołania:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Zastąp przykładowe informacje w &lt; &gt; symbolami własnymi wartościami.

   Aby uzyskać więcej informacji na temat wykonywania wywołań HTTP do interfejsu API REST, zobacz [zasoby interfejsu API REST platformy Azure](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Tworzenie i przypisywanie definicji zasad za pomocą interfejsu wiersza polecenia platformy Azure

Aby utworzyć definicję zasad, należy wykonać czynności opisane w poniższej procedurze:

1. Skopiuj poniższy fragment kodu JSON, aby utworzyć plik przydziału zasad JSON.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Aby uzyskać więcej informacji na temat tworzenia definicji zasad, zobacz [Azure Policy struktura definicji](../concepts/definition-structure.md).

1. Uruchom następujące polecenie, aby utworzyć definicję zasad:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Polecenie tworzy definicję zasad o nazwie _konta magazynu inspekcji otwarte dla sieci publicznych_.
   Aby uzyskać więcej informacji na temat innych parametrów, których można użyć, zobacz [AZ Policy Definition Create](/cli/azure/policy/definition#az_policy_definition_create).

   Gdy jest wywoływana bez parametrów lokalizacji, `az policy definition creation` domyślnie zapisuje definicję zasad w wybranej subskrypcji kontekstu sesji. Aby zapisać definicję w innej lokalizacji, użyj następujących parametrów:

   - **subskrypcja** — Zapisz w innej subskrypcji. Wymaga wartości identyfikatora _GUID_ dla identyfikatora subskrypcji lub wartości _ciągu_ dla nazwy subskrypcji.
   - **Zarządzanie — Grupa** — Zapisz w grupie zarządzania. Wymaga wartości _ciągu_ .

1. Użyj następującego polecenia, aby utworzyć przypisanie zasad. Zastąp przykładowe informacje w &lt; &gt; symbolami własnymi wartościami.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Parametr **SCOPE** w `az policy assignment create` działaniu z grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem. Parametr używa pełnej ścieżki zasobów. Wzorzec dla **zakresu** dla każdego kontenera jest następujący:. Zastąp `{rName}` `{rgName}` `{subId}` `{mgName}` odpowiednio nazwę zasobu, nazwę grupy zasobów, Identyfikator subskrypcji i nazwę grupy zarządzania. `{rType}` zostanie zastąpiony **typem zasobu** zasobu, `Microsoft.Compute/virtualMachines` na przykład dla maszyny wirtualnej.

   - Zasoby `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupa zasobów — `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Ramach `/subscriptions/{subID}`
   - Grupa zarządzania — `/providers/Microsoft.Management/managementGroups/{mgName}`

Identyfikator definicji Azure Policy można uzyskać za pomocą programu PowerShell przy użyciu następującego polecenia:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Identyfikator definicji zasad dla utworzonej definicji zasad powinien wyglądać podobnie do poniższego przykładu:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Aby uzyskać więcej informacji o sposobach zarządzania zasadami zasobów przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [zasady zasobów interfejsu wiersza polecenia platformy Azure](/cli/azure/policy).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej informacji na temat poleceń i zapytań zawartych w tym artykule.

- [Zasoby interfejsu API REST platformy Azure](/rest/api/resources/)
- [Moduły Azure PowerShell](/powershell/module/az.resources/#policy)
- [Poleceń zasad interfejsu wiersza polecenia platformy Azure](/cli/azure/policy)
- [Dokumentacja interfejsu API REST dostawcy zasobów usługi Azure Policy Insights](/rest/api/policy-insights)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../management-groups/overview.md).
