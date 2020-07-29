---
title: Importowanie i eksportowanie Azure Database for MySQL
description: W tym artykule opisano typowe sposoby importowania i eksportowania baz danych w programie Azure Database for MySQL przy użyciu narzędzi, takich jak MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78163730"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrowanie bazy danych MySQL przy użyciu funkcji importowania i eksportowania
W tym artykule opisano dwa typowe podejścia do importowania i eksportowania danych na serwer Azure Database for MySQL przy użyciu programu MySQL Workbench. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby krokowo poprowadzić ten przewodnik, musisz:
- Serwer Azure Database for MySQL, wykonując następujące czynności: [Utwórz serwer Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Narzędzia MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy do wykonania importu/eksportu.

## <a name="use-common-tools"></a>Korzystanie z popularnych narzędzi
Korzystaj z popularnych narzędzi i narzędzi, takich jak MySQL Workbench lub mysqldump, aby zdalnie łączyć i importować dane oraz eksportować je do Azure Database for MySQL. 

Użyj tych narzędzi na komputerze klienckim z połączeniem internetowym, aby nawiązać połączenie z usługą Azure Database for MySQL. Użyj połączenia szyfrowanego za pośrednictwem protokołu SSL w celu uzyskania najlepszych rozwiązań z zakresu zabezpieczeń, zgodnie z opisem w temacie [Konfigurowanie łączności SSL w Azure Database for MySQL](concepts-ssl-connection-security.md).

Nie ma potrzeby przenoszenia plików importu i eksportu do żadnej specjalnej lokalizacji w chmurze podczas migracji do Azure Database for MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Tworzenie bazy danych na serwerze Azure Database for MySQL
Utwórz pustą bazę danych na serwerze Azure Database for MySQL, na którym chcesz przeprowadzić migrację danych. Utwórz bazę danych za pomocą narzędzia, takiego jak MySQL Workbench, TOAD lub Navicat. Baza danych może mieć taką samą nazwę jak baza danych, która zawiera dane z danymi zrzutu, lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, Znajdź informacje o połączeniu w **przeglądzie** Azure Database for MySQL.

![Znajdź informacje o połączeniu w Azure Portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Dodaj informacje o połączeniu do programu MySQL Workbench.

![Parametry połączenia MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Ustalanie, kiedy należy używać technik importu i eksportu zamiast zrzutu i przywracania
Użyj narzędzi MySQL do importowania i eksportowania baz danych do bazy danych Azure MySQL w następujących scenariuszach. W innych scenariuszach może być korzystne użycie metody [zrzutu i przywracania](concepts-migrate-dump-restore.md) . 

- Jeśli musisz wybrać kilka tabel do zaimportowania z istniejącej bazy danych MySQL do bazy danych Azure MySQL, najlepiej użyć techniki importu i eksportu.  Dzięki temu można pominąć niepotrzebne tabele z migracji w celu zaoszczędzenia czasu i zasobów. Na przykład użyj `--include-tables` `--exclude-tables` przełącznika lub z [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) i `--tables` przełącznika z [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Gdy przenosisz obiekty bazy danych inne niż tabele, jawnie Twórz te obiekty. Uwzględnij ograniczenia (klucz podstawowy, klucz obcy, indeksy), widoki, funkcje, procedury, wyzwalacze i inne obiekty bazy danych, które chcesz migrować.
- Podczas migrowania danych z zewnętrznych źródeł danych innych niż baza danych MySQL Utwórz pliki proste i zaimportuj je za pomocą [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Upewnij się, że wszystkie tabele w bazie danych korzystają z aparatu magazynu InnoDB podczas ładowania danych do Azure Database for MySQL. Azure Database for MySQL obsługuje tylko aparat magazynu InnoDB, więc nie obsługuje alternatywnych aparatów pamięci masowej. Jeśli tabele wymagają alternatywnych aparatów pamięci masowej, przed rozpoczęciem migracji do Azure Database for MySQL należy przekonwertować je tak, aby korzystały z formatu aparatu InnoDB. 

Na przykład jeśli masz aplikację WordPress lub Web, która korzysta z aparatu MyISAM, najpierw przekonwertuj tabele przez Migrowanie danych do tabel InnoDB. Następnie Przywróć do Azure Database for MySQL. Użyj klauzuli, `ENGINE=INNODB` Aby ustawić aparat do tworzenia tabeli, a następnie Przenieś dane do zgodnej tabeli przed migracją. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Zalecenia dotyczące wydajności importu i eksportu
-   Utwórz indeksy klastrowane i klucze podstawowe przed załadowaniem danych. Załaduj dane w podstawowej kolejności kluczy. 
-   Opóźnienie tworzenia indeksów pomocniczych do momentu załadowania danych. Utwórz wszystkie indeksy pomocnicze po załadowaniu. 
-   Przed załadowaniem Wyłącz ograniczenia klucza obcego. Wyłączenie kontroli kluczy obcych zapewnia znaczący wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu, aby zapewnić integralność referencyjną.
-   Równoległe ładowanie danych. Należy unikać zbyt dużej ilości równoległości, która spowodowałaby osiągnięcie limitu zasobów i monitorowanie zasobów przy użyciu metryk dostępnych w Azure Portal. 
-   W razie potrzeby użyj tabel partycjonowanych.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importowanie i eksportowanie za pomocą programu MySQL Workbench
Istnieją dwa sposoby eksportowania i importowania danych w programie MySQL Workbench. Każdy z nich działa w innym celu. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Kreatory eksportu i importu danych z tabeli z menu kontekstowego przeglądarki obiektów
![Kreatorzy Workbench MySQL w menu kontekstowym przeglądarki obiektów](./media/concepts-migrate-import-export/p1.png)

Kreatory dla danych tabeli obsługują operacje importowania i eksportowania przy użyciu plików CSV i JSON. Obejmują one kilka opcji konfiguracji, takich jak separatory, wybór kolumn i wybór kodowania. Każdy Kreator można wykonać względem lokalnego lub zdalnie połączonego serwera MySQL. Akcja Importuj obejmuje mapowanie tabeli, kolumny i typu. 

Aby uzyskać dostęp do tych kreatorów z menu kontekstowego przeglądarki obiektów, kliknij prawym przyciskiem myszy tabelę. Następnie wybierz **Kreatora eksportu danych tabeli** lub **Kreatora importu danych tabeli**. 

#### <a name="table-data-export-wizard"></a>Kreator eksportu danych tabeli
Poniższy przykład eksportuje tabelę do pliku CSV: 
1. Kliknij prawym przyciskiem myszy tabelę bazy danych, która ma zostać wyeksportowana. 
2. Wybierz **Kreatora eksportu danych tabeli**. Wybierz kolumny, które mają zostać wyeksportowane, przesunięcie wierszy (jeśli istnieje) i liczbę (jeśli istnieje). 
3. Na stronie **Wybierz dane do eksportowania** kliknij przycisk **dalej**. Wybierz ścieżkę pliku, plik CSV lub typ pliku JSON. Należy również wybrać separator wierszy, metodę otaczających ciągów i separator pola. 
4. Na stronie **Wybierz lokalizację pliku wyjściowego** kliknij przycisk **dalej**. 
5. Na stronie **Eksportuj dane** kliknij przycisk **dalej**.

#### <a name="table-data-import-wizard"></a>Kreator importu danych tabeli
Poniższy przykład importuje tabelę z pliku CSV:
1. Kliknij prawym przyciskiem myszy tabelę bazy danych do zaimportowania. 
2. Wyszukaj i wybierz plik CSV, który ma zostać zaimportowany, a następnie kliknij przycisk **dalej**. 
3. Wybierz tabelę docelową (nową lub istniejącą) i zaznacz lub wyczyść pole wyboru **Obetnij tabelę przed importem** . Kliknij przycisk **Dalej**.
4. Wybierz opcję kodowanie i kolumny do zaimportowania, a następnie kliknij przycisk **dalej**. 
5. Na stronie **Importuj dane** kliknij przycisk **dalej**. Kreator importuje odpowiednie dane.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Kreatory eksportu i importu danych SQL z okienka Nawigator
Użyj kreatora, aby wyeksportować lub zaimportować kod SQL wygenerowany z Workbench MySQL lub wygenerowany z polecenia mysqldump. Uzyskaj dostęp do tych kreatorów z okienka **Nawigator** lub wybierając opcję **serwer** z menu głównego. Następnie wybierz pozycję **eksport danych** lub **Import danych**. 

#### <a name="data-export"></a>Eksport danych
![Eksport danych MySQL Workbench przy użyciu okienka Nawigator](./media/concepts-migrate-import-export/p2.png)

Karta **Eksportowanie danych** służy do eksportowania danych MySQL. 
1. Zaznacz każdy schemat, który chcesz wyeksportować, opcjonalnie wybierz konkretne obiekty schematu/tabele z każdego schematu i Wygeneruj eksport. Opcje konfiguracji obejmują eksport do folderu projektu lub samodzielnego pliku SQL, Zrzuć procedury składowane i zdarzenia lub Pomiń dane tabeli. 
 
   Alternatywnie możesz użyć opcji **Eksportuj zestaw wyników** , aby wyeksportować konkretny zestaw wyników w edytorze SQL do innego formatu, takiego jak CSV, JSON, HTML i XML. 
3. Wybierz obiekty bazy danych do wyeksportowania i skonfiguruj powiązane opcje.
4. Kliknij przycisk **Odśwież** , aby załadować bieżące obiekty.
5. Opcjonalnie możesz otworzyć kartę **Opcje zaawansowane** , aby uściślić operację eksportowania. Na przykład Dodaj blokady tabeli, użyj zamiany zamiast instrukcji INSERT oraz identyfikatorów cudzysłowów z znakami kreski.
6. Kliknij przycisk **Rozpocznij eksportowanie** , aby rozpocząć proces eksportowania.


#### <a name="data-import"></a>Importowanie danych
![Import danych MySQL Workbench za pomocą nawigatora zarządzania](./media/concepts-migrate-import-export/p3.png)

Karta **Importowanie danych** służy do importowania lub przywracania eksportowanych danych z operacji eksportu danych lub polecenia mysqldump. 
1. Wybierz folder projektu lub własny plik SQL, wybierz schemat do zaimportowania lub wybierz pozycję **Nowy** , aby zdefiniować nowy schemat. 
2. Kliknij przycisk **Rozpocznij Importowanie** , aby rozpocząć proces importowania.

## <a name="next-steps"></a>Następne kroki
- Jako inne podejście migracji należy przeczytać temat [Migrowanie bazy danych MySQL przy użyciu zrzutów i przywracania w Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do Azure Database for MySQL, zobacz [Przewodnik po migracji bazy danych](https://aka.ms/datamigration). 
