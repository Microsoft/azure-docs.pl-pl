---
title: Praca z obsługą kanału informacyjnego zmian w Azure Cosmos DB
description: Korzystanie z obsługi kanału informacyjnego Azure Cosmos DB zmiany w celu śledzenia zmian w dokumentach, przetwarzania opartego na zdarzeniach, takich jak wyzwalacze i utrzymywania aktualności pamięci podręcznych i systemów analitycznych
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c8c479050b434904de57397e5e7d73594090e031
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336135"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Zestawienie zmian w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Źródło zmian w Azure Cosmos DB jest trwałym rekordem zmian w kontenerze w kolejności ich występowania. Obsługa zestawienia zmian w usłudze Azure Cosmos DB działa przez nasłuchiwanie zmian w kontenerze usługi Azure Cosmos. Następnie tworzone są dane wyjściowe w postaci posortowanej listy zmienionych dokumentów w kolejności, w której zostały zmodyfikowane. Utrwalone zmiany mogą być przetwarzane asynchronicznie i przyrostowo, a dane wyjściowe mogą być dystrybuowane do jednego lub większej liczby użytkowników do przetwarzania równoległego.

Dowiedz się więcej o [wzorcach projektowych źródeł zmian](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Obsługiwane interfejsy API i zestawy SDK klienta

Ta funkcja jest obecnie obsługiwana przez następujące Azure Cosmos DB interfejsów API i zestawów SDK klienta.

| **Sterowniki klienta** | **INTERFEJS API SQL** | **Interfejs API Azure Cosmos DB dla Cassandra** | **Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB** | **Interfejs API języka Gremlin**|**Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Tak | Tak | Tak | Tak | Nie |
|Java|Tak|Tak|Tak|Tak|Nie|
|Python|Tak|Tak|Tak|Tak|Nie|
|Node/JS|Tak|Tak|Tak|Tak|Nie|

## <a name="change-feed-and-different-operations"></a>Zmień źródło danych i różne operacje

Dzisiaj zobaczysz wszystkie wstawienia i aktualizacje w kanale zmian. Nie można filtrować kanału informacyjnego zmiany dla określonego typu operacji. Jedną z możliwych alternatyw jest dodanie "miękkiego znacznika" w elemencie dla aktualizacji i filtru na podstawie tego, czy podczas przetwarzania elementów ze źródła zmian.

Obecnie Źródło zmian nie powoduje usunięcia dziennika. Podobnie jak w poprzednim przykładzie, można dodać znacznik elastyczny do elementów, które są usuwane. Na przykład, można dodać atrybut w elemencie o nazwie "usunięty" i ustawić go na "true" i ustawić wartość TTL dla elementu, tak aby można go było automatycznie usunąć. Możesz odczytać Źródło zmian dla elementów historycznych (Ostatnia zmiana odpowiadająca elementowi nie obejmuje zmian pośrednich), na przykład elementów, które zostały dodane pięć lat temu. Można odczytać kanał informacyjny zmiany w odniesieniu do miejsca pochodzenia kontenera, ale jeśli element zostanie usunięty, zostanie on usunięty ze źródła zmian.

### <a name="sort-order-of-items-in-change-feed"></a>Porządek sortowania elementów w strumieniu zmian

Zmiany elementów kanału informacyjnego są wprowadzane w kolejności ich modyfikacji. Ta kolejność sortowania jest gwarantowana na klucz partycji logicznej.

### <a name="consistency-level"></a>Poziom spójności

Podczas konsumowania źródła zmian na poziomie spójności ostatecznej może istnieć zduplikowane zdarzenie między kolejnymi operacjami odczytu kanału informacyjnego zmian (ostatnie zdarzenie jednej operacji odczytu pojawia się jako pierwsza z następnych).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Źródło zmian w wieloregionowych kontach usługi Azure Cosmos

W wieloregionowym koncie usługi Azure Cosmos, jeśli region zapisu zostanie przełączona w tryb failover, kanał informacyjny zmiany będzie działać w ramach operacji ręcznego przełączania awaryjnego i będzie ciągły.

### <a name="change-feed-and-time-to-live-ttl"></a>Zmień źródło danych i czas wygaśnięcia (TTL)

Jeśli właściwość czasu wygaśnięcia (Time to Live) jest ustawiona dla elementu na wartość-1, kanał informacyjny zmiany będzie trwały w nieskończoność. Jeśli dane nie zostaną usunięte, pozostanie w strumieniu zmian.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Zmień źródło danych i _etag, _lsn lub _ts

Format _etag jest wewnętrzny i nie powinien być zależny od niego, ponieważ może ulec zmianie w dowolnym momencie. _ts jest modyfikacją lub sygnaturą czasową utworzenia. Można użyć _ts do chronologicznego porównania. _lsn to identyfikator wsadu, który jest dodawany wyłącznie dla źródła zmian. reprezentuje identyfikator transakcji. Wiele elementów może mieć ten sam _lsn. Element ETag w FeedResponse różni się od _etag widocznej dla elementu. _etag jest identyfikatorem wewnętrznym i jest używany na potrzeby kontroli współbieżności. Właściwość _etag informuje o wersji elementu, natomiast Właściwość ETag służy do sekwencjonowania źródła danych.

## <a name="working-with-change-feed"></a>Praca ze źródłem zmian

Możesz współpracować ze źródłem zmian, korzystając z następujących opcji:

* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Używanie kanału informacyjnego zmiany z procesorem źródła zmian](change-feed-processor.md) 

