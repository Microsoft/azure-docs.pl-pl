---
title: Jak zwiększyć wydajność aplikacji za pomocą tworzenia pakietów wsadowych
description: Temat zawiera dowody, że przetwarzanie wsadowe operacji bazy danych znacznie poprawia szybkość i skalowalność Azure SQL Database i aplikacji wystąpienia zarządzanego Azure SQL. Chociaż te techniki wsadowe działają dla dowolnej bazy danych SQL, fokus artykułu znajduje się na platformie Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 7f45e7d1515f0d6fc4467b36d95242ef8697c75d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641392"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Korzystanie z usługi Batch w celu usprawnienia Azure SQL Database i wydajności aplikacji wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Przetwarzanie wsadowe operacji na Azure SQL Database i wystąpienie zarządzane usługi Azure SQL znacznie zwiększa wydajność i skalowalność aplikacji. Aby zrozumieć korzyści, w pierwszej części tego artykułu omówiono niektóre przykładowe wyniki testów, które porównują sekwencyjne i wsadowe żądania do bazy danych w Azure SQL Database lub wystąpienia zarządzanego Azure SQL. W pozostałej części artykułu przedstawiono techniki, scenariusze i zagadnienia, które ułatwią pomyślne użycie usługi Batch w aplikacjach platformy Azure.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Dlaczego usługa Batch jest ważna dla Azure SQL Database i wystąpienia zarządzanego Azure SQL?

Tworzenie wsadowe wywołań usługi zdalnej jest dobrze znaną strategią w celu zwiększenia wydajności i skalowalności. Istnieją stałe koszty przetwarzania w przypadku interakcji z usługą zdalną, takie jak Serializacja, transfer sieciowy i deserializacja. Pakowanie wielu oddzielnych transakcji w ramach jednej partii minimalizuje te koszty.

W tym artykule chcemy poznać różne strategie i scenariusze przetwarzania wsadowego. Chociaż te strategie są również ważne w przypadku aplikacji lokalnych, które używają SQL Server, istnieje kilka powodów, dla których należy wyróżnić użycie partii dla Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL:

* Dostęp do Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL jest potencjalnie większy, szczególnie w przypadku dostępu do Azure SQL Database lub wystąpienia zarządzanego Azure SQL spoza tego samego centrum danych Microsoft Azure.
* Wielodostępne cechy Azure SQL Database i wystąpienia zarządzanego Azure SQL to wydajność warstwy dostępu do danych, która jest skorelowana z ogólną skalowalnością bazy danych. W odpowiedzi na użycie poza wstępnie zdefiniowanymi przydziałami Azure SQL Database i wystąpienie zarządzane usługi Azure SQL mogą zmniejszyć przepływność lub odpowiedzieć na wyjątki ograniczające. Zwiększenie wydajności, takie jak przetwarzanie wsadowe, pozwala wykonywać więcej zadań przed osiągnięciem tych limitów.
* Przetwarzanie wsadowe jest również skuteczne dla architektury, które używają wielu baz danych (fragmentowania). Wydajność interakcji z każdą jednostką bazy danych jest nadal kluczowym czynnikiem w ogólnej skalowalności.

Jedną z zalet używania Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL jest to, że nie trzeba zarządzać serwerami, które obsługują bazę danych. Jednak ta infrastruktura zarządzana oznacza również, że trzeba myśleć o optymalizacji baz danych. Nie można już wyszukać poprawy jakości sprzętu lub infrastruktury sieciowej. Microsoft Azure kontroluje te środowiska. Głównym obszarem, który można kontrolować, jest interakcja aplikacji z Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL. Przetwarzanie wsadowe jest jedną z tych optymalizacji.

W pierwszej części tego artykułu są badane różne techniki wsadowe dla aplikacji platformy .NET, które używają Azure SQL Database lub wystąpienia zarządzanego Azure SQL. W ostatnich dwóch sekcjach omówiono wytyczne i scenariusze dotyczące przetwarzania wsadowego.

## <a name="batching-strategies"></a>Strategie przetwarzania wsadowego

### <a name="note-about-timing-results-in-this-article"></a>Uwaga dotycząca chronometrażu wyników w tym artykule

> [!NOTE]
> Wyniki nie są wzorcem, ale są przeznaczone do wyświetlania **względnej wydajności**. Chronometraż jest oparty na średniej z co najmniej 10 przebiegów testowych. Operacje są wstawiane do pustej tabeli. Te testy zostały zmierzone przed V12 i nie muszą odpowiadać przepływności, które można napotkać w bazie danych V12 przy użyciu nowych [warstw usług DTU](database/service-tiers-dtu.md) lub [rdzeń wirtualny usługi](database/service-tiers-vcore.md). Względne korzyści wynikające z techniki wsadowej powinny być podobne.

