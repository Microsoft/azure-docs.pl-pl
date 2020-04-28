---
title: Łączenie programu Excel z pojedynczą bazą danych
description: Dowiedz się, jak połączyć program Microsoft Excel z pojedynczą bazą danych w usłudze Azure SQL Database. Importowanie danych do programu Excel, raportowanie i eksploracja danych.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73827127"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Łączenie programu Excel z pojedynczą bazą danych w usłudze Azure SQL Database i Tworzenie raportu

Połącz program Excel z pojedynczą bazą danych w Azure SQL Database i Importuj dane oraz twórz tabele i wykresy na podstawie wartości w bazie danych. W tym samouczku skonfigurujesz połączenie między programem Excel i tabelą bazy danych, zapiszesz plik przechowujący dane oraz informacje o połączeniu dla programu Excel, a następnie utworzysz wykres przestawny z wartościami bazy danych.

Aby rozpocząć, musisz mieć pojedynczą bazę danych. Jeśli go nie masz, zobacz [Tworzenie pojedynczej bazy danych](sql-database-single-database-get-started.md) i [Tworzenie zapory IP na poziomie serwera](sql-database-server-level-firewall-rule.md) , aby uzyskać pojedynczą bazę danych z przykładowymi danymi, które można uruchomić w ciągu kilku minut.

W tym artykule zaimportujesz przykładowe dane do programu Excel z tego artykułu, ale możesz wykonać podobne kroki z własnymi danymi.

