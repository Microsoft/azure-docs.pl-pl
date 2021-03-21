---
title: Reguły oceny dla SQL Server do migracji wystąpienia zarządzanego SQL
description: Reguły oceny umożliwiające zidentyfikowanie problemów z wystąpieniem źródłowego SQL Server, które należy rozwiązać przed migracją do wystąpienia zarządzanego usługi Azure SQL.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054726"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>Reguły oceny dla SQL Server do migracji wystąpienia zarządzanego SQL
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Narzędzia migracji sprawdzają poprawność wystąpienia SQL Server źródłowego, uruchamiając kilka reguł oceny, aby zidentyfikować problemy, które należy rozwiązać przed migracją bazy danych SQL Server do wystąpienia zarządzanego Azure SQL. 

Ten artykuł zawiera listę reguł używanych do oceny możliwości migrowania bazy danych SQL Server do wystąpienia zarządzanego usługi Azure SQL. 

## <a name="analysiscommand-job"></a>Zadanie AnalysisCommand<a id="AnalysisCommandJob"></a>

**Title: etap zadania AnalysisCommand nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: Ostrzeżenie   


**Zharmonizowan**   
Jest to krok zadania, który uruchamia polecenie Analysis Services. Etap zadania AnalysisCommand nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.

**Zaleca**     
Przejrzyj sekcję dotyczącą obiektów, których dotyczy problem, w Azure Migrate, aby wyświetlić wszystkie zadania za pomocą kroku zadania usługi Analysis Service i Oceń, czy można usunąć krok zadania lub obiekt, którego dotyczy problem. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [SQL Server różnice w agencie w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Zadanie AnalysisQuery<a id="AnalysisQueryJob"></a>

**Title: etap zadania AnalysisQuery nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Jest to krok zadania, który uruchamia zapytanie Analysis Services. Etap zadania AnalysisQuery nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.


**Zaleca**   
Zapoznaj się z sekcją dotyczącej obiektów objętych problemem w Azure Migrate, aby wyświetlić wszystkie zadania za pomocą kroku zadanie zapytania usługi Analysis Service i Oceń, czy można usunąć etap zadania lub obiekt, którego dotyczy problem. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [SQL Server różnice w agencie w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Zestaw z pliku<a id="AssemblyFromFile"></a>

**Title: Instrukcja "CREATE ASSEMBLY" i "ALTER ASSEMBLY" z parametrem pliku nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Wystąpienie zarządzane Azure SQL nie może uzyskać dostępu do udziałów plików ani folderów systemu Windows. Zapoznaj się z sekcją "obiekty objęte wpływem" dotyczącą konkretnych zastosowania instrukcji BULK INSERT, które nie odwołują się do obiektu blob platformy Azure. Obiekty z "BULK INSERT", w których źródło nie jest magazynem obiektów blob platformy Azure, nie będzie działały po przeprowadzeniu migracji do wystąpienia zarządzanego usługi Azure SQL.


**Zaleca**   
Przed przeprowadzeniem migracji do wystąpienia zarządzanego Azure SQL należy przekonwertować instrukcje BULK INSERT, które używają plików lokalnych lub udziałów plików do używania plików z usługi Azure Blob Storage. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

Więcej informacji: [różnice środowiska CLR w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Wstawianie zbiorcze<a id="BulkInsert"></a>

**Title: BULK INSERT ze źródłem danych obiektów BLOB spoza platformy Azure nie jest obsługiwana w wystąpieniu zarządzanym Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Wystąpienie zarządzane Azure SQL nie może uzyskać dostępu do udziałów plików ani folderów systemu Windows. Zapoznaj się z sekcją "obiekty objęte wpływem" dotyczącą konkretnych zastosowania instrukcji BULK INSERT, które nie odwołują się do obiektu blob platformy Azure. Obiekty z "BULK INSERT", w których źródło nie jest magazynem obiektów blob platformy Azure, nie będzie działały po przeprowadzeniu migracji do wystąpienia zarządzanego usługi Azure SQL.


**Zaleca**   
Przed przeprowadzeniem migracji do wystąpienia zarządzanego Azure SQL należy przekonwertować instrukcje BULK INSERT, które używają plików lokalnych lub udziałów plików do używania plików z usługi Azure Blob Storage.

Więcej informacji: [zbiorcze Wstawianie i OPENROWSET różnic w wystąpieniu zarządzanym Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Zabezpieczenia CLR<a id="ClrStrictSecurity"></a>

**Title: zestawy CLR oznaczone jako bezpieczne lub EXTERNAL_ACCESS są uważane za niebezpieczne**   
**Kategoria**: problem   

**Zharmonizowan**   
Tryb CLR Strict Security jest wymuszany w wystąpieniu zarządzanym usługi Azure SQL. Ten tryb jest domyślnie włączony i wprowadza istotne zmiany dotyczące baz danych zawierających zestawy CLR zdefiniowane przez użytkownika jako bezpieczne lub EXTERNAL_ACCESS.


**Zaleca**   
Środowisko CLR używa zabezpieczeń dostępu kodu (CAS) w .NET Framework, który nie jest już obsługiwany jako granica zabezpieczeń. Począwszy od SQL Server 2017 (14. x) aparatu bazy danych, w `sp_configure` celu zwiększenia bezpieczeństwa zestawów CLR wprowadzono opcję o nazwie CLR Strict Security. Środowisko CLR Strict Security jest domyślnie włączone i traktuje bezpieczne i EXTERNAL_ACCESS zestawy CLR, tak jakby były oznaczone jako niebezpieczne. Gdy środowisko CLR rygorystyczne zabezpieczenia jest wyłączone, zestaw CLR utworzony z PERMISSION_SET = SAFE może mieć dostęp do zewnętrznych zasobów systemu, wywoływać kod niezarządzany i uzyskiwać uprawnienia sysadmin. Po włączeniu ścisłych zabezpieczeń wszystkie zestawy, które nie są podpisane, nie będą mogły zostać załadowane. Ponadto jeśli baza danych ma bezpieczne lub EXTERNAL_ACCESS zestawy, instrukcje przywracania lub dołączania bazy danych mogą zakończyć się niepowodzeniem, ale te zestawy mogą nie zostać załadowane. Aby załadować zestawy, należy zmienić lub porzucić i ponownie utworzyć każdy zestaw, tak aby był podpisany przy użyciu certyfikatu lub klucza asymetrycznego, który ma odpowiednie Logowanie przy użyciu uprawnienia do niebezpiecznego zestawu na serwerze.

Więcej informacji: [rygorystyczne zabezpieczenia CLR](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Klauzula COMPUTE<a id="ComputeClause"></a>

**Title: klauzula COMPUTE jest wycofana i została usunięta.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Klauzula COMPUTE generuje sumy, które są wyświetlane jako dodatkowe kolumny podsumowujące na końcu zestawu wyników. Jednakże ta klauzula nie jest już obsługiwana w wystąpieniu zarządzanym usługi Azure SQL.



**Zaleca**   
Moduł T-SQL musi zostać ponownie zapisany przy użyciu operatora ROLLUP. Poniższy kod ilustruje sposób wymiany obliczeń z pakietem zbiorczym: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Dostawca usług kryptograficznych<a id="CryptographicProvider"></a>

**Title: znaleziono użycie dostawcy usług KRYPTOGRAFICZNYch lub ALTER CRYPTOGRAPHIC PROVIDER, który nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Wystąpienie zarządzane Azure SQL nie obsługuje instrukcji dostawcy usług KRYPTOGRAFICZNYch, ponieważ nie może uzyskać dostępu do plików. Zapoznaj się z sekcją obiekty, których dotyczy problem, aby poznać określone zastosowania instrukcji dostawcy usług KRYPTOGRAFICZNYch. Obiekty z elementem "CREATE CRYPTOGRAPHIC PROVIDER" lub "ALTER CRYPTOGRAPHIC PROVIDER" nie będą działały prawidłowo po migracji do wystąpienia zarządzanego usługi Azure SQL.


**Zaleca**   
Przejrzyj obiekty z opcją "Utwórz dostawcę usług KRYPTOGRAFICZNYch" lub "ALTER CRYPTOGRAPHIC PROVIDER". W każdym z tych obiektów, które są wymagane, Usuń korzystanie z tych funkcji. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

Więcej informacji: [różnice między dostawcami kryptograficznymi w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Zgodność bazy danych<a id="DbCompatLevelLowerThan100"></a>

**Title: poziom zgodności bazy danych poniżej 100 nie jest obsługiwany**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Poziom zgodności bazy danych jest cennym narzędziem ułatwiającym modernizację bazy danych, umożliwiając uaktualnienie aparatu bazy danych SQL Server, zachowując jednocześnie stan funkcjonalny połączenia aplikacji przez utrzymywanie tego samego poziomu zgodności bazy danych przed uaktualnieniem. Wystąpienie zarządzane Azure SQL nie obsługuje poziomów zgodności poniżej 100. Gdy baza danych o poziomie zgodności poniżej 100 zostanie przywrócona w wystąpieniu zarządzanym Azure SQL, poziom zgodności zostanie uaktualniony do 100. 


**Zalecenie**... Oceń, czy funkcjonalność aplikacji jest nienaruszona, gdy poziom zgodności bazy danych zostanie uaktualniony do 100 w wystąpieniu zarządzanym Azure SQL. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [obsługiwane poziomy zgodności w wystąpieniu zarządzanym Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Alias podmiotu zabezpieczeń bazy danych<a id="DatabasePrincipalAlias"></a>

**Tytuł: SYS. DATABASE_PRINCIPAL_ALIASES jest wycofany i został usunięty.**   
**Kategoria**: problem   

**Zharmonizowan**   
Widoku. DATABASE_PRINCIPAL_ALIASES jest wycofana i została usunięta z wystąpienia zarządzanego Azure SQL.


**Zaleca**   
Używaj ról zamiast aliasów.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Opcja DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Tytuł: opcja SET DISABLE_DEF_CNST_CHK jest wycofana i została usunięta.**   
**Kategoria**: problem   

**Zharmonizowan**   
Opcja SET DISABLE_DEF_CNST_CHK jest wycofana i została usunięta z wystąpienia zarządzanego Azure SQL.


Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Wskazówka FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Title: Wskazówka dotycząca zapytania FASTFIRSTROW jest wycofana i została usunięta.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Wskazówka zapytania FASTFIRSTROW jest wycofana i została usunięta z wystąpienia zarządzanego Azure SQL.


**Zaleca**   
Zamiast opcji use zapytania FASTFIRSTROW OPTION (FAST n).

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>Strumieni<a id="FileStream"></a>

**Title: FILESTREAM i FileTable nie są obsługiwane w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Funkcja FILESTREAM, która umożliwia przechowywanie danych bez struktury, takich jak dokumenty tekstowe, obrazy i filmy wideo w systemie plików NTFS, nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL. **Nie można migrować tej bazy danych, ponieważ nie można przywrócić kopii zapasowej zawierającej grupy plików FILESTREAM w wystąpieniu zarządzanym usługi Azure SQL.**


**Zaleca**   
Przekazanie plików bez struktury do magazynu obiektów blob platformy Azure i przechowywanie metadanych związanych z tymi plikami (nazwa, typ, lokalizacja adresu URL, klucz magazynu itp.) w wystąpieniu zarządzanym usługi Azure SQL. Może być konieczne ponowne rozpoczęcie tworzenia aplikacji w celu włączenia przesyłania strumieniowego obiektów BLOB do i z wystąpienia zarządzanego usługi Azure SQL. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [przesyłanie strumieniowe obiektów BLOB do i z blogu SQL Azure](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>Heterogeniczna usługa MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Title: Rozpocznij transakcję rozproszoną z serwerem zdalnym innym niż SQL Server nie jest obsługiwana w wystąpieniu zarządzanym Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Transakcja rozproszona rozpoczęta po rozpoczęciu transakcji ROZPROSZONEj w języku Transact SQL i zarządzana przez firmę Microsoft Distributed Transaction Coordinator (MS DTC) nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL, jeśli serwer zdalny nie jest SQL Server. 


**Zaleca**   
Zapoznaj się z sekcją "obiekty objęte wpływem" w Azure Migrate, aby wyświetlić wszystkie obiekty przy użyciu transakcji BEGIN centrów. Rozważ Migrowanie baz danych uczestników do wystąpienia zarządzanego usługi Azure SQL, w którym obsługiwane są transakcje rozproszone w wielu wystąpieniach (obecnie w wersji zapoznawczej). Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [transakcje na wielu serwerach dla wystąpienia zarządzanego usługi Azure SQL ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Jednorodna usługa MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Title: rozpoczęcie transakcji ROZPROSZONEj jest obsługiwane na wielu serwerach dla wystąpienia zarządzanego Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Transakcja rozproszona rozpoczęta po rozpoczęciu transakcji ROZPROSZONEj języka Transact SQL i zarządzana przez firmę Microsoft Distributed Transaction Coordinator (MS DTC) jest obsługiwana na wielu serwerach dla wystąpienia zarządzanego usługi Azure SQL.


**Zaleca**   
Zapoznaj się z sekcją "obiekty objęte wpływem" w Azure Migrate, aby wyświetlić wszystkie obiekty przy użyciu transakcji BEGIN centrów. Rozważ Migrowanie baz danych uczestników do wystąpienia zarządzanego usługi Azure SQL, w którym obsługiwane są transakcje rozproszone w wielu wystąpieniach (obecnie w wersji zapoznawczej). Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

Więcej informacji: [transakcje na wielu serwerach dla wystąpienia zarządzanego usługi Azure SQL](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Połączony serwer (dostawca inny niż SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Title: połączony serwer z dostawcą innym niż SQL Server nie jest obsługiwany w wystąpieniu zarządzanym Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Połączone serwery umożliwiają aparatowi bazy danych SQL Server wykonywanie poleceń dotyczących OLE DB źródeł danych poza wystąpieniem SQL Server. Połączony serwer z dostawcą innym niż SQL Server nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL. 


**Zaleca**   
Wystąpienie zarządzane usługi Azure SQL nie obsługuje funkcji połączonego serwera, jeśli dostawca serwera zdalnego nie jest SQL Server, takich jak Oracle, Sybase itd. 

Aby wyeliminować konieczność stosowania połączonych serwerów, zaleca się wykonanie następujących czynności: 
- Zidentyfikuj zależne bazy danych ze zdalnych serwerów innych niż SQL i rozważ przeniesienie ich do migrowanej bazy danych. 
- Przeprowadź migrację zależnych baz danych do obsługiwanych celów, takich jak wystąpienie zarządzane SQL, SQL Database, usługa Azure Synapse SQL i wystąpienia SQL Server. 
- Rozważ utworzenie połączonego serwera między wystąpieniem zarządzanym usługi Azure SQL i SQL Server na maszynie wirtualnej platformy Azure (SQL VM).  Następnie z maszyny wirtualnej SQL Utwórz połączony serwer do bazy danych Oracle, Sybase itd. Takie podejście obejmuje dwa przeskoki, ale może być używane jako tymczasowe obejście.  
- Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [różnice między połączonymi serwerami w wystąpieniu zarządzanym usługi Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Zadanie scalania<a id="MergeJob"></a>

**Title: etap zadania scalania nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Jest to krok zadania, który aktywuje agenta scalania replikacji. Agent scalania replikacji to plik wykonywalny narzędzia, który stosuje początkową migawkę przechowywaną w tabelach bazy danych do subskrybentów. Scala również przyrostowe zmiany danych, które wystąpiły w wydawcę po utworzeniu początkowej migawki, a następnie uzgadnia konflikty zgodnie z regułami skonfigurowanymi lub przy użyciu utworzonego niestandardowego programu rozpoznawania. Etap zadania scalania nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.


**Zaleca**   
Przejrzyj sekcję dotyczące obiektów objętych problemem w Azure Migrate, aby wyświetlić wszystkie zadania za pomocą kroku scalania zadań i Oceń, czy można usunąć etap zadania lub obiekt, którego dotyczy problem. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [SQL Server różnice w agencie w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI rozmiar bazy danych<a id="MIDatabaseSize<"></a>

**Title: wystąpienie zarządzane Azure SQL nie obsługuje rozmiaru bazy danych większej niż 8 TB.**   
**Kategoria**: problem   

**Zharmonizowan**   
Rozmiar bazy danych jest większy niż maksymalna ilość miejsca zarezerwowanego wystąpienia. **Nie można wybrać tej bazy danych do migracji, ponieważ rozmiar przekracza dozwolony limit.**


**Zaleca**   
Oceń, czy dane mogą być archiwizowane lub podzielonej na fragmenty w wielu bazach danych. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [charakterystyki generowania sprzętu wystąpienia zarządzanego usługi Azure SQL ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Rozmiar wystąpienia MI<a id="MIInstanceSize<"></a>

**Title: maksymalny rozmiar magazynu wystąpień w wystąpieniu zarządzanym Azure SQL nie może być większy niż 8 TB.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Rozmiar wszystkich baz danych jest większy niż maksymalna ilość miejsca zarezerwowanego wystąpienia.  


**Zaleca**   
Rozważ migrację baz danych do różnych wystąpień zarządzanych usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure, jeśli wszystkie bazy danych muszą znajdować się w tym samym wystąpieniu. 

Więcej informacji: [charakterystyki generowania sprzętu wystąpienia zarządzanego usługi Azure SQL ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Wiele plików dziennika<a id="MultipleLogFiles<"></a>

**Title: wystąpienie zarządzane Azure SQL nie obsługuje wielu plików dziennika.**   
**Kategoria**: problem   

**Zharmonizowan**   
SQL Server umożliwia bazie danych logowanie do wielu plików. Ta baza danych ma wiele plików dziennika, które nie są obsługiwane w wystąpieniu zarządzanym usługi Azure SQL. * * Nie można migrować tej bazy danych, ponieważ nie można przywrócić kopii zapasowej w wystąpieniu zarządzanym usługi Azure SQL. 
**

**Zaleca**   
Wystąpienie zarządzane Azure SQL obsługuje tylko jeden dziennik na bazę danych. Przed migracją tej bazy danych do platformy Azure należy usunąć wszystkie pliki dziennika oprócz jednego z nich: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Więcej informacji: [Opcje nieobsługiwanej bazy danych w wystąpieniu zarządzanym Azure SQL  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Następna kolumna<a id="NextColumn"></a>

**Tytuł: tabele i kolumny o nazwie NEXT spowodują wystąpienie błędu w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Wykryto tabele lub kolumny o nazwie NEXT. Sekwencje wprowadzone w Microsoft SQL Server używają standardowej funkcji NEXT VALUE FOR. Jeśli tabela lub kolumna jest nazywana dalej, a kolumna jest aliasem jako wartość, a jeśli standard ANSI zostanie pominięty, wynikiem instrukcji może być błąd.


**Zaleca**   
Ponownie Napisz instrukcje, aby uwzględnić standard ANSI jako słowo kluczowe podczas aliasowania tabeli lub kolumny. Na przykład jeśli kolumna ma nazwę NEXT (dalej), a kolumna jest aliasem jako wartość, zapytanie wybierz NASTĘPNą wartość z tabeli spowoduje wystąpienie błędu i należy je ponownie zapisać jako wartość z tabeli. Podobnie, gdy tabela jest nazywana dalej, a ta tabela jest aliasem jako wartość, zapytanie wybierz pozycję Kol1 z następnej wartości spowoduje wystąpienie błędu i należy je ponownie zapisać jako pozycję Kol1 z następnego jako wartość.



## <a name="non-ansi-style-left-outer-join"></a>Styl niezgodny ze standardem ANSI — sprzężenie zewnętrzne<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: niezgodne z żadnymi stylami ANSI sprzężenie zewnętrzne jest wycofane i zostało usunięte.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Niezgodne z innym stylem ANSI sprzężenie zewnętrzne jest wycofane i zostało usunięte z wystąpienia zarządzanego Azure SQL. 


**Zaleca**   
Użyj składni sprzężenia ANSI.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Sprzężenie zewnętrzne prawego stylu bez ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: prawo zewnętrzne w stylu innym niż ANSI jest wycofane i zostało usunięte.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Niezgodne z prawem sprzężenie zewnętrzne w stylu nieansi jest przerywane i usuwane w wystąpieniu zarządzanym Azure SQL. 



Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Zaleca**   
Użyj składni sprzężenia ANSI.

## <a name="databases-exceed-100"></a>Bazy danych przekraczają 100 <a id="NumDbExceeds100"></a>

**Title: wystąpienie zarządzane SQL Azure obsługuje maksymalnie 100 baz danych na wystąpienie.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Maksymalna liczba baz danych obsługiwanych w wystąpieniu zarządzanym Azure SQL to 100, chyba że osiągnięto limit rozmiaru magazynu wystąpień.



**Zaleca**   
Rozważ migrację baz danych do różnych wystąpień zarządzanych usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure, jeśli wszystkie bazy danych muszą znajdować się w tym samym wystąpieniu. 

Więcej informacji: [limity zasobów wystąpienia zarządzanego usługi Azure SQL ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (Źródło danych inne niż BLOB)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: funkcja OpenRowSet użyta w operacji zbiorczej ze źródłem danych spoza magazynu obiektów blob platformy Azure nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Funkcja OPENROWSET obsługuje operacje zbiorcze za pośrednictwem wbudowanego dostawcy ZBIORCZego, który umożliwia odczytywanie danych z pliku i zwracanie ich jako zestawu wierszy. Funkcja OPENROWSET ze źródłem danych spoza magazynu obiektów blob platformy Azure nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL. 



**Zaleca**   
Funkcja OPENROWSET może służyć do wykonywania zapytań tylko w wystąpieniach SQL Server (zarządzane, lokalne lub w Virtual Machines). Tylko wartości SQLNCLI, SQLNCLI11 i SQLOLEDB są obsługiwane jako dostawcy. W związku z tym Akcja zalecenia polega na zidentyfikowaniu zależnych baz danych ze zdalnych serwerów innych niż SQL i rozważ przeniesienie ich do migrowanej bazy danych. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [zbiorcze Wstawianie i OPENROWSET różnic w wystąpieniu zarządzanym Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (dostawca inny niż SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Title: funkcja OpenRowSet z dostawcą innym niż SQL nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
Ta metoda jest alternatywą dla uzyskiwania dostępu do tabel na połączonym serwerze i jest metodą jednokrotnego łączenia i uzyskiwania dostępu do danych zdalnych przy użyciu OLE DB. Funkcja OpenRowSet z dostawcą innym niż SQL nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL. 



**Zaleca**   
Funkcja OPENROWSET może służyć do wykonywania zapytań tylko w wystąpieniach SQL Server (zarządzane, lokalne lub w Virtual Machines). Tylko wartości SQLNCLI, SQLNCLI11 i SQLOLEDB są obsługiwane jako dostawcy. W związku z tym Akcja zalecenia polega na zidentyfikowaniu zależnych baz danych ze zdalnych serwerów innych niż SQL i rozważ przeniesienie ich do migrowanej bazy danych.

Więcej informacji: [zbiorcze Wstawianie i OPENROWSET różnic w wystąpieniu zarządzanym Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Zadanie programu PowerShell<a id="PowerShellJob"></a>

**Title: krok zadania programu PowerShell nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Jest to krok zadania, który uruchamia skrypt programu PowerShell. Krok zadania programu PowerShell nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL. 



**Zaleca**   
Przejrzyj sekcję dotyczące obiektów objętych problemem w Azure Migrate, aby wyświetlić wszystkie zadania za pomocą kroku zadania programu PowerShell, i Oceń, czy można usunąć krok zadania lub obiekt, którego dotyczy problem.  Oceń, czy Azure Automation mogą być używane. Alternatywnie możesz przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure

Więcej informacji: [SQL Server różnice w agencie w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Zadanie czytnika kolejki<a id="QueueReaderJob"></a>

**Title: etap zadania czytnika kolejki nie jest obsługiwany w wystąpieniu zarządzanym Azure SQL.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Jest to krok zadania, który aktywuje agenta czytnika kolejki replikacji. Agent czytnika kolejki replikacji jest plikiem wykonywalnym, który odczytuje komunikaty przechowywane w kolejce Microsoft SQL Server lub w kolejce komunikatów firmy Microsoft, a następnie stosuje te komunikaty do wydawcy. Agent czytnika kolejki jest używany z migawkami i publikacjami transakcyjnymi, które zezwalają na aktualizację w kolejce. Etap zadania czytnika kolejki nie jest obsługiwany w wystąpieniu zarządzanym usługi Azure SQL.


**Zaleca**   
Zapoznaj się z sekcją obiekty objęte wpływem w Azure Migrate, aby wyświetlić wszystkie zadania za pomocą kroku zadanie czytnika kolejki i Oceń, czy można usunąć krok zadania lub obiekt, którego dotyczy problem. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [SQL Server różnice w agencie w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>INSTRUKCJI<a id="RAISERROR"></a>

**Title: wywołania instrukcji RAISERROR w stylu starszej metody muszą zostać zastąpione nowoczesnymi odpowiednikami.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Wywołania instrukcji RAISERROR, takie jak Poniższy przykład, są określane jako starsze niż style, ponieważ nie zawierają przecinków i nawiasów. RAISERROR 50001 "to jest test". Ta metoda wywoływania instrukcji RAISERROR jest przerywana i usuwana w wystąpieniu zarządzanym usługi Azure SQL.



**Zaleca**   
Napisz ponownie instrukcję przy użyciu bieżącej składni instrukcji RAISERROR lub Oceń, czy nowoczesne podejście `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` jest możliwe.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Broker usług<a id="ServiceBrokerWithNonLocalAddress"></a>

**Title: funkcja Service Broker jest częściowo obsługiwana w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   

**Zharmonizowan**   
SQL Server Service Broker zapewnia natywną obsługę komunikatów i kolejkowania aplikacji w aparacie SQL Server Database. Ta baza danych ma Service Broker jest włączone dla wielu wystąpień, co nie jest obsługiwane w wystąpieniu zarządzanym usługi Azure SQL. 


**Zaleca**   
Wystąpienie zarządzane Azure SQL nie obsługuje brokera usług dla wielu wystąpień, tj. lokalizacji, w której adres nie jest lokalny. Przed przeprowadzeniem migracji tej bazy danych do platformy Azure należy wyłączyć Service Broker przy użyciu następującego polecenia: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Ponadto może być konieczne usunięcie lub zatrzymanie punktu końcowego Service Broker, aby zapobiec odbieraniu komunikatów w wystąpieniu programu SQL. Po migracji bazy danych na platformę Azure Możesz zajrzeć do funkcji Azure Service Bus, aby zaimplementować ogólny system obsługi komunikatów oparty na chmurze, a nie Service Broker. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure. 

Więcej informacji: [Service Broker różnice w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>Poczta SQL<a id="SqlMail"></a>

**Title: program SQL mail został wycofany.**   
**Kategoria**: Ostrzeżenie   


**Zharmonizowan**   
Program SQL mail został wycofany i usunięty z wystąpienia zarządzanego Azure SQL.



**Zaleca**   
Użyj Poczta bazy danych.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: wykryto instrukcje odwołujące się do usuniętych procedur składowanych w systemie, które nie są dostępne w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: Ostrzeżenie   

**Zharmonizowan**   
Następujące nieobsługiwane systemy i rozszerzone procedury składowane nie mogą być używane w wystąpieniu zarządzanym Azure SQL —,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` i `sp_activedirectory_start` . 




**Zaleca**   
Usuń odwołania do nieobsługiwanych procedur systemowych, które zostały usunięte z wystąpienia zarządzanego usługi Azure SQL.

Więcej informacji: nieobsługiwane [funkcje aparatu bazy danych w SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Zadanie Transact-SQL<a id="TransactSqlJob"></a>

**Title: krok zadania TSQL zawiera nieobsługiwane polecenia w wystąpieniu zarządzanym usługi Azure SQL**   
**Kategoria**: Ostrzeżenie   


**Zharmonizowan**   
Jest to etap zadania, który uruchamia skrypty TSQL w zaplanowanym czasie. Krok zadania TSQL obejmuje nieobsługiwane polecenia, które nie są obsługiwane w wystąpieniu zarządzanym usługi Azure SQL.



**Zaleca**   
Zapoznaj się z sekcją obiekty objęte wpływem w Azure Migrate, aby wyświetlić wszystkie zadania, które zawierają nieobsługiwane polecenia w wystąpieniu zarządzanym usługi Azure SQL, i Oceń, czy można usunąć etap zadania lub obiekt, którego dotyczy problem. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [SQL Server różnice w agencie w wystąpieniu zarządzanym usługi Azure SQL ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Flagi śledzenia<a id="TraceFlags"></a>

**Title: znaleziono flagi śledzenia nieobsługiwane w wystąpieniu zarządzanym Azure SQL**   
**Kategoria**: Ostrzeżenie   


**Zharmonizowan**   
Wystąpienie zarządzane Azure SQL obsługuje tylko ograniczoną liczbę globalnych flag śledzenia. Flagi śledzenia sesji nie są obsługiwane.



**Zaleca**   
Przejrzyj sekcję dotyczące obiektów objętych problemem w Azure Migrate, aby wyświetlić wszystkie flagi śledzenia, które nie są obsługiwane w wystąpieniu zarządzanym usługi Azure SQL, i Oceń, czy można je usunąć. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [flagi śledzenia](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Uwierzytelnianie systemu Windows<a id="WindowsAuthentication"></a>

**Title: Użytkownicy bazy danych zamapowane z uwierzytelnianiem systemu Windows (zabezpieczenia zintegrowane) nie są obsługiwani w wystąpieniu zarządzanym Azure SQL**   
**Kategoria**: Ostrzeżenie   


**Zharmonizowan**   
Wystąpienie zarządzane Azure SQL obsługuje dwa typy uwierzytelniania: 
- Uwierzytelnianie SQL, które używa nazwy użytkownika i hasła
- Uwierzytelnianie usługi Azure Active Directory, które korzysta z tożsamości zarządzanej przez usługę Azure Active Directory i jest obsługiwane w przypadku zarządzanych i zintegrowanych domen. 

Użytkownicy bazy danych zamapowane z uwierzytelnianiem systemu Windows (zabezpieczenia zintegrowane) nie są obsługiwani w wystąpieniu zarządzanym usługi Azure SQL. 


**Zaleca**   
Sfederować Active Directory lokalnego z Azure Active Directory. Tożsamość systemu Windows można następnie zastąpić równoważnymi tożsamościami Azure Active Directory. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [funkcje zabezpieczeń wystąpienia zarządzanego SQL](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: xp_cmdshell nie jest obsługiwana w wystąpieniu zarządzanym usługi Azure SQL.**   
**Kategoria**: problem   


**Zharmonizowan**   
Xp_cmdshell, który duplikuje powłokę poleceń systemu Windows i przekazuje ciąg na potrzeby wykonywania nie jest obsługiwany w wystąpieniu zarządzanym Azure SQL. 



**Zaleca**   
Przejrzyj sekcję obiektów objętych wpływem w Azure Migrate, aby wyświetlić wszystkie obiekty przy użyciu xp_cmdshell i Oceń, czy można usunąć odwołanie do xp_cmdshell lub obiekt, którego dotyczy problem. Rozważ Eksplorowanie Azure Automation, które zapewniają usługę automatyzacji i konfiguracji opartą na chmurze. Alternatywnie można przeprowadzić migrację do SQL Server na maszynie wirtualnej platformy Azure.

Więcej informacji: [różnice procedury składowanej w wystąpieniu zarządzanym usługi Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć migrację SQL Server do wystąpienia zarządzanego usługi Azure SQL, zapoznaj się z [przewodnikiem migracji wystąpień zarządzanych SQL Server do bazy danych SQL](sql-server-to-managed-instance-guide.md).

- Macierz usług i narzędzi firmy Microsoft i innych firm, które są dostępne w celu ułatwienia pracy z różnymi scenariuszami bazy danych i migracji danych oraz zadaniami specjalistycznymi, można znaleźć w temacie [Usługa i narzędzia do migracji danych](../../../dms/dms-tools-matrix.md).

- Aby dowiedzieć się więcej na temat wystąpienia zarządzanego Azure SQL, zobacz:
   - [Warstwy usług w wystąpieniu zarządzanym usługi Azure SQL](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Różnice między SQL Server i wystąpieniem zarządzanym usługi Azure SQL](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kalkulator całkowitego kosztu posiadania na platformę Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Aby dowiedzieć się więcej na temat platformy i cyklu wdrażania migracji w chmurze, zobacz
   -  [Podręcznik Cloud Adoption Framework dla platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Najlepsze rozwiązania związane z wyceną i ustalaniem wielkości obciążeń migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Aby ocenić warstwę dostępu do aplikacji, zobacz [Data Access Migration Toolkit (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Aby uzyskać szczegółowe informacje na temat przeprowadzania testów warstwy dostępu do danych A/B, zobacz [Asystent eksperymentowania z bazą danych](/sql/dea/database-experimentation-assistant-overview).