Kanał informacyjny zmiany jest dostępny dla każdego klucza partycji logicznej w kontenerze i może być dystrybuowany przez jednego lub kilku odbiorców do przetwarzania równoległego, jak pokazano na poniższej ilustracji.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Rozproszone przetwarzanie Azure Cosmos DB źródła zmian" border="false":::

## <a name="features-of-change-feed"></a>Funkcje źródła zmian

* Kanał informacyjny zmiany jest domyślnie włączony dla wszystkich kont usługi Azure Cosmos.

* [Zainicjowanej przepływności](request-units.md) można użyć do odczytu ze źródła zmian, podobnie jak każda inna operacja Azure Cosmos DB, w dowolnym regionie skojarzonym z bazą danych Azure Cosmos.

* Kanał informacyjny zmiany zawiera operacje wstawiania i aktualizacji wykonywane do elementów w kontenerze. Można przechwytywać usunięcia, ustawiając flagę "unsoft-Delete" w obrębie elementów (na przykład dokumentów) zamiast usunięć. Alternatywnie można ustawić skończone okresy wygaśnięcia dla elementów z [możliwością czasu wygaśnięcia](time-to-live.md). Na przykład, 24 godziny i użyj wartości tej właściwości do przechwytywania usunięć. W tym rozwiązaniu należy przetwarzać zmiany w krótszym przedziale czasu niż okres ważności czasu wygaśnięcia.

* Każda zmiana elementu pojawia się dokładnie jeden raz w źródle zmian, a klienci muszą zarządzać logiką tworzenia punktów kontrolnych. Jeśli chcesz uniknąć złożoności zarządzania punktami kontrolnymi, procesor źródła zmian zapewnia automatyczne tworzenie punktów kontrolnych i semantykę "co najmniej raz". Zobacz [Używanie kanału informacyjnego zmiany z procesorem źródła zmian](change-feed-processor.md).

* Tylko Ostatnia zmiana dla danego elementu jest uwzględniona w dzienniku zmian. Zmiany pośrednie mogą być niedostępne.

* Kanał informacyjny zmiany jest sortowany według kolejności modyfikacji w ramach każdej wartości klucza partycji logicznej. Nie ma kolejności gwarantowanej w wartościach klucza partycji.

* Zmiany można synchronizować z dowolnego punktu w czasie, w którym nie ma stałego okresu przechowywania danych, dla którego zmiany są dostępne.

* Zmiany są dostępne równolegle dla wszystkich kluczy partycji logicznych kontenera usługi Azure Cosmos. Dzięki tej możliwości zmiany z dużych kontenerów mogą być przetwarzane równolegle przez wielu użytkowników.

* Aplikacje mogą jednocześnie zażądać wielu źródeł zmian w tym samym kontenerze. ChangeFeedOptions. StartTime może służyć do zapewnienia początkowego punktu początkowego. Na przykład, aby znaleźć token kontynuacji odpowiadający podanemu czasowi zegara. ContinuationToken, jeśli określony, ma pierwszeństwo przed wartościami StartTime i StartFromBeginning. Precyzja ChangeFeedOptions. StartTime to ~ 5 s.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Źródło zmian w interfejsach API dla Cassandra i MongoDB

Funkcja zmiany kanału informacyjnego jest oferowana jako strumień zmiany w interfejsie API MongoDB i zapytanie z predykatem w interfejs API Cassandra. Aby dowiedzieć się więcej na temat szczegółów implementacji interfejsu API MongoDB, zobacz [zmiana strumieni w interfejsie api Azure Cosmos DB dla MongoDB](mongodb-change-streams.md).

Natywny program Apache Cassandra udostępnia funkcję przechwytywania zmian danych (rerzucij), mechanizm do flagi określonych tabel do archiwizacji, a także odrzucanie zapisów w tych tabelach po osiągnięciu konfigurowalnego rozmiaru na dysku dla dziennika przechwytywania zmian. Funkcja zmiany kanału informacyjnego w interfejsie Azure Cosmos DB API for Cassandra zwiększa możliwość wykonywania zapytań o zmiany przy użyciu predykatu za pośrednictwem CQL. Aby dowiedzieć się więcej na temat szczegółów implementacji, zobacz temat [Zmiana kanału informacyjnego w interfejsie API Azure Cosmos DB Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Następne kroki

Teraz możesz dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Opcje odczytu źródła zmian](read-change-feed.md)
* [Używanie kanału informacyjnego zmiany z Azure Functions](change-feed-functions.md)
* [Korzystanie z procesora danych zmiany](change-feed-processor.md)
