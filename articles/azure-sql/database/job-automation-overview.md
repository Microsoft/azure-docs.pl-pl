---
title: Automatyzacja zadań
description: Używanie automatyzacji zadań do uruchamiania skryptów Transact-SQL (T-SQL) w zestawie co najmniej jednej bazy danych
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 7ecd7e847a91847db8f57c640a374dc329fce7ea
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782947"
---
# <a name="automate-management-tasks-using-database-jobs"></a>Automatyzowanie zadań zarządzania za pomocą zadań bazy danych
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Można tworzyć i planować zadania, które mogą być okresowo wykonywane dla jednej lub wielu baz danych w celu uruchamiania zapytań Transact-SQL (T-SQL) i wykonywania zadań konserwacyjnych.

Można określić docelową bazę danych lub grupy baz danych, w których zadania będą wykonywane, oraz zdefiniować harmonogramy uruchamiania zadania.
Zadanie obsługuje rejestrowanie w docelowej bazie danych. Możesz również definiować, obsługiwać i utrwalać skrypty języka Transact-SQL do wykonania w ramach grupy baz danych.

Każde zadanie rejestruje stan wykonania, a także automatycznie ponawia próbę wykonania operacji w przypadku awarii.

## <a name="when-to-use-automated-jobs"></a>Kiedy używać zadań zautomatyzowanych

Istnieje kilka scenariuszy, w których można używać funkcji automatyzacji zadań:

- Automatyzowanie zadań zarządzania i planowanie ich uruchamiania każdego dnia tygodnia, po godzinach itd.
  - Wdrażanie zmian schematu, zarządzanie poświadczeniami i zbieranie danych o wydajności lub danych telemetrycznych dotyczących dzierżaw (klientów).
  - Aktualizowanie danych referencyjnych (informacje wspólne dla wszystkich baz danych) i ładowanie danych z usługi Azure Blob Storage.
  - Odbudowywanie indeksów w celu zwiększenia wydajności zapytań. Konfigurowanie zadań w celu cyklicznego wykonywania w kolekcji baz danych, na przykład poza godzinami szczytu.
  - Zbieranie na bieżąco wyników zapytań z zestawu baz danych w centralnej tabeli. Zapytania dotyczące wydajności mogą być nieprzerwanie wykonywane i skonfigurowane do wyzwalania dodatkowych zadań do wykonania.
- Zbieranie danych na potrzeby raportowania
  - Agregowanie danych z kolekcji baz danych do pojedynczej tabeli docelowej.
  - Wykonywanie dłużej działających zapytań dotyczących przetwarzania danych w ramach dużego zestawu baz danych, na przykład w celu zbierania telemetrii klienta. Wyniki są zbierane w pojedynczej tabeli docelowej na potrzeby dalszej analizy.
- Operacje przenoszenia danych
  - Twórz zadania, które replikują zmiany wprowadzone w bazach danych do innych baz danych, lub Zbieraj aktualizacje wprowadzone w zdalnych bazach danych i stosują zmiany w bazie danych.
  - Tworzenie zadań polegających na ładowaniu zadań z lub do baz danych przy użyciu usług SQL Server Integration Services (SSIS).

## <a name="overview"></a>Omówienie

Dostępne są następujące technologie planowania zadań:

- **Zadania programu SQL Agent** są klasyczne i sprawdzonej — SQL Server testowany składnik planowania zadań, który jest dostępny w wystąpieniu zarządzanym usługi Azure SQL. Zadania programu SQL Agent nie są dostępne w Azure SQL Database.
- **Zadania Elastic Database (wersja zapoznawcza)** to usługi planowania zadań, które wykonują zadania niestandardowe dla jednej lub wielu baz danych w Azure SQL Database.

Warto zwrócić uwagę na kilka różnic między programem SQL Agent (dostępnym lokalnie i w ramach wystąpienia zarządzanego SQL) oraz agenta zadań elastycznych bazy danych (dostępne dla pojedynczych baz danych w Azure SQL Database i bazach danych w usłudze Azure Synapse Analytics).

