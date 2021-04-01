---
title: Obsługa długotrwałych procedur składowanych w łączniku SQL
description: Jak obsłużyć procedury składowane, które przekroczą limit czasu podczas korzystania z łącznika SQL w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93103114"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Obsługa limitów czasu procedury składowanej w łączniku SQL dla Azure Logic Apps

Gdy aplikacja logiki współpracuje z zestawami wyników tak duże, że [Łącznik SQL](../connectors/connectors-create-api-sqlazure.md) nie zwraca wszystkich wyników w tym samym czasie lub jeśli chcesz mieć większą kontrolę nad rozmiarem i strukturą zestawów wyników, możesz utworzyć [procedurę przechowywaną](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , która organizuje wyniki w żądany sposób. Łącznik SQL udostępnia wiele funkcji zaplecza, do których można uzyskać dostęp za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , dzięki czemu można łatwiej zautomatyzować zadania biznesowe, które współpracują z tabelami usługi SQL Database.

Na przykład podczas pobierania lub wstawiania wielu wierszy aplikacja logiki może wykonać iterację w tych wierszach przy użyciu pętli [ **until**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) w ramach tych [limitów](../logic-apps/logic-apps-limits-and-config.md). Jeśli jednak aplikacja logiki musi współpracować z tysiącami lub milionami wierszy, chce zminimalizować koszty związane z wywołaniami bazy danych. Aby uzyskać więcej informacji, zobacz temat [Obsługa danych zbiorczych przy użyciu łącznika SQL](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Limit czasu wykonywania procedury składowanej

Program SQL Connector ma limit czasu procedury składowanej, który jest [krótszy niż 2 minuty](/connectors/sql/#known-issues-and-limitations). Niektóre procedury składowane mogą trwać dłużej niż ten limit, co spowoduje wystąpienie `504 Timeout` błędu. Czasami te długotrwałe procesy są kodowane jako procedury składowane jawnie do tego celu. Ze względu na limit czasu wywoływanie tych procedur z Azure Logic Apps może spowodować problemy. Chociaż łącznik SQL nie obsługuje natywnie trybu asynchronicznego, można obejść ten problem i symulować ten tryb przy użyciu wyzwalacza uzupełniania SQL, natywnego zapytania przekazującego SQL, tabeli stanu i zadań po stronie serwera. W przypadku tego zadania można użyć [agenta elastycznego zadania platformy Azure](../azure-sql/database/elastic-jobs-overview.md) dla [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). W przypadku [SQL Server lokalnego](/sql/sql-server/sql-server-technical-documentation) i [wystąpienia zarządzanego usługi Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)można użyć [agenta SQL Server](/sql/ssms/agent/sql-server-agent).

Załóżmy na przykład, że masz następującą długotrwałą procedurę składowaną, która trwa dłużej niż limit limitu czasu, aby zakończyć działanie. Jeśli uruchomisz tę procedurę składowaną z poziomu aplikacji logiki przy użyciu łącznika SQL, w wyniku tego zostanie wyświetlony `HTTP 504 Gateway Timeout` komunikat o błędzie.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Zamiast bezpośrednio wywołać procedurę składowaną, można asynchronicznie uruchomić procedurę w tle przy użyciu *agenta zadań*. Dane wejściowe i wyjściowe można przechowywać w tabeli stanu, z którą można następnie korzystać za pomocą aplikacji logiki. Jeśli dane wejściowe i wyjściowe nie są potrzebne, lub jeśli masz już zapisane wyniki w tabeli w procedurze składowanej, możesz uprościć to podejście.

> [!IMPORTANT]
> Upewnij się, że procedura składowana i wszystkie zadania są *idempotentne*, co oznacza, że mogą być uruchamiane wiele razy bez wpływu na wyniki. Jeśli asynchroniczne przetwarzanie kończy się niepowodzeniem lub przekroczy limit czasu, Agent zadania może ponowić próbę wykonania kroku, a tym samym procedurę składowaną, wiele razy. Aby uniknąć duplikowania danych wyjściowych, przed utworzeniem jakichkolwiek obiektów zapoznaj się z tymi [najlepszymi rozwiązaniami i](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)metodami.

W następnej sekcji opisano, jak można użyć agenta elastycznego zadania platformy Azure dla Azure SQL Database. W przypadku SQL Server i wystąpienia zarządzanego usługi Azure SQL można użyć agenta SQL Server. Niektóre szczegóły dotyczące zarządzania różnią się, ale podstawowe kroki pozostają takie same jak w przypadku konfigurowania agenta zadań dla Azure SQL Database.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Agent zadań dla Azure SQL Database

Aby utworzyć zadanie, które może uruchomić procedurę przechowywaną dla [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), użyj [agenta elastycznego zadania platformy Azure](../azure-sql/database/elastic-jobs-overview.md). Utwórz agenta zadań w Azure Portal. Takie podejście spowoduje dodanie kilku procedur składowanych do bazy danych używanej przez agenta, znanej również jako *baza danych agentów*. Następnie można utworzyć zadanie, które uruchamia procedurę przechowywaną w docelowej bazie danych i przechwytuje dane wyjściowe po zakończeniu.

Aby można było utworzyć zadanie, należy skonfigurować uprawnienia, grupy i cele zgodnie z opisem w [pełnej dokumentacji dla agenta elastycznego zadania platformy Azure](../azure-sql/database/elastic-jobs-overview.md). Należy również utworzyć tabelę pomocniczą w docelowej bazie danych, zgodnie z opisem w poniższych sekcjach.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Utwórz tabelę stanu na potrzeby rejestrowania parametrów i przechowywania danych wejściowych

Zadania agenta SQL nie akceptują parametrów wejściowych. Zamiast tego w docelowej bazie danych Utwórz tabelę stanu, w której można zarejestrować parametry i zapisać dane wejściowe do użycia w celu wywołania procedur składowanych. Wszystkie kroki zadania agenta są wykonywane względem docelowej bazy danych, ale procedury składowane zadania są uruchamiane względem bazy danych agenta. 

Aby utworzyć tabelę stanu, użyj tego schematu:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Oto jak wygląda wynikowa tabela w [SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms):

![Zrzut ekranu pokazujący utworzoną tabelę stanu, która przechowuje dane wejściowe dla procedury składowanej.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Aby zapewnić dobrą wydajność i upewnić się, że zadanie agenta może znaleźć skojarzony rekord, w tabeli zostanie użyty identyfikator wykonania zadania ( `jobid` ) jako klucz podstawowy. Jeśli chcesz, możesz również dodać pojedyncze kolumny dla parametrów wejściowych. Wcześniej opisany schemat może bardziej ogólnie obsłużyć wiele parametrów, ale jest ograniczony do rozmiaru obliczonego przez `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Tworzenie zadania najwyższego poziomu w celu uruchomienia procedury składowanej

Aby wykonać długotrwałą procedurę przechowywaną, Utwórz tego agenta zadań najwyższego poziomu w bazie danych agenta:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Teraz Dodaj kroki do zadania, które Sparametryzuj, Uruchom i wykonaj procedurę składowaną. Domyślnie etap zadania przekroczy czas 12 godzin. Jeśli procedura składowana wymaga więcej czasu lub jeśli chcesz, aby procedura przekroczyła limit czasu, możesz zmienić `step_timeout_seconds` parametr na inną wartość określoną w sekundach. Domyślnie krok ma 10 wbudowanych ponownych prób z upływem limitu czasu wycofywania między kolejnymi ponowień, których można użyć do korzystania z tej funkcji.

Poniżej przedstawiono kroki, które należy dodać:

1. Poczekaj, aż parametry zostaną wyświetlone w `LongRunningState` tabeli.

   Ten pierwszy krok czeka, aż parametry zostaną dodane w `LongRunningState` tabeli, co się wkrótce po rozpoczęciu zadania. Jeśli identyfikator wykonywania zadania ( `jobid` ) nie zostanie dodany do `LongRunningState` tabeli, krok tylko zakończy się niepowodzeniem, a domyślny limit czasu ponawiania lub wycofywaniania oczekuje:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Wykonaj zapytanie dotyczące parametrów z tabeli State i przekaż je do procedury składowanej. Ten krok uruchamia również procedurę w tle. 

   Jeśli procedura składowana nie wymaga parametrów, wystarczy bezpośrednio wywołać procedurę składowaną. W przeciwnym razie, aby przekazać `@timespan` parametr, użyj `@callparams` , który można także rozłożyć, aby przekazać dodatkowe parametry.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Ukończ zadanie i Zapisz wyniki.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Uruchamianie zadania i przekazywanie parametrów

Aby uruchomić zadanie, użyj zapytania natywnego przekazywania z [akcją **wykonaj zapytanie SQL**](/connectors/sql/#execute-a-sql-query-(v2)) i natychmiast popchnij parametry zadania do tabeli Stanów. Aby zapewnić dane wejściowe dla `jobid` atrybutu w tabeli docelowej, Logic Apps dodaje pętlę **for each** , która wykonuje iterację w tabeli wyjściowej poprzedniej akcji. Dla każdego identyfikatora wykonywania zadania uruchom akcję **Wstaw wiersz** , która używa dynamicznych danych wyjściowych, `ResultSets JobExecutionId` Aby dodać parametry zadania do rozpakowania i przekazania do docelowej procedury składowanej.

![Zrzut ekranu pokazujący akcje umożliwiające uruchomienie zadania i przekazywanie parametrów do procedury składowanej.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Po zakończeniu zadania zadanie aktualizuje `LongRunningState` tabelę, aby można było łatwo wyzwolić wynik przy użyciu [wyzwalacza **kiedy element zostanie zmodyfikowany**](/connectors/sql/#when-an-item-is-modified-(v2)). Jeśli dane wyjściowe nie są potrzebne lub jeśli masz już wyzwalacz monitorujący tabelę wyjściową, możesz pominąć tę część.

![Zrzut ekranu, który pokazuje wyzwalacz SQL dla momentu modyfikacji elementu.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Agent zadań dla SQL Server lub wystąpienia zarządzanego Azure SQL

W tym samym scenariuszu można użyć [agenta SQL Server](/sql/ssms/agent/sql-server-agent) dla [SQL Server lokalnego](/sql/sql-server/sql-server-technical-documentation) i [wystąpienia zarządzanego Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Chociaż niektóre szczegóły dotyczące zarządzania różnią się, podstawowe kroki pozostają takie same, jak w przypadku konfigurowania agenta zadań dla Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

[Nawiązywanie połączenia z usługą SQL Server, Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL](../connectors/connectors-create-api-sqlazure.md)

