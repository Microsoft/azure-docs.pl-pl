---
title: Rozpoznawanie różnic w języku T-SQL — migracja
description: Instrukcje języka Transact-SQL, które są mniej niż w pełni obsługiwane w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 1e286b2329cb98d580bbf64071ff8767db304a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461877"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database

Podczas [migrowania bazy danych](migrate-to-database-from-sql-server.md) z SQL Server do Azure SQL Database, przed migracją bazy danych SQL Server należy wykonać pewne zmiany. Ten artykuł zawiera wskazówki ułatwiające wykonywanie tych czynności w ramach tego zadania i zrozumienie podstawowych przyczyn, dla których konieczne jest ponowne inżynierowanie. Aby wykryć niezgodności, użyj [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Omówienie

Większość funkcji języka Transact-SQL, które są używane przez aplikacje, jest w pełni obsługiwana zarówno w Microsoft SQL Server, jak i Azure SQL Database. Na przykład podstawowe składniki SQL, takie jak typy danych, operatory, ciągi, operacje arytmetyczne, logiczne i kursory działają identycznie w SQL Server i SQL Database. Istnieją jednak pewne różnice w języku T-SQL w przypadku elementów języka DDL (definicja danych) i DML (Language operowania danymi), które wystąpiły w instrukcjach i zapytaniach języka T-SQL, które są tylko częściowo obsługiwane (które omówiono w dalszej części tego artykułu).

Ponadto istnieją pewne funkcje i składnia, które nie są obsługiwane przez program, ponieważ Azure SQL Database zaprojektowano w celu izolowania funkcji z zależności od bazy danych Master i systemu operacyjnego. W związku z tym większość działań na poziomie serwera jest nieodpowiedni dla SQL Database. Instrukcje i Opcje języka T-SQL nie są dostępne, jeśli skonfigurują opcje na poziomie serwera, składniki systemu operacyjnego lub określają konfigurację systemu plików. Gdy takie możliwości są wymagane, odpowiednia alternatywa jest często dostępna w inny sposób od SQL Database lub z innej funkcji lub usługi platformy Azure.

Na przykład wysoka dostępność jest wbudowana w Azure SQL Database przy użyciu technologii podobnej do [zawsze dostępnych grup dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Instrukcje języka T-SQL powiązane z grupami dostępności nie są obsługiwane przez SQL Database, a dynamiczne widoki zarządzania powiązane z zawsze włączonymi grupami dostępności również nie są obsługiwane.

Aby zapoznać się z listą funkcji obsługiwanych i nieobsługiwanych przez SQL Database, zobacz [Azure SQL Database porównanie funkcji](features-comparison.md). Lista na tej stronie zawiera uzupełnienie artykułu wskazówki i funkcje oraz koncentruje się na instrukcjach języka Transact-SQL.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instrukcje języka Transact-SQL zawierające częściowe różnice

Są dostępne podstawowe instrukcje języka DDL (Data Definition Language), ale niektóre instrukcje języka DDL mają rozszerzenia związane z rozmieszczeniem dysku i nieobsługiwanymi funkcjami.

- Instrukcje CREATE i ALTER DATABASE zawierają ponad trzy opcje. Instrukcje obejmują umieszczanie plików, FILESTREAM i opcje brokera usług, które mają zastosowanie tylko do SQL Server. Może to nie mieć znaczenia w przypadku tworzenia baz danych przed migracją, ale Jeśli migrujesz kod T-SQL, który tworzy bazy danych, należy porównać opcję [Utwórz bazę danych (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql) z składnią SQL Server w programie [create Database (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) , aby upewnić się, że wszystkie używane opcje są obsługiwane. Opcja Utwórz bazę danych dla Azure SQL Database ma także cele usługi i elastyczne skalowanie, które mają zastosowanie tylko do SQL Database.
- Instrukcje CREATE i ALTER TABLE zawierają opcje obiektu FileTable, których nie można używać na SQL Database, ponieważ nie jest obsługiwany element FILESTREAM.
- Instrukcje CREATE i ALTER login są obsługiwane, ale SQL Database nie oferuje wszystkich opcji. Aby zapewnić lepszą przenośność bazy danych, SQL Database zachęca do korzystania z użytkowników zawartej bazy danych zamiast nazw logowania, gdy tylko jest to możliwe. Aby uzyskać więcej informacji, zobacz [Tworzenie/Zmienianie nazwy logowania](/sql/t-sql/statements/alter-login-transact-sql) i [Zarządzanie logowaniami i użytkownikami](logins-create-manage.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Składnia języka Transact-SQL nie jest obsługiwana w Azure SQL Database

Oprócz instrukcji języka Transact-SQL odnoszących się do nieobsługiwanych funkcji opisanych w ramach [porównania funkcji Azure SQL Database](features-comparison.md), następujące instrukcje i grupy instrukcji nie są obsługiwane. W związku z tym, jeśli baza danych, która ma zostać zmigrowana, korzysta z jednej z następujących funkcji, należy odtworzyć swój język T-SQL w celu wyeliminowania tych funkcji i instrukcji języka T-SQL.

- Sortowanie obiektów systemu
- Powiązane połączenie: punkty końcowe. SQL Database nie obsługuje uwierzytelniania systemu Windows, ale obsługuje podobne uwierzytelnianie Azure Active Directory. Niektóre typy uwierzytelniania wymagają najnowszej wersji usługi SSMS. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługami SQL Database lub Azure Synapse Analytics przy użyciu uwierzytelniania Azure Active Directory](authentication-aad-overview.md).
- Zapytania obejmujące wiele baz danych, korzystające z nazw trój- i czteroczęściowych. (Zapytania tylko do odczytu obejmujące wiele baz danych są obsługiwane za pomocą [zapytania elastycznej bazy danych](elastic-query-overview.md)).
- Tworzenie łańcucha własności między wieloma bazami danych, ustawienie `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Zamiast tego użyj instrukcji „EXECUTE AS USER”.
- Szyfrowanie jest obsługiwane z wyjątkiem rozszerzonego zarządzania kluczami
- Zdarzenia: zdarzenia, powiadomienia o zdarzeniach, powiadomienia o zapytaniach
- Położenie pliku: Składnia związana z rozmieszczeniem, rozmiarem i plikami bazy danych, które są automatycznie zarządzane przez Microsoft Azure.
- Wysoka dostępność: Składnia związana z wysoką dostępnością, która jest zarządzana przy użyciu konta Microsoft Azure. Obejmuje to składnię kopii zapasowych, przywracania, funkcji Always On, dublowania bazy danych, wysyłania dziennika oraz trybów odzyskiwania.
- Czytnik dzienników: Składnia, która opiera się na czytniku dziennika, który nie jest dostępny w SQL Database: replikacja wypychana, przechwytywanie zmian danych. Baza danych SQL Database może być subskrybentem artykułu replikacji wypychanej.
- Funkcje: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Sprzęt: Składnia dotycząca ustawień serwera związanych z sprzętem: takich jak pamięć, wątki robocze, koligacja procesora, flagi śledzenia. Zamiast nich używaj warstw usług i rozmiarów obliczeniowych.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` , `OPENDATASOURCE` i nazwy czterech części
- .NET Framework: integracja środowiska CLR z SQL Server
- Wyszukiwanie semantyczne
- Poświadczenia serwera: zamiast tego użyj [poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) .
- Elementy na poziomie serwera: role serwera, `sys.login_token` . `GRANT`, `REVOKE` i `DENY` uprawnienia na poziomie serwera nie są dostępne, chociaż niektóre są zastępowane przez uprawnienia na poziomie bazy danych. Niektóre przydatne dynamiczne widoki zarządzania na poziomie serwera mają swoje odpowiedniki na poziomie bazy danych.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Opcje polecenia `sp_configure` i instrukcja `RECONFIGURE`. Niektóre opcje są dostępne po użyciu instrukcji [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Agent SQL Server: Składnia, która opiera się na agencie SQL Server lub bazie danych MSDB: alerty, operatory, centralne serwery zarządzania. Zamiast tego używaj skryptów, takich jak Azure PowerShell.
- Inspekcja SQL Server: zamiast tego użyj inspekcji SQL Database.
- Śledzenie programu SQL Server
- Flagi śledzenia: niektóre elementy flagi śledzenia zostały przeniesione do trybu zgodności.
- Debugowanie języka Transact-SQL
- Wyzwalacze: wyzwalacze zakresu serwera lub wyzwalacze logowania
- Instrukcja `USE`: aby zmienić kontekst bazy danych na inną bazę danych, należy wprowadzić nowe połączenie z nową bazą danych.

## <a name="full-transact-sql-reference"></a>Pełna dokumentacja języka Transact-SQL

Aby uzyskać więcej informacji dotyczących gramatyki języka Transact-SQL oraz przykłady jego użycia, zobacz artykuł [Transact-SQL Reference (Database Engine)](/sql/t-sql/language-reference) (Dokumentacja języka Transact-SQL (aparat bazy danych)) w dokumentacji SQL Server — książki online.

### <a name="about-the-applies-to-tags"></a>Tagi „Applies to” (Dotyczy)

Dokumentacja języka Transact-SQL zawiera artykuły powiązane z SQL Server wersjami 2008. Poniżej tytułu artykułu znajduje się pasek ikon zawierający cztery SQL Server platformy i wskazujące na zastosowanie. Na przykład grupy dostępności zostały wprowadzone w programie SQL Server 2012. Artykuł [Tworzenie grupy dostępności](/sql/t-sql/statements/create-availability-group-transact-sql) wskazuje, że instrukcja ma zastosowanie do **SQL Server (począwszy od 2012)**. Instrukcja nie ma zastosowania do SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Synapse Analytics lub Parallel Data Warehouse.

W niektórych przypadkach ogólna część artykułu może być używana w produkcie, ale istnieją niewielkie różnice między produktami. Różnice są wskazane w środkach w artykule stosownie do potrzeb. W niektórych przypadkach ogólna część artykułu może być używana w produkcie, ale istnieją niewielkie różnice między produktami. Różnice są wskazane w środkach w artykule stosownie do potrzeb. Na przykład artykuł Tworzenie WYZWALACZa jest dostępny w SQL Database. Jednak opcja **wszystkie serwery** dla wyzwalaczy na poziomie serwera wskazuje, że w SQL Database nie można używać wyzwalaczy na poziomie serwera. Zamiast tego Użyj wyzwalaczy na poziomie bazy danych.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z listą funkcji obsługiwanych i nieobsługiwanych przez SQL Database, zobacz [Azure SQL Database porównanie funkcji](features-comparison.md). Lista na tej stronie zawiera uzupełnienie artykułu wskazówki i funkcje oraz koncentruje się na instrukcjach języka Transact-SQL.