---
title: Zarządzanie schematem w aplikacji z wieloma dzierżawcami
description: Zarządzanie schematami wielu dzierżaw w aplikacji z wieloma dzierżawami, która korzysta z usługi Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793300"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Zarządzanie schematem w aplikacji SaaS, która używa baz danych z wieloma dzierżawcami podzielonej na fragmenty
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten samouczek sprawdza wyzwania w zakresie utrzymywania floty baz danych w aplikacji oprogramowanie jako usługa (SaaS). Rozwiązania są prezentowane w celu Fanning zmian schematu w ramach floty baz danych.

Podobnie jak w przypadku wszystkich aplikacji, Wingtip bilety SaaS aplikacji będzie się rozwijać w miarę upływu czasu i będą wymagały zmian w bazie danych. Zmiany mogą wpływać na schemat lub dane referencyjne lub stosować zadania konserwacji bazy danych. W przypadku aplikacji SaaS przy użyciu bazy danych na potrzeby wzorca dzierżawy zmiany muszą być skoordynowane dla potencjalnie ogromnej floty baz danych dzierżaw. Ponadto należy uwzględnić te zmiany w procesie aprowizacji bazy danych, aby upewnić się, że są one uwzględnione w nowych bazach danych w miarę ich tworzenia.

#### <a name="two-scenarios"></a>Dwa scenariusze

W tym samouczku przedstawiono dwa następujące scenariusze:
- Wdróż aktualizacje danych referencyjnych dla wszystkich dzierżawców.
- Skompiluj ponownie indeks tabeli zawierającej dane referencyjne.

Funkcja [zadań elastycznych](./elastic-jobs-overview.md) Azure SQL Database służy do wykonywania tych operacji w bazach danych dzierżaw. Zadania te działają również w odniesieniu do bazy danych dzierżawy "template". W przykładowej aplikacji biletów Wingtip ta baza danych szablonów jest kopiowana w celu aprowizacji nowej bazy danych dzierżawy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz agenta zadań.
> * Wykonaj zapytanie T-SQL dotyczące wielu baz danych dzierżawy.
> * Zaktualizuj dane referencyjne we wszystkich bazach danych dzierżaw.
> * Utwórz indeks tabeli we wszystkich bazach danych dzierżaw.

## <a name="prerequisites"></a>Wymagania wstępne

- Wielodostępna aplikacja bazy danych biletów Wingtip musi już być wdrożona:
    - Aby uzyskać instrukcje, zobacz pierwszy samouczek, w którym wprowadzono bilety Wingtip SaaS aplikacji bazy danych z wieloma dzierżawcami:<br />[Wdróż i Eksploruj aplikację podzielonej na fragmenty z wieloma dzierżawcami, która używa Azure SQL Database](./saas-multitenantdb-get-started-deploy.md).
        - Proces wdrażania przebiega krócej niż pięć minut.
    - Musisz mieć zainstalowaną *podzielonej na fragmenty wielodostępną* wersję Wingtip. Wersje *autonomiczne* i *bazy danych na dzierżawcę* nie obsługują tego samouczka.

- Należy zainstalować najnowszą wersję programu SQL Server Management Studio (SSMS). [Pobierz i zainstaluj program SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

- Należy zainstalować Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps).

