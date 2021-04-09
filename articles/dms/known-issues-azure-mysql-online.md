---
title: 'Znane problemy: migracje online do Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Informacje o znanych problemach i ograniczeniach migracji z migracją online do Azure Database for MySQL podczas korzystania z Azure Database Migration Service.
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
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606893"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemy z migracją w trybie online & ograniczenia dotyczące usługi Azure DB dla programu MySQL z Azure Database Migration Service

Znane problemy i ograniczenia związane z migracją w trybie online z programu MySQL do Azure Database for MySQL są opisane w poniższych sekcjach.

## <a name="online-migration-configuration"></a>Konfiguracja migracji w trybie online


- Źródłowa wersja serwera MySQL musi być w wersji 5.6.35, 5.7.18 lub nowszej
- Azure Database for MySQL obsługuje:
  - MySQL Community Edition
  - Aparat InnoDB
- Migracja tej samej wersji. Migrowanie MySQL 5,6 do Azure Database for MySQL 5,7 nie jest obsługiwane. Migracja do lub z MySQL 8,0 nie jest obsługiwana.
- Włączanie rejestrowania binarnego w my.ini (Windows) lub My. cnf (UNIX)
  - Ustaw wartość Server_id na wartość większą lub równą 1, na przykład Server_id = 1 (tylko w przypadku programu MySQL 5,6)
  - Set log-bin = \<path> (tylko dla MySQL 5,6)
  - Ustaw binlog_format = wiersz
  - Expire_logs_days = 5 (zalecane-tylko dla MySQL 5,6)
