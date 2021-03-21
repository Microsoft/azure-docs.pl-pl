---
title: Reguły oceny dla SQL Server migracji SQL Database
description: Reguły oceny umożliwiające zidentyfikowanie problemów z wystąpieniem źródłowego SQL Server, które należy rozwiązać, aby przeprowadzić migrację do Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054733"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>Reguły oceny dla SQL Server migracji SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Narzędzia migracji sprawdzają poprawność wystąpienia SQL Server źródłowego, uruchamiając kilka reguł oceny, aby identyfikować problemy, które należy rozwiązać przed migracją bazy danych SQL Server do Azure SQL Database. 

Ten artykuł zawiera listę reguł używanych do oceny możliwości migrowania bazy danych SQL Server do Azure SQL Database. 


## <a name="bulk-insert"></a>Wstawianie zbiorcze<a id="BulkInsert"></a>

**Title: BULK INSERT ze źródłem danych obiektów BLOB spoza platformy Azure nie są obsługiwane w Azure SQL Database.**   
**Kategoria**: problem   

**Zharmonizowan**   
Azure SQL Database nie może uzyskać dostępu do udziałów plików ani folderów systemu Windows. Zapoznaj się z sekcją "obiekty objęte wpływem" dotyczącą konkretnych zastosowania instrukcji BULK INSERT, które nie odwołują się do obiektu blob platformy Azure. Obiekty z "BULK INSERT", w których źródło nie jest magazynem obiektów blob platformy Azure, nie będzie działały po migracji do Azure SQL Database. 


**Zaleca**   
Należy przekonwertować instrukcje BULK INSERT, które używają plików lokalnych lub udziałów plików do używania plików z usługi Azure Blob Storage, podczas migracji do Azure SQL Database. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

## <a name="compute-clause"></a>Klauzula COMPUTE<a id="ComputeClause"></a>

**Title: klauzula COMPUTE jest wycofana i została usunięta.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Klauzula COMPUTE generuje sumy, które są wyświetlane jako dodatkowe kolumny podsumowujące na końcu zestawu wyników. Jednakże ta klauzula nie jest już obsługiwana w Azure SQL Database. 


**Zaleca**   
Moduł T-SQL musi zostać ponownie zapisany przy użyciu operatora ROLLUP. Poniższy kod ilustruje sposób wymiany obliczeń z pakietem zbiorczym: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Przechwytywanie zmian danych (reprzechwytywania)<a id="CDC"></a>

**Title: funkcję przechwytywania zmian danych nie są obsługiwane w Azure SQL Database**   
**Kategoria**: problem   


**Zharmonizowan**   
Przechwytywanie zmian danych nie jest obsługiwane w Azure SQL Database. Oceń, czy Change Tracking może być używana zamiast tego.  Alternatywnie możesz przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na platformie Azure Virtual Machines. 


**Zaleca**   
Przechwytywanie zmian danych nie jest obsługiwane w Azure SQL Database. Oceń, czy Change Tracking może być użyty zamiast tego, lub Rozważ przeprowadzenie migracji do wystąpienia zarządzanego Azure SQL.

