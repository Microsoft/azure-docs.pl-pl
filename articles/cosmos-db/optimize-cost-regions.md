---
title: Optymalizacja kosztów wdrożeń wieloregionowych w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami wdrożeń wieloregionowych w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 010ca40f4f3aacd6353aecd150e944672cc09066
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097516"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów korzystania z wielu regionów w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W dowolnym momencie możesz dodawać i usuwać regiony do konta usługi Azure Cosmos. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos jest zarezerwowana w każdym regionie skojarzonym z Twoim kontem. Jeśli przepustowość zainicjowana na godzinę jest sumą jednostek RU/s skonfigurowanych dla wszystkich baz danych i kontenerów dla konta usługi Azure Cosmos, `T` a liczba regionów platformy Azure skojarzonych z kontem bazy danych to `N` , Łączna przepustowość dla konta Cosmos dla danej godziny jest równa `T x N RU/s` .

Aprowizowana przepływność z pojedynczym regionem zapisu kosztuje 0,008 USD/godz. na każde 100 RU/s, natomiast aprowizowana przepływność z wieloma regionami zapisu kosztuje 0,016 USD/godz. na każde 100 RU/s. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Koszt wielu regionów zapisu

W systemie zapisów w wielu regionach sieć dostępna jednostek ru dla operacji zapisu zwiększa się, `N` gdzie `N` jest liczbą regionów zapisu. W przeciwieństwie do zapisu w pojedynczym regionie, każdy region jest teraz zapisywalny i obsługuje Rozwiązywanie konfliktów. W punkcie planowania kosztów w celu przeprowadzenia `M` operacji zapisu na całym świecie należy udostępnić `RUs` element M na poziomie kontenera lub bazy danych. Następnie można dodać dowolną liczbę regionów i użyć ich do zapisu w celu przeprowadzenia operacji `M` zapisu ru na całym świecie.

### <a name="example"></a>Przykład

Rozważmy, że masz kontener w regionie zachodnie stany USA skonfigurowany do zapisu w jednym regionie, Zainicjowano obsługę przepływności 10 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz region, Wschodnie stany USA, z tym samym magazynem i przepływność, co pozwoli na zapisanie w kontenerach w obu regionach z poziomu aplikacji. Łączny rachunek miesięczny (przy założeniu 31 dni) w miesiącu jest następujący:

|**Element**|**Użycie (co miesiąc)**|**Częstotliwość**|**Koszt miesięczny**|
|----|----|----|----|
|Rachunek przepływności dla kontenera w regionie zachodnie stany USA (pojedyncze regiony zapisu) |10 000 jednostek RU/s * 24 godziny * 31 dni |$0,008 za 100 RU/s na godzinę |$584,06 |
|Rachunek przepływności dla kontenera w 2 regionach — zachodnie stany USA & Wschodnie stany USA (wiele regionów zapisu) |2 * 10 tys. jednostek RU/s * 24 godziny * 31 dni|$0,016 za 100 RU/s na godzinę |$2 336,26 |
|Magazyn dla kontenera w regionie zachodnie stany USA |1 TB (lub 1 024 GB) |$0,25/GB |$256 |
|Rachunek magazynu dla 2 regionów — zachodnie stany USA & Wschodnie stany USA |2 * 1 TB (lub 3 072 GB) |$0,25/GB |$768 |

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