- Użytkownik musi mieć rolę ReplicationAdmin.
- Ustawienia sortowania zdefiniowane dla źródłowej bazy danych MySQL są takie same jak te zdefiniowane w Azure Database for MySQL docelowym.
- Schemat musi pasować do źródłowej bazy danych MySQL i docelowej bazy danych w Azure Database for MySQL.
- Schemat w Azure Database for MySQL docelowym nie może mieć kluczy obcych. Użyj następującego zapytania, aby porzucić klucze obce:
    ```sql
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
      CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
        KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
        AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Uruchom docelowy klucz obcy (znajduje się w drugiej kolumnie) w wyniku zapytania.
- Schemat w Azure Database for MySQL docelowym nie może mieć żadnych wyzwalaczy. Aby porzucić Wyzwalacze w docelowej bazie danych:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Ograniczenia typów danych

- **Ograniczenie**: Jeśli w źródłowej bazie danych MySQL istnieje typ danych JSON, migracja zakończy się niepowodzeniem podczas synchronizacji ciągłej.

    **Obejście**: Zmodyfikuj element DataType JSON na średni tekst lub LONGTEXT w źródłowej bazie danych MySQL.

- **Ograniczenie**: Jeśli nie ma klucza podstawowego w tabelach, synchronizacja ciągła zakończy się niepowodzeniem.

    **Obejście**: tymczasowo Ustaw klucz podstawowy dla tabeli do migracji, aby kontynuować. Klucz podstawowy można usunąć po zakończeniu migracji danych.

## <a name="lob-limitations"></a>Ograniczenia dotyczące obiektów LOB

Kolumny dużego obiektu (LOB) są kolumnami, które mogą mieć duży rozmiar. W przypadku bazy danych MySQL, medium text, LONGTEXT, BLOB, mediumblob, Longblob itp., są częścią typów danych LOB.

- **Ograniczenie**: Jeśli typy danych LOB są używane jako klucze podstawowe, migracja zakończy się niepowodzeniem.

    **Obejście**: Zastąp klucz podstawowy innymi rodzajami danych lub kolumnami, które nie są LOB.

- **Ograniczenie**: Jeśli długość kolumny dużego obiektu (LOB) jest większa niż wartość parametru "Ogranicz rozmiar obiektu LOB" (nie powinna być większa niż 64 KB), dane mogą być obcinane w miejscu docelowym. Możesz sprawdzić długość kolumny LOB przy użyciu tego zapytania:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Obejście**: Jeśli masz obiekt LOB o rozmiarze większym niż 64 KB, użyj parametru "Zezwalaj na nieograniczone rozmiary LOB". Należy zauważyć, że migracja przy użyciu parametru "Zezwalaj na nieograniczone rozmiary obiektów LOB" będzie mniejsza niż migracja przy użyciu parametru "Ogranicz rozmiar obiektu LOB".

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Ograniczenia podczas migrowania w trybie online z AWS RDS MySQL

Podczas próby przeprowadzenia migracji w trybie online z AWS RDS MySQL do Azure Database for MySQL mogą wystąpić następujące błędy.

- **Błąd:** Baza danych " {0} " ma klucze obce w miejscu docelowym. Napraw miejsce docelowe i uruchom nowe działanie migracji danych. Wykonaj Poniższy skrypt w celu, aby wyświetlić listę kluczy obcych

  **Ograniczenie**: Jeśli w schemacie są klucze obce, początkowe ładowanie i ciągła synchronizacja migracji zakończy się niepowodzeniem.
  **Obejście**: wykonaj następujący skrypt w programie MySQL Workbench, aby wyodrębnić skrypt klucza obcego i dodać skrypt klucza obcego:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Błąd:** Baza danych " {0} " nie istnieje na serwerze. Podany serwer źródłowy MySQL uwzględnia wielkość liter. Sprawdź nazwę bazy danych.

  **Ograniczenie**: Podczas migrowania bazy danych MySQL na platformę Azure przy użyciu interfejsu wiersza polecenia (CLI), użytkownicy mogą napotkać ten błąd. Usługa nie może zlokalizować bazy danych na serwerze źródłowym, co może być spowodowane podaną nieprawidłową nazwą bazy danych lub baza danych nie istnieje na wymienionym serwerze. Uwaga w nazwach baz danych jest rozróżniana wielkość liter.

  **Obejście**: Podaj dokładną nazwę bazy danych, a następnie spróbuj ponownie.

- **Błąd:** W bazie danych "{Database}" istnieją tabele o tej samej nazwie. Usługa Azure Database for MySQL nie obsługuje tabel uwzględniających wielkość liter.

  **Ograniczenie**: ten błąd występuje, gdy istnieją dwie tabele o tej samej nazwie w źródłowej bazie danych. Azure Database for MySQL nie obsługuje tabel z uwzględnieniem wielkości liter.

  **Obejście**: zaktualizuj nazwy tabel, aby były unikatowe, a następnie spróbuj ponownie.

- **Błąd:** Docelowa baza danych {Database} jest pusta. Przeprowadź migrację schematu.

  **Ograniczenie**: ten błąd występuje, gdy docelowa baza danych Azure Database for MySQL nie ma wymaganego schematu. Migracja schematu jest wymagana, aby umożliwić Migrowanie danych do obiektu docelowego.

  **Obejście**: [Migruj schemat](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) ze źródłowej bazy danych do docelowej bazy danych.

## <a name="other-limitations"></a>Inne ograniczenia

- Ciąg hasła, który ma otwierające i zamykające nawiasy klamrowe {} na początku i na końcu ciągu hasła nie jest obsługiwany. To ograniczenie dotyczy zarówno łączenia z źródłową bazą danych MySQL, jak i Azure Database for MySQL docelowej.
- Następujące DDLs nie są obsługiwane:
  - Wszystkie DDLs partycji
  - Usuń tabelę
  - Zmień nazwę tabeli
- Przy użyciu instrukcji *ALTER table <table_name> column_name <Dodawanie* kolumn do początku lub do środka tabeli nie jest obsługiwane. *<ALTER table table_name> Dodaj kolumnę <column_name>* dodaje kolumnę na końcu tabeli.
- Indeksy utworzone tylko w części danych kolumn nie są obsługiwane. Poniższa instrukcja to przykład, który tworzy indeks, używając tylko części danych kolumny:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- W Azure Database Migration Service limit migracji baz danych w ramach jednego działania migracji wynosi cztery.

- Usługa Azure DMS nie obsługuje akcji referencyjnej CASCADE, która pomaga automatycznie usuwać lub aktualizować pasujący wiersz w tabeli podrzędnej po usunięciu lub zaktualizowaniu wiersza w tabeli nadrzędnej. Aby uzyskać więcej informacji, zobacz sekcję działania referencyjne w temacie [ograniczenia klucza obcego](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)artykułu. Usługa Azure DMS wymaga porzucenia ograniczeń klucza obcego na docelowym serwerze bazy danych podczas początkowego ładowania danych i nie można używać akcji referencyjnych. Jeśli obciążenie zależy od aktualizowania powiązanej tabeli podrzędnej za pomocą tej akcji referencyjnej, zalecamy wykonanie [zrzutu i przywrócenie](../mysql/concepts-migrate-dump-restore.md) zamiast tego. 

- **Błąd:** Zbyt duży rozmiar wiersza (> 8126). Zmiana niektórych kolumn na tekst lub obiekt BLOB może pomóc. W bieżącym formacie wiersza prefiks obiektu BLOB o wartości 0 bajtów jest przechowywany w tekście.

  **Ograniczenie**: ten błąd występuje w przypadku migrowania do Azure Database for MySQL przy użyciu aparatu magazynu InnoDB, a każdy rozmiar wiersza tabeli jest zbyt duży (>8126 bajtów).

  **Obejście**: zaktualizuj schemat tabeli o rozmiarze wiersza większym niż 8126 bajtów. Nie zalecamy zmiany trybu ścisłego, ponieważ dane zostaną obcięte. Zmiana page_size nie jest obsługiwana.