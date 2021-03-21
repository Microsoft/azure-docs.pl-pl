---
title: Parametry serwera — Azure Database for MariaDB
description: Ten temat zawiera wskazówki dotyczące konfigurowania parametrów serwera w Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7797ee9d20b33a25c1b51289036651c7ad9f22a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664150"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Parametry serwera w Azure Database for MariaDB

Ten artykuł zawiera zagadnienia i wskazówki dotyczące konfigurowania parametrów serwera w programie Azure Database for MariaDB.

## <a name="what-are-server-parameters"></a>Co to są parametry serwera? 

Aparat MariaDB udostępnia wiele różnych zmiennych/parametrów serwera, których można użyć do konfigurowania i dostrajania zachowania aparatu. Niektóre parametry można ustawić dynamicznie w czasie wykonywania, podczas gdy inne są "statyczne", co wymaga ponownego uruchomienia serwera, aby można było go zastosować.

Azure Database for MariaDB uwidacznia możliwość zmiany wartości różnych parametrów serwera MariaDB przy użyciu [Azure Portal](./howto-server-parameters.md), [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-cli.md)i [programu PowerShell](./howto-configure-server-parameters-using-powershell.md) w celu dopasowania do potrzeb związanych z obciążeniem.

## <a name="configurable-server-parameters"></a>Konfigurowalne parametry serwera

Lista obsługiwanych parametrów serwera stale rośnie. Użyj karty parametry serwera w Azure Portal, aby wyświetlić pełną listę i skonfigurować wartości parametrów serwera.

Zapoznaj się z poniższymi sekcjami poniżej, aby dowiedzieć się więcej o limitach kilku często aktualizowanych parametrów serwera. Limity są ustalane na podstawie warstwy cenowej i rdzeni wirtualnych serwera.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

W Azure Database for MariaDB dzienniki binarne są zawsze włączone (tj. `log_bin` jest ustawione na wartość włączone). Jeśli chcesz użyć wyzwalaczy, zostanie wyświetlony komunikat o błędzie podobny do tego, że *nie masz uprawnień administratora, a rejestrowanie binarne jest włączone (możesz chcieć użyć mniej bezpiecznej `log_bin_trust_function_creators` zmiennej)*.

