---
title: Wskazówki dotyczące dostrajania wydajności dla aplikacji i baz danych
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Dowiedz się więcej na temat dostrajania aplikacji baz danych i baz danych pod kątem wydajności w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/10/2020
ms.openlocfilehash: 40657ad2f3b69d62e0e0d9c7d9e0f0be7343547b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490605"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Dostrajanie aplikacji i baz danych pod kątem wydajności w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Po zidentyfikowaniu problemu z wydajnością, który jest dostępny dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL, ten artykuł ma na celu ułatwienie:

- Dostosuj aplikację i Zastosuj niektóre najlepsze rozwiązania, które mogą zwiększyć wydajność.
- Dostrajaj bazę danych, zmieniając indeksy i zapytania, aby skuteczniej pracować z danymi.

W tym artykule przyjęto założenie, że już pracujesz z [zaleceniami](database-advisor-implement-performance-recommendations.md) usługi Azure SQL Database Database i Azure SQL Database [zalecenia autodostrajania](automatic-tuning-overview.md), jeśli ma to zastosowanie. Przyjęto również założenie, że przegląd dotyczący [monitorowania i dostrajania](monitor-tune-overview.md) oraz powiązanych artykułów związanych z rozwiązywaniem problemów z wydajnością. Ponadto w tym artykule założono, że nie masz zasobów procesora, problem z wydajnością związany z działaniem, który można rozwiązać przez zwiększenie rozmiaru lub warstwy usług w celu zapewnienia większej ilości zasobów dla bazy danych.

## <a name="tune-your-application"></a>Dostrajanie aplikacji

W tradycyjnych SQL Server lokalnych, proces wstępnego planowania pojemności jest często oddzielony od procesu uruchamiania aplikacji w środowisku produkcyjnym. Najpierw zakupione są licencje sprzętowe i produktowe, a następnie dostrojenie wydajności odbywa się później. Gdy korzystasz z usługi Azure SQL, dobrym pomysłem jest przekazanie procesu uruchamiania aplikacji i dostosowanie go. Dzięki modelowi płacenia za pojemność na żądanie możesz dostosować swoją aplikację do korzystania z minimalnych zasobów wymaganych teraz, zamiast nadmiernej aprowizacji sprzętu na podstawie odgadnięcia przyszłych planów wzrostu dla aplikacji, które często są nieprawidłowe. Niektórzy klienci mogą zrezygnować z dostrajania aplikacji, a zamiast tego wybrać opcję nadmierne udostępnianie zasobów sprzętowych. Takie podejście może być dobrym pomysłem, jeśli nie chcesz zmieniać kluczowych aplikacji w okresie zajętości. Jednak dostrajanie aplikacji może zminimalizować wymagania dotyczące zasobów i obniżyć miesięczne rachunki w przypadku używania warstw usług w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.

### <a name="application-characteristics"></a>Charakterystyka aplikacji

Chociaż Azure SQL Database i warstwy usługi wystąpienia zarządzanego Azure SQL zostały zaprojektowane w celu poprawy stabilności wydajności i przewidywalności aplikacji, niektóre najlepsze rozwiązania mogą pomóc w dostosowaniu aplikacji w celu lepszego wykorzystania zasobów w rozmiarze obliczeniowym. Mimo że wiele aplikacji ma znaczący wpływ na wydajność, wystarczy przełączać się do wyższego rozmiaru lub warstwy usług, a niektóre aplikacje wymagają dodatkowego dostrajania do skorzystania z wyższego poziomu usługi. Aby zwiększyć wydajność, należy rozważyć dodatkowe dostrajanie aplikacji dla aplikacji o następujących cechach:

- **Aplikacje z niską wydajnością z powodu zachowania "rozmawiania"**

  Aplikacje rozmawiające umożliwiają nadmierne wykonywanie operacji dostępu do danych, które są wrażliwe na opóźnienia sieci. Może być konieczne zmodyfikowanie tych rodzajów aplikacji w celu zmniejszenia liczby operacji dostępu do danych w bazie danych. Na przykład można poprawić wydajność aplikacji przy użyciu technik takich jak przetwarzanie wsadowe zapytań ad hoc lub przechodzenie zapytań do procedur składowanych. Aby uzyskać więcej informacji, zobacz [zapytania wsadowe](#batch-queries).

- **Bazy danych z intensywnym obciążeniem, które nie mogą być obsługiwane przez całą pojedynczą maszynę**

   Bazy danych, które przekraczają zasoby o najwyższej wielkości obliczeń w warstwie Premium, mogą przynieść skalowanie obciążenia. Aby uzyskać więcej informacji, zobacz [fragmentowania między bazami danych](#cross-database-sharding) i [partycjonowanie funkcjonalne](#functional-partitioning).

- **Aplikacje z nieoptymalnymi zapytaniami**

  Aplikacje, szczególnie te w warstwie dostępu do danych, które mają źle dopasowane zapytania, mogą nie być korzystne z większym rozmiarem obliczeniowym. Obejmuje to zapytania, które nie mają klauzuli WHERE, mają brakujące indeksy lub mają nieaktualne dane statystyczne. Te aplikacje korzystają z standardowych technik dostrajania wydajności zapytań. Aby uzyskać więcej informacji, zobacz [brakujące indeksy](#identifying-and-adding-missing-indexes) i [dostrajanie zapytań oraz podpowiedzi](#query-tuning-and-hinting).

- **Aplikacje mające optymalny projekt dostępu do danych**

   Aplikacje, które mają nieodłączne problemy z współbieżnością dostępu do danych, na przykład zakleszczenie mogą nie korzystać z większego rozmiaru obliczeniowego. Należy rozważyć zmniejszenie liczby rund do bazy danych przez buforowanie danych po stronie klienta za pomocą usługi Azure buforowania lub innej technologii buforowania. Zobacz [buforowanie warstwy aplikacji](#application-tier-caching).

## <a name="tune-your-database"></a>Dostrajanie bazy danych

W tej sekcji Przyjrzyjmy się pewnym technikom, które służą do dostrajania bazy danych w celu uzyskania najlepszej wydajności aplikacji i uruchamiania jej przy najniższym możliwym rozmiarze. Niektóre z tych technik są zgodne z tradycyjnymi rozwiązaniami SQL Server dostrajania, ale inne są specyficzne dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL. W niektórych przypadkach można sprawdzić zużyte zasoby dla bazy danych, aby znaleźć obszary umożliwiające dalsze dostosowywanie i rozszerzanie tradycyjnych technik SQL Server do pracy w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.

### <a name="identifying-and-adding-missing-indexes"></a>Identyfikowanie i Dodawanie brakujących indeksów

Typowy problem związany z wydajnością bazy danych OLTP odnosi się do fizycznego projektu bazy danych. Często schematy bazy danych są zaprojektowane i dostarczane bez testów w dużej skali (w przypadku ładowania lub w woluminie danych). Niestety, wydajność planu zapytania może być akceptowalna na małą skalę, ale istotnie zmniejsza się w zależności od ilości danych na poziomie produkcyjnym. Najbardziej typowym źródłem tego problemu jest brak odpowiednich indeksów w celu spełnienia filtrów lub innych ograniczeń w zapytaniu. Często brakujące indeksy manifestuje się jako skanowanie tabeli, gdy wyszukiwanie może być wystarczające.

W tym przykładzie wybrany plan zapytania używa skanowania, gdy wystarczająca jest wartość wyszukiwania:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Plan zapytania z brakującymi indeksami](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL mogą pomóc w znalezieniu i naprawieniu typowych warunków brakujących indeksów. Widoków DMV, które są wbudowane w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL, przyjrzyj się kompilacjom zapytania, w którym indeks znacznie zmniejsza szacowany koszt do uruchomienia zapytania. Podczas wykonywania zapytania aparat bazy danych śledzi, jak często jest wykonywany każdy plan zapytania, i śledzi szacowaną lukę między wykonywanym planem zapytania a zawyobraź sobie, gdzie istniał ten indeks. Za pomocą tych widoków DMV można szybko odgadnąć, które zmiany w projekcie fizycznej bazy danych mogą poprawić ogólny koszt obciążeń dla bazy danych i jej rzeczywistego obciążenia.

Za pomocą tego zapytania można oszacować potencjalne brakujące indeksy:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

W tym przykładzie zapytanie spowodowało następującą sugestię:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Po jego utworzeniu ta sama instrukcja SELECT wybiera inny plan, który używa wyszukiwania zamiast skanowania, a następnie wykonuje plan wydajniejszie:

![Plan zapytania ze poprawionymi indeksami](./media/performance-guidance/query_plan_corrected_indexes.png)

Kluczowym wglądem jest to, że pojemność we/wy udostępnionego systemu asortymentu jest bardziej ograniczona niż w przypadku dedykowanego komputera serwera. Istnieje możliwość zminimalizowania niepotrzebnych operacji we/wy na wyższy poziom, co umożliwia maksymalne wykorzystanie systemu w zasobach każdego rozmiaru obliczeniowego warstw usług. Opcje projektowania odpowiednich fizycznych baz danych mogą znacząco poprawić opóźnienia poszczególnych zapytań, zwiększyć przepływność współbieżnych żądań obsłużonych na jednostkę skalowania i zminimalizować koszty wymagane do zaspokojenia zapytania. Aby uzyskać więcej informacji na temat brakującego indeksu widoków DMV, zobacz [sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql).

### <a name="query-tuning-and-hinting"></a>Dostrajanie zapytania i podpowiedzi

Optymalizator zapytań w Azure SQL Database i wystąpienie zarządzane Azure SQL jest podobny do tradycyjnego optymalizatora zapytań SQL Server. Większość najlepszych rozwiązań dotyczących dostrajania zapytań i zrozumienie ograniczeń modelu przyczyny dla optymalizatora zapytań ma zastosowanie również do Azure SQL Database i wystąpienia zarządzanego Azure SQL. Jeśli dostrojsz zapytania w Azure SQL Database i wystąpieniu zarządzanym Azure SQL, możesz skorzystać z dodatkowej korzyści wynikającej z obniżenia zagregowanych wymagań dotyczących zasobów. Aplikacja może być uruchomiona z niższym kosztem niż niedostrojony odpowiednik, ponieważ może działać z mniejszym rozmiarem obliczeniowym.

Przykład, który jest typowy w SQL Server i który ma zastosowanie również do Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL, to sposób, w jaki są parametry "wykrywanie" optymalizatora zapytań. Podczas kompilacji optymalizator zapytań szacuje bieżącą wartość parametru, aby określić, czy może generować bardziej optymalny plan zapytania. Chociaż taka strategia często może prowadzić do planu zapytania, który jest znacznie szybszy od planu skompilowanego bez znanych wartości parametrów, obecnie działa w sposób nieidealny zarówno w SQL Server, w Azure SQL Database, jak i w wystąpieniu zarządzanym usługi Azure SQL. Czasami parametr nie jest wygenerowany i czasami jest wykrywanie parametru, ale wygenerowany plan jest optymalny dla pełnego zestawu wartości parametrów w obciążeniu. Firma Microsoft zawiera wskazówki zapytania (dyrektywy), dzięki czemu można bardziej umyślnie określić cel i zastąpić domyślne zachowanie funkcji wykrywania parametrów. Często, jeśli używasz wskazówek, możesz naprawić przypadki, w których domyślne zachowanie wystąpienia zarządzanego SQL Server, Azure SQL Database i Azure SQL jest nieidealne dla określonego obciążenia klienta.

W następnym przykładzie pokazano, jak procesor zapytań może wygenerować plan, który jest optymalny zarówno w przypadku wymagań dotyczących wydajności, jak i zasobów. Ten przykład pokazuje również, że jeśli używasz wskazówki zapytania, możesz zmniejszyć czas wykonywania zapytania i wymagania dotyczące zasobów dla bazy danych:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

Kod instalacji tworzy tabelę, która ma skośną dystrybucję danych. Optymalny plan zapytania różni się w zależności od tego, który parametr został wybrany. Niestety zachowanie pamięci podręcznej planu nie zawsze powoduje ponowne skompilowanie zapytania na podstawie najbardziej typowej wartości parametru. W związku z tym, istnieje możliwość, że plan podrzędny jest buforowany i używany dla wielu wartości, nawet jeśli inny plan może być lepszym wyborem planu. Następnie plan zapytania tworzy dwie procedury składowane, które są identyczne, z tą różnicą, że jeden ma specjalną wskazówkę zapytania.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Zalecamy odczekanie co najmniej 10 minut przed rozpoczęciem części 2 tego przykładu, aby wyniki były odrębne w wynikowych danych telemetrycznych.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Każda część tego przykładu podejmuje próbę uruchomienia sparametryzowanej instrukcji INSERT 1 000 razy (w celu wygenerowania wystarczającego obciążenia do użycia jako zestawu danych testowych). Gdy wykonuje procedury składowane, procesor zapytań analizuje wartość parametru, która jest przenoszona do procedury podczas pierwszej kompilacji (parametru "wykrywanie"). Procesor buforuje plan i używa go do późniejszego wywołania, nawet jeśli wartość parametru jest różna. Optymalny plan nie może być używany we wszystkich przypadkach. Czasami musisz poprowadzić Optymalizator w celu wybrania planu, który jest lepszy dla średniej wielkości liter zamiast określonego przypadku, od momentu pierwszego skompilowania zapytania. W tym przykładzie początkowy plan generuje plan "Scan", który odczytuje wszystkie wiersze, aby znaleźć każdą wartość zgodną z parametrem:

![Dostrajanie zapytania przy użyciu planu skanowania](./media/performance-guidance/query_tuning_1.png)

Ponieważ procedura została wykonana przy użyciu wartości 1, otrzymany plan był optymalny dla wartości 1, ale był optymalny dla wszystkich innych wartości w tabeli. Wynik prawdopodobnie nie jest tym, co należy zrobić, jeśli chcesz losowo wybrać każdy plan, ponieważ plan działa wolniej i zużywa więcej zasobów.

W przypadku uruchomienia testu z `SET STATISTICS IO` ustawioną na wartość w `ON` tym przykładzie działanie skanowania logicznego jest wykonywane w tle. Zobaczysz, że istnieją 1 148 odczyty wykonywane przez plan (co jest niewydajne, jeśli średni przypadek ma zwrócić tylko jeden wiersz):

![Dostrajanie zapytania przy użyciu skanowania logicznego](./media/performance-guidance/query_tuning_2.png)

W drugiej części przykładu użyto wskazówki zapytania, aby nakazać Optymalizatorowi użycie określonej wartości podczas procesu kompilacji. W takim przypadku zmusza procesor zapytań do ignorowania wartości, która jest przesyłana jako parametr, a zamiast tego `UNKNOWN` . Odnosi się do wartości, która ma średnią częstotliwość w tabeli (ignorowanie pochylenia). Powstały plan jest planem opartym na wyszukiwaniach, który jest szybszy i używa mniejszej ilości zasobów, średnio od planu w części 1 tego przykładu:

![Dostrajanie zapytania przy użyciu podpowiedzi zapytania](./media/performance-guidance/query_tuning_3.png)

Efekt można zobaczyć w tabeli **sys.resource_stats** (istnieje opóźnienie od momentu wykonania testu oraz momentu, gdy dane wypełniają tabelę). W tym przykładzie część 1 została wykonana w przedziale czasu 22:25:00, a część 2 została uruchomiona o 22:35:00. Wcześniej przedział czasu użył więcej zasobów w tym przedziale czasu niż później (z powodu ulepszeń planu).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Przykładowe wyniki strojenia zapytania](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> Chociaż wolumin w tym przykładzie jest celowo mały, wpływ parametrów optymalnych może być istotny, szczególnie w przypadku większych baz danych. Różnica w skrajnych przypadkach może wynosić od sekund dla szybkich przypadków i godzin w przypadku wolnych przypadków.

Możesz sprawdzić, czy **sys.resource_stats** , aby określić, czy zasób do testu używa więcej lub mniej zasobów niż inny test. Podczas porównywania danych należy oddzielić chronometraż testów, tak aby nie były w tym samym oknie 5-minutowym w widoku **sys.resource_stats** . Celem ćwiczenia jest zminimalizowanie łącznej ilości używanych zasobów, a nie zminimalizowanie zasobów szczytowych. Ogólnie Optymalizacja fragmentu kodu do opóźnienia zmniejsza również zużycie zasobów. Upewnij się, że zmiany wprowadzane do aplikacji są niezbędne i że zmiany nie wpłyną negatywnie na wrażenia klienta dla kogoś, kto może korzystać z podpowiedzi zapytania w aplikacji.

Jeśli obciążenie zawiera zestaw powtarzających się zapytań, często warto przechwycić i sprawdzić Optymalność opcji planu, ponieważ obejmuje ona minimalną jednostkę rozmiaru zasobu wymaganą do hostowania bazy danych programu. Po sprawdzeniu poprawności należy ponownie sprawdzić plany, aby upewnić się, że nie zostały one obniżone. Więcej informacji na temat [wskazówek dotyczących zapytań (Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query).

### <a name="very-large-database-architectures"></a>Bardzo duże architektury baz danych

Przed udostępnieniem warstwy [usługi w](service-tier-hyperscale.md) warstwie górnej dla pojedynczych baz danych w Azure SQL Database klienci korzystający z limitów pojemności dla poszczególnych baz danych. Te limity pojemności nadal istnieją dla baz danych w puli w Azure SQL Database pul elastycznych i bazach danych wystąpień w wystąpieniach zarządzanych usługi Azure SQL. W poniższych dwóch sekcjach omówiono dwie opcje rozwiązywania problemów z bardzo dużymi bazami danych w systemie Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL, gdy nie można użyć warstwy usług.

### <a name="cross-database-sharding"></a>Fragmentowania między bazami danych

Ponieważ Azure SQL Database i usługa Azure SQL Managed instance jest uruchamiana na sprzęcie z asortymentu, limity pojemności dla pojedynczej bazy danych są mniejsze niż dla tradycyjnej instalacji SQL Server lokalnych. Niektórzy klienci używają technik fragmentowania, aby rozłożyć operacje bazy danych na wiele baz danych, gdy operacje nie mieszczą się w granicach pojedynczej bazy danych w Azure SQL Database i wystąpieniu zarządzanym Azure SQL. Większość klientów korzystających z technik fragmentowania w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL dzieli swoje dane w jednym wymiarze w wielu bazach danych. Dla tego podejścia należy zrozumieć, że aplikacje OLTP często wykonują transakcje, które są stosowane tylko do jednego wiersza lub do niewielkiej grupy wierszy w schemacie.

> [!NOTE]
> Azure SQL Database teraz udostępnia bibliotekę ułatwiającą fragmentowania. Aby uzyskać więcej informacji, zobacz [Elastic Database Omówienie biblioteki klienta](elastic-database-client-library.md).

Jeśli na przykład baza danych ma nazwę klienta, zamówienie i szczegółowe informacje o zamówieniu (takie jak tradycyjna Przykładowa baza danych Northwind, która jest dostarczana z SQL Server), można podzielić te dane na wiele baz danych, grupując klienta z pokrewnymi informacjami o zamówieniu i kolejności. Możesz zagwarantować, że dane klienta pozostają w pojedynczej bazie danych. Aplikacja będzie dzielić różne klientów między bazami danych, efektywnie rozłożyć obciążenie między wiele baz danych. W przypadku usługi fragmentowania klienci nie tylko mogą uniknąć maksymalnego limitu rozmiaru bazy danych, ale Azure SQL Database i wystąpienie zarządzane usługi Azure SQL mogą także przetwarzać obciążenia, które są znacznie większe niż limity różnych rozmiarów obliczeniowych, o ile każda z poszczególnych baz danych jest zgodna z limitami warstwy usługi.

Mimo że usługa Database fragmentowania nie zmniejsza zagregowanej pojemności zasobów dla rozwiązania, jest wysoce wydajna, aby obsługiwać bardzo duże rozwiązania, które są rozłożone na wiele baz danych. Każda baza danych może działać z innym rozmiarem obliczeniowym w celu obsługi bardzo dużych, "efektywnych" baz danych o wysokich wymaganiach dotyczących zasobów.

#### <a name="functional-partitioning"></a>Partycjonowanie funkcjonalne

Użytkownicy często łączą wiele funkcji w pojedynczej bazie danych. Na przykład jeśli aplikacja ma logikę do zarządzania zapasami dla magazynu, ta baza danych może być skojarzona z spisem, śledzeniem zamówień zakupu, procedurami składowanymi oraz widokami indeksowanymi lub z materiałami, które zarządzają raportami końcowymi. Ta technika ułatwia administrowanie bazą danych dla operacji, takich jak tworzenie kopii zapasowej, ale wymaga również zmiany rozmiaru sprzętu w celu obsługi szczytowego obciążenia we wszystkich funkcjach aplikacji.

W przypadku używania architektury skalowalnego w poziomie w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL warto podzielić różne funkcje aplikacji do różnych baz danych. Korzystając z tej techniki, każda aplikacja skaluje się niezależnie. Gdy aplikacja stanie się busier (a obciążenie bazy danych wzrasta), administrator może wybrać niezależne rozmiary obliczeń dla każdej funkcji w aplikacji. W ramach tej architektury, aplikacja może być większa niż pojedynczy komputer z asortymentem, może obsłużyć, ponieważ obciążenie jest rozłożone na wiele maszyn.

### <a name="batch-queries"></a>Zapytania wsadowe

W przypadku aplikacji, które uzyskują dostęp do danych przy użyciu dużych, częstych zapytań ad hoc, dużo czasu odpowiedzi poświęca się na komunikację sieciową między warstwą aplikacji a warstwą bazy danych. Nawet jeśli zarówno aplikacja, jak i baza danych znajdują się w tym samym centrum danych, opóźnienie sieci między tymi dwoma może być powiększone przez dużą liczbę operacji dostępu do danych. Aby zmniejszyć liczbę podróży sieci dla operacji dostępu do danych, należy rozważyć użycie opcji do wsadowych zapytań ad hoc lub skompilować je jako procedury składowane. W przypadku wsadowych zapytań ad hoc można wysyłać wiele zapytań jako jedną dużą partię do bazy danych. W przypadku kompilowania zapytań ad hoc w procedurze składowanej można osiągnąć ten sam wynik, jak w przypadku przetwarzania wsadowego. Użycie procedury składowanej umożliwia również zwiększenie ryzyka buforowania planów zapytania w bazie danych, aby można było ponownie użyć procedury składowanej.

Niektóre aplikacje są czasochłonne. Czasami można zmniejszyć łączne obciążenie we/wy w bazie danych, biorąc pod uwagę sposób tworzenia wsadowych zapisów. Często jest to proste użycie jawnych transakcji zamiast transakcji automatycznego zatwierdzania w procedurach składowanych i partiach ad hoc. Aby uzyskać ocenę różnych technik, których można użyć, zobacz [Przetwarzanie wsadowe dla aplikacji bazy danych na platformie Azure](../performance-improve-use-batching.md). Eksperymentuj z własnym obciążeniem, aby znaleźć odpowiedni model na potrzeby tworzenia pakietów wsadowych. Należy pamiętać, że model może mieć nieco inne gwarancje spójności transakcyjnej. Znalezienie odpowiedniego obciążenia, które minimalizuje użycie zasobów, wymaga znalezienia odpowiedniej kombinacji niespójności i wydajności.

### <a name="application-tier-caching"></a>Buforowanie w warstwie aplikacji

Niektóre aplikacje bazy danych mają obciążenia z dużą ilością odczytu. Buforowanie warstw może zmniejszyć obciążenie bazy danych i może zmniejszyć rozmiar obliczeń wymaganych do obsługi bazy danych przy użyciu Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL. W przypadku korzystania z [usługi Azure cache for Redis](https://azure.microsoft.com/services/cache/), jeśli masz obciążenie z dużą ilością danych, możesz odczytywać dane raz (lub na komputerach w warstwie aplikacji, w zależności od konfiguracji), a następnie przechowywać te dane poza bazą danych. Jest to sposób na zmniejszenie obciążenia bazy danych (we/wy), ale istnieje efekt spójności transakcyjnej, ponieważ dane odczytywane z pamięci podręcznej mogą nie być zsynchronizowane z danymi w bazie danych. Chociaż w wielu aplikacjach jest akceptowalny pewien poziom niespójności, to nie jest prawdą dla wszystkich obciążeń. Przed zaimplementowaniem strategii buforowania w warstwie aplikacji należy dokładnie zrozumieć wszystkie wymagania aplikacji.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat warstw usług opartych na jednostkach DTU, zobacz [model zakupu oparty na](service-tiers-dtu.md)jednostkach DTU.
- Aby uzyskać więcej informacji na temat warstw usług opartych na rdzeń wirtualny, zobacz [model zakupów oparty na rdzeń wirtualny](service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat pul elastycznych, zobacz [co to jest pula elastyczna platformy Azure?](elastic-pool-overview.md)
- Aby uzyskać informacje na temat wydajności i pul elastycznych, zobacz [kiedy należy wziąć pod uwagę pulę elastyczną](elastic-pool-overview.md) .