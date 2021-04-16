---
title: Rozwiązywanie problemów z dedykowaną pulą SQL (dawniej SQL DW)
description: Rozwiązywanie problemów z dedykowaną pulą SQL (dawniej SQL DW) w Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: af653585ec1b57b5fd697dc755e495a96e04e677
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565410"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Rozwiązywanie problemów z dedykowaną pulą SQL (dawniej SQL DW) w Azure Synapse Analytics

W tym artykule wymieniono typowe problemy z rozwiązywaniem problemów z dedykowaną pulą SQL (dawniej SQL DW) w Azure Synapse Analytics.

## <a name="connecting"></a>Łączenie

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Nieudane logowanie użytkownika „NT AUTHORITY\ANONYMOUS LOGON”. (Microsoft SQL Server, błąd: 18456) | Ten błąd występuje, gdy użytkownik usługi Azure AD próbuje nawiązać połączenie z bazą danych master, ale nie ma użytkownika w bazie danych master.  Aby rozwiązać ten problem, określ dedykowaną pulę SQL (dawniej SQL DW), z którą chcesz nawiązać połączenie w czasie połączenia, lub dodaj użytkownika do bazy danych master.  Aby uzyskać [więcej informacji,](sql-data-warehouse-overview-manage-security.md) zobacz artykuł Omówienie zabezpieczeń. |
| Podmiot zabezpieczeń serwera „nousername” nie jest w stanie uzyskać dostępu do bazy danych „master” w bieżącym kontekście zabezpieczeń. Nie można otworzyć domyślnej bazy danych użytkownika. Logowanie nie powiodło się. Logowanie użytkownika „nousername” nie powiodło się. (Microsoft SQL Server, błąd: 916) | Ten błąd występuje, gdy użytkownik usługi Azure AD próbuje nawiązać połączenie z bazą danych master, ale nie ma użytkownika w bazie danych master.  Aby rozwiązać ten problem, określ dedykowaną pulę SQL (dawniej SQL DW), z którą chcesz nawiązać połączenie w czasie połączenia, lub dodaj użytkownika do bazy danych master.  Aby uzyskać [więcej informacji,](sql-data-warehouse-overview-manage-security.md) zobacz artykuł Omówienie zabezpieczeń. |
| Błąd CTAIP                                                  | Ten błąd może wystąpić, gdy utworzono identyfikator logowania w bazie SQL Database master, ale nie w określonej bazie danych SQL.  Jeśli wystąpi ten błąd, zobacz artykuł [Omówienie](sql-data-warehouse-overview-manage-security.md) zabezpieczeń.  W tym artykule wyjaśniono, jak utworzyć identyfikator logowania i użytkownika w bazie danych master, a następnie jak utworzyć użytkownika w bazie danych SQL. |
| Zablokowany przez zaporę                                          | Dedykowana pula SQL (wcześniej SQL DW) jest chroniona przez zapory w celu zapewnienia, że tylko znane adresy IP mają dostęp do bazy danych. Zapory są domyślnie bezpieczne, co oznacza, że należy jawnie włączyć adresy IP lub zakresy adresów, zanim będzie można nawiązać połączenie.  Aby skonfigurować zaporę do uzyskiwania dostępu, wykonaj kroki opisane w te [tematze Configure server firewall access for your client IP](create-data-warehouse-portal.md) (Konfigurowanie dostępu zapory serwera dla adresu IP klienta) w [instrukcjach aprowingu](create-data-warehouse-portal.md). |
| Nie można nawiązać połączenia za pomocą narzędzia lub sterownika                           | Dedykowana pula SQL (wcześniej SQL DW) zaleca używanie programu [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [SSDT Visual Studio](sql-data-warehouse-install-visual-studio.md)lub [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) do wykonywania zapytań o dane. Aby uzyskać więcej informacji na temat sterowników i nawiązywania połączenia z Azure Synapse, zobacz sterowniki dla Azure Synapse [i](sql-data-warehouse-connection-strings.md) [Nawiązywanie](sql-data-warehouse-connect-overview.md) połączenia z Azure Synapse artykułami. |

## <a name="tools"></a>Narzędzia

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio obiektu nie ma użytkowników usługi Azure AD           | Jest to znany problem.  Aby obejść ten problem, wyświetl użytkowników w [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Zobacz [Authentication to Azure Synapse (Uwierzytelnianie](sql-data-warehouse-authentication.md) w sieci), aby dowiedzieć się więcej na temat używania usługi Azure Active Directory z dedykowaną pulą SQL (dawniej SQL DW). |
| Ręczne wykonywanie skryptów, korzystanie z kreatora skryptów lub nawiązywanie połączenia za pośrednictwem programu SSMS jest powolne, nie odpowiada lub tworzy błędy | Upewnij się, że użytkownicy zostały utworzeni w bazie danych master. W opcjach skryptów upewnij się również, że wersja aparatu jest ustawiona jako "Microsoft Azure Synapse Analytics Edition", a typ aparatu to "Microsoft Azure SQL Database". |
| Generowanie skryptów kończy się niepowodzeniem w programie SSMS                               | Generowanie skryptu dla dedykowanej puli SQL (dawniej SQL DW) kończy się niepowodzeniem, jeśli opcja "Generuj skrypt dla obiektów zależnych" jest ustawiona na wartość "True". Aby obejść ten problem, użytkownicy muszą ręcznie przejść do opcji Narzędzia **-> ->Eksplorator obiektów SQL Server -> Wygenerować** skrypt dla opcji zależnych i ustawić wartość false |

## <a name="data-ingestion-and-preparation"></a>Wprowadzanie i przygotowywanie danych

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Eksportowanie pustych ciągów przy użyciu cetas spowoduje wartości NULL w plikach Parquet i ORC. Należy pamiętać, że jeśli eksportujesz puste ciągi z kolumn z ograniczeniami NOT NULL, wynikiem tej pracy będzie odrzucenie rekordów, a eksport może potencjalnie się nie powieść. | Usuń puste ciągi lub kolumnę, która powoduje niechęć, w instrukcji SELECT instrukcji CETAS. |
| Ładowanie wartości spoza zakresu 0–127 do kolumny tinyint dla formatu plików Parquet i ORC nie jest obsługiwane. | Określ większy typ danych dla kolumny docelowej.           |

## <a name="performance"></a>Wydajność

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Rozwiązywanie problemów z wydajnością zapytań                            | Jeśli próbujesz rozwiązać problem z konkretnym zapytaniem, zacznij od [tematu Nauka monitorowania zapytań.](sql-data-warehouse-manage-monitor.md#monitor-query-execution) |
| Problemy z obszarem bazy danych TempDB | Monitorowanie użycia miejsca w bazie danych [TempDB.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Typowe przyczyny zaoczętego działania obszaru bazy danych TempDB to:<br>— Za mało zasobów przydzielonych do zapytania, co powoduje rozlanie danych do bazy danych TempDB.  Zobacz [Zarządzanie obciążeniami](resource-classes-for-workload-management.md) <br>— Brakuje statystyk lub są one aktualne, co powoduje nadmierne przemieszczenia danych.  Aby uzyskać szczegółowe informacje [na](sql-data-warehouse-tables-statistics.md) temat tworzenia statystyk, zobacz Obsługa statystyk tabeli<br>— Miejsce bazy danych TempDB jest przydzielane na poziom usługi.  [Skalowanie dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) do wyższego ustawienia jednostek DWU przydziela więcej miejsca w bazie danych TempDB.|
| Niska wydajność zapytań i plany są często wynikiem brakujących statystyk | Najczęstszą przyczyną niskiej wydajności jest brak statystyk dotyczących tabel.  Zobacz [Obsługa statystyk tabel,](sql-data-warehouse-tables-statistics.md) aby uzyskać szczegółowe informacje na temat sposobu tworzenia statystyk i powodów, dla których mają one kluczowe znaczenie dla wydajności. |
| Niska współbieżność/zapytania w kolejce                             | Zrozumienie [zarządzania obciążeniami](resource-classes-for-workload-management.md) jest ważne, aby zrozumieć, jak równoważyć alokację pamięci ze współbieżnością. |
| Jak zaimplementować najlepsze rozwiązania                              | Najlepszym miejscem, aby dowiedzieć się, jak poprawić wydajność zapytań, jest artykuł z najlepszymi rozwiązaniami dla dedykowanej puli [SQL (dawniej SQL DW).](sql-data-warehouse-best-practices.md) |
| Jak poprawić wydajność za pomocą skalowania                      | Czasami rozwiązaniem zwiększającym wydajność jest po prostu dodanie większej mocy obliczeniowej do zapytań przez skalowanie dedykowanej puli [SQL (dawniej SQL DW).](sql-data-warehouse-manage-compute-overview.md) |
| Niska wydajność zapytań z powodu niskiej jakości indeksu     | Czasami zapytania mogą spowalniać z powodu [niskiej jakości indeksu magazynu kolumn.](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)  Zobacz ten artykuł, aby uzyskać więcej informacji i dowiedzieć się, jak [ponownie kompilować indeksy w celu poprawy jakości segmentu](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Zarządzanie systemem

| Problem                                                        | Rozwiązanie                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nie można wykonać operacji, ponieważ serwer przekroczy dozwolony limit przydziału jednostki transakcji bazy danych 45000. | Zmniejsz liczbę [jednostek DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) bazy danych, która próbujesz utworzyć, lub [zażądaj zwiększenia limitu przydziału.](sql-data-warehouse-get-started-create-support-ticket.md) |
| Badanie wykorzystania przestrzeni                              | Zobacz [Rozmiary tabel,](sql-data-warehouse-tables-overview.md#table-size-queries) aby zrozumieć wykorzystanie miejsca w systemie. |
| Pomoc w zarządzaniu tabelami                                    | Zobacz artykuł [Omówienie tabel,](sql-data-warehouse-tables-overview.md) aby uzyskać pomoc w zarządzaniu tabelami.  Ten artykuł zawiera również linki do bardziej szczegółowych tematów, takich jak typy danych [tabel,](sql-data-warehouse-tables-data-types.md) [dystrybucja](sql-data-warehouse-tables-distribute.md) [tabeli,](sql-data-warehouse-tables-index.md)indeksowanie [tabeli,](sql-data-warehouse-tables-partition.md)partycjonowanie [tabeli,](sql-data-warehouse-tables-statistics.md) obsługa statystyk tabel i [tabele tymczasowe.](sql-data-warehouse-tables-temporary.md) |
| Pasek postępu transparent data encryption (TDE) nie jest aktualizowany w Azure Portal | Stan TDE można wyświetlić za pomocą programu [PowerShell.](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) |

## <a name="differences-from-sql-database"></a>Różnice między SQL Database

| Problem                                 | Rozwiązanie                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nieobsługiwane SQL Database funkcji     | Zobacz [Nieobsługiwane funkcje tabeli](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nieobsługiwane typy SQL Database typów danych   | Zobacz [Nieobsługiwane typy danych.](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)        |
| Ograniczenia procedury składowanej          | Zobacz [Ograniczenia procedury składowanej,](sql-data-warehouse-develop-stored-procedures.md#limitations) aby zrozumieć niektóre ograniczenia procedur składowanych. |
| Instrukcje SELECT nie obsługują instrukcje SELECT | Jest to bieżące ograniczenie naszych UDF.  Zobacz [CREATE FUNCTION ,](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) aby uzyskać informacje o składni, która jest przez nas wspierana. |
| sp_rename (wersja zapoznawcza) dla kolumn nie działa w schematach poza *dbo* | Jest to bieżące ograniczenie dotyczące programu Synapse [sp_rename (wersja zapoznawcza) dla kolumn](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Nazwy kolumn w obiektach, które nie są częścią *schematu dbo,* można zmienić za pośrednictwem ctaS na nową tabelę. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej pomocy w znalezieniu rozwiązania problemu, poniżej znajdują się inne zasoby, które możesz wypróbować.

* [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
* [Pytania i odpowiedzi&Microsoft Q](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)