### <a name="transactions"></a>Transakcje

Wydaje się, że nie można rozpocząć przeglądu partii, omawiając transakcje. Jednak użycie transakcji po stronie klienta ma delikatny efekt wsadowy po stronie serwera, który zwiększa wydajność. I można dodawać transakcje tylko za pomocą kilku wierszy kodu, dzięki czemu zapewniają szybki sposób poprawy wydajności operacji sekwencyjnych.

Rozważmy następujący kod w języku C#, który zawiera sekwencję operacji INSERT i Update w prostej tabeli.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

Poniższy kod ADO.NET wykonuje sekwencyjnie te operacje.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Najlepszym sposobem na optymalizację tego kodu jest zaimplementowanie pewnej postaci wsadowej po stronie klienta tych wywołań. Jednak istnieje prosty sposób zwiększenia wydajności tego kodu przez po prostu zapakowanie sekwencji wywołań w transakcji. Poniżej znajduje się ten sam kod, który używa transakcji.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Transakcje są faktycznie używane w obu tych przykładach. W pierwszym przykładzie każde pojedyncze wywołanie jest niejawną transakcją. W drugim przykładzie transakcja jawna otacza wszystkie wywołania. Zgodnie z dokumentacją [dziennika transakcji zapisu](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15&preserve-view=true#WAL)rekordy dziennika są opróżniane do dysku podczas zatwierdzania transakcji. Tak więc, dołączając więcej wywołań w transakcji, zapis w dzienniku transakcji może opóźnić się do momentu zatwierdzenia transakcji. W efekcie w dzienniku transakcji serwera jest włączana partia operacji zapisu.

W poniższej tabeli przedstawiono niektóre wyniki testów ad hoc. Testy przeprowadzono te same sekwencyjne Wstawianie z i bez transakcji. W celu uzyskania większej perspektywy pierwszy zestaw testów działał zdalnie z poziomu laptopa do bazy danych w Microsoft Azure. Drugi zestaw testów został uruchomiony z poziomu usługi w chmurze i bazy danych, która znajduje się w tym samym Microsoft Azure Datacenter (Zachodnie stany USA). W poniższej tabeli przedstawiono czas trwania operacji wstawiania sekwencyjnego z i bez transakcji w milisekundach.

**Lokalne na platformę Azure**:

| Operacje | Brak transakcji (MS) | Transakcja (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure na platformę Azure (to samo centrum danych)**:

| Operacje | Brak transakcji (MS) | Transakcja (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Wyniki nie są wzorcem. Zobacz [uwagi dotyczące chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

W oparciu o poprzednie wyniki testów, otoka pojedynczej operacji w transakcji w rzeczywistości zmniejsza wydajność. Ale w miarę zwiększania liczby operacji w ramach pojedynczej transakcji zwiększenie wydajności zostanie wyróżnione. Różnica wydajności jest również bardziej zauważalna, gdy wszystkie operacje są wykonywane w ramach Microsoft Azure centrum danych. Zwiększony czas oczekiwania na użycie Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL z zewnątrz poza centrum danych Microsoft Azure przesłonięcia wydajności przez korzystanie z transakcji.

Chociaż użycie transakcji może zwiększyć wydajność, nadal należy [przestrzegać najlepszych rozwiązań dotyczących transakcji i połączeń](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105)). Zachowaj transakcję jak najszybciej i Zamknij połączenie z bazą danych po zakończeniu pracy. Instrukcja Using w poprzednim przykładzie gwarantuje, że połączenie jest zamknięte po zakończeniu kolejnego bloku kodu.

W poprzednim przykładzie pokazano, że można dodać transakcję lokalną do dowolnego kodu ADO.NET z dwoma wierszami. Transakcje oferują szybki sposób na zwiększenie wydajności kodu, który wykonuje sekwencyjne operacje wstawiania, aktualizowania i usuwania. Jednak w celu uzyskania najszybszej wydajności Rozważ zmianę kodu w celu wykorzystania operacji wsadowych po stronie klienta, takich jak parametry z wartościami przechowywanymi w tabeli.

Aby uzyskać więcej informacji o transakcjach w programie ADO.NET, zobacz [lokalne transakcje w ADO.NET](/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parametry z wartościami przechowywanymi w tabeli

Parametry z wartościami przechowywanymi w tabeli obsługują typy tabel zdefiniowane przez użytkownika jako parametry w instrukcjach języka Transact-SQL, procedurach składowanych i funkcjach. Ta technika wsadowa po stronie klienta umożliwia wysyłanie wielu wierszy danych w ramach parametru z wartościami przechowywanymi w tabeli. Aby użyć parametrów z wartościami przechowywanymi w tabeli, należy najpierw zdefiniować typ tabeli. Poniższa instrukcja języka Transact-SQL tworzy typ tabeli o nazwie **Webtabletype**.

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

W kodzie można utworzyć **DataTable** o dokładnie takiej samej nazwie i typach typu tabeli. Przekaż tę tabelę **DataTable** do parametru w zapytaniu tekstowym lub wywołaniu procedury przechowywanej. W poniższym przykładzie przedstawiono tę technikę:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

W poprzednim przykładzie obiekt **SqlCommand** wstawia wiersze z parametru z wartościami przechowywanymi w tabeli, **\@ TestTvp**. Wcześniej utworzony obiekt **DataTable** jest przypisany do tego parametru za pomocą metody **SqlCommand. Parameters. Add** . Przetwarzanie wsadowe operacji wstawiania w jednym wywołaniu znacznie zwiększa wydajność nad wstawianiem sekwencyjnym.

Aby dodatkowo poprawić poprzedni przykład, użyj procedury składowanej zamiast polecenia tekstowego. Poniższe polecenie języka Transact-SQL tworzy procedurę składowaną, która przyjmuje **SimpleTestTableType** parametr z wartościami przechowywanymi w tabeli.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

Następnie zmień deklarację obiektu **SqlCommand** w poprzednim przykładzie kodu do poniższego.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

W większości przypadków parametry z wartościami przechowywanymi w tabeli mają równoważną lub lepszą wydajność niż inne techniki przetwarzania wsadowego. Parametry z wartościami przechowywanymi w tabeli są często preferowane, ponieważ są bardziej elastyczne niż inne opcje. Na przykład inne techniki, takie jak kopia Zbiorcza SQL, zezwalają tylko na wstawianie nowych wierszy. Jednak za pomocą parametrów z wartościami przechowywanymi w tabeli można użyć logiki w procedurze składowanej, aby określić, które wiersze są aktualizowane i które są wstawiane. Typ tabeli można także zmodyfikować tak, aby zawierał kolumnę "Operation", która wskazuje, czy określony wiersz powinien zostać wstawiony, zaktualizowany lub usunięty.

W poniższej tabeli przedstawiono wyniki testów ad hoc służące do używania parametrów z wartościami przechowywanymi w tabeli w milisekundach.

| Operacje | Lokalne na platformę Azure (MS) | To samo centrum danych platformy Azure (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10 000 |23830 |3586 |

> [!NOTE]
> Wyniki nie są wzorcem. Zobacz [uwagi dotyczące chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

Wzrost wydajności z partii jest natychmiast widoczny. W poprzednim teście sekwencyjnym 1000 operacje wymagały 129 sekund poza centrum danych i 21 sekund od centrum danych. Jednak w przypadku parametrów z wartościami przechowywanymi w tabeli operacje 1000 operacji trwają tylko 2,6 sekund poza centrum danych i 0,4 s w centrum danych.

Aby uzyskać więcej informacji na temat parametrów z wartościami przechowywanymi w tabeli, zobacz [Parametry z wartościami przechowywanymi w tabeli](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

### <a name="sql-bulk-copy"></a>Kopia Zbiorcza SQL

Kopiowanie masowe SQL to inny sposób wstawiania dużych ilości danych do docelowej bazy danych. Aplikacje platformy .NET mogą używać klasy **SqlBulkCopy** do wykonywania operacji wstawiania zbiorczego. **SqlBulkCopy** jest podobna do narzędzia wiersza polecenia, **Bcp.exe** lub instrukcji języka Transact-SQL, **BULK INSERT**. Poniższy przykład kodu pokazuje, jak zbiorczo kopiować wiersze ze źródłowej tabeli **DataTable** do tabeli docelowej, MyTable.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Istnieją przypadki, w których kopie zbiorcze są preferowane za pośrednictwem parametrów z wartościami przechowywanymi w tabeli. Zobacz tabelę porównawczą parametrów Table-Valued i BULK INSERT operacji w [parametrach z wartościami przechowywanymi w tabeli](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

Poniższe wyniki testu ad hoc przedstawiają wydajność przetwarzania wsadowego za pomocą **SqlBulkCopy** w milisekundach.

| Operacje | Lokalne na platformę Azure (MS) | To samo centrum danych platformy Azure (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10 000 |21605 |2737 |

> [!NOTE]
> Wyniki nie są wzorcem. Zobacz [uwagi dotyczące chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

W mniejszych rozmiarach partii Użyj parametrów z wartościami przechowywanymi w tabeli, w których jest wykonywana Klasa **SqlBulkCopy** . Jednak **SqlBulkCopy** wykonał 12-31% szybciej niż parametry z wartościami przechowywanymi w tabeli dla testów 1 000 i 10 000 wierszy. Podobnie jak w przypadku parametrów z wartościami przechowywanymi w tabeli, **SqlBulkCopy** jest dobrą opcją dla wkładów wsadowych, szczególnie w porównaniu z wydajnością operacji niewsadowych.

Aby uzyskać więcej informacji na temat kopiowania masowego w ADO.NET, zobacz [operacje kopiowania zbiorczego](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server).

### <a name="multiple-row-parameterized-insert-statements"></a>Instrukcje wstawiania z wieloma wierszami

Jedną alternatywą dla małych partii jest konstruowanie dużej sparametryzowanej instrukcji INSERT, która wstawia wiele wierszy. Poniższy przykład kodu demonstruje tę technikę.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Ten przykład jest przeznaczony do wyświetlania koncepcji podstawowej. Bardziej realistyczny scenariusz może przechodzić przez wymagane jednostki w celu utworzenia ciągu zapytania i parametrów polecenia jednocześnie. Maksymalna liczba parametrów zapytania jest równa 2100, dlatego ogranicza łączną liczbę wierszy, które można przetworzyć w ten sposób.

Poniższe wyniki testu ad hoc pokazują wydajność tego typu instrukcji INSERT w milisekundach.

| Operacje | Parametry z wartościami przechowywanymi w tabeli (MS) | Wstawianie pojedynczej instrukcji (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Wyniki nie są wzorcem. Zobacz [uwagi dotyczące chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

Takie podejście może być nieco szybsze dla partii, które są mniejsze niż 100 wierszy. Chociaż poprawa jest mała, ta technika jest kolejną opcją, która może współpracować z konkretnym scenariuszem aplikacji.

### <a name="dataadapter"></a>Element

Klasa **DataAdapter** pozwala modyfikować obiekt **DataSet** , a następnie przesyłać zmiany jako operacje INSERT, Update i DELETE. Jeśli używasz elementu **DataAdapter** w ten sposób, należy pamiętać, że osobne wywołania są wykonywane dla każdej operacji DISTINCT. Aby zwiększyć wydajność, należy użyć właściwości **UpdateBatchSize** do liczby operacji, które powinny być przetwarzane wsadowo w danym momencie. Aby uzyskać więcej informacji, zobacz [wykonywanie operacji wsadowych za pomocą adapterów](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters)danych.

### <a name="entity-framework"></a>Entity Framework

[Entity Framework Core](/ef/efcore-and-ef6/#saving-data) obsługuje przetwarzanie wsadowe.

### <a name="xml"></a>XML

W celu zapewnienia kompletności należy zwrócić uwagę na informacje dotyczące XML jako strategię przetwarzania wsadowego. Jednak użycie kodu XML nie ma żadnych korzyści w porównaniu z innymi metodami i kilkoma wadami. Podejście jest podobne do parametrów z wartościami przechowywanymi w tabeli, ale plik XML lub ciąg jest przesyłany do procedury składowanej zamiast tabeli zdefiniowanej przez użytkownika. Procedura składowana analizuje polecenia w procedurze składowanej.

Istnieje kilka wad tego podejścia:

* Praca z XML może być nieposkomplikowana i podatna na błędy.
* Analizowanie kodu XML w bazie danych może być czasochłonne.
* W większości przypadków ta metoda jest wolniejsza niż parametry z wartościami przechowywanymi w tabeli.

Z tego względu nie zaleca się stosowania XML dla zapytań wsadowych.

## <a name="batching-considerations"></a>Zagadnienia związane z przetwarzaniem wsadowym

Poniższe sekcje zawierają więcej wskazówek dotyczących używania tworzenia pakietów wsadowych w Azure SQL Database i aplikacji wystąpienia zarządzanego Azure SQL.

### <a name="tradeoffs"></a>Kompromisy

W zależności od architektury przetwarzanie wsadowe może stanowić kompromis między wydajnością i odpornością. Rozważmy na przykład scenariusz, w którym nieoczekiwanie wystąpiła rola. Jeśli utracisz jeden wiersz danych, wpływ jest mniejszy niż wpływ utraty dużej partii nieprzesłanych wierszy. Istnieje większe ryzyko związane z buforowaniem wierszy przed wysłaniem ich do bazy danych w określonym przedziale czasu.

Ze względu na te kompromisy należy oszacować typ operacji wykonywanych przez użytkownika. Przetwarzaj wsadowo bardziej agresywnie (większe partie i dłuższy czas w systemie Windows), które mają mniej krytyczne dane.

### <a name="batch-size"></a>Rozmiar partii

W naszych testach zwykle nie ma możliwości dzielenia dużych partii na mniejsze fragmenty. W rzeczywistości ta częściowa część często spowodowało wolniejszą wydajność niż przesyłanie pojedynczej dużej partii. Rozważmy na przykład scenariusz, w którym chcesz wstawić 1000 wierszy. W poniższej tabeli pokazano, jak długo należy używać parametrów z wartościami przechowywanymi w tabeli do wstawiania wierszy 1000 w przypadku dzielenia na mniejsze partie.

| Rozmiar partii | Iteracje | Parametry z wartościami przechowywanymi w tabeli (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Wyniki nie są wzorcem. Zobacz [uwagi dotyczące chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

Aby zobaczyć, że Najlepsza wydajność dla wierszy 1000, można przesłać wszystkie jednocześnie. W przypadku innych testów (nieprzedstawionych w tym miejscu) wystąpił mały wzrost wydajności, aby przerwać partię 10000-wierszową do dwóch partii 5000. Jednak schemat tabeli dla tych testów jest stosunkowo prosty, dlatego należy wykonać testy dotyczące konkretnych rozmiarów danych i partii, aby sprawdzić te wyniki.

Innym czynnikiem, który należy wziąć pod uwagę, jest to, że jeśli całkowita partia stanie się zbyt duża, Azure SQL Database lub wystąpienie zarządzane SQL Azure może ograniczać i odmawiać zatwierdzić partię. Aby uzyskać najlepsze wyniki, przetestuj konkretny scenariusz, aby określić, czy jest idealnym rozmiarem partii. Wprowadź rozmiar wsadu konfigurowalny w czasie wykonywania, aby włączyć szybkie korekty na podstawie wydajności lub błędów.

Na koniec należy zrównoważyć rozmiar partii przy użyciu ryzyka związanego z przetwarzaniem wsadowym. Jeśli występują błędy przejściowe lub rola się nie powiedzie, należy wziąć pod uwagę konsekwencje ponowienia operacji lub utraty danych w partii.

### <a name="parallel-processing"></a>Przetwarzanie równoległe

Co zrobić, jeśli zachodzi taka potrzeba zmniejszenia rozmiaru partii, ale użyto wielu wątków do wykonania pracy? Testy te wykazały, że kilka mniejszych partii wielowątkowych zwykle jest mniej więcej niż jedna większa partia. Następujący test próbuje wstawić 1000 wierszy w co najmniej jednej partii równoległej. Ten test pokazuje, jak coraz więcej równoczesnych partii zmniejszyło wydajność.

| Rozmiar wsadu [iteracje] | Dwa wątki (MS) | Cztery wątki (MS) | Sześć wątków (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Wyniki nie są wzorcem. Zobacz [uwagi dotyczące chronometrażu w tym artykule](#note-about-timing-results-in-this-article).

Istnieje kilka potencjalnych powodów obniżenia wydajności z powodu równoległości:

* Istnieje wiele równoczesnych wywołań sieciowych zamiast jednego.
* Wiele operacji na pojedynczej tabeli może skutkować rywalizacją i blokowaniem.
* Istnieją narzuty skojarzone z wielowątkowością.
* Koszt otwarcia wielu połączeń umożliwia przeważenie zalet przetwarzania równoległego.

W przypadku korzystania z różnych tabel lub baz danych, istnieje możliwość wyświetlenia pewnej wydajności z tą strategią. Fragmentowania bazy danych lub Federacji byłyby scenariuszem dla tego podejścia. Fragmentowania używa wielu baz danych i przekierowuje różne dane do poszczególnych baz danych. Jeśli każda mała partia odbywa się w innej bazie danych, wykonywanie operacji równoległych może być bardziej wydajne. Jednak wzrost wydajności nie jest wystarczająco znaczący, aby mógł zostać użyty jako podstawa do podjęcia decyzji o użyciu fragmentowania bazy danych w rozwiązaniu.

W niektórych projektach równoległe wykonywanie mniejszych partii może spowodować zwiększenie przepływności żądań w systemie pod obciążeniem. W takim przypadku, nawet jeśli jest szybszym rozwiązaniem do przetwarzania pojedynczej większej partii, przetwarzanie wielu partii równolegle może być bardziej wydajne.

Jeśli używasz wykonywania równoległego, Rozważ możliwość kontrolowania maksymalnej liczby wątków roboczych. Mniejsza liczba może spowodować mniejszą rywalizację i krótszy czas wykonywania. Należy również rozważyć dodatkowe obciążenie, które to miejsce w docelowej bazie danych zarówno w połączeniach, jak i transakcjach.

### <a name="related-performance-factors"></a>Powiązane czynniki wydajności

Typowe wskazówki dotyczące wydajności bazy danych wpływają również na przetwarzanie wsadowe. Na przykład wydajność wstawiania jest zmniejszana w przypadku tabel, które mają duży klucz podstawowy lub wiele indeksów nieklastrowanych.

Jeśli parametry z wartościami przechowywanymi w tabeli używają procedury składowanej, można użyć polecenia **SET NOCOUNT ON na** początku procedury. Ta instrukcja pomija zwracaną liczbę wierszy objętych procedurą. Jednak w naszych testach użycie **zestawu NOCOUNT on** nie miało wpływu ani na obniżenie wydajności. Procedura składowana testu była prosta przy użyciu jednego polecenia **INSERT** z parametru z wartościami przechowywanymi w tabeli. Istnieje możliwość, że bardziej złożone procedury składowane byłyby korzystne dla tej zasady. Nie zakłada się jednak, że dodanie **opcji SET NOCOUNT on** do procedury składowanej powoduje automatyczne zwiększenie wydajności. Aby zrozumieć efekt, przetestuj procedurę składowaną z użyciem instrukcji **SET NOCOUNT on** i bez niej.

## <a name="batching-scenarios"></a>Scenariusze przetwarzania wsadowego

W poniższych sekcjach opisano sposób używania parametrów z wartościami przechowywanymi w tabeli w trzech scenariuszach aplikacji. Pierwszy scenariusz przedstawia sposób, w jaki buforowanie i przetwarzanie wsadowe mogą współdziałać ze sobą. Drugi scenariusz poprawia wydajność przez wykonywanie operacji głównych i szczegółowych w jednym wywołaniu procedury składowanej. W ostatnim scenariuszu pokazano, jak używać parametrów z wartościami przechowywanymi w tabeli w operacji "UPSERT".

### <a name="buffering"></a>Buforowania

Chociaż istnieją pewne scenariusze, które są oczywistym kandydatem do tworzenia pakietów wsadowych, istnieje wiele scenariuszy, które mogą korzystać z przetwarzania wsadowego przez opóźnione przetwarzanie. Jednak opóźnione przetwarzanie również zwiększa ryzyko utraty danych w przypadku nieoczekiwanego błędu. Ważne jest zrozumienie tego ryzyka i rozważenie konsekwencji.

Rozważmy na przykład aplikację sieci Web, która śledzi historię przeglądania poszczególnych użytkowników. Na każdym żądaniu strony aplikacja może nawiązać połączenie z bazą danych, aby zarejestrować widok strony użytkownika. Jednak wyższą wydajność i skalowalność można osiągnąć, buforując działania nawigacyjne użytkowników, a następnie wysyłając je do bazy danych w partiach. Można wyzwolić aktualizację bazy danych według czasu, który upłynął i/lub rozmiar buforu. Na przykład reguła może określić, że partia powinna być przetwarzana po 20 sekundach lub gdy bufor osiągnie 1000 elementów.

Poniższy przykład kodu używa [reaktywnych rozszerzeń-RX](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) , aby przetwarzać zdarzenia buforowane wywoływane przez klasę monitorowania. Po osiągnięciu lub przekroczeniu limitu czasu buforu dane użytkownika są wysyłane do bazy danych za pomocą parametru z wartościami przechowywanymi w tabeli.

Poniższe klasy NavHistoryData modelują szczegóły nawigacji użytkownika. Zawiera podstawowe informacje, takie jak identyfikator użytkownika, dostęp do adresu URL i czas dostępu.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

Klasa NavHistoryDataMonitor jest odpowiedzialna za buforowanie danych nawigacyjnych użytkownika w bazie danych. Zawiera metodę RecordUserNavigationEntry, która reaguje przez podnoszenie zdarzenia **dodaniu** . Poniższy kod przedstawia logikę konstruktora, która używa RX do tworzenia kolekcji zauważalnej na podstawie zdarzenia. Następnie subskrybuje tę kolekcję dostrzegalną za pomocą metody bufora. Przeciążenie określa, że bufor ma być wysyłany co 20 sekund lub 1000 wpisów.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

Program obsługi konwertuje wszystkie elementy buforowane na typ z wartościami przechowywanymi w tabeli, a następnie przekazuje ten typ do procedury składowanej, która przetwarza partię. Poniższy kod przedstawia pełną definicję klasy NavHistoryDataEventArgs i NavHistoryDataMonitor.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

Program obsługi konwertuje wszystkie elementy buforowane na typ z wartościami przechowywanymi w tabeli, a następnie przekazuje ten typ do procedury składowanej, która przetwarza partię. Poniższy kod przedstawia pełną definicję klasy NavHistoryDataEventArgs i NavHistoryDataMonitor.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Aby użyć tej klasy buforowania, aplikacja tworzy statyczny obiekt NavHistoryDataMonitor. Za każdym razem, gdy użytkownik uzyskuje dostęp do strony, aplikacja wywołuje metodę NavHistoryDataMonitor. RecordUserNavigationEntry. Logika buforowania pozwala zachować ostrożność wysyłania tych wpisów do bazy danych w partiach.

### <a name="master-detail"></a>Szczegóły wzorca

Parametry z wartościami przechowywanymi w tabeli są przydatne w przypadku prostych scenariuszy wstawiania. Jednak może być trudniejsze do wstawiania wsadowego obejmujących więcej niż jedną tabelę. Scenariusz "wzorzec/szczegóły" jest dobrym przykładem. Tabela główna identyfikuje jednostkę podstawową. Co najmniej jedna tabela szczegółów przechowuje więcej danych o jednostce. W tym scenariuszu relacje klucza obcego wymuszają związek szczegółów z unikatową jednostką główną. Rozważ uproszczoną wersję tabeli PurchaseOrder i skojarzonej z nią tabeli OrderDetail. Poniższe polecenie języka Transact-SQL tworzy tabelę PurchaseOrder z czterema kolumnami: IDZamówienia, DataZamówienia, CustomerID i status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Każde zamówienie zawiera jeden lub więcej zakupów produktów. Te informacje są przechwytywane w tabeli PurchaseOrderDetail. Poniższe polecenie języka Transact-SQL tworzy tabelę PurchaseOrderDetail z pięcioma kolumnami: IDZamówienia, OrderDetailID, ProductID, CenaJednostkowa i OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

Kolumna IDZamówienia w tabeli PurchaseOrderDetail musi odwoływać się do zamówienia z tabeli PurchaseOrder. Następująca definicja klucza obcego wymusza to ograniczenie.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Aby można było używać parametrów z wartościami przechowywanymi w tabeli, dla każdej tabeli docelowej musi istnieć jeden typ tabeli zdefiniowanej przez użytkownika.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Następnie zdefiniuj procedurę składowaną, która akceptuje tabele tych typów. Ta procedura pozwala aplikacji na lokalną partię zestawu zamówień i szczegółów zamówienia w jednym wywołaniu. Poniższe instrukcje języka Transact-SQL udostępniają pełną deklarację procedury składowanej dla tego przykładowego zamówienia zakupu.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

W tym przykładzie w tabeli zdefiniowanej lokalnie @IdentityLink są przechowywane rzeczywiste wartości IDZamówienia z nowo wstawionych wierszy. Te identyfikatory kolejności różnią się od wartości tymczasowych IDZamówienia w @orders parametrach i z wartościami @details przechowywanymi w tabeli. Z tego powodu @IdentityLink tabela łączy wartości IDZamówienia z @orders parametru do rzeczywistych wartości IDZamówienia dla nowych wierszy w tabeli PurchaseOrder. Po wykonaniu tego kroku @IdentityLink tabela może ułatwić Wstawianie szczegółów zamówienia z rzeczywistym identyfikatorem IDZamówienia, który spełnia warunek ograniczenia klucza obcego.

Ta procedura składowana może być używana z kodu lub z innych wywołań Transact-SQL. Zobacz sekcję parametry z wartościami przechowywanymi w tabeli tego dokumentu dla przykładu kodu. Poniższe instrukcje języka Transact-SQL pokazują, jak wywołać sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

To rozwiązanie umożliwia każdej partii użycie zestawu wartości IDZamówienia, które zaczynają się od 1. Te tymczasowe wartości IDZamówienia opisują relacje w partii, ale rzeczywiste wartości IDZamówienia są określane w czasie operacji wstawiania. W poprzednim przykładzie można wielokrotnie uruchomić te same instrukcje i generować unikatowe zamówienia w bazie danych. Z tego powodu rozważ dodanie większej liczby kodów lub logiki bazy danych, która uniemożliwia duplikowanie zamówień podczas korzystania z tej techniki wsadowej.

Ten przykład pokazuje, że nawet bardziej złożone operacje bazy danych, takie jak operacje wzorca i szczegóły, mogą być przetwarzane wsadowo za pomocą parametrów z wartościami przechowywanymi w tabeli.

### <a name="upsert"></a>UPSERT

Inny scenariusz wsadowy obejmuje jednocześnie aktualizowanie istniejących wierszy i wstawianie nowych wierszy. Ta operacja jest czasami nazywana operacją "UPSERT" (Update + INSERT). Zamiast tworzenia oddzielnych wywołań do wstawiania i aktualizowania, instrukcja MERGE najlepiej nadaje się do tego zadania. Instrukcja MERGE umożliwia wykonywanie operacji INSERT i Update w pojedynczym wywołaniu.

Parametrów z wartościami przechowywanymi w tabeli można używać w połączeniu z instrukcją MERGE do wykonywania aktualizacji i wstawiania. Rozważmy na przykład uproszczoną tabelę pracowników, która zawiera następujące kolumny: IDPracownika, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

W tym przykładzie można użyć faktu, że SocialSecurityNumber jest unikatowy, aby wykonać SCALAnie wielu pracowników. Najpierw Utwórz typ tabeli zdefiniowanej przez użytkownika:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Następnie Utwórz procedurę składowaną lub napisz kod, który używa instrukcji MERGE do wykonania aktualizacji i wstawienia. W poniższym przykładzie używa się instrukcji MERGE dla parametru z wartościami przechowywanymi w tabeli, @employees typu EmployeeTableType. Zawartość @employees tabeli nie jest wyświetlana w tym miejscu.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją i przykładami instrukcji MERGE. Mimo że te same działania można wykonać w wywołaniu procedury składowanej z wieloma krokami z oddzielnymi operacjami INSERT i UPDATE, instrukcja MERGE jest bardziej wydajna. Kod bazy danych może również tworzyć wywołania Transact-SQL, które używają instrukcji MERGE bezpośrednio, bez konieczności wykonywania operacji INSERT i UPDATE przez dwa wywołania bazy danych.

## <a name="recommendation-summary"></a>Podsumowanie rekomendacji

Poniższa lista zawiera podsumowanie zaleceń dotyczących przetwarzania wsadowego omówionych w tym artykule:

* Używaj buforowania i tworzenia pakietów wsadowych, aby zwiększyć wydajność i skalowalność Azure SQL Database i aplikacji wystąpienia zarządzanego Azure SQL.
* Poznaj kompromisy między przetwarzaniem wsadowym a buforowaniem i odpornością. W trakcie awarii roli ryzyko utraty nieprzetworzonej partii danych o kluczowym znaczeniu dla firmy może być korzystne dla wydajności przetwarzania wsadowego.
* Staraj się zachować wszystkie wywołania bazy danych w ramach jednego centrum, aby zmniejszyć opóźnienie.
* W przypadku wybrania pojedynczej techniki wsadowej parametry z wartościami przechowywanymi w tabeli zapewniają najlepszą wydajność i elastyczność.
* Aby uzyskać najszybszą wydajność wstawiania, postępuj zgodnie z poniższymi ogólnymi wskazówkami, ale Przetestuj swój scenariusz:
  * W przypadku wierszy < 100 Użyj jednego sparametryzowanego polecenia INSERT.
  * W przypadku wierszy < 1000 Użyj parametrów z wartościami przechowywanymi w tabeli.
  * W przypadku >= 1000 wierszy Użyj SqlBulkCopy.
* W przypadku operacji aktualizowania i usuwania Użyj parametrów z wartościami przechowywanymi w tabeli z logiką procedury składowanej, która określa poprawność operacji dla każdego wiersza w parametrze tabeli.
* Wytyczne dotyczące rozmiaru partii:
  * Używaj największych rozmiarów partii, które mają sens dla aplikacji i wymagań firmy.
  * Równoważyć wzrost wydajności dużych partii z ryzykiem wystąpienia błędów tymczasowych lub katastrofalnych. Jaka jest konsekwencja ponownych prób lub utraty danych w partii?
  * Przetestuj największy rozmiar wsadu, aby sprawdzić, czy Azure SQL Database lub wystąpienie zarządzane usługi Azure SQL nie odrzuci go.
  * Utwórz ustawienia konfiguracji kontrolujące przetwarzanie wsadowe, takie jak rozmiar partii lub przedział czasu buforowania. Te ustawienia zapewniają elastyczność. Można zmienić działanie przetwarzania wsadowego w środowisku produkcyjnym bez ponownego wdrażania usługi w chmurze.
* Unikaj równoległego wykonywania partii, które działają w pojedynczej tabeli w jednej bazie danych. W przypadku wybrania opcji dzielenia pojedynczej partii między wiele wątków roboczych należy uruchomić testy w celu określenia idealnej liczby wątków. Po nieokreślonym progu większa liczba wątków zmniejszy wydajność, a nie zwiększy go.
* Należy rozważyć buforowanie według rozmiaru i godziny w celu zaimplementowania przetwarzania wsadowego w celu uzyskania większej liczby scenariuszy.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na tym, jak techniki projektowania i kodowania bazy danych związane z przetwarzaniem wsadowym mogą poprawić wydajność i skalowalność aplikacji. Jest to tylko jeden czynnik w ogólnej strategii. Aby uzyskać więcej informacji na temat poprawy wydajności i skalowalności, zobacz temat [wskazówki dotyczące wydajności bazy danych](database/performance-guidance.md) oraz [zagadnienia dotyczące cen i wydajności dla puli elastycznej](database/elastic-pool-overview.md).