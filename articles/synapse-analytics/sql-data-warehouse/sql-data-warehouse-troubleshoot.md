---
title: Rozwiązywanie problemów z dedykowaną pulą SQL (dawniej SQL DW)
description: Rozwiązywanie problemów z dedykowaną pulą SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 949a89dd4e84bdb44ad15eaff9ca160fdd21b395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602132"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Rozwiązywanie problemów z dedykowaną pulą SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

W tym artykule opisano typowe problemy związane z rozwiązywaniem problemów w dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.

## <a name="connecting"></a>Łączenie

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Nieudane logowanie użytkownika „NT AUTHORITY\ANONYMOUS LOGON”. (Microsoft SQL Server, błąd: 18456) | Ten błąd występuje, gdy użytkownik usługi Azure AD próbuje nawiązać połączenie z bazą danych Master, ale nie ma użytkownika w głównej.  Aby rozwiązać ten problem, należy określić dedykowaną pulę SQL (dawniej SQL DW), z którą chcesz nawiązać połączenie, lub dodać użytkownika do bazy danych Master.  Aby uzyskać więcej informacji, zobacz artykuł [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md) . |
| Podmiot zabezpieczeń serwera „nousername” nie jest w stanie uzyskać dostępu do bazy danych „master” w bieżącym kontekście zabezpieczeń. Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Logowanie użytkownika „nousername” nie powiodło się. (Microsoft SQL Server, błąd: 916) | Ten błąd występuje, gdy użytkownik usługi Azure AD próbuje nawiązać połączenie z bazą danych Master, ale nie ma użytkownika w głównej.  Aby rozwiązać ten problem, należy określić dedykowaną pulę SQL (dawniej SQL DW), z którą chcesz nawiązać połączenie, lub dodać użytkownika do bazy danych Master.  Aby uzyskać więcej informacji, zobacz artykuł [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md) . |
| Błąd CTAIP                                                  | Ten błąd może wystąpić, gdy została utworzona nazwa logowania w bazie danych Master SQL Database, ale nie w określonej bazie danych SQL.  Jeśli ten błąd wystąpi, zapoznaj się z artykułem [Omówienie zabezpieczeń](sql-data-warehouse-overview-manage-security.md) .  W tym artykule wyjaśniono, jak utworzyć identyfikator logowania i użytkownika w bazie danych Master, a następnie jak utworzyć użytkownika w bazie danych SQL. |
| Zablokowany przez zaporę                                          | Dedykowana Pula SQL (wcześniej SQL DW) jest chroniona przez zapory w celu zapewnienia, że tylko znane adresy IP mają dostęp do bazy danych. Zapory są domyślnie bezpieczne, co oznacza, że musisz jawnie włączyć i adres IP lub zakres adresów, aby można było nawiązać połączenie.  Aby skonfigurować zaporę w celu uzyskania dostępu, wykonaj kroki opisane w sekcji [Konfigurowanie dostępu do zapory serwera dla adresu IP klienta](create-data-warehouse-portal.md) w [instrukcje aprowizacji](create-data-warehouse-portal.md). |
| Nie można nawiązać połączenia z narzędziem lub sterownikiem                           | Dedykowana Pula SQL (wcześniej SQL DW) zaleca użycie programu [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [SSDT dla programu Visual Studio](sql-data-warehouse-install-visual-studio.md)lub [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) do wykonywania zapytań dotyczących danych. Aby uzyskać więcej informacji na temat sterowników i łączenia się z usługą Azure Synapse, zobacz [sterowniki dla platformy Azure Synapse](sql-data-warehouse-connection-strings.md) i Połącz się z artykułami [Synapse platformy Azure](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>narzędzia

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| W Eksploratorze obiektów programu Visual Studio brakuje użytkowników usługi Azure AD           | Jest to znany problem.  Aby obejść ten sposób, Wyświetl użytkowników w [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Zobacz [uwierzytelnianie w usłudze Azure Synapse](sql-data-warehouse-authentication.md) , aby dowiedzieć się więcej o korzystaniu z Azure Active Directory z dedykowaną pulą SQL (dawniej SQL DW). |
| Ręczne wykonywanie skryptów, Używanie kreatora skryptów lub Nawiązywanie połączenia za pośrednictwem programu SSMS jest powolne, nie odpowiada ani nie produkuje błędów | Upewnij się, że utworzono użytkowników w bazie danych Master. W obszarze Opcje obsługi skryptów upewnij się również, że wersja aparatu jest ustawiona na wartość "Microsoft Azure Synapse Analytics Edition", a typ aparatu to "Microsoft Azure SQL Database". |
| Generowanie skryptów kończy się niepowodzeniem w programie SSMS                               | Generowanie skryptu dla dedykowanej puli SQL (dawniej SQL DW) kończy się niepowodzeniem, jeśli opcja "Generuj skrypt dla obiektów zależnych" ma wartość "true". Aby obejść ten krok, użytkownicy muszą ręcznie przejść do **opcji narzędzia-> Options->Eksplorator obiektów SQL Server-> generować skrypt dla opcji zależnych i ustawić na wartość false** . |

## <a name="data-ingestion-and-preparation"></a>Wprowadzanie i przygotowywanie danych

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Eksportowanie pustych ciągów przy użyciu CETAS spowoduje utratę wartości NULL w plikach Parquet i ORC. Zwróć uwagę, Jeśli eksportujesz puste ciągi z kolumn z ograniczeniami NOT NULL, CETAS spowoduje odrzucenie rekordów i eksportowanie może być niemożliwe. | Usuń puste ciągi lub nienaruszane kolumny w instrukcji SELECT w CETAS. |
| Ładowanie wartości spoza zakresu 0-127 do kolumny tinyint dla formatu pliku Parquet i ORC nie jest obsługiwane. | Określ większy typ danych dla kolumny docelowej.           |

## <a name="performance"></a>Wydajność

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Rozwiązywanie problemów z wydajnością zapytań                            | Jeśli próbujesz rozwiązać określone zapytanie, Zacznij od [uczenia się, jak monitorować zapytania](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problemy dotyczące przestrzeni TempDB | [Monitoruj](sql-data-warehouse-manage-monitor.md#monitor-tempdb) użycie miejsca w bazie danych tempdb.  Typowymi przyczynami braku wolnego miejsca w bazie danych TempDB są:<br>-Za mało zasobów przypisywanych do zapytania powodującego rozłożenie danych na TempDB.  Zobacz [zarządzanie obciążeniami](resource-classes-for-workload-management.md) <br>-Statystyki nie są obecne lub są nieaktualne, powodując nadmierne przenoszenie danych.  Szczegółowe informacje na temat tworzenia statystyk można znaleźć w temacie [Obsługa statystyk tabeli](sql-data-warehouse-tables-statistics.md) .<br>-Pamięć TempDB jest alokowana na poziom usług.  [Skalowanie dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) do wyższego ustawienia jednostek dwu powoduje przydzielenie większej ilości miejsca w bazie danych tempdb.|
| Niska wydajność zapytań i plany często wynikają z brakujących statystyk | Najbardziej typową przyczyną niskiej wydajności jest brak statystyk w tabelach.  Zobacz temat [konserwowanie statystyk tabeli](sql-data-warehouse-tables-statistics.md) , aby uzyskać szczegółowe informacje na temat tworzenia statystyk oraz tego, dlaczego mają one kluczowe znaczenie dla wydajności. |
| W kolejce niskie współbieżność/zapytania                             | Zrozumienie [zarządzania obciążeniem](resource-classes-for-workload-management.md) jest ważne, aby zrozumieć, jak zrównoważyć alokację pamięci za pomocą współbieżności. |
| Jak zaimplementować najlepsze rozwiązania                              | Najlepszym miejscem, aby dowiedzieć się, jak zwiększyć wydajność zapytań, jest [dedykowana Pula SQL (dawniej SQL DW)](sql-data-warehouse-best-practices.md) . |
| Jak zwiększyć wydajność dzięki skalowaniu                      | Czasami rozwiązanie zwiększające wydajność to po prostu dodanie większej mocy obliczeniowej do zapytań przez [przeskalowanie dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-manage-compute-overview.md). |
| Niska wydajność zapytań w wyniku niskiej jakości indeksu     | Czasami zapytania mogą spowalniać działanie z powodu [niskiej jakości indeksu magazynu kolumn](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Zapoznaj się z tym artykułem, aby uzyskać więcej informacji oraz jak [ponownie skompilować indeksy w celu zwiększenia jakości segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Zarządzanie systemem

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nie można wykonać operacji, ponieważ serwer przekroczy dozwolony limit przydziału jednostek transakcji bazy danych wynoszący 45000. | Zmniejsz [jednostek dwu](what-is-a-data-warehouse-unit-dwu-cdwu.md) bazy danych, którą próbujesz utworzyć, lub [Zażądaj zwiększenia limitu przydziału](sql-data-warehouse-get-started-create-support-ticket.md). |
| Badanie wykorzystania miejsca                              | Zobacz [rozmiary tabeli](sql-data-warehouse-tables-overview.md#table-size-queries) , aby zrozumieć wykorzystanie miejsca w systemie. |
| Pomoc dotycząca zarządzania tabelami                                    | Zobacz artykuł [Omówienie tabeli](sql-data-warehouse-tables-overview.md) , aby uzyskać pomoc dotyczącą zarządzania tabelami.  Ten artykuł zawiera również linki do bardziej szczegółowych tematów, takich jak [typy danych tabeli](sql-data-warehouse-tables-data-types.md), [dystrybuowanie tabeli](sql-data-warehouse-tables-distribute.md), [indeksowanie tabeli](sql-data-warehouse-tables-index.md),  [partycjonowanie](sql-data-warehouse-tables-partition.md)tabeli, [Obsługa statystyk tabeli](sql-data-warehouse-tables-statistics.md) i [tabel tymczasowych](sql-data-warehouse-tables-temporary.md). |
| Pasek postępu programu transparent Data Encryption (TDE) nie jest aktualizowany w Azure Portal | Stan TDE można wyświetlić za pomocą [programu PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Różnice między SQL Database

| Problem                                 | Rozwiązanie                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nieobsługiwane funkcje SQL Database     | Zobacz [Nieobsługiwane funkcje tabeli](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nieobsługiwane typy danych SQL Database   | Zobacz [nieobsługiwane typy danych](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Ograniczenia procedury składowanej          | Zobacz [ograniczenia procedury składowanej](sql-data-warehouse-develop-stored-procedures.md#limitations) , aby poznać niektóre ograniczenia procedur składowanych. |
| UDF nie obsługują instrukcji SELECT | Jest to bieżące ograniczenie naszych UDF.  Zobacz [Tworzenie funkcji](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dla składni obsługiwanej przez nas. |
| sp_rename (wersja zapoznawcza) kolumn nie działa w schematach poza *dbo* | Jest to bieżące ograniczenie Synapse [sp_rename (wersja zapoznawcza) dla kolumn](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Kolumny w obiektach, które nie są częścią schematu *dbo* , można zmienić za pomocą CTAs w nową tabelę. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc w znalezieniu rozwiązania problemu, poniżej przedstawiono inne zasoby, które można wypróbować.

* [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
* [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-synapse-analytics.html)
* [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)