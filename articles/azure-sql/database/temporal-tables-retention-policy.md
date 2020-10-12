---
title: Zarządzanie danymi historycznymi w tabelach danych czasowych
description: Dowiedz się, jak używać zasad przechowywania danych czasowych, aby przechowywać dane historyczne w formancie.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/25/2018
ms.openlocfilehash: 1d68163a9fba3ba3bcd4c0c0f3fb5f442296e781
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619393"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Zarządzanie danymi historycznymi w tabelach danych czasowych przy użyciu zasad przechowywania
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tabele czasowe mogą zwiększyć rozmiar bazy danych więcej niż zwykłe tabele, zwłaszcza w przypadku przechowywania danych historycznych przez dłuższy czas. W związku z tym zasady przechowywania danych historycznych są ważnym aspektem planowania i zarządzania cyklem życia każdej tabeli czasowej. Tabele danych czasowych w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL zapewniają łatwy w użyciu mechanizm przechowywania, który pomaga wykonać to zadanie.

Przechowywanie historii danych czasowych można skonfigurować na poziomie poszczególnych tabel, co pozwala użytkownikom na tworzenie elastycznych zasad przedawnienia. Stosowanie przechowywania danych czasowych jest proste: wymaga ustawienia tylko jednego parametru podczas tworzenia tabeli lub zmiany schematu.

Po zdefiniowaniu zasad przechowywania, Azure SQL Database i wystąpienie zarządzane usługi Azure SQL rozpocznie sprawdzanie regularnie, jeśli istnieją wiersze historyczne kwalifikujące się do automatycznego czyszczenia danych. Identyfikacja pasujących wierszy i ich usunięcie z tabeli historii odbywa się w sposób przezroczysty, w zadaniu w tle, które jest zaplanowane i uruchamiane przez system. Warunki wieku dla wierszy tabeli historii są sprawdzane na podstawie kolumny reprezentującej koniec SYSTEM_TIMEego okresu. Jeśli na przykład okres przechowywania jest ustawiony na sześć miesięcy, wiersze tabeli kwalifikujące się do oczyszczenia spełniają następujący warunek:

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

W poprzednim przykładzie przyjęto, że kolumna **ValidTo** odnosi się do końca okresu SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Jak skonfigurować zasady przechowywania

Przed skonfigurowaniem zasad przechowywania dla tabeli danych czasowych, należy najpierw sprawdzić, czy czas przechowywania historycznego jest włączony *na poziomie bazy*.

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Flaga bazy danych **is_temporal_history_retention_enabled** jest domyślnie ustawiona na wartość włączone, ale użytkownicy mogą ją zmienić przy użyciu instrukcji ALTER DATABASE. Jest on również automatycznie ustawiany jako wyłączony po operacji [przywracania do punktu w czasie](recovery-using-backups.md) . Aby włączyć oczyszczanie historii czasowej przechowywania dla bazy danych, wykonaj następującą instrukcję:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Można skonfigurować przechowywanie dla tabel danych czasowych, nawet jeśli **is_temporal_history_retention_enabled** jest wyłączone, ale w takim przypadku automatyczne czyszczenie dla przestarzałych wierszy nie zostanie wyzwolone.

Zasady przechowywania są konfigurowane podczas tworzenia tabeli przez określenie wartości parametru HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL umożliwiają określenie okresu przechowywania przy użyciu różnych jednostek czasu: dni, tygodnie, miesiące i lata. W przypadku pominięcia HISTORY_RETENTION_PERIOD założono NIESKOŃCZONe przechowywanie. Można również jawnie użyć NIESKOŃCZONego słowa kluczowego.

W niektórych scenariuszach możesz chcieć skonfigurować przechowywanie po utworzeniu tabeli lub zmienić wcześniej skonfigurowaną wartość. W takim przypadku użyj instrukcji ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Ustawienie SYSTEM_VERSIONING wyłączone *nie zachowuje* wartości okresu przechowywania. Ustawienie SYSTEM_VERSIONING na włączone bez HISTORY_RETENTION_PERIOD określone jawnie spowoduje NIESKOŃCZONy okres przechowywania.

Aby sprawdzić bieżący stan zasad przechowywania, należy użyć następującego zapytania, które dołącza flagę włączenia przechowywania danych czasowych na poziomie bazy, z okresami przechowywania dla poszczególnych tabel:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

## <a name="how-ages-rows-are-deleted"></a>Jak są usuwane wiersze w wieku

