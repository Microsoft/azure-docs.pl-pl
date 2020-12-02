---
title: 'Rozwiązywanie problemów: odczytywanie tekstu UTF-8 z plików CSV lub PARQUET przy użyciu puli SQL bezserwerowej'
description: Odczytywanie tekstu UTF-8 z plików CSV lub PARQUET przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 11/24/2020
ms.openlocfilehash: 238880cb3f3628df7591e8d08e3057ebfd885900
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466936"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Rozwiązywanie problemów z odczytywaniem tekstu UTF-8 z plików CSV lub Parquet przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics

Ten artykuł zawiera kroki rozwiązywania problemów w celu odczytania tekstu UTF-8 z plików CSV lub Parquet przy użyciu bezserwerowej puli SQL w usłudze Azure Synapse Analytics.

Gdy tekst UTF-8 jest odczytywany z pliku CSV lub PARQUET przy użyciu puli SQL bez użycia serwera, niektóre znaki specjalne, takie jak ü i ö, są niepoprawnie konwertowane, jeśli zapytanie zwróci kolumny VARCHAR z sortowaniem innym niż UTF8. Jest to znany problem w SQL Server i Azure SQL. Sortowanie w języku innym niż UTF8 to wartość domyślna w Synapse SQL, dzięki czemu będzie to miało zastosowanie w przypadku zapytań klientów. Klienci używający standardowych znaków angielskich i niektórych podzestawów rozszerzonych znaków łacińskich mogą nie zauważyć błędów konwersji. Niepoprawna konwersja została omówiona bardziej szczegółowo w temacie [zawsze używaj sortowania UTF-8 w celu odczytania tekstu UTF-8 w puli SQL bezserwerowej](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Obejście

Obejście tego problemu polega na tym, że podczas odczytywania tekstu UTF-8 z plików CSV lub PARQUET należy zawsze używać sortowania UTF-8.

-   W wielu przypadkach wystarczy ustawić sortowanie UTF8 dla bazy danych (operacja metadanych).
-   Jeśli sortowanie UTF8 nie zostało określone w tabelach zewnętrznych, które odczytują dane UTF8, należy ponownie utworzyć tabele zewnętrzne, których dotyczy problem, i ustawić sortowanie UTF8 dla kolumn VARCHAR (operacja metadanych).


## <a name="next-steps"></a>Następne kroki

* [CETAS z Synapse SQL](../sql/develop-tables-cetas.md)
* [Szybki Start: korzystanie z puli SQL bezserwerowej](../quickstart-sql-on-demand.md)
