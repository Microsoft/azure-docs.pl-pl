---
title: Automatyzacja zadań z zadaniami agenta SQL w wystąpieniu zarządzanym Azure SQL
description: Opcje automatyzacji do uruchamiania skryptów Transact-SQL (T-SQL) w wystąpieniu zarządzanym Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 6ba1ba9d8c809a805fe8691baf96350b70ec6a51
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691896"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Automatyzowanie zadań zarządzania przy użyciu zadań programu SQL Agent w wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Za pomocą [agenta SQL Server](/sql/ssms/agent/sql-server-agent) w programie SQL Server i [wystąpieniu zarządzanym SQL](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)można tworzyć i planować zadania, które mogą być wykonywane okresowo względem jednej lub wielu baz danych w celu uruchamiania zapytań Transact-SQL (T-SQL) i wykonywania zadań konserwacyjnych. W tym artykule wprowadzono agenta SQL dla wystąpienia zarządzanego SQL.

> [!Note]
> Program SQL Agent nie jest dostępny w Azure SQL Database lub Azure Synapse Analytics. Zamiast tego zalecamy [automatyzację zadań przy użyciu elastycznych zadań](job-automation-overview.md).

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Ograniczenia zadania agenta SQL w wystąpieniu zarządzanym usługi Azure SQL

Warto zwrócić uwagę na różnice między programem SQL Agent dostępnym w SQL Server i w ramach wystąpienia zarządzanego SQL. Aby uzyskać więcej informacji o obsługiwanych funkcjach różnic między SQL Server i wystąpieniem zarządzanym SQL, zobacz [różnice w języku T-SQL wystąpienia zarządzanego usługi Azure SQL Server z SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent). 

Niektóre funkcje programu SQL Agent dostępne w SQL Server nie są obsługiwane w wystąpieniu zarządzanym SQL:

- Ustawienia agenta SQL są tylko do odczytu. 
    - Procedura składowana systemu `sp_set_agent_properties` nie jest obsługiwana w wystąpieniu zarządzanym SQL.
- Włączenie/wyłączenie agenta SQL nie jest obecnie obsługiwane w wystąpieniu zarządzanym SQL. Agent SQL zawsze działa.
- Powiadomienia są częściowo obsługiwane:
  - Pager nie jest obsługiwany.
  - Polecenie NetSend nie jest obsługiwane.
  - Alerty nie są obsługiwane.
- Serwery proxy nie są obsługiwane.
- Dziennik zdarzeń nie jest obsługiwany.
- Wyzwalacz harmonogramu zadań oparty na bezczynnym procesorze CPU nie jest obsługiwany.

## <a name="when-to-use-sql-agent-jobs"></a>Kiedy używać zadań programu SQL Agent 

Istnieje kilka scenariuszy, w których można korzystać z zadań programu SQL Agent:

- Automatyzowanie zadań zarządzania i planowanie ich uruchamiania każdego dnia tygodnia, po godzinach itd.
  - Wdrażanie zmian schematu, zarządzanie poświadczeniami i zbieranie danych o wydajności lub danych telemetrycznych dotyczących dzierżaw (klientów).
  - Aktualizowanie danych referencyjnych (informacje wspólne dla wszystkich baz danych) i ładowanie danych z usługi Azure Blob Storage.
  - Typowe zadania konserwacji, w tym polecenie DBCC CHECKDB, aby zapewnić integralność danych lub obsługę indeksu w celu poprawy wydajności zapytań. Konfigurowanie zadań w celu cyklicznego wykonywania w kolekcji baz danych, na przykład poza godzinami szczytu.
  - Zbieranie na bieżąco wyników zapytań z zestawu baz danych w centralnej tabeli. Zapytania dotyczące wydajności mogą być nieprzerwanie wykonywane i skonfigurowane do wyzwalania dodatkowych zadań do wykonania.
- Zbieranie danych na potrzeby raportowania
  - Agregowanie danych z kolekcji baz danych do pojedynczej tabeli docelowej.
  - Wykonywanie dłużej działających zapytań dotyczących przetwarzania danych w ramach dużego zestawu baz danych, na przykład w celu zbierania telemetrii klienta. Wyniki są zbierane w pojedynczej tabeli docelowej na potrzeby dalszej analizy.
- Operacje przenoszenia danych
  - Twórz zadania, które replikują zmiany wprowadzone w bazach danych do innych baz danych, lub Zbieraj aktualizacje wprowadzone w zdalnych bazach danych i stosują zmiany w bazie danych.
  - Tworzenie zadań polegających na ładowaniu zadań z lub do baz danych przy użyciu usług SQL Server Integration Services (SSIS).

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Zadania agenta SQL w wystąpieniu zarządzanym usługi Azure SQL