Proces oczyszczania zależy od układu indeksu tabeli historii. Należy zauważyć, że *tylko tabele historii z indeksem klastrowanym (B-Tree lub magazynu kolumn) mogą mieć skonfigurowane ograniczone zasady przechowywania*. Zadanie w tle jest tworzone w celu przeprowadzenia przestarzałego oczyszczania danych dla wszystkich tabel danych czasowych z skończonego okresu przechowywania.
Logika oczyszczania dla indeksu klastrowanego magazynu wierszy (B-Tree) usuwa przestarzały wiersz w mniejszych fragmentach (do 10 tys.), minimalizując nacisk na dziennik bazy danych i podsystem we/wy. Chociaż logika oczyszczania wykorzystuje wymagany indeks B-drzewa, kolejność usuwania wierszy starszych niż okres przechowywania nie może być gwarantowana. W związku z tym nie należy *podejmować żadnych zależności od kolejności oczyszczania w aplikacjach*.

Zadanie oczyszczania dla klastrowanej magazynu kolumn usuwa jednocześnie wszystkie [grupy wierszy](/sql/relational-databases/indexes/columnstore-indexes-overview) (zazwyczaj zawierają 1 000 000 wierszy każdego), co jest bardzo wydajne, szczególnie gdy dane historyczne są generowane w dużym tempie.

![Przechowywanie klastrowanej magazynu kolumn](./media/temporal-tables-retention-policy/cciretention.png)

Doskonałej kompresji danych i wydajne oczyszczanie przechowywania sprawia, że klastrowany indeks magazynu kolumn jest idealnym wyborem dla scenariuszy, gdy obciążenie szybko generuje duże ilości danych historycznych. Ten wzorzec jest typowy dla intensywnych [obciążeń przetwarzania transakcyjnego, które używają tabel](/sql/relational-databases/tables/temporal-table-usage-scenarios) czasowych do śledzenia zmian i inspekcji, analizy trendów lub pozyskiwania danych IoT.

## <a name="index-considerations"></a>Zagadnienia dotyczące indeksów

Zadanie oczyszczania tabel z indeksem klastrowanym magazynu wierszy wymaga, aby indeks rozpoczynał się od kolumny odpowiadającej końcowi okresu SYSTEM_TIME. Jeśli taki indeks nie istnieje, nie można skonfigurować skończonego okresu przechowywania:

*Msg 13765, Level 16, State 1 <br> </br> Ustawianie skończonego okresu przechowywania w tabeli danych czasowych z systemową obsługą wersji "temporalstagetestdb. dbo. WebsiteUserInfo", ponieważ tabela historii "temporalstagetestdb. dbo. WebsiteUserInfoHistory" nie zawiera wymaganego indeksu klastrowanego. Rozważ utworzenie klastrowanego magazynu kolumn lub spisu B-drzewa, rozpoczynając od kolumny, która jest zgodna z końcem SYSTEM_TIME okresu, w tabeli historii.*

Należy pamiętać, że domyślna tabela historii utworzona przez Azure SQL Database i wystąpienie zarządzane Azure SQL ma już indeks klastrowany, który jest zgodny z zasadami przechowywania. Jeśli spróbujesz usunąć ten indeks z tabeli z ograniczonym okresem przechowywania, operacja kończy się niepowodzeniem z powodu następującego błędu:

*Komunikat 13766, poziom 16, stan 1 <br> </br> nie może porzucić klastrowanego indeksu "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory", ponieważ jest on używany do automatycznego czyszczenia przestarzałych danych. Należy rozważyć ustawienie nieograniczonej HISTORY_RETENTION_PERIOD w odpowiedniej tabeli danych czasowych z systemową obsługą wersji, jeśli trzeba będzie usunąć ten indeks.*

Czyszczenie na klastrowanym indeksie magazynu kolumn działa optymalnie, jeśli wiersze historyczne są wstawiane w kolejności rosnącej (uporządkowane według końca kolumny okresu), która zawsze jest uwzględniana w przypadku, gdy tabela historii zostanie wypełniona wyłącznie przez mechanizm SYSTEM_VERSIONIOING. Jeśli wiersze w tabeli historii nie są uporządkowane według kolumny końca okresu (w przypadku przeprowadzenia migracji istniejących danych historycznych), należy ponownie utworzyć klastrowany indeks magazynu kolumn na podstawie indeksu B-Tree magazynu wierszy, który jest prawidłowo uporządkowany, aby osiągnąć optymalną wydajność.