| |Zadania elastyczne |Agent SQL |
|---------|---------|---------|
|**Zakres** | Dowolna liczba baz danych w Azure SQL Database i/lub magazynów danych w tej samej chmurze platformy Azure jako Agent zadania. Elementy docelowe mogą znajdować się na różnych serwerach, subskrypcjach i/lub regionach. <br><br>Grupy docelowe mogą składać się z pojedynczych baz danych lub hurtowni danych albo wszystkich baz danych na serwerze, w puli lub w mapie fragmentów (wyliczanych dynamicznie w czasie wykonywania zadania). | Każda pojedyncza baza danych w tym samym wystąpieniu co program SQL Agent. |
|**Obsługiwane interfejsy API i narzędzia** | Witryna Azure Portal, program PowerShell, język T-SQL, usługa Azure Resource Manager | Język T-SQL, program SQL Server Management Studio (SSMS) |

## <a name="sql-agent-jobs"></a>Zadania agenta SQL

Zadania programu SQL Agent są określoną serią skryptów T-SQL w odniesieniu do bazy danych. Zadania pozwalają na zdefiniowanie zadania administracyjnego, które można uruchomić jeden lub więcej razy i monitorować pod kątem powodzenia lub niepowodzenia.
Zadanie można uruchomić na jednym serwerze lokalnym lub na wielu serwerach zdalnych. Zadania programu SQL Agent to składnik aparatu wewnętrznej bazy danych, który jest wykonywany w ramach usługi wystąpienia zarządzanego.
Istnieje kilka podstawowych pojęć dotyczących zadań agenta SQL:

- **Kroki zadania** ustawiają co najmniej jeden krok, który ma zostać wykonany w ramach zadania. Dla każdego kroku zadania można zdefiniować strategię ponawiania próby i akcję do wykonania, gdy krok zadania zakończy się powodzeniem lub niepowodzeniem.
- **Harmonogramy** definiują, kiedy należy wykonać zadanie.
- **Powiadomienia** umożliwiają zdefiniowanie reguł, które będą używane do powiadamiania operatorów za pośrednictwem poczty e-mail po zakończeniu zadania.

### <a name="job-steps"></a>Kroki zdania

Kroki zadania agenta SQL to sekwencje akcji, które mają być wykonywane przez agenta SQL. Każdy krok ma następny krok, który ma zostać wykonany, jeśli krok zakończy się pomyślnie lub nie powiedzie się. W razie niepowodzenia jest określana liczba ponownych prób.

Program SQL Agent umożliwia tworzenie różnych typów kroków zadań, takich jak czynności zadania Transact-SQL, które wykonują pojedyncze dane wsadowe języka Transact-SQL względem bazy danych, lub polecenia systemu operacyjnego/programu PowerShell, które mogą wykonywać niestandardowe skrypty systemu operacyjnego, kroki dotyczące zadania SSIS, które umożliwiają ładowanie danych przy użyciu środowiska uruchomieniowego usług SSIS, lub kroki [replikacji](../managed-instance/replication-transactional-overview.md) , które mogą publikować zmiany z bazy danych w innych bazach

[Replikacja transakcyjna](../managed-instance/replication-transactional-overview.md) to funkcja aparatu bazy danych, która umożliwia publikowanie zmian wprowadzonych w jednej lub wielu tabelach w jednej bazie danych oraz publikowanie/dystrybuowanie ich do zestawu baz danych subskrybenta. Publikowanie zmian jest implementowane za pomocą następujących typów kroków zadania agenta SQL:

- Czytnik dziennika transakcji.
- Migawka.
- Dystrybutor.

Inne typy kroków zadania nie są obecnie obsługiwane, w tym:

- Krok zadania replikacji scalającej nie jest obsługiwany.
- Czytnik danych kolejki nie jest obsługiwany.
- Usługi Analysis Services nie są obsługiwane.

### <a name="job-schedules"></a>Harmonogramy zadań

Harmonogram określa moment uruchomienia zadania. W ramach tego samego harmonogramu można uruchomić więcej niż jedno zadanie, a do tego samego zadania można zastosować więcej niż jeden harmonogram.
W harmonogramie można zdefiniować następujące warunki związane z czasem wykonywania zadania:

