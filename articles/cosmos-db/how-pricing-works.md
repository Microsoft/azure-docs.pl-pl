---
title: Model cen Azure Cosmos DB
description: W tym artykule opisano model cen Azure Cosmos DB i sposób upraszczający zarządzanie kosztami i planowanie kosztów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: d36b4fd433af716ebd97d88d05922d94bd74c309
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523540"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Model cen w usłudze Azure Cosmos DB

Model cen usługi Azure Cosmos DB upraszcza zarządzanie kosztami i planowanie. W usłudze Azure Cosmos DB płacisz za aprowizowaną przepływność i ilość zużytego miejsca do magazynowania.

* **Nieobsługiwana przepływność**: [zainicjowana przepływność](how-to-choose-offer.md) (nazywana również przepływem zarezerwowanym) gwarantuje wysoką wydajność w dowolnej skali. Należy określić przepływność (RU/s), a Azure Cosmos DB przydzielenia zasobów wymaganych do zagwarantowania skonfigurowanej przepływności. Opłaty są naliczane godzinowo za maksymalną zainicjowaną przepływność przez daną godzinę. Można ręcznie zainicjować obsługę przepływności lub użyć funkcji [automatycznego skalowania](provision-throughput-autoscale.md).

   > [!NOTE]
   > Ponieważ dedykowany model przepływności przydzieli zasoby do swojego kontenera lub bazy danych, zostanie naliczona opłata za zainicjowaną przepływność, nawet jeśli nie będzie można uruchamiać żadnych obciążeń.

* **Magazyn zużyty**: jest naliczana stała stawka za łączną ilość pamięci masowej (GB) zużywaną na dane i indeksy dla danej godziny.

Obsługiwana przepływność, określona jako [jednostki żądań](request-units.md) na sekundę (ru/s), umożliwia odczytywanie i zapisywanie danych w kontenerach lub bazach danych. Przepustowość można [zainicjować przy użyciu bazy danych lub kontenera](set-throughput.md). W zależności od potrzeb związanych z obciążeniem można skalować przepustowość w górę/w dół w dowolnym momencie. Ceny Azure Cosmos DB są elastyczne i są proporcjonalne do przepływności skonfigurowanej w bazie danych lub kontenerze. Minimalne wartości przepływności i magazynu oraz przyrosty skali zapewniają pełen zakres cen i spektrum elastyczności dla wszystkich segmentów klientów — od małych skali do kontenerów o dużej skali. Każda baza danych lub kontener jest rozliczany godzinowo na podstawie przepustowości w jednostkach 100 RU/s, z co najmniej 400 RU/s i magazyn zużywany w GB. W przeciwieństwie do przepływności, magazyn jest rozliczany na podstawie zużycia. Oznacza to, że nie trzeba rezerwować żadnego magazynu z wyprzedzeniem. Opłaty są naliczane tylko za zużyty magazyn.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) i [poznanie Azure Cosmos DB rachunku](understand-your-bill.md).

Model cen w Azure Cosmos DB jest spójny dla wszystkich interfejsów API. Aby dowiedzieć się więcej, zobacz [jak model cen Azure Cosmos DB jest ekonomiczny dla klientów](total-cost-ownership.md). W przypadku bazy danych lub kontenera należy określić minimalną przepływność, aby zapewnić umowy SLA i zwiększyć lub zmniejszyć zainicjowaną przepływność dla każdego 100 RU/s.

W przypadku wdrożenia konta Azure Cosmos DB w regionie nierządowym Stanów Zjednoczonych obecnie minimalna cena zarówno dla bazy danych, jak i przepływności opartej na kontenerze wynosi około 24 USD/miesiąc. Ceny różnią się w zależności od regionu, z którego korzystasz, aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Jeśli obciążenie używa wielu kontenerów, można je zoptymalizować pod kątem kosztów przy użyciu przepływności na poziomie bazy danych, ponieważ przepływność na poziomie bazy danych umożliwia korzystanie z dowolnej liczby kontenerów w bazie danych udostępniającej przepływność między kontenerami. Poniższa tabela zawiera podsumowanie przepływności i kosztów dla różnych jednostek:

|**Jednostka**  | **Minimalna przepływność** |**Przyrosty skalowania** |**Zakres aprowizacji** |
|---------|---------|---------|-------|
|baza danych    | 400 RU/s    | 100 RU/s   |Przepływność jest zarezerwowana dla bazy danych i jest współdzielona przez kontenery w ramach bazy danych |
|Kontener     | 400 RU/s   | 100 RU/s  |Przepływność jest zarezerwowana dla określonego kontenera |

Jak pokazano w poprzedniej tabeli, minimalną przepływność w Azure Cosmos DB zaczyna się od ceny wynoszącej około $24 miesięcznie. W przypadku rozpoczęcia od minimalnej przepływności i skalowania w górę w czasie w celu zapewnienia obsługi obciążeń produkcyjnych Twoje koszty zostaną zakłócone w przyrostach wynoszących około $6 miesięcznie. Ceny różnią się w zależności od regionu, z którego korzystasz, aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Model cen w Azure Cosmos DB jest elastyczny i ma płynny wzrost lub spadek ceny podczas skalowania w górę lub w dół.

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie

Azure Cosmos DB oferuje dla deweloperów wiele opcji bezpłatnych. Dostępne są następujące opcje:

* **Azure Cosmos DB bezpłatna**: warstwa Bezpłatna Azure Cosmos DB ułatwia rozpoczęcie pracy, opracowywanie i testowanie aplikacji, a nawet uruchamianie małych obciążeń produkcyjnych. Po włączeniu warstwy Bezpłatna na koncie uzyskasz pierwsze 400 RU/s i 5 GB miejsca do magazynowania w ramach konta, przez okres istnienia konta. W ramach każdej subskrypcji platformy Azure można korzystać z maksymalnie jednego konta warstwy bezpłatnej i musi on być w trakcie tworzenia konta. Aby rozpocząć, [Utwórz nowe konto w Azure Portal z włączoną bezpłatną warstwą](create-cosmosdb-resources-portal.md) lub Użyj [szablonu ARM](manage-sql-with-resource-manager.md#free-tier).

* **Bezpłatne konto platformy Azure**: platforma Azure oferuje [bezpłatną warstwę](https://azure.microsoft.com/free/) , która daje $200 na środki na korzystanie z platformy Azure za pierwsze 30 dni i ograniczoną liczbę bezpłatnych usług przez 12 miesięcy. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB jest częścią bezpłatnego konta platformy Azure. W odniesieniu do Azure Cosmos DB to bezpłatne konto oferuje 5 GB miejsca do magazynowania i 400 RU/s zainicjowanej przepływności przez cały rok.

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
