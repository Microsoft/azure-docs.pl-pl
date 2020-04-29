---
title: Dodawanie lub usuwanie przypisań ról przy użyciu usług Azure RBAC i interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu funkcji kontroli dostępu opartej na rolach (RBAC) i interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79245670"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Dodawanie lub usuwanie przypisań ról przy użyciu usług Azure RBAC i interfejsu wiersza polecenia platformy Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]W tym artykule opisano sposób przypisywania ról przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)
- [Bash w Azure Cloud Shell](/azure/cloud-shell/overview) lub [interfejs wiersza polecenia platformy Azure](/cli/azure)

## <a name="get-object-ids"></a>Pobierz identyfikatory obiektów

Aby dodać lub usunąć przypisania ról, może być konieczne określenie unikatowego identyfikatora obiektu. Identyfikator ma format: `11111111-1111-1111-1111-111111111111`. Identyfikator można uzyskać przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator obiektu dla użytkownika usługi Azure AD, możesz użyć [AZ AD User show](/cli/azure/ad/user#az-ad-user-show).

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Grupa

Aby uzyskać identyfikator obiektu dla grupy usługi Azure AD, możesz użyć [AZ AD Group Show](/cli/azure/ad/group#az-ad-group-show) lub [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator obiektu dla jednostki usługi Azure AD (tożsamość używana przez aplikację), możesz użyć [AZ AD Sp list](/cli/azure/ad/sp#az-ad-sp-list). Dla jednostki usługi Użyj identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W celu udzielenia dostępu w ramach RBAC należy dodać przypisanie roli.

### <a name="user-at-a-resource-group-scope"></a>Użytkownik w zakresie grupy zasobów

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zasobów, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do *patlong\@contoso.com* użytkownika w zakresie grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Używanie unikatowego identyfikatora roli

Istnieje kilka razy więcej czasu, gdy nazwa roli może ulec zmianie, na przykład:

- Używasz własnej roli niestandardowej i zdecydujesz się zmienić nazwę.
- W nazwie jest używana rola w wersji zapoznawczej mająca **(wersja zapoznawcza)** . Po wydaniu roli zostanie zmieniona nazwa roli.

> [!IMPORTANT]
> Wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Nawet w przypadku zmiany nazwy roli identyfikator roli nie zmienia się. Jeśli używasz skryptów lub automatyzacji do tworzenia przypisań ról, najlepszym rozwiązaniem jest użycie unikatowego identyfikatora roli zamiast nazwy roli. W związku z tym, jeśli zostanie zmieniona nazwa roli, skrypty mogą pracować.

Aby dodać przypisanie roli przy użyciu unikatowego identyfikatora roli zamiast nazwy roli, użyj polecenia [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create).

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę [współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) do użytkownika *patlong\@contoso.com* w zakresie grupy zasobów *Pharma-Sales* . Aby uzyskać unikatowy identyfikator roli, można użyć polecenie [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list) lub zobaczyć [wbudowane role dla zasobów platformy Azure](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Grupuj w zakresie subskrypcji

Aby dodać przypisanie roli dla grupy, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać informacje na temat pobierania identyfikatora obiektu grupy, zobacz [pobieranie identyfikatorów obiektów](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje rolę *czytnika* do grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Grupuj w zakresie zasobów

Aby dodać przypisanie roli dla grupy, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać informacje na temat pobierania identyfikatora obiektu grupy, zobacz [pobieranie identyfikatorów obiektów](#get-object-ids).

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie zasobów dla sieci wirtualnej o nazwie *Pharma-Sales-Project-Network*.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Aplikacja w zakresie grupy zasobów

Aby dodać przypisanie roli dla aplikacji, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać informacje na temat pobierania identyfikatora obiektu aplikacji, zobacz [pobieranie identyfikatorów obiektów](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do aplikacji z identyfikatorem obiektu 44444444-4444-4444-4444-444444444444 w zakresie grupy zasobów *Pharma-Sales* .

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Użytkownik objęty zakresem subskrypcji

Aby dodać przypisanie roli dla użytkownika w zakresie subskrypcji, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć w bloku **subskrypcje** w Azure Portal lub użyć [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Poniższy przykład przypisuje rolę *czytnika* do użytkownika *annm\@example.com* w zakresie subskrypcji.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Użytkownik w zakresie grupy zarządzania

Aby dodać przypisanie roli dla użytkownika w zakresie grupy zarządzania, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create). Aby uzyskać identyfikator grupy zarządzania, można go znaleźć w bloku **grupy zarządzania** w Azure Portal lub użyć [AZ Account Management-Group list](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Poniższy przykład przypisuje rolę *czytnika rozliczeń* do *użytkownika\@Alain example.com* w zakresie grupy zarządzania.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Nowa nazwa główna usługi

Jeśli utworzysz nową nazwę główną usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może zakończyć się niepowodzeniem. Na przykład, jeśli używasz skryptu do utworzenia nowej tożsamości zarządzanej, a następnie spróbujesz przypisać rolę do tej jednostki usługi, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Nazwa główna usługi jest tworzona w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który jeszcze nie replikuje jednostki usługi. Aby rozwiązać ten scenariusz, należy określić typ podmiotu podczas tworzenia przypisania roli.

Aby dodać przypisanie roli, użyj [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create), określ wartość dla `--assignee-object-id`, a następnie ustaw `--assignee-principal-type` jako. `ServicePrincipal`

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Poniższy przykład przypisuje rolę *współautor maszyny wirtualnej* do tożsamości zarządzanej przez *test MSI* w zakresie grupy zasobów *Pharma-Sales* :

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Aby usunąć dostęp w ramach RBAC, usuń przypisanie roli za pomocą polecenia [AZ role przypisanie Delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Poniższy przykład usuwa przypisanie roli *współautor maszyny wirtualnej* z użytkownika *patlong\@contoso.com* w grupie zasobów *Pharma-Sales* :

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Poniższy przykład usuwa rolę *czytnika* z grupy *zespołu Ann Mack* o identyfikatorze 22222222-2222-2222-2222-222222222222 w zakresie subskrypcji. Aby uzyskać informacje na temat pobierania identyfikatora obiektu grupy, zobacz [pobieranie identyfikatorów obiektów](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Poniższy przykład usuwa rolę *czytnika rozliczeń* z użytkownika *Alain\@example.com* w zakresie grupy zarządzania. Aby uzyskać identyfikator grupy zarządzania, można użyć [AZ Account Management-Group list](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure](role-assignments-list-cli.md)
- [Zarządzanie zasobami i grupami zasobów platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/cli-azure-resource-manager.md)
