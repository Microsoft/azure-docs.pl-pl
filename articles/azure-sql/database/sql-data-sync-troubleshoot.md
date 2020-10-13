---
title: Rozwiązywanie problemów z funkcją SQL Data Sync
description: Dowiedz się, jak identyfikować, rozwiązywać problemy i rozwiązywać typowe problemy z SQL Data Sync na platformie Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: cb66c0371914b29c3db911823519b29df8c4b16c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616469"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Rozwiązywanie problemów z funkcją SQL Data Sync
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano sposób rozwiązywania znanych problemów z SQL Data Sync na platformie Azure. Jeśli istnieje rozwiązanie problemu, jest on dostępny tutaj.

Aby zapoznać się z omówieniem SQL Data Sync, zobacz [synchronizowanie danych między wieloma bazami danych w chmurze i lokalnymi przy użyciu SQL Data Sync na platformie Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> W tej chwili SQL Data Sync **nie obsługuje wystąpienia** zarządzanego usługi Azure SQL.

## <a name="sync-issues"></a>Problemy z synchronizacją

- [Synchronizacja w interfejsie użytkownika portalu dla lokalnych baz danych skojarzonych z agentem klienta nie powiodła się](#sync-fails)

- [Moja grupa synchronizacji jest zablokowana w stanie przetwarzania](#sync-stuck)

- [Widzę błędne dane w moich tabelach](#sync-baddata)

- [Po pomyślnym zsynchronizowaniu widzę niespójne dane klucza podstawowego](#sync-pkdata)

- [Widzę znaczącą spadek wydajności](#sync-perf)

- [Zobaczysz następujący komunikat: "nie można wstawić wartości NULL do kolumny \<column> . Kolumna nie zezwala na wartości nulls. " Co to znaczy i jak można rozwiązać ten problem?](#sync-nulls)

- [Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, że gdy te same dane są synchronizowane w wielu grupach synchronizacji i ciągle nie zmieniają się w wyniku?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> Synchronizacja w interfejsie użytkownika portalu dla lokalnych baz danych skojarzonych z agentem klienta nie powiodła się

Synchronizacja w interfejsie użytkownika portalu SQL Data Sync dla lokalnych baz danych skojarzonych z agentem klienta nie powiodła się. Na komputerze lokalnym, na którym jest uruchomiony Agent programu, w dzienniku zdarzeń są widoczne błędy System. IO. IOException. Błędy mówią, że na dysku jest za mało miejsca.

- **Przyczyna**. Na dysku nie ma wystarczającej ilości miejsca.

- **Rozwiązanie**. Utwórz więcej miejsca na dysku, na którym znajduje się katalog% TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> Moja grupa synchronizacji jest zablokowana w stanie przetwarzania

Grupa synchronizacji w SQL Data Sync była w stanie przetwarzania przez długi czas. Nie odpowiada na polecenie **zatrzymania** , a w dziennikach nie są wyświetlane żadne nowe wpisy.

Jeden z następujących warunków może spowodować zablokowanie grupy synchronizacji w stanie przetwarzania:

- **Przyczyna**. Agent klienta jest w trybie offline

- **Rozwiązanie**. Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Brak agenta klienta lub został on odinstalowany.

- **Rozwiązanie**. Jeśli agent klienta został odinstalowany lub brakuje go z innego powodu:

    1. Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.
    1. Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.

- **Przyczyna**. Usługa SQL Data Sync jest zatrzymana.

- **Rozwiązanie**. Uruchom ponownie usługę SQL Data Sync.

    1. W menu **Start** Wyszukaj pozycję **usługi**.
    1. W wynikach wyszukiwania wybierz pozycję **usługi**.
    1. Znajdź usługę **SQL Data Sync** .
    1. Jeśli stan usługi jest **zatrzymany**, kliknij prawym przyciskiem myszy nazwę usługi, a następnie wybierz polecenie **Uruchom**.

> [!NOTE]
> Jeśli poprzednie informacje nie przeniesieją grupy synchronizacji poza stan przetwarzania, pomoc techniczna firmy Microsoft może zresetować stan grupy synchronizacji. Aby można było zresetować stan grupy synchronizacji, na [stronie pytań i odpowiedzi Microsoft pytań&na Azure SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html)Utwórz wpis. W wpisie Uwzględnij Identyfikator subskrypcji i identyfikator grupy synchronizacji dla grupy, które muszą zostać zresetowane. Inżynier pomoc techniczna firmy Microsoft będzie odpowiadał na Twoje ogłoszenie i informuje o tym, kiedy stan zostanie zresetowany.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Widzę błędne dane w moich tabelach

Jeśli w synchronizacji są uwzględniane tabele o tej samej nazwie, ale które pochodzą z różnych schematów bazy danych, podczas synchronizacji są widoczne błędne dane w tabelach.

- **Przyczyna**. Proces aprowizacji SQL Data Sync używa tych samych tabel śledzenia dla tabel o tej samej nazwie, które znajdują się w różnych schematach. Z tego powodu zmiany z obu tabel zostaną odzwierciedlone w tej samej tabeli śledzenia. Powoduje to błędne zmiany danych podczas synchronizacji.

- **Rozwiązanie**. Upewnij się, że nazwy tabel, które są objęte synchronizacją, są inne, nawet jeśli tabele należą do różnych schematów w bazie danych.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Po pomyślnym zsynchronizowaniu widzę niespójne dane klucza podstawowego

Synchronizacja jest raportowana jako pomyślna, a dziennik nie zawiera żadnych elementów zakończonych niepowodzeniem lub pominiętych, ale należy zauważyć, że dane klucza podstawowego są niespójne wśród baz danych w grupie synchronizacji.

- **Przyczyna**. Wynika to z projektu. Zmiany w kolumnie klucza podstawowego powodują niespójne dane w wierszach, w których zmieniono klucz podstawowy.

- **Rozwiązanie**. Aby uniknąć tego problemu, upewnij się, że nie zmieniono danych w kolumnie klucza podstawowego. Aby rozwiązać ten problem po jego wystąpieniu, Usuń wiersz, który zawiera niespójne dane ze wszystkich punktów końcowych w grupie synchronizacji. Następnie ponownie Wstaw wiersz.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Widzę znaczącą spadek wydajności

Wydajność jest znacznie istotna, prawdopodobnie do momentu, w którym nie można nawet otworzyć interfejsu użytkownika synchronizacji danych.

- **Przyczyna**. Najbardziej prawdopodobną przyczyną jest pętla synchronizacji. Pętla synchronizacji występuje, gdy grupa synchronizacji przez synchronizację wyzwala synchronizację według grupy synchronizacji B, która następnie wyzwala synchronizację według grupy synchronizacji A. Rzeczywista sytuacja może być bardziej złożona i może zawierać więcej niż dwie grupy synchronizacji w pętli. Problem polega na tym, że istnieje cykliczne wyzwalanie synchronizacji spowodowane przez grupy synchronizacji nakładające się na siebie nawzajem.

- **Rozwiązanie**. Najlepszym rozwiązaniem jest zapobieganie. Upewnij się, że w grupach synchronizacji nie ma odwołań cyklicznych. Żaden wiersz synchronizowany przez jedną grupę synchronizacji nie może zostać zsynchronizowany przez inną grupę synchronizacji.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Zobaczysz następujący komunikat: "nie można wstawić wartości NULL do kolumny \<column> . Kolumna nie zezwala na wartości nulls. " Co to znaczy i jak można rozwiązać ten problem? 
Ten komunikat o błędzie wskazuje, że wystąpił jeden z dwóch następujących problemów:
-  Tabela nie ma klucza podstawowego. Aby rozwiązać ten problem, należy dodać klucz podstawowy do wszystkich tabel, które są synchronizowane.
-  W instrukcji CREATE INDEX znajduje się klauzula WHERE. Synchronizacja danych nie obsługuje tego warunku. Aby rozwiązać ten problem, Usuń klauzulę WHERE lub ręcznie wprowadź zmiany we wszystkich bazach danych. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Jak synchronizacja danych obsługuje odwołania cykliczne? Oznacza to, że gdy te same dane są synchronizowane w wielu grupach synchronizacji i ciągle nie zmieniają się w wyniku?
Synchronizacja danych nie obsługuje odwołań cyklicznych. Pamiętaj, aby je uniknąć. 

## <a name="client-agent-issues"></a>Problemy z agentem klienta

Aby rozwiązać problemy z agentem klienta, zobacz [Rozwiązywanie problemów z agentem synchronizacji danych](sql-data-sync-agent-overview.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problemy z instalacją i konserwacją

- [Otrzymuję komunikat "Brak miejsca na dysku"](#setup-space)

- [Nie można usunąć grupy synchronizacji](#setup-delete)

- [Nie można wyrejestrować bazy danych SQL Server](#setup-unreg)

- [Nie mam wystarczających uprawnień do uruchamiania usług systemowych](#setup-perms)

- [Stan bazy danych jest nieaktualny.](#setup-date)

- [Stan grupy synchronizacji to "nieaktualna"](#setup-date2)

- [Nie można usunąć grupy synchronizacji w ciągu trzech minut od odinstalowania lub zatrzymywania agenta](#setup-delete2)

- [Co się stanie w przypadku przywrócenia utraconej lub uszkodzonej bazy danych?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> Otrzymuję komunikat "Brak miejsca na dysku"

- **Przyczyna**. Komunikat "Brak miejsca na dysku" może pojawić się, gdy należy usunąć pozostałościowe pliki. Może to być spowodowane przez oprogramowanie antywirusowe lub pliki są otwarte w przypadku próby usunięcia operacji usuwania.

- **Rozwiązanie**. Ręcznie usuń pliki synchronizacji, które znajdują się w folderze% temp% ( `del \*sync\* /s` ). Następnie usuń podkatalogi w folderze% temp%.

> [!IMPORTANT]
> Nie usuwaj plików, gdy synchronizacja jest w toku.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Nie można usunąć grupy synchronizacji

Próba usunięcia grupy synchronizacji nie powiedzie się. Niektóre z następujących scenariuszy mogą spowodować niepowodzenie usunięcia grupy synchronizacji:

- **Przyczyna**. Agent klienta jest w trybie offline.

- **Rozwiązanie**. Upewnij się, że agent klienta jest w trybie online, a następnie spróbuj ponownie.

- **Przyczyna**. Brak agenta klienta lub został on odinstalowany.

- **Rozwiązanie**. Jeśli agent klienta został odinstalowany lub brakuje go z innego powodu:  
    a. Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.  
    b. Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.

- **Przyczyna**. Baza danych jest w trybie offline.

- **Rozwiązanie**. Upewnij się, że bazy danych są w trybie online.

- **Przyczyna**. Grupa synchronizacji jest inicjowana lub synchronizowana.

- **Rozwiązanie**. Poczekaj, aż zakończy się proces aprowizacji lub synchronizacji, a następnie ponów próbę usunięcia grupy synchronizacji.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> Nie można wyrejestrować bazy danych SQL Server

- **Przyczyna**. Najprawdopodobniej próbujesz wyrejestrować bazę danych, która została już usunięta.

- **Rozwiązanie**. Aby wyrejestrować bazę danych SQL Server, wybierz bazę danych, a następnie wybierz pozycję **Wymuszaj usuwanie**.

  Jeśli ta operacja nie spowoduje usunięcia bazy danych z grupy synchronizacji:

  1. Zatrzymaj, a następnie ponownie uruchom usługę hosta agenta klienta:  
    a. Wybierz menu **Start**.  
    b. W polu wyszukiwania wpisz polecenie **Services. msc**.  
    c. W sekcji **programy** okienka wyników wyszukiwania kliknij dwukrotnie pozycję **usługi**.  
    d. Kliknij prawym przyciskiem myszy usługę **SQL Data Sync** .  
    e. Jeśli usługa jest uruchomiona, Zatrzymaj ją.  
    f. Kliknij prawym przyciskiem myszy usługę, a następnie wybierz polecenie **Uruchom**.  
    przykład Sprawdź, czy baza danych jest nadal zarejestrowana. Jeśli nie jest już zarejestrowany, oznacza to, że wszystko jest gotowe. W przeciwnym razie przejdź do następnego kroku.
  1. Otwórz aplikację agent klienta (SqlAzureDataSyncAgent).
  1. Wybierz pozycję **Edytuj poświadczenia**, a następnie wprowadź poświadczenia dla bazy danych.
  1. Kontynuuj wyrejestrowywanie.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Nie mam wystarczających uprawnień do uruchamiania usług systemowych

- **Przyczyna**. Ten błąd występuje w dwóch sytuacjach:
  -   Nazwa użytkownika i/lub hasło są niepoprawne.
  -   Określone konto użytkownika nie ma wystarczających uprawnień do logowania się jako usługa.

- **Rozwiązanie**. Przyznaj do konta użytkownika poświadczenia logowania jako usługa:

  1. Wybierz kolejno pozycje **Start**  >  **Panel sterowania**  >  **Narzędzia administracyjne**  >  **lokalne zasady zabezpieczeń**  >  **Local Policy**  >  **użytkownika Rights Management**.
  1. Wybierz pozycję **Zaloguj się jako usługa**.
  1. W oknie dialogowym **Właściwości** Dodaj konto użytkownika.
  1. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.
  1. Zamknij wszystkie okna.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> Stan bazy danych jest nieaktualny.

- **Przyczyna**. SQL Data Sync usuwa bazy danych, które były w trybie offline z usługi przez 45 dni lub więcej (zliczane od czasu przekroczenia bazy danych w trybie offline). Jeśli baza danych jest w trybie offline przez 45 dni lub więcej, a następnie wróci do trybu online, jego **stan jest nieaktualny.**

- **Rozwiązanie**. Można uniknąć **nieaktualnego** stanu, upewniając się, że żadna z baz danych nie przejdzie do trybu offline przez 45 dni lub dłużej.

  Jeśli stan bazy danych jest nieaktualny **:**

  1. Usuń bazę danych z **nieaktualnego** stanu z grupy synchronizacji.
  1. Dodaj bazę danych z powrotem do grupy synchronizacji.

  > [!WARNING]
  > Utracisz wszystkie zmiany wprowadzone do tej bazy danych, gdy była ona w trybie offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> Stan grupy synchronizacji to "nieaktualna"

- **Przyczyna**. Jeśli co najmniej jedna zmiana nie zostanie zastosowana w całym okresie przechowywania wynoszącym 45 dni, Grupa synchronizacji może stać się nieaktualna.

- **Rozwiązanie**. Aby uniknąć **nieaktualnego** stanu grupy synchronizacji, należy regularnie przeanalizować wyniki zadań synchronizacji w podglądzie historii. Zbadaj i rozwiąż wszelkie zmiany, które nie zostały zastosowane.

  Jeśli stan grupy synchronizacji jest **nieaktualny**, Usuń grupę synchronizacji, a następnie utwórz ją ponownie.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Nie można usunąć grupy synchronizacji w ciągu trzech minut od odinstalowania lub zatrzymywania agenta

Nie można usunąć grupy synchronizacji w ciągu trzech minut od odinstalowania lub zatrzymywania skojarzonego SQL Data Sync agenta klienta.

- **Rozwiązanie**.

  1. Usuń grupę synchronizacji, gdy skojarzone agenci synchronizacji są w trybie online (zalecane).
  1. Jeśli Agent jest w trybie offline, ale jest zainstalowany, przełącz go w tryb online na komputerze lokalnym. Poczekaj na wyświetlenie stanu agenta jako **online** w portalu SQL Data Sync. Następnie Usuń grupę synchronizacji.
  1. Jeśli Agent jest w trybie offline, ponieważ został odinstalowany:  
    a.  Usuń plik XML agenta z folderu instalacji usługi SQL Data Sync, jeśli plik istnieje.  
    b.  Zainstaluj agenta na komputerze lokalnym (może to być ten sam lub inny komputer). Następnie prześlij klucz agenta wygenerowany w portalu dla agenta, który jest widoczny jako będący w trybie offline.  
    c. Spróbuj usunąć grupę synchronizacji.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> Co się stanie w przypadku przywrócenia utraconej lub uszkodzonej bazy danych?

W przypadku przywrócenia utraconej lub uszkodzonej bazy danych z kopii zapasowej może istnieć niezbieżność danych w grupach synchronizacji, do których należy baza danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz:

-   Przegląd — [Synchronizacja danych między wieloma bazami danych w chmurze i lokalnymi przy użyciu SQL Data Sync na platformie Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Samouczek: skonfiguruj SQL Data Sync, aby synchronizować dane między Azure SQL Database i SQL Server](sql-data-sync-sql-server-configure.md)
    - Z programem PowerShell
        -  [Synchronizowanie wielu baz danych w Azure SQL Database przy użyciu programu PowerShell](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Używanie programu PowerShell do synchronizowania bazy danych w Azure SQL Database i bazy danych w wystąpieniu SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Agent synchronizacji danych dla SQL Data Sync na platformie Azure](sql-data-sync-agent-overview.md)
-   Najlepsze rozwiązania — [najlepsze rozwiązania dotyczące SQL Data Sync na platformie Azure](sql-data-sync-best-practices.md)
-   Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](sql-data-sync-monitor-sync.md)
-   Aktualizowanie schematu synchronizacji
    -   Przy użyciu języka Transact-SQL — [Automatyzowanie replikacji zmian schematu w SQL Data Sync na platformie Azure](sql-data-sync-update-sync-schema.md)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/update-sync-schema-in-sync-group.md)

Aby uzyskać więcej informacji na temat SQL Database, zobacz:

-   [Omówienie usługi SQL Database](sql-database-paas-overview.md)
-   [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
