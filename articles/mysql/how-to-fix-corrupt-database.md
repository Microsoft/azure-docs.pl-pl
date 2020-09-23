---
title: Rozwiązywanie problemów z uszkodzeniem bazy danych — Azure Database for MySQL
description: Dowiedz się więcej o sposobie rozwiązywania problemów z uszkodzeniem bazy danych Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938936"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Rozwiązywanie problemów z uszkodzeniem bazy danych na Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Uszkodzenie bazy danych może spowodować przestoje aplikacji, a także krytyczne rozwiązanie problemu, aby uniknąć utraty danych. W przypadku uszkodzenia bazy danych w dzienniku serwera zostanie wyświetlony komunikat o błędzie **InnoDB: uszkodzenie strony bazy danych na dysku lub błąd**.

W tym przewodniku dowiesz się, jak rozwiązać problem z uszkodzeniem bazy danych lub tabeli. Azure Database for MySQL używa aparatu InnoDB i oferuje automatyczne sprawdzanie uszkodzeń i operacje naprawcze. InnoDB sprawdza uszkodzone strony przez wykonywanie sum kontrolnych na każdej stronicowanej stronie, a jeśli wykryje niezgodność sumy kontrolnej, spowoduje to automatyczne zatrzymanie serwera MySQL.

Wypróbuj poniższe opcje, aby szybko ograniczyć problemy z uszkodzeniem bazy danych.

## <a name="restart-your-mysql-server"></a>Ponowne uruchamianie serwera MySQL

Zazwyczaj należy zauważyć, że baza danych lub tabela jest uszkodzona, gdy aplikacja uzyskuje dostęp do tej tabeli danych typu ro. Ponieważ funkcja InnoDB ma mechanizm odzyskiwania po awarii, który może rozwiązać większość problemów po ponownym uruchomieniu serwera. W związku z tym ponowne uruchomienie serwera powinno pomóc serwerowi odzyskać sprawność po awarii, która spowodowała, że baza danych jest w złej kondycji.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Rozwiązywanie problemów z uszkodzeniem danych za pomocą metody dump i Restore

Zaleca się rozwiązanie problemu uszkodzenia przy użyciu **metody zrzutu i przywracania**. Dotyczy to uzyskiwania dostępu do uszkodzonej tabeli przy użyciu narzędzia **mysqldump** do utworzenia logicznej kopii zapasowej tabeli, która zachowuje strukturę tabeli i dane w niej, a następnie ponownie ładuje tabelę z powrotem do bazy danych.

### <a name="backup-your-database-or-tables"></a>Tworzenie kopii zapasowej bazy danych lub tabel

> [!Important]
> - Skonfigurowanie reguły zapory w celu uzyskania dostępu do serwera z komputera klienckiego. Zobacz jak skonfigurować [regułę zapory na jednym serwerze](howto-manage-firewall-using-portal.md) i [regule zapory na serwerze elastycznym](flexible-server/how-to-connect-tls-ssl.md).
> - Użyj opcji SSL ```--ssl-cert``` dla **mysqldump** , jeśli włączono protokół SSL

Utwórz plik kopii zapasowej z wiersza polecenia przy użyciu mysqldump za pomocą tego polecenia

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry, które należy podać:
- [SSL-cert =/Path/to/PEM] Pobierz certyfikat SSL na komputerze klienckim i ustaw w nim ścieżkę w poleceniu. NIE należy używać protokołu SSL jest wyłączony.
- [Host] to serwer Azure Database for MySQL
- [uname] to nazwa użytkownika administratora serwera
- [Pass] to hasło użytkownika administratora
- [dbname] to nazwa bazy danych
- [backupfile. SQL] Jeśli nazwa pliku kopii zapasowej bazy danych

> [!Important]
> - W przypadku pojedynczego serwera użyj formatu ```admin-user@servername``` do zastąpienia ```myserveradmin``` w poniższych poleceniach.
> - W przypadku serwera elastycznego Użyj formatu ```admin-user``` do zastąpienia ```myserveradmin``` w poniższych poleceniach.

Jeśli określona tabela jest uszkodzona, wybierz określone tabele w bazie danych, aby utworzyć kopię zapasową przy użyciu tego przykładu
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Aby utworzyć kopię zapasową co najmniej jednej bazy danych, należy użyć przełącznika--Database i wyświetlić listę nazw baz danych rozdzielonych spacjami.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Przywracanie bazy danych lub tabel

W poniższych krokach przedstawiono sposób przywracania bazy danych lub tabel przez jednostkę TP. Po utworzeniu pliku kopii zapasowej można przywrócić tabelę lub bazy danych za pomocą narzędzia ***MySQL** . Uruchom polecenie, jak pokazano poniżej:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Oto przykład przywracania ```testdb``` z pliku kopii zapasowej utworzonego za pomocą **mysqldump**. 

> [!Important]
> - W przypadku pojedynczego serwera użyj formatu ```admin-user@servername``` do zastąpienia ```myserveradmin``` w poleceniu poniżej.
> - W przypadku serwera elastycznego Użyj formatu ```admin-user``` do zastąpienia ```myserveradmin``` w poleceniu poniżej. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Następne kroki
Jeśli powyższe kroki nie pomogą rozwiązać problemu, można zawsze przywrócić cały serwer.
- [Przywracanie Azure Database for MySQL pojedynczego serwera](howto-restore-server-portal.md)
- [Przywracanie Azure Database for MySQL elastycznego serwera](flexible-server/how-to-restore-server-portal.md)



