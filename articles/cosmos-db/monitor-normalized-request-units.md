---
title: Monitorowanie znormalizowanych jednostek RU/s dla kontenera usługi Azure Cosmos lub konta
description: Dowiedz się, jak monitorować znormalizowane użycie jednostek żądań dla operacji w Azure Cosmos DB. Właściciele konta Azure Cosmos DB mogą zrozumieć, które operacje zużywają więcej jednostek żądań.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118816"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Jak monitorować znormalizowane Elementy RU/s dla kontenera usługi Azure Cosmos lub konta

Azure Monitor dla Azure Cosmos DB zawiera widok metryk do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki Azure Cosmos DB są zbierane domyślnie. Ta funkcja nie wymaga jawnie włączania ani konfigurowania niczego.

**Znormalizowana Metryka zużycia ru** jest używana do sprawdzenia, jak dobrze jest zastosowana replika WRT do użycia jednostek żądań w ramach zakresów kluczy partycji. Azure Cosmos DB dystrybuuje przepływność równomiernie na wszystkie partycje fizyczne. Ta Metryka zawiera widok na sekundę maksymalnego wykorzystania przepływności w zestawie replik. Korzystając z tej metryki, jeśli widzisz wysoki procent wykorzystania jednostek żądań, należy zwiększyć przepływność w celu spełnienia wymagań związanych z obciążeniem.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Czego można oczekiwać, a jeśli znormalizowana RU/s jest wyższa

Gdy znormalizowane użycie RU/s osiągnie 100%, klient otrzymuje błędy ograniczające szybkość. Klient powinien przestrzegać czasu oczekiwania i ponowić próbę. Jeśli istnieje Krótki skok, który osiągnie użycie 100%, oznacza to, że przepływność w replice osiągnęła maksymalny limit wydajności. Na przykład pojedyncza operacja, taka jak procedura składowana, która zużywa wszystkie jednostki RU/s w replice, będzie prowadzić do krótkotrwałego wzrostu w znormalizowanym zużyciu RU/s. W takich przypadkach nie będzie żadnych natychmiastowego ograniczania liczby błędów, jeśli częstotliwość żądań jest niska. Wynika to z tego, że Azure Cosmos DB zezwala na opłaty za żądania, które nie są obsługiwane w przypadku określonego żądania i innych żądań w tym okresie, są ograniczone.

Metryki Azure Monitor ułatwiają znajdowanie operacji według kodu stanu przy użyciu metryki **łączna liczba żądań** . Później można filtrować te żądania według kodu stanu 429 i dzielić je według **typu operacji**.

Aby znaleźć żądania, które są ograniczone, zalecanym sposobem jest uzyskanie tych informacji za pomocą dzienników diagnostycznych.

W przypadku ciągłego szczytu zużycia RU/s przez 100% lub blisko 100% zaleca się zwiększenie przepływności. Korzystając z metryk usługi Azure monitor i dzienników usługi Azure monitor, można dowiedzieć się, które operacje są duże i ich użycie szczytowe.

**Znormalizowana Metryka zużycia ru** jest również używana do sprawdzenia, który zakres kluczy partycji jest bardziej rozgrzany w warunkach użytkowania. Dzięki temu możesz pochylić przepływność do zakresu kluczy partycji. Aby uzyskać informacje na temat tego, które klucze partycji logicznej są gorącą częścią użycia, można wyświetlić dziennik **PartitionKeyRUConsumption** w dziennikach Azure monitor.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Wyświetlanie znormalizowanej metryki użycia jednostki żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie, a następnie wybierz pozycję **metryki**.

   ![Okienko metryki w Azure Monitor](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. W okienku **metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję**i **grupę zasobów**. W polu **Typ zasobu**wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   ![Wybierz konto usługi Azure Cosmos, aby wyświetlić metryki](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. Następnie możesz wybrać metrykę z listy dostępnych metryk. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnień, dostępności, Cassandra i innych. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **znormalizowaną metrykę zużycia ru** i **maksymalną** wartość agregacji.

   Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres.

   ![Wybierz metrykę z Azure Portal](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtry dla znormalizowanego zużycia jednostek żądań

Można również filtrować metryki i wykres wyświetlany przez określoną **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**i **region**. Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **CollectionName** i odpowiednią wartość. Na wykresie są następnie wyświetlane znormalizowane jednostki zużycia RU używane dla kontenera w wybranym okresie.  

Metryki można grupować przy użyciu opcji **Zastosuj dzielenie** .  

Znormalizowana Metryka użycia jednostki żądania dla każdego kontenera jest wyświetlana jak pokazano na poniższej ilustracji:

![Zastosuj filtry do znormalizowanej metryki użycia jednostki żądania](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>Następne kroki

* Monitoruj Azure Cosmos DB dane przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure.
* [Inspekcja operacji Azure Cosmos DB kontroli płaszczyzny](audit-control-plane-logs.md)