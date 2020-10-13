---
title: Łączenie z programem Excel
description: Dowiedz się, jak połączyć program Microsoft Excel z bazą danych w Azure SQL Database lub wystąpieniu zarządzanym usługi Azure SQL. Importowanie danych do programu Excel, raportowanie i eksploracja danych.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444043"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>Łączenie programu Excel z bazą danych w Azure SQL Database lub wystąpieniu zarządzanym usługi Azure SQL i Tworzenie raportu
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Program Excel można połączyć z bazą danych, a następnie zaimportować dane oraz utworzyć tabele i wykresy na podstawie wartości w bazie danych. W tym samouczku skonfigurujesz połączenie między programem Excel i tabelą bazy danych, zapiszesz plik przechowujący dane oraz informacje o połączeniu dla programu Excel, a następnie utworzysz wykres przestawny z wartościami bazy danych.

Przed rozpoczęciem należy utworzyć bazę danych. Jeśli go nie masz, zobacz [Tworzenie bazy danych w Azure SQL Database](single-database-create-quickstart.md) i [Tworzenie zapory IP na poziomie serwera](firewall-create-server-level-portal-quickstart.md) , aby uzyskać bazę danych z przykładowymi danymi, które można uruchomić w ciągu kilku minut.

W tym artykule zaimportujesz przykładowe dane do programu Excel z tego artykułu, ale możesz wykonać podobne kroki z własnymi danymi.

