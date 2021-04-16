---
title: Najlepsze rozwiązania dotyczące ładowania danych dla dedykowanych pul SQL
description: Zalecenia i optymalizacje wydajności ładowania danych przy użyciu dedykowanych pul SQL w Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 294b2a5188f0dfd8cb29f21bdbb29236b1740231
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565869"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Najlepsze rozwiązania dotyczące ładowania danych przy użyciu dedykowanych pul SQL w Azure Synapse Analytics

W tym artykule poznasz zalecenia i optymalizacje wydajności dotyczące ładowania danych przy użyciu dedykowanej puli SQL.

## <a name="preparing-data-in-azure-storage"></a>Przygotowywanie danych w usłudze Azure Storage

Aby zminimalizować opóźnienie, należy kolokować warstwę magazynu i dedykowaną pulę SQL.

Podczas eksportowania danych do formatu plików ORC mogą pojawić się błędy braku pamięci Java, jeśli w danych znajdują się duże kolumny tekstu. Aby obejść to ograniczenie, można wyeksportować tylko podzestaw wszystkich kolumn.

Różne typy plików mają różną charakterystykę wydajności. Najszybsze ładowanie zapewnia użycie skompresowanych rozdzielanych plików tekstowych. Różnica w wydajności między kodowaniem UTF-8 a UTF-16 jest minimalna.

Duże pliki skompresowane można podzielić na mniejsze.

## <a name="running-loads-with-enough-compute"></a>Uruchamianie zadań ładowania przy użyciu wystarczających zasobów obliczeniowych

Aby uzyskać większą szybkość ładowania, uruchamiaj tylko jedno zadanie ładowania naraz. Jeśli nie jest to możliwe, uruchom jednocześnie minimalną liczbę obciążeń. Jeśli spodziewasz się dużego zadania ładowania, rozważ skalowanie w górę dedykowanej puli SQL przed załadowaniem.

Aby uruchamiać zadania ładowania przy użyciu odpowiednich zasobów obliczeniowych, utwórz użytkowników ładujących na potrzeby uruchamiania ładowania. Sklasyfikuj każdego użytkownika ładu w określonej grupie obciążeń. Aby uruchomić ładowanie, zaloguj się jako jeden z użytkowników ładu, a następnie uruchom ładowanie. Obciążenie jest uruchamiane z grupą obciążenia użytkownika.  

### <a name="example-of-creating-a-loading-user"></a>Przykład tworzenia użytkownika ładującego

W tym przykładzie tworzy użytkownika ładu, który został sklasyfikowany do określonej grupy obciążeń. Pierwszym krokiem jest **nawiązanie połączenia z główną bazą danych** i utworzenie nazwy logowania.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

Połącz się z dedykowaną pulą SQL i utwórz użytkownika. W poniższym kodzie przyjęto założenie, że masz połączenie z bazą danych o nazwie mySampleDataWarehouse. Pokazano w nim, jak utworzyć użytkownika o nazwie loader i nadać użytkownikowi uprawnienia do tworzenia tabel i ładowania przy użyciu [instrukcji COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true). Następnie klasyfikuje użytkownika do grupy obciążeń DataLoads z maksymalnymi zasobami. 

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
>Jest to skrajny przykład przydzielania 100% zasobów puli SQL do jednego obciążenia. Zapewni to maksymalną współbieżność 1. Należy pamiętać, że powinno to być używane tylko w przypadku początkowego obciążenia, w którym konieczne będzie utworzenie dodatkowych grup obciążeń z ich własnymi konfiguracjami w celu wytłaniania zasobów w ramach obciążeń. 

Aby uruchomić obciążenie z zasobami dla grupy obciążenia ładowania, zaloguj się jako modułu ładującego i uruchom obciążenie.

## <a name="allowing-multiple-users-to-load-polybase"></a>Zezwalanie wielu użytkownikom na ładowanie (PolyBase)

