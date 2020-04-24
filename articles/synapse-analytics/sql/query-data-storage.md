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
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116251"
---
# <a name="overview-query-data-in-storage"></a>Przegląd: wykonywanie zapytań dotyczących danych w magazynie

Ta sekcja zawiera przykładowe zapytania, których można użyć w celu wypróbowania zasobu SQL na żądanie (wersja zapoznawcza) w ramach usługi Azure Synapse Analytics.
Obecnie obsługiwane są następujące pliki: 
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

Przed użyciem przykładów zamieszczonych w dalszej części tego artykułu, należy wykonać dwie czynności:

- Tworzenie bazy danych dla widoków (w przypadku, gdy chcesz używać widoków)
- Utwórz poświadczenia, które będą używane przez SQL na żądanie do uzyskiwania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Do tworzenia widoków potrzebna jest baza danych. Ta baza danych będzie używana dla niektórych przykładowych zapytań w tej dokumentacji.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.  Zapisz nazwę używanej bazy danych, która będzie potrzebna później.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Utwórz poświadczenia

Aby można było uruchamiać zapytania, należy utworzyć poświadczenia. To poświadczenie będzie używane przez usługę SQL na żądanie do uzyskiwania dostępu do plików w magazynie.

> [!NOTE]
> W celu pomyślnego uruchomienia tego tematu należy użyć tokenu SAS.
>
> Aby rozpocząć korzystanie z tokenów SAS, należy porzucić tożsamość użytkownika, który został wyjaśniony w następującym [artykule](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> Funkcja SQL on-Demand domyślnie zawsze używa przekazywania w usłudze AAD.

Aby uzyskać więcej informacji na temat zarządzania kontrolą dostępu do magazynu, zaznacz ten [link](develop-storage-files-storage-access-control.md).

Aby utworzyć poświadczenia dla kontenerów CSV, JSON i Parquet, uruchom poniższy kod:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>Walidacja

Wykonaj następujące trzy zapytania i sprawdź, czy poświadczenia zostały utworzone prawidłowo.

> [!NOTE]
> Wszystkie identyfikatory URI w przykładowych zapytaniach używają konta magazynu znajdującego się w regionie platformy Azure Europa Północna. Upewnij się, że utworzono odpowiednie poświadczenie. Uruchom poniższe zapytanie i upewnij się, że konto magazynu jest wymienione na liście.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Jeśli nie możesz znaleźć odpowiedniego poświadczenia, zapoznaj się z tematem [Konfiguracja pierwszego czasu](#first-time-setup).

### <a name="sample-query"></a>Przykładowe zapytanie

Ostatnim krokiem weryfikacji jest wykonanie następującej kwerendy:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
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
