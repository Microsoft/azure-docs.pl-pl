---
title: Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB
description: Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193262"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB

W poniższych tabelach uwzględniono linki do Azure Resource Manager szablonów dla Azure Cosmos DB:

## <a name="sql-core-api"></a>Interfejs API SQL (podstawowy)

|**Szablon**|**Opis**|
|---|---|
|[Tworzenie konta, bazy danych, kontenera usługi Azure Cosmos](manage-sql-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API programu SQL (rdzeń) w dwóch regionach z dwoma kontenerami z przepływem danych udostępnionej usługi Database i kontenerem o dedykowanej przepływności. Przepływność można zaktualizować przez ponowne przesłanie szablonu ze zaktualizowaną wartością właściwości przepływności. |
|[Utwórz konto usługi Azure Cosmos, bazę danych i kontener przy użyciu procedury składowanej, wyzwalacza i UDF](manage-sql-with-resource-manager.md#create-sproc) | Ten szablon służy do tworzenia konta interfejsu API SQL (rdzeń) w dwóch regionach z procedurą składowaną, wyzwalaczem i formatem UDF dla kontenera. |
|[Utwórz prywatny punkt końcowy dla istniejącego konta usługi Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Ten szablon służy do tworzenia prywatnego punktu końcowego dla istniejącego konta interfejsu API usługi Azure Cosmos w istniejącej sieci wirtualnej. |
|[Tworzenie bezpłatnego konta usługi Azure Cosmos](manage-sql-with-resource-manager.md#free-tier) |  Ten szablon służy do tworzenia konta Azure Cosmos DB w ramach warstwy Bezpłatna. |

## <a name="mongodb-api"></a>Interfejs API usługi MongoDB

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, kolekcję](manage-mongodb-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta przy użyciu interfejsu API Azure Cosmos DB dla MongoDB w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa kontenery, które współużytkują przepływność na poziomie bazy danych. |

## <a name="cassandra-api"></a>Interfejs API rozwiązania Cassandra

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, przestrzeni kluczy, tabeli](manage-cassandra-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejs API Cassandra w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwie tabele, które współdzielą przepływność na poziomie przestrzeni kluczy. |

## <a name="gremlin-api"></a>Interfejs API języka Gremlin

|**Szablon**|**Opis**|
|---| ---|
|[Utwórz konto usługi Azure Cosmos, bazę danych, wykres](manage-gremlin-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejsu API Gremlin w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało dwa wykresy, które współdzielą przepływność na poziomie bazy danych. |

## <a name="table-api"></a>Interfejs API tabel

|**Szablon**|**Opis**|
|---| ---|
|[Tworzenie konta usługi Azure Cosmos, tabela](manage-table-with-resource-manager.md#create-resource) | Ten szablon służy do tworzenia konta interfejs API tabel w dwóch regionach z włączoną obsługą wielu wzorców. Konto usługi Azure Cosmos będzie miało jedną tabelę. |

> [!TIP]
> Aby włączyć współdzieloną przepływność przy korzystaniu z interfejs API tabel, należy włączyć przepływność na poziomie konta w witrynie Azure Portal.

Aby uzyskać dokumentację referencyjną, zobacz sekcję [Azure Resource Manager Reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .
