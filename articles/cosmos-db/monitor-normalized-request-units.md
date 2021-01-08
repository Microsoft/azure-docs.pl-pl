---
title: Monitorowanie znormalizowanych jednostek RU/s dla kontenera usługi Azure Cosmos lub konta
description: Dowiedz się, jak monitorować znormalizowane użycie jednostek żądań dla operacji w Azure Cosmos DB. Właściciele konta Azure Cosmos DB mogą zrozumieć, które operacje zużywają więcej jednostek żądań.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 01/07/2021
ms.openlocfilehash: ec82532b54e7834b62fcc03d3ee7de1345a0f546
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027812"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Jak monitorować znormalizowane Elementy RU/s dla kontenera usługi Azure Cosmos lub konta
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor dla Azure Cosmos DB zawiera widok metryk do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki Azure Cosmos DB są zbierane domyślnie. Ta funkcja nie wymaga jawnie włączania ani konfigurowania niczego.

**Znormalizowana Metryka zużycia ru** jest używana do sprawdzenia, jak dobrze nasycone zakresy kluczy partycji są związane z ruchem. Azure Cosmos DB dystrybuuje przepływność równomiernie we wszystkich zakresach kluczy partycji. Ta Metryka zawiera widok na sekundę maksymalnego wykorzystania przepływności dla zakresu kluczy partycji. Użyj tej metryki, aby obliczyć użycie RU/s między zakresem kluczy partycji dla danego kontenera. Korzystając z tej metryki, jeśli widzisz wysoki procent użycia jednostek żądań dla wszystkich zakresów kluczy partycji w usłudze Azure monitor, należy zwiększyć przepływność, aby spełniała potrzeby obciążenia. Przykład — znormalizowane użycie jest zdefiniowane jako maksymalne użycie RU/s we wszystkich zakresach kluczy partycji. Załóżmy na przykład, że maksymalna przepływność wynosi 20 000 RU/s, a istnieją dwa zakresy kluczy partycji, P_1 i P_2, z których każdy może skalować do 10 000 RU/s. W danym drugim, jeśli P_1 użył 6000 jednostek ru i P_2 8000 jednostek ru, znormalizowanym wykorzystaniem jest MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Czego można oczekiwać, a jeśli znormalizowana RU/s jest wyższa

Gdy znormalizowane użycie RU/s osiągnie 100% dla danego zakresu kluczy partycji, a klient nadal wysyła żądania w tym przedziale czasowym z 1 sekund do tego zakresu kluczy partycji, otrzymuje błąd z ograniczoną szybkością. Klient powinien przestrzegać sugerowanego czasu oczekiwania i ponowić próbę żądania. Dzięki zestawowi SDK można łatwo obsługiwać tę sytuację przez ponowną próbę skonfigurowaną w odpowiednim czasie.  Nie jest konieczne, aby zobaczyć błąd ograniczania liczby jednostek RU tylko w przypadku, gdy znormalizowany RU osiągnął 100%. Wynika to z faktu, że znormalizowany RU jest pojedynczą wartością, która reprezentuje maksymalne użycie przez wszystkie zakresy kluczy partycji, jeden zakres kluczy partycji może być zajęty, ale inne zakresy kluczy partycji mogą obsłużyć żądania bez problemów. Na przykład pojedyncza operacja, taka jak procedura składowana, która zużywa wszystkie RU/s w zakresie kluczy partycji, będzie prowadzić do krótkiego wzrostu w znormalizowanym zużyciu RU/s. W takich przypadkach nie będzie żadnych natychmiastowego ograniczania liczby błędów, jeśli częstotliwość żądań jest niska lub żądania są wysyłane do innych partycji w różnych zakresach kluczy partycji. 

Metryki Azure Monitor ułatwiają znajdowanie operacji według kodu stanu dla interfejsu API SQL za pomocą metryk **całkowitej liczby żądań** . Później można filtrować te żądania według kodu stanu 429 i dzielić je według **typu operacji**.  

Aby znaleźć żądania, które są ograniczone, zalecanym sposobem jest uzyskanie tych informacji za pomocą dzienników diagnostycznych.

W przypadku ciągłego szczytu zużycia RU/s przez 100% lub blisko 100% między wieloma zakresami kluczy partycji zaleca się zwiększenie przepływności. Możesz dowiedzieć się, które operacje są duże i szczytowe użycie, korzystając z metryk usługi Azure monitor i dzienników diagnostycznych usługi Azure monitor.

Podsumowując, **znormalizowana Metryka zużycia ru** jest używana do sprawdzenia, który zakres kluczy partycji jest bardziej rozgrzany w warunkach użytkowania. Dzięki temu można obchylić przepływność do zakresu kluczy partycji. Aby uzyskać informacje na temat tego, które klucze partycji logicznej są gorącą częścią użycia, można wyświetlić dziennik **PartitionKeyRUConsumption** w dziennikach Azure monitor. Spowoduje to zmianę w wyborze klucza partycji lub zmianę logiki aplikacji. Aby rozwiązać ograniczenie szybkości, należy rozpowszechnić obciążenie danymi na wielu partycjach lub po prostu zwiększyć przepływność, gdy jest to wymagane. 

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Wyświetlanie znormalizowanej metryki użycia jednostki żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie, a następnie wybierz pozycję **metryki**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Okienko metryki w Azure Monitor":::

3. W okienku **metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję** i **grupę zasobów**. W polu **Typ zasobu** wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Wybierz konto usługi Azure Cosmos, aby wyświetlić metryki":::

4. Następnie możesz wybrać metrykę z listy dostępnych metryk. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnień, dostępności, Cassandra i innych. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **znormalizowaną metrykę zużycia ru** i **maksymalną** wartość agregacji.

   Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Wybierz metrykę z Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtry dla znormalizowanego zużycia jednostek żądań

Można również filtrować metryki i wykres wyświetlany przez określoną **CollectionName**, **DatabaseName**, **PartitionKeyRangeID** i **region**. Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **CollectionName** i odpowiednią wartość. Na wykresie są następnie wyświetlane znormalizowane jednostki zużycia RU używane dla kontenera w wybranym okresie.  

Metryki można grupować przy użyciu opcji **Zastosuj dzielenie** . W przypadku wspólnych baz danych przepływności znormalizowana Metryka RU przedstawia dane tylko z dokładnością do bazy danych, nie wyświetla żadnych danych na kolekcję. W przypadku udostępnionej bazy danych przepływności nie będą wyświetlane żadne dane w przypadku zastosowania podziału według nazwy kolekcji.

Dla każdego kontenera zostanie wyświetlona znormalizowana Metryka użycia jednostki żądania, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Zastosuj filtry do znormalizowanej metryki użycia jednostki żądania":::

## <a name="next-steps"></a>Następne kroki

* Monitoruj Azure Cosmos DB dane przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure.
* [Inspekcja operacji Azure Cosmos DB kontroli płaszczyzny](audit-control-plane-logs.md)
