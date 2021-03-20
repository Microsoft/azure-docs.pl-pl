---
title: Jak monitorować opóźnienia po stronie serwera dla operacji w Azure Cosmos DB
description: Dowiedz się, jak monitorować opóźnienia serwera w przypadku operacji w ramach konta Azure Cosmos DB lub kontenera. Właściciele konta Azure Cosmos DB mogą zrozumieć problemy opóźnienia po stronie serwera z kontami usługi Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: ec6a9db63504958640137fcd0fcfc904eb01afa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93074733"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Jak monitorować opóźnienia po stronie serwera dla operacji w kontenerze Azure Cosmos DB lub koncie
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor dla Azure Cosmos DB zawiera widok metryk do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki Azure Cosmos DB są zbierane domyślnie. Ta funkcja nie wymaga jawnie włączania ani konfigurowania niczego. Metryka opóźnienia po stronie serwera służy do wyświetlania opóźnienia operacji po stronie serwera. Azure Cosmos DB oferuje umowę SLA mniejszą niż 10 ms dla operacji odczytu/zapisu w punkcie z bezpośrednią łącznością. Na potrzeby operacji odczytu i zapisu umowy SLA są obliczane zgodnie z opisem w [dokumencie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Możesz monitorować opóźnienia po stronie serwera, jeśli widzisz nienormalnie wysokie opóźnienia dla operacji punktowej, takie jak:

* Operacja GET lub SET z kluczem partycji i IDENTYFIKATORem w trybie bezpośredniej łączności
* Operacja odczytu lub zapisu lub
* Zapytanie

Można wyszukać dziennik diagnostyczny, aby zobaczyć rozmiar zwracanych danych. Jeśli widzisz duże opóźnienie dla operacji zapytania, należy wyszukać dziennik diagnostyczny pod kątem wyższej [przepływności lub używanych jednostek ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) . Opóźnienie po stronie serwera przedstawia ilość czasu poświęcanego na infrastrukturę zaplecza przed zwróceniem danych do klienta. Ważne jest, aby przyjrzeć się tej metryki, aby wykluczyć wszelkie problemy z opóźnieniami zaplecza.

## <a name="view-the-server-side-latency-metric"></a>Wyświetl metrykę opóźnienia po stronie serwera

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie i wybierz pozycję **metryki**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Okienko metryki w Azure Monitor":::

1. W okienku **metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję** i **grupę zasobów**. W polu **Typ zasobu** wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Wybierz konto Azure Cosmos DB, aby wyświetlić metryki":::

1. Następnie wybierz metrykę **opóźnienia po stronie serwera**  z listy dostępnych metryk. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **opóźnienie po stronie serwera** i **średnią** jako wartość agregacji. Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć opóźnienia po stronie serwera na minutę.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Wybierz Server-Side metrykę opóźnienia z Azure Portal":::

## <a name="filters-for-server-side-latency"></a>Filtry dla opóźnień po stronie serwera

Można również filtrować metryki i uzyskiwać wykresy wyświetlane przez określoną **CollectionName**, **connectionmode**, **DatabaseName**, **OperationType**, **region** i **PublicAPIType**. 

Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **PublicAPIType** , i wybierz wartość **SQL**. Dodaj inny filtr dla elementu **OperationType**. Na wykresie są następnie wyświetlane opóźnienia po stronie serwera dla różnych operacji w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach metryki operacji.

Metryki **opóźnienia po stronie serwera** dla każdej operacji są wyświetlane jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filtry dla metryk opóźnienia po stronie serwera":::

Metryki można także grupować przy użyciu opcji **Zastosuj dzielenie** .  

## <a name="next-steps"></a>Następne kroki

* Monitoruj Azure Cosmos DB dane przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure.
* [Inspekcja operacji Azure Cosmos DB kontroli płaszczyzny](audit-control-plane-logs.md)
