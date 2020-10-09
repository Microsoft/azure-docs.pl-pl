---
title: Globalna dystrybucja usługi Azure Cosmos DB
description: Dowiedz się, jak globalnie replikować dane za pomocą usługi Azure Cosmos DB w witrynie Azure Portal
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: cb9899712fd023aaec41f4b6bb34357b60ecb106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75751641"
---
## <a name="add-global-database-regions-using-the-azure-portal"></a><a id="addregion"></a>Dodawanie regionów globalnej bazy danych przy użyciu witryny Azure Portal
Azure Cosmos DB jest dostępna we wszystkich [regionach świadczenia usługi Azure][azureregions] na całym świecie. Po wybraniu domyślnego poziomu spójności dla Twojego konta bazy danych możesz skojarzyć co najmniej jeden region (w zależności od wybranego domyślnego poziomu spójności i globalnych potrzeb dystrybucji).

1. W witrynie [Azure Portal](https://portal.azure.com/) na pasku po lewej stronie kliknij pozycję **Azure Cosmos DB**.
2. Na stronie usługi **Azure Cosmos DB** wybierz konto bazy danych do modyfikacji.
3. Na stronie konta kliknij w menu pozycję **Replikuj dane globalnie**.
4. Na stronie **Globalna replikacja danych** wybierz regiony do dodania lub usunięcia, klikając regiony na mapie, a następnie klikając przycisk **Zapisz**. Dodanie regionu nic nie kosztuje, zobacz [cennik](https://azure.microsoft.com/pricing/details/cosmos-db/) lub artykuł [Globalna dystrybucja danych za pomocą usługi Azure Cosmos DB](../articles/cosmos-db/distribute-data-globally.md), aby uzyskać więcej informacji.
   
    ![Kliknij regiony na mapie, aby je dodać lub usunąć][1]
    
Po dodaniu drugiego regionu w portalu na stronie **Globalna replikacja danych** zostanie udostępniona opcja **Ręczne przejście do trybu failover**. Ta opcja umożliwia testowanie procesu pracy awaryjnej lub zmianę podstawowego regionu zapisu. Po dodaniu trzeciego regionu na tej samej stronie zostanie udostępniona opcja **Priorytety trybu failover**, dzięki czemu możesz zmienić kolejność pracy awaryjnej dla odczytów.  

### <a name="selecting-global-database-regions"></a>Wybieranie regionów globalnej bazy danych
Istnieją dwa typowe scenariusze konfigurowania co najmniej dwóch regionów:

1. Zapewnienie użytkownikom końcowym dostępu z małym opóźnieniem niezależnie od tego, gdzie znajdują się na całym świecie
2. Dodawanie regionalnych odporności dla ciągłości prowadzenia działalności biznesowej i odzyskiwania po awarii (BCDR)

Aby zapewnić użytkownikom końcowym małe opóźnienia, zalecane jest wdrożenie zarówno aplikacji, jak i usługi Azure Cosmos DB w regionach, które odpowiadają lokalizacjom użytkowników aplikacji.

W przypadku BCDR zalecane jest dodawanie regionów na podstawie par regionów opisanych w artykule [Business continuity and disaster recovery (BCDR): Azure Paired Regions (Ciągłość działalności biznesowej i odzyskiwanie po awarii (BCDR): regiony sparowane platformy Azure)][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
