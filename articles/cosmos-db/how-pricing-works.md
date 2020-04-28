---
title: Model cen Azure Cosmos DB
description: W tym artykule opisano model cen Azure Cosmos DB i sposób upraszczający zarządzanie kosztami i planowanie kosztów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75977536"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Model cen w usłudze Azure Cosmos DB 

Model cen usługi Azure Cosmos DB upraszcza zarządzanie kosztami i planowanie. W usłudze Azure Cosmos DB płacisz za aprowizowaną przepływność i ilość zużytego miejsca do magazynowania.

* **Nieobsługiwana przepływność**: zainicjowana przepływność (nazywana również przepływem zarezerwowanym) gwarantuje wysoką wydajność w dowolnej skali. Należy określić przepływność (RU/s), a Azure Cosmos DB przydzielenia zasobów wymaganych do zagwarantowania skonfigurowanej przepływności. Opłaty są naliczane godzinowo za maksymalną zainicjowaną przepływność przez daną godzinę.

   > [!NOTE]
   > Ponieważ dedykowany model przepływności przydzieli zasoby do swojego kontenera lub bazy danych, zostanie naliczona opłata za zainicjowaną przepływność, nawet jeśli nie będzie można uruchamiać żadnych obciążeń.

* **Magazyn zużyty**: jest naliczana stała stawka za łączną ilość pamięci masowej (GB) zużywaną na dane i indeksy dla danej godziny.

Obsługiwana przepływność, określona jako [jednostki żądań](request-units.md) na sekundę (ru/s), umożliwia odczytywanie i zapisywanie danych w kontenerach lub bazach danych. Przepustowość można [zainicjować przy użyciu bazy danych lub kontenera](set-throughput.md). W zależności od potrzeb związanych z obciążeniem można skalować przepustowość w górę/w dół w dowolnym momencie. Ceny Azure Cosmos DB są elastyczne i są proporcjonalne do przepływności skonfigurowanej w bazie danych lub kontenerze. Minimalne wartości przepływności i magazynu oraz przyrosty skali zapewniają pełen zakres cen i spektrum elastyczności dla wszystkich segmentów klientów — od małych skali do kontenerów o dużej skali. Każda baza danych lub kontener jest rozliczany godzinowo na podstawie przepustowości w jednostkach 100 RU/s, z co najmniej 400 RU/s i magazyn zużywany w GB. W przeciwieństwie do przepływności, magazyn jest rozliczany na podstawie zużycia. Oznacza to, że nie trzeba rezerwować żadnego magazynu z wyprzedzeniem. Opłaty są naliczane tylko za zużyty magazyn.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) i [poznanie Azure Cosmos DB rachunku](understand-your-bill.md).

Model cen w Azure Cosmos DB jest spójny dla wszystkich interfejsów API. Aby dowiedzieć się więcej, zobacz [jak model cen Azure Cosmos DB jest ekonomiczny dla klientów](total-cost-ownership.md). W przypadku bazy danych lub kontenera należy określić minimalną przepływność, aby upewnić się, że umowy SLA i zwiększyć lub zmniejszyć przepływność zainicjowaną przez $6 dla każdego 100 RU/s.

Obecnie minimalna cena zarówno dla bazy danych, jak i przepływności opartej na kontenerze wynosi $24 miesięcznie (zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) , aby uzyskać najnowsze informacje. Jeśli obciążenie używa wielu kontenerów, można je zoptymalizować pod kątem kosztów przy użyciu przepływności na poziomie bazy danych, ponieważ przepływność na poziomie bazy danych umożliwia korzystanie z dowolnej liczby kontenerów w bazie danych udostępniającej przepływność między kontenerami. Poniższa tabela zawiera podsumowanie przepływności i kosztów dla różnych jednostek:

|**Jednostka**  | **Minimalny koszt & przepływności** |**Przyrosty skalowania & kosztu** |**Zakres aprowizacji** |
|---------|---------|---------|-------|
|baza danych    | 400 RU/s (24/Miesiąc)    | 100 RU/s ($ 6/miesiąc)   |Przepływność jest zarezerwowana dla bazy danych i jest współdzielona przez kontenery w ramach bazy danych |
|Kontener     | 400 RU/s (24/Miesiąc)    | 100 RU/s ($ 6/miesiąc)  |Przepływność jest zarezerwowana dla określonego kontenera |