> [!NOTE]
> W tym samouczku są używane funkcje usługi Azure SQL Database, które znajdują się w ograniczonej wersji zapoznawczej ([zadania Elastic Database](elastic-database-client-library.md)). Jeśli chcesz wykonać ten samouczek, podaj identyfikator subskrypcji, aby *SaaSFeedback \@ Microsoft.com* z tematem "zadania elastyczne" w wersji zapoznawczej. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, [pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej](https://github.com/jaredmoo/azure-powershell/releases). Ta wersja zapoznawcza jest ograniczona, więc skontaktuj się z *SaaSFeedback \@ Microsoft.com* , aby uzyskać powiązane pytania lub pomoc techniczną.

## <a name="introduction-to-saas-schema-management-patterns"></a>Wprowadzenie do wzorców zarządzania schematami SaaS

Model bazy danych z wieloma dzierżawcami podzielonej na fragmenty używany w tym przykładzie umożliwia bazie danych dzierżawców zawiera co najmniej jedną dzierżawę. Ten przykład eksploruje potencjalne użycie kombinacji baz danych z wieloma dzierżawcami i jedną dzierżawą, umożliwiając model zarządzania dzierżawcą *hybrydową* . Zarządzanie zmianami w tych bazach danych może być skomplikowane. [Zadania elastyczne](./elastic-jobs-overview.md) ułatwiają administrację i zarządzanie dużą liczbą baz danych. Zadania umożliwiają bezpieczne i niezawodne uruchamianie skryptów Transact-SQL jako zadań w odniesieniu do grupy baz danych dzierżaw. Zadania są niezależne od interakcji z użytkownikiem lub danych wejściowych. Tej metody można użyć do wdrożenia zmian schematu lub wspólnych danych referencyjnych we wszystkich dzierżawcach w aplikacji. Zadania elastyczne mogą również służyć do obsługi złota kopia bazy danych. Szablon jest używany do tworzenia nowych dzierżawców, zawsze w celu zapewnienia, że najnowsze schematy i dane referencyjne są używane.

![ekran](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Ograniczona wersja zapoznawcza Zadań elastycznych

Dostępna jest nowa wersja zadań elastycznych, która jest teraz zintegrowaną funkcją Azure SQL Database. Nowa wersja Zadań elastycznych jest obecnie dostępna w ograniczonej wersji zapoznawczej. Ograniczona wersja zapoznawcza jest obecnie obsługiwana przy użyciu programu PowerShell do tworzenia agenta zadań i języka T-SQL w celu tworzenia zadań i zarządzania nimi.
> [!NOTE]
> Ten samouczek zakłada użycie funkcji usługi SQL Database, które znajdują się w ograniczonej wersji zapoznawczej (zadania Elastic Database). Jeśli chcesz wykonać ten samouczek, podaj identyfikator subskrypcji SaaSFeedback@microsoft.com z tematem "zadania elastyczne" w wersji zapoznawczej. Po otrzymaniu potwierdzenia, że Twoja subskrypcja została włączona, pobierz i zainstaluj najnowsze polecenia cmdlet zadań w wersji wstępnej. Ta wersja zapoznawcza jest ograniczona, dlatego należy skontaktować się z SaaSFeedback@microsoft.com tematem powiązanych pytań lub pomocy technicznej.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Pobierz Wingtip bilety SaaS kodu źródłowego i skryptów aplikacji bazy danych z wieloma dzierżawcami

Wingtip bilety SaaS wielodostępnych skryptów bazy danych i kodu źródłowego aplikacji są dostępne w repozytorium [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) w witrynie GitHub. Zapoznaj się z [ogólnymi wskazówkami](saas-tenancy-wingtip-app-guidance-tips.md) dotyczącymi kroków pobierania i odblokowywania Wingtip biletów SaaS.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Tworzenie bazy danych agenta zadań i nowego agenta zadań

Ten samouczek wymaga użycia programu PowerShell do utworzenia bazy danych i agenta zadań agenta zadań. Podobnie jak w przypadku bazy danych MSDB używanej przez program SQL Agent, Agent zadań korzysta z bazy danych w Azure SQL Database do przechowywania definicji zadań, stanu zadania i historii. Po utworzeniu agenta zadań można natychmiast utworzyć i monitorować zadania.

1. W programie **POWERSHELL ISE** Otwórz *... \\ \\ \\Demo-SchemaManagement.ps1zarządzania schematami modułów szkoleniowych* .
2. Naciśnij klawisz **F5** , aby uruchomić skrypt.

Skrypt *Demo-SchemaManagement.ps1* wywołuje skrypt *Deploy-SchemaManagement.ps1* , aby utworzyć bazę danych o nazwie _jobagent_ na serwerze wykazu. Następnie skrypt tworzy agenta zadania, przekazując bazę danych _jobagent_ jako parametr.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Tworzenie zadania służącego do wdrożenia nowych danych referencyjnych we wszystkich dzierżawach

#### <a name="prepare"></a>Przygotowywanie

Baza danych każdej dzierżawy zawiera zestaw typów miejsc w tabeli **VenueTypes** . Każdy typ miejsca określa rodzaj zdarzeń, które mogą być hostowane w miejscu. Te typy miejsc odpowiadają obrazom tła widocznym w aplikacji zdarzenia dzierżawców.  W tym ćwiczeniu wdrażasz aktualizację do wszystkich baz danych, aby dodać dwa dodatkowe typy miejsc: *wyścigi motocykla* i *Trefl* .

Najpierw przejrzyj typy miejsc zawarte w każdej bazie danych dzierżawcy. Połącz się z jedną z baz danych dzierżaw w SQL Server Management Studio (SSMS) i sprawdź tabelę VenueTypes.  Możesz również zbadać tę tabelę w edytorze zapytań w Azure Portal dostępnym ze strony baza danych.

1. Otwórz narzędzie SSMS i Połącz się z serwerem dzierżawy: *tenants1-DPT- &lt; user &gt; . Database.Windows.NET*
1. Aby upewnić się, że *Swimming Club* **nie są** obecnie uwzględniane wyścigi i klubu z *motocykla* , przejdź do bazy danych *contosoconcerthall* na serwerze *Tenants1-DPT- &lt; User &gt;* i zbadaj tabelę *VenueTypes* .



#### <a name="steps"></a>Kroki

Teraz utworzysz zadanie aktualizowania tabeli **VenueTypes** w każdej bazie danych dzierżawców, dodając dwa nowe typy miejsc.

Aby utworzyć nowe zadanie, należy użyć zestawu zadań procedury składowane, które zostały utworzone w bazie danych _jobagent_ . Procedury składowane zostały utworzone podczas tworzenia agenta zadań.

1. W programie SSMS Połącz się z serwerem dzierżawy: tenants1-MT- &lt; User &gt; . Database.Windows.NET

2. Przejdź do bazy danych *tenants1* .

3. Zbadaj tabelę *VenueTypes* , aby upewnić się *, że na* liście wyników nie ma jeszcze *klubu i trefl* .

4. Połącz się z serwerem wykazu, który jest *katalogiem-MT- &lt; user &gt; . Database.Windows.NET* .

5. Nawiąż połączenie z bazą danych _jobagent_ na serwerze wykazu.

6. W programie SSMS Otwórz plik *... \\ Moduły uczenia \\ zarządzania schematami \\ DeployReferenceData. SQL* .

7. Zmodyfikuj instrukcję: set @User = &lt; User i Zastąp &gt; wartość użytkownika używaną podczas wdrażania wielodostępnej aplikacji bazy danych SaaS biletów Wingtip.

8. Naciśnij klawisz **F5** , aby uruchomić skrypt.

#### <a name="observe"></a>Obserwacj

Obserwuj następujące elementy w skrypcie *DeployReferenceData. SQL* :

- **SP \_ Add \_ Target \_ Group** tworzy nazwę grupy docelowej *DemoServerGroup* i dodaje docelowe elementy członkowskie do grupy.

- **\_ \_ \_ \_ członek grupy Dodaj grupę docelową dodatku SP** dodaje następujące elementy:
    - Typ elementu członkowskiego *serwera* docelowego.
        - Jest to serwer *tenants1-MT- &lt; User &gt;* , który zawiera bazy danych dzierżawców.
        - Uwzględnienie serwera obejmuje bazy danych dzierżawy, które istnieją w czasie wykonywania zadania.
    - Docelowy typ elementu członkowskiego *bazy danych* dla bazy danych szablonów ( *basetenantdb* ), który znajduje się w *katalogu-MT- &lt; User &gt;* Server,
    - Docelowy typ elementu członkowskiego *bazy danych* do uwzględnienia bazy danych *adhocreporting* , która jest używana w późniejszym samouczku.

- **SP \_ Add \_ Job** tworzy zadanie o nazwie *wdrożenie danych referencyjnych* .

- **SP \_ Add \_ JobStep** tworzy krok zadania zawierający tekst polecenia T-SQL w celu zaktualizowania tabeli referencyjnej, VenueTypes.

- Pozostałe widoki w skrypcie dotyczą istnienia obiektów i monitorowania wykonania zadań. Użyj tych zapytań, aby przejrzeć wartość stanu w kolumnie **cykl życia** , aby określić, kiedy zadanie zostało zakończone. Zadanie aktualizuje bazę danych dzierżawców i aktualizuje dwie dodatkowe bazy danych, które zawierają tabelę referencyjną.

W programie SSMS przejdź do bazy danych dzierżawy na serwerze *tenants1-MT &lt; - &gt; User* . Zbadaj tabelę *VenueTypes* , aby upewnić się, że do tabeli dodano teraz dane wyścigowe i *klubu* z *motocykla* . Łączna liczba typów miejsc powinna wzrósł o dwa.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Tworzenie zadania do zarządzania indeksem tabeli referencyjnej

To ćwiczenie tworzy zadanie w celu odbudowania indeksu klucza podstawowego tabeli referencyjnej we wszystkich bazach danych dzierżaw. Ponowne kompilowanie indeksu to typowa operacja zarządzania bazami danych, którą administrator może uruchomić po załadowaniu dużej ilości danych w celu zwiększenia wydajności.

1. W programie SSMS Nawiąż połączenie z bazą danych _jobagent_ w *katalogu-MT- &lt; User &gt; . Database.Windows.NET* .

2. W programie SSMS Otwórz pozycję *... \\ Moduły uczenia \\ zarządzania schematami \\ OnlineReindex. SQL* .

3. Naciśnij klawisz **F5** , aby uruchomić skrypt.

#### <a name="observe"></a>Obserwacj

Obserwuj następujące elementy w skrypcie *OnlineReindex. SQL* :

* **SP \_ Add \_ Job** tworzy nowe zadanie o nazwie *online reindex PK \_ \_ VenueTyp \_ \_ 265E44FD7FD4C885* .

* **SP \_ Add \_ JobStep** tworzy krok zadania zawierający tekst polecenia T-SQL w celu zaktualizowania indeksu.

* Pozostałe widoki w ramach wykonywania zadania monitora skryptów. Użyj tych zapytań, aby przejrzeć wartość stanu w kolumnie **cykl życia** , aby określić, kiedy zadanie zostało pomyślnie zakończone na wszystkich członkach grupy docelowej.

## <a name="additional-resources"></a>Dodatkowe zasoby

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Zarządzanie skalowaną w poziomie bazą danych w chmurze](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie agenta zadań do uruchamiania zadań T-SQL w wielu bazach danych
> * Aktualizowanie danych referencyjnych we wszystkich bazach danych dzierżaw
> * Tworzenie indeksu tabeli we wszystkich bazach danych dzierżaw

Następnie Wypróbuj [samouczek sprawozdawczości ad hoc](./saas-multitenantdb-adhoc-reporting.md) , aby poznać uruchamianie zapytań rozproszonych w bazach danych dzierżaw.