---
title: Przesyłanie strumieniowe danych w usłudze Azure SQL Edge
description: Informacje na temat przesyłania strumieniowego danych w usłudze Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392116"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Przesyłanie strumieniowe danych w usłudze Azure SQL Edge

Usługa Azure SQL Edge oferuje natywną implementację funkcji przesyłania strumieniowego danych o nazwie Streaming-SQL. Umożliwia przesyłanie strumieniowe danych w czasie rzeczywistym, analizowanie i przetwarzanie zdarzeń, co pozwala analizować i przetwarzać duże ilości szybkiego przesyłania strumieniowego danych z wielu źródeł jednocześnie. Przesyłanie strumieniowe T-SQL jest kompilowane przy użyciu tego samego aparatu przesyłania strumieniowego o wysokiej wydajności, który umożliwia [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) w Microsoft Azure. Funkcja obsługuje podobny zestaw możliwości oferowanych przez Azure Stream Analytics uruchomionych na krawędzi.

Podobnie jak w przypadku Stream Analytics, przesyłanie strumieniowe T-SQL rozpoznaje wzorce i relacje w informacjach wyodrębnionych z wielu źródeł danych wejściowych IoT, w tym urządzeń, czujników i aplikacji. Można użyć tych wzorców do wyzwalania akcji i inicjowania przepływów pracy. Można na przykład utworzyć alerty, dostarczyć informacje do rozwiązania do raportowania lub wizualizacji lub przechowywać dane do późniejszego użycia. 

Przesyłanie strumieniowe T-SQL może pomóc:

* Analizuj strumienie telemetryczne w czasie rzeczywistym z urządzeń IoT.
* Użyj analizy danych w czasie rzeczywistym wygenerowanych na podstawie autonomicznych i bezsterowników pojazdów.
* Używaj zdalnego monitorowania i konserwacji predykcyjnej zasobów przemysłowych i produkcyjnych o wysokiej wartości.
* Korzystaj z wykrywania anomalii i rozpoznawania wzorców dla odczytów czujników IoT w rolnictwie lub w farmie energetycznej.

## <a name="how-does-t-sql-streaming-work"></a>Jak działa przesyłanie strumieniowe T-SQL?

Przesyłanie strumieniowe T-SQL działa w taki sam sposób jak [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work). Na przykład używa koncepcji *zadań* przesyłania strumieniowego do przetwarzania przesyłania strumieniowego danych w czasie rzeczywistym. 

Zadanie usługi Stream Analytics składa się z:

- **Wejście strumienia**: definiuje połączenia ze źródłem danych, z którego ma zostać odczytany strumień danych. Usługa Azure SQL Edge obecnie obsługuje następujące typy danych wejściowych strumienia:
    - Centrum brzegowe
    - Kafka (obsługa danych wejściowych Kafka jest obecnie dostępna tylko w wersjach Intel/AMD64 usługi Azure SQL Edge).

- **Dane wyjściowe strumienia**: definiuje połączenia ze źródłem danych, do którego ma zostać zapisany strumień danych. Usługa Azure SQL Edge obecnie obsługuje następujące typy danych wyjściowych strumienia
    - Centrum brzegowe
    - SQL (dane wyjściowe SQL mogą być lokalną bazą danych w wystąpieniu usługi Azure SQL Edge lub SQL Server zdalnego lub Azure SQL Database). 

- **Zapytanie strumienia**: definiuje transformację, agregacje, filtrowanie, sortowanie i sprzężenia, które mają być zastosowane do strumienia wejściowego, zanim zostanie on zapisany w danych wyjściowych strumienia. Zapytanie strumienia jest oparte na tym samym języku zapytań, jak używany przez Stream Analytics. Aby uzyskać więcej informacji, zobacz [Stream Analytics języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference).

> [!IMPORTANT]
> Przesyłanie strumieniowe T-SQL, w przeciwieństwie do Stream Analytics, nie obsługuje obecnie [korzystania z danych referencyjnych dla odnośników](../stream-analytics/stream-analytics-use-reference-data.md) lub [korzystania z funkcji UDF i uda w zadaniu strumienia](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Przesyłanie strumieniowe T-SQL obsługuje tylko podzestaw obszaru powierzchni języka obsługiwanego przez Stream Analytics. Aby uzyskać więcej informacji, zobacz [Stream Analytics języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference).

## <a name="limitations-and-restrictions"></a>Ograniczenia i ograniczenia

Poniższe ograniczenia i ograniczenia dotyczą przesyłania strumieniowego T-SQL. 

- Tylko jedno zadanie przesyłania strumieniowego może być aktywne w określonym czasie. Zadania, które są już uruchomione, muszą zostać zatrzymane przed uruchomieniem innego zadania.
- Każde wykonanie zadania przesyłania strumieniowego jest jednowątkowe. Jeśli zadanie przesyłania strumieniowego zawiera wiele zapytań, każde zapytanie jest oceniane w kolejności szeregowej.
- Po zatrzymaniu zadania przesyłania strumieniowego w usłudze Azure SQL Edge może wystąpić pewne opóźnienie, zanim będzie można uruchomić następne zadanie przesyłania strumieniowego. To opóźnienie jest wprowadzane, ponieważ podstawowy proces przesyłania strumieniowego musi zostać zatrzymany w odpowiedzi na żądanie zatrzymania zadania, a następnie ponownie uruchomiony w odpowiedzi na żądanie uruchomienia zadania. 
- Przesyłanie strumieniowe T-SQL do 32 partycji dla strumienia Kafka. Próba skonfigurowania większej liczby partycji spowoduje wystąpienie błędu. 

## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadania Stream Analytics w usłudze Azure SQL Edge ](create-stream-analytics-job.md)
- [Wyświetlanie metadanych skojarzonych z zadaniami przesyłania strumieniowego w usłudze Azure SQL Edge ](streaming-catalog-views.md)
- [Utwórz strumień zewnętrzny](create-external-stream-transact-sql.md)