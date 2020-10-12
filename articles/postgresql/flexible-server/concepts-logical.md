---
title: Replikacja logiczna i dekodowanie logiczne — serwer elastyczny Azure Database for PostgreSQL
description: Dowiedz się więcej na temat korzystania z replikacji logicznej i dekodowania logicznego w Azure Database for PostgreSQL-elastycznym serwerze
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707867"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Replikacja logiczna i dekodowanie logiczne w Azure Database for PostgreSQL-elastycznym serwerze

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Funkcje replikacji logicznej i logicznego dekodowania PostgreSQL są obsługiwane w Azure Database for PostgreSQL-elastycznym serwerze dla Postgres w wersji 11.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Porównywanie logicznej replikacji i dekodowania logicznego
Replikacja logiczna i dekodowanie logiczne mają różne podobieństwa. Oba te elementy
* umożliwia replikowanie danych z Postgres
* Użyj [dziennika zapisu (WAL)](https://www.postgresql.org/docs/current/wal.html) jako źródła zmian
* Wyślij dane przy użyciu [miejsc replikacji logicznej](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) . Gniazdo reprezentuje strumień zmian.
* Użyj [właściwości tożsamości repliki](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) tabeli, aby określić, jakie zmiany mogą być wysyłane
* Nie replikuj zmian DDL


Te dwie technologie mają różnice: replikacja logiczna 
* umożliwia określenie tabeli lub zestawu tabel, które mają być replikowane.
* replikuje dane między wystąpieniami PostgreSQL

Dekodowanie logiczne 
* wyodrębnia zmiany we wszystkich tabelach w bazie danych 
* nie można bezpośrednio wysyłać danych między wystąpieniami PostgreSQL


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Wymagania wstępne dotyczące replikacji logicznej i dekodowania logicznego

1. Dla parametru serwer Ustaw `wal_level` wartość `logical` .
2. Uruchom ponownie serwer, aby zastosować `wal_level` zmianę.
3. Upewnij się, że wystąpienie PostgreSQL zezwala na ruch sieciowy z połączonego zasobu.
4. Udziel uprawnień do replikacji użytkownika administrator.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Korzystanie z replikacji logicznej i dekodowania logicznego

### <a name="logical-replication"></a>Replikacja logiczna
Replikacja logiczna używa wyrazów "Wydawca" i "Subscriber". 
* Wydawca to baza danych PostgreSQL, **z**której są wysyłane dane. 
* Abonentem jest baza danych PostgreSQL, **do**której są wysyłane dane.

Oto przykładowy kod, którego można użyć do wypróbowania replikacji logicznej.

1. Nawiąż połączenie z bazą danych wydawcy. Utwórz tabelę i Dodaj dane.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Utwórz publikację dla tabeli.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Nawiąż połączenie z bazą danych subskrybenta. Utwórz tabelę z tym samym schematem co na wydawcy.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Utwórz subskrypcję, która będzie łączyć się z utworzoną wcześniej publikacją.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Teraz możesz wysyłać zapytania do tabeli na subskrybencie. Zobaczysz, że dane zostały odebrane od wydawcy.
   ```SQL
   SELECT * FROM basic;
   ```

Możesz dodać więcej wierszy do tabeli wydawcy i wyświetlić zmiany na subskrybencie.

Zapoznaj się z dokumentacją PostgreSQL, aby uzyskać więcej informacji na temat [replikacji logicznej](https://www.postgresql.org/docs/current/logical-replication.html).

### <a name="logical-decoding"></a>Dekodowanie logiczne
Dekodowanie logiczne może być używane za pośrednictwem protokołu przesyłania strumieniowego lub interfejsu SQL. 

#### <a name="streaming-protocol"></a>Protokół przesyłania strumieniowego
Użycie protokołu przesyłania strumieniowego jest często preferowane. Możesz utworzyć własnego użytkownika/łącznik lub użyć usługi innej firmy, takiej jak [Debezium](https://debezium.io/). 

Zapoznaj się z dokumentacją wal2json, aby zapoznać się [z przykładem przy użyciu protokołu przesyłania strumieniowego z pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>Interfejs SQL 
W poniższym przykładzie użyto interfejsu SQL z wtyczką wal2json.
 
1. Utwórz miejsce.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Wydaj polecenia SQL. Na przykład:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Użyj zmian.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Dane wyjściowe będą wyglądać następująco:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Upuść miejsce po zakończeniu korzystania z niego.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Zapoznaj się z dokumentacją PostgreSQL, aby dowiedzieć się więcej na temat [dekodowania logicznego](https://www.postgresql.org/docs/current/logicaldecoding.html).


## <a name="monitoring"></a>Monitorowanie
Należy monitorować logiczne dekodowanie. Wszystkie nieużywane gniazda replikacji muszą zostać porzucone. Gniazda znajdują się na Postgres dzienników i odpowiednich wykazów systemowych do momentu odczytania zmian. Jeśli subskrybent lub odbiorca nie powiodło się lub nie został prawidłowo skonfigurowany, nieużywane dzienniki będą wymagały użycia i wypełnienia magazynu. Ponadto niewykorzystane dzienniki zwiększają ryzyko związane z IDENTYFIKATORem transakcji wraparound. Obie sytuacje mogą spowodować, że serwer stanie się niedostępny. W związku z tym ważne jest, aby gniazda replikacji logicznej były używane w sposób ciągły. Jeśli gniazdo replikacji logicznej nie jest już używane, Porzuć je od razu.

Kolumna "Active" w widoku pg_replication_slots wskazuje, czy klient jest połączony z miejscem.
```SQL
SELECT * FROM pg_replication_slots;
```

[Ustawianie alertów](howto-alert-on-metrics.md) dotyczących **maksymalnego użycia identyfikatorów transakcji** i **magazynu używanych** przez elastyczne metryki serwera do powiadamiania o zwiększeniu liczby ostatnich progów normalnych przez wartości. 

## <a name="limitations"></a>Ograniczenia
* **Odczytaj repliki** — Azure Database for PostgreSQL odczytu replik nie są obecnie obsługiwane dla elastycznych serwerów.
* **Gniazda i tryb failover z wysoką dostępnością** — gniazda replikacji logicznej na serwerze podstawowym nie są dostępne na serwerze rezerwy w pomocniczym programie AZ. Dotyczy to sytuacji, gdy serwer używa opcji "nadmiarowa wysoka dostępność" strefy. W przypadku przejścia w tryb failover do serwera rezerwowego gniazda replikacji logicznej nie będą dostępne w stanie wstrzymania.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [opcjach sieciowych](concepts-networking.md)
* Uzyskaj informacje na temat [rozszerzeń](concepts-extensions.md) dostępnych na serwerze elastycznym
* Dowiedz się więcej o [wysokiej dostępności](concepts-high-availability.md)

