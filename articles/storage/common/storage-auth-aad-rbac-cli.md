---
title: Przypisywanie roli platformy Azure na użytek dostępu do danych przy użyciu interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Dowiedz się, jak przy użyciu interfejsu wiersza polecenia platformy Azure przypisywać uprawnienia do podmiotu zabezpieczeń usługi Azure Active Directory przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure. Usługa Azure Storage obsługuje wbudowane i niestandardowe role platformy Azure do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785329"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Przypisywanie roli platformy Azure na użytek dostępu do danych obiektów blob i kolejek przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem kontroli dostępu na podstawie ról [(RBAC)](../../role-based-access-control/overview.md)platformy Azure. Usługa Azure Storage definiuje zestaw wbudowanych ról platformy Azure, które obejmują typowe zestawy uprawnień używanych do uzyskiwania dostępu do danych obiektów blob lub kolejek.

Gdy rola platformy Azure jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure udziela dostępu do tych zasobów dla tego podmiotu zabezpieczeń. Zakres dostępu może być ograniczony do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera bądź kolejki. Podmiotem zabezpieczeń usługi Azure AD może być użytkownik, grupa, nazwa główna usługi aplikacji lub tożsamość [zarządzana dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

W tym artykule opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure wyświetlić listę wbudowanych ról platformy Azure i przypisać je do użytkowników. Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy [Azure, zobacz Azure Command-Line Interface (CLI)](/cli/azure).

## <a name="azure-roles-for-blobs-and-queues"></a>Role platformy Azure dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określanie zakresu zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Lista dostępnych ról platformy Azure

Aby wyświetlić listę dostępnych wbudowanych ról platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, użyj [polecenia az role definition list:](/cli/azure/role/definition#az_role_definition_list)

```azurecli-interactive
az role definition list --out table
```

Zobaczysz listę wbudowanych ról danych usługi Azure Storage wraz z innymi wbudowanymi rolami dla platformy Azure:

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

Aby przypisać rolę platformy Azure do podmiotu zabezpieczeń, użyj [polecenia az role assignment create.](/cli/azure/role/assignment#az_role_assignment_create) Format polecenia może się różnić w zależności od zakresu przypisania. Poniższe przykłady pokazują, jak przypisać rolę do użytkownika w różnych zakresach, ale można użyć tego samego polecenia, aby przypisać rolę do dowolnego podmiotu zabezpieczeń.

> [!IMPORTANT]
> Podczas tworzenia konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych za pośrednictwem usługi Azure AD. Aby uzyskać dostęp do danych, musisz jawnie przypisać sobie rolę RBAC platformy Azure. Możesz przypisać go na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera bądź kolejki.
>
> Jeśli konto magazynu jest zablokowane z blokadą tylko do odczytu Azure Resource Manager, blokada uniemożliwia przypisanie ról RBAC platformy Azure, które są w zakresie konta magazynu lub kontenera danych (kontenera obiektów blob lub kolejki).

### <a name="container-scope"></a>Zakres kontenera

Aby przypisać rolę o zakresie do kontenera, określ ciąg zawierający zakres kontenera dla `--scope` parametru . Zakres kontenera ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

W poniższym przykładzie przypisano użytkownikowi rolę Współautor danych obiektu blob usługi **Storage** w zakresie do poziomu kontenera. Pamiętaj, aby zastąpić przykładowe wartości i wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Zakres kolejki

Aby przypisać rolę o zakresie do kolejki, określ ciąg zawierający zakres kolejki dla `--scope` parametru. Zakres kolejki ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

W poniższym przykładzie przypisano użytkownikowi rolę Współautor danych kolejki usługi **Storage** w zakresie do poziomu kolejki. Pamiętaj, aby zastąpić przykładowe wartości i wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Zakres konta magazynu

Aby przypisać rolę o zakresie do konta magazynu, określ zakres zasobu konta magazynu dla `--scope` parametru . Zakres konta magazynu ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

W poniższym przykładzie pokazano, jak przypisać użytkownikowi rolę Czytelnik danych obiektu blob usługi **Storage** na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Zakres grupy zasobów

Aby przypisać rolę o zakresie do grupy zasobów, określ nazwę lub identyfikator grupy zasobów dla `--resource-group` parametru . W poniższym przykładzie przypisano użytkownikowi rolę **Czytelnik danych** kolejki usługi Storage na poziomie grupy zasobów. Pamiętaj, aby zastąpić przykładowe wartości i wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Zakres subskrypcji

Aby przypisać rolę w zakresie subskrypcji, określ zakres subskrypcji dla `--scope` parametru . Zakres subskrypcji ma postać:

```
/subscriptions/<subscription>
```

W poniższym przykładzie pokazano, jak przypisać użytkownikowi rolę Czytelnik danych obiektu blob usługi **Storage** na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell modułu](../../role-based-access-control/role-assignments-powershell.md)
- [Użyj modułu Azure PowerShell, aby przypisać rolę platformy Azure w celu uzyskania dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-powershell.md)
- [Przypisywanie roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek za pomocą witryny Azure Portal](storage-auth-aad-rbac-portal.md)
