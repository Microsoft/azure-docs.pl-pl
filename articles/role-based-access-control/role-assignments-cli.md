---
title: Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure — kontrola RBAC Azure
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu interfejsu wiersza polecenia platformy Azure i kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: ee356f32b6799c6182ec1c9e061a35271a4bbc23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556974"
---
# <a name="assign-azure-roles-using-azure-cli"></a>Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przypisać role, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)
- [Bash w Azure Cloud Shell](../cloud-shell/overview.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="steps-to-assign-an-azure-role"></a>Procedura przypisywania roli platformy Azure

Aby przypisać rolę składa się z trzech elementów: podmiot zabezpieczeń, definicja roli i zakres.

### <a name="step-1-determine-who-needs-access"></a>Krok 1. Określanie, kto potrzebuje dostępu

Rolę można przypisać do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Aby przypisać rolę, może być konieczne określenie unikatowego identyfikatora obiektu. Identyfikator ma format: `11111111-1111-1111-1111-111111111111` . Identyfikator można uzyskać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

**Użytkownik**

W przypadku użytkownika usługi Azure AD Pobierz główną nazwę użytkownika, taką jak *patlong \@ contoso.com* lub identyfikator obiektu użytkownika. Aby uzyskać identyfikator obiektu, można użyć [AZ AD User show](/cli/azure/ad/user#az_ad_user_show).

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Grupa**

W przypadku grupy usługi Azure AD wymagany jest identyfikator obiektu grupy. Aby uzyskać identyfikator obiektu, można użyć [AZ AD Group Show](/cli/azure/ad/group#az_ad_group_show) lub [AZ AD Group list](/cli/azure/ad/group#az_ad_group_list).

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Jednostka usługi**

W przypadku jednostki usługi Azure AD (tożsamości używanej przez aplikację) wymagany jest identyfikator obiektu jednostki usługi. Aby uzyskać identyfikator obiektu, można użyć [AZ AD Sp list](/cli/azure/ad/sp#az_ad_sp_list). Dla jednostki usługi Użyj identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Tożsamość zarządzana**

Dla przypisanej do systemu lub tożsamości zarządzanej przypisanej przez użytkownika wymagany jest identyfikator obiektu. Aby uzyskać identyfikator obiektu, można użyć [AZ AD Sp list](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Aby tylko wyświetlić tożsamości zarządzane przypisane przez użytkownika, można użyć [AZ Identity list](/cli/azure/identity#az_identity_list).

```azurecli
az identity list
```
    
### <a name="step-2-select-the-appropriate-role"></a>Krok 2. Wybierz odpowiednią rolę

Uprawnienia są pogrupowane w role. Możesz wybrać jedną z listy [wbudowanych ról platformy Azure](built-in-roles.md) lub użyć własnych ról niestandardowych. Najlepszym rozwiązaniem jest przyznanie dostępu z najniższymi potrzebami, dlatego nie należy przypisywać szerszej roli.

Aby wyświetlić listę ról i uzyskać unikatowy identyfikator roli, można użyć [AZ role Definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Poniżej przedstawiono sposób wyświetlania szczegółów konkretnej roli.

```azurecli
az role definition list --name "{roleName}"
```

Aby uzyskać więcej informacji, zobacz [lista definicji ról platformy Azure](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Krok 3. identyfikowanie wymaganego zakresu

Platforma Azure udostępnia cztery poziomy zakresu: zasobu, [grupy zasobów](../azure-resource-manager/management/overview.md#resource-groups), subskrypcji i [grupy zarządzania](../governance/management-groups/overview.md). Najlepszym rozwiązaniem jest przyznanie dostępu z najniższymi potrzebami, dlatego nie należy przypisywać roli w szerszym zakresie. Aby uzyskać więcej informacji na temat zakresu, zobacz [Opis zakresu](scope-overview.md).

**Zakres zasobu**

Dla zakresu zasobów wymagany jest identyfikator zasobu dla zasobu. Identyfikator zasobu można znaleźć, przeglądając właściwości zasobu w Azure Portal. Identyfikator zasobu ma następujący format.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Zakres grupy zasobów**

Dla zakresu grupy zasobów wymagana jest nazwa grupy zasobów. Nazwę można znaleźć na stronie **grupy zasobów** w Azure Portal lub można użyć [AZ Group list](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Zakres subskrypcji** 

W przypadku zakresu subskrypcji wymagany jest Identyfikator subskrypcji. Identyfikator można znaleźć na stronie **subskrypcje** w Azure Portal lub można użyć [AZ Account List](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Zakres grupy zarządzania** 

W przypadku zakresu grupy zarządzania potrzebna jest nazwa grupy zarządzania. Nazwę można znaleźć na stronie **grupy zarządzania** w Azure Portal lub można użyć [AZ Account Management-Group list](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-assign-role"></a>Krok 4. Przypisywanie roli

Aby przypisać rolę, użyj polecenia [AZ role Assign Create](/cli/azure/role/assignment#az_role_assignment_create) . W zależności od zakresu polecenie ma zwykle jeden z następujących formatów.

**Zakres zasobu**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Zakres grupy zasobów**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Zakres subskrypcji** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Zakres grupy zarządzania** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Poniżej przedstawiono przykład danych wyjściowych po przypisaniu roli [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika w zakresie grupy zasobów.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="assign-role-examples"></a>Przypisywanie przykładów roli

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Przypisywanie roli dla wszystkich kontenerów obiektów BLOB w zakresie zasobów konta magazynu

Przypisuje rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) do jednostki usługi z identyfikatorem obiektu *55555555-5555-5555-5555-555555555555* w zakresie zasobów dla konta magazynu o nazwie *storage12345*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Przypisywanie roli dla określonego zakresu zasobów kontenera obiektów BLOB

Przypisuje rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) do jednostki usługi z identyfikatorem obiektu *55555555-5555-5555-5555-555555555555* w zakresie zasobów dla kontenera obiektów BLOB o nazwie *BLOB-Container-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Przypisywanie roli dla grupy w określonym zakresie zasobów sieci wirtualnej

Przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Przypisywanie roli dla użytkownika w zakresie grupy zasobów

Przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika *patlong \@ contoso.com* w zakresie grupy zasobów *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Przypisywanie roli dla użytkownika przy użyciu unikatowego identyfikatora roli w zakresie grupy zasobów

Istnieje kilka razy więcej czasu, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- W nazwie jest używana rola w wersji zapoznawczej mająca **(wersja zapoznawcza)** . Po wydaniu roli zostanie zmieniona nazwa roli.

Nawet w przypadku zmiany nazwy roli identyfikator roli nie zmienia się. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym, jeśli zostanie zmieniona nazwa roli, skrypty mogą pracować.

Poniższy przykład przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika *patlong \@ contoso.com* w zakresie grupy zasobów *Pharma-Sales* .

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-resource-group-scope"></a>Przypisywanie roli dla wszystkich kontenerów obiektów BLOB w zakresie grupy zasobów

Przypisuje rolę [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) do jednostki usługi z identyfikatorem obiektu *55555555-5555-5555-5555-555555555555* w zakresie grupy zasobów *przykład-Storage-RG* .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternatywnie można określić w pełni kwalifikowaną grupę zasobów z `--scope` parametrem:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Przypisywanie roli dla aplikacji w zakresie grupy zasobów

Przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do aplikacji z identyfikatorem obiektu jednostki usługi 44444444-4444-4444-4444-444444444444 w zakresie grupy zasobów *Pharma-Sales* .

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-new-service-principal-at-a-resource-group-scope"></a>Przypisywanie roli dla nowej nazwy głównej usługi w zakresie grupy zasobów

Jeśli utworzysz nową nazwę główną usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może zakończyć się niepowodzeniem. Na przykład, jeśli używasz skryptu do utworzenia nowej tożsamości zarządzanej, a następnie spróbujesz przypisać rolę do tej jednostki usługi, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Nazwa główna usługi jest tworzona w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który jeszcze nie replikuje jednostki usługi. Aby rozwiązać ten scenariusz, należy określić typ podmiotu podczas tworzenia przypisania roli.

Aby przypisać rolę, użyj [AZ role Assign Create](/cli/azure/role/assignment#az_role_assignment_create), określ wartość dla `--assignee-object-id` , a następnie ustaw `--assignee-principal-type` jako `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

Poniższy przykład przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do tożsamości zarządzanej przez *test MSI* w zakresie grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Przypisywanie roli dla użytkownika w zakresie subskrypcji

Przypisuje rolę [czytnika](built-in-roles.md#reader) do użytkownika *annm \@ example.com* w zakresie subskrypcji.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-group-at-a-subscription-scope"></a>Przypisywanie roli dla grupy w zakresie subskrypcji

Przypisuje rolę [czytnika](built-in-roles.md#reader) do grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-subscription-scope"></a>Przypisz rolę dla wszystkich kontenerów obiektów BLOB w zakresie subskrypcji

Przypisuje rolę [czytnika danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-reader) do użytkownika *Alain \@ example.com* w zakresie subskrypcji.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Przypisywanie roli dla użytkownika w zakresie grupy zarządzania

Przypisuje rolę [czytnika rozliczeń](built-in-roles.md#billing-reader) do użytkownika *Alain \@ example.com* w zakresie grupy zarządzania.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Następne kroki

- [Lista przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](role-assignments-list-cli.md)
- [Zarządzanie zasobami i grupami zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/management/manage-resources-cli.md)
