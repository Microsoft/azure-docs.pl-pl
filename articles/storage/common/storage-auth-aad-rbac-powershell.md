---
title: Przypisywanie roli platformy Azure na potrzeby dostępu do danych za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą modułu Azure PowerShell przypisać uprawnienia do Azure Active Directory podmiotu zabezpieczeń za pomocą kontroli dostępu opartej na rolach (Azure RBAC). Usługa Azure Storage obsługuje wbudowaną i niestandardową rolę platformy Azure do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e4fea52f56da9f2e84746daf0121df0b4355411
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375956"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Przypisywanie roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek przy użyciu programu PowerShell

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do kontenerów lub kolejek.

Gdy rola platformy Azure zostanie przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano, jak używać Azure PowerShell do wyświetlania listy ról wbudowanych platformy Azure i przypisywania ich do użytkowników. Aby uzyskać więcej informacji o korzystaniu z Azure PowerShell, zobacz [omówienie Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Role platformy Azure dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określ zakres zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Wyświetl listę dostępnych ról platformy Azure

Aby wyświetlić listę dostępnych wbudowanych ról platformy Azure z Azure PowerShell, użyj polecenia [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Zostaną wyświetlone wbudowane role danych usługi Azure Storage wraz z innymi wbudowanymi rolami platformy Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Przypisywanie roli platformy Azure do podmiotu zabezpieczeń

Aby przypisać rolę platformy Azure do podmiotu zabezpieczeń, użyj polecenia [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Format polecenia może różnić się w zależności od zakresu przypisania. Aby uruchomić polecenie, musisz mieć przypisaną rolę właściciela lub współautora w odpowiednim zakresie. W poniższych przykładach pokazano, jak przypisać rolę do użytkownika w różnych zakresach, ale można użyć tego samego polecenia, aby przypisać rolę do dowolnego podmiotu zabezpieczeń.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych za pośrednictwem usługi Azure AD. Aby uzyskać dostęp do danych, należy jawnie przypisać rolę RBAC usługi Azure. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.
>
> W przypadku zablokowania konta magazynu z Azure Resource Manager blokadą tylko do odczytu, blokada uniemożliwia Przypisanie ról RBAC platformy Azure objętych zakresem do konta magazynu lub kontenera danych (kontenera obiektów blob lub kolejki).

### <a name="container-scope"></a>Zakres kontenera

Aby przypisać rolę do zakresu kontenera, Określ ciąg zawierający zakres kontenera dla `--scope` parametru. Zakres dla kontenera ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Poniższy przykład przypisuje rolę **współautor danych obiektów blob magazynu** użytkownikowi w zakresie kontenera o nazwie *Sample-Container*. Pamiętaj o zamianie wartości przykładowych i symboli zastępczych w nawiasach z własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Zakres kolejki

Aby przypisać rolę do zakresu kolejki, Określ ciąg zawierający zakres kolejki dla `--scope` parametru. Zakres dla kolejki ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Poniższy przykład przypisuje rolę **współautor danych kolejki magazynu** użytkownikowi z zakresu kolejki o nazwie *przykład-Queue*. Pamiętaj o zamianie wartości przykładowych i symboli zastępczych w nawiasach z własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Zakres konta magazynu

Aby przypisać rolę do zakresu konta magazynu, określ zakres zasobów konta magazynu dla `--scope` parametru. Zakres dla konta magazynu ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Poniższy przykład pokazuje, jak określić zakres roli **czytnika danych obiektów blob magazynu** dla użytkownika na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Zakres grupy zasobów

Aby przypisać rolę objętą zakresem do grupy zasobów, określ nazwę lub identyfikator grupy zasobów dla `--resource-group` parametru. Poniższy przykład przypisuje rolę **czytnika danych kolejki magazynu** użytkownikowi na poziomie grupy zasobów. Pamiętaj, aby zastąpić przykładowe wartości i wartości symboli zastępczych w nawiasach własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Zakres subskrypcji

Aby przypisać rolę objętą zakresem subskrypcji, określ zakres subskrypcji dla `--scope` parametru. Zakres subskrypcji ma postać:

```
/subscriptions/<subscription>
```

Poniższy przykład pokazuje, jak przypisać rolę **czytnika danych obiektu blob magazynu** do użytkownika na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu modułu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-cli.md)
- [Przypisywanie roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek za pomocą witryny Azure Portal](storage-auth-aad-rbac-portal.md)