Jak pokazano w poprzedniej tabeli, minimalna przepływność w Azure Cosmos DB zaczyna się od ceny $24 miesięcznie. W przypadku rozpoczęcia od minimalnej przepływności i skalowania w górę w czasie w celu zapewnienia obsługi obciążeń produkcyjnych Twoje koszty zostaną zakłócone w przyrostach wynoszących $6/miesiąc. Model cen w Azure Cosmos DB jest elastyczny i ma płynny wzrost lub spadek ceny podczas skalowania w górę lub w dół.

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie 

Azure Cosmos DB oferuje dla deweloperów kilka opcji bezpłatnych. Dostępne są następujące opcje:

* **Bezpłatne konto platformy Azure**: platforma Azure oferuje [bezpłatną warstwę](https://azure.microsoft.com/free/) , która daje $200 na środki na korzystanie z platformy Azure za pierwsze 30 dni i ograniczoną liczbę bezpłatnych usług przez 12 miesięcy. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB jest częścią bezpłatnego konta platformy Azure. W odniesieniu do Azure Cosmos DB to bezpłatne konto oferuje 5 GB pamięci masowej i 400 jednostek ru przepływności dla całego roku. 

* **Wypróbuj bezpłatnie usługę Azure Cosmos DB**: Azure Cosmos DB oferuje ograniczony czas środowisko, korzystając z funkcji Wypróbuj Azure Cosmos DB w przypadku bezpłatnych kont. Możesz utworzyć konto Azure Cosmos DB, utworzyć bazę danych i kolekcje oraz uruchomić przykładową aplikację przy użyciu przewodników Szybki Start i samouczków. Możesz uruchomić przykładową aplikację bez subskrybowania konta platformy Azure lub korzystania z karty kredytowej. [Wypróbuj Azure Cosmos DB bezpłatnych](https://azure.microsoft.com/try/cosmosdb/) ofert Azure Cosmos DB przez jeden miesiąc, z możliwością odnowienia konta przez dowolną liczbę razy.

* **Emulator Azure Cosmos DB**: Azure Cosmos DB emulator udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów deweloperskich. Emulator jest oferowany bezpłatnie i z wysoką dokładnością do usługi w chmurze. Korzystając z emulatora Azure Cosmos DB, możesz tworzyć i testować aplikacje lokalnie, bez tworzenia subskrypcji platformy Azure ani ponoszenia kosztów. Aplikacje można opracowywać przy użyciu emulatora lokalnie przed przejściem do środowiska produkcyjnego. Po spełnieniu funkcjonalności aplikacji względem emulatora możesz przełączyć się do korzystania z konta Azure Cosmos DB w chmurze i znacząco zaoszczędzić koszt. Więcej informacji na temat emulatora można znaleźć w temacie [Using Azure Cosmos DB for Development and test](local-emulator.md) article (Aby uzyskać więcej informacji).

## <a name="pricing-with-reserved-capacity"></a>Cennik z zarezerwowaną pojemnością

Azure Cosmos DB [zarezerwowana pojemność](cosmos-db-reserved-capacity.md) pomaga zaoszczędzić pieniądze dzięki przedpłaceniu za zasoby Azure Cosmos DB przez jeden rok lub trzy lata. Możesz znacząco obniżyć koszty dzięki rocznym lub trzecim zobowiązaniom z góry, a następnie zaoszczędzić od 20-65% rabatów w porównaniu z regularnymi cenami. Azure Cosmos DB zarezerwowana pojemność pomaga obniżyć koszty dzięki wstępnej płatności za zainicjowaną przepływność (RU/s) przez okres jednego roku lub trzech lat i uzyskać rabat dla alokowanej przepływności. 

Pojemność zarezerwowana zapewnia rabat na rachunkach i nie wpływa na stan czasu wykonywania zasobów usługi Azure Cosmos DB. Zarezerwowana pojemność jest spójna ze wszystkimi interfejsami API, takimi jak MongoDB, Cassandra, SQL, Gremlin i Azure Tables oraz wszystkie regiony na całym świecie. Możesz dowiedzieć się więcej o pojemności zarezerwowanych w [przedpłatach za zasoby Azure Cosmos DB z zastrzeżoną pojemnością](cosmos-db-reserved-capacity.md) i kupić zarezerwowaną pojemność z [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat optymalizowania kosztów zasobów Azure Cosmos DB można znaleźć w następujących artykułach:

* Dowiedz się więcej [na temat optymalizacji na potrzeby tworzenia i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont Cosmos](optimize-cost-regions.md)
* Dowiedz się więcej o [zarezerwowanej pojemności Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Informacje na temat [emulatora Azure Cosmos DB](local-emulator.md)
