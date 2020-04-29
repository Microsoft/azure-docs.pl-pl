---
title: Agent synchronizacji danych dla SQL Data Sync
description: Dowiedz się, jak zainstalować i uruchomić agenta synchronizacji danych dla platformy Azure SQL Data Sync w celu synchronizowania danych z lokalnymi bazami danych SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 39471ebded6280e7d394ee69c2d732b779c9ea50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380908"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agent synchronizacji danych dla platformy Azure SQL Data Sync

Synchronizuj dane z lokalnymi bazami danych SQL Server przez zainstalowanie i skonfigurowanie agenta synchronizacji danych dla SQL Data Sync platformy Azure. Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz [synchronizowanie danych między wieloma bazami danych w chmurze i lokalnymi przy użyciu SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync **nie** obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="download-and-install"></a>Pobieranie i instalowanie

Aby pobrać agenta synchronizacji danych, przejdź do [agenta usługi SQL Data Sync](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instaluj w trybie dyskretnym

Aby zainstalować agenta synchronizacji danych w trybie dyskretnym z wiersza polecenia, wprowadź polecenie podobne do poniższego przykładu. Sprawdź nazwę pliku pobranego pliku MSI i podaj własne wartości argumentów **TARGETDIR** i **ServiceAccount** .

- Jeśli nie podano wartości dla **TARGETDIR**, wartość domyślna to `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Jeśli podano `LocalSystem` wartość PARAMETRU **ServiceAccount**, Użyj uwierzytelniania SQL Server podczas konfigurowania agenta do nawiązywania połączenia z SQL Server lokalnymi.

- W przypadku podania konta użytkownika domeny lub konta użytkownika lokalnego jako wartości **ServiceAccount**należy również podać hasło przy użyciu ARGUMENTU **ServicePassword** . Na przykład `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Synchronizuj dane przy użyciu SQL Server lokalnego

Aby skonfigurować agenta synchronizacji danych, dzięki czemu można synchronizować dane z co najmniej jedną lokalną SQL Server bazami danych, zobacz [Dodawanie lokalnej SQL Server bazy danych](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>Agent synchronizacji danych — często zadawane pytania

### <a name="why-do-i-need-a-client-agent"></a>Dlaczego jest potrzebny agent klienta

Usługa SQL Data Sync komunikuje się z bazami danych SQL Server za pośrednictwem agenta klienta. Ta funkcja zabezpieczeń uniemożliwia bezpośrednią komunikację z bazami danych za zaporą. Gdy usługa SQL Data Sync komunikuje się z agentem, robi to za pomocą szyfrowanych połączeń i unikatowym tokenem lub *kluczem agenta*. Bazy danych SQL Server uwierzytelniają agenta przy użyciu parametrów połączenia i klucza agenta. Ten projekt zapewnia wysoki poziom bezpieczeństwa danych.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Ile wystąpień lokalnego interfejsu użytkownika agenta można uruchomić

Można uruchomić tylko jedno wystąpienie interfejsu użytkownika.

### <a name="how-can-i-change-my-service-account"></a>Jak zmienić konto usługi

Po zainstalowaniu agenta klienta jedynym sposobem zmiany konta usługi jest jej odinstalowanie i zainstalowanie nowego agenta klienta przy użyciu nowego konta usługi.

### <a name="how-do-i-change-my-agent-key"></a>Jak mogę zmienić mój klucz agenta

Klucz agenta może być używany tylko raz przez agenta. Nie można jej ponownie użyć po usunięciu i ponownym zainstalowaniu nowego agenta. Jeśli konieczne jest utworzenie nowego klucza dla istniejącego agenta, należy się upewnić, że ten sam klucz jest zarejestrowany w ramach agenta klienta i usługi SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Jak mogę wycofać agenta klienta

Aby natychmiast unieważnić lub wycofać agenta, Wygeneruj ponownie swój klucz w portalu, ale nie przesyłaj go w interfejsie użytkownika agenta. Ponowne wygenerowanie klucza unieważnia poprzedni klucz, niezależnie od tego, czy odpowiedni Agent jest w trybie online, czy offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Jak mogę przenieść agenta klienta na inny komputer

Jeśli chcesz uruchomić agenta lokalnego z innego komputera niż aktualnie włączony, wykonaj następujące czynności:

1. Zainstaluj agenta na żądanym komputerze.
2. Zaloguj się do portalu SQL Data Sync i ponownie Wygeneruj klucz agenta dla nowego agenta.
3. Użyj nowego interfejsu użytkownika agenta, aby przesłać nowy klucz agenta.
4. Poczekaj, aż agent klienta pobierze listę lokalnych baz danych, które zostały zarejestrowane wcześniej.
5. Podaj poświadczenia bazy danych dla wszystkich baz danych, które są wyświetlane jako nieosiągalne. Te bazy danych muszą być dostępne na nowym komputerze, na którym jest zainstalowany agent programu.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Rozwiązywanie problemów z agentem synchronizacji danych

- [Instalacja, dezinstalacja lub naprawa agenta klienta kończy się niepowodzeniem](#agent-install)

- [Agent klienta nie działa po anulowaniu dezinstalacji](#agent-uninstall)

- [Moja baza danych nie znajduje się na liście agentów](#agent-list)

- [Nie uruchomiono agenta klienta (Błąd 1069)](#agent-start)

- [Nie mogę przesłać klucza agenta](#agent-key)

- [Nie można usunąć agenta klienta z portalu, jeśli skojarzona z nim lokalna baza danych jest nieosiągalna](#agent-delete)

- [Aplikacja agenta synchronizacji lokalnej nie może nawiązać połączenia z lokalną usługą synchronizacji](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>Instalacja, dezinstalacja lub naprawa agenta klienta kończy się niepowodzeniem

- **Przyczyna**. Przyczyną tego błędu może być wiele scenariuszy. Aby ustalić konkretną przyczynę tego błędu, należy zapoznać się z dziennikami.

- **Rozwiązanie**. Aby znaleźć konkretną przyczynę niepowodzenia, wygeneruj i poszukaj dzienników Instalator Windows. Rejestrowanie można włączyć w wierszu polecenia. Na przykład, jeśli pobrany plik instalacyjny to `SQLDataSyncAgent-2.0-x86-ENU.msi`, wygeneruj i Przejrzyj pliki dziennika przy użyciu następujących wierszy poleceń:

  - W przypadku instalacji:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - W przypadku dezinstalacji:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Możesz również włączyć rejestrowanie dla wszystkich instalacji, które są wykonywane przez Instalator Windows. Artykuł z bazy wiedzy Microsoft Knowledge Base, [jak włączyć rejestrowanie Instalator Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) , udostępnia rozwiązanie z jednym kliknięciem umożliwiające włączenie rejestrowania Instalator Windows. Zawiera również lokalizację dzienników.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>Agent klienta nie działa po anulowaniu dezinstalacji

Agent klienta nie działa, nawet po anulowaniu jego dezinstalacji.

- **Przyczyna**. Dzieje się tak, ponieważ agent klienta SQL Data Sync nie przechowuje poświadczeń.

- **Rozwiązanie**. Możesz wypróbować te dwa rozwiązania:

    -   Użyj programu Services. msc, aby ponownie wprowadzić poświadczenia dla agenta klienta.
    -   Odinstaluj tego agenta klienta, a następnie zainstaluj nowy. Pobierz i zainstaluj najnowszego agenta klienta z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>Moja baza danych nie znajduje się na liście agentów

Podczas próby dodania istniejącej bazy danych SQL Server do grupy synchronizacji baza danych nie zostanie wyświetlona na liście agentów.

Te scenariusze mogą spowodować wystąpienie tego problemu:

- **Przyczyna**. Agent klienta i Grupa synchronizacji znajdują się w różnych centrach danych.

- **Rozwiązanie**. Agent klienta i Grupa synchronizacji muszą znajdować się w tym samym centrum danych. Aby skonfigurować to ustawienie, dostępne są dwie opcje:

    -   Utwórz nowego agenta w centrum danych, w którym znajduje się grupa synchronizacji. Następnie zarejestruj bazę danych z tym agentem.
    -   Usuń bieżącą grupę synchronizacji. Następnie utwórz ponownie grupę synchronizacji w centrum danych, w którym znajduje się Agent.

- **Przyczyna**. Lista baz danych agenta klienta nie jest aktualna.

- **Rozwiązanie**. Zatrzymaj, a następnie ponownie uruchom usługę agenta klienta.

    Lokalny agent pobiera listę skojarzonych baz danych tylko przy pierwszym przesyłaniu klucza agenta. Nie pobiera listy skojarzonych baz danych przy kolejnych przesłanych kluczach agentów. Bazy danych zarejestrowane podczas przenoszenia agenta nie są wyświetlane w oryginalnym wystąpieniu agenta.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>Nie uruchomiono agenta klienta (Błąd 1069)

Użytkownik stwierdzi, że Agent nie jest uruchomiony na komputerze, który hostuje SQL Server. Podczas próby ręcznego uruchomienia agenta zostanie wyświetlone okno dialogowe z komunikatem "Błąd 1069: usługa nie została uruchomiona z powodu błędu logowania".

![Błąd synchronizacji danych — okno dialogowe 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Przyczyna**. Przyczyną tego błędu jest to, że hasło na serwerze lokalnym uległo zmianie od czasu utworzenia agenta i hasła agenta.

- **Rozwiązanie**. Zaktualizuj hasło agenta do bieżącego hasła serwera:

  1. Znajdź usługę agenta klienta SQL Data Sync.  
    a. Wybierz pozycję **Uruchom**.  
    b. W polu wyszukiwania wpisz polecenie **Services. msc**.  
    c. W wynikach wyszukiwania wybierz pozycję **usługi**.  
    d. W oknie **usługi** przewiń do pozycji **SQL Data Sync agenta**.  
  1. Kliknij prawym przyciskiem myszy pozycję **SQL Data Sync Agent**, a następnie wybierz polecenie **Zatrzymaj**.
  1. Kliknij prawym przyciskiem myszy pozycję **SQL Data Sync Agent**, a następnie wybierz polecenie **Właściwości**.
  1. Na **SQL Data Sync właściwości agenta**wybierz kartę **Logowanie** .
  1. W polu **hasło** wprowadź hasło.
  1. W polu **Potwierdź hasło** ponownie wprowadź hasło.
  1. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  1. W oknie **usługi** kliknij prawym przyciskiem myszy usługę **Agent SQL Data Sync** , a następnie kliknij polecenie **Uruchom**.
  1. Zamknij okno **usługi** .

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Nie mogę przesłać klucza agenta

Po utworzeniu lub ponownym utworzeniu klucza agenta Próbujesz przesłać klucz za pomocą aplikacji SqlAzureDataSyncAgent. Nie można ukończyć przesłania.

![Okno dialogowe błędu synchronizacji — nie można przesłać klucza agenta](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Wymagania wstępne**. Przed kontynuowaniem sprawdź następujące wymagania wstępne:

  - Usługa SQL Data Sync systemu Windows jest uruchomiona.

  - Konto usługi dla SQL Data Sync systemu Windows ma dostęp do sieci.

  - Port wychodzący 1433 jest otwarty w regule lokalnej zapory.

  - Lokalny adres IP zostanie dodany do reguły zapory serwera lub bazy danych dla bazy danych metadanych synchronizacji.

- **Przyczyna**. Klucz agenta jednoznacznie identyfikuje każdego agenta lokalnego. Klucz musi spełniać dwa warunki:

  -   Klucz agenta klienta na serwerze SQL Data Sync i na komputerze lokalnym musi być identyczny.
  -   Klucz agenta klienta może być używany tylko raz.

- **Rozwiązanie**. Jeśli Agent nie działa, jest to spowodowane tym, że jeden lub oba te warunki nie są spełnione. Aby zapewnić, że agent będzie działał ponownie:

  1. Wygeneruj nowy klucz.
  1. Zastosuj nowy klucz do agenta.

  Aby zastosować nowy klucz do agenta:

  1. W Eksploratorze plików przejdź do katalogu instalacji agenta. Domyślny katalog instalacji to C:\\Program Files (x86)\\Microsoft SQL Data Sync.
  1. Kliknij dwukrotnie podkatalog bin.
  1. Otwórz aplikację SqlAzureDataSyncAgent.
  1. Wybierz pozycję **Prześlij klucz agenta**.
  1. W podanym miejscu Wklej klucz ze schowka.
  1. Wybierz przycisk **OK**.
  1. Zamknij program.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>Nie można usunąć agenta klienta z portalu, jeśli skojarzona z nim lokalna baza danych jest nieosiągalna

Jeśli lokalny punkt końcowy (czyli baza danych) zarejestrowany przy użyciu agenta klienta SQL Data Sync jest niedostępny, nie można usunąć agenta klienta.

- **Przyczyna**. Nie można usunąć agenta lokalnego, ponieważ nieosiągalna baza danych jest nadal zarejestrowana w agencie. Podczas próby usunięcia agenta proces usuwania próbuje uzyskać dostęp do bazy danych, która kończy się niepowodzeniem.

- **Rozwiązanie**. Użyj "Wymuś usunięcie", aby usunąć nieosiągalną bazę danych.

> [!NOTE]
> Jeśli tabele metadanych synchronizacji pozostają po "Wymuś usunięcie", użyj `deprovisioningutil.exe` , aby wyczyścić je.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>Aplikacja agenta synchronizacji lokalnej nie może nawiązać połączenia z lokalną usługą synchronizacji

- **Rozwiązanie**. Spróbuj wykonać następujące kroki:

  1. Zamknij aplikację.  
  1. Otwórz panel usługi składowe.  
    a. W polu wyszukiwania na pasku zadań wpisz polecenie **Services. msc**.  
    b. W wynikach wyszukiwania kliknij dwukrotnie pozycję **usługi**.  
  1. Zatrzymaj usługę **SQL Data Sync** .
  1. Uruchom ponownie usługę **SQL Data Sync** .  
  1. Otwórz ponownie tę aplikację.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Uruchamianie agenta synchronizacji danych z wiersza polecenia

W wierszu polecenia można uruchomić następujące polecenia agenta synchronizacji danych:

### <a name="ping-the-service"></a>Wyślij polecenie ping do usługi

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Wyświetlanie zarejestrowanych baz danych

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Prześlij klucz agenta

#### <a name="usage"></a>Sposób użycia

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Rejestrowanie bazy danych

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Przykłady

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Wyrejestrowywanie bazy danych

Korzystając z tego polecenia, można wyrejestrować bazę danych w całości. Jeśli baza danych uczestniczy w innych grupach synchronizacji, ta operacja przerywa pozostałe grupy synchronizacji.

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Przykład

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Aktualizowanie poświadczeń

#### <a name="usage"></a>Sposób użycia

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Przykłady

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz następujące artykuły:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        -  [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](sql-database-sync-monitor-oms.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)