Unikaj odbudowywania klastrowanego indeksu magazynu kolumn w tabeli historii przy użyciu skończonego okresu przechowywania, ponieważ może to zmienić kolejność w grupach wierszy, które są w sposób naturalny nałożone przez operację obsługi wersji systemu. Jeśli musisz ponownie skompilować klastrowany indeks magazynu kolumn w tabeli historii, zrób to przez ponowne utworzenie go na podstawie zgodnego indeksu B-drzewa, zachowując kolejność w RowGroups koniecznym do regularnego oczyszczania danych. Takie samo podejście należy wykonać w przypadku utworzenia tabeli danych czasowych z istniejącą tabelą historii, która ma klastrowany indeks kolumn bez gwarantowanej kolejności danych:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

W przypadku skonfigurowania skończonego okresu przechowywania w tabeli historii z klastrowanym indeksem magazynu kolumn nie można utworzyć dodatkowych indeksów nieklastrowanych B-drzewa w tej tabeli:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Próba wykonania powyższej instrukcji kończy się niepowodzeniem z powodu następującego błędu:

*Msg 13772, Level 16, State 1 <br> </br> nie można utworzyć indeksu nieklastrowanego w tabeli historii danych czasowych "WebsiteUserInfoHistory", ponieważ ma on ograniczony okres przechowywania i zdefiniowany klastrowany indeks magazynu kolumn.*

## <a name="querying-tables-with-retention-policy"></a>Wykonywanie zapytań dotyczących tabel przy użyciu zasad przechowywania

Wszystkie zapytania w tabeli danych czasowych automatycznie filtrują wiersze historyczne pasujące do skończonej zasady przechowywania, aby uniknąć nieprzewidywalnych i niespójnych wyników, ponieważ w dowolnym momencie zadanie oczyszczania może usunąć przestarzałe wiersze z dowolnego momentu *i w dowolnej kolejności*.

Na poniższej ilustracji przedstawiono plan zapytania dla prostego zapytania:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Plan zapytania obejmuje dodatkowy filtr zastosowany do kolumny końca okresu (ValidTo) w operatorze skanowania indeksu klastrowanego w tabeli historii (wyróżniony). W tym przykładzie przyjęto założenie, że w tabeli WebsiteUserInfo ustawiono jeden miesiąc okresu przechowywania.

![Filtr zapytania przechowywania](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

Jednak w przypadku bezpośredniej kwerendy tabeli historii można zobaczyć wiersze, które są starsze niż określony okres przechowywania, ale bez żadnych gwarancji dla powtarzających się wyników zapytania. Na poniższej ilustracji przedstawiono plan wykonywania zapytania dla zapytania w tabeli historii bez zastosowania dodatkowych filtrów:

![Wykonywanie zapytania dotyczącego historii bez filtru przechowywania](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

Nie należy polegać na logice biznesowej podczas odczytywania tabeli historii poza okresem przechowywania, ponieważ mogą być niespójne lub nieoczekiwane wyniki. Zalecamy używanie zapytań czasowych z klauzulą FOR SYSTEM_TIME, aby analizować dane w tabelach danych czasowych.

## <a name="point-in-time-restore-considerations"></a>Uwagi dotyczące przywracania do punktu w czasie

Podczas tworzenia nowej bazy danych przez [przywrócenie istniejącej bazy danych do określonego punktu w czasie, okresowe](recovery-using-backups.md)przechowywanie jest wyłączone na poziomie bazy danych. (flaga**is_temporal_history_retention_enabled** ustawiona na off). Ta funkcja pozwala zbadać wszystkie wiersze historyczne podczas przywracania, bez obaw przed usunięciem przestarzałych wierszy przed uzyskaniem zapytania. Za jego pomocą można *sprawdzić dane historyczne poza skonfigurowanym okresem przechowywania*.

Załóżmy, że dla tabeli danych czasowych określono jeden miesiąc okresu przechowywania. Jeśli baza danych została utworzona w warstwie usługi Premium, można utworzyć kopię bazy danych z stanem bazy danych do 35 dni wstecz w przeszłości. Dzięki temu można analizować wiersze historyczne, które są do 65 dni przed upływem zapytania bezpośrednio do tabeli historii.

Jeśli chcesz uaktywnić czyszczenie danych czasowych, uruchom następującą instrukcję Transact-SQL po przywróceniu punktu w czasie:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać tabel danych czasowych w aplikacjach, Wyewidencjonuj [wprowadzenie z tabelami](../temporal-tables.md)czasowymi.

Odwiedź stronę Channel 9, aby poznać [historię sukcesu wykonywania](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) danych czasowych klienta i obejrzyj [prezentację](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)czasową na żywo.

Aby uzyskać szczegółowe informacje o tabelach danych czasowych, przejrzyj [tabele](/sql/relational-databases/tables/temporal-tables)danych czasowych.
