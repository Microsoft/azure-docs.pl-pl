---
title: Praca z kontami Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia i używania kont usługi Azure Cosmos. Pokazuje również hierarchię elementów na koncie usługi Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/09/2020
ms.reviewer: sngun
ms.openlocfilehash: d01164552b1f3f10f592cedf80eafe57566bf09c
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661078"
---
# <a name="work-with-azure-cosmos-account"></a>Praca z kontem usługi Azure Cosmos

Azure Cosmos DB to w pełni zarządzana platforma jako usługa (PaaS). Aby rozpocząć korzystanie z Azure Cosmos DB, należy najpierw utworzyć konto usługi Azure Cosmos w ramach subskrypcji platformy Azure. Twoje konto usługi Azure Cosmos zawiera unikatową nazwę DNS, a konto można zarządzać przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub przy użyciu różnych zestawów SDK specyficznych dla języka. Aby uzyskać więcej informacji, zobacz [jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md).

Konto usługi Azure Cosmos jest podstawową jednostką dystrybucji globalnej i wysokiej dostępności. Aby globalnie dystrybuować dane i przepływność w wielu regionach platformy Azure, możesz w dowolnym momencie dodawać i usuwać regiony platformy Azure do konta usługi Azure Cosmos. Konto usługi Azure Cosmos można skonfigurować tak, aby miało jeden lub wiele regionów zapisu. Aby uzyskać więcej informacji, zobacz [temat jak dodawać i usuwać regiony platformy Azure na koncie usługi Azure Cosmos](how-to-manage-database-account.md). [Domyślny poziom spójności](consistency-levels.md) można skonfigurować na koncie usługi Azure Cosmos. Azure Cosmos DB zapewnia kompleksową przepływność obejmującą umowy SLA, opóźnienia w 99 percentylu, spójności i wysokiej dostępności. Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Aby bezpiecznie zarządzać dostępem do wszystkich danych w ramach konta usługi Azure Cosmos, możesz użyć [kluczy podstawowych](secure-access-to-data.md) skojarzonych z Twoim kontem. Aby dodatkowo zabezpieczyć dostęp do danych, możesz skonfigurować [punkt końcowy usługi sieci wirtualnej](vnet-service-endpoint.md) i [zaporę IP](firewall-support.md) na koncie usługi Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementy na koncie usługi Azure Cosmos

Usługa Azure Cosmos Container jest podstawową jednostką skalowalności. Można praktycznie mieć nieograniczoną przepływność (RU/s) i magazyn w kontenerze. Azure Cosmos DB przezroczyste partycjonowanie kontenera przy użyciu klucza partycji logicznej, który można określić w celu elastycznego skalowania zainicjowanej przepływności i magazynu. Aby uzyskać więcej informacji, zobacz [Praca z kontenerami i elementami usługi Azure Cosmos](databases-containers-items.md).

Obecnie można utworzyć maksymalnie 50 kont usługi Azure Cosmos w ramach subskrypcji platformy Azure (jest to limit elastyczny, który można zwiększyć za pośrednictwem żądania pomocy technicznej). Pojedyncze konto usługi Azure Cosmos może praktycznie zarządzać nieograniczoną ilością danych i elastyczną przepływność. Aby zarządzać danymi i elastyczną przepływność, można utworzyć co najmniej jedną bazę danych usługi Azure Cosmos na Twoim koncie i w ramach tej bazy danych. można utworzyć jeden lub więcej kontenerów. Na poniższej ilustracji przedstawiono hierarchię elementów na koncie usługi Azure Cosmos:

:::image type="content" source="./media/account-overview/hierarchy.png" alt-text="Hierarchia konta usługi Azure Cosmos" border="false":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać kontem usługi Azure Cosmos i innymi pojęciami:

* [Jak zarządzać kontem usługi Azure Cosmos](how-to-manage-database-account.md)
* [Dystrybucja globalna](distribute-data-globally.md)
* [Poziomy spójności](consistency-levels.md)
* [Praca z kontenerami i elementami platformy Azure Cosmos](databases-containers-items.md)
* [Punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos](vnet-service-endpoint.md)
* [IP-Zapora dla konta usługi Azure Cosmos](firewall-support.md)
* [Jak dodawać i usuwać regiony platformy Azure na koncie usługi Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
