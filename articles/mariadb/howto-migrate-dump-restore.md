---
title: Migrowanie z użyciem zrzutów i przywracania — Azure Database for MariaDB
description: W tym artykule opisano dwa typowe sposoby tworzenia kopii zapasowych i przywracania baz danych w usłudze Azure Database for MariaDB przy użyciu narzędzi takich jak mysqldump, MySQL Workbench i phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628224"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Migrowanie bazy danych MariaDB do usługi Azure Database for MariaDB przy użyciu zrzutów i przywracania

W tym artykule opisano dwa typowe sposoby tworzenia kopii zapasowych i przywracania baz danych w usłudze Azure Database for MariaDB:
- Zrzuć i Przywróć przy użyciu narzędzia wiersza polecenia (przy użyciu mysqldump) 
- Zrzuć i Przywróć przy użyciu phpMyAdmin

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem migrowania bazy danych wykonaj następujące czynności:
- Utwórz [Azure Portal Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Zainstaluj narzędzie wiersza polecenia [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) .
- Pobierz i zainstaluj program [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy na potrzeby uruchamiania poleceń dump i Restore.

## <a name="use-common-tools"></a>Korzystanie z popularnych narzędzi
Korzystaj z popularnych narzędzi i narzędzi, takich jak MySQL Workbench lub mysqldump, aby zdalnie łączyć i przywracać dane do usługi Azure Database for MariaDB. Użyj tych narzędzi na komputerze klienckim z połączeniem internetowym, aby nawiązać połączenie z usługą Azure Database for MariaDB. Użycie połączenia szyfrowanego za pośrednictwem protokołu SSL jest najlepszym rozwiązaniem w zakresie zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Konfigurowanie łączności SSL w Azure Database for MariaDB](concepts-ssl-connection-security.md). Nie musisz przenosić plików zrzutu do żadnej specjalnej lokalizacji w chmurze podczas migrowania danych do usługi Azure Database for MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania zrzutów i przywracania
Za pomocą narzędzi MySQL, takich jak mysqldump i mysqlpump, można zrzucić i ładować bazy danych do serwera usługi Azure Database for MariaDB w kilku typowych scenariuszach. 

- Podczas migrowania całej bazy danych używaj zrzutów baz danych. To zalecenie jest przechowywane podczas przesuwania dużej ilości danych lub w przypadku, gdy chcesz zminimalizować przerwy w działaniu usługi dla witryn lub aplikacji na żywo. 
-  Upewnij się, że wszystkie tabele w bazie danych korzystają z aparatu magazynu InnoDB podczas ładowania danych do usługi Azure Database for MariaDB. Azure Database for MariaDB obsługuje tylko aparat magazynu InnoDB i nie ma innych aparatów magazynu. Jeśli tabele są skonfigurowane z innymi aparatami magazynu, przekonwertuj je na format aparatu InnoDB przed przeprowadzeniem migracji do usługi Azure Database for MariaDB.
   
   Jeśli na przykład masz aplikację WordPress lub aplikację sieci Web korzystającą z tabel MyISAM, najpierw przekonwertuj te tabele, Przeprowadź migrację ich do formatu InnoDB przed przystąpieniem do przywracania ich do usługi Azure Database for MariaDB. Użyj klauzuli, `ENGINE=InnoDB` Aby ustawić aparat do tworzenia nowej tabeli, a następnie przetransferować dane do zgodnej tabeli przed przystąpieniem do przywracania. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć problemów ze zgodnością podczas zrzucania baz danych, upewnij się, że korzystasz z tej samej wersji programu MariaDB w systemach źródłowych i docelowych. Na przykład jeśli istniejący serwer MariaDB jest w wersji 10,2, należy przeprowadzić migrację do usługi Azure Database for MariaDB, która jest skonfigurowana do uruchamiania wersji 10,2. `mysql_upgrade`Polecenie nie działa na serwerze Azure Database for MariaDB i nie jest obsługiwane. Jeśli musisz przeprowadzić uaktualnienie w wersjach MariaDB, najpierw wykonaj zrzut lub wyeksportuj starszą wersję bazy danych do nowszej wersji MariaDB we własnym środowisku. Następnie można uruchomić program `mysql_upgrade` przed rozpoczęciem migracji do usługi Azure Database for MariaDB.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności
Aby zoptymalizować wydajność w przypadku dumpingu dużych baz danych, należy pamiętać o następujących kwestiach:
-   Użyj `exclude-triggers` opcji w mysqldump. Wyklucz wyzwalacze z plików zrzutów, aby uniknąć uruchamiania poleceń wyzwalacza podczas przywracania danych. 
-   Użyj `single-transaction` opcji, aby ustawić tryb izolacji transakcji na powtarzający odczyt i wysyłanie instrukcji SQL Start Transaction do serwera przed zatopieniem danych. Zatopienie wielu tabel w ramach jednej transakcji powoduje, że niektóre dodatkowe magazyny mają być zużywane podczas przywracania. `single-transaction`Opcja i opcja wykluczają `lock-tables` się wzajemnie. Dzieje się tak, ponieważ tabele blokady powodują niejawne zatwierdzenie oczekujących transakcji. Aby zrzucić duże tabele, Połącz `single-transaction` opcję z `quick` opcją. 
-   Użyj `extended-insert` składni wielowierszowej, która zawiera kilka list wartości. To podejście powoduje zmniejszenie pliku zrzutu i przyspieszenie operacji wstawiania podczas ponownego ładowania pliku.
-  Użyj `order-by-primary` opcji w mysqldump, gdy są zrzucane bazy danych, dzięki czemu dane są określane w kolejności klucza podstawowego.
-   `disable-keys`Aby wyłączyć ograniczenia klucza obcego przed obciążeniem, należy użyć opcji w mysqldump. Wyłączenie kontroli kluczy obcych ułatwia zwiększenie wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu, aby zapewnić integralność referencyjną.
-   W razie potrzeby użyj tabel partycjonowanych.
-   Równoległe ładowanie danych. Unikaj zbyt dużej liczby równoległości, co może spowodować trafienie limitu zasobów i monitorowanie zasobów przy użyciu metryk dostępnych w Azure Portal. 
-   Użyj `defer-table-indexes` opcji w mysqlpump w przypadku zrzucania baz danych, dzięki czemu tworzenie indeksów odbywa się po załadowaniu danych tabeli.
-   Skopiuj pliki kopii zapasowej do magazynu obiektów blob platformy Azure i wykonaj przywracanie z tego miejsca. Takie podejście powinno być dużo szybsze niż przeprowadzenie przywracania przez Internet.

## <a name="create-a-backup-file"></a>Utwórz plik kopii zapasowej

Aby utworzyć kopię zapasową istniejącej bazy danych MariaDB na serwerze lokalnym lub na maszynie wirtualnej, uruchom następujące polecenie za pomocą mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

Parametry, które należy podać:
- *\<uname>*: Nazwa użytkownika bazy danych 
- *\<pass>*: Hasło do bazy danych (należy zauważyć, że nie ma spacji między-p i hasłem) 
- *\<dbname>*: Nazwa bazy danych 
- *\<backupfile.sql>*: Nazwa pliku kopii zapasowej bazy danych 
- *\<--opt>*: Opcja mysqldump 

Na przykład, aby utworzyć kopię zapasową bazy danych o nazwie *TestDB* na serwerze MariaDB przy użyciu nazwy użytkownika *Użytkownik testowy* i bez hasła do pliku testdb_backup. SQL, użyj następującego polecenia. Polecenie tworzy kopię zapasową `testdb` bazy danych w pliku o nazwie `testdb_backup.sql` , który zawiera wszystkie instrukcje SQL wymagane do ponownego utworzenia bazy danych. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Aby wybrać konkretne tabele do utworzenia kopii zapasowej w bazie danych programu, należy wyświetlić listę nazw tabel rozdzielonych spacjami. Na przykład, aby utworzyć kopię zapasową tylko tabel Tabela1 i tabela2 z *TestDB*, wykonaj następujące czynności: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Aby utworzyć kopię zapasową więcej niż jednej bazy danych, należy użyć przełącznika--Database i wyświetlić listę nazw baz danych rozdzielonych spacjami. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Tworzenie bazy danych na serwerze docelowym
Utwórz pustą bazę danych na docelowym serwerze Azure Database for MariaDB, na którym chcesz przeprowadzić migrację danych. Utwórz bazę danych za pomocą narzędzia, takiego jak MySQL Workbench. Baza danych może mieć taką samą nazwę jak baza danych, która zawiera dane z danymi zrzutu, lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, Znajdź informacje o połączeniu w okienku **Przegląd** usługi Azure Database for MariaDB.

![Zrzut ekranu przedstawiający okienko przegląd dla serwera usługi Azure Database for MariaDB w Azure Portal.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

W programie MySQL Workbench Dodaj informacje o połączeniu.

![Zrzut ekranu przedstawiający okienko połączenia MySQL w programie MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Przywracanie bazy danych MariaDB
Po utworzeniu docelowej bazy danych można użyć polecenia MySQL lub MySQL Workbench do przywrócenia danych do nowo utworzonej bazy danych z pliku zrzutu.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

W tym przykładzie dane są przywracane do nowo utworzonej bazy danych na docelowym serwerze Azure Database for MariaDB.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Eksportowanie bazy danych MariaDB przy użyciu phpMyAdmin

W celu wyeksportowania można użyć typowego narzędzia phpMyAdmin, które mogło być już zainstalowane lokalnie w danym środowisku. Aby wyeksportować bazę danych MariaDB, wykonaj następujące czynności:
1. Otwórz phpMyAdmin.
1. W lewym okienku wybierz swoją bazę danych, a następnie wybierz łącze **Eksportuj** . Zostanie wyświetlona nowa strona umożliwiająca wyświetlenie zrzutu bazy danych.
1. W obszarze **eksport** wybierz łącze **Zaznacz wszystko** , aby wybrać tabele w bazie danych. 
1. W obszarze **Opcje SQL** wybierz odpowiednie opcje. 
1. Wybierz opcję **Zapisz jako plik** i odpowiednią opcję kompresji, a następnie wybierz pozycję **Przejdź**. W wierszu polecenia Zapisz plik lokalnie.

## <a name="import-your-database-by-using-phpmyadmin"></a>Importowanie bazy danych za pomocą phpMyAdmin

Proces importowania jest podobny do procesu eksportowania. Wykonaj następujące czynności:
1. Otwórz phpMyAdmin. 
1. Na stronie Konfiguracja phpMyAdmin wybierz pozycję **Dodaj** , aby dodać serwer Azure Database for MariaDB. 
1. Wprowadź szczegóły połączenia i informacje logowania.
1. Utwórz odpowiednio nazwę bazy danych, a następnie wybierz ją w okienku po lewej stronie. Aby ponownie zapisać istniejącą bazę danych, wybierz nazwę bazy danych, zaznacz wszystkie pola wyboru obok nazwy tabeli, a następnie wybierz pozycję **Usuń, aby usunąć** istniejące tabele. 
1. Wybierz łącze **SQL** , aby wyświetlić stronę, na której można wprowadzić polecenia SQL lub przekazać plik SQL. 
1. Wybierz przycisk **Przeglądaj** , aby znaleźć plik bazy danych. 
1. Wybierz przycisk **Przejdź** , aby wyeksportować kopię zapasową, wykonaj polecenia SQL i ponownie utwórz bazę danych.

## <a name="next-steps"></a>Następne kroki
- [Połącz aplikacje z usługą Azure Database for MariaDB](./howto-connection-string.md).
