---
title: Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics
description: Informacje na temat analizowania dzienników witryn sieci Web przy użyciu Azure Data Lake Analytics do uruchamiania funkcji i zapytań U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: d2764e5e3994eb7b80a8e7cf62fb7f437323c97e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015302"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics
Dowiedz się, jak analizować dzienniki witryn sieci Web przy użyciu Data Lake Analytics, szczególnie w przypadku szukania odwołujących się błędów podczas próby odwiedzenia witryny sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne
* **Program Visual Studio 2015 lub Visual Studio 2013**.
* **[Data Lake Tools dla Visual Studio](https://aka.ms/adltoolsvs)**.

    Po zainstalowaniu narzędzi Data Lake Tools for Visual Studio zobaczysz **Data Lake** element w menu **Narzędzia** w programie Visual Studio:

    ![Menu U-SQL programu Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Podstawowa znajomość Data Lake Analytics i narzędzi Data Lake Tools for Visual Studio**. Aby rozpocząć, zobacz:

  * [Utwórz skrypt U-SQL przy użyciu narzędzi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Konto usługi Data Lake Analytics.**  Zobacz [Tworzenie konta Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
* **Zainstaluj przykładowe dane.** W witrynie Azure Portal Otwórz konto Data Lake Analytics a następnie kliknij pozycję **przykładowe skrypty** w menu po lewej stronie, a następnie kliknij pozycję **Kopiuj przykładowe dane**. 

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Przed rozpoczęciem kompilowania i testowania skryptów U-SQL należy najpierw nawiązać połączenie z platformą Azure.

### <a name="to-connect-to-data-lake-analytics"></a>Aby nawiązać połączenie z usługą Data Lake Analytics

1. Otwórz program Visual Studio.
2. Kliknij **Data Lake > opcje i ustawienia**.
3. Kliknij przycisk **Zaloguj** lub **Zmień użytkownika** , jeśli ktoś się zalogował, i postępuj zgodnie z instrukcjami.
4. Kliknij przycisk **OK** , aby zamknąć okno dialogowe Opcje i ustawienia.

### <a name="to-browse-your-data-lake-analytics-accounts"></a>Aby przeglądać konta Data Lake Analytics

1. W programie Visual Studio Otwórz **Eksplorator serwera** , naciskając **klawisze CTRL + ALT + S**.
2. W **Eksploratorze serwera** rozwiń węzeł **Azure**, a następnie rozwiń węzeł **Data Lake Analytics**. Zostanie wyświetlona lista kont usługi Data Lake Analytics, o ile jakieś istnieją. Nie można tworzyć kont Data Lake Analytics z poziomu programu Studio. Aby utworzyć konto, zobacz Rozpoczynanie [pracy z usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-get-started-portal.md) lub Rozpoczynanie [pracy z usługą Azure Data Lake Analytics przy użyciu Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Tworzenie aplikacji U-SQL
Aplikacja U-SQL jest głównie skryptem U-SQL. Aby dowiedzieć się więcej o języku U-SQL, zobacz Wprowadzenie [do języka u-SQL](data-lake-analytics-u-sql-get-started.md).

Do aplikacji można dodawać Dodawanie operatorów zdefiniowanych przez użytkownika.  Aby uzyskać więcej informacji, zobacz temat [Tworzenie zdefiniowanych przez użytkownika operatorów U-SQL dla zadań Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).

### <a name="to-create-and-submit-a-data-lake-analytics-job"></a>Aby utworzyć i przesłać zadanie usługi Data Lake Analytics

1. Kliknij **plik > nowym > projekcie**.

2. Wybierz typ U-SQL Project (Projekt U-SQL).

   ![nowy projekt U-SQL programu Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Kliknij przycisk **OK**. Program Visual Studio tworzy rozwiązanie z użyciem pliku Script.usql.

4. Wprowadź następujący skrypt do pliku Script. usql:

   ```usql
   // Create a database for easy reuse, so you don't need to read from a file very time.
   CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

   // Create a Table valued function. TVF ensures that your jobs fetch data from he weblog file with the correct schema.
   DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
   CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
   RETURNS @result TABLE
   (
       s_date DateTime,
       s_time string,
       s_sitename string,
       cs_method string,
       cs_uristem string,
       cs_uriquery string,
       s_port int,
       cs_username string,
       c_ip string,
       cs_useragent string,
       cs_cookie string,
       cs_referer string,
       cs_host string,
       sc_status int,
       sc_substatus int,
       sc_win32status int,
       sc_bytes int,
       cs_bytes int,
       s_timetaken int
   )
   AS
   BEGIN

       @result = EXTRACT
           s_date DateTime,
           s_time string,
           s_sitename string,
           cs_method string,
           cs_uristem string,
           cs_uriquery string,
           s_port int,
           cs_username string,
           c_ip string,
           cs_useragent string,
           cs_cookie string,
           cs_referer string,
           cs_host string,
           sc_status int,
           sc_substatus int,
           sc_win32status int,
           sc_bytes int,
           cs_bytes int,
           s_timetaken int
       FROM @"/Samples/Data/WebLog.log"
       USING Extractors.Text(delimiter:' ');
       RETURN;
   END;

   // Create a table for storing referrers and status
   DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
   @weblog = SampleDBTutorials.dbo.WeblogsView();
   CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
   (
       INDEX idx1
       CLUSTERED(Year ASC)
       DISTRIBUTED BY HASH(Year)
   ) AS

   SELECT s_date.Year AS Year,
       s_date.Month AS Month,
       s_date.Day AS Day,
       cs_referer,
       sc_status,
       COUNT(DISTINCT c_ip) AS cnt
   FROM @weblog
   GROUP BY s_date,
           cs_referer,
           sc_status;
   ```

    Aby zrozumieć język U-SQL, zobacz Wprowadzenie do [języka u-sql Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).

5. Dodaj nowy skrypt U-SQL do projektu i wprowadź następujące elementy:

   ```usql
   // Query the referrers that ran into errors
   @content =
       SELECT *
       FROM SampleDBTutorials.dbo.ReferrersPerDay
       WHERE sc_status >=400 AND sc_status < 500;

   OUTPUT @content
   TO @"/Samples/Outputs/UnsuccessfulResponses.log"
   USING Outputters.Tsv();
   ```

6. Przełącz się z powrotem do pierwszego skryptu U-SQL i obok przycisku **Prześlij** Określ konto analizy.

7. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Build Script** (Kompiluj skrypt). Sprawdź wyniki w okienku danych wyjściowych.

8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **Script.usql**, a następnie kliknij pozycję **Submit Script** (Prześlij skrypt).

9. Sprawdź, czy **konto analizy jest kontem** , w którym chcesz uruchomić zadanie, a następnie kliknij przycisk **Prześlij**. Po zakończeniu przesyłania wyniki przesyłania i link do zadania są dostępne w oknie wyników narzędzi Data Lake Tools dla programu Visual Studio.

10. Poczekaj, aż zadanie zostanie ukończone pomyślnie.  Jeśli zadanie nie powiodło się, najprawdopodobniej brakuje pliku źródłowego.  Zapoznaj się z sekcją wymagań wstępnych w tym samouczku. Aby uzyskać dodatkowe informacje dotyczące rozwiązywania problemów, zobacz [monitorowanie i rozwiązywanie problemów z zadaniami Azure Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Po zakończeniu zadania zobaczysz następujący ekran:

    ![Dzienniki witryn sieci Web analizy usługi Data Lake Analytics](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Teraz Powtórz kroki 7-10 dla **Script1. usql**.

### <a name="to-see-the-job-output"></a>Aby wyświetlić dane wyjściowe zadania

1. W **Eksploratorze serwera** rozwiń węzeł **Azure**, rozwiń węzeł **Data Lake Analytics**, rozwiń konto usługi Data Lake Analytics, rozwiń węzeł **Konta magazynu**, kliknij prawym przyciskiem myszy domyślne konto usługi Data Lake Storage, a następnie kliknij przycisk **Explorer**.
2. Kliknij dwukrotnie pozycję **przykłady** , aby otworzyć folder, a następnie kliknij dwukrotnie pozycję dane **wyjściowe**.
3. Kliknij dwukrotnie pozycję **UnsuccessfulResponses. log**.
4. Możesz również kliknąć dwukrotnie plik wyjściowy w widoku grafu zadania, aby przejść bezpośrednio do danych wyjściowych.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć pracę z usługą Data Lake Analytics przy użyciu różnych narzędzi, zobacz:

* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu Portalu Azure](data-lake-analytics-get-started-portal.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu zestawu SDK programu .NET](./data-lake-analytics-get-started-cli.md)