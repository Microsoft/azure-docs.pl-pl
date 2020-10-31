---
title: Monitoruj użycie przepływności operacji w Azure Cosmos DB
description: Dowiedz się, jak monitorować przepływność i użycie jednostek żądań operacji w Azure Cosmos DB. Właściciele konta Azure Cosmos DB mogą zrozumieć, które operacje zajmują więcej jednostek żądań.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098250"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Jak monitorować przepływność lub zażądać użycia jednostek operacji w Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor dla Azure Cosmos DB zawiera widok metryk do monitorowania konta i tworzenia pulpitów nawigacyjnych. Metryki Azure Cosmos DB są zbierane domyślnie. Ta funkcja nie wymaga jawnie włączania ani konfigurowania niczego. Metryka **łączna liczba jednostek żądań** służy do uzyskiwania użycia jednostek żądań dla różnych typów operacji. Później można analizować, których operacji używa większość przepływności. Domyślnie dane przepływności są agregowane w przedziale czasu jednej minuty. Można jednak zmienić jednostkę agregacji, zmieniając opcję stopnia szczegółowości czasu.

Istnieją dwa sposoby analizowania danych użycia jednostki żądania:

* W danym przedziale czasu operacje zajmują więcej jednostek żądań.
* Które operacje ogólnie przeważają w obciążeniu, zużywając więcej jednostek żądania.
Ta analiza umożliwia skoncentrowanie się na operacjach, takich jak INSERT, upsert i spójrz na ich indeksowanie. Możesz sprawdzić, czy w obszarze indeksowania określone pola i zmodyfikować [zasady indeksowania](index-policy.md#include-exclude-paths) , aby uwzględnić lub wykluczyć ścieżki.

Jeśli zauważysz, że niektóre zapytania zajmują więcej jednostek żądań, możesz wykonać następujące czynności:

* Rozważ ponowne zażądanie, jeśli żądasz odpowiedniej ilości danych.
* Zmodyfikuj zapytanie tak, aby używało indeksu z klauzulą Filter.
* Wykonywanie mniej kosztownych wywołań funkcji UDF.
* Zdefiniuj klucze partycji, aby zminimalizować wentylator poza zapytania do różnych partycji.
* Aby dowiedzieć się więcej na temat wykonywania zapytań, można również użyć metryk kwerendy zwracanych w odpowiedzi na wywołanie, szczegóły dziennika diagnostycznego i zapoznaj się z artykułem dotyczącym [dostrajania wydajności zapytań](sql-api-query-metrics.md) .
* Możesz zacząć od sum, a następnie przyjrzeć się średniemu wykorzystaniu przy użyciu odpowiedniego wymiaru.

## <a name="view-the-total-request-unit-usage-metric"></a>Wyświetl metrykę użycia całkowitej jednostki żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie i wybierz pozycję **metryki** .

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Okienko metryki w Azure Monitor":::

1. W okienku **metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję** i **grupę zasobów** . W polu **Typ zasobu** wybierz pozycję **konta Azure Cosmos DB** , wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj** .

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Okienko metryki w Azure Monitor":::

1. Następnie wybierz metrykę **łączna liczba jednostek żądań** z listy dostępnych metryk. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **sumę jednostek żądań** i **średnią** jako wartość agregacji. Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć średnią liczbę jednostek żądań zużytych na minutę.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Okienko metryki w Azure Monitor":::

## <a name="filters-for-request-unit-usage"></a>Filtry dotyczące użycia jednostek żądania

Można również filtrować metryki i uzyskiwać wykresy wyświetlane przez konkretną **CollectionName** , **DatabaseName** , **OperationType** , **region** , **status** i **StatusCode** . Opcje **Dodaj filtr** i **Zastosuj podział** umożliwiają filtrowanie użycia jednostek żądań i grupowanie metryk.

Aby uzyskać użycie jednostki żądania dla każdej operacji według sum (sum) lub średniej, wybierz pozycję **Zastosuj podział** i wybierz pozycję **Typ operacji** oraz wartość filtru, jak pokazano na poniższej ilustracji:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Okienko metryki w Azure Monitor":::

Jeśli chcesz zobaczyć użycie jednostki żądania według kolekcji, wybierz pozycję **Zastosuj podział** i wybierz nazwę kolekcji jako filtr. Zobaczysz czat podobny do poniższego, który umożliwia wybranie kolekcji na pulpicie nawigacyjnym. Następnie możesz wybrać określoną nazwę kolekcji, aby wyświetlić więcej szczegółów:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Okienko metryki w Azure Monitor":::

## <a name="next-steps"></a>Następne kroki

* Monitoruj Azure Cosmos DB dane przy użyciu [ustawień diagnostycznych](cosmosdb-monitor-resource-logs.md) na platformie Azure.
* [Inspekcja operacji Azure Cosmos DB kontroli płaszczyzny](audit-control-plane-logs.md)
