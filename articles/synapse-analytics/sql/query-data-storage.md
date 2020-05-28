---
title: Przegląd — wykonywanie zapytań dotyczących danych w magazynie przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: Ta sekcja zawiera przykładowe zapytania, których można użyć w celu wypróbowania zasobu SQL na żądanie (wersja zapoznawcza) w ramach usługi Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118265"
---
# <a name="overview-query-data-in-storage"></a>Przegląd: wykonywanie zapytań dotyczących danych w magazynie

Ta sekcja zawiera przykładowe zapytania, których można użyć w celu wypróbowania zasobu SQL na żądanie (wersja zapoznawcza) w ramach usługi Azure Synapse Analytics.
Obecnie obsługiwane formaty plików:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia potrzebne do wysyłania zapytań:

- Wybrany klient SQL:
    - Azure Synapse Studio (wersja zapoznawcza)
    - Azure Data Studio
    - SQL Server Management Studio

Dodatkowo parametry są następujące:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Zostanie użyta jako nazwa serwera.                                   |
| Region punktu końcowego usługi SQL na żądanie     | Zostanie użyta do określenia magazynu używanego w przykładach. |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Zostanie użyta w celu uzyskania dostępu do punktu końcowego.                               |
| Baza danych, która będzie używana do tworzenia widoków     | Ta baza danych będzie używana jako punkt wyjścia dla przykładów.       |

## <a name="first-time-setup"></a>Konfiguracja pierwszego czasu

Pierwszym krokiem jest **utworzenie bazy danych** , w której będą wykonywane zapytania. Następnie zainicjuj obiekty, wykonując [skrypt Instalatora](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) w tej bazie danych. Ten skrypt instalacyjny utworzy źródła danych, poświadczenia w zakresie bazy danych i zewnętrzne formaty plików, które są używane do odczytywania danych w tych przykładach.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.  Zapisz nazwę używanej bazy danych, która będzie potrzebna później.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Podane dane demonstracyjne

Dane demonstracyjne zawierają następujące zestawy danych:

- NYC taksówki — w przypadku rekordów podróży z podróżą za taksówkę — część publicznego zestawu danych NYC
  - Format CSV
  - Format Parquet
- Zestaw danych populacji
  - Format CSV
- Przykładowe pliki Parquet z kolumnami zagnieżdżonymi
  - Format Parquet
- Books JSON
  - Format JSON

| Ścieżka folderu                                                  | Opis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| CSV                                                        | Folder nadrzędny dla danych w formacie CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Foldery z plikami danych populacji w różnych formatach CSV. |
| /csv/taxi/                                                   | Folder z plikami danych publicznych NYC w formacie CSV              |
| Parquet                                                    | Folder nadrzędny dla danych w formacie Parquet                     |
| /parquet/taxi                                                | NYC publiczne pliki danych w formacie Parquet, podzielone na partycje według roku i miesiąca przy użyciu schematu partycjonowania Hive/Hadoop. |
| /parquet/nested/                                             | Przykładowe pliki Parquet z kolumnami zagnieżdżonymi                     |
| kodu                                                       | Folder nadrzędny dla danych w formacie JSON                        |
| /json/books/                                                 | Pliki JSON z danymi książek                                   |

### <a name="sample-query"></a>Przykładowe zapytanie

Ostatnim krokiem weryfikacji jest wykonanie następującej kwerendy:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

Powyższe zapytanie powinno zwrócić ten numer: **8945574**.

## <a name="next-steps"></a>Następne kroki

Teraz można przystąpić do dalszej pracy z następującymi artykułami:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)

- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)

- [Kwerenda określonych plików](query-specific-files.md)

- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)

- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)

- [Wykonywanie zapytań względem plików JSON](query-json-files.md)

- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
