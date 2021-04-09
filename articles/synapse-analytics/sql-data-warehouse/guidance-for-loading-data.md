---
title: Najlepsze rozwiązania dotyczące ładowania danych dla dedykowanych pul SQL
description: Zalecenia i optymalizacje wydajności służące do ładowania danych przy użyciu dedykowanych pul SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 004084097e2f24a4ceb36f7a016049a574b80b42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594839"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące ładowania danych przy użyciu dedykowanych pul SQL w usłudze Azure Synapse Analytics

W tym artykule przedstawiono zalecenia i optymalizacje wydajności dotyczące ładowania danych przy użyciu dedykowanej puli SQL.

## <a name="preparing-data-in-azure-storage"></a>Przygotowywanie danych w usłudze Azure Storage

Aby zminimalizować opóźnienie, należy rozszukać warstwę magazynu i dedykowaną pulę SQL.

Podczas eksportowania danych do formatu plików ORC mogą pojawić się błędy braku pamięci Java, jeśli w danych znajdują się duże kolumny tekstu. Aby obejść to ograniczenie, można wyeksportować tylko podzestaw wszystkich kolumn.

Różne typy plików mają różną charakterystykę wydajności. Najszybsze ładowanie zapewnia użycie skompresowanych rozdzielanych plików tekstowych. Różnica w wydajności między kodowaniem UTF-8 a UTF-16 jest minimalna.

Duże pliki skompresowane można podzielić na mniejsze.

## <a name="running-loads-with-enough-compute"></a>Uruchamianie zadań ładowania przy użyciu wystarczających zasobów obliczeniowych

Aby uzyskać większą szybkość ładowania, uruchamiaj tylko jedno zadanie ładowania naraz. Jeśli to nie jest możliwe, należy uruchomić minimalną liczbę obciążeń jednocześnie. Jeśli spodziewasz się dużego zadania ładowania, rozważ przeskalowanie w górę dedykowanej puli SQL przed obciążeniem.

Aby uruchamiać zadania ładowania przy użyciu odpowiednich zasobów obliczeniowych, utwórz użytkowników ładujących na potrzeby uruchamiania ładowania. Klasyfikowanie każdego użytkownika ładującego do określonej grupy obciążeń. Aby uruchomić ładowanie, zaloguj się jako jeden z użytkowników ładujących, a następnie uruchom obciążenie. Obciążenie jest uruchamiane z grupą obciążeń użytkownika.  

### <a name="example-of-creating-a-loading-user"></a>Przykład tworzenia użytkownika ładującego

W tym przykładzie tworzony jest użytkownik ładujący sklasyfikowany do określonej grupy obciążeń. Pierwszym krokiem jest **nawiązanie połączenia z główną bazą danych** i utworzenie nazwy logowania.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Nawiąż połączenie z dedykowaną pulą SQL i Utwórz użytkownika. Poniższy kod założono, że masz połączenie z bazą danych o nazwie mySampleDataWarehouse. Przedstawiono w nim sposób tworzenia użytkownika o nazwie Loader i nadaje użytkownikowi uprawnienia do tworzenia tabel i ładowania przy użyciu [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Następnie klasyfikuje użytkownika do grupy obciążeń dataloads z maksymalnymi zasobami. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>Jest to skrajny przykład przydziału zasobów puli SQL o 100% do jednego obciążenia. Pozwoli to na maksymalne współbieżność 1. Należy pamiętać, że ta wartość powinna być używana tylko w przypadku początkowego obciążenia, w którym konieczne będzie utworzenie dodatkowych grup obciążeń z własnymi konfiguracjami w celu balanace zasobów w ramach obciążeń. 

Aby uruchomić obciążenie z zasobami dla ładowania grupy obciążeń, zaloguj się jako moduł ładujący i uruchom obciążenie.

## <a name="allowing-multiple-users-to-load-polybase"></a>Zezwalanie wielu użytkownikom na ładowanie (baza Base)

Często istnieje potrzeba, aby wielu użytkowników ładowała dane do dedykowanej puli SQL. Ładowanie przy użyciu [CREATE TABLE jako Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (baza Base) wymaga uprawnień kontroli bazy danych.  Uprawnienia kontrolne (CONTROL) zapewniają dostęp z prawem kontroli do wszystkich schematów.

Być może nie chcesz, aby wszyscy użytkownicy, którzy wykonują zadania ładowania, mieli dostęp z prawem kontroli do wszystkich schematów. Aby ograniczyć uprawnienia, użyj instrukcji DENY CONTROL.

Rozważmy na przykład dwa schematy bazy danych, schema_A dla działu A i schema_B dla działu B. Załóżmy, że użytkownicy bazy danych o nazwie user_A i user_B są użytkownikami ładującymi technologii PolyBase w działach A i B. Obaj użytkownicy otrzymali uprawnienia CONTROL do bazy danych. Osoby, które utworzyły schematy A i B, teraz blokują swoje schematy przy użyciu instrukcji DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A i user_B są teraz zablokowane w schemacie innego działu.

## <a name="loading-to-a-staging-table"></a>Ładowanie do tabeli przejściowej

Aby osiągnąć najszybszą szybkość ładowania do przenoszenia danych do dedykowanej tabeli puli SQL, Załaduj dane do tabeli przejściowej.  Zdefiniuj tabelę przejściową jako stertę i użyj opcji dystrybucji z działaniem okrężnym.

Należy wziąć pod uwagę, że ładowanie jest zwykle procesem dwuetapowym, w którym należy najpierw załadować do tabeli przejściowej, a następnie wstawić dane do dedykowanej tabeli puli SQL w środowisku produkcyjnym. Jeśli tabela produkcyjna korzysta z dystrybucji skrótów, łączny czas ładowania i wstawiania może być krótszy w przypadku zdefiniowania tabeli przejściowej za pomocą dystrybucji skrótów.

Ładowanie do tabeli przejściowej trwa dłużej, ale drugi etap wstawiania wierszy do tabeli produkcyjnej nie powoduje przenoszenia danych między dystrybucjami.

## <a name="loading-to-a-columnstore-index"></a>Ładowanie do indeksu magazynu kolumn

Indeksy magazynu kolumn wymagają dużej ilości pamięci w celu skompresowania danych w grupy wierszy wysokiej jakości. Najlepsza wydajność kompresji i indeksu występuje wówczas, gdy indeks magazynu kolumn kompresuje maksymalnie 1 048 576 wierszy w każdej grupie wierszy.

Jeśli pamięci jest za mało, indeks magazynu kolumn może nie osiągać maksymalnej szybkości kompresji. W tym scenariuszu wyniki zapytania są skutkiem. Aby zapoznać się ze szczegółowymi informacjami, zobacz [Columnstore memory optimizations (Optymalizowanie pamięci na potrzeby magazynu kolumn)](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Aby mieć pewność, że użytkownik ładujący ma wystarczająco dużo pamięci do uzyskania maksymalnej szybkości kompresji, korzystaj z użytkowników ładujących będących członkami średnich lub dużych klas zasobów.
- Załaduj wystarczającą liczbę wierszy, aby całkowicie wypełnić nowe grupy wierszy. Podczas ładowania zbiorczego każde 1 048 576 wierszy jest kompresowanych bezpośrednio do magazynu kolumn jako pełna grupa wierszy. W przypadku zadań ładowania z liczbą wierszy mniejszą niż 102 400 wiersze są przekazywane do magazynu delta, gdzie są przechowywane w indeksie b-drzewa.

> [!NOTE]
> Jeśli załadujesz zbyt mało wierszy, wszystkie trasy do magazynu Delta i nie zostaną bezpośrednio skompresowane w formacie magazynu kolumn.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Zwiększ rozmiar wsadu podczas korzystania z interfejsu API SqLBulkCopy lub BCP

Ładowanie za pomocą instrukcji COPY zapewnia najwyższą przepływność przy użyciu dedykowanych pul SQL. Jeśli nie można użyć kopii do załadowania i należy użyć [interfejsu API SqLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), należy rozważyć zwiększenie rozmiaru wsadu w celu uzyskania lepszej przepływności.

> [!TIP]
> Rozmiar wsadu między 100 K a 1M wierszy jest zalecaną linią bazową do określania optymalnej pojemności wsadu.

## <a name="handling-loading-failures"></a>Postępowanie w przypadku błędów ładowania

Ładowanie z użyciem tabeli zewnętrznej może zakończyć się wystąpieniem błędu *Zapytanie zostało przerwane — osiągnięto maksymalny próg odrzuceń podczas odczytu ze źródła zewnętrznego*. Ten komunikat wskazuje, że dane zewnętrzne zawierają zanieczyszczone rekordy.

Rekord danych jest traktowany jako zanieczyszczony, jeśli spełnia jeden z następujących warunków:

- Typy danych i liczba kolumn nie są zgodne z definicjami kolumn tabeli zewnętrznej.
- Dane nie są zgodne z określonym zewnętrznym formatem pliku.

Aby poprawić zanieczyszczone rekordy, upewnij się, że definicje tabeli zewnętrznej i zewnętrznego formatu pliku są prawidłowe, a dane zewnętrzne są zgodne z tymi definicjami.

Jeśli podzestaw rekordów danych zewnętrznych jest zanieczyszczony, możesz odrzucić te rekordy w zapytaniach, używając opcji odrzucania w [instrukcji CREATE External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="inserting-data-into-a-production-table"></a>Wstawianie danych do tabeli produkcyjnej

Jednorazowe ładowanie do małej tabeli za pomocą [instrukcji INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) lub nawet okresowe ponowne ładowanie wyszukiwania to strategie, które mogą sprawdzić się w przypadku instrukcji takich jak `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Jednak pojedyncze wstawienia są mniej wydajne niż ładowanie zbiorcze.

Jeśli w ciągu dnia wykonujesz kilka tysięcy lub więcej wstawień, utwórz partię wstawień i załaduj je zbiorczo.  Opracuj proces dołączania pojedynczych wstawień do pliku, a następnie kolejny proces okresowo ładujący ten plik.

## <a name="creating-statistics-after-the-load"></a>Tworzenie statystyk po załadowaniu

Aby poprawić wydajność zapytań, należy utworzyć statystyki dla wszystkich kolumn wszystkich tabel po pierwszym załadowaniu danych, a następnie po każdej istotnej zmianie danych. Tworzenie statystyk można wykonać ręcznie lub można włączyć [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic).

Aby zapoznać się ze szczegółowym opisem statystyk, zobacz temat [Statystyki](sql-data-warehouse-tables-statistics.md). Poniższy przykład pokazuje, jak ręcznie utworzyć statystyki dla pięciu kolumn tabeli Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Obróć klucze magazynu (baza Base)

Zalecanym rozwiązaniem w zakresie zabezpieczeń jest regularne zmienianie klucza dostępu do usługi Blob Storage. Istnieją dwa klucze magazynu dla konta usługi Blob Storage, co pozwala na przenoszenie tych kluczy.

Aby przeprowadzić rotację kluczy konta usługi Azure Storage:

Dla każdego konta magazynu, którego klucz został zmieniony, wydaj poświadczenie [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Przykład:

Został utworzony klucz oryginalny

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Zamień klucz 1 na klucz 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Nie są potrzebne żadne inne zmiany podstawowych zewnętrznych źródeł danych.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat instrukcji COPY lub MyBase podczas projektowania procesu wyodrębniania, ładowania i przekształcania (ELT), zobacz [Design ELT for Azure Synapse Analytics](design-elt-data-loading.md).
- W przypadku samouczka ładowania [Użyj instrukcji Copy, aby załadować dane z usługi Azure Blob Storage do programu SQL Server Synapse](./load-data-from-azure-blob-storage-using-copy.md).
- Aby dowiedzieć się, jak monitorować ładowanie danych, zobacz [Monitor your workload using DMVs](sql-data-warehouse-manage-monitor.md) (Monitorowanie obciążenia przy użyciu widoków DMV).