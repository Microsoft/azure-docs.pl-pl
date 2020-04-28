---
title: Narzędzia Data Lake Tools for Visual Studio & Hortonworks — Azure HDInsight
description: Dowiedz się, jak używać narzędzi Azure Data Lake Tools for Visual Studio z piaskownicą Hortonworks działającą na lokalnej maszynie wirtualnej. Za pomocą tych narzędzi możesz tworzyć i uruchamiać zadania Hive i świńskie w piaskownicy oraz wyświetlać dane wyjściowe i historyczne zadań.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73500164"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Korzystanie z Azure Data Lake narzędzi dla programu Visual Studio z piaskownicą Hortonworks

Azure Data Lake zawiera narzędzia do pracy z ogólnymi klastrami Apache Hadoop. Ten dokument zawiera kroki niezbędne do korzystania z narzędzi Data Lake z piaskownicą Hortonworks działającą na lokalnej maszynie wirtualnej.

Użycie piaskownicy Hortonworks umożliwia lokalne korzystanie z usługi Hadoop w środowisku deweloperskim. Po opracowaniu rozwiązania i zamiaru wdrożenia go w odpowiedniej skali można przejść do klastra usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Piaskownica Hortonworks uruchamiana na maszynie wirtualnej w środowisku deweloperskim. Ten dokument został zapisany i przetestowany przy użyciu piaskownicy działającej w programie Oracle VirtualBox. Aby uzyskać informacje na temat konfigurowania piaskownicy, zobacz Rozpoczynanie [pracy z piaskownicą Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) dokumentu.

* Program Visual Studio.

