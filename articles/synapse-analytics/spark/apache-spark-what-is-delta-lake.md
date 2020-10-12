---
title: Co to jest usługa Delta Lake
description: Omówienie usługi Delta Lake i sposobu jej działania w ramach analizy Synapse Azure
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6a38b61ee03aa4853526586ca60542bd3641b66f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91249403"
---
# <a name="what-is-delta-lake"></a>Co to jest usługa Delta Lake

Usługa Azure Synapse Analytics jest zgodna z systemem Linux Foundation w systemie. Delta Lake to warstwa magazynu "open source", która umożliwia transakcję dotyczącą KWASów (niepodzielności, spójności, izolacji i trwałości) do Apache Spark i obciążeń danych Big Data.

Bieżąca wersja usługi Delta Lake dostępna w usłudze Azure Synapse ma obsługę języka dla Scala, PySpark i .NET. W dolnej części strony znajdują się linki do bardziej szczegółowych przykładów i dokumentacji.

## <a name="key-features"></a>Najważniejsze funkcje

| Cecha | Opis |
| --- | --- |
| **Transakcje KWASowe** | Jeziora dotyczące danych są zwykle wypełniane przez wiele procesów i potoków, a niektóre z nich zapisują dane jednocześnie przy użyciu operacji odczytu. Przed zmianą i dodaniem transakcji inżynierowie danych musieli przeprowadzić proces ręcznego błędu, aby zapewnić integralność danych. Zmiany w usłudze Data Lake zapewniają znane transakcje dotyczące KWAŚNych jezior. Zapewnia serializację, najwyższy poziom izolacji. Dowiedz się więcej na temat zmiany w usłudze Data [Lake: rozpakowywanie dziennika transakcji](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Skalowalność obsługi metadanych** | W przypadku danych Big Data nawet metadane mogą być "Big Data". Usługa Delta w usłudze Data Lake traktuje metadane podobnie jak dane, wykorzystując rozproszone możliwości przetwarzania w usłudze Spark do obsługi wszystkich metadanych. W związku z tym, w usłudze Delta Lake można obsłużyć tabele skalowania petabajtów z użyciem miliardów partycji i plików. |
| **Czas podróży (wersja danych)** | Możliwość "cofnięcia" zmiany lub powrotu do poprzedniej wersji jest jedną z najważniejszych funkcji transakcji. Funkcja Delta Lake oferuje migawki danych umożliwiające przywrócenie wcześniejszych wersji danych na potrzeby inspekcji, wycofywania lub odtwarzania eksperymentów. Dowiedz się więcej na temat [przedstawiania czasu trwania usługi Delta Lake w przypadku dużych ilości danych o dużej skali](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Otwórz format** | Apache Parquet to format linii bazowej usługi Delta Lake, dzięki czemu można wykorzystać wydajne schematy kompresji i kodowania, które są natywne w formacie. |
| **Ujednolicona partia i źródło strumieniowe i ujścia** | Tabela w formacie różnic Lake to tabela wsadowa, a także źródło i ujścia przesyłania strumieniowego. Strumieniowe pozyskiwanie danych, tworzenie wsadowe historyczne i zapytania interaktywne wszystko po prostu z pola. |
| **Wymuszanie schematu** | Wymuszanie schematu pomaga upewnić się, że typy danych są poprawne i są obecne wymagane kolumny, uniemożliwiając niespójności danych. Aby uzyskać więcej informacji, zobacz artykuł o przeniesieniu [do programu Delta Lake: wymuszanie schematu & ewolucji](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Ewolucja schematu** | Program Delta Lake umożliwia wprowadzanie zmian w schemacie tabeli, które mogą być stosowane automatycznie, bez konieczności pisania kodu DDL migracji. Aby uzyskać więcej informacji, zobacz artykuł o przeniesieniu [do programu Delta Lake: wymuszanie schematu & ewolucji](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Historia inspekcji** | Dziennik różnicowych transakcji rejestruje szczegółowe informacje o każdej zmianie dokonanej w danych, dostarczając pełny dziennik inspekcji zmian. |
| **Aktualizacje i usunięcia** | Funkcja Delta Lake obsługuje interfejsy API Scala/Java/python i SQL dla różnych funkcji. Obsługa operacji scalania, aktualizowania i usuwania pomaga spełnić wymagania dotyczące zgodności. Aby uzyskać więcej informacji, zobacz temat [Zapowiedźing Lake 0.6.1 Release](https://delta.io/news/delta-lake-0-6-1-released/),  [informującego o wersji delty Lake 0,7](https://delta.io/news/delta-lake-0-7-0-released/) i [prostej, niezawodnej Upserts i usunięciu w tabelach różnicowych usługi w języku Python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), które zawierają fragmenty kodu do scalania, aktualizowania i usuwania poleceń DML. |
| **100% zgodne z interfejsem API Apache Spark** | Deweloperzy mogą korzystać z różnicowej Lake z istniejącymi potokami danych o minimalnej zmianie, ponieważ jest w pełni zgodna z istniejącymi implementacjami platformy Spark. |

Pełną dokumentację można znaleźć na [stronie z dokumentacją usługi Delta Lake](https://docs.delta.io/latest/delta-intro.html) .

Aby uzyskać więcej informacji, zobacz temat [różnicowa Lake Project](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
