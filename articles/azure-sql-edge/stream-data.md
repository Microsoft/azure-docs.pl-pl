---
title: Przesyłanie strumieniowe danych w usłudze Azure SQL Edge (wersja zapoznawcza)
description: Informacje na temat przesyłania strumieniowego danych w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597310"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Przesyłanie strumieniowe danych w usłudze Azure SQL Edge (wersja zapoznawcza)

Usługa Azure SQL Edge (wersja zapoznawcza) oferuje dwie różne opcje implementacji przesyłania strumieniowego danych. 

1. Wdrażanie zadań usługi Azure Stream Analytics Edge utworzonych na platformie Azure. Aby uzyskać więcej informacji na temat wdrażania zadań usługi Azure Stream Analytics w usłudze Azure SQL Edge, zobacz [wdrażanie Azure Stream Analytics zadań](deploy-dacpac.md).
2. Korzystanie z nowej funkcji **przesyłania strumieniowego T-SQL** do tworzenia zadań przesyłania strumieniowego w programie SQL Edge bez konieczności konfigurowania zadań przesyłania strumieniowego na platformie Azure. 

Chociaż możliwe jest użycie obu opcji w celu zaimplementowania przesyłania strumieniowego danych w programie SQL Edge, zdecydowanie zaleca się użycie tylko jednego. W przypadku korzystania z obu tych warunków mogą istnieć sytuacje wyścigu, które mają wpływ na działanie operacji przesyłania strumieniowego danych.

Pozostała część tego dokumentu odnosi się do nowej funkcji, **przesyłania strumieniowego T-SQL**, która zapewnia przesyłanie strumieniowe danych w czasie rzeczywistym, analizowanie i przetwarzanie zdarzeń w celu analizy i przetwarzania dużych ilości danych szybkiego przesyłania strumieniowego z wielu źródeł jednocześnie. *Przesyłanie strumieniowe T-SQL* jest tworzone przy użyciu tego samego aparatu przesyłania strumieniowego o wysokiej wydajności, który umożliwia [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) w Microsoft Azure, i obsługuje podobny zestaw możliwości oferowanych przez Azure Stream Analytics działający na brzegu.

Podobnie jak w przypadku Azure Stream Analytics, przesyłanie strumieniowe T-SQL umożliwia rozpoznawanie wzorców i relacji w informacjach wyodrębnionych z różnych źródeł danych wejściowych IoT, w tym urządzeń, czujników i aplikacji. Wzorce te mogą służyć do wyzwalania akcji i inicjowania przepływów pracy, takich jak tworzenie alertów, podawanie informacji do rozwiązania do raportowania lub wizualizacji lub przechowywanie danych do późniejszego użycia. 

Poniższe scenariusze są przykładami, w których można użyć przesyłania strumieniowego T-SQL:

* Analizuj strumienie telemetryczne w czasie rzeczywistym z urządzeń IoT.
* Analiza danych w czasie rzeczywistym wygenerowanych przez pojazdy autonomiczne i bezkierowcowe.
* Zdalne monitorowanie i konserwacja predykcyjna zasobów przemysłowych i produkcyjnych o wysokiej wartości.
* Wykrywania anomalii i/lub uznawania wzorców odczytów czujnika IoT w rolnictwie lub farmie energetycznej.

## <a name="how-does-t-sql-streaming-work"></a>Jak działa przesyłanie strumieniowe T-SQL?

Przesyłanie strumieniowe T-SQL działa w taki sam sposób jak [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work), na przykład używa koncepcji zadań przesyłania strumieniowego do przetwarzania przesyłania strumieniowego danych w czasie rzeczywistym. 

Zadanie usługi Stream Analytics składa się z:

- Wejście strumienia — wejście strumienia definiuje połączenia ze źródłem danych, z którego ma zostać odczytany strumień danych. Usługa Azure SQL Edge obecnie obsługuje następujące typy danych wejściowych strumienia:
    - Centrum brzegowe
    - Kafka — obsługa danych wejściowych Kafka jest obecnie dostępna tylko w wersjach Intel/AMD64 usługi Azure SQL Edge.

- Wyjście strumienia — dane wyjściowe strumienia definiują połączenia ze źródłem danych, do którego ma zostać zapisany strumień danych. Usługa Azure SQL Edge obecnie obsługuje następujące typy danych wyjściowych strumienia
    - Centrum brzegowe
    - SQL — dane wyjściowe SQL mogą być lokalną bazą danych w ramach wystąpienia programu SQL Edge lub zdalnej SQL Server lub Azure SQL Database. 
    - Azure Blob Storage

- Zapytanie strumienia — zapytanie strumienia definiuje transformację, agregacje, filtrowanie, sortowanie i sprzężenia, które należy zastosować do strumienia wejściowego, zanim zostanie on zapisany w strumieniu wyjściowym strumienia. Zapytanie strumienia jest oparte na tym samym języku zapytań, który jest używany przez Azure Stream Analytics. Aby uzyskać więcej informacji na Azure Stream Analytics języku zapytań, zapoznaj się z tematem [Stream Analytics języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> Przesyłanie strumieniowe T-SQL, w przeciwieństwie do Azure Stream Analytics, obecnie nie obsługuje [korzystania z danych referencyjnych dla odnośników](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) lub [korzystania z funkcji UDF i uda w usłudze Stream](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> Przesyłanie strumieniowe T-SQL obsługuje tylko podzestaw obszaru powierzchni języka obsługiwanego przez Azure Stream Analytics. Aby uzyskać więcej informacji na temat języka zapytań Azure Stream Analytics, zobacz [Stream Analytics języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Ograniczenia i ograniczenia

Poniższe ograniczenia i ograniczenia dotyczą przesyłania strumieniowego T-SQL. 

- W dowolnym momencie może być aktywne tylko jedno zadanie przesyłania strumieniowego. Zadania, które są już wykonywane, muszą zostać zatrzymane przed uruchomieniem innego zadania.
- Każde wykonanie zadania przesyłania strumieniowego jest jednowątkowe. Jeśli zadanie przesyłania strumieniowego zawiera wiele zapytań, każde zapytanie zostanie ocenione w kolejności szeregowej.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie zadania Stream Analytics w usłudze Azure SQL Edge (wersja zapoznawcza)](create-stream-analytics-job.md)
- [Wyświetlanie metadanych skojarzonych z zadaniami przesyłania strumieniowego w usłudze Azure SQL Edge (wersja zapoznawcza)](streaming-catalog-views.md)
- [Utwórz strumień zewnętrzny](create-external-stream-transact-sql.md)