* [Zestaw Azure SDK dla programu .NET](https://azure.microsoft.com/downloads/) 2.7.1 lub nowszego.

* [Narzędzia Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurowanie haseł dla piaskownicy

Upewnij się, że piaskownica Hortonworks jest uruchomiona. Następnie wykonaj kroki opisane w dokumencie Wprowadzenie do [piaskownicy Hortonworks](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) . Poniższe kroki służą do konfigurowania hasła dla `root` konta SSH oraz konta Apache Ambari `admin` . Te hasła są używane podczas nawiązywania połączenia z piaskownicą z programu Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Łączenie narzędzi z piaskownicą

1. Otwórz program Visual Studio, wybierz pozycję **Widok**, a następnie wybierz pozycję **Eksplorator serwera**.

2. W **Eksplorator serwera**kliknij prawym przyciskiem myszy wpis usługi **HDInsight** , a następnie wybierz pozycję **Połącz z emulatorem usługi HDInsight**.

    ![Eksplorator serwera, z wyróżnionym emulatorem usługi HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. W oknie dialogowym **łączenie z emulatorem usługi HDInsight** wprowadź hasło skonfigurowane do Ambari.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym polem tekstowym hasła Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Wybierz przycisk **Dalej**, aby kontynuować.

4. Użyj pola **hasło** , aby wprowadzić hasło skonfigurowane dla `root` konta. Pozostaw pozostałe pola w wartości domyślnej.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym polem tekstowym hasło główne](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Wybierz przycisk **Dalej**, aby kontynuować.

5. Poczekaj na zakończenie weryfikacji usług. W niektórych przypadkach sprawdzanie poprawności może się nie powieść i zostanie wyświetlony monit o zaktualizowanie konfiguracji. Jeśli walidacja nie powiedzie się, wybierz pozycję **Aktualizuj**i poczekaj na zakończenie konfiguracji i weryfikacji usługi.

    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przyciskiem Aktualizuj](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > Proces aktualizacji używa Ambari, aby zmodyfikować konfigurację piaskownicy Hortonworks na oczekiwaną przez narzędzia Data Lake Tools for Visual Studio.

6. Po zakończeniu walidacji wybierz pozycję **Zakończ** , aby zakończyć konfigurację.
    ![Zrzut ekranu przedstawiający okno dialogowe z wyróżnionym przyciskiem zakończenia](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > W zależności od szybkości środowiska programistycznego i ilości pamięci przystosowanej do maszyny wirtualnej może upłynąć kilka minut, aby skonfigurować i sprawdzić poprawność usług.

Po wykonaniu tych kroków masz teraz wpis **lokalnego klastra usługi HDInsight** w Eksplorator serwera w sekcji usługi **HDInsight** .

## <a name="write-an-apache-hive-query"></a>Napisz zapytanie Apache Hive

Program Hive udostępnia język zapytań przypominający SQL (HiveQL) do pracy z danymi strukturalnymi. Wykonaj następujące kroki, aby dowiedzieć się, jak uruchamiać zapytania na żądanie w klastrze lokalnym.

1. W **Eksplorator serwera**kliknij prawym przyciskiem myszy wpis dla lokalnego klastra, który został wcześniej dodany, a następnie wybierz polecenie **Napisz zapytanie Hive**.

    ![Zrzut ekranu przedstawiający Eksplorator serwera, z wyróżnioną funkcją Zapisz zapytanie Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Zostanie otwarte okno nowego zapytania. W tym miejscu możesz szybko napisać i przesłać zapytanie do klastra lokalnego.

2. W oknie Nowa kwerenda wprowadź następujące polecenie:

        select count(*) from sample_08;

    Aby uruchomić zapytanie, wybierz pozycję **Prześlij** w górnej części okna. Pozostaw wartości domyślne innych wartości (**partii** i nazwy serwera).

    ![Zrzut ekranu okna zapytania z wyróżnionym przyciskiem Prześlij](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Możesz również użyć menu rozwijanego obok pozycji **Prześlij** do wybierz pozycję **Zaawansowane**. Opcje zaawansowane umożliwiają udostępnianie dodatkowych opcji podczas przesyłania zadania.

    ![Zrzut ekranu przedstawiający gałąź okna dialogowego przesyłania skryptu](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Po przesłaniu zapytania zostanie wyświetlony stan zadania. Stan zadania zawiera informacje o zadaniu, które jest przetwarzane przez platformę Hadoop. **Stan zadania** zapewnia stan zadania. Stan jest aktualizowany okresowo lub można użyć ikony odświeżania, aby ręcznie odświeżyć stan.

    ![Zrzut ekranu przedstawiający okno dialogowe Widok zadania z wyróżnionym stanem zadania](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Gdy **stan zadania** zostanie zmieniony na **zakończone**, zostanie wyświetlony ukierunkowany wykres o postaci acykliczne (DAG). Ten diagram zawiera opis ścieżki wykonywania, która została określona przez tez podczas przetwarzania zapytania programu Hive. Tez jest domyślnym aparatem wykonywania dla programu Hive w klastrze lokalnym.

    > [!NOTE]  
    > Apache Tez jest również wartością domyślną w przypadku korzystania z klastrów usługi HDInsight opartych na systemie Linux. Nie jest to ustawienie domyślne w usłudze HDInsight opartej na systemie Windows. Aby go użyć, musisz dodać wiersz `set hive.execution.engine = tez;` na początku zapytania programu Hive.

    Użyj linku **danych wyjściowych zadania** , aby wyświetlić dane wyjściowe. W tym przypadku jest to 823, liczba wierszy w tabeli sample_08. Informacje diagnostyczne dotyczące zadania można wyświetlić, korzystając z **dziennika zadania** i pobierając linki do **dziennika przędzy** .

4. Zadania programu Hive można także interaktywnie uruchamiać, zmieniając pole **Batch** na **Interactive**. Następnie wybierz pozycję **Wykonaj**.

    ![Zrzut ekranu przedstawiający przyciski interaktywne i wykonane](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Zapytanie interakcyjne przesyła strumieniowo dziennik wyjściowy wygenerowany podczas przetwarzania do okna **danych wyjściowych serwera hiveserver2** .

    > [!NOTE]  
    > Informacje są takie same, które są dostępne w linku **dziennika zadań** po zakończeniu zadania.

    ![Zrzut ekranu przedstawiający dane wyjściowe serwera hiveserver2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Tworzenie projektu Hive

Można również utworzyć projekt, który zawiera wiele skryptów Hive. Użyj projektu, gdy masz powiązane skrypty lub chcesz przechowywać skrypty w systemie kontroli wersji.

1. W programie Visual Studio wybierz kolejno opcje **plik**, **Nowy**i **projekt**.

2. Z listy projektów rozwiń węzeł **Szablony**, rozwiń węzeł **Azure Data Lake**, a następnie wybierz pozycję **Hive (HDInsight)**. Z listy szablonów wybierz pozycję **przykład Hive**. Wprowadź nazwę i lokalizację, a następnie wybierz przycisk **OK**.

    ![Okno nowego projektu, z Azure Data Lake, przykładem Hive i OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

**Przykładowy projekt Hive** zawiera dwa skrypty, **WebLogAnalysis. HQL** i **SensorDataAnalysis. HQL**. Można przesłać te skrypty za pomocą tego samego przycisku **Prześlij** w górnej części okna.

## <a name="create-an-apache-pig-project"></a>Tworzenie projektu Apache świni

Gdy Hive zawiera język przypominający SQL do pracy z danymi strukturalnymi, świnie działają przez wykonywanie transformacji danych. Świnie zawiera język (surówka), który umożliwia tworzenie potoku transformacji. Aby użyć świni z klastrem lokalnym, wykonaj następujące kroki:

1. Otwórz program Visual Studio i wybierz kolejno opcje **plik**, **Nowy**i **projekt**. Z listy projektów rozwiń węzeł **Szablony**, rozwiń węzeł **Azure Data Lake**, a następnie wybierz pozycję **świnia (HDInsight)**. Z listy szablonów wybierz pozycję Aplikacja dla **trzody chlewnej**. Wprowadź nazwę i lokalizację, a następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno nowego projektu, z Azure Data Lakeem, świnią, aplikacją wieprzową i pozycją OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Wprowadź następujący tekst jako zawartość pliku **Script. świni** , który został utworzony przy użyciu tego projektu.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Gdy świnia używa innego języka niż Hive, sposób uruchamiania zadań jest spójny w obu językach za pośrednictwem przycisku **Prześlij** . Wybranie listy rozwijanej obok pozycji **Prześlij** powoduje wyświetlenie zaawansowanego okna dialogowego przesyłania dla świni.

    ![Zrzut ekranu przedstawiający okno dialogowe przesyłania skryptu](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. Wyświetlany jest również stan zadania i dane wyjściowe, takie same jak zapytanie programu Hive.

    ![Zrzut ekranu ukończonego zadania Świniowego](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Wyświetl zadania

Narzędzia Data Lake umożliwiają również łatwe wyświetlanie informacji o zadaniach, które zostały uruchomione w usłudze Hadoop. Wykonaj następujące kroki, aby wyświetlić zadania, które zostały uruchomione w klastrze lokalnym.

1. W **Eksplorator serwera**kliknij prawym przyciskiem myszy lokalny klaster, a następnie wybierz polecenie **Wyświetl zadania**. Zostanie wyświetlona lista zadań, które zostały przesłane do klastra.

    ![Zrzut ekranu przedstawiający Eksplorator serwera, z wyróżnionymi zadaniami Wyświetl](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Z listy zadań wybierz jeden, aby wyświetlić szczegóły zadania.

    ![Zrzut ekranu przeglądarki zadań z jednym z wyróżnionych zadań](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    Wyświetlane informacje są podobne do zawartości widocznej po uruchomieniu zapytania Hive lub świni, w tym linków do wyświetlania informacji wyjściowych i dzienników.

3. Możesz również zmodyfikować i ponownie przesłać zadanie w tym miejscu.

## <a name="view-hive-databases"></a>Wyświetlanie baz danych programu Hive

1. W **Eksplorator serwera**rozwiń wpis **klastra lokalnego usługi HDInsight** , a następnie rozwiń węzeł **bazy danych programu Hive**. Są wyświetlane **domyślne** i **xademo** bazy danych w klastrze lokalnym. Rozszerzanie bazy danych pokazuje tabele w bazie danych.

    ![Zrzut ekranu przedstawiający Eksplorator serwera, z rozwiniętymi bazami danych](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Rozwinięcie tabeli Wyświetla kolumny dla tej tabeli. Aby szybko wyświetlić dane, kliknij prawym przyciskiem myszy tabelę, a następnie wybierz pozycję **Wyświetl pierwszych 100 wierszy**.

    ![Eksplorator serwera, w przypadku wybrania tabeli rozwiniętej i wyświetlenia pierwszych 100 wierszy](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Właściwości bazy danych i tabeli

Można wyświetlić właściwości bazy danych lub tabeli. Wybranie **Właściwości** wyświetla szczegóły wybranego elementu w oknie właściwości. Na przykład zapoznaj się z informacjami podanymi na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający okno Właściwości](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Tworzenie tabeli

Aby utworzyć tabelę, kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz tabelę**.

![Zrzut ekranu przedstawiający Eksplorator serwera, z wyróżnioną pozycją Utwórz tabelę](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Następnie można utworzyć tabelę przy użyciu formularza. Na dole poniższego zrzutu ekranu można zobaczyć nieprzetworzony HiveQL, który jest używany do tworzenia tabeli.

![Zrzut ekranu przedstawiający formularz użyty do utworzenia tabeli](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Następne kroki

* [Uczenie lin piaskownicy Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Samouczek Apache Hadoop — wprowadzenie do HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
