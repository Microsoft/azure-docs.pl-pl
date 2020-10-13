---
title: Rozwiązywanie problemów z opóźnieniem replikacji — Azure Database for MySQL
description: Dowiedz się, jak rozwiązywać problemy z opóźnieniem replikacji przy użyciu replik Azure Database for MySQL odczytu
keywords: Baza danych MySQL, rozwiązywanie problemów, opóźnienie replikacji (w sekundach)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877110"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Rozwiązywanie problemów z opóźnieniem replikacji w Azure Database for MySQL

Funkcja [Read Replica](concepts-read-replicas.md) umożliwia replikowanie danych z serwera Azure Database for MySQL do serwera repliki tylko do odczytu. Repliki odczytu są używane do skalowania obciążenia przez kierowanie zapytań odczytu/raportowania z aplikacji na serwery repliki. Zmniejsza to nacisk na serwer podstawowy i zwiększa ogólną wydajność i opóźnienie aplikacji w miarę skalowania. Repliki są aktualizowane asynchronicznie przy użyciu natywnej technologii replikacji aparatu programu MySQL opartej na pozycji w pliku dziennika binarnego (binlog). Aby dowiedzieć się więcej na temat replikacji binlog, zobacz [Omówienie replikacji MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

Opóźnienie replikacji w odniesieniu do pomocniczych replik odczytu zależy od liczby czynników, w tym między innymi 

- Opóźnienie sieci
- Wolumin transakcji na serwerze źródłowym
- Warstwa obliczeniowa serwera repliki źródłowej i pomocniczej
- Zapytania działające na serwerze podstawowym i pomocniczym. 

W tym dokumencie przedstawiono sposób rozwiązywania problemów z opóźnieniem replikacji w Azure Database for MySQL. Ponadto są również zrozumiałe niektóre typowe przyczyny zwiększonego opóźnienia replikacji na serwerach repliki.

## <a name="replication-concepts"></a>Pojęcia związane z replikacją

Po włączeniu rejestrowania binarnego serwer źródłowy zapisuje zatwierdzoną transakcję do dziennika binarnego, który jest używany na potrzeby replikacji. Dziennik binarny jest domyślnie włączony dla wszystkich nowo zainicjowanych serwerów, które obsługują do 16 TB magazynu. Na serwerach repliki dostępne są dwa wątki działające na serwer repliki, jeden nazywany wątkiem we/wy, a drugi nazywanym wątkiem SQL.

- **Wątek we/wy** nawiązuje połączenie z serwerem źródłowym i żąda zaktualizowanych dzienników binarnych. Po otrzymaniu przez ten wątek aktualizacji dzienników binarnych są one zapisywane na serwerze repliki w dzienniku lokalnym o nazwie dziennik przekaźnika.
- **Wątek SQL** odczytuje dziennik przekaźnika i stosuje zmiany danych na serwerach repliki.

## <a name="monitoring-replication-latency"></a>Monitorowanie opóźnienia replikacji

Azure Database for MySQL zapewnia wartość opóźnienia replikacji w sekundach w [Azure monitor](concepts-monitoring.md). Ta Metryka jest dostępna tylko na serwerach odczytu replik. Ta Metryka jest obliczana przy użyciu metryki seconds_behind_master dostępnej w programie MySQL. Aby zrozumieć główną przyczynę zwiększonego opóźnienia replikacji, Połącz się z serwerem repliki przy użyciu programu [MySQL Workbench](connect-workbench.md) lub [Azure Cloud Shell](https://shell.azure.com) i wykonaj następujące polecenie:

 Zamień wartości na rzeczywistą nazwę serwera repliki i nazwę logowania użytkownika administracyjnego. Nazwa użytkownika administratora wymaga znaku "@ \<servername> " dla Azure Database for MySQL:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Oto jak wygląda jak środowisko w terminalu Cloud Shell
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
  W tym samym terminalu Azure Cloud Shell wykonaj następujące polecenie

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Typowe dane wyjściowe będą wyglądać następująco:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitorowanie opóźnienia replikacji&quot;:::


Dane wyjściowe zawierają wiele informacji, ale zazwyczaj ważne jest, aby skoncentrować się na następujących kolumnach:

|Metryka|Opis|
|---|---|
|Slave_IO_State| Bieżący stan wątku we/wy. Zwykle stanem jest &quot;Oczekiwanie na wysłanie zdarzenia przez serwer główny&quot; w przypadku synchronizowania. Jeśli jednak zobaczysz stan, na przykład &quot;Nawiązywanie połączenia z serwerem głównym&quot;, replika utraciła połączenie z serwerem głównym. Sprawdź, czy serwer główny działa lub czy Zapora blokuje połączenie.|
|Master_Log_File| Plik dziennika binarnego, do którego jest zapisywany serwer główny.|
|Read_Master_Log_Pos| Reprezentuje pozycję w powyższym pliku dziennika binarnego, w którym zapisuje się dane główne.|
|Relay_Master_Log_File| Wskazany element reprezentuje binarny plik dziennika, który serwer repliki odczytuje z serwera głównego.|
|Slave_IO_Running| Wskazuje, czy wątek we/wy jest uruchomiony. Powinna to być wartość &quot;yes&quot; (tak). Jeśli wartość &quot;nie&quot;, najprawdopodobniej replikacja zostanie przerwana.|
|Slave_SQL_Running| Wskazuje, czy wątek SQL jest uruchomiony. Powinna to być wartość &quot;yes&quot; (tak). Jeśli wartość &quot;nie&quot;, najprawdopodobniej replikacja zostanie przerwana.|
|Exec_Master_Log_Pos| Wyświetla pozycję Relay_Master_Log_File stosowanym przez replikę. W przypadku opóźnienia ta sekwencja pozycji powinna być mniejsza niż Read_Master_Log_Pos.|
|Relay_Log_Space|Wyświetla górny limit rozmiaru dziennika przekazywania. Możesz sprawdzić rozmiar, wykonując zapytania o Pokaż zmienne globalne, takie jak &quot;relay_log_space_limit&quot;.|
|Seconds_Behind_Master| Wyświetla opóźnienie replikacji w sekundach.|
|Last_IO_Errno|Wyświetla kod błędu wątku we/wy (jeśli istnieje). Aby uzyskać więcej informacji na temat tych kodów, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_IO_Error| Wyświetla komunikat o błędzie wątku we/wy (jeśli istnieje).|
|Last_SQL_Errno|Wyświetla kod błędu wątku SQL, jeśli istnieje. Aby uzyskać więcej informacji na temat tych kodów, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Wyświetla komunikat o błędzie wątku SQL, jeśli istnieje.|
|Slave_SQL_Running_State| Wskazuje bieżący stan wątku SQL. Należy zauważyć, że &quot;blokada systemu" pokazana w tym stanie jest normalnym zachowaniem. Jest to normalne, aby zobaczyć stan jako "Oczekiwanie na zatwierdzenie transakcji zależnych". Wskazuje on, że replika oczekuje na zaktualizowanie zatwierdzonych transakcji przez serwer główny.|

Jeśli Slave_IO_Running ma wartość tak, a Slave_SQL_Running ma wartość tak, replikacja działa prawidłowo. 

Następnie należy sprawdzić Last_IO_Errno, Last_IO_Error, Last_SQL_Errno i Last_SQL_Error.  Te pola zawierają numer błędu i komunikat o błędzie ostatniego błędu, który spowodował zatrzymanie wątku SQL. Błąd o numerze 0 i pustej wiadomości oznacza brak błędu. W przypadku błędu należy dokładniej zbadać wartość różną od zera, wyszukując kod błędu w [dokumentacji programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Typowe scenariusze dotyczące wysokiego czasu oczekiwania na replikację

### <a name="network-latency-or-high-cpu-on-source-server"></a>Opóźnienie sieci lub wysoki procesor CPU na serwerze źródłowym

W przypadku przestrzegania następujących wartości najczęstsze przyczyny opóźnienia replikacji to duże opóźnienie sieci lub wysokie użycie procesora na serwerze źródłowym. W takim przypadku wątek we/wy działa i oczekuje na wzorcu. Główny (serwer źródłowy) został już zapisany w pliku dziennika binarnego #20, podczas gdy replika została odebrana tylko do #10 plików. Głównym czynnikiem wpływającym na duże opóźnienie replikacji w tym scenariuszu jest szybkość sieci lub wysokie użycie procesora na serwerze źródłowym.  Na platformie Azure opóźnienie sieci w regionie zwykle mieści się w milisekundach, a między regionami może przejść do kilku sekund. W większości przypadków opóźnienie w wątku we/wy w celu nawiązania połączenia z serwerem źródłowym jest spowodowane wysokim użyciem procesora CPU na serwerze źródłowym, powodującym spowolnienie przetwarzania wątku we/wy. Można to wykryć, monitorując wykorzystanie procesora CPU i obserwując liczbę współbieżnych połączeń na serwerze źródłowym przy użyciu usługi Azure monitor.

Jeśli na serwerze źródłowym nie jest widoczne wysokie wykorzystanie procesora CPU, możliwe przyczyny opóźnią się sieci. Jeśli zbyt gwałtownie wywidzisz wysokie opóźnienia sieci, zalecamy sprawdzenie [strony stanu platformy Azure](https://status.azure.com/status) , aby upewnić się, że występują nieznane problemy lub awarie. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Duże obciążenie transakcji na serwerze źródłowym

Jeśli zauważysz następujące wartości, najbardziej typową przyczyną opóźnienia replikacji jest duże obciążenie transakcji na serwerze źródłowym. W danych wyjściowych poniżej, chociaż replika może pobrać dziennik binarny za wzorcem, wątek we/wy repliki wskazuje, że miejsce w dzienniku przekaźnika jest już zapełnione. W związku z tym szybkość sieci nie powoduje opóźnienia, ponieważ replika została już poddana próbie przechwycenia. Zamiast tego zaktualizowany rozmiar dziennika binarnego przekracza górny limit miejsca w dzienniku przekaźnika. Aby rozwiązać ten problem, należy na serwerze głównym włączyć [wolny dziennik zapytań](concepts-server-logs.md) . Wolne dzienniki zapytań umożliwiają identyfikowanie długotrwałych transakcji na serwerze źródłowym. Zidentyfikowane zapytania muszą zostać dostrojone w celu zmniejszenia opóźnień na serwerze. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Poniżej przedstawiono typowe przyczyny opóźnienia w tej kategorii:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Opóźnienie replikacji z powodu obciążenia danych na serwerze źródłowym
W niektórych przypadkach trwa cotygodniowe lub comiesięczne ładowanie danych na serwerach źródłowych. Niestety, w tym przypadku opóźnienie replikacji nie jest możliwe. W tym scenariuszu serwery repliki ostatecznie przechwytuje po zakończeniu ładowania danych na serwerze źródłowym.


### <a name="slowness-on-the-replica-server"></a>Spowolnienie na serwerze repliki

Jeśli zauważysz następujące wartości, najbardziej typową przyczyną może być problem na serwerze repliki, który wymaga dalszych badań. W tym scenariuszu, jak widać w danych wyjściowych, oba wątki we/wy i SQL działają prawidłowo, a replika odczytuje ten sam binarny plik dziennika jako zapis główny. Jednak pewne opóźnienie występuje na serwerze repliki w celu odzwierciedlenia tej samej transakcji z serwera źródłowego. 

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

Poniżej przedstawiono typowe przyczyny opóźnienia w tej kategorii:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Brak podstawowego lub unikatowego klucza w tabeli

Azure Database for MySQL używa replikacji opartej na wierszach. W przypadku replikacji opartej na wierszach serwer główny zapisuje zdarzenia do dziennika binarnego o każdej zmianie wiersza tabeli. Wątek SQL w programie włącza te zmiany do odpowiednich wierszy tabeli na serwerze repliki. Żaden klucz podstawowy lub unikatowy w tabeli nie jest jednym z typowych przyczyn opóźnienia replikacji. Brak podstawowego lub unikatowego klucza prowadzi do skanowania wszystkich wierszy w tabeli docelowej przez wątek SQL w celu zastosowania zmian.

W programie MySQL klucz podstawowy jest skojarzonym indeksem, który zapewnia szybką wydajność zapytań, ponieważ nie może zawierać wartości NULL. W przypadku aparatu magazynu InnoDB dane tabeli są fizycznie zorganizowane w celu szybkiego wyszukiwania i sortowania na podstawie klucza podstawowego. W związku z tym przed utworzeniem serwera repliki zaleca się dodanie klucza podstawowego do tabel na serwerze źródłowym. W tym scenariuszu należy dodać klucze podstawowe na serwerze źródłowym i utworzyć replikę odczytu, aby ułatwić skrócenie opóźnienia replikacji.

Za pomocą następującego zapytania można określić, że na serwerze źródłowym brakuje tabel z kluczem podstawowym:

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

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Opóźnienie replikacji ze względu na długotrwałe wykonywanie zapytań na serwerze repliki

Istnieje możliwość, że obciążenie na serwerze repliki może uniemożliwić zachowanie wątku SQL w wątku we/wy. Jest to jeden z typowych przyczyn opóźnienia replikacji w przypadku długotrwałego wykonywania zapytań na serwerze repliki. W takim przypadku należy włączyć na serwerze repliki [wolny dziennik zapytań](concepts-server-logs.md) , aby pomóc w rozwiązywaniu problemu. Wolne zapytania mogą zwiększyć zużycie zasobów lub spowolnić serwer, dzięki czemu replika nie będzie mogła przechwycić danych do wzorca. W tym scenariuszu należy dostosować powolne zapytania. Szybsze zapytania uniemożliwiają blokowanie wątku SQL i znacząco skracają opóźnienia replikacji.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Opóźnienie replikacji z powodu zapytań DDL na serwerze źródłowym
Jeśli istnieje długotrwałe polecenie języka DDL, takie jak [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) wykonywane na serwerze źródłowym i Załóżmy, że wykonanie operacji zostało wykonane przez 1 godzinę. W tym czasie mogą istnieć tysiące innych zapytań działających równolegle na serwerze źródłowym. Gdy kod DDL zostanie zreplikowany do repliki, aby zapewnić spójność bazy danych, aparat MySQL musi uruchomić kod DDL w pojedynczym wątku replikacji. Wszystkie inne zreplikowane zapytania będą blokowane i będą musiały czekać na godzinę lub dłużej, dopóki operacja DDL nie zostanie ukończona na serwerze repliki. Ta wartość jest prawdziwa niezależnie od operacji w trybie online DDL. W przypadku operacji DDL oczekuje się, że replikacja powinna mieć większe opóźnienia replikacji.

Jeśli na serwerze źródłowym jest włączony [wolny dziennik zapytań](concepts-server-logs.md) , ten scenariusz można wykryć, przeglądając dzienniki wolnych zapytań, aby sprawdzić, czy polecenie języka DDL zostało wykonane na serwerze źródłowym. Mimo porzucania indeksu, zmiany nazwy i tworzenia powinny używać algorytmu UMIESZCZAnia dla instrukcji ALTER TABLE, może to oznaczać, że kopiowanie danych tabeli i ponowne skompilowanie tabeli. Typowo obsługiwane współbieżne DML dla algorytmu UMIESZCZAnia, ale wyłączna blokada metadanych w tabeli może być podejmowana krótko podczas fazy przygotowania i wykonania operacji. W związku z tym w przypadku instrukcji CREATE INDEX algorytm i blokada mogą mieć wpływ na metodę kopiowania tabeli i poziom współbieżności do odczytu i zapisu, jednak dodanie indeksu PEŁNOTEKSTOWEGO lub PRZESTRZENnego nadal uniemożliwi wykonywanie operacji DML. Zobacz poniżej przykład tworzenia indeksu z klauzulami ALGORITHM i LOCK:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Niestety, w przypadku instrukcji języka DDL wymagającej blokady nie można uniknąć opóźnień replikacji, zamiast tego typy operacji DDL powinny być wykonywane w godzinach szczytu, na przykład podczas Nighttime, aby zmniejszyć potencjalny wpływ.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Opóźnienie replikacji z powodu mniejszej jednostki SKU serwera repliki

W Azure Database for MySQL odczytywać repliki są tworzone z tą samą konfiguracją serwera co serwer główny. Konfigurację serwera repliki można zmienić po jego utworzeniu. Jeśli jednak serwer repliki zostanie obniżony, obciążenie może spowodować zwiększenie zużycia zasobów, które z kolei może prowadzić do opóźnień replikacji. Można to zaobserwować przez monitorowanie użycia procesora CPU i pamięci serwera repliki w Azure Monitor. W tym scenariuszu zaleca się, aby konfiguracja serwera repliki była utrzymywana z równymi lub większymi wartościami niż źródło, aby upewnić się, że replika jest w stanie utrzymać się z serwerem głównym.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Poprawianie opóźnień replikacji przy użyciu dostrajania parametrów serwera na serwerze źródłowym

W Azure Database for MySQL replikacja jest zoptymalizowana pod kątem domyślnego uruchamiania równoległych wątków w replikach. W przypadku obciążeń o wysokim poziomie współbieżności na serwerze źródłowym, na których serwer repliki nie przechwytuje, można zwiększyć opóźnienie replikacji przez skonfigurowanie binlog_group_commit_sync_delay parametrów na serwerze źródłowym. Ten parametr określa, ile mikrosekund zatwierdzania dzienników binarnych czeka przed synchronizacją binarnego pliku dziennika. Korzyść polega na tym, że zamiast natychmiastowego zastosowania każdej przekazanej transakcji, główny wysyła aktualizacje dzienników binarnych zbiorczo. Pozwala to zmniejszyć liczbę operacji we/wy na replice oraz poprawić wydajność. W tym scenariuszu przydatne może być ustawienie binlog_group_commit_sync_delay na 1000 lub tak i monitorowanie opóźnienia replikacji. Ten parametr należy ustawić ostrożnie i korzystać wyłącznie w przypadku dużych obciążeń współbieżnych. W przypadku scenariusza o niskiej współbieżności z dużą liczbą pojedynczych transakcji ustawienie binlog_group_commit_sync_delay można dodać do opóźnienia, ponieważ wątek we/wy oczekuje na aktualizacje zbiorcze dzienników binarnych, w przypadku których może zostać zatwierdzona tylko kilka transakcji. 

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [replikacji MySQL binlog — Omówienie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
