---
title: Importowanie i eksportowanie Azure Database for MySQL
description: W tym artykule opisano typowe sposoby importowania i eksportowania baz danych w programie Azure Database for MySQL przy użyciu narzędzi, takich jak MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 049a0ad45ea82210d8fac28db0fb3d067841bba4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625147"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrowanie bazy danych MySQL przy użyciu funkcji importowania i eksportowania

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

W tym artykule opisano dwa typowe podejścia do importowania i eksportowania danych na serwer Azure Database for MySQL przy użyciu programu MySQL Workbench.

Aby uzyskać szczegółowe i kompleksowe wskazówki dotyczące migracji, zobacz [zasoby przewodnika migracji](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). 

Inne scenariusze migracji można znaleźć w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem migrowania bazy danych MySQL należy:
- Utwórz [serwer Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Pobierz i zainstaluj program [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) lub inne narzędzie MySQL innej firmy na potrzeby importowania i eksportowania.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Tworzenie bazy danych na serwerze Azure Database for MySQL
Utwórz pustą bazę danych na serwerze Azure Database for MySQL za pomocą programu MySQL Workbench, TOAD lub Navicat. Baza danych może mieć taką samą nazwę jak baza danych, która zawiera dane z danymi zrzutu, lub można utworzyć bazę danych o innej nazwie.

Aby nawiązać połączenie, wykonaj następujące czynności:

1. W Azure Portal Poszukaj informacji o połączeniu w okienku **Przegląd** usługi Azure Database for MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Zrzut ekranu przedstawiający informacje o połączeniu z serwerem Azure Database for MySQL w Azure Portal.":::

1. Dodaj informacje o połączeniu do programu MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Parametry połączenia MySQL Workbench":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Ustalanie, kiedy należy używać technik importu i eksportu

> [!TIP]
> W przypadku scenariuszy, w których chcesz zrzucić i przywrócić całą bazę danych, zamiast tego użyj metody [zrzutu i przywracania](concepts-migrate-dump-restore.md) .

W poniższych scenariuszach za pomocą narzędzi MySQL można importować i eksportować bazy danych do bazy danych MySQL. W przypadku innych narzędzi przejdź do sekcji "metody migracji" (strona 22) [podręcznika migracji bazy danych MySQL do platformy Azure](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Jeśli musisz wybrać kilka tabel do zaimportowania z istniejącej bazy danych MySQL do bazy danych Azure MySQL, najlepiej użyć techniki importu i eksportu.  Dzięki temu można pominąć niepotrzebne tabele z migracji w celu zaoszczędzenia czasu i zasobów. Na przykład użyj `--include-tables` `--exclude-tables` przełącznika lub z [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) i `--tables` przełącznika z [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Gdy przenosisz obiekty bazy danych inne niż tabele, jawnie Twórz te obiekty. Uwzględnij ograniczenia (klucz podstawowy, klucz obcy i indeksy), widoki, funkcje, procedury, wyzwalacze i inne obiekty bazy danych, które chcesz migrować.
- Podczas migrowania danych z zewnętrznych źródeł danych innych niż baza danych MySQL Utwórz pliki proste i zaimportuj je za pomocą [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Zarówno jeden serwer, jak i elastyczny serwer obsługują *tylko aparat magazynu InnoDB*. Upewnij się, że wszystkie tabele w bazie danych korzystają z aparatu magazynu InnoDB podczas ładowania danych do usługi Azure Database for MySQL.
>
> Jeśli źródłowa baza danych korzysta z innego aparatu magazynu, przed migracją bazy danych przekonwertuj ją na aparat InnoDB. Na przykład jeśli masz aplikację WordPress lub Web, która korzysta z aparatu MyISAM, najpierw przekonwertuj tabele przez Migrowanie danych do tabel InnoDB. Użyj klauzuli, `ENGINE=INNODB` Aby ustawić aparat do tworzenia tabeli, a następnie Przenieś dane do zgodnej tabeli przed migracją.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Zalecenia dotyczące wydajności importu i eksportu

Aby zapewnić optymalną wydajność importowania i eksportowania danych, zalecamy wykonanie następujących czynności:
-   Utwórz indeksy klastrowane i klucze podstawowe przed załadowaniem danych. Załaduj dane w podstawowej kolejności kluczy.
-   Opóźnij Tworzenie indeksów pomocniczych do momentu załadowania danych.
-   Przed załadowaniem danych Wyłącz ograniczenia klucza obcego. Wyłączenie kontroli kluczy obcych zapewnia znaczący wzrost wydajności. Włącz ograniczenia i sprawdź dane po załadowaniu, aby zapewnić integralność referencyjną.
-   Równoległe ładowanie danych. Należy unikać zbyt dużej ilości równoległości, która spowodowałaby osiągnięcie limitu zasobów i monitorowanie zasobów przy użyciu metryk dostępnych w Azure Portal.
-   W razie potrzeby użyj tabel partycjonowanych.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Importowanie i eksportowanie danych przy użyciu programu MySQL Workbench
Istnieją dwa sposoby eksportowania i importowania danych w programie MySQL Workbench: z menu kontekstowego przeglądarki obiektów lub z okienka Nawigator. Każda metoda służy do innego celu.

> [!NOTE]
> Jeśli dodajesz połączenie z pojedynczym serwerem MySQL lub elastycznym serwerem (wersja zapoznawcza) na Workbench MySQL, wykonaj następujące czynności:
> - W przypadku pojedynczego serwera MySQL upewnij się, że nazwa użytkownika ma format *\<username@servername>* .
> - W przypadku serwera elastycznego MySQL używaj *\<username>* tylko. Jeśli używasz *\<username@servername>* do nawiązywania połączenia, połączenie zakończy się niepowodzeniem.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Uruchom Kreatora eksportu i importu danych z tabeli z menu kontekstowego przeglądarki obiektów

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Zrzut ekranu przedstawiający polecenia Kreatora eksportu i importu MySQL Workbench w menu kontekstowym przeglądarki obiektów.":::

Kreatory danych tabeli obsługują operacje importowania i eksportowania przy użyciu plików CSV i JSON. Kreatorzy zawierają kilka opcji konfiguracji, takich jak separatory, wybór kolumn i wybór kodowania. Każdy Kreator można uruchomić na lokalnym lub zdalnie połączonym serwerze MySQL. Akcja Importuj obejmuje mapowanie tabeli, kolumny i typu.

Aby uzyskać dostęp do tych kreatorów z menu kontekstowego przeglądarki obiektów, kliknij prawym przyciskiem myszy tabelę, a następnie wybierz **Kreatora eksportu danych tabeli** lub **Kreatora importu danych tabeli**.

#### <a name="the-table-data-export-wizard"></a>Kreator eksportu danych tabeli

Aby wyeksportować tabelę do pliku CSV:

1. Kliknij prawym przyciskiem myszy tabelę bazy danych, która ma zostać wyeksportowana.
1. Wybierz **Kreatora eksportu danych tabeli**. Wybierz kolumny, które mają zostać wyeksportowane, przesunięcie wierszy (jeśli istnieje) i liczbę (jeśli istnieje).
1. W okienku **Wybierz dane do eksportu** wybierz pozycję **dalej**. Wybierz ścieżkę pliku, plik CSV lub typ pliku JSON. Należy również wybrać separator wierszy, metodę otaczających ciągów i separator pola.
1. W okienku **Wybierz lokalizację pliku wyjściowego** wybierz pozycję **dalej**.
1. W okienku **Eksportuj dane** wybierz pozycję **dalej**.

#### <a name="the-table-data-import-wizard"></a>Kreator importu danych tabeli

Aby zaimportować tabelę z pliku CSV:

1. Kliknij prawym przyciskiem myszy tabelę bazy danych do zaimportowania.
1. Wyszukaj i wybierz plik CSV, który ma zostać zaimportowany, a następnie wybierz przycisk **dalej**.
1. Wybierz tabelę docelową (nową lub istniejącą), zaznacz lub wyczyść pole wyboru **Obetnij tabelę przed importem** , a następnie wybierz przycisk **dalej**.
1. Wybierz kodowanie i kolumny do zaimportowania, a następnie wybierz przycisk **dalej**.
1. W okienku **Importuj dane** wybierz pozycję **dalej**. Kreator importuje dane.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Uruchamianie kreatorów eksportu i importu danych SQL z okienka Nawigator
Użyj kreatora, aby wyeksportować lub zaimportować dane SQL, które są generowane z Workbench MySQL lub z polecenia mysqldump. Możesz uzyskać dostęp do kreatorów z okienka **nawigatora** lub wybrać opcję **serwer** z menu głównego.

#### <a name="export-data"></a>Eksportowanie danych

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Zrzut ekranu przedstawiający używanie okienka Nawigator do wyświetlania okienka eksportu danych w programie MySQL Workbench.":::

Aby wyeksportować dane MySQL, można użyć okienka **Eksportowanie danych** .

1. W programie MySQL Workbench w okienku **Nawigator** wybierz pozycję **eksport danych**.

1. W okienku **eksport danych** wybierz każdy schemat, który chcesz wyeksportować.
 
   Dla każdego schematu można wybrać określone obiekty schematu lub tabele do wyeksportowania. Opcje konfiguracji obejmują eksport do folderu projektu lub samodzielnego pliku SQL, Zrzuć procedury składowane i zdarzenia lub Pomiń dane tabeli.

   Alternatywnie możesz użyć opcji **Eksportuj zestaw wyników** , aby wyeksportować konkretny zestaw wyników w edytorze SQL do innego formatu, takiego jak CSV, JSON, HTML i XML.

1. Wybierz obiekty bazy danych do wyeksportowania i skonfiguruj powiązane opcje.
1. Wybierz pozycję **Odśwież** , aby załadować bieżące obiekty.
1. Opcjonalnie wybierz pozycję **Opcje zaawansowane** w prawym górnym rogu, aby uściślić operację eksportowania. Na przykład Dodaj blokady tabeli, użyj zamiany zamiast instrukcji INSERT oraz identyfikatorów cudzysłowów z znakami kreski.
1. Wybierz pozycję **Rozpocznij eksportowanie** , aby rozpocząć proces eksportowania.


#### <a name="import-data"></a>Importowanie danych

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Zrzut ekranu przedstawiający używanie okienka Nawigator do wyświetlania okienka importowania danych w programie MySQL Workbench.":::

Za pomocą okienka **importowania danych** można importować lub przywracać eksportowane dane z operacji eksportu danych lub z polecenia mysqldump.

1. W programie MySQL Workbench w okienku **Nawigator** wybierz pozycję **Eksportuj/Przywróć dane**.
1. Wybierz folder projektu lub własny plik SQL, wybierz schemat do zaimportowania lub wybierz przycisk **Nowy** , aby zdefiniować nowy schemat.
1. Wybierz pozycję **Rozpocznij Importowanie** , aby rozpocząć proces importowania.

## <a name="next-steps"></a>Następne kroki
- Inne podejście do migracji można znaleźć w temacie [Migrowanie bazy danych MySQL do usługi Azure Database for MySQL przy użyciu zrzutów i przywracania](concepts-migrate-dump-restore.md).
- Aby uzyskać więcej informacji na temat migrowania baz danych do usługi Azure Database for MySQL, zobacz [Przewodnik po migracji bazy danych](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