Zadania programu SQL Agent są wykonywane przez usługę agenta SQL, która jest nadal używana do automatyzacji zadań w SQL Server i wystąpieniu zarządzanym SQL. 

Zadania programu SQL Agent są określoną serią skryptów T-SQL w odniesieniu do bazy danych. Zadania pozwalają na zdefiniowanie zadania administracyjnego, które można uruchomić jeden lub więcej razy i monitorować pod kątem powodzenia lub niepowodzenia. 

Zadanie można uruchomić na jednym serwerze lokalnym lub na wielu serwerach zdalnych. Zadania programu SQL Agent to składnik aparatu wewnętrznej bazy danych, który jest wykonywany w ramach usługi wystąpienia zarządzanego SQL.

Istnieje kilka podstawowych pojęć dotyczących zadań agenta SQL:

- **Kroki zadania** ustawiają co najmniej jeden krok, który ma zostać wykonany w ramach zadania. Dla każdego kroku zadania można zdefiniować strategię ponawiania próby i akcję do wykonania, gdy krok zadania zakończy się powodzeniem lub niepowodzeniem.
- **Harmonogramy** definiują, kiedy należy wykonać zadanie.
- **Powiadomienia** umożliwiają zdefiniowanie reguł, które będą używane do powiadamiania operatorów za pośrednictwem poczty e-mail po zakończeniu zadania.

### <a name="sql-agent-job-steps"></a>Kroki zadania agenta SQL

Kroki zadania agenta SQL to sekwencje akcji, które mają być wykonywane przez agenta SQL. Każdy krok ma następny krok, który ma zostać wykonany, jeśli krok zakończy się pomyślnie lub nie powiedzie się. W razie niepowodzenia jest określana liczba ponownych prób.

Program SQL Agent umożliwia tworzenie różnych typów kroków zadań, takich jak czynności zadania Transact-SQL, które wykonują pojedyncze dane wsadowe języka Transact-SQL względem bazy danych, lub polecenia systemu operacyjnego/programu PowerShell, które mogą wykonywać niestandardowe skrypty systemu operacyjnego, [kroki dotyczące zadania SSIS](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md) , które umożliwiają ładowanie danych przy użyciu środowiska uruchomieniowego usług SSIS, lub kroki [replikacji](../managed-instance/replication-transactional-overview.md) , które mogą publikować zmiany z bazy danych w innych bazach

> [!Note]
> Aby uzyskać więcej informacji na temat korzystania z usługi Azure SSIS Integration Runtime z usługą SSISDB hostowaną przez wystąpienie zarządzane usługi Azure SQL, zobacz temat [Używanie wystąpienia zarządzanego Azure SQL z usługą SQL Server Integration Services (SSIS) w Azure Data Factory](/../azure/data-factory/how-to-use-sql-managed-instance-with-ir.md).

[Replikacja transakcyjna](../managed-instance/replication-transactional-overview.md) może replikować zmiany z tabel do innych baz danych w wystąpieniu zarządzanym usługi Azure SQL, Azure SQL Database lub SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji w wystąpieniu zarządzanym usługi Azure SQL](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md). 

Inne typy kroków zadań nie są obecnie obsługiwane w wystąpieniu zarządzanym SQL, w tym:

- Krok zadania replikacji scalającej nie jest obsługiwany.
- Czytnik danych kolejki nie jest obsługiwany.
- Usługi Analysis Services nie są obsługiwane.
 
### <a name="sql-agent-job-schedules"></a>Harmonogramy zadań programu SQL Agent

Harmonogram określa moment uruchomienia zadania. W ramach tego samego harmonogramu można uruchomić więcej niż jedno zadanie, a do tego samego zadania można zastosować więcej niż jeden harmonogram.

W harmonogramie można zdefiniować następujące warunki związane z czasem wykonywania zadania:

- Za każdym razem, gdy SQL Server Agent zostanie uruchomiony. Zadanie jest aktywowane po każdym przejściu w tryb failover.
- Jeden raz w określonym dniu i o określonej godzinie — ta opcja jest przydatna w przypadku opóźnionego wykonywania zadania.
- Zgodnie z harmonogramem cyklicznym.

> [!Note]
> Wystąpienie zarządzane SQL obecnie nie umożliwia uruchamiania zadania, gdy procesor jest bezczynny.

### <a name="sql-agent-job-notifications"></a>Powiadomienia o zadaniach agenta SQL

Zadania programu SQL Agent umożliwiają otrzymywanie powiadomień, gdy zadanie zakończy się pomyślnie lub nie powiedzie się. Powiadomienia można otrzymywać za pośrednictwem poczty e-mail.

