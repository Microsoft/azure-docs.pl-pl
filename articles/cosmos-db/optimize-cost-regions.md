---
title: Optymalizacja kosztów wdrożeń wieloregionowych w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zarządzać kosztami wdrożeń wieloregionowych w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8d98c9a7e58f08d9ad63183805cd6cd0d2ab3b3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570195"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów korzystania z wielu regionów w usłudze Azure Cosmos DB

W dowolnym momencie możesz dodawać i usuwać regiony do konta usługi Azure Cosmos. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos jest zarezerwowana w każdym regionie skojarzonym z Twoim kontem. Jeśli przepustowość zainicjowana na godzinę jest sumą jednostek RU/s skonfigurowanych dla wszystkich baz danych i kontenerów dla konta usługi Azure Cosmos, `T` a liczba regionów platformy Azure skojarzonych z kontem bazy danych to `N` , łączna przepływność przyznanych dla konta Cosmos dla danej godziny jest równa:

1. `T x N RU/s` Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane z jednym regionem zapisu. 

1. `T x (N+1) RU/s` Jeśli Twoje konto usługi Azure Cosmos jest skonfigurowane ze wszystkimi regionami, które mogą przetwarzać operacje zapisu. 

Aprowizowana przepływność z pojedynczym regionem zapisu kosztuje 0,008 USD/godz. na każde 100 RU/s, natomiast aprowizowana przepływność z wieloma regionami zapisu kosztuje 0,016 USD/godz. na każde 100 RU/s. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)Azure Cosmos DB.

## <a name="costs-for-multiple-write-regions"></a>Koszt wielu regionów zapisu

W systemie zapisów w wielu regionach sieć dostępna jednostek ru dla operacji zapisu zwiększa się, `N` gdzie `N` jest liczbą regionów zapisu. W przeciwieństwie do zapisu w pojedynczym regionie, każdy region jest teraz zapisywalny i powinien obsługiwać rozwiązywanie konfliktów. Zwiększona ilość obciążeń dla autorów. W punkcie planowania kosztów w celu przeprowadzenia `M` operacji zapisu na całym świecie należy udostępnić `RUs` element M na poziomie kontenera lub bazy danych. Następnie można dodać dowolną liczbę regionów i użyć ich do zapisu w celu przeprowadzenia operacji `M` zapisu ru na całym świecie. 

### <a name="example"></a>Przykład

Rozważmy, że masz kontener w regionie zachodnie stany USA o przepływności 10 000 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony — Wschodnie stany USA, Europa Północna i Azja Wschodnia, z których każdy ma ten sam magazyn i przepływność, i chcesz mieć możliwość zapisu w kontenerach we wszystkich czterech regionach z aplikacji rozproszonej globalnie. Łączny rachunek miesięczny (przy założeniu 31 dni) w miesiącu jest następujący:

|**Element**|**Użycie (co miesiąc)**|**Częstotliwość**|**Koszt miesięczny**|
|----|----|----|----|
|Opłaty za przepływność dla kontenera w regionie zachodnie stany USA (wiele regionów zapisu) |10 000 jednostek RU/s * 24 * 31 |$0,016 za 100 RU/s na godzinę |$1 190,40 |
|Rachunek przepływności dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia (wiele regionów zapisu) |(3 + 1) * 10 000 jednostek RU/s * 24 * 31 |$0,016 za 100 RU/s na godzinę |$4 761,60 |
|Magazyn dla kontenera w regionie zachodnie stany USA |1 TB (lub 1 024 GB) |$0,25/GB |$256 |
|Rachunek za magazyn dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia |3 * 1 TB (lub 3 072 GB) |$0,25/GB |$768 |
|**Łącznie**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Zwiększenie wykorzystania przepływności na podstawie poszczególnych regionów

Jeśli masz niewydajne użycie, na przykład co najmniej jeden obszar lub nadmiernie wykorzystywanych regionów, możesz wykonać następujące kroki, aby zwiększyć wykorzystanie przepływności:  

1. Upewnij się, że w regionie zapisu została najpierw zoptymalizowana przepływność (jednostek ru), a następnie ustaw maksymalne użycie jednostek RU w regionach odczytu przy użyciu źródła zmian z regionu do odczytu itp. 

2. Wiele regionów zapisu odczytuje i zapisuje dane można skalować we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. 

3. Monitoruj działanie w regionach i możesz dodawać i usuwać regiony na żądanie, aby skalować przepływność odczytu i zapisu.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)