Format rejestrowania binarnego to zawsze **wiersz** i wszystkie połączenia z serwerem **zawsze** używają rejestrowania binarnego opartego na wierszach. W przypadku rejestrowania binarnego opartego na wierszach problemy z zabezpieczeniami nie istnieją i rejestrowanie danych binarnych nie może zostać zerwane, więc można bezpiecznie ustawić [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) **wartość true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size) , aby dowiedzieć się więcej o tym parametrze.

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Serwery obsługujące do 4 TB magazynu

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|872415232|134217728|872415232|
|Podstawowa|2|2684354560|134217728|2684354560|
|Ogólnego przeznaczenia|2|3758096384|134217728|3758096384|
|Ogólnego przeznaczenia|4|8053063680|134217728|8053063680|
|Ogólnego przeznaczenia|8|16106127360|134217728|16106127360|
|Ogólnego przeznaczenia|16|32749125632|134217728|32749125632|
|Ogólnego przeznaczenia|32|66035122176|134217728|66035122176|
|Ogólnego przeznaczenia|64|132070244352|134217728|132070244352|
|Optymalizacja pod kątem pamięci|2|7516192768|134217728|7516192768|
|Optymalizacja pod kątem pamięci|4|16106127360|134217728|16106127360|
|Optymalizacja pod kątem pamięci|8|32212254720|134217728|32212254720|
|Optymalizacja pod kątem pamięci|16|65498251264|134217728|65498251264|
|Optymalizacja pod kątem pamięci|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>Serwery obsługują do 16 TB pamięci masowej

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|872415232|134217728|872415232|
|Podstawowa|2|2684354560|134217728|2684354560|
|Ogólnego przeznaczenia|2|7516192768|134217728|7516192768|
|Ogólnego przeznaczenia|4|16106127360|134217728|16106127360|
|Ogólnego przeznaczenia|8|32212254720|134217728|32212254720|
|Ogólnego przeznaczenia|16|65498251264|134217728|65498251264|
|Ogólnego przeznaczenia|32|132070244352|134217728|132070244352|
|Ogólnego przeznaczenia|64|264140488704|134217728|264140488704|
|Optymalizacja pod kątem pamięci|2|15032385536|134217728|15032385536|
|Optymalizacja pod kątem pamięci|4|32212254720|134217728|32212254720|
|Optymalizacja pod kątem pamięci|8|64424509440|134217728|64424509440|
|Optymalizacja pod kątem pamięci|16|130996502528|134217728|130996502528|
|Optymalizacja pod kątem pamięci|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` można ją zaktualizować tylko w warstwach cenowych Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

MariaDB przechowuje tabelę InnoDB w różnych obszarach tabel na podstawie konfiguracji podanej podczas tworzenia tabeli. Przestrzeń dyskowa [systemu](https://mariadb.com/kb/en/innodb-system-tablespaces/) jest obszarem magazynu dla słownika danych InnoDB. Obszar tabel dla [poszczególnych tabel](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) zawiera dane i indeksy dla pojedynczej tabeli InnoDB i jest przechowywany w systemie plików w osobnym pliku danych. Takie zachowanie jest kontrolowane przez `innodb_file_per_table` parametr serwer. Ustawienie `innodb_file_per_table` `OFF` powoduje, że InnoDB utworzyć tabele w obszarze obszary obszaru systemu. W przeciwnym razie InnoDB tworzy tabele w tabelach tabel w tabeli.

Azure Database for MariaDB obsługuje o największych **1 TB** w jednym pliku danych. Jeśli rozmiar bazy danych jest większy niż 1 TB, należy utworzyć tabelę w [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) obszar tabel. Jeśli rozmiar pojedynczej tabeli jest większy niż 1 TB, należy użyć tabeli partycji.

### <a name="join_buffer_size"></a>join_buffer_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Ogólnego przeznaczenia|2|262144|128|268435455|
|Ogólnego przeznaczenia|4|262144|128|536870912|
|Ogólnego przeznaczenia|8|262144|128|1073741824|
|Ogólnego przeznaczenia|16|262144|128|2147483648|
|Ogólnego przeznaczenia|32|262144|128|4294967295|
|Ogólnego przeznaczenia|64|262144|128|4294967295|
|Optymalizacja pod kątem pamięci|2|262144|128|536870912|
|Optymalizacja pod kątem pamięci|4|262144|128|1073741824|
|Optymalizacja pod kątem pamięci|8|262144|128|2147483648|
|Optymalizacja pod kątem pamięci|16|262144|128|4294967295|
|Optymalizacja pod kątem pamięci|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowa|1|50|10|50|
|Podstawowa|2|100|10|100|
|Ogólnego przeznaczenia|2|300|10|600|
|Ogólnego przeznaczenia|4|625|10|1250|
|Ogólnego przeznaczenia|8|1250|10|2500|
|Ogólnego przeznaczenia|16|2500|10|5000|
|Ogólnego przeznaczenia|32|5000|10|10 000|
|Ogólnego przeznaczenia|64|10 000|10|20000|
|Optymalizacja pod kątem pamięci|2|625|10|1250|
|Optymalizacja pod kątem pamięci|4|1250|10|2500|
|Optymalizacja pod kątem pamięci|8|2500|10|5000|
|Optymalizacja pod kątem pamięci|16|5000|10|10 000|
|Optymalizacja pod kątem pamięci|32|10 000|10|20000|

Gdy połączenia przekroczą limit, może zostać wyświetlony następujący błąd:
> BŁĄD 1040 (08004): zbyt wiele połączeń

> [!IMPORTANT]
> W celu uzyskania najlepszego środowiska zalecamy użycie połączenia pulę, takiego jak ProxySQL, aby efektywnie zarządzać połączeniami.

Tworzenie nowych połączeń klientów do MariaDB trwa po upływie czasu i po jego ustanowieniu te połączenia zajmują zasoby bazy danych, nawet jeśli są bezczynne. Większość aplikacji żąda wielu krótkich połączeń, które są związane z tą sytuacją. Wynikiem jest mniej zasobów dostępnych dla rzeczywistego obciążenia, co prowadzi do zmniejszenia wydajności. Pulę połączenia, który zmniejsza bezczynne połączenia i ponownie używa istniejących połączeń, będzie pomóc w uniknięciu tego działania. Aby dowiedzieć się więcej o konfigurowaniu ProxySQL, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

>[!Note]
>ProxySQL to narzędzie społecznościowe Open Source. Jest ona obsługiwana przez firmę Microsoft w oparciu o najlepszy nakład pracy. Aby uzyskać pomoc techniczną z wiarygodnymi wskazówkami, możesz oszacować i skontaktować się z [pomocą techniczną produktu ProxySQL](https://proxysql.com/services/support/).

### <a name="max_heap_table_size"></a>max_heap_table_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Ogólnego przeznaczenia|2|16777216|16384|268435455|
|Ogólnego przeznaczenia|4|16777216|16384|536870912|
|Ogólnego przeznaczenia|8|16777216|16384|1073741824|
|Ogólnego przeznaczenia|16|16777216|16384|2147483648|
|Ogólnego przeznaczenia|32|16777216|16384|4294967295|
|Ogólnego przeznaczenia|64|16777216|16384|4294967295|
|Optymalizacja pod kątem pamięci|2|16777216|16384|536870912|
|Optymalizacja pod kątem pamięci|4|16777216|16384|1073741824|
|Optymalizacja pod kątem pamięci|8|16777216|16384|2147483648|
|Optymalizacja pod kątem pamięci|16|16777216|16384|4294967295|
|Optymalizacja pod kątem pamięci|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

Pamięć podręczna zapytań jest domyślnie włączona w MariaDB z `have_query_cache` parametrem. 

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Ogólnego przeznaczenia|2|0|0|16777216|
|Ogólnego przeznaczenia|4|0|0|33554432|
|Ogólnego przeznaczenia|8|0|0|67108864|
|Ogólnego przeznaczenia|16|0|0|134217728|
|Ogólnego przeznaczenia|32|0|0|134217728|
|Ogólnego przeznaczenia|64|0|0|134217728|
|Optymalizacja pod kątem pamięci|2|0|0|33554432|
|Optymalizacja pod kątem pamięci|4|0|0|67108864|
|Optymalizacja pod kątem pamięci|8|0|0|134217728|
|Optymalizacja pod kątem pamięci|16|0|0|134217728|
|Optymalizacja pod kątem pamięci|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Ogólnego przeznaczenia|2|524288|32768|4194304|
|Ogólnego przeznaczenia|4|524288|32768|8388608|
|Ogólnego przeznaczenia|8|524288|32768|16777216|
|Ogólnego przeznaczenia|16|524288|32768|33554432|
|Ogólnego przeznaczenia|32|524288|32768|33554432|
|Ogólnego przeznaczenia|64|524288|32768|33554432|
|Optymalizacja pod kątem pamięci|2|524288|32768|8388608|
|Optymalizacja pod kątem pamięci|4|524288|32768|16777216|
|Optymalizacja pod kątem pamięci|8|524288|32768|33554432|
|Optymalizacja pod kątem pamięci|16|524288|32768|33554432|
|Optymalizacja pod kątem pamięci|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna (w bajtach)**|**Wartość minimalna (w bajtach)**|**Maksymalna wartość (w bajtach)**|
|---|---|---|---|---|
|Podstawowa|1|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Podstawowa|2|Nie można skonfigurować w warstwie Podstawowa|NIE DOTYCZY|NIE DOTYCZY|
|Ogólnego przeznaczenia|2|16777216|1024|67108864|
|Ogólnego przeznaczenia|4|16777216|1024|134217728|
|Ogólnego przeznaczenia|8|16777216|1024|268435456|
|Ogólnego przeznaczenia|16|16777216|1024|536870912|
|Ogólnego przeznaczenia|32|16777216|1024|1073741824|
|Ogólnego przeznaczenia|64|16777216|1024|1073741824|
|Optymalizacja pod kątem pamięci|2|16777216|1024|134217728|
|Optymalizacja pod kątem pamięci|4|16777216|1024|268435456|
|Optymalizacja pod kątem pamięci|8|16777216|1024|536870912|
|Optymalizacja pod kątem pamięci|16|16777216|1024|1073741824|
|Optymalizacja pod kątem pamięci|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Po wstępnym wdrożeniu serwer platformy Azure dla MariaDB zawiera tabele systemów dla informacji o strefie czasowej, ale te tabele nie są wypełnione. Tabele strefy czasowej mogą być wypełniane przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Zapoznaj się z artykułami [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) lub [interfejsem wiersza polecenia platformy Azure](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) dotyczącymi sposobu wywoływania procedury składowanej i ustawiania stref czasowych na poziomie globalnym lub w sesji.

## <a name="non-configurable-server-parameters"></a>Parametry serwera, które nie zostały konfigurowalne

Następujące parametry serwera nie są konfigurowalne w usłudze:

|**Parametr**|**Stała wartość**|
| :------------------------ | :-------- |
|innodb_file_per_table w warstwie Podstawowa|WYŁ.|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Inne parametry serwera, które nie są wymienione w tym miejscu, są ustawione na wartości domyślne MariaDB dla [MariaDB](https://mariadb.com/kb/en/server-system-variables/).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak skonfigurować parametry serwera przy użyciu Azure Portal](./howto-server-parameters.md)
- Dowiedz się [, jak skonfigurować parametry serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-cli.md)
- Dowiedz się, jak [skonfigurować parametry serwera przy użyciu programu PowerShell](./howto-configure-server-parameters-using-powershell.md)