Często istnieje potrzeba, aby wielu użytkowników ładowało dane do dedykowanej puli SQL. Ładowanie za pomocą [CREATE TABLE AS SELECT (Transact-SQL) (PolyBase)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) wymaga uprawnień CONTROL bazy danych.  Uprawnienia kontrolne (CONTROL) zapewniają dostęp z prawem kontroli do wszystkich schematów.

Być może nie chcesz, aby wszyscy użytkownicy, którzy wykonują zadania ładowania, mieli dostęp z prawem kontroli do wszystkich schematów. Aby ograniczyć uprawnienia, użyj instrukcji DENY CONTROL.

Rozważmy na przykład dwa schematy bazy danych, schema_A dla działu A i schema_B dla działu B. Załóżmy, że użytkownicy bazy danych o nazwie user_A i user_B są użytkownikami ładującymi technologii PolyBase w działach A i B. Obaj użytkownicy otrzymali uprawnienia CONTROL do bazy danych. Osoby, które utworzyły schematy A i B, teraz blokują swoje schematy przy użyciu instrukcji DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A i user_B są teraz zablokowane w schemacie innego fragmentu.

## <a name="loading-to-a-staging-table"></a>Ładowanie do tabeli przejściowej

Aby osiągnąć najszybszą szybkość ładowania w przypadku przenoszenia danych do dedykowanej tabeli puli SQL, załaduj dane do tabeli przejściowej.  Zdefiniuj tabelę przejściową jako stertę i użyj opcji dystrybucji z działaniem okrężnym.

Należy wziąć pod uwagę, że ładowanie jest zwykle procesem dwuetapowym, w którym najpierw należy załadować dane do tabeli przejściowej, a następnie wstawić je do tabeli produkcyjnej dedykowanej puli SQL. Jeśli tabela produkcyjna korzysta z dystrybucji skrótów, łączny czas ładowania i wstawiania może być krótszy w przypadku zdefiniowania tabeli przejściowej za pomocą dystrybucji skrótów.

Ładowanie do tabeli przejściowej trwa dłużej, ale drugi etap wstawiania wierszy do tabeli produkcyjnej nie powoduje przenoszenia danych między dystrybucjami.

## <a name="loading-to-a-columnstore-index"></a>Ładowanie do indeksu magazynu kolumn

Indeksy magazynu kolumn wymagają dużej ilości pamięci w celu skompresowania danych w grupy wierszy wysokiej jakości. Najlepsza wydajność kompresji i indeksu występuje wówczas, gdy indeks magazynu kolumn kompresuje maksymalnie 1 048 576 wierszy w każdej grupie wierszy.

Jeśli pamięci jest za mało, indeks magazynu kolumn może nie osiągać maksymalnej szybkości kompresji. Ten scenariusz z kolei ma wpływ na wydajność zapytań. Aby zapoznać się ze szczegółowymi informacjami, zobacz [Columnstore memory optimizations (Optymalizowanie pamięci na potrzeby magazynu kolumn)](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Aby mieć pewność, że użytkownik ładujący ma wystarczająco dużo pamięci do uzyskania maksymalnej szybkości kompresji, korzystaj z użytkowników ładujących będących członkami średnich lub dużych klas zasobów.
- Załaduj wystarczającą liczbę wierszy, aby całkowicie wypełnić nowe grupy wierszy. Podczas ładowania zbiorczego każde 1 048 576 wierszy jest kompresowanych bezpośrednio do magazynu kolumn jako pełna grupa wierszy. W przypadku zadań ładowania z liczbą wierszy mniejszą niż 102 400 wiersze są przekazywane do magazynu delta, gdzie są przechowywane w indeksie b-drzewa.

> [!NOTE]
> Jeśli załadujesz zbyt mało wierszy, wszystkie one mogą być kierowane do magazynu delta i nie są natychmiast kompresowane do formatu magazynu kolumn.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Zwiększanie rozmiaru partii w przypadku korzystania z interfejsu API SqLBulkCopy lub narzędzia bcp

Ładowanie za pomocą instrukcji COPY zapewni największą przepływność dzięki dedykowanym pulom SQL. Jeśli nie możesz użyć kopii do załadowania i musisz użyć interfejsu [API SqLBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) lub [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), rozważ zwiększenie rozmiaru partii w celu zwiększenia przepływności.

> [!TIP]
> Zalecanym punktem odniesienia do określenia optymalnej pojemności partii jest rozmiar partii od 100 K do 1 miliona wierszy.

## <a name="handling-loading-failures"></a>Postępowanie w przypadku błędów ładowania

Ładowanie z użyciem tabeli zewnętrznej może zakończyć się wystąpieniem błędu *Zapytanie zostało przerwane — osiągnięto maksymalny próg odrzuceń podczas odczytu ze źródła zewnętrznego*. Ten komunikat wskazuje, że dane zewnętrzne zawierają zanieczyszczone rekordy.

Rekord danych jest uznawany za zanieczyszczony, jeśli spełnia jeden z następujących warunków:

- Typy danych i liczba kolumn nie są zgodne z definicjami kolumn tabeli zewnętrznej.
- Dane nie są zgodne z określonym formatem pliku zewnętrznego.

Aby poprawić zanieczyszczone rekordy, upewnij się, że definicje tabeli zewnętrznej i zewnętrznego formatu pliku są prawidłowe, a dane zewnętrzne są zgodne z tymi definicjami.

Jeśli podzestaw rekordów danych zewnętrznych jest zanieczyszczony, możesz odrzucić te rekordy dla zapytań przy użyciu opcji odrzucania w instrukcji [CREATE EXTERNAL TABLE (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="inserting-data-into-a-production-table"></a>Wstawianie danych do tabeli produkcyjnej

Jednorazowe ładowanie do małej tabeli za pomocą [instrukcji INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) lub nawet okresowe ponowne ładowanie wyszukiwania to strategie, które mogą sprawdzić się w przypadku instrukcji takich jak `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Jednak pojedyncze wstawienia są mniej wydajne niż ładowanie zbiorcze.

Jeśli w ciągu dnia wykonujesz kilka tysięcy lub więcej wstawień, utwórz partię wstawień i załaduj je zbiorczo.  Opracuj proces dołączania pojedynczych wstawień do pliku, a następnie kolejny proces okresowo ładujący ten plik.

## <a name="creating-statistics-after-the-load"></a>Tworzenie statystyk po załadowaniu

Aby poprawić wydajność zapytań, należy utworzyć statystyki dla wszystkich kolumn wszystkich tabel po pierwszym załadowaniu danych, a następnie po każdej istotnej zmianie danych. Statystyki można tworzyć ręcznie lub włączyć AUTO_CREATE_STATISTICS [.](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)

Aby zapoznać się ze szczegółowym opisem statystyk, zobacz temat [Statystyki](sql-data-warehouse-tables-statistics.md). W poniższym przykładzie pokazano, jak ręcznie utworzyć statystyki dla pięciu kolumn tabeli Customer_Speed tabeli.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>Obracanie kluczy magazynu (PolyBase)

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

- Aby dowiedzieć się więcej o instrukcji COPY lub programie PolyBase podczas projektowania procesu wyodrębniania, ładowania i transformacji (ELT), zobacz [Design ELT for Azure Synapse Analytics](design-elt-data-loading.md)(Projektowanie procesu ELT dla procesu Azure Synapse Analytics).
- Aby uzyskać samouczek dotyczący ładowania, [użyj instrukcji COPY, aby załadować](./load-data-from-azure-blob-storage-using-copy.md)dane z usługi Azure Blob Storage do Synapse SQL .
- Aby dowiedzieć się, jak monitorować ładowanie danych, zobacz [Monitor your workload using DMVs](sql-data-warehouse-manage-monitor.md) (Monitorowanie obciążenia przy użyciu widoków DMV).