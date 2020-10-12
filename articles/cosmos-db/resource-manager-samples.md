---
title: Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83586154"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB

W poniższych tabelach uwzględniono linki do Azure Resource Manager szablonów dla Azure Cosmos DB:

## <a name="core-sql-api"></a>Interfejs API bazy danych Core (SQL)

|**Szablon**|**Opis**|
|---|---|
|[Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera z przepływności automatycznego skalowania](manage-sql-with-resource-manager.md#create-autoscale) | Ten szablon tworzy podstawowe konto interfejsu API (SQL) w dwóch regionach, bazę danych i kontener o przepływności skalowania automatycznego. |
|[Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera z magazynem analitycznym](manage-sql-with-resource-manager.md#create-analytical-store) | Ten szablon służy do tworzenia konta interfejsu API programu Core (SQL) w jednym regionie z kontenerem skonfigurowanym z włączonym wartością czasową TTL i opcją używania przepustowości ręcznej lub skalowania automatycznego. |
|[Tworzenie konta usługi Azure Cosmos, bazy danych i kontenera ze standardową (ręczną) przepływności](manage-sql-with-resource-manager.md#create-manual) | Ten szablon służy do tworzenia konta interfejsu API Core (SQL) w dwóch regionach, bazy danych i kontenera ze standardową przepływność. |
|[Utwórz konto usługi Azure Cosmos, bazę danych i kontener przy użyciu procedury składowanej, wyzwalacza i UDF](manage-sql-with-resource-manager.md#create-sproc) | Ten szablon tworzy podstawowe konto interfejsu API (SQL) w dwóch regionach z procedurą składowaną, wyzwalaczem i formatem UDF dla kontenera. |
|[Utwórz prywatny punkt końcowy dla istniejącego konta usługi Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ten szablon służy do tworzenia prywatnego punktu końcowego dla istniejącego konta interfejsu API usługi Azure Cosmos Core (SQL) w istniejącej sieci wirtualnej. |
|[Tworzenie bezpłatnego konta usługi Azure Cosmos](manage-sql-with-resource-manager.md#free-tier) |  Ten szablon służy do tworzenia konta interfejsu API programu Azure Cosmos DB Core (SQL) w warstwie Bezpłatna. |

## <a name="mongodb-api"></a>Interfejs API usługi MongoDB

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych i kolekcję o przepływności automatycznego skalowania](manage-mongodb-with-resource-manager.md#create-autoscale) | Ten szablon służy do tworzenia konta przy użyciu interfejsu API Azure Cosmos DB MongoDB w dwóch regionach z dwoma kontenerami, które współużytkują przepływność automatycznego skalowania na poziomie bazy danych. |
|[Utwórz konto usługi Azure Cosmos, bazę danych, kolekcję z standardową (ręczną) przepływności](manage-mongodb-with-resource-manager.md#create-manual) | Ten szablon służy do tworzenia konta przy użyciu interfejsu API Azure Cosmos DB MongoDB w dwóch regionach z dwoma kontenerami, które współużytkują standardową przepływność na poziomie bazy danych. |

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, przestrzeń kluczy, tabelę z przepływem automatycznego skalowania](manage-cassandra-with-resource-manager.md#create-autoscale) | Ten szablon służy do tworzenia konta interfejs API Cassandra w dwóch regionach z przestrzenią kluczy i tabelą o przepływności skalowania automatycznego. |
|[Utwórz konto usługi Azure Cosmos, przestrzeń kluczy, tabelę z standardową (ręczną) przepływności](manage-cassandra-with-resource-manager.md#create-manual) | Ten szablon służy do tworzenia konta interfejs API Cassandra w dwóch regionach z przestrzenią kluczy i tabelą z ręczną przepływność. |

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, Graf o przepływności automatycznego skalowania](manage-gremlin-with-resource-manager.md#create-autoscale) | Ten szablon służy do tworzenia konta interfejsu API Gremlin w dwóch regionach z bazą danych i wykresem o przepływności skalowania automatycznego. |
|[Utwórz konto usługi Azure Cosmos, bazę danych programu Graph z standardową (ręczną) przepływności](manage-gremlin-with-resource-manager.md#create-manual) | Ten szablon służy do tworzenia konta interfejsu API Gremlin w dwóch regionach z bazą danych i wykresem o standardowej przepływności. |

## <a name="table-api"></a>Interfejs API tabel

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, tabelę z przepływem automatycznego skalowania](manage-table-with-resource-manager.md#create-autoscale) | Ten szablon służy do tworzenia konta interfejs API tabel w dwóch regionach i pojedynczej tabeli o przepływności skalowania automatycznego. |
|[Utwórz konto usługi Azure Cosmos, tabelę z standardową (ręczną) przepływności](manage-table-with-resource-manager.md#create-manual) | Ten szablon służy do tworzenia konta interfejs API tabel w dwóch regionach i pojedynczej tabeli ze standardową przepływność. |

Aby uzyskać dokumentację referencyjną, zobacz sekcję [Azure Resource Manager Reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .
