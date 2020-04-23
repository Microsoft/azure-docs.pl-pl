---
title: 'Samouczek: Analizowanie danych platformy Azure HDInsight Apache Spark za pomocą usługi Power BI'
description: Samouczek — wizualizacja danych programu Apache Spark przechowywanych w klastrach usługi HDInsight za pomocą usługi Microsoft Power BI
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 04/21/2020
ms.openlocfilehash: dd0b4d1b0998bd4b13a17fb8345b87924bc27e1d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869975"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Samouczek: analizowanie danych platformy Apache Spark przy użyciu usługi Power BI w usłudze HDInsight

W tym samouczku dowiesz się, jak wizualizować dane w klastrze platformy Apache Spark w usłudze Azure HDInsight za pomocą usługi Microsoft Power BI.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wizualizowanie danych platformy Spark przy użyciu usługi Power BI

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie samouczka [Samouczek: ładowanie danych i uruchamianie zapytań w klastrze platformy Apache Spark w usłudze Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Program Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Opcjonalnie: [subskrypcja próbna usługi Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Weryfikowanie danych

[Notes Jupyter](https://jupyter.org/) utworzony w ramach [poprzedniego samouczka](apache-spark-load-data-run-query.md) zawiera kod do utworzenia tabeli `hvac`. Ta tabela jest oparta na pliku CSV dostępnym `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`dla wszystkich klastrów hdinsight Spark pod adresem . Postępuj zgodnie z następującą procedurą, aby sprawdzić dane.

1. Z notesu Jupyter wklej poniższy kod, a następnie naciśnij klawisze **SHIFT + ENTER**. Kod sprawdza obecność tabel.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Dane wyjściowe wyglądają następująco:

    ![Wyświetlanie tabel na platformie Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Jeśli notes został zamknięty przed rozpoczęciem tego samouczka, tabela `hvactemptable` jest wyczyszczona, więc nie znajduje się w danych wyjściowych.  Z poziomu narzędzi do analizy biznesowej można uzyskać dostęp tylko do tabel Hive przechowywanych w magazynie metadanych (wskazywanych przez wartość **False** w kolumnie **isTemporary**). W tym samouczku nawiążesz połączenie z utworzoną tabelą **hvac**.

2. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**. Kod sprawdza dane w tabeli.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Dane wyjściowe wyglądają następująco:

    ![Pokazywanie wierszy z tabeli hvac platformy Spark](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknij notes, aby zwolnić zasoby.

## <a name="visualize-the-data"></a>Wizualizacja danych

W tej sekcji użyjemy usługi Power BI do utworzenia wizualizacji, raportów i pulpitów nawigacyjnych na podstawie danych klastra platformy Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Tworzenie raportu w programie Power BI Desktop

Pierwsze kroki podczas pracy z platformą Spark obejmują połączenie się z klastrem w programie Power BI Desktop, załadowanie danych z klastra i utworzenie podstawowej wizualizacji na podstawie tych danych.

1. Otwórz program Power BI Desktop. Zamknij ekran powitalny startowy, jeśli zostanie otwarty.

2. Na karcie **Narzędzia** główne przejdź do pozycji Pobierz**więcej danych..** **Get Data** > .

    ![Pobierz dane do programu Power BI Desktop z programu HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Pobierz dane do usługi Power BI z usługi Apache Spark BI")

3. Wprowadź `Spark` w polu wyszukiwania, wybierz pozycję **Azure HDInsight Spark**, a następnie wybierz pozycję **Połącz**.

    ![Pobierz dane do usługi Power BI z usługi Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Pobierz dane do usługi Power BI z usługi Apache Spark BI")

4. Wprowadź adres URL klastra `mysparkcluster.azurehdinsight.net`(w formularzu) w polu tekstowym **Serwer.**

5. W **obszarze Tryb łączności danych**wybierz opcję **Zapytanie bezpośrednie**. Następnie wybierz przycisk **OK**.

    Platforma Spark umożliwia wykorzystanie dowolnego trybu łączności danych. Jeśli używasz zapytania bezpośredniego, zmiany są uwzględniane w raportach bez odświeżania całego zestawu danych. W przypadku importowania danych należy odświeżyć zestaw danych, aby zobaczyć zmiany. Aby uzyskać więcej informacji o tym, jak i kiedy korzystać z zapytania bezpośredniego, zobacz [Używanie zapytania bezpośredniego w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Wprowadź informacje o koncie logowania HDInsight, a następnie wybierz pozycję **Połącz**. Domyślna nazwa konta to *admin*.

7. Wybierz `hvac` tabelę, poczekaj, aż wyświetli się podgląd danych, a następnie wybierz pozycję **Załaduj**.

    ![Nazwa użytkownika i hasło klastra platformy Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nazwa użytkownika i hasło klastra platformy Spark")

    Program Power BI Desktop posiada informacje niezbędne do połączenia się z klastrem Spark i załadowania danych z tabeli `hvac`. Tabela i jej kolumny zostaną wyświetlone w okienku **Pola**.

8. Wizualizuj różnicę między temperaturą docelową i temperaturą rzeczywistą każdego budynku:

    1. W okienku **WIZUALIZACJE** wybierz pozycję **Wykres warstwowy**.

    2. Przeciągnij pole **BuildingID** do obszaru **Oś** i przeciągnij pola **ActualTemp** i **TargetTemp** do obszaru **Wartość**.

        ![dodawanie kolumn wartości](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "dodawanie kolumn wartości")

        Diagram wygląda następująco:

        ![suma wykresu warstwy](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "suma wykresu warstwy")

        Domyślnie wizualizacja pokazuję sumę wartości **ActualTemp** i **TargetTemp**. Zaznacz strzałkę w dół obok **pozycji ActualTemp** i **TragetTemp** w okienku Wizualizacje, możesz wyświetlić opcję **Suma** jest zaznaczona.

    3. Wybierz strzałki w dół obok **pozycji ActualTemp** i **TragetTemp** w okienku Wizualizacje, wybierz **średnią,** aby uzyskać średnią rzeczywistej i docelowej temperatury dla każdego budynku.

        ![średnia wartości](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "średnia wartości")

        Twoja wizualizacja danych będzie podobna do przedstawionej na zrzucie ekranu. Przesuń kursor nad wizualizację, aby wyświetlić etykietki narzędzi z odpowiednimi danymi.

        ![wykres warstwowy](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "wykres warstwowy")

9. Przejdź do pozycji**Zapisz** `BuildingTemperature` **plik** > , wprowadź nazwę pliku, a następnie wybierz pozycję **Zapisz**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publikowanie raportu w usłudze Power BI (opcjonalnie)

Usługa Power BI umożliwia udostępnianie raportów i pulpitów nawigacyjnych w ramach organizacji. W tej sekcji opublikuj najpierw zestaw danych i raport. Następnie przypnij raport do pulpitu nawigacyjnego. Pulpity nawigacyjne są zwykle używane do skupiania się na podzbiór danych w raporcie. Masz tylko jedną wizualizację w raporcie, ale nadal warto przejść przez te kroki.

1. Otwórz program Power BI Desktop.

1. Na karcie **Narzędzia** główne wybierz pozycję **Publikuj**.

    ![Publikowanie z programu Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publikowanie z programu Power BI Desktop")

1. Wybierz obszar roboczy do opublikowania zestawu danych i raportu, a następnie wybierz pozycję **Wybierz**. Na poniższej ilustracji domyślnie wybrany jest **Mój obszar roboczy**.

    ![Wybierz obszar roboczy, aby opublikować zestaw danych i zgłosić](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Wybierz obszar roboczy, aby opublikować zestaw danych i zgłosić")

1. Po pomyślnym zakończeniu publikowania wybierz opcję **Otwórz "BuildingTemperature.pbix" w usłudze Power BI**.

    ![Publikowanie sukcesu, kliknij, aby wprowadzić poświadczenia](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publikowanie sukcesu, kliknij, aby wprowadzić poświadczenia")

1. W usłudze Power BI wybierz pozycję **Wprowadź poświadczenia**.

    ![Wprowadzanie poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Wprowadzanie poświadczeń w usłudze Power BI")

1. Wybierz **pozycję Edytuj poświadczenia**.

    ![Edytowanie poświadczeń w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edytowanie poświadczeń w usłudze Power BI")

1. Wprowadź informacje o koncie logowania HDInsight, a następnie wybierz pozycję **Zaloguj**się . Domyślna nazwa konta to *admin*.

    ![Zaloguj się do klastra Platformy Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Zaloguj się do klastra Platformy Spark")

1. W lewym okienku przejdź do **obszaru roboczego** > Moje**raporty****obszaru roboczego,** > a następnie wybierz pozycję **BuildingTemperature**.

    ![Raport wymieniony w obszarze Raporty w lewym okienku](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Raport wymieniony w obszarze Raporty w lewym okienku")

    Pozycja **BuildingTemperature** powinna również zostać wyświetlona w obszarze **ZESTAWY DANYCH** w okienku po lewej stronie.

    Wizualizacja utworzona w programie Power BI Desktop jest teraz dostępna w usłudze Power BI.

1. Umieść kursor nad wizualizacją, a następnie wybierz ikonę pinezki w prawym górnym rogu.

    ![Raport w usłudze Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Raport w usłudze Power BI")

1. Wybierz "Nowy pulpit nawigacyjny", wprowadź nazwę, `Building temperature`a następnie wybierz **Pin**.

    ![Przypnij do nowego pulpitu nawigacyjnego](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Przypnij do nowego pulpitu nawigacyjnego")

1. W raporcie wybierz pozycję **Przejdź do pulpitu nawigacyjnego**.

Wizualizacja zostanie przypięta do pulpitu nawigacyjnego — możesz dodać inne wizualizacje do raportu i przypiąć je do tego samego pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat raportów i pulpitów nawigacyjnych, zobacz [Raporty w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) i [Pulpity nawigacyjne w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z samouczka warto usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wizualizować dane w klastrze platformy Apache Spark w usłudze Azure HDInsight za pomocą usługi Microsoft Power BI. Przejdź do następnego artykułu, aby zobaczyć, czy można utworzyć aplikację uczenia maszynowego.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji uczenia maszynowego](./apache-spark-ipython-notebook-machine-learning.md)