Jeśli nie jest jeszcze włączona, najpierw należy skonfigurować [funkcję poczta bazy danych](/sql/relational-databases/database-mail/database-mail) w wystąpieniu zarządzanym Azure SQL:

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

Na przykład ćwiczenie Skonfiguruj konto e-mail, które będzie używane do wysyłania powiadomień e-mail. Przypisz konto do profilu poczty e-mail o nazwie `AzureManagedInstance_dbmail_profile` . Aby wysłać wiadomość e-mail przy użyciu zadań programu SQL Agent w wystąpieniu zarządzanym SQL, powinien istnieć profil, który musi zostać wywołany `AzureManagedInstance_dbmail_profile` . W przeciwnym razie wystąpienie zarządzane SQL nie będzie w stanie wysyłać wiadomości e-mail za pośrednictwem programu SQL Agent. Zobacz następujący przykład:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Przetestuj konfigurację Poczta bazy danych przy użyciu języka T-SQL za pomocą procedury składowanej systemu [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) :

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

Można powiadomić operatora, że coś się stało z zadaniami programu SQL Agent. Operator definiuje informacje kontaktowe osoby odpowiedzialnej za konserwację jednego lub większej liczby wystąpień w wystąpieniu zarządzanym SQL. Czasami obowiązki operatorów są przypisane do jednej osoby.

W systemach z wieloma wystąpieniami w wystąpieniu zarządzanym SQL lub SQL Server wiele osób może udostępniać obowiązki operatorów. Operator nie zawiera informacji o zabezpieczeniach i nie definiuje podmiotu zabezpieczeń. W idealnym przypadku operator nie jest osobą, której obowiązki mogą ulec zmianie, ale grupy dystrybucyjnej poczty e-mail.   

Operatory można [tworzyć](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql) przy użyciu programu SQL Server Management Studio (SSMS) lub skryptu Transact-SQL pokazanego w poniższym przykładzie:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

Potwierdź sukces lub niepowodzenie wiadomości e-mail za pośrednictwem [dziennika poczta bazy danych](/sql/relational-databases/database-mail/database-mail-log-and-audits) w programie SSMS.

Następnie można [zmodyfikować dowolne zadanie agenta SQL](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql) i przypisać operatory, które zostaną powiadomione za pośrednictwem poczty e-mail, jeśli zadanie zakończy się niepowodzeniem lub zakończy się pomyślnie przy użyciu programu SSMS lub następującego skryptu Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>Historia zadania agenta SQL

Wystąpienie zarządzane Azure SQL obecnie nie pozwala na zmianę żadnych właściwości agenta SQL, ponieważ są one przechowywane w podstawowych wartościach rejestru. Oznacza to, że opcje dostosowywania zasad przechowywania agentów dla rekordów historii zadań są stałe domyślnie 1000 łączne rekordy i maksymalna 100 rekordów historii dla każdego zadania.

### <a name="sql-agent-fixed-database-role-membership"></a>Członkostwo w stałej roli bazy danych programu SQL Agent

Jeśli użytkownicy połączeni z logowaniem niebędącym administratorem systemu są dodawani do dowolnych z trzech ról stałych baz danych programu SQL Agent w systemowej bazie danych msdb, istnieje problem polegający na tym, że jawne uprawnienia wykonywania muszą zostać przyznane głównym procedurom składowanym dla tych logowań do działania. Jeśli wystąpi ten problem, zostanie wyświetlony komunikat o błędzie "uprawnienie EXECUTE zostało odrzucone w obiekcie <object_name> (Microsoft SQL Server, błąd: 229)". 

Po dodaniu użytkowników do stałej roli bazy danych (SQLAgentUserRole, SQLAgentReaderRole lub SQLAgentOperatorRole) agenta programu SQL Server dla każdego z identyfikatorów logowania użytkownika, które zostały dodane do tych ról, należy wykonać poniższy skrypt T-SQL w celu jawnego udzielenia uprawnień do procedury składowanej system. W tym przykładzie przyjęto założenie, że nazwa użytkownika i nazwa logowania są takie same. 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>Więcej tutaj

- [Co to jest wystąpienie zarządzane Azure SQL?](../managed-instance/sql-managed-instance-paas-overview.md)
- [Co nowego w Azure SQL Database & wystąpieniu zarządzanym SQL?](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Różnice T-SQL wystąpienia zarządzanego usługi Azure SQL na podstawie SQL Server](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [Porównanie funkcji: Azure SQL Database i wystąpienie zarządzane Azure SQL](../../azure-sql/database/features-comparison.md)