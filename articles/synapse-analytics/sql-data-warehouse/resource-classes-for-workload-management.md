---
title: Klasy zasobów do zarządzania obciążeniami
description: Wskazówki dotyczące korzystania z klas zasobów do zarządzania zasobami współbieżności i zasobów obliczeniowych dla zapytań w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a557d4045b18b5c0ff71b3e47f0c189028702863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289535"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Zarządzanie obciążeniami przy użyciu klas zasobów w usłudze Azure Synapse Analytics

Wskazówki dotyczące używania klas zasobów do zarządzania pamięcią i współbieżnością dla Synapseych zapytań puli SQL w usłudze Azure Synapse.  

## <a name="what-are-resource-classes"></a>Co to są klasy zasobów

Wydajność zapytania jest określana na podstawie klasy zasobów użytkownika.  Klasy zasobów to wstępnie określone limity zasobów w puli Synapse SQL, które regulują zasoby obliczeniowe i współbieżność na potrzeby wykonywania zapytań. Klasy zasobów mogą pomóc w konfigurowaniu zasobów dla zapytań przez ustawienie limitów liczby zapytań, które są uruchamiane współbieżnie i w zasobach obliczeniowych przypisanych do każdego zapytania.  Istnieje kompromis między ilością pamięci i współbieżnością.

- Mniejsze klasy zasobów zmniejszają maksymalną ilość pamięci na zapytanie, ale zwiększają współbieżność.
- Większe klasy zasobów zwiększają maksymalną ilość pamięci na zapytanie, ale zmniejszają współbieżność.

Istnieją dwa typy klas zasobów:

- Klasy zasobów statycznych, które są dobrze dopasowane w celu zwiększenia współbieżności dla rozmiaru zestawu danych, który został rozwiązany.
- Dynamiczne klasy zasobów, które są odpowiednie dla zestawów danych, które zwiększają rozmiar i potrzebują zwiększonej wydajności w miarę skalowania poziomu usługi.

