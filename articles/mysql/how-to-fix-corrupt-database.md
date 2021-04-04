---
title: Rozwiązywanie problemów z uszkodzeniem bazy danych — Azure Database for MySQL
description: W tym artykule opisano sposób rozwiązywania problemów z uszkodzeniem bazy danych w Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766891"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Rozwiązywanie problemów z uszkodzeniem bazy danych w usłudze Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Uszkodzenie bazy danych może spowodować przestoje aplikacji. Istnieje również krytyczne rozwiązanie problemów z uszkodzeniem w czasie, aby uniknąć utraty danych. W przypadku uszkodzenia bazy danych w dziennikach serwera zostanie wyświetlony następujący błąd: `InnoDB: Database page corruption on disk or a failed.`

W tym artykule opisano sposób rozwiązywania problemów z uszkodzeniem bazy danych lub tabelą. Azure Database for MySQL używa aparatu InnoDB. Funkcja IT umożliwia automatyczne sprawdzanie uszkodzeń i wykonywanie napraw. InnoDB sprawdza uszkodzone strony, uruchamiając sumy kontrolne na każdej stronie, którą odczytuje. W przypadku znalezienia niezgodności sumy kontrolnej zostanie automatycznie zatrzymany serwer MySQL.

Wypróbuj poniższe opcje, aby szybko ograniczyć problemy z uszkodzeniem bazy danych.

## <a name="restart-your-mysql-server"></a>Ponowne uruchamianie serwera MySQL

Zazwyczaj należy zauważyć, że baza danych lub tabela jest uszkodzona, gdy aplikacja uzyskuje dostęp do tabeli lub bazy danych. InnoDB funkcje mechanizmu odzyskiwania po awarii, który może rozwiązać większość problemów po ponownym uruchomieniu serwera. Dlatego ponowne uruchomienie serwera może pomóc serwerowi odzyskać sprawność po awarii, która spowodowała, że baza danych jest w złej kondycji.

## <a name="use-the-dump-and-restore-method"></a>Użyj metody zrzutu i przywracania

Zalecamy rozwiązanie problemów z uszkodzeniem przy użyciu metody *zrzutu i przywracania* . Ta metoda obejmuje:
1. Uzyskiwanie dostępu do uszkodzonej tabeli.
1. Tworzenie logicznej kopii zapasowej tabeli przy użyciu narzędzia mysqldump. Kopia zapasowa zachowa strukturę tabeli i znajdujące się w niej dane.
1. Ponowne ładowanie tabeli do bazy danych programu.

### <a name="back-up-your-database-or-tables"></a>Tworzenie kopii zapasowej bazy danych lub tabel

> [!Important]
> - Upewnij się, że skonfigurowano regułę zapory w celu uzyskania dostępu do serwera z komputera klienckiego. Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguły zapory na pojedynczym serwerze](howto-manage-firewall-using-portal.md) i [Konfigurowanie reguły zapory na serwerze elastycznym](flexible-server/how-to-connect-tls-ssl.md).
> - Użyj opcji SSL `--ssl-cert` dla mysqldump, jeśli jest włączony protokół SSL.

Utwórz plik kopii zapasowej z wiersza polecenia przy użyciu mysqldump. Użyj następującego polecenia:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Opisy parametrów:
- `[ssl-cert=/path/to/pem]`: Ścieżka do certyfikatu SSL. Pobierz certyfikat SSL na komputerze klienckim i ustaw dla niego ścieżkę w poleceniu. Nie używaj tego parametru, jeśli protokół SSL jest wyłączony.
- `[host]`: Serwer Azure Database for MySQL.
- `[uname]`: Nazwa użytkownika administratora serwera.
- `[pass]`: Hasło administratora.
- `[dbname]`: Nazwa bazy danych.
- `[backupfile.sql]`: Nazwa pliku kopii zapasowej bazy danych.

> [!Important]
> - W przypadku pojedynczego serwera użyj formatu, `admin-user@servername` Aby zamienić `myserveradmin` następujące polecenia.
> - W przypadku serwera elastycznego Użyj formatu, `admin-user` Aby zamienić `myserveradmin` następujące polecenia.

Jeśli określona tabela jest uszkodzona, wybierz określone tabele w bazie danych, aby utworzyć kopię zapasową:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Aby utworzyć kopię zapasową co najmniej jednej bazy danych, należy użyć `--database` przełącznika i wyświetlić listę nazw baz danych rozdzielonych spacjami:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Przywracanie bazy danych lub tabel

Poniższe kroki pokazują, jak przywrócić bazę danych lub tabele. Po utworzeniu pliku kopii zapasowej można przywrócić tabele lub bazy danych za pomocą narzędzia MySQL. Uruchom następujące polecenie:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Oto przykład, który przywraca `testdb` z pliku kopii zapasowej utworzonego za pomocą mysqldump: 

> [!Important]
> - W przypadku pojedynczego serwera użyj formatu, `admin-user@servername` Aby zastąpić `myserveradmin` następujące polecenie.
> - W przypadku serwera elastycznego Użyj formatu, ```admin-user``` Aby zastąpić `myserveradmin` następujące polecenie. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Następne kroki
Jeśli powyższe kroki nie rozwiążą problemu, zawsze można przywrócić cały serwer:
- [Przywracanie serwera w Azure Database for MySQL — pojedynczy serwer](howto-restore-server-portal.md)
- [Przywracanie serwera w Azure Database for MySQL-elastycznym serwerze](flexible-server/how-to-restore-server-portal.md)



