---
title: Skonfiguruj zarządzane tożsamości za pomocą usługi Azure AD dla konta Azure Cosmos DB
description: Dowiedz się, jak skonfigurować zarządzane tożsamości za pomocą Azure Active Directory dla konta Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231499"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Skonfiguruj zarządzane tożsamości za pomocą Azure Active Directory dla konta Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. W tym artykule pokazano, jak utworzyć zarządzaną tożsamość dla kont Azure Cosmos DB.

> [!NOTE]
> Azure Cosmos DB są obecnie obsługiwane tylko tożsamości zarządzane przypisane do systemu.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zobacz co to [są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md). Aby dowiedzieć się więcej o zarządzanych typach tożsamości, zobacz [zarządzane typy tożsamości](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Aby skonfigurować tożsamości zarządzane, Twoje konto musi mieć [rolę współautor konta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

### <a name="using-an-azure-resource-manager-arm-template"></a>Korzystanie z szablonu Azure Resource Manager (ARM)

> [!IMPORTANT]
> Upewnij się, że `apiVersion` `2021-03-15` podczas pracy z tożsamościami zarządzanymi jest używana lub wyższa.

Aby włączyć tożsamość przypisaną przez system na nowym lub istniejącym koncie Azure Cosmos DB, należy uwzględnić w definicji zasobu następującą właściwość:

```json
"identity": {
    "type": "SystemAssigned"
}
```

`resources`Sekcja szablonu ARM powinna wyglądać następująco:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Po utworzeniu lub zaktualizowaniu konta Azure Cosmos DB zostanie wyświetlona następująca Właściwość:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md)
- Dowiedz się więcej o [kluczach zarządzanych przez klienta na Azure Cosmos DB](how-to-setup-cmk.md)
