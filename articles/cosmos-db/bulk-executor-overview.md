---
title: Azure Cosmos DB bulk executor library overview (Omówienie biblioteki funkcji wykonawczej operacji zbiorczych w usłudze Azure Cosmos DB)
description: Wykonywanie operacji zbiorczych w Azure Cosmos DB za pomocą importu zbiorczego i interfejsów API aktualizacji zbiorczych oferowanych przez bibliotekę wykonawców zbiorczych.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 5d1251813486d4b7406f89ac9c09c49f985fa205
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055286"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB bulk executor library overview (Omówienie biblioteki funkcji wykonawczej operacji zbiorczych w usłudze Azure Cosmos DB)
 
Usługa Azure Cosmos DB to szybka, elastyczna i globalnie rozproszona baza danych zaprojektowana pod kątem elastycznego skalowania w poziomie i obsługi następujących funkcji: 

* Duża przepływność odczytu i zapisu (miliony operacji na sekundę).  
* Przechowywanie dużych ilości (setki terabajtów lub jeszcze więcej) danych transakcyjnych i operacyjnych przy przewidywalnym opóźnieniu liczonym w milisekundach.  

Biblioteka funkcji wykonawczej operacji zbiorczych ułatwia korzystanie z tej ogromnej przepływności i magazynu. Biblioteka funkcji wykonawczej operacji zbiorczych umożliwia wykonywanie operacji zbiorczych w usłudze Azure Cosmos DB za pośrednictwem interfejsów API importu zbiorczego i aktualizacji zbiorczej. Więcej informacji o funkcjach biblioteki funkcji wykonawczej operacji zbiorczych znajdziesz w poniższych sekcjach. 

> [!NOTE] 
> Obecnie zbiorcze biblioteki wykonawcze obsługują operacje importu i aktualizacji, a ta biblioteka jest obsługiwana przez Azure Cosmos DB tylko konta interfejsu API SQL i Gremlin.

> [!IMPORTANT]
> Biblioteka wykonawców zbiorczych nie jest obecnie obsługiwana w przypadku kont [bezserwerowych](serverless.md) . W przypadku platformy .NET zaleca się korzystanie z [pomocy technicznej zbiorczej](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) dostępnej w wersji v3 zestawu SDK.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Najważniejsze funkcje biblioteki wykonawców zbiorczej  
 
* Znacznie zmniejsza zasoby obliczeniowe po stronie klienta, które są niezbędne do nasycenia przepływności przydzieloną do kontenera. Jednowątkowa aplikacja, która zapisuje dane przy użyciu interfejsu API importu zbiorczego, uzyskuje 10 razy większą przepływność zapisu w porównaniu do aplikacji wielowątkowej, która zapisuje dane równolegle, jednocześnie obniżając jednocześnie procesor CPU komputera klienckiego.  

* Determinuje żmudnym zadania tworzenia logiki aplikacji w celu obsłużenia ograniczenia szybkości żądania, przekroczenia limitu czasu żądań i innych przejściowych wyjątków przez wydajne obsługiwanie ich w bibliotece.  

* Zapewnia uproszczony mechanizm dla aplikacji wykonujących operacje zbiorcze w celu skalowania w poziomie. Pojedyncze wystąpienie programu wykonującego zbiorczo uruchomione na maszynie wirtualnej platformy Azure może zużywać więcej niż 500 000 RU/s i osiągnąć wyższą stawkę przepływności, dodając dodatkowe wystąpienia na poszczególnych maszynach wirtualnych klienta.  
 
* Umożliwia zbiorcze Importowanie ponad terabajtów danych w ciągu godziny przy użyciu architektury skalowalnej w poziomie.  

* Można zbiorczo aktualizować istniejące dane w kontenerach usługi Azure Cosmos jako poprawki. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Jak działa moduł wykonujący zbiorczo? 

Gdy operacja zbiorcza do importowania lub aktualizowania dokumentów jest wyzwalana przy użyciu partii jednostek, są one początkowo przestają przedziałem, odpowiadające ich zakresowi kluczy partycji Azure Cosmos DB. W każdym zasobniku, który odnosi się do zakresu kluczy partycji, są one podzielone na dane typu mini-Batch, a każda z nich działa jako ładunek, który jest zatwierdzany po stronie serwera. Biblioteka wykonawców zbiorczych została wbudowana w optymalizacje w celu jednoczesnego wykonania tych miniowych partii zarówno w ramach zakresów kluczy partycji, jak i między nimi. Na poniższej ilustracji przedstawiono, jak programowy moduł wykonujący zbiorczo dane wsadowe w różnych kluczach partycji:  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="Architektura wykonawców zbiorczych" :::

Biblioteka wykonawców zbiorczych gwarantuje, że Maximally wykorzystać przepływność przydzieloną do kolekcji. Używa [mechanizmu kontroli przeciążenia w stylu AIMD](https://tools.ietf.org/html/rfc5681) dla każdego zakresu kluczy partycji Azure Cosmos DB, aby skutecznie obsłużyć ograniczenie szybkości i przekroczenia limitu czasu. 

## <a name="next-steps"></a>Następne kroki 
  
* Dowiedz się więcej, pobierając przykładowe aplikacje zużywające zbiorczą bibliotekę wykonawczą w programie [.NET i środowisku](bulk-executor-dot-net.md) [Java](bulk-executor-java.md).  
* Zapoznaj się z informacjami o zestawie SDK programu do wykonywania zbiorczego i informacje o wersji w językach [.NET](sql-api-sdk-bulk-executor-dot-net.md) i [Java](sql-api-sdk-bulk-executor-java.md).
* Biblioteka wykonawców zbiorczych jest zintegrowana z łącznikiem Cosmos DB Spark, aby dowiedzieć się więcej, zobacz Azure Cosmos DB artykuł dotyczący [łącznika Spark](spark-connector.md) .  
* Biblioteka wykonawców zbiorczych jest również zintegrowana z nową wersją [łącznika Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md) , aby Azure Data Factory do kopiowania danych.
