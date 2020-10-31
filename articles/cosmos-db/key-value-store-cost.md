---
title: Opłata jednostkowa żądania dla Azure Cosmos DB jako magazyn wartości klucza
description: Dowiedz się więcej na temat opłat za jednostkę żądania Azure Cosmos DB w przypadku prostych operacji zapisu i odczytu, gdy są one używane jako magazyn klucz/wartość.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: cd80e98d0838cb06228c92c7ea7efeb3fc30a0ed
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082882"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB jako magazyn wartości klucza — przegląd kosztów
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB to globalnie dystrybuowana, wielomodelowa usługa bazy danych służąca do łatwego tworzenia aplikacji o wysokiej dostępności. Domyślnie Azure Cosmos DB automatycznie i efektywnie indeksuje wszystkie dane, które pobiera. Pozwala to na szybkie i spójne zapytania [SQL](./sql-query-getting-started.md) (i [JavaScript](stored-procedures-triggers-udfs.md)) dotyczące danych. 

W tym artykule opisano koszt Azure Cosmos DB prostych operacji zapisu i odczytu, gdy jest używany jako magazyn klucz/wartość. Operacje zapisu obejmują Wstawianie, zamienianie, usuwanie i upserts elementów danych. Oprócz zagwarantowania, że umowa SLA dostępności na 99,999% dla wszystkich kont wieloregionowych, Azure Cosmos DB oferuje gwarantowane <10 ms dla operacji odczytu i dla (indeksowane) zapisów w 99 percentylu. 

## <a name="why-we-use-request-units-rus"></a>Dlaczego używamy jednostek żądania (jednostek ru)

Wydajność Azure Cosmos DB zależy od ilości alokowanej przepływności wyrażonej w [jednostkach żądania](request-units.md) (ru/s). Inicjowanie obsługi jest na drugim poziomie szczegółowości i jest kupowane w RU/s ([nie należy mylić z godzinową płatnością](https://azure.microsoft.com/pricing/details/cosmos-db/)). Jednostek ru należy traktować jako logiczne abstrakcyjne (walutowe), które upraszczają obsługę wymaganej przepływności dla aplikacji. Użytkownicy nie muszą myśleć o rozróżnieniu między przepływem operacji odczytu i zapisu. Model pojedynczej waluty jednostek ru tworzy efektywność udostępniania udostępnionej pojemności między operacjami odczytu i zapisu. Ten model pojemności zainicjowanej pozwala usłudze zapewnić **przewidywalną i spójną przepływność, gwarantowane małe opóźnienia i wysoką dostępność** . Na koniec model RU jest używany do przedstawiania przepływności, a każdy zainicjowany RU ma również określoną ilość zasobów (np. pamięci, rdzeni/procesora i operacji we/wy na sekundę).

Jako globalnie dystrybuowany system bazy danych, Cosmos DB jest jedyną usługą platformy Azure, która zapewnia kompleksową umowy slaę obejmującą opóźnienia, przepływność, spójność i wysoką dostępność. Wybrana przepustowość jest stosowana do każdego regionu skojarzonego z Twoim kontem Cosmos. W przypadku operacji odczytu Cosmos DB oferuje wiele dobrze zdefiniowanych [poziomów spójności](consistency-levels.md) , które można wybrać. 

W poniższej tabeli przedstawiono liczbę jednostek ru wymaganych do wykonania operacji odczytu i zapisu na podstawie elementu danych o rozmiarze 1 KB i 100 artykułów bazy wiedzy z wyłączonym domyślnym automatycznym indeksem. 

|Rozmiar elementu|1 odczyt|1 zapis|
|-------------|------|-------|
|1 KB|1 RU|5 jednostek ru|
|100 KB|10 jednostek RU|50 jednostek ru|

## <a name="cost-of-reads-and-writes"></a>Koszt odczytu i zapisu

Jeśli zainicjujesz 1 000 RU/s, ta kwota jest równa 3 600 000 RU/godzina i będzie kosztować $0,08 za godzinę (w Stanach Zjednoczonych i Europie). W przypadku elementu danych o rozmiarze 1 KB oznacza to, że można korzystać z 3 600 000 odczytów lub 720 000 zapisów (3 600 000 RU/5) przy użyciu zainicjowanej przepływności. Znormalizowany do miliona odczytów i zapisów, koszty byłyby $0,022/mln od odczytu ($0,08/3,6) i $0.111/mln zapisów ($0,08/0,72). Koszt na milion jest minimalny, jak pokazano w poniższej tabeli.

|Rozmiar elementu|Koszt operacji odczytu 1 000 000|Koszt zapisu 1 000 000|
|-------------|-------|--------|
|1 KB|$0,022|$0,111|
|100 KB|$0,222|$1,111|


Większość podstawowych obiektów blob lub obiektów przechowuje opłaty $0,40 za milion odczytanych transakcji i $5 na milion transakcji zapisu. Jeśli jest używana optymalnie, Cosmos DB może być do 98% tańsze niż te inne rozwiązania (dla transakcji 1 KB).

## <a name="next-steps"></a>Następne kroki

* Użyj [kalkulatora ru](https://cosmos.azure.com/capacitycalculator/) , aby oszacować przepływność dla obciążeń.