- Po każdym ponownym uruchomieniu wystąpienia (lub po uruchomieniu agenta programu SQL Server). Zadanie jest aktywowane po każdym przejściu w tryb failover.
- Jeden raz w określonym dniu i o określonej godzinie — ta opcja jest przydatna w przypadku opóźnionego wykonywania zadania.
- Zgodnie z harmonogramem cyklicznym.

> [!Note]
> Wystąpienie zarządzane SQL obecnie nie pozwala na uruchamianie zadania, gdy wystąpienie jest w stanie bezczynności.

### <a name="job-notifications"></a>Powiadomienia dotyczące zadań

Zadania programu SQL Agent umożliwiają otrzymywanie powiadomień, gdy zadanie zakończy się pomyślnie lub nie powiedzie się. Powiadomienia można otrzymywać za pośrednictwem poczty e-mail.

Najpierw musisz skonfigurować konto e-mail, które będzie służyć do wysyłania powiadomień e-mail, i przypisać konto do profilu poczty e-mail o nazwie `AzureManagedInstance_dbmail_profile`, jak pokazano w następującym przykładzie:

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)'

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.' ;

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

Trzeba będzie również włączyć funkcję Poczta bazy danych w wystąpieniu zarządzanym:

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

Można powiadomić operatora, że coś się stało z zadaniami programu SQL Agent. Operator definiuje informacje kontaktowe osoby odpowiedzialnej za konserwację jednego lub większej liczby wystąpień w wystąpieniu zarządzanym SQL. Czasami obowiązki operatorów są przypisane do jednej osoby.
W systemach z wieloma wystąpieniami w wystąpieniu zarządzanym SQL lub SQL Server wiele osób może udostępniać obowiązki operatorów. Operator nie zawiera informacji o zabezpieczeniach i nie definiuje podmiotu zabezpieczeń.

Możesz tworzyć operatory przy użyciu programu SSMS lub skryptu Transact-SQL, który pokazano w następującym przykładzie:

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'Mihajlo Pupun',
    @enabled=1,
    @email_address=N'mihajlo.pupin@contoso.com'
```

Można modyfikować dowolne zadania i przypisać operatory, które będą powiadamiane za pośrednictwem poczty e-mail, jeśli zadanie zakończy się niepowodzeniem lub zakończy się pomyślnie przy użyciu programu SSMS lub następującego skryptu Transact-SQL:

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'Mihajlo Pupun'
```

### <a name="sql-agent-job-limitations"></a>Ograniczenia zadania agenta SQL

Niektóre funkcje agenta SQL dostępne w programie SQL Server nie są obsługiwane w wystąpieniu zarządzanym:

- Ustawienia agenta SQL są tylko do odczytu. Procedura `sp_set_agent_properties` nie jest obsługiwana w wystąpieniu zarządzanym.
- Włączenie/wyłączenie agenta SQL nie jest obecnie obsługiwane w wystąpieniu zarządzanym. Agent SQL zawsze działa.
- Powiadomienia są obsługiwane częściowo.
  - Pager nie jest obsługiwany.
  - Polecenie NetSend nie jest obsługiwane.
  - Alerty nie są obsługiwane.
- Serwery proxy nie są obsługiwane.
- Dziennik zdarzeń nie jest obsługiwany.

Aby uzyskać informacje na temat agenta programu SQL Server, zobacz [SQL Server Agent (Agent programu SQL Server)](/sql/ssms/agent/sql-server-agent).

## <a name="elastic-database-jobs-preview"></a>Zadania Elastic Database (wersja zapoznawcza)

**Zadania Elastic Database** umożliwiają równoległe uruchamianie wielu skryptów T-SQL względem wielu baz danych na podstawie harmonogramu lub na żądanie.

**Uruchamianie zadań w ramach dowolnej kombinacji baz danych** : co najmniej jedna pojedyncza baza danych, wszystkie bazy danych na serwerze, wszystkie bazy danych w elastycznej puli lub mapa fragmentów ze zwiększoną elastycznością umożliwiającą dołączanie lub wykluczanie dowolnej określonej bazy danych. **Zadania można wykonywać na wielu serwerach, w wielu pulach, a nawet w obrębie baz danych w ramach różnych subskrypcji.** Serwery i pule są dynamicznie wyliczane w czasie wykonywania, więc zadania są uruchamiane względem wszystkich baz danych, które istnieją w docelowej grupie w czasie wykonywania.

