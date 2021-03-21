---
title: 'Rozwiązywanie problemów: odczytywanie tekstu UTF-8 z plików CSV lub PARQUET przy użyciu puli SQL bezserwerowej'
description: Odczytywanie tekstu UTF-8 z plików CSV lub PARQUET przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576421"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Rozwiązywanie problemów z odczytywaniem tekstu UTF-8 z plików CSV lub Parquet przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics

Ten artykuł zawiera kroki rozwiązywania problemów w celu odczytania tekstu UTF-8 z plików CSV lub Parquet przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics.

Gdy tekst UTF-8 jest odczytywany z pliku CSV lub PARQUET przy użyciu puli SQL bez użycia serwera, niektóre znaki specjalne, takie jak ü i ö, są niepoprawnie konwertowane, jeśli zapytanie zwróci kolumny VARCHAR z sortowaniem innym niż UTF8. Jest to znany problem w SQL Server i Azure SQL. Sortowanie w języku innym niż UTF8 to wartość domyślna w Synapse SQL, dzięki czemu będzie to miało zastosowanie w przypadku zapytań klientów. Klienci używający standardowych znaków angielskich i niektórych podzestawów rozszerzonych znaków łacińskich mogą nie zauważyć błędów konwersji. Niepoprawna konwersja została omówiona bardziej szczegółowo w temacie [zawsze używaj sortowania UTF-8 w celu odczytania tekstu UTF-8 w puli SQL bezserwerowej](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Obejście

Obejście tego problemu polega na tym, że podczas odczytywania tekstu UTF-8 z plików CSV lub PARQUET należy zawsze używać sortowania UTF-8.

- W wielu przypadkach wystarczy ustawić sortowanie UTF8 dla bazy danych (operacja metadanych).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- Można jawnie zdefiniować sortowanie w kolumnie VARCHAR w elemencie OPENROWSET lub tabeli zewnętrznej:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Jeśli sortowanie UTF8 nie zostało określone w tabelach zewnętrznych, które odczytują dane UTF8, należy ponownie utworzyć tabele zewnętrzne, których dotyczy problem, i ustawić sortowanie UTF8 dla kolumn VARCHAR (operacja metadanych).


## <a name="next-steps"></a>Następne kroki

* [Wykonywanie zapytań o pliki Parquet za pomocą języka SQL Synapse](../sql/query-parquet-files.md)
* [Wykonywanie zapytań dotyczących plików CSV za pomocą języka SQL Synapse](../sql/query-single-csv-file.md)
* [CETAS z Synapse SQL](../sql/develop-tables-cetas.md)
* [Szybki Start: korzystanie z puli SQL bezserwerowej](../quickstart-sql-on-demand.md)
