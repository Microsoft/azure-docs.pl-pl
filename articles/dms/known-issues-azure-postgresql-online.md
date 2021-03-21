---
title: 'Znane problemy: migracje online z PostgreSQL do Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informacje o znanych problemach i ograniczeniach migracji z migracją online z PostgreSQL do Azure Database for PostgreSQL przy użyciu Azure Database Migration Service.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: fdefcabdda64402610f115832976ec9f7af81b80
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258833"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Znane problemy/ograniczenia migracji z migracją online z PostgreSQL do usługi Azure DB dla PostgreSQL

Znane problemy i ograniczenia związane z migracją online z PostgreSQL do Azure Database for PostgreSQL są opisane w poniższych sekcjach.

## <a name="online-migration-configuration"></a>Konfiguracja migracji w trybie online

- Na źródłowym serwerze PostgreSQL musi być uruchomiona wersja 9,4, 9,5, 9,6, 10 lub 11. Aby uzyskać więcej informacji, zobacz artykuł [Obsługiwane wersje bazy danych PostgreSQL](../postgresql/concepts-supported-versions.md).
- Obsługiwane są tylko migracje do tej samej lub wyższej wersji. Na przykład Migrowanie PostgreSQL 9,5 do Azure Database for PostgreSQL 9,6 lub 10 jest obsługiwane, ale Migrowanie z PostgreSQL 11 do PostgreSQL 9,6 nie jest obsługiwane.
- Aby włączyć replikację logiczną w pliku **Source PostgreSQL PostgreSQL. conf** , ustaw następujące parametry:
  - **wal_level** = logiczny
  - **max_replication_slots** = [co najmniej Maksymalna liczba baz danych na potrzeby migracji]; Jeśli chcesz migrować cztery bazy danych, ustaw wartość na co najmniej 4.
  - **max_wal_senders** = [liczba baz danych uruchomionych współbieżnie]; Zalecana wartość to 10
- Dodawanie adresu IP agenta DMS do źródła PostgreSQL pg_hba. conf
  1. Zanotuj adres IP DMS po zakończeniu aprowizacji wystąpienia Azure Database Migration Service.
  2. Dodaj adres IP do pliku pg_hba. conf, jak pokazano:

      ```
          host    all    172.16.136.18/10    md5
          host    replication postgres    172.16.136.18/10     md5
      ```

- Użytkownik musi mieć rolę replikacji na serwerze hostującym źródłową bazę danych.
- Źródłowe i docelowe schematy bazy danych muszą być zgodne.
- Schemat w docelowym serwerze usługi Azure Database for PostgreSQL-Single nie może mieć kluczy obcych. Użyj następującego zapytania, aby porzucić klucze obce:

    ```
                  SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.

- Schemat w docelowym serwerze usługi Azure Database for PostgreSQL-Single nie może mieć żadnych wyzwalaczy. Aby wyłączyć Wyzwalacze w docelowej bazie danych, wykonaj następujące czynności:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Ograniczenia rozmiaru
- Można migrować do 2 TB danych z PostgreSQL do usługi Azure DB dla PostgreSQL przy użyciu jednej usługi DMS.
## <a name="datatype-limitations"></a>Ograniczenia typów danych

  **Ograniczenie**: Jeśli w tabelach nie istnieje klucz podstawowy, zmiany mogą nie być synchronizowane z docelową bazą danych.

  **Obejście**: tymczasowo Ustaw klucz podstawowy dla tabeli do migracji, aby kontynuować. Klucz podstawowy można usunąć po zakończeniu migracji danych.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Ograniczenia dotyczące migracji z usługi AWS RDS PostgreSQL do trybu online

Podczas próby przeprowadzenia migracji w trybie online z AWS RDS PostgreSQL do Azure Database for PostgreSQL mogą wystąpić następujące błędy.

- **Błąd**: Wartość domyślna kolumny „{column}” w tabeli „{table}” w bazie danych „{baza danych}” różni się na serwerze źródłowym i docelowym. Na serwerze źródłowym jest to „{value on source}”, a na serwerze docelowym — „{value on target}”.

  **Ograniczenie**: ten błąd występuje, gdy wartość domyślna schematu kolumny różni się między źródłową i docelową bazą danych.
  **Obejście**: Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle. Aby uzyskać szczegółowe informacje na temat migrowania schematu, zapoznaj się z [dokumentacją dotyczącą migracji do usługi Azure PostgreSQL online](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Błąd**: docelowa baza danych „{database}” zawiera następującą liczbę tabel: „{number of tables}”, natomiast źródłowa baza danych „{database}” zawiera następującą liczbę tabel: „{number of tables}”. Liczba tabel w źródłowej i docelowej bazie danych powinna być taka sama.

  **Ograniczenie**: ten błąd występuje, gdy liczba tabel jest różna między źródłową i docelową bazą danych.

  **Obejście**: Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle. Aby uzyskać szczegółowe informacje na temat migrowania schematu, zapoznaj się z [dokumentacją dotyczącą migracji do usługi Azure PostgreSQL online](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Błąd:** Źródłowa baza danych {Database} jest pusta.

  **Ograniczenie**: ten błąd występuje, gdy źródłowa baza danych jest pusta. Najprawdopodobniej z powodu wybrania niewłaściwej bazy danych jako źródła.

  **Obejście**: dokładnie sprawdź źródłową bazę danych wybraną do migracji, a następnie spróbuj ponownie.

- **Błąd:** Docelowa baza danych {Database} jest pusta. Przeprowadź migrację schematu.

  **Ograniczenie**: ten błąd występuje, gdy nie ma schematu w docelowej bazie danych. Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle.
  **Obejście**: Upewnij się, że schemat na miejscu docelowym jest zgodny ze schematem w źródle. Aby uzyskać szczegółowe informacje na temat migrowania schematu, zapoznaj się z [dokumentacją dotyczącą migracji do usługi Azure PostgreSQL online](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Inne ograniczenia

- Nazwa bazy danych nie może zawierać średnika (;).
- Przechwycona tabela musi mieć klucz podstawowy. Jeśli tabela nie ma klucza podstawowego, wynik operacji usuwania i aktualizowania rekordu będzie nieprzewidywalny.
- Aktualizacja segmentu klucza podstawowego jest ignorowana. W takich przypadkach zastosowanie takiej aktualizacji zostanie zidentyfikowane przez obiekt docelowy jako aktualizacja, która nie zaktualizowano żadnych wierszy i spowoduje powstanie rekordu zarejestrowanego w tabeli wyjątków.
- Migracja wielu tabel o takiej samej nazwie, ale innym przypadku (np. Tabela1, TABELA1 i Tabela1) może spowodować nieprzewidywalne zachowanie i dlatego nie jest obsługiwana.
- Przetwarzanie zmian [CREATE | ZMIEŃ | UPUŚĆ | Truncate] tabela DDLs nie jest obsługiwana.
- W Azure Database Migration Service pojedyncze działanie migracji może obsługiwać maksymalnie cztery bazy danych.
- Migracja tabeli pg_largeobject nie jest obsługiwana. 