Na poniższej ilustracji przedstawiono agenta zadań wykonującego zadania w ramach różnych typów grup docelowych:

![Model koncepcyjny agenta zadań elastycznych](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Składniki zadań elastycznych

|Składnik | Opis (dodatkowe szczegóły znajdują się pod tabelą) |
|---------|---------|
|[**Agent zadań elastycznych**](#elastic-job-agent) | Zasób platformy Azure tworzony w celu uruchamiania zadań i zarządzania nimi. |
|[**Baza danych zadań**](#job-database) | Baza danych w Azure SQL Database, której Agent zadań używa do przechowywania danych związanych z zadaniami, definicji zadań itp. |
|[**Grupa docelowa**](#target-group) | Zestaw serwerów, pul, baz danych i map fragmentów, względem których ma być uruchamiane zadanie. |
|[**Zadanie**](#job) | Zadanie to jednostka pracy, która składa się z co najmniej jednego [kroku zadania](#job-step). Kroki zadania określają skrypty T-SQL do uruchomienia, a także inne szczegóły wymagane do wykonania skryptu. |

#### <a name="elastic-job-agent"></a>Agent zadań elastycznych

Agent zadań elastycznych jest zasobem platformy Azure służącym do tworzenia i uruchamiania zadań oraz do zarządzania nimi. Agent zadań elastycznych jest zasobem platformy Azure tworzonym w witrynie Azure Portal (obsługiwany jest również program [PowerShell](elastic-jobs-powershell-create.md) i interfejs REST).

Utworzenie **agenta elastycznego zadania** wymaga istniejącej bazy danych w Azure SQL Database. Agent konfiguruje tę istniejącą bazę danych jako [*bazę danych zadań*](#job-database).

Agent zadań elastycznych jest bezpłatny. Ta baza danych zadań jest rozliczana według stawki co każda baza danych w Azure SQL Database.

#### <a name="job-database"></a>Baza danych zadań

*Baza danych zadań* służy do definiowania zadań oraz śledzenia stanu i historii wykonywania zadań. *Baza danych zadań* służy również do przechowywania metadanych agentów, dzienników, wyników, definicji zadań, a także zawiera wiele przydatnych procedur składowanych i innych obiektów baz danych służących do tworzenia i uruchamiania zadań oraz zarządzania nimi przy użyciu języka T-SQL.

W przypadku bieżącej wersji zapoznawczej istniejąca baza danych w Azure SQL Database (S0 lub nowsza) jest wymagana do utworzenia agenta zadań elastycznych.

*Baza danych zadań* nie musi być nowością, ale powinna być celem czystego, pustego, S0 lub wyższej usługi. Zalecany cel usługi *bazy danych zadań* ma wartość S1 lub wyższą, ale optymalny wybór zależy od potrzeb związanych z wydajnością zadań: liczby kroków zadań, liczby elementów docelowych zadania i częstotliwości uruchamiania zadań. Na przykład baza danych S0 może być wystarczająca dla agenta zadań, który uruchamia kilka zadań o godzinie docelowej mniejszej niż dziesięć baz danych, ale uruchamianie zadania co minutę może być niedostatecznie szybkie w przypadku bazy danych S0, a wyższa warstwa usług może być lepsza.

Jeśli operacje dotyczące bazy danych zadań są wolniejsze niż oczekiwane, [Monitoruj](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) wydajność bazy danych i wykorzystanie zasobów w bazie danych zadań w okresach spowolnienia przy użyciu Azure Portal lub [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV. Jeśli użycie zasobu, takiego jak procesor CPU, we/wy danych lub zapis w dzienniku, zbliża się do 100% i jest skorelowane z okresami spowolnienia, należy rozważyć przyrostowe skalowanie bazy danych do wyższych celów usługi (w [modelu DTU](service-tiers-dtu.md) lub w [modelu rdzeń wirtualny](service-tiers-vcore.md)), dopóki wydajność bazy danych zadań zostanie dostatecznie zwiększona.

##### <a name="job-database-permissions"></a>Uprawnienia dotyczące bazy danych zadań

Podczas tworzenia agenta zadań ma miejsce tworzenie schematu, tabel i roli o nazwie *jobs_reader* w *bazie danych zadań* . Ta rola jest tworzona z następującymi uprawnieniami i została zaprojektowana w celu umożliwienia administratorom bardziej precyzyjnej kontroli dostępu na potrzeby monitorowania zadań:

|Nazwa roli |Uprawnienia do schematu „jobs” |Uprawnienia do schematu „jobs_internal” |
|---------|---------|---------|
|**jobs_reader** | SELECT | Brak |

> [!IMPORTANT]
> Przed udzieleniem dostępu do *bazy danych zadań* na poziomie administratora bazy danych rozważ konsekwencje takiego działania w zakresie zabezpieczeń. Złośliwy użytkownik z uprawnieniami do tworzenia lub edytowania zadań może utworzyć lub edytować zadanie korzystające z przechowywanych poświadczeń do nawiązywania połączenia z kontrolowaną przez niego bazą danych, co może mu umożliwić określenie hasła poświadczeń.

#### <a name="target-group"></a>Grupa docelowa

*Grupa docelowa* definiuje zestaw baz danych, względem którego będzie wykonywany krok zadania. Grupa docelowa może zawierać dowolną liczbę i kombinację następujących elementów:

- **Logiczny serwer SQL** — w przypadku określenia serwera wszystkie bazy danych istniejące na serwerze w momencie wykonywania zadania są częścią grupy. Konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania.
- **Elastyczna pula** — jeśli elastyczna pula jest określona, wszystkie bazy danych istniejące w niej w czasie wykonywania zadania są częścią grupy. Podobnie jak w przypadku serwera, konieczne jest podanie poświadczeń bazy danych master, aby możliwe było wyliczenie i zaktualizowanie grupy przed wykonaniem zadania.
- **Pojedyncza baza danych** — określ co najmniej jedną pojedynczą bazę danych, która będzie należeć do grupy.
- **Mapa fragmentów** — bazy danych mapy fragmentów.

> [!TIP]
> W momencie wykonywania zadania za pomocą *dynamicznego wyliczania* ma miejsce ponowne obliczenie zestawu baz danych w grupach docelowych, które zawierają serwery lub pule. Pozwala to zapewnić, że **zadania są uruchamiane we wszystkich bazach danych, które istnieją na serwerze lub w puli w czasie wykonywania zadania** . Ponowne obliczanie listy baz danych w czasie wykonywania jest szczególnie przydatne w scenariuszach, w których często zmienia się członkostwo w puli lub na serwerze.

Pule i pojedyncze bazy danych można określić jako uwzględnione lub wykluczone z grupy. Umożliwia to tworzenie grupy docelowej z dowolną kombinacją baz danych. Na przykład możesz dodać serwer do grupy docelowej, ale wykluczyć określone bazy danych znajdujące się w elastycznej puli (lub wykluczyć całą pulę).

Grupa docelowa może zawierać bazy danych znajdujące się w wielu subskrypcjach i regionach. Należy pamiętać, że operacje wykonywane między regionami mają większe opóźnienia niż te wykonywane w tym samym regionie.

W poniższych przykładach pokazano, jak różne definicje grup docelowych są dynamicznie wyliczane w momencie wykonania zadania w celu określenia baz danych do uruchomienia w ramach zadania:

![Przykłady grup docelowych](./media/job-automation-overview/targetgroup-examples1.png)

**Przykład 1** przedstawia grupę docelową, która zawiera listę poszczególnych baz danych. Gdy krok zadania jest wykonywany przy użyciu tej grupy docelowej, akcja kroku zadania zostanie wykonana w każdej z tych baz danych.<br>
**Przykład 2** pokazuje grupę docelową, która zawiera serwer jako element docelowy. Gdy krok zadania jest wykonywany przy użyciu tej grupy docelowej, serwer jest dynamicznie wyliczany w celu określenia listy baz danych, które obecnie znajdują się na serwerze. Akcja kroku zadania zostanie wykonana w każdej z tych baz danych.<br>
**Przykład 3** zawiera grupę docelową podobną do *przykładu 2* z wykluczeniem pojedynczej bazy danych. Akcja kroku zadania *nie* zostanie wykonana w wykluczonej bazie danych.<br>
**Przykład 4** przedstawia grupę docelową, która zawiera elastyczną pulę jako obiekt docelowy. Podobnie jak w *przykładzie 2* , pula będzie dynamicznie wyliczana w momencie uruchomienia zadania w celu określenia listy baz danych w puli.
<br><br>

![Dodatkowe przykłady grupy docelowej](./media/job-automation-overview/targetgroup-examples2.png)

**Przykład 5** i **przykład 6** zawierają zaawansowane scenariusze, w których serwery, pule elastyczne i bazy danych można łączyć za pomocą reguł dołączania i wykluczania.<br>
**Przykład 7** pokazuje, że w trakcie uruchamiania zadania można również wyliczać fragmenty na mapie fragmentów.

> [!NOTE]
> Sama baza danych zadań może być obiektem docelowym zadania. W tym scenariuszu baza danych zadań jest traktowana jak każda inna docelowa baza danych. Użytkownik zadania musi być utworzony i ma przyznane odpowiednie uprawnienia w bazie danych zadań, a poświadczenia zakresu bazy danych dla użytkownika zadania muszą także istnieć w bazie danych zadań, podobnie jak w przypadku każdej innej docelowej bazy danych.
>

#### <a name="job"></a>Zadanie

*Zadanie* to jednostka pracy, która jest wykonywana na podstawie harmonogramu lub jako zadanie jednorazowe. Zadanie składa się z co najmniej jednego *kroku zadania* .

##### <a name="job-step"></a>Krok zadania

Każdy krok zadania określa skrypt T-SQL do wykonania, co najmniej jedną grupę docelową, względem której ten skrypt będzie uruchamiany, oraz poświadczenia wymagane dla agenta zadań do nawiązania połączenia z docelową bazą danych. Każdy krok zadania posiada możliwy do dostosowania limit czasu i zasady ponawiania oraz pozwala na opcjonalne określenie parametrów wyjściowych.

#### <a name="job-output"></a>Dane wyjściowe zadania

Wyniki wykonywania kroków zadania w każdej docelowej bazie danych są szczegółowo rejestrowane, a dane wyjściowe skryptu mogą zostać przechwycone w określonej tabeli. Możesz określić bazę danych, aby zapisać wszystkie dane zwrócone z zadania.

#### <a name="job-history"></a>Historia zadania

Historia wykonywania zadań jest przechowywana w *bazie danych zadań* . Zadanie oczyszczania systemu czyści historię wykonywania, która jest starsza niż 45 dni. Aby usunąć historię, która ma mniej niż 45 dni, wywołaj procedurę składowaną **sp_purge_history** w *bazie danych zadań* .

### <a name="agent-performance-capacity-and-limitations"></a>Wydajność agenta, pojemność i ograniczenia

Zadania elastyczne używają minimalnych zasobów obliczeniowych podczas oczekiwania na zakończenie długotrwałych zadań.

W zależności od rozmiaru grupy docelowej baz danych i żądanego czasu wykonywania zadania (liczba równoczesnych procesów roboczych) agent wymaga różnej ilości zasobów obliczeniowych i wydajności *bazy danych zadań* (im więcej elementów docelowych i większa liczba zadań, tym wymagana jest większa ilość zasobów obliczeniowych).

Wersja zapoznawcza jest obecnie ograniczona do 100 współbieżnych zadań.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Zapobieganie zmniejszania wydajności docelowej bazy danych przez zadania

Aby zapewnić, że zasoby nie będą przeciążone podczas uruchamiania zadań w ramach baz danych w elastycznej puli SQL, możliwe jest skonfigurowanie zadań w taki sposób, aby ograniczana była liczba baz danych, w ramach których mogą one być jednocześnie uruchamiane.

## <a name="next-steps"></a>Następne kroki

- [What is SQL Server Agent (Co to jest agent programu SQL Server)](/sql/ssms/agent/sql-server-agent)
- [How to create and manage elastic jobs (Jak tworzyć zadania elastyczne i zarządzać nimi)](elastic-jobs-overview.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi przy użyciu programu PowerShell](elastic-jobs-powershell-create.md)
- [Tworzenie zadań elastycznych i zarządzanie nimi za pomocą języka Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)