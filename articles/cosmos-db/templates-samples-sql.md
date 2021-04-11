---
title: Szablony Azure Resource Manager dla Azure Cosmos DB Core (SQL API)
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: 7163658024d150a7c5d75c3b3ac0b6b6b29cd3cb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105037312"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule przedstawiono tylko przykłady Azure Resource Manager szablonów dla podstawowych kont interfejsu API (SQL). Możesz również znaleźć przykłady szablonów dla interfejsów API [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md)i [Table](templates-samples-table.md) .

## <a name="core-sql-api"></a>Interfejs API bazy danych Core (SQL)

|**Szablon**|**Opis**|
|---|---|
|[Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera z przepływności automatycznego skalowania](manage-with-templates.md#create-autoscale) | Ten szablon tworzy podstawowe konto interfejsu API (SQL) w dwóch regionach, bazę danych i kontener o przepływności skalowania automatycznego. |
|[Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera z magazynem analitycznym](manage-with-templates.md#create-analytical-store) | Ten szablon służy do tworzenia konta interfejsu API programu Core (SQL) w jednym regionie z kontenerem skonfigurowanym z włączonym wartością czasową TTL i opcją używania przepustowości ręcznej lub skalowania automatycznego. |
|[Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera ze standardową (ręczną) przepływności](manage-with-templates.md#create-manual) | Ten szablon służy do tworzenia konta interfejsu API Core (SQL) w dwóch regionach, bazy danych i kontenera ze standardową przepływność. |
|[Utwórz konto usługi Azure Cosmos, bazę danych i kontener przy użyciu procedury składowanej, wyzwalacza i UDF](manage-with-templates.md#create-sproc) | Ten szablon tworzy podstawowe konto interfejsu API (SQL) w dwóch regionach z procedurą składowaną, wyzwalaczem i formatem UDF dla kontenera. |
|[Utwórz konto usługi Azure Cosmos z tożsamością usługi Azure AD, definicjami ról i przypisaniem roli](manage-with-templates.md#create-rbac) | Ten szablon służy do tworzenia konta interfejsu API Core (SQL) z tożsamością usługi Azure AD, definicjami ról i przypisaniem roli w jednostce nazwy. |
|[Utwórz prywatny punkt końcowy dla istniejącego konta usługi Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ten szablon służy do tworzenia prywatnego punktu końcowego dla istniejącego konta interfejsu API usługi Azure Cosmos Core (SQL) w istniejącej sieci wirtualnej. |
|[Tworzenie bezpłatnego konta usługi Azure Cosmos](manage-with-templates.md#free-tier) |  Ten szablon służy do tworzenia konta interfejsu API programu Azure Cosmos DB Core (SQL) w warstwie Bezpłatna. |

Aby uzyskać dokumentację referencyjną, zobacz sekcję [Azure Resource Manager Reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .
