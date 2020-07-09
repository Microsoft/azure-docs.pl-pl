---
title: Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli RBAC na potrzeby dostępu do danych
titleSuffix: Azure Storage
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure przypisać uprawnienia do Azure Active Directory podmiotu zabezpieczeń z kontrolą dostępu opartą na rolach (RBAC). Usługa Azure Storage obsługuje wbudowane i niestandardowe role RBAC do uwierzytelniania za pośrednictwem usługi Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 25a38fc6f9607ef878ad3c5bf7074f5b63d5c121
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808863"
---
# <a name="use-azure-cli-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli RBAC na potrzeby dostępu do danych obiektów blob i kolejek

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych obiektu BLOB lub kolejki.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

W tym artykule opisano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć listę wbudowanych ról RBAC i przypisać je do użytkowników. Aby uzyskać więcej informacji o korzystaniu z interfejsu wiersza polecenia platformy Azure, zobacz [Azure Command Line Interface (CLI)](https://docs.microsoft.com/cli/azure).

## <a name="rbac-roles-for-blobs-and-queues"></a>Role RBAC dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Określ zakres zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Wyświetl dostępne role RBAC

Aby wyświetlić listę dostępnych wbudowanych ról RBAC przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list) :

```azurecli-interactive
az role definition list --out table
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

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Przypisywanie roli RBAC do podmiotu zabezpieczeń

Aby przypisać rolę RBAC do podmiotu zabezpieczeń, użyj polecenia [AZ role Assign Create](/cli/azure/role/assignment#az-role-assignment-create) . Format polecenia może różnić się w zależności od zakresu przypisania. W poniższych przykładach pokazano, jak przypisać rolę do użytkownika w różnych zakresach, ale można użyć tego samego polecenia, aby przypisać rolę do dowolnego podmiotu zabezpieczeń.

### <a name="container-scope"></a>Zakres kontenera

Aby przypisać rolę do zakresu kontenera, Określ ciąg zawierający zakres kontenera dla `--scope` parametru. Zakres dla kontenera ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Poniższy przykład przypisuje rolę **współautor danych obiektów blob magazynu** użytkownikowi w zakresie do poziomu kontenera. Pamiętaj o zamianie wartości przykładowych i symboli zastępczych w nawiasach z własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Zakres kolejki

Aby przypisać rolę do zakresu kolejki, Określ ciąg zawierający zakres kolejki dla `--scope` parametru. Zakres dla kolejki ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Poniższy przykład przypisuje rolę **współautor danych kolejki magazynu** użytkownikowi, w zakresie do poziomu kolejki. Pamiętaj o zamianie wartości przykładowych i symboli zastępczych w nawiasach z własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Zakres konta magazynu

Aby przypisać rolę do zakresu konta magazynu, określ zakres zasobów konta magazynu dla `--scope` parametru. Zakres dla konta magazynu ma postać:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Poniższy przykład pokazuje, jak przypisać rolę **czytnika danych obiektu blob magazynu** do użytkownika na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Zakres grupy zasobów

Aby przypisać rolę objętą zakresem do grupy zasobów, określ nazwę lub identyfikator grupy zasobów dla `--resource-group` parametru. Poniższy przykład przypisuje rolę **czytnika danych kolejki magazynu** użytkownikowi na poziomie grupy zasobów. Pamiętaj, aby zastąpić przykładowe wartości i wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Zakres subskrypcji

Aby przypisać rolę objętą zakresem subskrypcji, określ zakres subskrypcji dla `--scope` parametru. Zakres subskrypcji ma postać:

```
/subscriptions/<subscription>
```

Poniższy przykład pokazuje, jak przypisać rolę **czytnika danych obiektu blob magazynu** do użytkownika na poziomie konta magazynu. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Udzielanie dostępu do obiektów blob platformy Azure i danych z kolejki RBAC przy użyciu Azure PowerShell](storage-auth-aad-rbac-powershell.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach w witrynie Azure Portal](storage-auth-aad-rbac-portal.md)