Klasy zasobów używają miejsc współbieżności do mierzenia zużycia zasobów.  [Gniazda współbieżności](#concurrency-slots) są wyjaśnione w dalszej części tego artykułu.

- Aby wyświetlić wykorzystanie zasobów dla klas zasobów, zobacz [limity pamięci i współbieżności](memory-concurrency-limits.md).
- Aby dostosować klasę zasobów, można uruchomić zapytanie w ramach innego użytkownika lub [zmienić członkostwo klasy zasobów bieżącego użytkownika](#change-a-users-resource-class) .

### <a name="static-resource-classes"></a>Statyczne klasy zasobów

Statyczne klasy zasobów przydzielą tę samą ilość pamięci niezależnie od bieżącego poziomu wydajności, który jest mierzony w [jednostkach magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). Ponieważ zapytania pobierają ten sam przydział pamięci niezależnie od poziomu wydajności, [skalowanie magazynu danych](quickstart-scale-compute-portal.md) umożliwia wykonywanie większej liczby zapytań w obrębie klasy zasobów.  Statyczne klasy zasobów są idealnym rozwiązaniem, jeśli wolumin danych jest znany i stały.

Statyczne klasy zasobów są implementowane przy użyciu tych wstępnie zdefiniowanych ról bazy danych:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamiczne klasy zasobów

Dynamiczne klasy zasobów przydzielą zmienną ilość pamięci w zależności od bieżącego poziomu usługi. Chociaż statyczne klasy zasobów są korzystne dla wyższych woluminów danych współbieżności i statycznych, dynamiczne klasy zasobów są lepiej dopasowane do rosnącej lub zmiennej ilości danych.  Skalowanie w górę do większego poziomu usługi powoduje, że zapytania automatycznie uzyskają więcej pamięci.  

Dynamiczne klasy zasobów są implementowane przy użyciu tych wstępnie zdefiniowanych ról bazy danych:

- smallrc
- mediumrc
- largerc
- xlargerc

Alokacja pamięci dla każdej klasy zasobów jest następująca.

| Poziom usługi  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12,5%             | 12,5%                  | 22                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22                    | 70%                    |
| DW400c         | 6,25%             | 10%                    | 22                    | 70%                    |
| DW500c.         | 5%                | 10%                    | 22                    | 70%                    |
| DW1000c do<br> DW30000c | 3%       | 10%                    | 22                    | 70%                    |

### <a name="default-resource-class"></a>Domyślna klasa zasobów

Domyślnie każdy użytkownik jest członkiem klasy zasobów dynamicznych **smallrc**.

Klasa zasobów administratora usługi ma stałą wartość smallrc i nie można jej zmienić.  Administrator usługi jest użytkownikiem utworzonym podczas procesu aprowizacji.  Administrator usługi w tym kontekście to nazwa logowania określona dla "logowania administratora serwera" podczas tworzenia nowej puli SQL Synapse z nowym serwerem.

> [!NOTE]
> Użytkownicy lub grupy zdefiniowane jako administrator Active Directory są również administratorami usługi.
>
>

## <a name="resource-class-operations"></a>Operacje klasy zasobów

Klasy zasobów zaprojektowano w celu poprawy wydajności zarządzania danymi i manipulowania nimi. Skomplikowane zapytania mogą również korzystać z uruchamiania w ramach dużej klasy zasobów. Na przykład wydajność zapytań dla dużych sprzężeń i sortowania może poprawić, gdy Klasa zasobów jest wystarczająco duża, aby umożliwić wykonanie zapytania w pamięci.

### <a name="operations-governed-by-resource-classes"></a>Operacje regulowane przez klasy zasobów

Te operacje podlegają klasom zasobów:

- WSTAW — WYBIERZ, AKTUALIZUJ, USUŃ
- Wybierz (podczas wykonywania zapytania dotyczącego tabel użytkowników)
- Zmień indeks — Odbudowywanie lub Reorganizowanie
- ZMIEŃ PONOWNĄ KOMPILACJĘ TABELI
- CREATE INDEX
- UTWÓRZ KLASTROWANY INDEKS MAGAZYNU KOLUMN
- CREATE TABLE JAKO SELECT (CTAS)
- Ładowanie danych
- Operacje przenoszenia danych wykonywane przez usługę przenoszenia danych (DMS)

> [!NOTE]  
> Instrukcje SELECT w widokach zarządzania dynamicznego (widoków DMV) lub inne widoki systemowe nie podlegają żadnym ograniczeniom współbieżności. System można monitorować niezależnie od liczby wykonywanych zapytań.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Operacje nieregulowane przez klasy zasobów

Niektóre zapytania są zawsze uruchamiane w klasie zasobów smallrc, mimo że użytkownik jest członkiem większej klasy zasobów. Te wykluczone zapytania nie są wliczane do limitu współbieżności. Na przykład jeśli limit współbieżności wynosi 16, wielu użytkowników może wybierać z widoków systemowych bez wpływania na dostępne gniazda współbieżności.

Następujące instrukcje są wykluczone z klas zasobów i zawsze uruchamiane w smallrc:

- Utwórz lub upuść tabelę
- ZMIEŃ TABELĘ... Przełączanie, dzielenie lub SCALAnie partycji
- WYŁĄCZANIE ZMIANY INDEKSU
- DROP INDEX
- Tworzenie, aktualizowanie lub porzucanie statystyk
- TRUNCATE TABLE
- ZMIEŃ AUTORYZACJĘ
- UTWÓRZ NAZWĘ LOGOWANIA
- Tworzenie, zmienianie lub porzucanie użytkownika
- PROCEDURA CREATE, ALTER lub DROP
- Utwórz lub upuść widok
- WSTAW WARTOŚCI
- Wybierz z widoków systemowych i widoków DMV
- UZASADNIENI
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Gniazda współbieżności

Gniazda współbieżności to wygodny sposób śledzenia zasobów dostępnych na potrzeby wykonywania zapytań. Są one podobne do biletów zakupionych w celu zarezerwowania miejsc, ponieważ jest ograniczone. Łączna liczba miejsc współbieżności na magazyn danych jest określana na podstawie poziomu usługi. Aby można było rozpocząć wykonywanie zapytania, musi być w stanie zarezerwować wystarczającą liczbę miejsc współbieżności. Po zakończeniu zapytania zwalnia jego miejsca współbieżności.  

- Zapytanie działające z 10 miejsc współbieżności może uzyskać dostęp do 5 razy więcej zasobów obliczeniowych niż zapytanie działające z 2 miejscami współbieżności.
- Jeśli każde zapytanie wymaga 10 miejsc współbieżności, a istnieją 40 miejsc współbieżności, można jednocześnie uruchamiać tylko 4 zapytania.

Tylko zapytania z zarządzanymi zasobami używają miejsc współbieżności. Zapytania systemowe i niektóre proste zapytania nie zużywają żadnych miejsc. Dokładna liczba zużytych miejsc współbieżności jest określana przez klasę zasobów zapytania.

## <a name="view-the-resource-classes"></a>Wyświetlanie klas zasobów

Klasy zasobów są implementowane jako wstępnie zdefiniowane role baz danych. Istnieją dwa typy klas zasobów: dynamiczne i statyczne. Aby wyświetlić listę klas zasobów, należy użyć następującej kwerendy:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Zmienianie klasy zasobów użytkownika

Klasy zasobów są implementowane przez przypisanie użytkowników do ról bazy danych. Gdy użytkownik uruchamia zapytanie, zapytanie jest uruchamiane z klasą zasobów użytkownika. Na przykład, jeśli użytkownik jest członkiem roli bazy danych staticrc10, ich zapytania są uruchamiane z niewielkimi ilościami pamięci. Jeśli użytkownik bazy danych jest członkiem roli bazy danych xlargerc lub staticrc80, ich zapytania działają z dużą ilością pamięci.

Aby zwiększyć klasę zasobów użytkownika, należy użyć [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby dodać użytkownika do roli bazy danych dla dużej klasy zasobów.  Poniższy kod dodaje użytkownika do roli bazy danych largerc.  Każde żądanie otrzymuje 22% pamięci systemowej.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Aby zmniejszyć klasę zasobów, użyj [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Jeśli "loaduser" nie jest członkiem ani innymi klasami zasobów, przychodzą do domyślnej klasy zasobów smallrc z przyznanym pamięcią 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Pierwszeństwo klasy zasobów

Użytkownicy mogą być członkami wielu klas zasobów. Gdy użytkownik należy do więcej niż jednej klasy zasobów:

- Dynamiczne klasy zasobów mają pierwszeństwo przed klasami zasobów statycznych. Na przykład, jeśli użytkownik jest członkiem obu mediumrc (dynamicznych) i staticrc80 (static), zapytania są uruchamiane z mediumrc.
- Większe klasy zasobów mają pierwszeństwo przed klasami mniejszych zasobów. Na przykład jeśli użytkownik jest członkiem mediumrc i largerc, zapytania są uruchamiane z largerc. Podobnie, jeśli użytkownik jest członkiem zarówno staticrc20, jak i statirc80, zapytania uruchamiają się przy użyciu alokacji zasobów staticrc80.

## <a name="recommendations"></a>Zalecenia

>[!NOTE]
>Rozważ użycie funkcji zarządzania obciążeniami ([izolacja obciążenia](sql-data-warehouse-workload-isolation.md), [klasyfikacji](sql-data-warehouse-workload-classification.md) i [ważności](sql-data-warehouse-workload-importance.md)), aby uzyskać większą kontrolę nad obciążeniem i przewidywalną wydajnością.  
>
>

Zalecamy utworzenie użytkownika, który jest przeznaczony do uruchamiania określonego typu zapytania lub operacji ładowania. Nadaj temu użytkownikowi trwałą klasę zasobów zamiast zmiany klasy zasobów. Statyczne klasy zasobów zapewniają większą ogólną kontrolę nad obciążeniem, dlatego zalecamy korzystanie z klas zasobów statycznych przed uwzględnieniem klas zasobów dynamicznych.

### <a name="resource-classes-for-load-users"></a>Klasy zasobów do załadowania użytkowników

`CREATE TABLE` domyślnie używa klastrowanych indeksów magazynu kolumn. Kompresowanie danych do indeksu magazynu kolumn jest operacją intensywnie dotyczącą pamięci, a wykorzystanie pamięci może zmniejszyć jakość indeksu. Wykorzystanie pamięci może prowadzić do potrzeb wyższych klas zasobów podczas ładowania danych. Aby zapewnić, że ładunki mają wystarczającą ilość pamięci, można utworzyć użytkownika, który jest przeznaczony do uruchamiania obciążeń i przypisać tego użytkownika do wyższej klasy zasobów.

Pamięć wymagana do wydajnego przetwarzania obciążeń zależy od rodzaju załadowanej tabeli i rozmiaru danych. Aby uzyskać więcej informacji o wymaganiach dotyczących pamięci, zobacz [maksymalizacja jakości grupy wierszy](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Po ustaleniu wymagania dotyczącego pamięci Zdecyduj, czy użytkownik ma zostać przypisany do statycznej czy dynamicznej klasy zasobów.

- Użyj statycznej klasy zasobów, gdy wymagania dotyczące pamięci tabeli mieszczą się w określonym zakresie. Ładowania są wykonywane z odpowiednią ilością pamięci. Podczas skalowania magazynu danych obciążenia nie potrzebują większej ilości pamięci. Przy użyciu statycznej klasy zasobów alokacje pamięci pozostają stałe. Ta spójność chroni pamięć i umożliwia równoczesne uruchamianie większej liczby zapytań. Zalecamy, aby nowe rozwiązania używały najpierw klas zasobów statycznych, ponieważ zapewniają one większą kontrolę.
- Użyj dynamicznej klasy zasobów, gdy wymagania dotyczące pamięci tabeli różnią się znacznie. Obciążenia mogą wymagać większej ilości pamięci niż na bieżącym poziomie jednostek dwu lub cDWU. Skalowanie magazynu danych zwiększa ilość pamięci do operacji ładowania, co umożliwia szybsze wykonywanie obciążeń.

### <a name="resource-classes-for-queries"></a>Klasy zasobów dla zapytań

Niektóre zapytania są czasochłonne, a niektóre nie.  

- Wybierz dynamiczną klasę zasobów, gdy zapytania są złożone, ale nie potrzebują dużej współbieżności.  Na przykład generowanie raportów codziennych lub cotygodniowych jest okazjonalnym zapotrzebowaniem na zasoby. Jeśli raporty przetwarzają duże ilości danych, skalowanie magazynu danych zapewnia większą ilość pamięci dla istniejącej klasy zasobów użytkownika.
- Wybierz klasę zasobów statycznych, gdy oczekiwania zasobów różnią się w ciągu dnia. Na przykład, statyczna klasa zasobów działa dobrze, gdy magazyn danych jest wysyłany przez wiele osób. W przypadku skalowania magazynu danych ilość pamięci przydzieloną dla użytkownika nie ulega zmianie. W związku z tym więcej zapytań można wykonać równolegle w systemie.

Odpowiednie dotacje pamięci są zależne od wielu czynników, takich jak ilość danych, które są zapytania, charakter schematów tabeli oraz różne sprzężenia, wyboru i predykatów grup. Ogólnie rzecz biorąc, przydzielanie większej ilości pamięci pozwala na szybsze wykonywanie zapytań, ale zmniejsza ogólną współbieżność. Jeśli współbieżność nie jest problemem, nadmierna alokacja pamięci nie szkodzi w przepływności.

Aby dostosować wydajność, Użyj różnych klas zasobów. Następna sekcja zawiera procedurę składowaną, która ułatwia ustalenie najlepszej klasy zasobów.

## <a name="example-code-for-finding-the-best-resource-class"></a>Przykładowy kod służący do znajdowania najlepszej klasy zasobów

Można użyć następującej określonej procedury składowanej, aby ustalić użycie współbieżności i pamięci na klasę zasobów w danym przewyższym i najlepszej klasie zasobów dla operacji WIK z dużą ilością pamięci w tabeli WIK niepartycjonowanej w danej klasie zasobów:

Oto przeznaczenie tej procedury składowanej:

1. Aby zobaczyć, jak współbieżność i przydział pamięci według klasy zasobów w danym przewyższym. Użytkownik musi podać wartość NULL dla schematu i tabeliname, jak pokazano w tym przykładzie.  
2. Aby wyświetlić najlepszą klasę zasobów dla operacji WIK intensywnie korzystających z pamięci (ładowanie, kopiowanie tabeli, ponowne kompilowanie indeksu itp.) w tabeli WIK niepartycjonowanej w danej klasie zasobów. Procedura składowana używa schematu tabeli, aby sprawdzić wymagane przydzielenie pamięci.

### <a name="dependencies--restrictions"></a>Ograniczenia & ograniczeń

- Ta procedura składowana nie została zaprojektowana w celu obliczenia wymagania dotyczącego pamięci dla partycjonowanej tabeli WIK.
- Ta procedura składowana nie wymaga wymagania dotyczącego pamięci dla wybranych części CTAS/INSERT-SELECT i zakłada, że jest to wybór.
- Ta procedura składowana używa tabeli tymczasowej, która jest dostępna w sesji, w której została utworzona ta procedura składowana.
- Ta procedura składowana jest zależna od bieżącej oferty (na przykład konfiguracja sprzętowa, DMS config), a jeśli jakiekolwiek z nich ulegną zmianie, ten proces przechowywany nie będzie działał poprawnie.  
- Ta procedura składowana jest zależna od istniejących ofert ograniczeń współbieżności i jeśli te zmiany następnie nie będą działały prawidłowo.  
- Ta procedura składowana jest zależna od istniejących ofert klasy zasobów i jeśli te zmiany następnie nie będą działały prawidłowo.  

>[!NOTE]  
>Jeśli dane wyjściowe nie są uzyskiwane po wykonaniu procedury składowanej z podanymi parametrami, mogą wystąpić dwa przypadki.
>
>1. Parametr DW zawiera nieprawidłową wartość SLO
>2. Lub nie ma żadnej zgodnej klasy zasobów dla operacji WIK w tabeli.
>
>Na przykład w DW100c najwyższy możliwy dostęp do pamięci wynosi 1 GB, a jeśli schemat tabeli jest wystarczająco szeroki, aby przekroczyć wymaganie 1 GB.

### <a name="usage-example"></a>Przykład użycia

Składnia:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Podaj parametr o wartości NULL, aby wyodrębnić bieżącą jednostek dwu z bazy danych DW lub podać dowolne obsługiwane jednostek dwu w postaci "DW100c"
2. @SCHEMA_NAME: Podaj nazwę schematu tabeli
3. @TABLE_NAME: Podaj nazwę interesującej tabeli

Przykłady wykonywania tego procesu przechowywanego:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Poniższa instrukcja tworzy tabelę Tabela1, która jest używana w powyższych przykładach.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definicja procedury składowanej

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania użytkownikami i zabezpieczeniami bazy danych, zobacz temat [Zabezpieczanie bazy danych w programie Synapse SQL](sql-data-warehouse-overview-manage-security.md). Aby uzyskać więcej informacji o tym, jak większe klasy zasobów mogą ulepszyć jakość klastrowanego indeksu magazynu kolumn, zobacz [optymalizacje pamięci dla kompresji magazynu kolumn](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).
