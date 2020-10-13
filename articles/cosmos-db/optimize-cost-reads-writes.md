---
title: Optymalizacja kosztów odczytu i zapisu w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zmniejszyć koszty Azure Cosmos DB podczas wykonywania operacji odczytu i zapisu danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: 38084bf30df2a597e7a7bc46ba4c52cf371c3c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318253"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optymalizuj operacje odczytu i zapisu w Azure Cosmos DB

W tym artykule opisano, jak koszt wymagany do odczytu i zapisu danych z Azure Cosmos DB jest obliczany. Operacje odczytu obejmują [odczyty punktów i zapytania](sql-query-getting-started.md). Operacje zapisu obejmują Wstawianie, zastępowanie, usuwanie i upsert elementów.  

## <a name="cost-of-reads-and-writes"></a>Koszt odczytu i zapisu

Azure Cosmos DB gwarantuje przewidywalną wydajność w zakresie przepływności i opóźnienia przy użyciu modelu przepływności aprowizacji. Obsługiwana przepływność jest reprezentowana w zakresie [jednostek żądań](request-units.md) na sekundę lub RU/s. Jednostka żądania (RU) to logiczne Abstrakcja zasobów obliczeniowych, takich jak procesor CPU, pamięć, we/wy itp., które są wymagane do wykonania żądania. Zainicjowana przepływność (jednostek ru) jest oddzielona i przeznaczona dla kontenera lub bazy danych, aby zapewnić przewidywalną przepływność i opóźnienia. Elastyczna przepływność umożliwia Azure Cosmos DB w celu zapewnienia przewidywalnej i spójnej wydajności, gwarantowanych małych opóźnień i wysokiej dostępności w dowolnej skali. Jednostki żądań reprezentują znormalizowaną walutę, która upraszcza powód, ile zasobów potrzebuje aplikacja.

Nie trzeba myśleć o rozróżnieniu jednostek żądań między operacjami odczytu i zapisu. Ujednolicony model waluty jednostek żądania tworzy efektywność w celu zapewnienia niezmienionego użycia tej samej przepustowości dla operacji odczytu i zapisu. W poniższej tabeli przedstawiono koszt odczytu punktów i zapisów w warunkach RU/s dla elementów, które mają rozmiar 1 KB i 100 KB.

|**Rozmiar elementu**  |**Koszt odczytu z jednego punktu** |**Koszt jednego zapisu**|
|---------|---------|---------|
|1 KB |1 RU |5 jednostek ru |
|100 KB |10 jednostek RU |50 jednostek ru |

Odczyt punktu dla elementu o rozmiarze 1 KB jest kosztem jednego RU. Pisanie elementu z 1 KB kosztami pięciu jednostek ru. Koszty odczytu i zapisu są stosowane w przypadku korzystania z domyślnego [poziomu spójności](consistency-levels.md)sesji.  Zagadnienia dotyczące jednostek ru obejmują: rozmiar elementu, liczba właściwości, spójność danych, indeksowane właściwości, indeksowanie i wzorce zapytań.

[Punkty odczytują](sql-query-getting-started.md) koszt znacznie mniej niż w przypadku zapytań. Odczyty punktów, w przeciwieństwie do zapytań, nie muszą używać aparatu zapytań do uzyskiwania dostępu do danych, mogą zaoszczędzić RU. Opłata za zapytanie RU jest zależna od złożoności zapytania i liczby elementów, które aparat kwerend potrzebował do załadowania.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optymalizowanie kosztów operacji zapisu i odczytu

Podczas wykonywania operacji zapisu należy zapewnić wystarczającą pojemność, aby obsługiwać liczbę operacji zapisu na sekundę. Można zwiększyć przepływność przy użyciu zestawu SDK, portalu, interfejsu wiersza polecenia przed wykonaniem operacji zapisu, a następnie zmniejszyć przepływność po zakończeniu zapisywania. Przepustowość dla okresu zapisu to minimalna przepływność wymagana dla danych oraz przepływność wymagana do wstawiania obciążeń przy założeniu, że żadne inne obciążenia nie są uruchomione.

Jeśli używasz innych obciążeń współbieżnie, na przykład zapytania/odczytu/aktualizacji/usuwania, należy dodać dodatkowe jednostki żądania wymagane dla tych operacji. Jeśli operacje zapisu są ograniczone proporcjonalnie, można dostosować zasady ponawiania/wycofywania przy użyciu zestawów SDK Azure Cosmos DB. Na przykład można zwiększyć obciążenie do momentu, aż mała częstotliwość żądań uzyska wartość rate-Limited. W przypadku wystąpienia limitu współczynnika aplikacja kliencka powinna wycofać się z żądań ograniczenia szybkości dla określonego interwału ponowień. Przed ponowną próbą zapisu należy mieć minimalny odstęp czasu między ponownymi próbami. Obsługa zasad ponawiania jest uwzględniana w zestawach SDK programu SQL .NET, Java, Node.js i Python oraz we wszystkich obsługiwanych wersjach zestawów SDK platformy .NET Core.

Można również zbiorczo wstawiać dane do Azure Cosmos DB lub kopiować dane ze wszystkich obsługiwanych źródłowych magazynów danych do Azure Cosmos DB przy użyciu [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory natywnie integruje się z Azure Cosmos DB zbiorczej interfejsu API w celu zapewnienia najlepszej wydajności podczas zapisywania danych.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
