---
title: Transakcje rozproszone w bazach danych w chmurze (wersja zapoznawcza)
description: Przegląd transakcji Elastic Database z usługą Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793351"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Transakcje rozproszone w bazach danych w chmurze (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Transakcje elastycznej bazy danych dla Azure SQL Database i wystąpienia zarządzanego Azure SQL umożliwiają uruchamianie transakcji obejmujących kilka baz danych. Transakcje Elastic Database są dostępne dla aplikacji .NET korzystających z programu ADO.NET i integrują się z znanym środowiskiem programistycznym korzystającym z klas [System. Transaction](/dotnet/api/system.transactions) . Aby uzyskać bibliotekę, zobacz [.NET Framework 4.6.1 (Instalator sieci Web)](https://www.microsoft.com/download/details.aspx?id=49981).
Ponadto w przypadku transakcji rozproszonych wystąpienia zarządzanego są dostępne w [języku Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

W środowisku lokalnym taki scenariusz zwykle wymaga uruchomionego programu Microsoft Distributed Transaction Coordinator (MSDTC). Ponieważ usługa MSDTC nie jest dostępna dla aplikacji platformy jako usługi na platformie Azure, możliwość skoordynowania transakcji rozproszonych została teraz zintegrowana bezpośrednio z SQL Database lub wystąpieniem zarządzanym. Aplikacje mogą łączyć się z dowolnymi bazami danych w celu uruchamiania transakcji rozproszonych, a jedna z baz danych lub serwerów w sposób przezroczysty koordynuje transakcję rozproszoną, jak pokazano na poniższej ilustracji.

W tym dokumencie warunki "transakcje rozproszone" i "transakcje Elastic Database" są uważane za synonimy i będą używane zamiennie.

  ![Transakcje rozproszone z Azure SQL Database przy użyciu transakcji Elastic Database ][1]

## <a name="common-scenarios"></a>Typowe scenariusze

Transakcje Elastic Database umożliwiają aplikacjom wprowadzanie niepodzielnych zmian danych przechowywanych w kilku różnych bazach danych. Wersja zapoznawcza koncentruje się na środowiskach deweloperskich po stronie klienta w językach C# i .NET. Środowisko pracy po stronie serwera (kod zapisany w procedurach składowanych lub skrypty po stronie serwera) za pomocą [języka Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) jest dostępne tylko dla wystąpienia zarządzanego.
> [!IMPORTANT]
> W wersji zapoznawczej uruchamianie transakcji Elastic Database między Azure SQL Database i wystąpieniem zarządzanym Azure SQL nie jest obecnie obsługiwane. Transakcja Elastic Database może obejmować tylko zbiory baz danych SQL lub zestaw wystąpień zarządzanych.

Transakcje elastycznej bazy danych są przeznaczone dla następujących scenariuszy:

* Aplikacje z wieloma bazami danych na platformie Azure: w tym scenariuszu dane są partycjonowane w pionie w wielu bazach danych w SQL Database lub w wystąpieniu zarządzanym, w taki sposób, że różne rodzaje danych znajdują się w różnych bazach. Niektóre operacje wymagają zmian danych, które są przechowywane w co najmniej dwóch bazach danych. Aplikacja używa transakcji Elastic Database do koordynowania zmian w bazach danych i zapewniania niepodzielności.
* Podzielonej na fragmenty bazy danych na platformie Azure: w tym scenariuszu warstwa danych używa [biblioteki klienta Elastic Database](elastic-database-client-library.md) lub fragmentowania w poziomie, aby podzielić dane między wiele baz danych w SQL Database lub zarządzanym wystąpieniu. Jednym z widocznych przypadków użycia jest konieczność wykonywania niepodzielnych zmian w podzielonej na fragmenty aplikacji wielodostępnej, gdy zmiany obejmują dzierżawców. Pomyśl o wystąpieniu transferu między dzierżawcami, które znajdują się w różnych bazach danych. Drugi przypadek jest szczegółowym fragmentowania, aby sprostać potrzebom związanym z pojemnością dla dużej dzierżawy, co z kolei oznacza, że niektóre operacje niepodzielne muszą być rozciągane między kilka baz danych używanych dla tej samej dzierżawy. Trzecia sprawa jest niepodzielnymi aktualizacjami odwołań do danych replikowanych między bazami danych. Niepodzielna, transakcyjna, operacje w tych wierszach, można teraz koordynować w kilku bazach danych przy użyciu wersji zapoznawczej.
  Transakcje elastycznej bazy danych korzystają z dwóch zatwierdzeń fazy, aby zapewnić niepodzielność transakcji między bazami danych. Jest to dobre dopasowanie w przypadku transakcji obejmujących mniej niż 100 baz danych jednocześnie w ramach jednej transakcji. Te limity nie są wymuszane, ale jeden z nich powinien oczekiwać wydajności i częstotliwości sukcesów dla transakcji Elastic Database, gdy przekraczają te limity.

## <a name="installation-and-migration"></a>Instalacja i migracja

Możliwości transakcji Elastic Database są udostępniane za pomocą aktualizacji bibliotek .NET System.Data.dll i System.Transactions.dll. Biblioteki DLL zapewniają, że dwufazowe zatwierdzanie jest używane w razie potrzeby w celu zapewnienia niepodzielności. Aby rozpocząć tworzenie aplikacji przy użyciu transakcji Elastic Database, zainstaluj [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) lub nowszą wersję. W przypadku uruchomienia w starszej wersji programu .NET Framework transakcje nie będą poddawane promocji do transakcji rozproszonej i zostanie zgłoszony wyjątek.

Po zakończeniu instalacji można używać interfejsów API transakcji rozproszonych w programie System. Transactions z połączeniami z usługą SQL Database i wystąpieniem zarządzanym. Jeśli masz istniejące aplikacje usługi MSDTC korzystające z tych interfejsów API, ponownie skompiluj istniejące aplikacje dla platformy .NET 4,6 po zainstalowaniu przeglądarki 4.6.1. Jeśli projekty są przeznaczone dla platformy .NET 4,6, będą automatycznie używały zaktualizowanych bibliotek DLL z nowej wersji platformy i wywołań interfejsu API transakcji rozproszonych w połączeniu z połączeniami z SQL Database lub wystąpieniem zarządzanym.

Pamiętaj, że transakcje Elastic Database nie wymagają instalowania usługi MSDTC. Zamiast tego transakcje Elastic Database są bezpośrednio zarządzane przez usługę i w ramach tej usługi. Znacznie upraszcza to scenariusze w chmurze, ponieważ wdrożenie usługi MSDTC nie jest konieczne do korzystania z transakcji rozproszonych z SQL Databasem lub wystąpieniem zarządzanym. Sekcja 4 wyjaśnia bardziej szczegółowo, jak wdrażać transakcje Elastic Database i wymagane środowisko .NET Framework razem z aplikacjami w chmurze na platformie Azure.

## <a name="net-installation-for-azure-cloud-services"></a>Instalacja platformy .NET dla usługi Azure Cloud Services

Platforma Azure oferuje kilka ofert do hostowania aplikacji platformy .NET. Porównanie różnych ofert jest dostępne w ramach [porównania Azure App Service, Cloud Services i Virtual Machines](/azure/architecture/guide/technology-choices/compute-decision-tree). Jeśli system operacyjny gościa oferty jest mniejszy niż .NET 4.6.1 wymagany dla transakcji elastycznych, musisz uaktualnić system operacyjny gościa do 4.6.1.

W przypadku Azure App Service uaktualnienia do systemu operacyjnego gościa nie są obecnie obsługiwane. W przypadku usługi Azure Virtual Machines wystarczy zalogować się do maszyny wirtualnej i uruchomić Instalatora w celu uzyskania najnowszej wersji programu .NET Framework. W przypadku usługi Azure Cloud Services należy dołączyć instalację nowszej wersji platformy .NET do zadań uruchamiania danego wdrożenia. Koncepcje i kroki są udokumentowane w temacie [Instalowanie programu .NET w ramach roli usługi w chmurze](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Należy pamiętać, że Instalator programu .NET 4.6.1 może wymagać więcej tymczasowego magazynu podczas procesu uruchamiania w usługach Azure Cloud Services niż Instalator programu .NET 4,6. Aby zapewnić pomyślną instalację, należy zwiększyć tymczasowy magazyn dla usługi w chmurze platformy Azure w pliku ServiceDefinition. csdef w sekcji LocalResources i ustawieniach środowiska zadania uruchamiania, jak pokazano w następującym przykładzie:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>Środowisko deweloperskie platformy .NET

### <a name="multi-database-applications"></a>Aplikacje z obsługą kilku baz danych

Następujący przykładowy kod używa znanego środowiska programistycznego w programie .NET system. Transactions. Klasa TransactionScope tworzy otaczającą transakcję w programie .NET. ("Otoczenia transakcji" to ten, który znajduje się w bieżącym wątku). Wszystkie połączenia otwarte w ramach elementu TransactionScope biorą udział w transakcji. W przypadku korzystania z różnych baz danych transakcja zostanie automatycznie podwyższona do transakcji rozproszonej. Wynik transakcji jest kontrolowany przez ustawienie zakresu do ukończenia, aby wskazać zatwierdzenie.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Aplikacje bazy danych podzielonej na fragmenty

Transakcje elastycznej bazy danych dla SQL Database i wystąpienia zarządzanego obsługują również koordynowanie transakcji rozproszonych w przypadku używania metody OpenConnectionForKey biblioteki klienta Elastic Database do otwierania połączeń dla warstwy danych skalowanych w poziomie. Rozważ przypadki, w których należy zagwarantowanie spójności transakcyjnej dla zmian w kilku różnych wartościach klucza fragmentowania. Połączenia z fragmentów hostowania różnych wartości klucza fragmentowania są obsługiwane przez brokera przy użyciu OpenConnectionForKey. W ogólnym przypadku połączenia mogą być różne fragmentów w taki sposób, że zapewnienie transakcyjnych gwarancji wymaga transakcji rozproszonej.
Poniższy przykład kodu ilustruje to podejście. Przyjęto założenie, że zmienna o nazwie shardmap jest używana do reprezentowania mapy fragmentu z biblioteki klienta Elastic Database:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Środowisko programistyczne języka Transact-SQL

Transakcje rozproszone po stronie serwera przy użyciu języka Transact-SQL są dostępne tylko dla wystąpienia zarządzanego usługi Azure SQL. Transakcja rozproszona może być wykonywana tylko między wystąpieniami zarządzanymi należącymi do tej samej [grupy zaufania serwera](../managed-instance/server-trust-group-overview.md). W tym scenariuszu wystąpienia zarządzane muszą używać [połączonego serwera](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) w celu odwoływania się do siebie nawzajem.

Następujący przykładowy kod w języku Transact-SQL używa [Rozpocznij transakcji rozproszonej](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) do uruchomienia transakcji rozproszonej.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Łączenie środowiska programistycznego .NET i Transact-SQL

Aplikacje .NET używające klas System. Transaction mogą łączyć klasę TransactionScope z instrukcją Transact-SQL Rozpocznij transakcję rozproszoną. W ramach elementu TransactionScope transakcja wewnętrzna, która wykonuje transakcje BEGIN rozdzielonej, zostanie jawnie podwyższona do transakcji rozproszonej. Ponadto, gdy druga SqlConnecton jest otwierana w elemencie TransactionScope, zostanie niejawnie podwyższona do transakcji rozproszonej. Po rozpoczęciu transakcji rozproszonej wszystkie kolejne żądania transakcji, niezależnie od tego, czy pochodzą z platformy .NET, czy Transact-SQL, dołączają nadrzędną transakcję rozproszoną. W miarę jak wszystkie zakresy zagnieżdżonych transakcji inicjowanych przez instrukcję BEGIN będą kończyć się w tej samej transakcji, a instrukcje COMMIT/ROLLBACK będą miały następujący wpływ na ogólny wynik:
 * Instrukcja COMMIT nie będzie miała wpływu na zakres transakcji zainicjowanej przez instrukcję BEGIN, czyli nie zostanie zatwierdzona żadna liczba wyników przed ukończeniem () Metoda jest wywoływana w obiekcie TransactionScope. Jeśli obiekt TransactionScope zostanie zniszczony przed ukończeniem, wszystkie zmiany wprowadzone w zakresie zostaną wycofane.
 * Instrukcja ROLLBACK spowoduje wycofanie całego elementu TransactionScope. Wszelkie próby zarejestrowania nowych transakcji w ramach elementu TransactionScope zakończą się niepowodzeniem, a także próba wywołania metody Complete () dla obiektu TransactionScope.

Oto przykład, w którym transakcja została jawnie podwyższona do transakcji rozproszonej przy użyciu języka Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

W poniższym przykładzie przedstawiono transakcję, która została niejawnie podwyższona do transakcji rozproszonej po uruchomieniu drugiego SqlConnecton w elemencie TransactionScope.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transakcje na wielu serwerach dla Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Transakcje Elastic Database są obsługiwane na różnych serwerach w Azure SQL Database. W przypadku transakcji między serwerami, serwery uczestniczące muszą zostać wprowadzone w relacji wzajemnej komunikacji. Po ustanowieniu relacji komunikacji każda baza danych na jednym z tych dwóch serwerów może uczestniczyć w transakcjach elastycznych z bazami danych z innego serwera. W przypadku transakcji obejmujących więcej niż dwa serwery, relacja komunikacji musi być stosowane dla dowolnej pary serwerów.

Użyj następujących poleceń cmdlet programu PowerShell do zarządzania relacjami komunikacji między serwerami dla transakcji Elastic Database:

* **New-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby utworzyć nową relację komunikacji między dwoma serwerami w Azure SQL Database. Relacja jest symetryczna, co oznacza, że oba serwery mogą inicjować transakcje z innym serwerem.
* **Get-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby pobrać istniejące relacje komunikacji i ich właściwości.
* **Remove-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby usunąć istniejącą relację komunikacji.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transakcje na wielu serwerach dla wystąpienia zarządzanego usługi Azure SQL

Transakcje rozproszone są obsługiwane na różnych serwerach w wystąpieniu zarządzanym usługi Azure SQL. Gdy transakcje krzyżowe wystąpienia są zarządzane, najpierw muszą zostać wprowadzone wystąpienia uczestniczące w relacji zabezpieczeń i komunikacji. W tym celu należy utworzyć [grupę zaufania serwera](../managed-instance/server-trust-group-overview.md), którą można wykonać na Azure Portal. Jeśli wystąpienia zarządzane nie znajdują się w tej samej sieci wirtualnej, należy skonfigurować [komunikację równorzędną sieci wirtualnych](../../virtual-network/virtual-network-peering-overview.md) , a reguły ruchu przychodzącego i wychodzącego grupy zabezpieczeń muszą zezwalać na porty 5024 i 11000-12000 we wszystkich uczestniczących sieciach wirtualnych.

  ![Grupy zaufania serwera w witrynie Azure Portal][3]

Na poniższym diagramie przedstawiono grupę zaufania serwera z wystąpieniami zarządzanymi, które mogą wykonywać transakcje rozproszone przy użyciu platformy .NET lub języka Transact-SQL.

  ![Transakcje rozproszone z wystąpieniem zarządzanym usługi Azure SQL przy użyciu transakcji elastycznych][2]

## <a name="monitoring-transaction-status"></a>Monitorowanie stanu transakcji

Dynamiczne widoki zarządzania (widoków DMV) służą do monitorowania stanu i postępu trwających transakcji Elastic Database. Wszystkie widoków DMV związane z transakcjami są istotne dla transakcji rozproszonych w SQL Database i wystąpieniu zarządzanym. Odpowiednią listę widoków DMV można znaleźć tutaj: [powiązane z transakcją dynamiczne widoki zarządzania i funkcje (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Te widoków DMV są szczególnie przydatne:

* **\_ \_ aktywne \_ transakcje sys.DM przeładunku**: Wyświetla aktualnie aktywne transakcje i ich stan. Kolumna "jednostka pracy" może identyfikować różne transakcje podrzędne należące do tej samej transakcji rozproszonej. Wszystkie transakcje w ramach tej samej transakcji rozproszonej mają tę samą wartość JEDNOSTKowy. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **\_ \_ \_ transakcje bazy danych sys.DM przeładunku**: zawiera dodatkowe informacje o transakcjach, takich jak umieszczanie transakcji w dzienniku. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **\_ \_ blokady sys.DM**: zawiera informacje o blokadach, które są obecnie przechowywane przez bieżące transakcje. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Ograniczenia

W SQL Database są obecnie stosowane następujące ograniczenia dotyczące transakcji Elastic Database:

* Obsługiwane są tylko transakcje między bazami danych w SQL Database. Inni dostawcy zasobów (X) i bazy danych platformy [XA](https://en.wikipedia.org/wiki/X/Open_XA) poza programem SQL Database nie mogą uczestniczyć w transakcjach Elastic Database. Oznacza to, że transakcje Elastic Database nie mogą być rozciągane między SQL Serverami lokalnymi i Azure SQL Database. W przypadku transakcji rozproszonych w środowisku lokalnym Kontynuuj korzystanie z usługi MSDTC.
* Obsługiwane są tylko transakcje skoordynowane przez klienta z aplikacji .NET. Obsługa po stronie serwera dla języka T-SQL, taka jak Rozpocznij transakcję rozproszoną, jest planowana, ale nie jest jeszcze dostępna.
* Transakcje w ramach usług WCF nie są obsługiwane. Na przykład masz metodę usługi WCF, która wykonuje transakcję. Załączenie wywołania w ramach zakresu transakcji zakończy się niepowodzeniem jako [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

W przypadku transakcji rozproszonych w wystąpieniu zarządzanym są obecnie stosowane następujące ograniczenia:

* Obsługiwane są tylko transakcje między bazami danych w wystąpieniu zarządzanym. Inni dostawcy zasobów (X) i bazy danych usługi [XA](https://en.wikipedia.org/wiki/X/Open_XA) spoza wystąpienia zarządzanego Azure SQL nie mogą uczestniczyć w transakcjach rozproszonych. Oznacza to, że transakcje rozproszone nie mogą rozciągać się w zależności od lokalnych SQL Server i wystąpienia zarządzanego usługi Azure SQL. W przypadku transakcji rozproszonych w środowisku lokalnym Kontynuuj korzystanie z usługi MSDTC.
* Transakcje w ramach usług WCF nie są obsługiwane. Na przykład masz metodę usługi WCF, która wykonuje transakcję. Załączenie wywołania w ramach zakresu transakcji zakończy się niepowodzeniem jako [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* Wystąpienie zarządzane Azure SQL musi być częścią [grupy zaufania serwera](../managed-instance/server-trust-group-overview.md) , aby można było uczestniczyć w transakcji rozproszonej.
* Ograniczenia [grup zaufania serwera](../managed-instance/server-trust-group-overview.md) mają wpływ na transakcje rozproszone.
* Wystąpienia zarządzane, które uczestniczą w transakcjach rozproszonych, muszą mieć łączność z prywatnymi punktami końcowymi (przy użyciu prywatnego adresu IP z sieci wirtualnej, w której są wdrażane) i muszą być wzajemnie przywoływane przy użyciu prywatnych nazw FQDN. Aplikacje klienckie mogą używać transakcji rozproszonych w prywatnych punktach końcowych. Ponadto w przypadku, gdy Transact-SQL wykorzystuje połączone serwery odwołujące się do prywatnych punktów końcowych, aplikacje klienckie mogą również używać transakcji rozproszonych w publicznych punktach końcowych. To ograniczenie jest wyjaśnione na poniższym diagramie.
  ![Ograniczenie łączności z prywatnym punktem końcowym][4]
## <a name="next-steps"></a>Następne kroki

* Aby uzyskać odpowiedzi na pytania, skontaktuj się z nami na [stronie pytań firmy&Microsoft dotyczącym SQL Database](/answers/topics/azure-sql-database.html).
* W przypadku żądań funkcji należy je dodać do forum [SQL Database opinii](https://feedback.azure.com/forums/217321-sql-database/) lub [wystąpienia zarządzanego](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
