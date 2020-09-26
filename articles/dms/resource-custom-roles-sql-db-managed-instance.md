---
title: 'Role niestandardowe: SQL Server online do migracji wystąpienia zarządzanego SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak używać ról niestandardowych dla SQL Server do migracji online wystąpienia zarządzanego Azure SQL.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: e950e34a1965e309d226c42bf4b3128cbe3bf3c5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91291507"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>Role niestandardowe dla SQL Server migracji do usługi Azure SQL Managed instance w trybie online

Azure Database Migration Service używa identyfikatora aplikacji do korzystania z usług platformy Azure. Identyfikator aplikacji wymaga roli współautor na poziomie subskrypcji (której wiele działów zabezpieczeń firmy nie zezwoli) lub tworzenia ról niestandardowych przyznających określone uprawnienia wymagane przez usługę Azure Database Migration Service. Ponieważ w Azure Active Directory istnieje limit 2 000 ról niestandardowych, można połączyć wszystkie uprawnienia wymagane przez identyfikator aplikacji w jedną lub dwie role niestandardowe, a następnie przyznać identyfikator aplikacji rolę niestandardową dla określonych obiektów lub grup zasobów (a na poziomie subskrypcji). Jeśli liczba ról niestandardowych nie jest istotna, można podzielić role niestandardowe według typu zasobu, aby utworzyć trzy role niestandardowe w sumie, zgodnie z poniższym opisem.

Sekcja AssignableScopes ciągu JSON definicji roli umożliwia kontrolowanie, gdzie uprawnienia są wyświetlane w interfejsie użytkownika **Dodaj przypisanie roli** w portalu. Prawdopodobnie chcesz zdefiniować rolę w grupie zasobów, a nawet poziomie zasobów, aby uniknąć bałaganu interfejsu użytkownika z dodatkowymi rolami. Należy zauważyć, że nie jest to zgodne z rzeczywistym przypisaniem roli.

## <a name="minimum-number-of-roles"></a>Minimalna liczba ról

Obecnie zalecamy utworzenie co najmniej dwóch ról niestandardowych dla identyfikatora aplikacji, jeden na poziomie zasobu, a drugi na poziomie subskrypcji.

> [!NOTE]
> Ostatni wymóg roli niestandardowej może zostać usunięty, ponieważ nowy kod wystąpienia zarządzanego SQL jest wdrażany na platformie Azure.

**Rola niestandardowa dla identyfikatora aplikacji**. Ta rola jest wymagana do Azure Database Migration Service migracji na poziomie *zasobu* lub *grupy zasobów* (Aby uzyskać więcej informacji na temat identyfikatora aplikacji, zobacz artykuł [Używanie portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Rola niestandardowa identyfikatora aplikacji — subskrypcja**. Ta rola jest wymagana do migracji Azure Database Migration Service na poziomie *subskrypcji* .

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Plik JSON powyżej musi być przechowywany w trzech plikach tekstowych i można użyć polecenia cmdlet AzureRM, AZ PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć role przy użyciu polecenia **New-AzureRmRoleDefinition (AzureRM)** lub **New-AzRoleDefinition (az)**.

Aby uzyskać więcej informacji, zobacz artykuł [role niestandardowe platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Po utworzeniu ról niestandardowych należy dodać przypisania ról do użytkowników i identyfikatorów aplikacji do odpowiednich zasobów lub grup zasobów:

* Rola "DMS rola-aplikacja" musi być przyznana IDENTYFIKATORowi aplikacji, który będzie używany na potrzeby migracji, a także na kontach magazynu, wystąpieniu Azure Database Migration Service i poziomach zasobów wystąpienia zarządzanego SQL.
* Rola "DMS rola-identyfikator aplikacji — Sub" musi być przyznana IDENTYFIKATORowi aplikacji na poziomie subskrypcji (nadanie nie powiedzie się dla zasobu lub grupy zasobów). Ten wymóg jest tymczasowy do momentu wdrożenia aktualizacji kodu.

## <a name="expanded-number-of-roles"></a>Rozszerzona Liczba ról

Jeśli liczba ról niestandardowych w Azure Active Directory nie jest istotna, zalecamy utworzenie łącznej liczby trzech ról. Nadal będzie potrzebna rola "DMS rola-identyfikator aplikacji — Sub", ale powyższa rola "DMS rola-aplikacja" jest dzielona według typu zasobu na dwie różne role.

**Rola niestandardowa dla identyfikatora aplikacji dla wystąpienia zarządzanego SQL**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Rola niestandardowa dla identyfikatora aplikacji dla magazynu**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Przypisanie roli

Aby przypisać rolę do użytkowników/identyfikatora aplikacji, Otwórz Azure Portal, wykonaj następujące czynności:

1. Przejdź do grupy zasobów lub zasobu (z wyjątkiem roli, która musi zostać przyznana w ramach subskrypcji), przejdź do **Access Control**, a następnie przewiń, aby znaleźć utworzone role niestandardowe.

2. Wybierz odpowiednią rolę, wybierz identyfikator aplikacji, a następnie Zapisz zmiany.

  IDENTYFIKATORY aplikacji zostaną wyświetlone na liście na karcie **przypisania ról** .

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji w tym scenariuszu w [przewodniku migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.
