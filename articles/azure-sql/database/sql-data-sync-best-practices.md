---
title: Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
description: Poznaj najlepsze rozwiązania dotyczące konfigurowania i uruchamiania usługi Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ee15bfaa1d69e2e5047e7d24986f8e4e7d5b8b31
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180245"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync) 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano najlepsze rozwiązania dotyczące usługi Azure SQL Data Sync.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> W tej chwili usługa Azure SQL Data Sync **nie** obsługuje wystąpienia zarządzanego usługi Azure SQL.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> Bezpieczeństwo i niezawodność

### <a name="client-agent"></a>Agent klienta

-   Zainstaluj agenta klienta przy użyciu konta użytkownika z najniższymi uprawnieniami, które ma dostęp do usługi sieciowej.  
-   Zainstaluj agenta klienta programu na komputerze, który nie jest komputerem SQL Server.  
-   Nie należy rejestrować lokalnej bazy danych z więcej niż jednym agentem.    
    -   Unikaj tego, nawet jeśli synchronizujesz różne tabele dla różnych grup synchronizacji.  
    -   Zarejestrowanie lokalnej bazy danych za pomocą wielu agentów klienta powoduje problemy po usunięciu jednej z grup synchronizacji.

### <a name="database-accounts-with-least-required-privileges"></a>Konta bazy danych z najmniej wymaganymi uprawnieniami

-   **Dla konfiguracji synchronizacji**. Utwórz/Zmień tabelę; ALTER DATABASE; Utwórz procedurę; Wybierz/Zmień schemat; Utwórz typ User-Defined.

-   **Dla trwającej synchronizacji**. Wybierz/Wstaw/Aktualizuj/Usuń w tabelach wybranych do synchronizowania oraz w metadanych synchronizacji i tabelach śledzenia; Uprawnienie EXECUTE w procedurach składowanych utworzonych przez usługę; Uprawnienie EXECUTE dla typów tabel zdefiniowanych przez użytkownika.

-   **W celu anulowania** aprowizacji. Modyfikuj w tabelach część synchronizacji; Zaznacz/Usuń w tabelach metadanych synchronizacji; Kontrola nad tabelami śledzenia synchronizacji, procedurami składowanymi i typami zdefiniowanymi przez użytkownika.

Azure SQL Database obsługuje tylko jeden zestaw poświadczeń. Aby wykonać te zadania w ramach tego ograniczenia, należy wziąć pod uwagę następujące opcje:

-   Zmień poświadczenia dla różnych faz (na przykład *credentials1* na potrzeby instalacji i *credentials2* na bieżąco).  
-   Zmień uprawnienia poświadczeń (oznacza to, że po skonfigurowaniu synchronizacji należy zmienić uprawnienia.

### <a name="auditing"></a>Inspekcja

Zaleca się włączenie inspekcji na poziomie baz danych w grupach synchronizacji. 

## <a name="setup"></a>Konfigurowanie

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> Zagadnienia i ograniczenia dotyczące bazy danych

#### <a name="database-size"></a>Rozmiar bazy danych

Podczas tworzenia nowej bazy danych ustaw maksymalny rozmiar, tak aby był zawsze większy niż wdrażana baza danych. Jeśli maksymalny rozmiar nie zostanie ustawiony na wartość większą niż wdrożona baza danych, synchronizacja nie powiedzie się. Chociaż SQL Data Sync nie oferuje automatycznego wzrostu, można uruchomić polecenie, `ALTER DATABASE` Aby zwiększyć rozmiar bazy danych po jej utworzeniu. Upewnij się, że nie ma limitu rozmiaru bazy danych.

> [!IMPORTANT]
> SQL Data Sync przechowuje dodatkowe metadane dla każdej bazy danych. Upewnij się, że te metadane są używane podczas obliczania wymaganego miejsca. Ilość dodanego obciążenia jest powiązana z szerokością tabel (na przykład wąskich tabel wymaga większego nakładu pracy) i ilości ruchu.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> Zagadnienia i ograniczenia dotyczące tabeli

#### <a name="selecting-tables"></a>Wybieranie tabel

Nie trzeba uwzględniać wszystkich tabel znajdujących się w bazie danych w grupie synchronizacji. Tabele uwzględnione w grupie synchronizacji mają wpływ na wydajność i koszty. Uwzględnij tabele i tabele, od których są one zależne, w grupie synchronizacji tylko wtedy, gdy wymagają tego wymagania biznesowe.

#### <a name="primary-keys"></a>Klucze podstawowe

Każda tabela w grupie synchronizacji musi mieć klucz podstawowy. SQL Data Sync nie może zsynchronizować tabeli, która nie ma klucza podstawowego.

Przed użyciem SQL Data Sync w środowisku produkcyjnym Przetestuj początkową i trwającą wydajność synchronizacji.

#### <a name="empty-tables-provide-the-best-performance"></a>Puste tabele zapewniają najlepszą wydajność

Puste tabele zapewniają najlepszą wydajność w czasie inicjacji. Jeśli tabela docelowa jest pusta, synchronizacja danych używa operacji wstawiania zbiorczego w celu załadowania danych. W przeciwnym razie synchronizacja danych wykonuje porównanie wierszy po wierszu i wstawianie w celu sprawdzenia konfliktów. Jeśli jednak wydajność nie jest istotna, można skonfigurować synchronizację między tabelami, które zawierają już dane.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> Inicjowanie obsługi administracyjnej baz danych

SQL Data Sync zapewnia podstawowe Inicjowanie obsługi bazy danych.

W tej sekcji omówiono ograniczenia dotyczące aprowizacji w programie SQL Data Sync.

#### <a name="autoprovisioning-limitations"></a>Ograniczenia dotyczące autoaprowizacji

SQL Data Sync ma następujące ograniczenia dotyczące autoaprowizacji:

-   Wybierz tylko kolumny, które są tworzone w tabeli docelowej. Wszystkie kolumny, które nie są częścią grupy synchronizacji, nie są obsługiwane w tabelach docelowych.
-   Indeksy są tworzone tylko dla wybranych kolumn. Jeśli indeks tabeli źródłowej zawiera kolumny, które nie są częścią grupy synchronizacji, te indeksy nie są obsługiwane w tabelach docelowych.  
-   Nie są obsługiwane indeksy kolumn typu XML.  
-   Ograniczenia CHECK nie są obsługiwane.  
-   Istniejące Wyzwalacze w tabelach źródłowych nie są obsługiwane.  
-   Widoki i procedury składowane nie są tworzone w docelowej bazie danych.
-   W przypadku aktualizacji KASKADowych i po USUNIĘCIu akcji w kluczu obcym nie są ponownie tworzone w tabelach docelowych.
-   Jeśli masz kolumny dziesiętne lub liczbowe o dokładności większej niż 28, SQL Data Sync może wystąpić problem z przepełnieniem konwersji podczas synchronizacji. Zalecamy ograniczenie dokładności kolumn dziesiętnych lub liczbowych do wartości 28 lub mniejszej.

#### <a name="recommendations"></a>Zalecenia

-   Użyj funkcji autoaprowizacji SQL Data Sync tylko podczas próby wykonania usługi.  
-   Dla środowiska produkcyjnego Zainicjuj obsługę administracyjną schematu bazy danych.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> Gdzie można znaleźć bazę danych centrum

#### <a name="enterprise-to-cloud-scenario"></a>Scenariusz między przedsiębiorstwami a chmurą

Aby zminimalizować opóźnienie, należy pozostawić bazę danych centrum w pobliżu największego stężenia ruchu bazy danych grupy synchronizacji.

#### <a name="cloud-to-cloud-scenario"></a>Scenariusz między chmurą a chmurą

-   Gdy wszystkie bazy danych w grupie synchronizacji znajdują się w jednym z centrów, centrum powinno znajdować się w tym samym centrum danych. Ta konfiguracja zmniejsza opóźnienia i koszt transferu danych między centrami elementów.
-   Gdy bazy danych w grupie synchronizacji znajdują się w wielu centrach danych, centrum powinno znajdować się w tym samym centrum danych co w przypadku większości baz zadań i ruchu w sieci.

#### <a name="mixed-scenarios"></a>Scenariusze mieszane

Zastosuj powyższe wskazówki do złożonych konfiguracji grup synchronizacji, takich jak te, które są mieszane w scenariuszach między przedsiębiorstwami a chmurą i chmurą.

## <a name="sync"></a>Synchronizuj

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> Unikaj powolnej i kosztownej synchronizacji początkowej

W tej sekcji omówiono początkową synchronizację grupy synchronizacji. Dowiedz się, jak zapobiegać dłuższej i bardziej kosztownej synchronizacji początkowej, niż jest to konieczne.

#### <a name="how-initial-sync-works"></a>Jak działa synchronizacja początkowa

Podczas tworzenia grupy synchronizacji należy zacząć od danych tylko w jednej bazie danych. Jeśli masz dane w wielu bazach danych, SQL Data Sync traktuje każdy wiersz jako konflikt, który należy rozwiązać. To rozwiązywanie konfliktów powoduje spowolnienie synchronizacji początkowej. Jeśli masz dane w wielu bazach danych, synchronizacja początkowa może potrwać od kilku dni do kilku miesięcy, w zależności od rozmiaru bazy danych.

Jeśli bazy danych znajdują się w różnych centrach, każdy wiersz musi się poruszać między różnymi centrami danych. Zwiększa to koszt synchronizacji początkowej.

#### <a name="recommendation"></a>Zalecenie

Jeśli to możliwe, Zacznij od danych tylko w jednej z baz danych grupy synchronizacji.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> Zaprojektuj, aby uniknąć pętli synchronizacji

Pętla synchronizacji występuje, gdy istnieją odwołania cykliczne w obrębie grupy synchronizacji. W tym scenariuszu każda zmiana w jednej bazie danych jest nieskończonie i cyklicznie replikowana przez bazy danych w grupie synchronizacji.   

Upewnij się, że nie są używane pętle synchronizacji, ponieważ powodują spadek wydajności i znacząco zwiększają koszty.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Zmiany, które nie są propagowane

#### <a name="reasons-that-changes-fail-to-propagate"></a>Przyczyny niepowodzenia propagacji zmian

Propagacja zmian może się nie powieść z jednego z następujących powodów:

-   Niezgodność schematu/typu danych.
-   Wstawianie wartości null w kolumnach, które nie dopuszcza wartości null.
-   Naruszanie ograniczeń klucza obcego.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Co się stanie, gdy propagowanie zmian nie powiedzie się?

-   Grupa synchronizacji pokazuje, że jest w stanie **ostrzegawczym** .
-   Szczegóły znajdują się w podglądzie dziennika interfejsu użytkownika portalu.
-   Jeśli problem nie zostanie rozwiązany przez 45 dni, baza danych jest nieaktualna.

> [!NOTE]
> Te zmiany nigdy nie są propagowane. Jedynym sposobem na odzyskanie w tym scenariuszu jest ponowne utworzenie grupy synchronizacji.

#### <a name="recommendation"></a>Zalecenie

Regularnie Monitoruj grupę synchronizacji i kondycję bazy danych za pomocą portalu i interfejsu dziennika.


## <a name="maintenance"></a>Konserwacja

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> Unikaj nieaktualnych baz danych i grup synchronizacji

Grupa synchronizacji lub baza danych w grupie synchronizacji mogą stać się nieaktualne. Gdy stan grupy synchronizacji jest **nieaktualny**, przestaje działać. Jeśli stan bazy **danych jest nieaktualny,** dane mogą zostać utracone. Najlepszym rozwiązaniem jest uniknięcie tego scenariusza zamiast próby odzyskania z niego.

#### <a name="avoid-out-of-date-databases"></a>Unikaj nieaktualnych baz danych

Stan bazy danych jest ustawiony na **nieaktualne** , gdy jest on w trybie offline przez 45 dni lub więcej. Aby uniknąć **nieaktualnego** stanu bazy danych, upewnij się, że żadna z baz danych nie jest w trybie offline przez 45 dni lub więcej.

#### <a name="avoid-out-of-date-sync-groups"></a>Unikaj nieaktualnych grup synchronizacji

Stan grupy synchronizacji jest ustawiany na **nieaktualną** , gdy jakakolwiek zmiana w grupie synchronizacji nie zostanie propagowana do pozostałej części grupy synchronizacji przez 45 dni lub więcej. Aby uniknąć **nieaktualnego** stanu grupy synchronizacji, należy regularnie sprawdzać dziennik historii grupy synchronizacji. Upewnij się, że wszystkie konflikty zostały rozwiązane, a zmiany zostały pomyślnie rozpropagowane w całej bazie danych grupy synchronizacji.

Grupa synchronizacji może nie zastosować zmiany z jednego z następujących powodów:

-   Niezgodność schematów między tabelami.
-   Niezgodność danych między tabelami.
-   Wstawianie wiersza z wartością null w kolumnie, która nie zezwala na wartości null.
-   Aktualizowanie wiersza z wartością, która narusza ograniczenie klucza obcego.

Aby zapobiec nieaktualnym grupom synchronizacji:

-   Zaktualizuj schemat, aby zezwolić na wartości, które są zawarte w błędnych wierszach.
-   Zaktualizuj wartości klucza obcego, aby uwzględnić wartości, które są zawarte w wierszach zakończonych niepowodzeniem.
-   Zaktualizuj wartości danych w wierszu zakończonym niepowodzeniem, aby były zgodne ze schematem lub kluczami obcym w docelowej bazie danych.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> Unikaj anulowania aprowizacji

W niektórych okolicznościach Wyrejestrowanie bazy danych z agentem klienta może spowodować niepowodzenie synchronizacji.

#### <a name="scenario"></a>Scenariusz

1. Grupa synchronizacji A została utworzona przy użyciu wystąpienia SQL Database i bazy danych SQL Server, która jest skojarzona z agentem lokalnym 1.
2. Ta sama lokalna baza danych jest zarejestrowana w lokalnym agencie 2 (ten Agent nie jest skojarzony z żadną grupą synchronizacji).
3. Wyrejestrowanie lokalnej bazy danych z agenta lokalnego 2 powoduje usunięcie śledzenia i meta tabel dla grupy synchronizacji A dla lokalnej bazy danych.
4. Operacja grupy synchronizacji nie powiodła się z powodu błędu: "nie można ukończyć bieżącej operacji, ponieważ baza danych nie została zainicjowana do synchronizacji lub nie masz uprawnień do tabel konfiguracji synchronizacji".

#### <a name="solution"></a>Rozwiązanie

Aby uniknąć tego scenariusza, nie należy rejestrować bazy danych z więcej niż jednym agentem.

Aby odzyskać sprawność z tego scenariusza:

1. Usuń bazę danych z każdej grupy synchronizacji, do której należy.  
2. Dodaj bazę danych z powrotem do każdej grupy synchronizacji, z której został usunięty.  
3. Wdróż każdą przydaną grupę synchronizacji (Ta akcja dotyczy bazy danych).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> Modyfikowanie grupy synchronizacji

Nie należy próbować usunąć bazy danych z grupy synchronizacji, a następnie edytować grupy synchronizacji bez wcześniejszego wdrożenia jednej ze zmian.

Zamiast tego należy najpierw usunąć bazę danych z grupy synchronizacji. Następnie wdróż zmianę i poczekaj na zakończenie anulowania aprowizacji. Po zakończeniu anulowania aprowizacji można edytować grupę synchronizacji i wdrożyć zmiany.

W przypadku próby usunięcia bazy danych, a następnie edytowania grupy synchronizacji bez wcześniejszego wdrożenia jednej z tych zmian jedna lub druga operacja zakończy się niepowodzeniem. Interfejs portalu może stać się niespójny. W takim przypadku należy odświeżyć stronę, aby przywrócić poprawny stan.

### <a name="avoid-schema-refresh-timeout"></a>Unikanie limitu czasu odświeżania schematu

Jeśli masz złożony schemat do synchronizacji, podczas odświeżania schematu może wystąpić komunikat "limit czasu operacji", jeśli baza danych metadanych synchronizacji ma niższą jednostkę SKU (przykład: Basic). 

#### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy przeskalować bazę danych metadanych synchronizacji w celu uzyskania wyższej jednostki SKU, takiej jak S3. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfiguracja funkcji SQL Data Sync
    - W portalu — [Samouczek: skonfiguruj SQL Data Sync, aby synchronizować dane między Azure SQL Database i SQL Server](sql-data-sync-sql-server-configure.md)
    - Z programem PowerShell
        -  [Synchronizowanie wielu baz danych w Azure SQL Database przy użyciu programu PowerShell](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Używanie programu PowerShell do synchronizowania bazy danych w SQL Database i bazy danych w wystąpieniu SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](./monitor-tune-overview.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](sql-data-sync-troubleshoot.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](sql-data-sync-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/update-sync-schema-in-sync-group.md)

Aby uzyskać więcej informacji na temat SQL Database, zobacz:

-   [Przegląd SQL Database](sql-database-paas-overview.md)
-   [Zarządzanie cyklem życia bazy danych](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
