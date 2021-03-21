---
title: Optymalizacja kosztów wdrożeń wieloregionowych w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami wdrożeń wieloregionowych w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459612"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów korzystania z wielu regionów w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W dowolnym momencie możesz dodawać i usuwać regiony do konta usługi Azure Cosmos. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos jest zarezerwowana w każdym regionie skojarzonym z Twoim kontem. Jeśli przepustowość zainicjowana na godzinę jest sumą jednostek RU/s skonfigurowanych dla wszystkich baz danych i kontenerów dla konta usługi Azure Cosmos, `T` a liczba regionów platformy Azure skojarzonych z kontem bazy danych to `N` , Łączna przepustowość dla konta Cosmos dla danej godziny jest równa `T x N RU/s` .

Aprowizowana przepływność z pojedynczym regionem zapisu kosztuje 0,008 USD/godz. na każde 100 RU/s, natomiast aprowizowana przepływność z wieloma regionami zapisu kosztuje 0,016 USD/godz. na każde 100 RU/s. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Koszt wielu regionów zapisu

W systemie zapisów w wielu regionach sieć dostępna jednostek ru dla operacji zapisu zwiększa się, `N` gdzie `N` jest liczbą regionów zapisu. W przeciwieństwie do zapisu w pojedynczym regionie, każdy region jest teraz zapisywalny i obsługuje Rozwiązywanie konfliktów. W punkcie planowania kosztów w celu przeprowadzenia `M` operacji zapisu na całym świecie należy udostępnić `RUs` element M na poziomie kontenera lub bazy danych. Następnie można dodać dowolną liczbę regionów i użyć ich do zapisu w celu przeprowadzenia operacji `M` zapisu ru na całym świecie.

### <a name="example"></a>Przykład

Należy wziąć pod uwagę, że masz kontener w regionie zachodnie stany USA skonfigurowany do zapisu w jednym regionie, ale Zainicjowano obsługę przepływności 10 jednostek RU/s, przechowując 0,5 TB danych w tym miesiącu. Załóżmy, że dodasz region, Wschodnie stany USA, korzystając z tego samego magazynu i przepływności, i chcesz, aby można było zapisywać w kontenerach w obu regionach aplikacji. Nowy łączny rachunek miesięczny (przy założeniu, że 730 godzin w miesiącu) będzie następujący:

|**Element**|**Użycie (co miesiąc)**|**Częstotliwość**|**Koszt miesięczny**|
|----|----|----|----|
|Rachunek przepływności dla kontenera w regionie zachodnie stany USA (pojedynczy region zapisu) |10 000 jednostek RU/s * 730 godz. |$0,008 za 100 RU/s na godzinę |$584 |
|Rachunek przepływności dla kontenera w 2 regionach — zachodnie stany USA & Wschodnie stany USA (wiele regionów zapisu) |2 * 10 tys. RU/s * 730 godz. |$0,016 za 100 RU/s na godzinę |$2 336 |
|Magazyn dla kontenera w regionie zachodnie stany USA |0,5 TB (lub 512 GB) |$0,25/GB |$128 |
|Rachunek za magazyn dla kontenera w 2 regionach — zachodnie stany USA & Wschodnie stany USA |2 * 0,5 TB (lub 1 024 GB) |$0,25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zwiększenie wykorzystania przepływności na podstawie poszczególnych regionów

Jeśli masz niewydajne użycie, na przykład co najmniej jeden region odczytu, można wykonać kroki, aby zwiększyć maksymalne użycie jednostek RU w regionach odczytu, używając źródła danych zmiany z regionu odczytu lub przenieść go do innego pomocniczego, jeśli jest nadmiernie wykorzystane. Należy upewnić się, że w regionie zapisu zostanie najpierw zoptymalizowana przepływność (jednostek ru). Zapisuje koszt więcej niż odczyty, chyba że bardzo duże zapytania, które utrzymują nawet użycie, mogą być trudne. Ogólnie, Monitoruj zużytą przepływność w regionach i dodawaj lub usuwaj regiony na żądanie, aby skalować przepływność odczytu i zapisu, a także Zadbaj o to, aby poznać wpływ na opóźnienia dla wszystkich aplikacji wdrożonych w tym samym regionie.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](./optimize-cost-reads-writes.md)
