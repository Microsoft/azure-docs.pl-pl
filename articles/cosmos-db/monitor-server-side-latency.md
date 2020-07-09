---
title: Jak monitorować opóźnienia po stronie serwera dla operacji w Azure Cosmos DB
description: Dowiedz się, jak monitorować opóźnienia serwera w przypadku operacji w ramach konta Azure Cosmos DB lub kontenera. Właściciele konta Azure Cosmos DB mogą zrozumieć problemy opóźnienia po stronie serwera z kontami usługi Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262705"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Jak monitorować opóźnienia po stronie serwera dla operacji w kontenerze Azure Cosmos DB lub koncie

Azure Monitor dla Azure Cosmos DB zawiera widok metryk do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki Azure Cosmos DB są zbierane domyślnie. Ta funkcja nie wymaga jawnie włączania ani konfigurowania niczego. Metryka opóźnienia po stronie serwera służy do wyświetlania opóźnienia operacji po stronie serwera. Azure Cosmos DB oferuje umowę SLA mniejszą niż 10 ms dla operacji odczytu/zapisu w punkcie z bezpośrednią łącznością. Na potrzeby operacji odczytu i zapisu umowy SLA są obliczane zgodnie z opisem w [dokumencie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Jeśli widzisz nietypowo duże opóźnienie dla operacji punktowych, takich jak:

* Operacja pobierania lub ustawiania z kluczem partycji i IDENTYFIKATORem w trybie bezpośrednim
* Operacja odczytu lub zapisu lub
* Zapytanie

Możesz wyszukać dziennik diagnostyczny, aby zobaczyć rozmiar zwracanych danych. Jeśli widzisz duże opóźnienie dla operacji zapytań, można wyszukać dziennik diagnostyczny dotyczący rozmiaru zwracanych danych, [przepływności lub](cosmosdb-monitor-resource-logs.md#diagnostic-queries) użycia jednostek ru lub liczby takich operacji w danym okresie. W ten sposób można debugować problemy opóźnienia po stronie serwera.

## <a name="view-the-server-side-latency-metric"></a>Wyświetl metrykę opóźnienia po stronie serwera

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie i wybierz pozycję **metryki**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Okienko metryki w Azure Monitor":::

1. W okienku **metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję**i **grupę zasobów**. W polu **Typ zasobu**wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Wybierz konto Azure Cosmos DB, aby wyświetlić metryki":::

1. Następnie wybierz metrykę **opóźnienia po stronie serwera** z listy dostępnych metryk. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **opóźnienie po stronie serwera** i **średnią** jako wartość agregacji. Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć opóźnienia po stronie serwera na minutę.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Wybierz metrykę opóźnienia po stronie serwera z Azure Portal":::

## <a name="filters-for-server-side-latency"></a>Filtry dla opóźnień po stronie serwera

Można również filtrować metryki i uzyskiwać wykresy wyświetlane przez określoną **CollectionName**, **connectionmode**, **DatabaseName**, **OperationType**, **region**i **PublicAPIType**. 

Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **PublicAPIType** , i wybierz wartość **SQL**. Dodaj inny filtr dla elementu **OperationType**. Na wykresie są następnie wyświetlane opóźnienia po stronie serwera dla różnych operacji w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach metryki operacji.

Metryki **opóźnienia po stronie serwera** dla każdej operacji są wyświetlane jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filtry dla metryk opóźnienia po stronie serwera":::

Metryki można także grupować przy użyciu opcji **Zastosuj dzielenie** .  

## <a name="next-steps"></a>Następne kroki

* Monitoruj Azure Cosmos DB dane przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure.
* [Inspekcja operacji Azure Cosmos DB kontroli płaszczyzny](audit-control-plane-logs.md)
