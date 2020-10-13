---
title: Model cen Azure Cosmos DB
description: W tym artykule opisano model cen Azure Cosmos DB i sposób upraszczający zarządzanie kosztami i planowanie kosztów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: a992d240955f42ec030a84c887ba086ce92f9790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605255"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Model cen w usłudze Azure Cosmos DB

Model cen usługi Azure Cosmos DB upraszcza zarządzanie kosztami i planowanie. Dzięki Azure Cosmos DB płacisz za operacje wykonywane w bazie danych i w przypadku magazynu zużywanego przez dane.

- **Operacje bazy danych**: sposób naliczania opłat za operacje bazy danych zależy od typu konta usługi Azure Cosmos, którego używasz.

  - **Nieobsługiwana przepływność**: [zainicjowana przepływność](set-throughput.md) (nazywana również przepływem zarezerwowanym) gwarantuje wysoką wydajność w dowolnej skali. Należy określić przepływność, która jest wymagana w [jednostkach żądań](request-units.md) na sekundę (ru/s), a Azure Cosmos DB przydzielenia zasobów wymaganych do zagwarantowania skonfigurowanej przepływności. Przepustowość można [zainicjować przy użyciu bazy danych lub kontenera](set-throughput.md). W zależności od potrzeb związanych z obciążeniem można skalować przepustowość w górę/w dół w dowolnym momencie lub użyć funkcji [automatycznego skalowania](provision-throughput-autoscale.md) (chociaż w bazie danych lub kontenerze jest wymagana minimalna przepływność, która gwarantuje umowy SLA). Opłaty są naliczane godzinowo za maksymalną zainicjowaną przepływność przez daną godzinę.

   > [!NOTE]
   > Ze względu na to, że dedykowany model przepływności przydzieli zasoby do kontenera lub bazy danych, opłata zostanie naliczona za przepływność, która została zainicjowana, nawet jeśli nie uruchomiono żadnych obciążeń.

  - **Bezserwerowe**: w trybie [bezserwerowym](serverless.md) nie trzeba udostępniać żadnej przepływności podczas tworzenia zasobów na koncie usługi Azure Cosmos. Na koniec okresu rozliczeniowego opłaty są naliczane za liczbę jednostek żądania, które zostały zużyte przez operacje bazy danych.

- **Magazyn**: jest rozliczana stała stawka za łączną ilość miejsca w magazynie (w GB) zużywaną przez dane i indeksy w danej godzinie. Magazyn jest rozliczany na podstawie zużycia, więc nie trzeba zarezerwować z góry żadnych magazynów. Opłaty są naliczane tylko za zużyty magazyn.

Model cen w Azure Cosmos DB jest spójny dla wszystkich interfejsów API. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/), [opisującą Azure Cosmos DB rachunku](understand-your-bill.md) i [sposób, w jaki model cen Azure Cosmos DB jest ekonomiczny dla klientów](total-cost-ownership.md).

W przypadku wdrożenia konta Azure Cosmos DB w regionie nierządowym w Stanach Zjednoczonych istnieje minimalna cena zarówno dla bazy danych, jak i przepływności opartej na kontenerach w trybie przepływności. Nie ma minimalnej ceny w trybie bezserwerowym. Ceny różnią się w zależności od regionu, z którego korzystasz, aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="try-azure-cosmos-db-for-free"></a>Wypróbuj usługę Azure Cosmos DB bezpłatnie

Azure Cosmos DB oferuje dla deweloperów wiele opcji bezpłatnych. Dostępne są następujące opcje:

* **Azure Cosmos DB bezpłatna**: warstwa Bezpłatna Azure Cosmos DB ułatwia rozpoczęcie pracy, opracowywanie i testowanie aplikacji, a nawet uruchamianie małych obciążeń produkcyjnych. Po włączeniu warstwy Bezpłatna na koncie uzyskasz pierwsze 400 RU/s i 5 GB miejsca do magazynowania w ramach konta, przez okres istnienia konta. W ramach każdej subskrypcji platformy Azure można korzystać z maksymalnie jednego konta warstwy bezpłatnej i musi on być w trakcie tworzenia konta. Aby rozpocząć, [Utwórz nowe konto w Azure Portal z włączoną bezpłatną warstwą](create-cosmosdb-resources-portal.md) lub Użyj [szablonu ARM](manage-sql-with-resource-manager.md#free-tier).

* **Bezpłatne konto platformy Azure**: platforma Azure oferuje [bezpłatną warstwę](https://azure.microsoft.com/free/) , która daje $200 na środki na korzystanie z platformy Azure za pierwsze 30 dni i ograniczoną liczbę bezpłatnych usług przez 12 miesięcy. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB jest częścią bezpłatnego konta platformy Azure. W odniesieniu do Azure Cosmos DB to bezpłatne konto oferuje 5 GB miejsca do magazynowania i 400 RU/s zainicjowanej przepływności przez cały rok.

* **Wypróbuj bezpłatnie usługę Azure Cosmos DB**: Azure Cosmos DB oferuje ograniczony czas środowisko, korzystając z funkcji Wypróbuj Azure Cosmos DB w przypadku bezpłatnych kont. Możesz utworzyć konto Azure Cosmos DB, utworzyć bazę danych i kolekcje oraz uruchomić przykładową aplikację przy użyciu przewodników Szybki Start i samouczków. Możesz uruchomić przykładową aplikację bez subskrybowania konta platformy Azure lub korzystania z karty kredytowej. [Wypróbuj Azure Cosmos DB bezpłatnych](https://azure.microsoft.com/try/cosmosdb/) ofert Azure Cosmos DB przez jeden miesiąc, z możliwością odnowienia konta przez dowolną liczbę razy.

* **Emulator Azure Cosmos DB**: Azure Cosmos DB emulator udostępnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów deweloperskich. Emulator jest oferowany bezpłatnie i z wysoką dokładnością do usługi w chmurze. Korzystając z emulatora Azure Cosmos DB, możesz tworzyć i testować aplikacje lokalnie, bez tworzenia subskrypcji platformy Azure ani ponoszenia kosztów. Aplikacje można opracowywać przy użyciu emulatora lokalnie przed przejściem do środowiska produkcyjnego. Po spełnieniu funkcjonalności aplikacji względem emulatora możesz przełączyć się do korzystania z konta Azure Cosmos DB w chmurze i znacząco zaoszczędzić koszt. Więcej informacji na temat emulatora można znaleźć w temacie [Using Azure Cosmos DB for Development and test](local-emulator.md) article (Aby uzyskać więcej informacji).

## <a name="pricing-with-reserved-capacity"></a>Cennik z zarezerwowaną pojemnością

Azure Cosmos DB [zarezerwowana pojemność](cosmos-db-reserved-capacity.md) pomaga zaoszczędzić pieniądze w przypadku korzystania z trybu przepływności z obsługą administracyjną przez przedpłatę za zasoby Azure Cosmos DB przez jeden rok lub trzy lata. Możesz znacząco obniżyć koszty dzięki rocznym lub trzecim zobowiązaniom z góry, a następnie zaoszczędzić od 20-65% rabatów w porównaniu z regularnymi cenami. Azure Cosmos DB zarezerwowana pojemność pomaga obniżyć koszty dzięki wstępnej płatności za zainicjowaną przepływność (RU/s) przez okres jednego roku lub trzech lat i uzyskać rabat dla alokowanej przepływności. 

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
