---
title: Funkcje i możliwości usługi Azure ARC z włączonym wystąpieniem zarządzanym SQL
description: Funkcje i możliwości usługi Azure ARC z włączonym wystąpieniem zarządzanym SQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589223"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Funkcje i możliwości usługi Azure ARC z włączonym wystąpieniem zarządzanym SQL

Wystąpienie zarządzane SQL z włączonym użyciem usługi Azure Arc udostępnia wspólną bazę kodu z najnowszą stabilną wersją SQL Server. Większość standardowego języka SQL, przetwarzania zapytań i funkcji zarządzania bazami danych jest identyczna. Typowe funkcje między SQL Server i SQL Database lub wystąpieniem zarządzanym SQL:

- Funkcje języka — [Kontrola słów kluczowych języka Flow](/sql/t-sql/language-elements/control-of-flow), [kursorów](/sql/t-sql/language-elements/cursors-transact-sql), [typów danych](/sql/t-sql/data-types/data-types-transact-sql), [instrukcji DML](/sql/t-sql/queries/queries), [predykatów](/sql/t-sql/queries/predicates), [numerów sekwencyjnych](/sql/relational-databases/sequence-numbers/sequence-numbers), [procedur składowanych](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)i [zmiennych](/sql/t-sql/language-elements/variables-transact-sql).
- Funkcje bazy danych — [dostrajanie automatyczne (Wymuszanie planu)](/sql/relational-databases/automatic-tuning/automatic-tuning), [śledzenie zmian](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [sortowanie bazy danych](/sql/relational-databases/collations/set-or-change-the-database-collation), [zawarte bazy danych](/sql/relational-databases/databases/contained-databases), [zawarte użytkownicy](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [kompresja danych](/sql/relational-databases/data-compression/data-compression), [Ustawienia konfiguracji bazy danych](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [operacje indeksowania w trybie online](/sql/relational-databases/indexes/perform-index-operations-online), [partycjonowanie](/sql/relational-databases/partitions/partitioned-tables-and-indexes)i [tabele](/sql/relational-databases/tables/temporal-tables) danych czasowych ([Zobacz przewodnik wprowadzający](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Funkcje zabezpieczeń — [role aplikacji](/sql/relational-databases/security/authentication-access/application-roles), [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ([Rozpoczynanie pracy z SQL Database dynamiczną maskowanie danych przy użyciu Azure Portal](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security)
- Możliwości wielomodelowe — [Przetwarzanie grafów](/sql/relational-databases/graphs/sql-graph-overview), [dane JSON](/sql/relational-databases/json/json-data-sql-server), indeksy [OPENXML](/sql/t-sql/functions/openxml-transact-sql), [przestrzenne](/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)i [XML](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Funkcje usługi Azure ARC z włączonym wystąpieniem zarządzanym SQL

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS wysoka dostępność  
  
|Cecha|Usługa SQL Managed Instance z obsługą usługi Azure Arc|
|-------------|----------------|
|Wysyłanie dziennika|Tak| 
|Kompresja kopii zapasowej|Tak|
|Migawka bazy danych|Tak|
|Zawsze włączone wystąpienie klastra trybu failover<sup>1</sup>| Nie dotyczy. Podobne funkcje są dostępne |
|Zawsze włączone grupy dostępności<sup>2</sup>|Zaplanowano możliwości korzystania z dużej dostępności.|
|Podstawowe grupy dostępności <sup>2</sup>|Zaplanowano możliwości korzystania z dużej dostępności.|
|Minimalna replika — zatwierdzenie grupy dostępności <sup>2</sup>|Zaplanowano możliwości korzystania z dużej dostępności.|
|Bezklastrowa grupa dostępności|Tak|
|Przywracanie stron i plików online|Tak|
|Indeksowanie online|Tak|
|Możliwością wznowienia ponowne kompilacje indeksu online|Tak|
|Zmiana schematu w trybie online|Tak|
|Szybkie odzyskiwanie|Tak|
|Dublowane kopie zapasowe|Tak|
|Gorące Dodawanie pamięci i procesora CPU|Tak|
|Zaszyfrowana kopia zapasowa|Tak|
|Hybrydowa kopia zapasowa na platformie Azure (Tworzenie kopii zapasowej do adresu URL|Tak|

<sup>1</sup> w scenariuszu, w którym wystąpił błąd, nowe wystąpienie zarządzane SQL zostanie uruchomione i ponownie dołączone do woluminu trwałego zawierającego dane. [Więcej informacji na temat Kubernetesych woluminów trwałych znajdziesz tutaj](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> przyszłe wersje zapewniają funkcje w technologii AG 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS skalowalność i wydajność  

| Cecha | Usługa SQL Managed Instance z obsługą usługi Azure Arc |
|--|--|
| Klastrowany | Tak |
| Duże wartości binarne obiektów w klastrowanych indeksach magazynu kolumn | Tak |
| Odbudowywanie nieklastrowanego indeksu magazynu kolumn w trybie online | Tak |
| Przetwarzanie OLTP w pamięci | Tak |
| Trwała pamięć główna | Tak |
| Partycjonowanie tabel i indeksów | Tak |
| Kompresja danych | Tak |
| Zarządca zasobów | Tak |
| Równoległość tabeli partycjonowanej | Tak |
| Pamięć RAM z obsługą architektury NUMA i alokacja tablicy buforowej | Tak |
| Zarządzanie zasobami we/wy | Tak |
| Opóźniona trwałość | Tak |
| Dostrajanie automatyczne | Tak |
| Adaptacyjne sprzężenia w trybie wsadowym | Tak |
| Opinie dotyczące przydziału pamięci w trybie wsadowym | Tak |
| Wykonywanie z przeplotem dla funkcji z wartościami przechowywanymi w tabeli z obsługą instrukcji wieloskładnikowej | Tak |
| Udoskonalenia wstawiania zbiorczego | Tak |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> Zabezpieczenia RDBMS

| Cecha | Usługa SQL Managed Instance z obsługą usługi Azure Arc |
|--|--|
| Zabezpieczenia na poziomie wiersza | Tak |
| Funkcja Always Encrypted | Tak |
| Magazyny Always Encrypted z bezpiecznymi enklawami | Nie |
| Dynamiczne maskowanie danych | Tak |
| Podstawowa Inspekcja | Tak |
| Szczegółowe inspekcje | Tak |
| Przezroczyste szyfrowanie bazy danych | Tak |
| Role zdefiniowane przez użytkownika | Tak |
| Zawarte bazy danych | Tak |
| Szyfrowanie kopii zapasowych | Tak |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> Łatwość zarządzania RDBMS  

| Cecha | Usługa SQL Managed Instance z obsługą usługi Azure Arc |
|--|--|
| Dedykowane połączenie administratora | Tak |
| Obsługa skryptów programu PowerShell | Tak |
| Obsługa operacji składników aplikacji warstwy danych — wyodrębnianie, wdrażanie, uaktualnianie, usuwanie | Tak |
| Automatyzacja zasad (sprawdzanie harmonogramu i zmiana) | Tak |
| Moduł zbierający dane wydajności | Tak |
| Standardowe raporty wydajności | Tak |
| Przewodniki planu i zamrożenie planu dla przewodników planu | Tak |
| Bezpośrednie zapytanie o indeksowane widoki (przy użyciu wskazówki NOEXPAND) | Tak |
| Automatyczna konserwacja widoków indeksowanych | Tak |
| Rozproszone widoki partycjonowane | Tak |
| Równoległe operacje indeksowane | Tak |
| Automatyczne korzystanie z widoku indeksowanego przez optymalizator zapytań | Tak |
| Równoległe sprawdzanie spójności | Tak |

### <a name="programmability"></a><a name="Programmability"></a> Programowania  

| Cecha | Usługa SQL Managed Instance z obsługą usługi Azure Arc |
|--|--|
| JSON | Tak |
| Magazyn zapytań | Tak |  |
| Danych czasowych | Tak |  |
| Natywna obsługa kodu XML | Tak |  |
| Indeksowanie XML | Tak |  |
| Scal & możliwości UPSERT | Tak |  |
| Typy danych daty i godziny | Tak |  |
| Obsługa funkcji wielojęzycznych | Tak |  |
| Wyszukiwanie pełnotekstowe i semantyczne | Nie |
| Specyfikacja języka w kwerendzie | Tak |  |
| Service Broker (obsługa komunikatów) | Tak |  |
| Punkty końcowe języka Transact-SQL | Tak |  |
| Graph | Tak |  |
| Machine Learning Services | Nie |  |
| PolyBase | Nie |


### <a name="tools"></a>Narzędzia

Usługa Azure ARC z włączonym wystąpieniem zarządzanym SQL obsługuje różne narzędzia danych, które mogą pomóc w zarządzaniu danymi.

| **Narzędzie** | Usługa SQL Managed Instance z obsługą usługi Azure Arc|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | Nie |
| Interfejs wiersza polecenia platformy Azure | Nie |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Tak |
| Azure PowerShell | Tak |
| [Plik BACPAC (eksportowanie)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Tak |
| [Plik BACPAC (importowanie)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Tak |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Tak |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Tak |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Tak |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Tak |

<sup>1</sup> Azure Portal służy tylko do wyświetlania wystąpień zarządzanych przez usługę Azure Arc w trybie tylko do odczytu w trakcie okresu zapoznawczego.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Nieobsługiwane funkcje & usług

Następujące funkcje i usługi nie są dostępne dla wystąpienia zarządzanego SQL z obsługą usługi Azure Arc. Obsługa tych funkcji będzie coraz częściej włączona.

| Warstwowy | Nieobsługiwana funkcja lub usługa |
|-----|-----|
| **Aparat bazy danych** | Replikacja scalająca |
| &nbsp; | Rozciągana baza danych |
| &nbsp; | Zapytanie rozproszone z połączeniami innych firm |
| &nbsp; | Połączone serwery ze źródłami danych innymi niż SQL Server i produktami SQL platformy Azure |
| &nbsp; | Rozszerzone procedury składowane systemu (XP_CMDSHELL itd.) |
| &nbsp; | FileTable, FILESTREAM |
| &nbsp; | Zestawy CLR z EXTERNAL_ACCESS lub niebezpiecznym zestawem uprawnień |
| &nbsp; | Rozszerzenie puli buforów |
| **Program SQL Server Agent** |  Podsystemy: CmdExec, PowerShell, Reader do kolejki, SSIS, SSAS, SSRS |
| &nbsp; | Alerty |
| &nbsp; | Tworzenie zarządzanej kopii zapasowej |
| **Wysoka dostępność** | Dublowanie bazy danych  |
| **Bezpieczeństwo** | Rozszerzalne zarządzanie kluczami |
| &nbsp; | Uwierzytelnianie usługi AD dla połączonych serwerów | 
| &nbsp; | Uwierzytelnianie usługi AD dla grup dostępności (AGs) | 