Więcej informacji: [Włączanie śledzenia zmian w usłudze Azure SQL](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>Zestawy CLR<a id="ClrAssemblies"></a>

**Title: zestawy SQL CLR nie są obsługiwane w Azure SQL Database**   
**Kategoria**: problem   


**Zharmonizowan**   
Azure SQL Database nie obsługuje zestawów SQL CLR. 


**Zaleca**   
Obecnie nie ma możliwości osiągnięcia tego w Azure SQL Database. Zalecane rozwiązania alternatywne będą wymagały zmiany kodu i bazy danych aplikacji, aby używały tylko zestawów obsługiwanych przez Azure SQL Database. Alternatywnie można przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [nieobsługiwane różnice w języku Transact-SQL w SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Dostawca usług kryptograficznych<a id="CryptographicProvider"></a>

**Title: znaleziono użycie dostawcy usług KRYPTOGRAFICZNYch lub ALTER CRYPTOGRAPHIC PROVIDER, który nie jest obsługiwany w Azure SQL Database**   
**Kategoria**: problem   

**Zharmonizowan**   
Azure SQL Database nie obsługuje instrukcji dostawcy usług KRYPTOGRAFICZNYch, ponieważ nie może uzyskać dostępu do plików. Zapoznaj się z sekcją obiekty, których dotyczy problem, aby poznać określone zastosowania instrukcji dostawcy usług KRYPTOGRAFICZNYch. Obiekty z `CREATE CRYPTOGRAPHIC PROVIDER` lub `ALTER CRYPTOGRAPHIC PROVIDER` nie będą działały prawidłowo po przeprowadzeniu migracji do Azure SQL Database.  


**Zaleca**   
Przejrzyj obiekty z `CREATE CRYPTOGRAPHIC PROVIDER` lub `ALTER CRYPTOGRAPHIC PROVIDER` . W każdym z tych obiektów, które są wymagane, Usuń korzystanie z tych funkcji. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

## <a name="cross-database-references"></a>Odwołania między bazami danych<a id="CrossDataseReferences"></a>

**Title: zapytania między bazami danych nie są obsługiwane w Azure SQL Database**   
**Kategoria**: problem   

**Zharmonizowan**   
Bazy danych na tym serwerze używają zapytań między bazami danych, które nie są obsługiwane w Azure SQL Database. 


**Zaleca**   
Azure SQL Database nie obsługuje zapytań między bazami danych. Zalecane są następujące akcje: 
- Migruj zależne bazy danych do Azure SQL Database i użyj funkcji Elastic Database Query (obecnie w wersji zapoznawczej) do wykonywania zapytań w bazach danych Azure SQL. 
- Przenieś zależne zestawy danych z innych baz danych do bazy, która jest migrowana. 
- Przeprowadź migrację do wystąpienia zarządzanego Azure SQL.
- Migruj do SQL Server na maszynie wirtualnej platformy Azure. 

Więcej informacji: [sprawdzanie Azure SQL Database zapytania Elastic Database (wersja zapoznawcza)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Zgodność bazy danych<a id="DbCompatLevelLowerThan100"></a>

**Title: Azure SQL Database nie obsługuje poziomów zgodności poniżej 100.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Poziom zgodności bazy danych jest cennym narzędziem ułatwiającym modernizację bazy danych, umożliwiając uaktualnienie aparatu bazy danych SQL Server, zachowując jednocześnie stan funkcjonalny połączenia aplikacji przez utrzymywanie tego samego poziomu zgodności bazy danych przed uaktualnieniem. Azure SQL Database nie obsługuje poziomów zgodności poniżej 100. 


**Zaleca**   
Oceń, czy funkcjonalność aplikacji jest nienaruszona, gdy poziom zgodności bazy danych zostanie uaktualniony do 100 w wystąpieniu zarządzanym Azure SQL. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

## <a name="database-mail"></a>Poczta bazy danych<a id="DatabaseMail"></a>

**Title: Poczta bazy danych nie jest obsługiwana w Azure SQL Database.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Ten serwer używa funkcji Poczta bazy danych, która nie jest obsługiwana w programie Azure SQL Database.


**Zaleca**   
Rozważ przeprowadzenie migracji do wystąpienia zarządzanego usługi Azure SQL, które obsługuje Poczta bazy danych.  Możesz również rozważyć użycie usługi Azure Functions i SendGrid, aby uzyskać funkcje poczty na Azure SQL Database.

Więcej informacji: [wysyłanie wiadomości e-mail z Azure SQL Database przy użyciu skryptu Azure Functions](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Alias podmiotu zabezpieczeń bazy danych<a id="DatabasePrincipalAlias"></a>

**Tytuł: SYS. DATABASE_PRINCIPAL_ALIASES jest wycofany i został usunięty.**   
**Kategoria**: problem   

**Zharmonizowan**   
Widoku. DATABASE_PRINCIPAL_ALIASES jest wycofany i został usunięty z Azure SQL Database.  


**Zaleca**   
Używaj ról zamiast aliasów.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>Opcja DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Tytuł: opcja SET DISABLE_DEF_CNST_CHK jest wycofana i została usunięta.**   
**Kategoria**: problem   

**Zharmonizowan**   
Opcja SET DISABLE_DEF_CNST_CHK jest wycofana i została usunięta z Azure SQL Database.  


Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Wskazówka FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Title: Wskazówka dotycząca zapytania FASTFIRSTROW jest wycofana i została usunięta.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Wskazówka zapytania FASTFIRSTROW jest wycofana i została usunięta w Azure SQL Database.  


**Zaleca**   
Zamiast opcji use zapytania FASTFIRSTROW OPTION (FAST n).

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Strumieni<a id="FileStream"></a>

**Title: element FILESTREAM nie jest obsługiwany w Azure SQL Database**   
**Kategoria**: problem   

**Zharmonizowan**   
Funkcja FILESTREAM, która umożliwia przechowywanie danych bez struktury, takich jak dokumenty tekstowe, obrazy i wideo w systemie plików NTFS, nie jest obsługiwana w Azure SQL Database. 


**Zaleca**   
Przekazanie plików bez struktury do magazynu obiektów blob platformy Azure i przechowywanie metadanych związanych z tymi plikami (nazwa, typ, lokalizacja adresu URL, klucz magazynu itp.) w Azure SQL Database. Może być konieczne ponowne rozpoczęcie tworzenia aplikacji w celu włączenia przesyłania strumieniowego obiektów BLOB do i z Azure SQL Database. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [przesyłanie strumieniowe obiektów BLOB do i z blogu usługi Azure SQL](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Serwer połączony<a id="LinkedServer"></a>

**Title: funkcje połączonego serwera nie są obsługiwane w Azure SQL Database**   
**Kategoria**: problem   

**Zharmonizowan**   
Połączone serwery umożliwiają aparatowi bazy danych SQL Server wykonywanie poleceń dotyczących OLE DB źródeł danych poza wystąpieniem SQL Server. 


**Zaleca**   
Azure SQL Database nie obsługuje funkcji połączonego serwera. Aby wyeliminować konieczność stosowania połączonych serwerów, zaleca się wykonanie następujących czynności: 
- Zidentyfikuj zależne zestawy danych ze zdalnych serwerów SQL Server i rozważ przeniesienie ich do migrowanej bazy. 
- Migruj zależne bazy danych na platformę Azure i korzystaj z funkcji Elastic Database Query (wersja zapoznawcza), aby wykonywać zapytania między bazami danych w programie Azure SQL Database. 

Więcej informacji: [sprawdzanie Azure SQL Database Elastic Query (wersja zapoznawcza)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>Narzędzie MS DTC<a id="MSDTCTransactSQL"></a>

**Title: rozpoczęcie transakcji ROZPROSZONEj nie jest obsługiwane w Azure SQL Database.**   
**Kategoria**: problem   

**Zharmonizowan**   
Transakcja rozproszona rozpoczęta po rozpoczęciu transakcji ROZPROSZONEj w języku Transact SQL i zarządzana przez firmę Microsoft Distributed Transaction Coordinator (MS DTC) nie jest obsługiwana w programie Azure SQL Database.  


**Zaleca**   
Zapoznaj się z sekcją "obiekty objęte wpływem" w Azure Migrate, aby wyświetlić wszystkie obiekty przy użyciu transakcji BEGIN centrów. Rozważ Migrowanie baz danych uczestników do wystąpienia zarządzanego usługi Azure SQL, w którym obsługiwane są transakcje rozproszone w wielu wystąpieniach (obecnie w wersji zapoznawczej). Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [transakcje na wielu serwerach dla wystąpienia zarządzanego usługi Azure SQL ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (bulk)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: funkcja OpenRowSet użyta w operacji zbiorczej ze źródłem danych spoza magazynu obiektów blob platformy Azure nie jest obsługiwana w Azure SQL Database.**   
**Kategoria**: problem   

**Opis** Funkcja OPENROWSET obsługuje operacje zbiorcze za pośrednictwem wbudowanego dostawcy ZBIORCZego, który umożliwia odczytywanie danych z pliku i zwracanie ich jako zestawu wierszy. Funkcja OPENROWSET ze źródłem danych spoza magazynu obiektów blob platformy Azure nie jest obsługiwana w Azure SQL Database.


**Zaleca**   
Azure SQL Database nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc pliki muszą zostać zaimportowane z usługi Azure Blob Storage. W związku z tym tylko źródło danych typu BLOB jest obsługiwane w funkcji OPENROWSET. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (dostawca)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Title: funkcja OpenRowSet z dostawcą SQL lub innym niż SQL nie jest obsługiwana w Azure SQL Database.**   
**Kategoria**: problem   

**Zharmonizowan**   
Funkcja OpenRowSet z dostawcą SQL lub innym niż SQL jest alternatywnym rozwiązaniem w przypadku uzyskiwania dostępu do tabel na połączonym serwerze i jest jednokrotną metodą ad hoc umożliwiającą łączenie się z danymi zdalnymi i uzyskiwanie do nich dostępu przy użyciu OLE DB. Funkcja OpenRowSet z dostawcą SQL lub innym niż SQL nie jest obsługiwana w Azure SQL Database.


**Zaleca**   
Azure SQL Database obsługuje OPENROWSET tylko do importowania z usługi Azure Blob Storage. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Lewe sprzężenie zewnętrzne inne niż ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: niezgodne z żadnymi stylami ANSI sprzężenie zewnętrzne jest wycofane i zostało usunięte.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Niezgodne z innym stylem ANSI sprzężenie zewnętrzne jest wycofane i zostało usunięte w Azure SQL Database. 


**Zaleca**   
Użyj składni sprzężenia ANSI.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Sprzężenie zewnętrzne z prawem innym niż ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: prawo zewnętrzne w stylu innym niż ANSI jest wycofane i zostało usunięte.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Niezgodne z prawem sprzężenie zewnętrzne w stylu nieansi jest przerywane i usuwane w Azure SQL Database. 


**Zaleca**   
Użyj składni sprzężenia ANSI.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Następna kolumna<a id="NextColumn"></a>

**Tytuł: tabele i kolumny o nazwie NEXT spowodują wystąpienie błędu w Azure SQL Database.**   
**Kategoria**: problem   

**Zharmonizowan**   
Wykryto tabele lub kolumny o nazwie NEXT. Sekwencje wprowadzone w Microsoft SQL Server używają standardowej funkcji NEXT VALUE FOR. Jeśli tabela lub kolumna jest nazywana dalej, a kolumna jest aliasem jako wartość, a jeśli standard ANSI zostanie pominięty, wynikiem instrukcji może być błąd.  


**Zaleca**   
Ponownie Napisz instrukcje, aby uwzględnić standard ANSI jako słowo kluczowe podczas aliasowania tabeli lub kolumny. Na przykład jeśli kolumna ma nazwę NEXT (dalej), a kolumna jest aliasem jako wartość, zapytanie `SELECT NEXT VALUE FROM TABLE` spowoduje wystąpienie błędu i należy je ponownie zapisać jako wartość z tabeli. Podobnie, gdy tabela jest nazywana dalej, a tabela jest aliasem jako wartość, zapytanie `SELECT Col1 FROM NEXT VALUE` spowoduje wystąpienie błędu i należy je napisać ponownie jako `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>INSTRUKCJI<a id="RAISERROR"></a>

**Title: wywołania instrukcji RAISERROR w stylu starszej metody muszą zostać zastąpione nowoczesnymi odpowiednikami.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Wywołania instrukcji RAISERROR, takie jak Poniższy przykład, są określane jako starsze niż style, ponieważ nie zawierają przecinków i nawiasów. `RAISERROR 50001 'this is a test'`. Ta metoda wywoływania instrukcji RAISERROR została wycofana i usunięta w Azure SQL Database. 


**Zaleca**   
Napisz ponownie instrukcję przy użyciu bieżącej składni instrukcji RAISERROR lub Oceń, czy nowoczesne podejście `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` jest możliwe.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Inspekcje serwera<a id="ServerAudits"></a>

**Title: Użyj funkcji inspekcji Azure SQL Database, aby zastąpić inspekcje serwera**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Inspekcje serwera nie są obsługiwane w Azure SQL Database.


**Zaleca**   
Należy wziąć pod uwagę Azure SQL Database funkcji inspekcji, aby zastąpić inspekcje serwera.  Usługa Azure SQL obsługuje inspekcję, a funkcje są bogatsze niż SQL Server. Usługa Azure SQL Database może przeprowadzać inspekcję różnych akcji i zdarzeń bazy danych, w tym: dostęp do danych, zmian schematu (DDL), zmian danych (DML), kont, ról i uprawnień (DCL, wyjątki zabezpieczeń). Inspekcja Azure SQL Database zwiększa możliwości organizacji, aby uzyskać szczegółowe informacje o zdarzeniach i zmianach w ich bazie danych, w tym o aktualizacjach i zapytaniach dotyczących danych. Alternatywnie można przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [inspekcja Azure SQL Database ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Poświadczenia serwera<a id="ServerCredentials"></a>

**Title: poświadczenia z zakresem serwera nie są obsługiwane w Azure SQL Database**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Poświadczenie jest rekordem zawierającym informacje o uwierzytelnianiu (poświadczenia) wymagane do nawiązania połączenia z zasobem spoza SQL Server. Azure SQL Database obsługuje poświadczenia bazy danych, ale nie te utworzone w zakresie SQL Server.   


**Zaleca**   
Azure SQL Database obsługuje poświadczenia w zakresie bazy danych. Konwertowanie poświadczeń z zakresem serwera na poświadczenia w zakresie bazy danych. Alternatywnie można przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [Tworzenie poświadczeń o zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**Title: funkcja Service Broker nie jest obsługiwana w programie Azure SQL Database**   
**Kategoria**: problem   

**Zharmonizowan**   
SQL Server Service Broker zapewnia natywną obsługę komunikatów i kolejkowania aplikacji w aparacie SQL Server Database. Funkcja Service Broker nie jest obsługiwana w programie Azure SQL Database.


**Zaleca**   
Funkcja Service Broker nie jest obsługiwana w programie Azure SQL Database. Rozważ migrację do wystąpienia zarządzanego usługi Azure SQL, które obsługuje brokera usług w tym samym wystąpieniu. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

## <a name="server-scoped-triggers"></a>Wyzwalacze z zakresem serwera<a id="ServerScopedTriggers"></a>

**Title: wyzwalacz z zakresem serwera nie jest obsługiwany w Azure SQL Database**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Wyzwalacz jest specjalnym rodzajem procedury składowanej, która jest wykonywana w odpowiedzi na określoną akcję w tabeli, np. wstawieniu, usunięciu lub aktualizacji danych. Wyzwalacze w zakresie serwera nie są obsługiwane w Azure SQL Database. Azure SQL Database nie obsługuje następujących opcji wyzwalaczy: w przypadku logowania, szyfrowania, z dołączaniem, nie do replikacji, opcja nazwy zewnętrznej (nie jest obsługiwana zewnętrzna Metoda), wszystkie opcje serwera (wyzwalacz DDL), wyzwalacz dla zdarzenia logowania (wyzwalacz logowania), Azure SQL Database nie obsługuje wyzwalaczy CLR.


**Zaleca**   
Zamiast tego użyj wyzwalacza poziomu bazy danych. Alternatywnie można przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>Zadania programu SQL Agent<a id="AgentJobs"></a>

**Title: zadania agenta SQL Server są niedostępne w programie Azure SQL Database**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Agent SQL Server to usługa systemu Microsoft Windows, która wykonuje zaplanowane zadania administracyjne, nazywane zadaniami w SQL Server. Zadania agenta SQL Server są niedostępne w programie Azure SQL Database. 


**Zalecenie** Korzystaj z elastycznych zadań (wersja zapoznawcza), które zastępują zadania agenta SQL Server w Azure SQL Database. Zadania Elastic Database dla Azure SQL Database umożliwiają niezawodne wykonywanie skryptów T-SQL, które rozciągają się na wiele baz danych podczas automatycznego ponawiania próby i zapewnienia ostatecznej gwarancji uzupełniania. Alternatywnie możesz rozważyć Migrowanie do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na platformie Azure Virtual Machines.

Więcej informacji: [wprowadzenie do zadania Elastic Database (wersja zapoznawcza) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>Rozmiar SQL Database<a id="SQLDBDatabaseSize"></a>

**Title: Azure SQL Database nie obsługuje rozmiaru bazy danych większej niż 100 TB.**   
**Kategoria**: problem   

**Zharmonizowan**   
Rozmiar bazy danych jest większy niż maksymalny obsługiwany rozmiar wynoszący 100 TB. 


**Zaleca**   
Oceń, czy dane mogą być archiwizowane lub kompresowane lub podzielonej na fragmenty w wielu bazach danych. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

Więcej informacji: [limity zasobów rdzeń wirtualny](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>Poczta SQL<a id="SqlMail"></a>

**Title: program SQL mail został wycofany.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Poczta SQL została wycofana i usunięta w Azure SQL Database.


**Zaleca**   
Rozważ migrację do wystąpienia zarządzanego usługi Azure SQL lub SQL Server na platformie Azure Virtual Machines i użyj Poczta bazy danych.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: wykryto instrukcje odwołujące się do usuniętych procedur składowanych w systemie, które nie są dostępne w Azure SQL Database.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Następujące nieobsługiwane systemy i rozszerzone procedury składowane nie mogą być używane w usłudze Azure SQL Database —,,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` .


**Zaleca**    
Usuń odwołania do nieobsługiwanych procedur systemowych, które zostały usunięte w Azure SQL Database.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Flagi śledzenia<a id="TraceFlags"></a>

**Title: Azure SQL Database nie obsługuje flag śledzenia**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Flagi śledzenia są używane do tymczasowego ustawiania określonych charakterystyk serwera lub do przełączania określonego zachowania. Flagi śledzenia są często używane do diagnozowania problemów z wydajnością lub debugowania procedur składowanych lub złożonych systemów komputerowych. Azure SQL Database nie obsługuje flag śledzenia. 


**Zaleca**   
Przejrzyj sekcję obiekty objęte wpływem w Azure Migrate, aby wyświetlić wszystkie flagi śledzenia, które nie są obsługiwane w programie Azure SQL Database, i Oceń, czy można je usunąć. Alternatywnie można przeprowadzić migrację do wystąpienia zarządzanego usługi Azure SQL, które obsługuje ograniczoną liczbę globalnych flag śledzenia lub SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Uwierzytelnianie systemu Windows<a id="WindowsAuthentication"></a>

**Title: Użytkownicy bazy danych zamapowane z uwierzytelnianiem systemu Windows (zabezpieczenia zintegrowane) nie są obsługiwani w Azure SQL Database.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Azure SQL Database obsługuje dwa typy uwierzytelniania 
- Uwierzytelnianie SQL: używa nazwy użytkownika i hasła 
- Uwierzytelnianie Azure Active Directory: używa tożsamości zarządzanych przez program Azure Active Directory i jest obsługiwana w przypadku domen zarządzanych i zintegrowanych. 

Użytkownicy bazy danych zamapowane z uwierzytelnianiem systemu Windows (zabezpieczenia zintegrowane) nie są obsługiwani w Azure SQL Database. 



**Zaleca**   
Sfederować Active Directory lokalnego z Azure Active Directory. Tożsamość systemu Windows można następnie zastąpić równoważnymi tożsamościami Azure Active Directory. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [możliwości zabezpieczeń SQL Database](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell nie jest obsługiwana w Azure SQL Database.**   
**Kategoria**: problem   

**Zharmonizowan**   
xp_cmdshell, który duplikuje powłokę poleceń systemu Windows i przekazuje ciąg na potrzeby wykonywania nie jest obsługiwany w Azure SQL Database. 


**Zaleca**   
Przejrzyj sekcję obiektów objętych wpływem w Azure Migrate, aby wyświetlić wszystkie obiekty przy użyciu xp_cmdshell i Oceń, czy można usunąć odwołanie do xp_cmdshell lub obiekt, którego dotyczy problem. Rozważ również Eksplorowanie Azure Automation, które zapewniają usługę automatyzacji i konfiguracji opartą na chmurze. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć migrację SQL Server do Azure SQL Database, zapoznaj się z [przewodnikiem migracji SQL Server do SQL Database](sql-server-to-sql-database-guide.md).

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat SQL Database, zobacz:
   - [Omówienie Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).