Potrzebna będzie również kopia programu Excel. W tym artykule wykorzystano program [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-and-load-data"></a>Łączenie programu Excel i ładowanie danych

1. Aby połączyć program Excel z bazą danych w SQL Database, Otwórz program Excel, a następnie utwórz nowy skoroszyt lub Otwórz istniejący skoroszyt programu Excel.
2. Na pasku menu w górnej części strony wybierz kartę **dane** , wybierz pozycję **Pobierz dane**, wybierz pozycję z platformy Azure, a następnie wybierz pozycję **z Azure SQL Database**.

   ![Wybierz źródło danych: Połącz program Excel z SQL Database.](./media/connect-excel/excel_data_source.png)

3. W oknie dialogowym **SQL Server Database** wpisz **nazwę serwera** , z którym chcesz nawiązać połączenie w postaci <*servername* > **. Database.Windows.NET**. Na przykład **msftestserver.Database.Windows.NET**. Opcjonalnie wprowadź nazwę bazy danych. Wybierz **przycisk OK** , aby otworzyć okno poświadczenia.

   ![Okno dialogowe łączenie z serwerem bazy danych](./media/connect-excel/server-name.png)

4. W oknie dialogowym **SQL Server Database** wybierz pozycję **baza danych** po lewej stronie, a następnie wprowadź **nazwę użytkownika** i **hasło** dla serwera, z którym chcesz nawiązać połączenie. Wybierz pozycję **Połącz** , aby otworzyć **Nawigator**.

   ![Wpisywanie nazwy serwera i poświadczeń logowania](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > W zależności od używanego środowiska sieciowego może nie być możliwe nawiązanie połączenia lub utratę połączenia, jeśli serwer nie zezwoli na ruch z adresu IP klienta. Przejdź do witryny [Azure Portal](https://portal.azure.com/), kliknij serwery SQL, kliknij serwer, którego używasz, kliknij zaporę systemu w ustawieniach i dodaj swój adres IP klienta. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to configure firewall settings](firewall-configure.md) (Jak skonfigurować ustawienia zapory).

5. W **Nawigatorze**wybierz bazę danych, z której chcesz korzystać z listy, wybierz tabele lub widoki, z którymi chcesz współpracować (wybieramy **vGetAllCategories**), a następnie wybierz pozycję **Załaduj** , aby przenieść dane z bazy danych do arkusza kalkulacyjnego programu Excel.

    ![Wybierz bazę danych i tabelę.](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importowanie danych do programu Excel i tworzenie wykresu przestawnego

Po nawiązaniu połączenia masz kilka różnych opcji dotyczących ładowania danych. Na przykład poniższe kroki tworzą wykres przestawny na podstawie danych znalezionych w bazie danych w SQL Database.

1. Wykonaj kroki opisane w poprzedniej sekcji, ale tym razem zamiast wybierania **obciążenia**, wybierz pozycję **Załaduj do na** liście rozwijanej **ładowania** .
2. Następnie wybierz sposób wyświetlania tych danych w skoroszycie. Wybrano **Wykres przestawny**. Można również utworzyć **Nowy arkusz** lub wybrać opcję **Dodaj te dane do modelu danych**. Więcej informacji o modelach danych można znaleźć w temacie [Tworzenie modelu danych w programie Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B).

    ![Wybieranie formatu danych w programie Excel](./media/connect-excel/import-data.png)

    Arkusz zawiera teraz pustą tabelę przestawną i wykres.
3. W obszarze **Pola tabeli przestawnej** zaznacz wszystkie pola wyboru dla pól do wyświetlenia.

    ![Skonfiguruj raport bazy danych.](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> Jeśli chcesz połączyć inne skoroszyty i arkusze programu Excel z bazą danych, wybierz kartę **dane** , a następnie wybierz pozycję **ostatnie źródła** , aby uruchomić okno dialogowe **ostatnie źródła** . Z tego miejsca wybierz utworzone połączenie z listy, a następnie kliknij przycisk **Otwórz**.
> ![Ostatnie źródła — okno dialogowe](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Tworzenie stałego połączenia przy użyciu pliku odc

Aby trwale zapisać szczegóły połączenia, można utworzyć plik odc i wybrać opcję wyboru w oknie dialogowym **istniejące połączenia** .

1. Na pasku menu w górnej części strony wybierz kartę **dane** , a następnie wybierz pozycję **istniejące połączenia** , aby uruchomić okno dialogowe **istniejące połączenia** .
   1. Wybierz pozycję Przeglądaj, aby **uzyskać więcej informacji** , aby otworzyć okno dialogowe **Wybieranie źródła danych** .
   2. Wybierz plik **+ NewSqlServerConnection. odc** , a następnie wybierz pozycję **Otwórz** , aby otworzyć **Kreatora połączenia danych**.

      ![Nowe połączenie — okno dialogowe](./media/connect-excel/new-connection.png)

2. W **Kreatorze połączenia danych**wpisz nazwę serwera i poświadczenia SQL Database. Wybierz opcję **Dalej**.
   1. Wybierz z listy rozwijanej bazę danych zawierającą dane.
   2. Wybierz tabelę lub widok, który Cię interesuje. Wybrano vGetAllCategories.
   3. Wybierz opcję **Dalej**.

      ![Kreator połączenia danych](./media/connect-excel/data-connection-wizard.png)

3. Wybierz lokalizację pliku, **nazwę pliku**i **przyjazną nazwę** na następnym ekranie Kreatora połączenia danych. Możesz również zapisać hasło w pliku, ale może to spowodować, że dane nie są niepożądane. Wybierz pozycję **Zakończ** , gdy wszystko będzie gotowe.

    ![Zapisz połączenie danych](./media/connect-excel/save-data-connection.png)

4. Wybierz sposób importowania danych. Wybrano opcję wykonania tabeli przestawnej. Możesz również zmodyfikować właściwości połączenia, wybierając pozycję **Właściwości**. Wybierz **przycisk OK** , gdy wszystko będzie gotowe. Jeśli nie chcesz zapisać hasła przy użyciu pliku, zostanie wyświetlony monit o wprowadzenie poświadczeń.

    ![Importowanie danych](./media/connect-excel/import-data2.png)

5. Sprawdź, czy nowe połączenie zostało zapisane poprzez rozwinięcie karty **dane** , a następnie wybierz pozycję **istniejące połączenia**.

    ![Istniejące połączenie](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak nawiązywać połączenia i wykonywać zapytania przy użyciu SQL Server Management Studio](connect-query-ssms.md) na potrzeby zaawansowanego wykonywania zapytań i analizy.
* Dowiedz się, jakie zalety mają [pule elastyczne](elastic-pool-overview.md).
* Dowiedz się [, jak utworzyć aplikację sieci Web, która łączy się z Azure SQL Database na zapleczu](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
