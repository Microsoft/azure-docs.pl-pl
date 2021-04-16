---
title: Importowanie i eksportowanie — Azure Database for MySQL
description: W tym artykule wyjaśniono typowe sposoby importowania i eksportowania baz danych w programie Azure Database for MySQL przy użyciu narzędzi, takich jak MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389778"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrowanie bazy danych MySQL przy użyciu funkcji importowania i eksportowania

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

W tym artykule wyjaśniono dwa typowe podejścia do importowania i eksportowania danych na serwer Azure Database for MySQL przy użyciu aplikacji MySQL Workbench.

Szczegółowe i kompleksowe wskazówki dotyczące migracji można znaleźć w [zasobach przewodnika po migracji.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) 

W przypadku innych scenariuszy migracji zobacz [Przewodnik migracji bazy danych](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migracji bazy danych MySQL należy:
- Utwórz serwer [Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Pobierz i zainstaluj [program MySQL Workbench](https://dev.mysql.com/downloads/workbench/) lub narzędzie MySQL innej firmy do importowania i eksportowania.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Tworzenie bazy danych na Azure Database for MySQL serwera
Utwórz pustą bazę danych na Azure Database for MySQL przy użyciu aplikacji MySQL Workbench, Toad lub Navicat. Baza danych może mieć taką samą nazwę jak baza danych zawierająca zrzucane dane lub można utworzyć bazę danych o innej nazwie.

Aby nałączyć połączenie, wykonaj następujące czynności:

1. W Azure Portal odszukaj informacje o połączeniu w **okienku Przegląd** Azure Database for MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Zrzut ekranu przedstawiający Azure Database for MySQL połączenia z serwerem w Azure Portal.":::

1. Dodaj informacje o połączeniu do programu MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Zrzut ekranu przedstawiający ciąg połączenia programu MySQL Workbench.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Określanie, kiedy należy używać technik importowania i eksportowania

> [!TIP]
> W scenariuszach, w których chcesz zrzucić i przywrócić całą bazę danych, zamiast tego użyj metody [zrzutu i](concepts-migrate-dump-restore.md) przywracania.

W poniższych scenariuszach użyj narzędzi MySQL do importowania i eksportowania baz danych do bazy danych MySQL. W przypadku innych narzędzi przejdź do sekcji "Metody migracji" (strona 22) przewodnika po migracji bazy danych [MySQL do usługi Azure Database.](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf) 

- Jeśli musisz selektywnie wybrać kilka tabel do zaimportowania z istniejącej bazy danych MySQL do bazy danych Azure MySQL, najlepiej użyć techniki importowania i eksportowania. W ten sposób można pominąć wszelkie niepotrzebnych tabel z migracji, aby zaoszczędzić czas i zasoby. Na przykład użyj `--include-tables` przełącznika lub `--exclude-tables` z [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)i przełącznika `--tables` z [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- W przypadku przenoszenia obiektów bazy danych innych niż tabele należy jawnie utworzyć te obiekty. Uwzględnij ograniczenia (klucz podstawowy, klucz obcy i indeksy), widoki, funkcje, procedury, wyzwalacze i inne obiekty bazy danych, które chcesz migrować.
- W przypadku migrowania danych z zewnętrznych źródeł danych innych niż baza danych MySQL utwórz pliki płaskie i zaimportuj je przy użyciu [programu mysqlimport.](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)

> [!Important]
> Zarówno pojedynczy serwer, jak i serwer elastyczny obsługują tylko aparat magazynu InnoDB. Upewnij się, że wszystkie tabele w bazie danych używają aparatu magazynu InnoDB podczas ładowania danych do usługi Azure Database for MySQL.
>
> Jeśli źródłową bazę danych używa inny aparat magazynu, przekonwertuj go na aparat InnoDB przed migracją bazy danych. Jeśli na przykład masz aplikację wordPress lub aplikację internetową korzystającą z aparatu MyISAM, najpierw przekonwertuj tabele, migrując dane do tabel InnoDB. Użyj klauzuli , aby ustawić aparat tworzenia tabeli, a następnie przenieś dane do zgodnej tabeli `ENGINE=INNODB` przed migracją.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Zalecenia dotyczące wydajności importowania i eksportowania

Aby uzyskać optymalną wydajność importowania i eksportowania danych, zalecamy wykonanie następujących czynności:
-   Przed załadowaniem danych utwórz indeksy klastrowane i klucze podstawowe. Załaduj dane w kolejności kluczy podstawowych.
-   Opóźnij tworzenie indeksów pomocniczych do momentu załadowania danych.
-   Wyłącz ograniczenia klucza obcego przed załadowaniem danych. Wyłączenie kontroli klucza obcego zapewnia znaczny wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu w celu zapewnienia więzów integralności.
-   Równoległe ładowanie danych. Unikaj zbyt dużej równoległości, która spowoduje przekroenie limitu zasobów, i monitoruj zasoby przy użyciu metryk dostępnych w Azure Portal.
-   W razie potrzeby użyj tabel partycjonowanych.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importowanie i eksportowanie danych przy użyciu aplikacji MySQL Workbench
Istnieją dwa sposoby eksportowania i importowania danych w aplikacji MySQL Workbench: z menu kontekstowego przeglądarki obiektów lub z okienka Nawigator. Każda metoda ma inne przeznaczenie.

> [!NOTE]
> Jeśli dodajesz połączenie z pojedynczym serwerem MySQL lub serwerem elastycznym (wersja zapoznawcza) w aplikacji MySQL Workbench, wykonaj następujące czynności:
> - W przypadku pojedynczego serwera MySQL upewnij się, że nazwa użytkownika ma format *\<username@servername>* .
> - W przypadku serwera elastycznego MySQL użyj *\<username>* tylko opcji . Jeśli *\<username@servername>* użyjemy funkcji do nawiązania połączenia, połączenie nie powiedzie się.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Uruchamianie kreatorów eksportu i importowania danych tabeli z menu kontekstowego przeglądarki obiektów

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Zrzut ekranu przedstawiający polecenia kreatora eksportowania i importowania aplikacji MySQL Workbench w menu kontekstowym przeglądarki obiektów.":::

Kreatorzy danych tabeli obsługują operacje importowania i eksportowania przy użyciu plików CSV i JSON. Kreatory obejmują kilka opcji konfiguracji, takich jak separatory, wybór kolumn i wybór kodowania. Każdego kreatora można uruchomić na lokalnych lub zdalnie połączonych serwerach MySQL. Akcja importu obejmuje mapowanie tabel, kolumn i typów.

Aby uzyskać dostęp do tych kreatorów z menu kontekstowego przeglądarki obiektów, kliknij prawym przyciskiem myszy tabelę, a następnie wybierz pozycję Kreator eksportu danych tabeli lub Kreator **importu danych tabeli.** 

#### <a name="the-table-data-export-wizard"></a>Kreator eksportu danych tabeli

Aby wyeksportować tabelę do pliku CSV:

1. Kliknij prawym przyciskiem myszy tabelę bazy danych do wyeksportowania.
1. Wybierz **Kreatora eksportu danych tabeli.** Wybierz kolumny do wyeksportowania, przesunięcie wiersza (jeśli jest) i liczbę (jeśli są).
1. W **okienku Wybierz dane do eksportu** wybierz pozycję **Dalej.** Wybierz ścieżkę pliku, plik CSV lub typ pliku JSON. Wybierz również separator wiersza, metodę otaczającego ciągi i separator pola.
1. W **okienku Wybierz lokalizację pliku wyjściowego** wybierz pozycję **Dalej.**
1. W **okienku Eksportuj dane** wybierz pozycję **Dalej.**

#### <a name="the-table-data-import-wizard"></a>Kreator importu danych tabeli

Aby zaimportować tabelę z pliku CSV:

1. Kliknij prawym przyciskiem myszy tabelę bazy danych do zaimportowania.
1. Poszukaj i wybierz plik CSV do zaimportowania, a następnie wybierz pozycję **Dalej.**
1. Zaznacz tabelę docelową (nową lub istniejącą), zaznacz lub wyczyść pole wyboru **Truncate table before import** (Tabela Truncate przed importowaniem), a następnie wybierz pozycję Next **(Dalej).**
1. Wybierz kodowanie i kolumny do zaimportowania, a następnie wybierz pozycję **Dalej.**
1. W **okienku Importowanie** danych wybierz pozycję **Dalej.** Kreator importuje dane.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Uruchamianie kreatorów eksportowania i importowania danych SQL w okienku Nawigator
Użyj kreatora, aby wyeksportować lub zaimportować dane SQL wygenerowane z aplikacji MySQL Workbench lub za pomocą polecenia mysqldump. Dostęp do kreatorów można uzyskać w okienku **Nawigator** lub wybrać **pozycję Serwer** z menu głównego.

#### <a name="export-data"></a>Eksportowanie danych

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Zrzut ekranu przedstawiający wyświetlanie okienka Eksportowanie danych w aplikacji MySQL Workbench przy użyciu okienka Nawigator.":::

Aby wyeksportować dane MySQL, możesz użyć okienka Eksportowanie danych. 

1. W aplikacji MySQL Workbench w okienku **Nawigator** wybierz **pozycję Eksport danych.**

1. W **okienku Eksport** danych wybierz każdy schemat, który chcesz wyeksportować.
 
   Dla każdego schematu można wybrać określone obiekty schematu lub tabele do wyeksportowania. Opcje konfiguracji obejmują eksportowanie do folderu projektu lub samodzielnego pliku SQL, zrzucanie przechowywanych procedur i zdarzeń lub pomijanie danych tabeli.

   Możesz też użyć polecenia **Eksportuj** zestaw wyników, aby wyeksportować określony zestaw wyników w edytorze SQL do innego formatu, takiego jak CSV, JSON, HTML i XML.

1. Wybierz obiekty bazy danych do wyeksportowania i skonfiguruj powiązane opcje.
1. Wybierz **pozycję Odśwież,** aby załadować bieżące obiekty.
1. Opcjonalnie wybierz pozycję **Opcje zaawansowane** w prawym górnym rogu, aby uściślić operację eksportowania. Możesz na przykład dodać blokady tabeli, użyć zamiast instrukcji i identyfikatorów `replace` `insert` cudzysłowów ze znakami cudzysłowu.
1. Wybierz **pozycję Rozpocznij eksportowanie,** aby rozpocząć proces eksportowania.


#### <a name="import-data"></a>Importowanie danych

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Zrzut ekranu przedstawiający wyświetlanie okienka Importowanie danych w aplikacji MySQL Workbench przy użyciu okienka Nawigator.":::

Za pomocą okienka **Importowanie** danych można zaimportować lub przywrócić wyeksportowane dane z operacji eksportu danych lub z polecenia mysqldump.

1. W aplikacji MySQL Workbench w okienku **Nawigator** wybierz pozycję **Eksport/przywracanie danych.**
1. Wybierz folder projektu lub samodzielny plik SQL, wybierz schemat do  zaimportowania lub wybierz przycisk Nowy, aby zdefiniować nowy schemat.
1. Wybierz **pozycję Rozpocznij importowanie,** aby rozpocząć proces importowania.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać inne podejście do migracji, zobacz Migrowanie bazy danych MySQL do usługi [Azure Database for MySQL przy użyciu zrzutu i przywracania](concepts-migrate-dump-restore.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do usługi Azure Database for MySQL, zobacz [Przewodnik migracji bazy danych](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
