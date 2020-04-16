---
title: Omówienie — zapytanie o dane w magazynie przy użyciu języka SQL na żądanie (wersja zapoznawcza)
description: Ta sekcja zawiera przykładowe kwerendy, których można użyć do wypróbowania zasobu SQL on-demand (preview) w usłudze Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424902"
---
# <a name="overview-query-data-in-storage"></a>Omówienie: Zapytanie o dane w magazynie

Ta sekcja zawiera przykładowe kwerendy, których można użyć do wypróbowania zasobu SQL on-demand (preview) w usłudze Azure Synapse Analytics.
Obecnie obsługiwane pliki to: 
- CSV
- Parkiet
- JSON

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia potrzebne do wystawiania zapytań:

- Wybrany klient SQL:
    - Usługa Azure Synapse Studio (wersja zapoznawcza)
    - Azure Data Studio
    - SQL Server Management Studio

Ponadto parametry są następujące:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Będzie używany jako nazwa serwera.                                   |
| Region punktu końcowego usługi SQL na żądanie     | Będzie używany do określenia magazynu używanego w próbkach. |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Będzie używany do uzyskiwania dostępu do punktu końcowego.                               |
| Baza danych, której użyjesz do tworzenia widoków     | Ta baza danych będzie używana jako punkt wyjścia dla przykładów.       |

## <a name="first-time-setup"></a>Konfiguracja po raz pierwszy

Przed użyciem próbek zawartych w dalszej części tego artykułu, masz dwa kroki:

- Tworzenie bazy danych dla widoków (w przypadku, gdy chcesz użyć widoków)
- Tworzenie poświadczeń, które mają być używane przez SQL na żądanie, aby uzyskać dostęp do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Do utworzenia widoków potrzebna jest baza danych. Użyjesz tej bazy danych dla niektórych przykładowych zapytań w tej dokumentacji.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.  Zapisz nazwę bazy danych, której używasz, będziesz jej potrzebować później.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Tworzenie poświadczeń

Przed uruchomieniem kwerend należy utworzyć poświadczenia. To poświadczenie będzie używane przez usługę SQL na żądanie, aby uzyskać dostęp do plików w magazynie.

> [!NOTE]
> Aby pomyślnie uruchomić How To w tej sekcji, musisz użyć tokenu Sygnatury dostępu Współdzielonego.
>
> Aby rozpocząć korzystanie z tokenów sygnatury dostępu Współdzielonego, należy upuścić UserIdentity, który jest wyjaśniony w poniższym [artykule](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).
>
> SQL na żądanie domyślnie zawsze używa przekazywania usługi AAD.

Aby uzyskać więcej informacji na temat zarządzania kontrolą dostępu do magazynu, sprawdź to [łącze](develop-storage-files-storage-access-control.md).

> [!WARNING]
> Należy utworzyć poświadczenia dla konta magazynu, który znajduje się w regionie punktu końcowego. Chociaż SQL na żądanie może uzyskać dostęp do magazynów z różnych regionów, posiadanie magazynu i punktu końcowego w tym samym regionie zapewni lepszą wydajność.

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

## <a name="provided-demo-data"></a>Dostarczone dane demonstracyjne

Dane demonstracyjne zawierają następujące zestawy danych:

- NYC Taxi - Yellow Taxi Trip Records - część publicznego zestawu danych NYC
  - Format CSV
  - Format Parquet
- Zestaw danych populacji
  - Format CSV
- Przykładowe pliki parkietu z zagnieżdżonych kolumn
  - Format Parquet
- Książki JSON
  - Format JSON

| Ścieżka folderu                                                  | Opis                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Folder nadrzędny dla danych w formacie CSV                         |
| /csv/populacja/<br />/csv/populacja-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-cytowany | Foldery z plikami danych populacji w różnych formatach CSV. |
| /csv/taxi/                                                   | Folder z publicznymi plikami danych NYC w formacie CSV              |
| /parkiet/                                                    | Folder nadrzędny dla danych w formacie Parkietu                     |
| /parkiet/taksówka                                                | NYC publicznych plików danych w formacie Parkiet, podzielony na partycje według roku i miesiąca przy użyciu hive/Hadoop partycjonowania schematu. |
| /parkiet/zagnieżdżone/                                             | Przykładowe pliki parkietu z zagnieżdżonych kolumn                     |
| /json/                                                       | Folder nadrzędny dla danych w formacie JSON                        |
| /json/książki/                                                 | Pliki JSON z danymi książek                                   |

## <a name="validation"></a>Walidacja

Wykonaj następujące trzy kwerendy i sprawdź, czy poświadczenia są poprawnie utworzone.

> [!NOTE]
> Wszystkie identyfikatory URI w przykładowych kwerendach używają konta magazynu znajdującego się w regionie Platformy Azure w Europie Północnej. Upewnij się, że utworzono odpowiednie poświadczenia. Uruchom poniższą kwerendę i upewnij się, że konto magazynu znajduje się na liście.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Jeśli nie możesz znaleźć odpowiednich poświadczeń, zaznacz pole wyboru [Pierwsza konfiguracja](#first-time-setup).

### <a name="sample-query"></a>Przykładowe zapytanie

Ostatnim krokiem sprawdzania poprawności jest wykonanie następującej kwerendy:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

Powyższa kwerenda powinna zwrócić ten numer: **8945574**.

## <a name="next-steps"></a>Następne kroki

Teraz możesz kontynuować następujące artykuły Jak to zrobić:

- [Zapytanie o pojedynczy plik CSV](query-single-csv-file.md)

- [Foldery kwerend i wiele plików CSV](query-folders-multiple-csv-files.md)

- [Kwerenda określonych plików](query-specific-files.md)

- [Pliki parkietu kwerendy](query-parquet-files.md)

- [Kwerenda Parkiet zagnieżdżone typy](query-parquet-nested-types.md)

- [Kwerenda plików JSON](query-json-files.md)

- [Tworzenie widoków i korzystanie z ich używania](create-use-views.md)
