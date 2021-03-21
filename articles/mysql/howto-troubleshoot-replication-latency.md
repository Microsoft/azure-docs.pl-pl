---
title: Rozwiązywanie problemów z opóźnieniem replikacji — Azure Database for MySQL
description: Dowiedz się, jak rozwiązywać problemy z opóźnieniem replikacji przy użyciu Azure Database for MySQL odczytu replik.
keywords: Baza danych MySQL, rozwiązywanie problemów, opóźnienie replikacji (w sekundach)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 01/13/2021
ms.openlocfilehash: 92513a8c24b5106e3a59c8cfa4d743e900b957bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249775"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Rozwiązywanie problemów z opóźnieniami replikacji w usłudze Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

Funkcja [Read Replica](concepts-read-replicas.md) umożliwia replikowanie danych z serwera Azure Database for MySQL do serwera repliki tylko do odczytu. Obciążenia można skalować w poziomie przez kierowanie zapytań odczytu i raportowania z aplikacji na serwery repliki. Ta konfiguracja zmniejsza nacisk na serwer źródłowy. Zwiększa również ogólną wydajność i opóźnienie aplikacji w miarę skalowania.

Repliki są aktualizowane asynchronicznie przy użyciu natywnej lokalizacji pliku dziennika binarnego (binlog) aparatu programu MySQL. Aby uzyskać więcej informacji, zobacz [Konfiguracja replikacji oparta na pozycjach plików MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Opóźnienie replikacji w odniesieniu do pomocniczych replik odczytu zależy od kilku czynników. Czynniki te obejmują, ale nie są ograniczone do:

- Opóźnienie sieci.
- Wolumin transakcji na serwerze źródłowym.
- Warstwa obliczeniowa serwera źródłowego i serwera repliki pomocniczej.
- Zapytania uruchomione na serwerze źródłowym i pomocniczym.

W tym artykule dowiesz się, jak rozwiązywać problemy z opóźnieniem replikacji w Azure Database for MySQL. Poznasz również typowe przyczyny zwiększonego opóźnienia replikacji na serwerach repliki.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku _podrzędnego_, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.
>

## <a name="replication-concepts"></a>Pojęcia związane z replikacją

Po włączeniu dziennika binarnego serwer źródłowy zapisuje zatwierdzone transakcje do dziennika binarnego. Dziennik binarny jest używany na potrzeby replikacji. Jest on domyślnie włączony dla wszystkich nowo zainicjowanych serwerów, które obsługują do 16 TB pamięci masowej. Na serwerach replik dwa wątki są uruchamiane na każdym serwerze repliki. Jeden wątek jest *wątkiem we/wy*, a drugi jest *wątkiem SQL*:

- Wątek we/wy nawiązuje połączenie z serwerem źródłowym i żąda zaktualizowanych dzienników binarnych. Ten wątek otrzymuje aktualizacje dzienników binarnych. Aktualizacje te są zapisywane na serwerze repliki, w dzienniku lokalnym o nazwie *Dziennik przekaźnika*.
- Wątek SQL odczytuje dziennik przekaźnika, a następnie stosuje zmiany danych na serwerach repliki.

## <a name="monitoring-replication-latency"></a>Monitorowanie opóźnienia replikacji

Azure Database for MySQL zapewnia metrykę opóźnienia replikacji w sekundach w [Azure monitor](concepts-monitoring.md). Ta Metryka jest dostępna tylko na serwerach repliki odczytu. Jest on obliczany przez metrykę seconds_behind_master, która jest dostępna w bazie danych MySQL. 

Aby zrozumieć przyczynę zwiększonego opóźnienia replikacji, Połącz się z serwerem repliki przy użyciu programu [MySQL Workbench](connect-workbench.md) lub [Azure Cloud Shell](https://shell.azure.com). Następnie uruchom następujące polecenie.

>[!NOTE]
> W kodzie Zastąp przykładowe wartości Nazwa serwera repliki i nazwę użytkownika administratora. Nazwa użytkownika administratora wymaga `@\<servername>` Azure Database for MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Oto jak wygląda środowisko w terminalu Cloud Shell:

```
Requesting a Cloud Shell.Succeeded.
Connecting terminal...

Welcome to Azure Cloud Shell

Type "az" to use Azure CLI
Type "help" to learn about Cloud Shell

user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 64796
Server version: 5.6.42.0 Source distribution

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

W tym samym terminalu Cloud Shell Uruchom następujące polecenie:

```
mysql> SHOW SLAVE STATUS;
```

Oto typowe dane wyjściowe:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorowanie opóźnienia replikacji":::

Dane wyjściowe zawierają wiele informacji. Zwykle należy skoncentrować się na wierszach, które opisano w poniższej tabeli.

|Metric|Opis|
|---|---|
|Slave_IO_State| Reprezentuje bieżący stan wątku we/wy. Zwykle stanem jest "Oczekiwanie na wysłanie zdarzenia przez kapitana", jeśli serwer źródłowy (główny) jest synchronizowany. Stan, taki jak "łączenie z głównym" oznacza, że replika utraciła połączenie z serwerem źródłowym. Upewnij się, że serwer źródłowy jest uruchomiony, lub sprawdź, czy Zapora blokuje połączenie.|
|Master_Log_File| Reprezentuje binarny plik dziennika, do którego jest zapisywany serwer źródłowy.|
|Read_Master_Log_Pos| Wskazuje, gdzie serwer źródłowy zapisuje w pliku dziennika binarnego.|
|Relay_Master_Log_File| Reprezentuje binarny plik dziennika, który serwer repliki odczytuje z serwera źródłowego.|
|Slave_IO_Running| Wskazuje, czy wątek we/wy jest uruchomiony. Wartość powinna być równa `Yes` . Jeśli wartość to `NO` , replikacja prawdopodobnie zostanie przerwana.|
|Slave_SQL_Running| Wskazuje, czy wątek SQL jest uruchomiony. Wartość powinna być równa `Yes` . Jeśli wartość to `NO` , replikacja prawdopodobnie zostanie przerwana.|
|Exec_Master_Log_Pos| Wskazuje pozycję Relay_Master_Log_File stosowanej przez replikę. Jeśli wystąpi opóźnienie, ta sekwencja pozycji powinna być mniejsza niż Read_Master_Log_Pos.|
|Relay_Log_Space|Wskazuje górny limit rozmiaru dziennika przekaźnika. Możesz sprawdzić rozmiar, wykonując zapytania, `SHOW GLOBAL VARIABLES` tak jak `relay_log_space_limit` .|
|Seconds_Behind_Master| Wyświetla opóźnienie replikacji w sekundach.|
|Last_IO_Errno|Wyświetla kod błędu wątku we/wy (jeśli istnieje). Aby uzyskać więcej informacji na temat tych kodów, zobacz [Informacje o komunikacie o błędzie serwera MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Wyświetla komunikat o błędzie wątku we/wy (jeśli istnieje).|
|Last_SQL_Errno|Wyświetla kod błędu wątku SQL, jeśli istnieje. Aby uzyskać więcej informacji na temat tych kodów, zobacz [Informacje o komunikacie o błędzie serwera MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Wyświetla komunikat o błędzie wątku SQL, jeśli istnieje.|
|Slave_SQL_Running_State| Wskazuje bieżący stan wątku SQL. W tym stanie `System lock` jest normalne. Jest również normalne, aby wyświetlić stan `Waiting for dependent transaction to commit` . Ten stan wskazuje, że replika oczekuje na zaktualizowanie zatwierdzonych transakcji na serwerze źródłowym.|

Jeśli Slave_IO_Running jest `Yes` i Slave_SQL_Running jest `Yes` , replikacja działa prawidłowo. 

Następnie sprawdź Last_IO_Errno, Last_IO_Error, Last_SQL_Errno i Last_SQL_Error.  Te pola zawierają numer błędu i komunikat o błędzie ostatniego błędu, który spowodował zatrzymanie wątku SQL. Numer błędu `0` i pusty komunikat oznacza, że nie ma błędu. Zbadaj dowolną niezerową wartość błędu, sprawdzając kod błędu w [odwołaniu do komunikatu o błędzie serwera MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Typowe scenariusze dotyczące wysokiego czasu oczekiwania na replikację

W poniższych sekcjach opisano scenariusze, w których często trwa opóźnienie wysokiej replikacji.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Opóźnienie sieci lub wysokie użycie procesora CPU na serwerze źródłowym

Jeśli zobaczysz następujące wartości, opóźnienie replikacji prawdopodobnie jest spowodowane przez duże opóźnienie sieci lub wysokie użycie procesora na serwerze źródłowym.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

W takim przypadku wątek we/wy działa i oczekuje na serwerze źródłowym. Serwer źródłowy został już zapisany w binarnym pliku dziennika o numerze 20. Replika otrzymała tylko do pliku o numerze 10. Podstawowym czynnikiem opóźnienia replikacji w tym scenariuszu jest szybkość sieci lub wysokie użycie procesora na serwerze źródłowym.  

Na platformie Azure opóźnienie sieci w regionie może być zwykle mierzone w milisekundach. W różnych regionach, zakresy opóźnień z milisekund do sekund.

W większości przypadków opóźnienie połączenia między wątkami we/wy a serwerem źródłowym jest spowodowane wysokim wykorzystaniem procesora CPU na serwerze źródłowym. Wątki we/wy są przetwarzane powoli. Ten problem można wykryć przy użyciu Azure Monitor, aby sprawdzić użycie procesora i liczbę jednoczesnych połączeń na serwerze źródłowym.

Jeśli nie widzisz wysokiego użycia procesora na serwerze źródłowym, problem może być opóźnienia sieci. Jeśli opóźnienie sieci jest nagle nadmiernie wysokie, sprawdź na [stronie stanu platformy Azure](https://status.azure.com/status) znane problemy lub przestoje. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Duże rozerwania transakcji na serwerze źródłowym

Jeśli zostaną wyświetlone następujące wartości, duże obciążenie transakcji na serwerze źródłowym prawdopodobnie spowoduje opóźnienia replikacji. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Dane wyjściowe pokazują, że replika może pobrać dziennik binarny za serwerem źródłowym. Ale wątek we/wy repliki wskazuje, że miejsce w dzienniku przekaźnika jest już zapełnione.

Szybkość sieci nie powoduje opóźnienia. Replika próbuje wychwycić. Ale zaktualizowany rozmiar dziennika binarnego przekracza górny limit miejsca w dzienniku przekaźnika.

Aby rozwiązać ten problem, Włącz [Dziennik wolnych zapytań](concepts-server-logs.md) na serwerze źródłowym. Używaj dzienników wolnych zapytań, aby identyfikować długotrwałe transakcje na serwerze źródłowym. Następnie dostosuj zidentyfikowane zapytania, aby zmniejszyć opóźnienie na serwerze. 

Opóźnienie replikacji tego sortowania jest zwykle spowodowane obciążeniem danych na serwerze źródłowym. Gdy serwery źródłowe mają cotygodniowe lub comiesięczne ładunki danych, opóźnienie replikacji jest niemożliwe. Serwery repliki ostatecznie przechwytuje po zakończeniu ładowania danych na serwerze źródłowym.

### <a name="slowness-on-the-replica-server"></a>Spowolnienie na serwerze repliki

Jeśli zaobserwujesz poniższe wartości, problem może znajdować się na serwerze repliki.

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

W tym scenariuszu dane wyjściowe pokazują, że zarówno wątek we/wy, jak i wątek SQL działają prawidłowo. Replika odczytuje ten sam plik dziennika binarnego, który zapisuje na serwerze źródłowym. Jednak niektóre opóźnienia na serwerze repliki odzwierciedlają tę samą transakcję z serwera źródłowego.

W poniższych sekcjach opisano typowe przyczyny tego rodzaju opóźnienia.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Brak klucza podstawowego lub unikatowego klucza w tabeli

Azure Database for MySQL używa replikacji opartej na wierszach. Serwer źródłowy zapisuje zdarzenia w dzienniku binarnym, rejestrując zmiany w poszczególnych wierszach tabeli. Następnie wątek SQL replikuje te zmiany do odpowiednich wierszy tabeli na serwerze repliki. Gdy tabela nie ma klucza podstawowego lub klucza unikatowego, wątek SQL skanuje wszystkie wiersze w tabeli docelowej, aby zastosować zmiany. Takie skanowanie może spowodować opóźnienie replikacji.

W programie MySQL klucz podstawowy jest skojarzonym indeksem, który zapewnia szybką wydajność zapytań, ponieważ nie może zawierać wartości NULL. W przypadku korzystania z aparatu magazynu InnoDB dane tabeli są fizycznie zorganizowane w celu wykonywania szybkich wyszukiwań i sortowania na podstawie klucza podstawowego.

Przed utworzeniem serwera repliki zalecamy dodanie klucza podstawowego do tabel na serwerze źródłowym. Dodaj klucze podstawowe na serwerze źródłowym, a następnie utwórz ponownie repliki odczytu, aby ułatwić skrócenie opóźnienia replikacji.

Użyj następującego zapytania, aby dowiedzieć się, które tabele nie mają klucza podstawowego na serwerze źródłowym:

```sql
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="long-running-queries-on-the-replica-server"></a>Długotrwałe zapytania na serwerze repliki

Obciążenie na serwerze repliki może spowodować opóźnienie wątku SQL za wątkiem we/wy. Długotrwałe zapytania na serwerze repliki są jednym z typowych przyczyn opóźnienia replikacji. Aby rozwiązać ten problem, Włącz [Dziennik wolnych zapytań](concepts-server-logs.md) na serwerze repliki.

Wolne zapytania mogą zwiększyć zużycie zasobów lub spowolnić serwer, aby replika nie mogła przechwycić na serwer źródłowy. W tym scenariuszu Dostosuj powolne zapytania. Szybsze zapytania uniemożliwiają zatorem wątku SQL i znacząco skracają opóźnienia replikacji.

#### <a name="ddl-queries-on-the-source-server"></a>Zapytania DDL na serwerze źródłowym

Na serwerze źródłowym polecenie języka definicji danych (DDL) [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) może zająć dużo czasu. Gdy polecenie DDL jest uruchomione, na serwerze źródłowym mogą działać równolegle tysiące innych zapytań. 

Podczas replikowania DDL, aby zapewnić spójność bazy danych, aparat MySQL uruchamia kod DDL w pojedynczym wątku replikacji. Podczas tego zadania wszystkie inne zreplikowane zapytania są blokowane i muszą czekać, aż operacja DDL zostanie ukończona na serwerze repliki. Nawet operacje w trybie online DDL powodują to opóźnienie. Operacje DDL zwiększają opóźnienia replikacji.

Jeśli na serwerze źródłowym włączono [Dziennik wolnych zapytań](concepts-server-logs.md) , można wykryć ten problem opóźnienia, sprawdzając polecenie języka DDL uruchomione na serwerze źródłowym. Za pomocą indeksu porzucania, zmiany nazwy i tworzenia można użyć algorytmu InPlace dla instrukcji ALTER TABLE. Być może trzeba będzie skopiować dane tabeli i ponownie skompilować tabelę.

Zwykle współbieżne DML jest obsługiwane dla algorytmu UMIESZCZAnia. Jednak podczas przygotowywania i uruchamiania operacji można krótko wykonać blokadę na wyłączność metadanych w tabeli. Tak więc dla instrukcji CREATE INDEX można użyć algorytmu klauzul i zablokować wpływ na metodę kopiowania tabeli oraz poziom współbieżności do odczytu i zapisu. Nadal można zapobiegać operacjom DML przez dodanie indeksu PEŁNOTEKSTOWEGO lub indeksu PRZESTRZENnego.

Poniższy przykład tworzy indeks przy użyciu ALGORYTMów i blokad.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Niestety, w przypadku instrukcji języka DDL wymagającej blokady nie można uniknąć opóźnień replikacji. Aby zmniejszyć potencjalną liczbę efektów, należy wykonać te typy operacji DDL w godzinach poza szczytem, na przykład w porze nocnej.

#### <a name="downgraded-replica-server"></a>Obniżony serwer repliki

W Azure Database for MySQL Odczytaj repliki używają tej samej konfiguracji serwera co serwer źródłowy. Konfigurację serwera repliki można zmienić po jego utworzeniu.

W przypadku obniżenia poziomu serwera repliki obciążenie może zużywać więcej zasobów, co z kolei może prowadzić do opóźnień replikacji. Aby wykryć ten problem, użyj Azure Monitor, aby sprawdzić użycie procesora CPU i pamięci przez serwer repliki.

W tym scenariuszu zaleca się pozostawienie konfiguracji serwera repliki na wartości równe lub większe niż wartości serwera źródłowego. Ta konfiguracja umożliwia przeprowadzenie replikacji z serwerem źródłowym.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Zwiększanie opóźnień replikacji przez dostrajanie parametrów serwera źródłowego

W Azure Database for MySQL Domyślnie replikacja jest zoptymalizowana pod kątem uruchamiania równoległych wątków w replikach. W przypadku obciążeń o wysokim poziomie współbieżności na serwerze źródłowym może dojść do poprawienia opóźnienia replikacji przez skonfigurowanie parametru binlog_group_commit_sync_delay na serwerze źródłowym.

Parametr binlog_group_commit_sync_delay kontroluje, ile mikrosekund zatwierdzania dzienników binarnych czeka przed synchronizacją binarnego pliku dziennika. Zaletą tego parametru jest to, że zamiast natychmiastowego zastosowania każdej zatwierdzonej transakcji serwer źródłowy wysyła aktualizacje dzienników binarnych zbiorczo. To opóźnienie zmniejsza liczbę operacji we/wy na replice i pomaga zwiększyć wydajność.

Przydatne może być ustawienie parametru binlog_group_commit_sync_delay na 1000 lub tak. Następnie Monitoruj opóźnienie replikacji. Należy ostrożnie ustawiać ten parametr i używać go tylko w przypadku obciążeń o dużej współbieżności.

> [!IMPORTANT]
> W przypadku serwera repliki zaleca się, aby parametr binlog_group_commit_sync_delay był równy 0. Jest to zalecane, ponieważ w przeciwieństwie do serwera źródłowego serwer repliki nie będzie miał dużej współbieżności i zwiększenie wartości binlog_group_commit_sync_delay na serwerze repliki może przypadkowo spowodować opóźnienia replikacji.

W przypadku obciążeń o niskiej współbieżności, które obejmują wiele pojedynczych transakcji, ustawienie binlog_group_commit_sync_delay może zwiększyć opóźnienia. Opóźnienie może wzrosnąć, ponieważ wątek we/wy czeka na aktualizacje zbiorcze dzienników binarnych, nawet jeśli zatwierdzono tylko kilka transakcji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