Potrzebna będzie również kopia programu Excel. W tym artykule wykorzystano program [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Łączenie programu Excel z bazą danych SQL i ładowanie danych

1. Aby połączyć program Excel z bazą danych SQL, otwórz program Excel, a następnie utwórz nowy skoroszyt lub otwórz istniejący skoroszyt programu Excel.
2. Na pasku menu w górnej części strony wybierz kartę **dane** , wybierz pozycję **Pobierz dane**, wybierz pozycję z platformy Azure, a następnie wybierz pozycję **z Azure SQL Database**. 

   ![Wybór źródła danych: połącz program Excel z bazą danych SQL.](./media/sql-database-connect-excel/excel_data_source.png)

   Zostanie otwarty Kreator połączenia danych.
3. W oknie dialogowym **Łączenie z serwerem bazy danych** wpisz **nazwę serwera** usługi SQL Database, z którym chcesz nawiązać połączenie w następującej formie: <*nazwaserwera*>**. database.windows.net**. Na przykład **msftestserver.Database.Windows.NET**. Opcjonalnie wprowadź nazwę bazy danych. Wybierz **przycisk OK** , aby otworzyć okno poświadczenia. 

   ![Okno dialogowe łączenie z serwerem bazy danych](media/sql-database-connect-excel/server-name.png)

4. W oknie dialogowym **SQL Server Database** wybierz pozycję **baza danych** po lewej stronie, a następnie wprowadź **nazwę użytkownika** i **hasło** do serwera SQL Database, z którym chcesz nawiązać połączenie. Wybierz pozycję **Połącz** , aby otworzyć **Nawigator**. 

   ![Wpisywanie nazwy serwera i poświadczeń logowania](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > W zależności od środowiska sieciowego może nie być możliwe nawiązanie połączenia lub można utracić połączenie, jeśli serwer usługi SQL Database nie zezwala na ruch z adresu IP klienta. Przejdź do witryny [Azure Portal](https://portal.azure.com/), kliknij serwery SQL, kliknij serwer, którego używasz, kliknij zaporę systemu w ustawieniach i dodaj swój adres IP klienta. Aby uzyskać szczegółowe informacje, zobacz artykuł [How to configure firewall settings](sql-database-configure-firewall-settings.md) (Jak skonfigurować ustawienia zapory).

5. W **Nawigatorze**wybierz bazę danych, z której chcesz korzystać z listy, wybierz tabele lub widoki, z którymi chcesz współpracować (wybieramy **vGetAllCategories**), a następnie wybierz pozycję **Załaduj** , aby przenieść dane z bazy danych do arkusza kalkulacyjnego programu Excel.

    ![Wybierz bazę danych i tabelę.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importowanie danych do programu Excel i tworzenie wykresu przestawnego

Po nawiązaniu połączenia masz kilka różnych opcji dotyczących ładowania danych. Na przykład poniższe kroki tworzą wykres przestawny na podstawie danych znalezionych w SQL Database. 

1. Wykonaj kroki opisane w poprzedniej sekcji, ale tym razem zamiast wybierania **obciążenia**, wybierz pozycję **Załaduj do na** liście rozwijanej **ładowania** .
2. Następnie wybierz sposób wyświetlania tych danych w skoroszycie. Wybrano **Wykres przestawny**. Można również utworzyć **Nowy arkusz** lub wybrać opcję **Dodaj te dane do modelu danych**. Więcej informacji o modelach danych można znaleźć w temacie [Tworzenie modelu danych w programie Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Wybieranie formatu danych w programie Excel](./media/sql-database-connect-excel/import-data.png)

    Arkusz zawiera teraz pustą tabelę przestawną i wykres.
3. W obszarze **Pola tabeli przestawnej** zaznacz wszystkie pola wyboru dla pól do wyświetlenia.

    ![Skonfiguruj raport bazy danych.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Jeśli chcesz połączyć inne skoroszyty i arkusze programu Excel z bazą danych, wybierz kartę **dane** , a następnie wybierz pozycję **ostatnie źródła** , aby uruchomić okno dialogowe **ostatnie źródła** . Z tego miejsca wybierz utworzone połączenie z listy, a następnie kliknij przycisk **Otwórz**.
> ![Ostatnie źródła — okno dialogowe](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Tworzenie stałego połączenia przy użyciu pliku odc

Aby trwale zapisać szczegóły połączenia, można utworzyć plik odc i wybrać opcję wyboru w oknie dialogowym **istniejące połączenia** . 

1. Na pasku menu w górnej części strony wybierz kartę **dane** , a następnie wybierz pozycję **istniejące połączenia** , aby uruchomić okno dialogowe **istniejące połączenia** . 
   1. Wybierz pozycję Przeglądaj, aby **uzyskać więcej informacji** , aby otworzyć okno dialogowe **Wybieranie źródła danych** .   
   2. Wybierz plik **+ NewSqlServerConnection. odc** , a następnie wybierz pozycję **Otwórz** , aby otworzyć **Kreatora połączenia danych**.

      ![Nowe połączenie — okno dialogowe](media/sql-database-connect-excel/new-connection.png)

2. W **Kreatorze połączenia danych**wpisz nazwę serwera i poświadczenia SQL Database. Wybierz pozycję **Dalej**. 
   1. Wybierz z listy rozwijanej bazę danych zawierającą dane. 
   2. Wybierz tabelę lub widok, który Cię interesuje. Wybrano vGetAllCategories.
   3. Wybierz pozycję **Dalej**. 

      ![Kreator połączenia danych](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Wybierz lokalizację pliku, **nazwę pliku**i **przyjazną nazwę** na następnym ekranie Kreatora połączenia danych. Możesz również zapisać hasło w pliku, ale może to spowodować, że dane nie są niepożądane. Wybierz pozycję **Zakończ** , gdy wszystko będzie gotowe. 

    ![Zapisz połączenie danych](media/sql-database-connect-excel/save-data-connection.png)

4. Wybierz sposób importowania danych. Wybrano opcję wykonania tabeli przestawnej. Możesz również zmodyfikować właściwości połączenia, wybierając pozycję **Właściwości**. Wybierz **przycisk OK** , gdy wszystko będzie gotowe. Jeśli nie chcesz zapisać hasła przy użyciu pliku, zostanie wyświetlony monit o wprowadzenie poświadczeń. 

    ![Importowanie danych](media/sql-database-connect-excel/import-data2.png)

5. Sprawdź, czy nowe połączenie zostało zapisane poprzez rozwinięcie karty **dane** , a następnie wybierz pozycję **istniejące połączenia**. 

    ![Istniejące połączenie](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Następne kroki

* Aby wykonywać zaawansowane zapytania i analizy, zobacz temat [Connect to SQL Database with SQL Server Management Studio](sql-database-connect-query-ssms.md) (Nawiązywanie połączenia z usługą SQL Database za pomocą programu SQL Server Management Studio).
* Dowiedz się, jakie zalety mają [pule elastyczne](sql-database-elastic-pool.md).
* Dowiedz się, jak [utworzyć aplikację internetową, która łączy się z bazą danych SQL Database zaplecza](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
