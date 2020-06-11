---
title: Przesyłanie strumieniowe danych w usłudze Azure SQL Edge (wersja zapoznawcza)
description: Informacje na temat przesyłania strumieniowego danych w usłudze Azure SQL Edge (wersja zapoznawcza).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 866c74fbdfcfcef7cbb7d6cddb360c4265a2f776
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669617"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Przesyłanie strumieniowe danych w usłudze Azure SQL Edge (wersja zapoznawcza)

Usługa Azure SQL Edge (wersja zapoznawcza) oferuje następujące opcje implementacji przesyłania strumieniowego danych: 

- Wdrażanie Azure Stream Analytics zadań brzegowych utworzonych na platformie Azure. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Stream Analytics zadań](deploy-dacpac.md).
- Używanie przesyłania strumieniowego T-SQL do tworzenia zadań przesyłania strumieniowego w usłudze Azure SQL Edge bez konieczności konfigurowania zadań przesyłania strumieniowego na platformie Azure. 

Chociaż można używać obu opcji do implementowania przesyłania strumieniowego danych w usłudze Azure SQL Edge, należy używać tylko jednego z nich. W przypadku korzystania z obu tych elementów mogą wystąpić sytuacje wyścigu, które wpływają na działanie operacji przesyłania strumieniowego danych.

Funkcja przesyłania strumieniowego T-SQL jest skoncentrowana na tym artykule. Umożliwia przesyłanie strumieniowe danych w czasie rzeczywistym, analizowanie i przetwarzanie zdarzeń, co pozwala analizować i przetwarzać duże ilości szybkiego przesyłania strumieniowego danych z wielu źródeł jednocześnie. Przesyłanie strumieniowe T-SQL jest kompilowane przy użyciu tego samego aparatu przesyłania strumieniowego o wysokiej wydajności, który umożliwia [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) w Microsoft Azure. Funkcja obsługuje podobny zestaw możliwości oferowanych przez Azure Stream Analytics uruchomionych na krawędzi.

Podobnie jak w przypadku Stream Analytics, przesyłanie strumieniowe T-SQL rozpoznaje wzorce i relacje w informacjach wyodrębnionych z wielu źródeł danych wejściowych IoT, w tym urządzeń, czujników i aplikacji. Można użyć tych wzorców do wyzwalania akcji i inicjowania przepływów pracy. Można na przykład utworzyć alerty, dostarczyć informacje do rozwiązania do raportowania lub wizualizacji lub przechowywać dane do późniejszego użycia. 

Przesyłanie strumieniowe T-SQL może pomóc:

* Analizuj strumienie telemetryczne w czasie rzeczywistym z urządzeń IoT.
* Użyj analizy danych w czasie rzeczywistym wygenerowanych na podstawie autonomicznych i bezsterowników pojazdów.
* Używaj zdalnego monitorowania i konserwacji predykcyjnej zasobów przemysłowych i produkcyjnych o wysokiej wartości.
* Korzystaj z wykrywania anomalii i rozpoznawania wzorców dla odczytów czujników IoT w rolnictwie lub w farmie energetycznej.

## <a name="how-does-t-sql-streaming-work"></a>Jak działa przesyłanie strumieniowe T-SQL?

Przesyłanie strumieniowe T-SQL działa w taki sam sposób jak [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Na przykład używa koncepcji *zadań* przesyłania strumieniowego do przetwarzania przesyłania strumieniowego danych w czasie rzeczywistym. 

Zadanie usługi Stream Analytics składa się z:

- **Wejście strumienia**: definiuje połączenia ze źródłem danych, z którego ma zostać odczytany strumień danych. Usługa Azure SQL Edge obecnie obsługuje następujące typy danych wejściowych strumienia:
    - Centrum brzegowe
    - Kafka (obsługa danych wejściowych Kafka jest obecnie dostępna tylko w wersjach Intel/AMD64 usługi Azure SQL Edge).

- **Dane wyjściowe strumienia**: definiuje połączenia ze źródłem danych, do którego ma zostać zapisany strumień danych. Usługa Azure SQL Edge obecnie obsługuje następujące typy danych wyjściowych strumienia
    - Centrum brzegowe
    - SQL (dane wyjściowe SQL mogą być lokalną bazą danych w wystąpieniu usługi Azure SQL Edge lub SQL Server zdalnego lub Azure SQL Database). 
    - Azure Blob Storage

- **Zapytanie strumienia**: definiuje transformację, agregacje, filtrowanie, sortowanie i sprzężenia, które mają być zastosowane do strumienia wejściowego, zanim zostanie on zapisany w danych wyjściowych strumienia. Zapytanie strumienia jest oparte na tym samym języku zapytań, jak używany przez Stream Analytics. Aby uzyskać więcej informacji, zobacz [Stream Analytics języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> Przesyłanie strumieniowe T-SQL, w przeciwieństwie do Stream Analytics, nie obsługuje obecnie [korzystania z danych referencyjnych dla odnośników](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) lub [korzystania z funkcji UDF i uda w zadaniu strumienia](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Przesyłanie strumieniowe T-SQL obsługuje tylko podzestaw obszaru powierzchni języka obsługiwanego przez Stream Analytics. Aby uzyskać więcej informacji, zobacz [Stream Analytics języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Ograniczenia i ograniczenia

Poniższe ograniczenia i ograniczenia dotyczą przesyłania strumieniowego T-SQL. 

- Tylko jedno zadanie przesyłania strumieniowego może być aktywne w określonym czasie. Zadania, które są już uruchomione, muszą zostać zatrzymane przed uruchomieniem innego zadania.
- Każde wykonanie zadania przesyłania strumieniowego jest jednowątkowe. Jeśli zadanie przesyłania strumieniowego zawiera wiele zapytań, każde zapytanie jest oceniane w kolejności szeregowej.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadania Stream Analytics w usłudze Azure SQL Edge (wersja zapoznawcza)](create-stream-analytics-job.md)
- [Wyświetlanie metadanych skojarzonych z zadaniami przesyłania strumieniowego w usłudze Azure SQL Edge (wersja zapoznawcza)](streaming-catalog-views.md)
- [Utwórz strumień zewnętrzny](create-external-stream-transact-sql.md)