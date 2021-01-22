---
title: Migrowanie z użyciem zrzutów i przywracania — Azure Database for MariaDB
description: W tym artykule opisano dwa typowe sposoby tworzenia kopii zapasowych i przywracania baz danych w Azure Database for MariaDB przy użyciu narzędzi takich jak mysqldump, MySQL Workbench i PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 8f7cb0710c11e0db9628ad19e2ede7ff05a19f88
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664974"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrowanie bazy danych MariaDB do Azure Database for MariaDB przy użyciu zrzutów i przywracania
W tym artykule opisano dwa typowe sposoby tworzenia kopii zapasowych i przywracania baz danych w Azure Database for MariaDB
- Zrzuć i Przywróć z wiersza polecenia (przy użyciu mysqldump) 
- Zrzuć i Przywróć przy użyciu PHPMyAdmin

## <a name="before-you-begin"></a>Zanim rozpoczniesz
Aby krokowo korzystać z tego przewodnika, musisz mieć:
- [Tworzenie Azure Database for MariaDB Server — Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- Narzędzie wiersza polecenia [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) zainstalowane na komputerze.
- Narzędzia MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy do wykonywania poleceń zrzutów i przywracania.

## <a name="use-common-tools"></a>Korzystanie z popularnych narzędzi
Używaj popularnych narzędzi i narzędzi, takich jak MySQL Workbench lub mysqldump, aby zdalnie łączyć i przywracać dane w Azure Database for MariaDB. Użyj tych narzędzi na komputerze klienckim z połączeniem internetowym, aby nawiązać połączenie z Azure Database for MariaDB. Użyj połączenia szyfrowanego protokołem SSL, aby uzyskać najlepsze rozwiązania w zakresie zabezpieczeń, zobacz również [Konfigurowanie łączności SSL w Azure Database for MariaDB](concepts-ssl-connection-security.md). Nie ma potrzeby przenoszenia plików zrzutu do żadnej specjalnej lokalizacji w chmurze podczas migracji do Azure Database for MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Typowe zastosowania zrzutów i przywracania
Możesz użyć narzędzi MySQL, takich jak mysqldump i mysqlpump, aby zrzucić i ładować bazy danych do serwera Azure Database for MariaDB w kilku typowych scenariuszach. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Podczas migrowania całej bazy danych używaj zrzutów baz danych. To zalecenie jest przechowywane w przypadku przeniesienia dużej ilości danych lub w celu zminimalizowania przerw w działaniu usługi dla witryn lub aplikacji na żywo. 
-  Upewnij się, że podczas ładowania danych do usługi Azure Database for MariaDB wszystkie tabele w bazie danych korzystają z aparatu magazynu InnoDB. Azure Database for MariaDB obsługuje tylko aparat magazynu InnoDB i w związku z tym nie obsługuje alternatywnych aparatów pamięci masowej. Jeśli tabele są skonfigurowane z innymi aparatami magazynu, przed rozpoczęciem migracji do Azure Database for MariaDB należy je przekonwertować na format aparatu InnoDB.
   Jeśli na przykład masz witrynę WordPress lub WebApp przy użyciu tabel MyISAM, najpierw przekonwertuj te tabele przez Migrowanie do formatu InnoDB przed przywróceniem do Azure Database for MariaDB. Użyj klauzuli, `ENGINE=InnoDB` Aby ustawić aparat używany podczas tworzenia nowej tabeli, a następnie Przenieś dane do zgodnej tabeli przed przywróceniem. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Aby uniknąć problemów ze zgodnością, upewnij się, że w przypadku zrzucania baz danych w systemach źródłowym i docelowym jest używana ta sama wersja programu MariaDB. Na przykład jeśli istniejący serwer MariaDB jest w wersji 10,2, należy przeprowadzić migrację do Azure Database for MariaDB skonfigurowany do uruchamiania wersji 10,2. Polecenie nie działa `mysql_upgrade` na serwerze Azure Database for MariaDB i nie jest obsługiwane. Jeśli musisz przeprowadzić uaktualnienie w wersjach MariaDB, najpierw wykonaj zrzut lub wyeksportuj niższą wersję bazy danych do nowszej wersji MariaDB we własnym środowisku. Następnie uruchom `mysql_upgrade` polecenie, przed podjęciem próby migracji do Azure Database for MariaDB.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności
Aby zoptymalizować wydajność, należy wziąć pod uwagę następujące kwestie w przypadku zatopienia dużych baz danych:
-   Użyj `exclude-triggers` opcji w mysqldump, gdy zrzucane są bazy danych. Wyklucz wyzwalacze z plików zrzutów, aby uniknąć uruchamiania poleceń wyzwalacza podczas przywracania danych. 
-   Użyj `single-transaction` opcji, aby ustawić tryb izolacji transakcji na powtarzalne odczytywanie i wysyłanie instrukcji SQL Start Transaction do serwera przed zatopieniem danych. Zatopienie wielu tabel w ramach jednej transakcji powoduje, że niektóre dodatkowe magazyny mają być zużywane podczas przywracania. `single-transaction`Opcja i `lock-tables` opcja wykluczają się wzajemnie, ponieważ tabele blokad powodują niejawne zatwierdzenie oczekujących transakcji. Aby zrzucić duże tabele, Połącz `single-transaction` opcję z `quick` opcją. 
-   Użyj `extended-insert` składni wielowierszowej, która zawiera kilka list wartości. Powoduje to zmniejszenie pliku zrzutu i przyspieszenie operacji wstawiania podczas ponownego ładowania pliku.
-  Użyj `order-by-primary` opcji w mysqldump, gdy zrzucane są bazy danych, aby dane były określane w kolejności klucza podstawowego.
-   Użyj `disable-keys` opcji w mysqldump, gdy zrzucasz dane, aby wyłączyć ograniczenia klucza obcego przed załadowaniem. Wyłączenie kontroli kluczy obcych zapewnia wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu, aby zapewnić integralność referencyjną.
-   W razie potrzeby użyj tabel partycjonowanych.
-   Równoległe ładowanie danych. Należy unikać zbyt dużej liczby równoległości, która spowodowałaby osiągnięcie limitu zasobów i monitorowanie zasobów przy użyciu metryk dostępnych w Azure Portal. 
-   Użyj `defer-table-indexes` opcji w mysqlpump podczas zrzucania baz danych, dzięki czemu tworzenie indeksów odbywa się po załadowaniu danych tabeli.
-   Skopiuj pliki kopii zapasowej do magazynu lub obiektów blob platformy Azure, a następnie wykonaj przywracanie z tego miejsca, które powinny być znacznie szybsze niż przeprowadzenie przywracania przez Internet.

## <a name="create-a-backup-file"></a>Utwórz plik kopii zapasowej
Aby utworzyć kopię zapasową istniejącej bazy danych MariaDB na serwerze lokalnym lub na maszynie wirtualnej, uruchom następujące polecenie za pomocą mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametry, które należy podać:
- [uname] Nazwa użytkownika bazy danych 
- chodzenia Hasło do bazy danych (należy zauważyć, że nie ma spacji między-p i hasłem) 
- Name Nazwa bazy danych 
- [backupfile. SQL] nazwa pliku kopii zapasowej bazy danych 
- [--opt] Opcja mysqldump 

Na przykład, aby utworzyć kopię zapasową bazy danych o nazwie "TestDB" na serwerze MariaDB z nazwą użytkownika "Użytkownik testowy" i bez hasła do pliku testdb_backup. SQL, użyj następującego polecenia. Polecenie tworzy kopię zapasową `testdb` bazy danych w pliku o nazwie `testdb_backup.sql` , który zawiera wszystkie instrukcje SQL wymagane do ponownego utworzenia bazy danych. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Aby wybrać określone tabele w bazie danych, aby utworzyć kopię zapasową, należy wyświetlić listę nazw tabel rozdzielonych spacjami. Na przykład, aby utworzyć kopię zapasową tylko tabel Tabela1 i tabela2 z elementu "TestDB", wykonaj następujące czynności: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Aby utworzyć kopię zapasową więcej niż jednej bazy danych, należy użyć przełącznika--Database i wyświetlić listę nazw baz danych rozdzielonych spacjami. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Tworzenie bazy danych na serwerze docelowym
Utwórz pustą bazę danych na docelowym serwerze Azure Database for MariaDB, na którym chcesz przeprowadzić migrację danych. Utwórz bazę danych za pomocą narzędzia, takiego jak MySQL Workbench. Baza danych może mieć taką samą nazwę, jak baza danych, która zawiera dane z danymi zrzutu, lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, Znajdź informacje o połączeniu w **przeglądzie** Azure Database for MariaDB.

![Znajdź informacje o połączeniu w Azure Portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do usługi MySQL Workbench.

![Parametry połączenia MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Przywracanie bazy danych MariaDB
Po utworzeniu docelowej bazy danych można użyć polecenia MySQL lub MySQL Workbench do przywrócenia danych do konkretnej nowo utworzonej bazy danych z pliku zrzutu.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
W tym przykładzie Przywróć dane do nowo utworzonej bazy danych na docelowym serwerze Azure Database for MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Eksportuj przy użyciu PHPMyAdmin
W celu wyeksportowania można użyć typowego narzędzia phpMyAdmin, które mogło już być zainstalowane lokalnie w danym środowisku. Aby wyeksportować bazę danych MariaDB przy użyciu PHPMyAdmin:
1. Otwórz phpMyAdmin.
2. Wybierz bazę danych. Kliknij nazwę bazy danych na liście po lewej stronie. 
3. Kliknij link **Eksportuj** . Zostanie wyświetlona nowa strona umożliwiająca wyświetlenie zrzutu bazy danych.
4. W obszarze eksport kliknij link **Zaznacz wszystko** , aby wybrać tabele w bazie danych. 
5. W obszarze Opcje SQL kliknij odpowiednie opcje. 
6. Kliknij opcję **Zapisz jako plik** i odpowiednią opcję kompresji, a następnie kliknij przycisk **Przejdź** . Zostanie wyświetlone okno dialogowe z monitem o zapisanie pliku lokalnie.

## <a name="import-using-phpmyadmin"></a>Importuj przy użyciu PHPMyAdmin
Importowanie bazy danych jest podobne do eksportowania. Wykonaj następujące czynności:
1. Otwórz phpMyAdmin. 
2. Na stronie Konfiguracja phpMyAdmin kliknij przycisk **Dodaj** , aby dodać serwer Azure Database for MariaDB. Podaj szczegóły połączenia i informacje logowania.
3. Utwórz odpowiednio nazwę bazy danych i wybierz ją po lewej stronie ekranu. Aby ponownie zapisać istniejącą bazę danych, kliknij nazwę bazy danych, zaznacz wszystkie pola wyboru obok nazwy tabeli, a następnie wybierz pozycję **Usuń, aby usunąć** istniejące tabele. 
4. Kliknij link **SQL** , aby wyświetlić stronę, na której można wpisywać polecenia SQL, lub Przekaż plik SQL. 
5. Użyj przycisku **Przeglądaj** , aby znaleźć plik bazy danych. 
6. Kliknij przycisk **Przejdź** , aby wyeksportować kopię zapasową, wykonać polecenia SQL i ponownie utworzyć bazę danych.

## <a name="next-steps"></a>Następne kroki
- [Połącz aplikacje z Azure Database for MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
