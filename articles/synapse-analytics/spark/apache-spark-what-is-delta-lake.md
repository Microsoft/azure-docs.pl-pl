---
title: Co to jest Delta Lake
description: Omówienie jeziora Delta i jego działania w ramach usługi Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429202"
---
# <a name="what-is-delta-lake"></a>Co to jest Delta Lake?

Usługa Azure Synapse Analytics jest zgodna z systemem Linux Foundation Delta Lake. Delta Lake to warstwa magazynu open source, która przenosi transakcje ACID (niepodzielność, spójność, izolacja i trwałość) do apache spark i obciążeń dużych zbiorów danych.

## <a name="key-features"></a>Najważniejsze funkcje

| Funkcja | Opis |
| --- | --- |
| **Transakcje ACID** | Jeziora danych są zazwyczaj wypełniane za pośrednictwem wielu procesów i potoków, z których niektóre są zapisy danych jednocześnie z odczytów. Przed Delta Lake i dodawaniem transakcji inżynierowie danych musieli przejść przez ręczny proces podatny na błędy, aby zapewnić integralność danych. Delta Lake przenosi znane transakcje ACID do jezior danych. Zapewnia serializability, najsilniejszy poziom izolacji. Dowiedz się więcej na [nurkowanie w Delta Lake: Rozpakowywanie dziennika transakcji](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Skalowalna obsługa metadanych** | W dużych zbiorach danych nawet same metadane mogą być "dużymi zbiorami danych". Delta Lake traktuje metadane podobnie jak dane, wykorzystując rozproszoną moc obliczeniową platformy Spark do obsługi wszystkich metadanych. W rezultacie Delta Lake może obsługiwać tabele w skali petabajtów z miliardami partycji i plików na luzie. |
| **Podróż w czasie (przechowywanie wersji danych)** | Możliwość "cofnąć" zmiany lub wrócić do poprzedniej wersji jest jedną z kluczowych cech transakcji. Delta Lake udostępnia migawki danych umożliwiające powrót do wcześniejszych wersji danych do inspekcji, wycofywania lub odtworzenia eksperymentów. Dowiedz się więcej [na temat Przedstawianie Delta Lake Time Travel dla dużych jezior danych.](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html) |
| **Otwórz format** | Apache Parquet jest formatem bazowym dla Delta Lake, co pozwala na wykorzystanie wydajnych schematów kompresji i kodowania, które są natywne do formatu. |
| **Ujednolicone źródło partii i przesyłania strumieniowego oraz ujście** | Tabela w Delta Lake jest zarówno tabelą wsadową, jak i źródłem przesyłania strumieniowego i ujściem. Przesyłanie strumieniowe danych pozyskiwania, partii historycznych zasypakowania i interaktywnych zapytań wszystko po prostu działa po wyjęciu z pudełka. |
| **Wymuszanie schematu** | Wymuszanie schematu pomaga zapewnić, że typy danych są poprawne i wymagane kolumny są obecne, zapobiegając złe dane z przyczyną niespójności danych. Aby uzyskać więcej informacji, zobacz [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Ewolucja schematu** | Delta Lake umożliwia wprowadzanie zmian w schemacie tabeli, które mogą być stosowane automatycznie, bez konieczności zapisu migracji DDL. Aby uzyskać więcej informacji, zobacz [Diving Into Delta Lake: Schema Enforcement & Evolution](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Historia inspekcji** | Dziennik transakcji Delta Lake rejestruje szczegóły dotyczące każdej zmiany wprowadzonej do danych, zapewniając pełną ścieżkę inspekcji zmian. |
| **Aktualizacje i usuwanie** | Delta Lake obsługuje interfejsy API Scala / Java / Python i SQL dla różnych funkcji. Obsługa operacji scalania, aktualizacji i usuwania pomaga spełnić wymagania dotyczące zgodności. Aby uzyskać więcej informacji, zobacz [Ogłaszanie wydania Delta Lake 0.4.0 oraz](https://delta.io/news/delta-lake-0-4-0-released/) [prostych, niezawodnych upserts i deletes w tabelach Delta Lake przy użyciu interfejsów API języka Python](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), które zawierają fragmenty kodu do scalania, aktualizowania i usuwania poleceń DML. |
| **100% kompatybilny z API Apache Spark** | Deweloperzy mogą używać Delta Lake z istniejących potoków danych z minimalnymi zmianami, ponieważ jest w pełni zgodny z istniejącymi implementacjami Platformy Spark. |

Pełna dokumentacja znajduje się na [stronie dokumentacji jeziora Delta](https://docs.delta.io/latest/delta-intro.html)

Aby uzyskać więcej informacji, zobacz [Delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>Następne kroki

- [.NET dla apache Spark dokumentacji](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
