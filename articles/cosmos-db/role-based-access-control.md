---
title: Kontrola dostępu oparta na rolach w usłudze Azure Cosmos DB
description: Dowiedz się, jak Azure Cosmos DB zapewnia ochronę bazy danych za pomocą integracji z usługą Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445101"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Kontrola dostępu oparta na rolach w usłudze Azure Cosmos DB

Azure Cosmos DB zapewnia wbudowaną funkcję kontroli dostępu opartej na rolach (RBAC) dla typowych scenariuszy zarządzania w programie Azure Cosmos DB. Osoba mająca profil w Azure Active Directory może przypisywać te role kontroli dostępu do użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych w celu udzielenia lub odmowy dostęp do zasobów i operacji na zasobach Azure Cosmos DB. Przypisania ról są objęte zakresem tylko dostępu do płaszczyzny kontroli, który obejmuje dostęp do kont usługi Azure Cosmos, baz danych, kontenerów i ofert (przepływności).

## <a name="built-in-roles"></a>Wbudowane role

Poniżej przedstawiono wbudowane role obsługiwane przez Azure Cosmos DB:

|**Wbudowana rola**  |**Opis**  |
|---------|---------|
|[Współautor konta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Może zarządzać kontami Azure Cosmos DB.|
|[Cosmos DB czytelnika konta](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Może odczytywać Azure Cosmos DB dane konta.|
|[Operator kopii zapasowej Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Może przesłać żądanie przywracania dla bazy danych lub kontenera usługi Azure Cosmos.|
|[Operator Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Program umożliwia inicjowanie obsługi kont, baz danych i kontenerów usługi Azure Cosmos, ale nie ma dostępu do kluczy wymaganych do uzyskania dostępu do danych.|

> [!IMPORTANT]
> Obsługa RBAC w Azure Cosmos DB dotyczy tylko operacji na płaszczyźnie kontroli. Operacje płaszczyzny danych są zabezpieczane przy użyciu kluczy głównych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz [bezpieczny dostęp do danych w Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Zarządzanie tożsamościami i dostępem (IAM)

Okienko **kontroli dostępu (IAM)** w Azure Portal służy do konfigurowania kontroli dostępu opartej na rolach dla zasobów usługi Azure Cosmos. Role są stosowane do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych w Active Directory. Można używać wbudowanych ról lub ról niestandardowych dla użytkowników indywidualnych i grup. Poniższy zrzut ekranu przedstawia Active Directory integrację (RBAC) przy użyciu funkcji kontroli dostępu (IAM) w Azure Portal:

![Kontrola dostępu (IAM) w zabezpieczeniach bazy danych Azure Portal.](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Role niestandardowe

Oprócz wbudowanych ról użytkownicy mogą również tworzyć [role niestandardowe](../role-based-access-control/custom-roles.md) na platformie Azure i stosować te role do jednostek usługi we wszystkich subskrypcjach w ramach dzierżawy Active Directory. Role niestandardowe zapewniają użytkownikom sposób tworzenia definicji ról RBAC z niestandardowym zestawem operacji dostawcy zasobów. Aby dowiedzieć się, które operacje są dostępne do tworzenia ról niestandardowych dla Azure Cosmos DB Zobacz, [Azure Cosmos DB operacje dostawcy zasobów](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Uniemożliwianie zmian z zestawu SDK Cosmos

Dostawcę zasobów Cosmos można zablokować, aby zapobiec wprowadzeniu zmian w zasobach, takich jak konto Cosmos, bazy danych, kontenery i przepływność z dowolnego klienta łączącego klucze kont (tj. aplikacje łączące się za pośrednictwem zestawu SDK Cosmos). Po ustawieniu zmiany w dowolnym zasobie muszą pochodzić od użytkownika z poprawnym rolą RBAC i poświadczeniami. Ta funkcja jest ustawiana `disableKeyBasedMetadataWriteAccess` za pomocą wartości właściwości w dostawcy zasobów Cosmos. Przykładem szablonu Azure Resource Manager z tym ustawieniem właściwości jest poniżej.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure](../role-based-access-control/overview.md)
- [Niestandardowe role dla zasobów platformy Azure](../role-based-access-control/custom-roles.md)
- [Operacje dostawcy zasobów Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
