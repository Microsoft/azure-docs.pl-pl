---
title: Ograniczenia — Azure Database for MariaDB
description: W tym artykule opisano ograniczenia w Azure Database for MariaDB, takie jak liczba opcji połączenia i aparatu magazynu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/5/2020
ms.openlocfilehash: c99851125552873e63f8199bf7b206c5fad231be
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484736"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Ograniczenia w Azure Database for MariaDB
W poniższych sekcjach opisano pojemność, obsługę aparatu magazynu, obsługę uprawnień, obsługę instrukcji manipulowania danymi oraz limity funkcjonalne w usłudze bazy danych.

## <a name="server-parameters"></a>Parametry serwera

Minimalne i maksymalne wartości kilku popularnych parametrów serwera są określane przez warstwę cenową i rdzeni wirtualnych. Limity można znaleźć w poniższych tabelach.

### <a name="max_connections"></a>max_connections

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|50|10|50|
|Podstawowy|2|100|10|100|
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

### <a name="query_cache_size"></a>query_cache_size

Pamięć podręczna zapytań jest domyślnie wyłączona. Aby włączyć pamięć podręczną zapytań, należy skonfigurować `query_cache_type` parametr. 

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
|Podstawowy|2|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
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

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
|Podstawowy|2|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
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

### <a name="join_buffer_size"></a>join_buffer_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
|Podstawowy|2|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
|Podstawowy|2|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
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

### <a name="tmp_table_size"></a>tmp_table_size

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) , aby dowiedzieć się więcej o tym parametrze.

|**Warstwa cenowa**|**Rdzeń wirtualny**|**Wartość domyślna**|**Wartość minimalna**|**Wartość maksymalna**|
|---|---|---|---|---|
|Podstawowy|1|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
|Podstawowy|2|Nie można skonfigurować w warstwie Podstawowa|Brak|Brak|
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

Tabele strefy czasowej mogą być wypełniane przez wywołanie `mysql.az_load_timezone` procedury składowanej z narzędzia, takiego jak wiersz polecenia MySQL lub MySQL Workbench. Zapoznaj się z artykułami [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) lub [interfejsem wiersza polecenia platformy Azure](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) dotyczącymi sposobu wywoływania procedury składowanej i ustawiania stref czasowych na poziomie globalnym lub w sesji.

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MariaDB przechowuje tabelę InnoDB w różnych obszarach tabel na podstawie konfiguracji podanej podczas tworzenia tabeli. Przestrzeń dyskowa [systemu](https://mariadb.com/kb/en/innodb-system-tablespaces/) jest obszarem magazynu dla słownika danych InnoDB. Obszar tabel dla [poszczególnych tabel](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) zawiera dane i indeksy dla pojedynczej tabeli InnoDB i jest przechowywany w systemie plików w osobnym pliku danych. Takie zachowanie jest kontrolowane przez `innodb_file_per_table` parametr serwer. Ustawienie `innodb_file_per_table` `OFF` powoduje, że InnoDB utworzyć tabele w obszarze obszary obszaru systemu. W przeciwnym razie InnoDB tworzy tabele w tabelach tabel w tabeli.

Azure Database for MariaDB obsługuje o największych **1 TB**w jednym pliku danych. Jeśli rozmiar bazy danych jest większy niż 1 TB, należy utworzyć tabelę w [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) obszar tabel. Jeśli rozmiar pojedynczej tabeli jest większy niż 1 TB, należy użyć tabeli partycji.

## <a name="storage-engine-support"></a>Obsługa aparatu magazynu

### <a name="supported"></a>Obsługiwane
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [ROZMIAR](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nieobsługiwane
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [FOLDERU](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Obsługa uprawnień

### <a name="unsupported"></a>Nieobsługiwane
- Rola DBA: wiele parametrów serwera i ustawień może przypadkowo obniżyć wydajność serwera lub Negate właściwości KWASów systemu DBMS. W związku z tym, aby zachować integralność usługi i umowę SLA na poziomie produktu, ta usługa nie ujawnia roli DBA. Domyślne konto użytkownika, które jest konstruowane podczas tworzenia nowego wystąpienia bazy danych, umożliwia temu użytkownikowi wykonywanie większości instrukcji języka DDL i DML w zarządzanym wystąpieniu bazy danych.
- Uprawnienie "noprivileged": podobne [nieuprzywilejowane](https://mariadb.com/kb/en/library/grant/#global-privileges) również jest ograniczone.
- Zdefiniuj: wymagane są uprawnienia administratora do tworzenia i jest ograniczone. W przypadku importowania danych przy użyciu kopii zapasowej Usuń `CREATE DEFINER` polecenia ręcznie lub przy użyciu `--skip-definer` polecenia podczas wykonywania mysqldump.

## <a name="data-manipulation-statement-support"></a>Obsługa instrukcji manipulowania danymi

### <a name="supported"></a>Obsługiwane
- `LOAD DATA INFILE`jest obsługiwane, ale `[LOCAL]` parametr musi być określony i skierowany do ścieżki UNC (magazyn platformy Azure zainstalowany za pomocą protokołu SMB).

### <a name="unsupported"></a>Nieobsługiwane
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

### <a name="scale-operations"></a>Operacje skalowania
- Dynamiczne skalowanie do i z podstawowych warstw cenowych nie jest obecnie obsługiwane.
- Zmniejszenie rozmiaru magazynu serwera nie jest obsługiwane.

### <a name="server-version-upgrades"></a>Uaktualnienia wersji serwera
- Automatyczna Migracja między wersjami aparatu głównej bazy danych nie jest obecnie obsługiwana.

### <a name="point-in-time-restore"></a>Przywracanie do punktu w czasie
- W przypadku korzystania z funkcji kopie nowy serwer jest tworzony z tymi samymi konfiguracjami co serwer, na którym jest oparta.
- Przywracanie usuniętego serwera nie jest obsługiwane.

### <a name="subscription-management"></a>Zarządzanie subskrypcjami
- Dynamiczne przeniesienie wstępnie utworzonych serwerów między subskrypcją i grupą zasobów nie jest obecnie obsługiwane.

### <a name="vnet-service-endpoints"></a>Punkty końcowe usługi sieci wirtualnej
- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

### <a name="storage-size"></a>Rozmiar magazynu
- Zapoznaj się z [warstwami cenowymi](concepts-pricing-tiers.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="current-known-issues"></a>Bieżące znane problemy
- Wystąpienie serwera MariaDB wyświetla nieprawidłową wersję serwera po nawiązaniu połączenia. Aby uzyskać poprawną wersję aparatu wystąpienia serwera, użyj `select version();` polecenia.

## <a name="next-steps"></a>Następne kroki
- [Co jest dostępne w poszczególnych warstwach usług](concepts-pricing-tiers.md)
- [Obsługiwane wersje bazy danych MariaDB](concepts-supported-versions.md)
