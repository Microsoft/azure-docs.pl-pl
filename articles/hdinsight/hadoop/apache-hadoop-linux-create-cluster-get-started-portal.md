---
title: 'Szybki Start: Apache Hadoop Apache Hive & portalu usługi Azure HDInsight'
description: W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć klaster usługi HDInsight Hadoop
keywords: wprowadzenie do usługi hadoop,hadoop linux,hadoop szybki start,wprowadzenie do usługi hive,hive szybki start
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 02990ce287aa423481c1bfe8ec5e2a8a4cfdb555
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858493"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki Start: Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu Azure Portal

W tym artykule dowiesz się, jak tworzyć klastry usługi Apache Hadoop w usłudze HDInsight przy użyciu witryny Azure Portal, a następnie uruchamiać zadania usługi Apache Hive w usłudze HDInsight. Większość zadań usługi Hadoop to zadania wsadowe. Tworzysz klaster, uruchamiasz pewne zadania, a następnie usuwasz klaster. W tym artykule wykonasz wszystkie trzy zadania. Szczegółowe wyjaśnienie dostępnych konfiguracji można znaleźć w temacie [Konfigurowanie klastrów w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji na temat korzystania z portalu do tworzenia klastrów, zobacz [Tworzenie klastrów w portalu](../hdinsight-hadoop-create-linux-clusters-portal.md).

W tym przewodniku Szybki start użyjesz witryny Azure Portal do utworzenia klastra Hadoop w usłudze HDInsight. Klaster możesz utworzyć również przy użyciu [szablonu usługi Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Obecnie Usługa HDInsight zawiera [siedem różnych typów klastrów](../hdinsight-overview.md#cluster-types-in-hdinsight). Każdy typ klastra obsługuje inny zestaw składników. Wszystkie typy klastrów obsługują technologię Hive. Aby uzyskać listę składników obsługiwanych w usłudze HDInsight, zobacz artykuł [Nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight](../hdinsight-component-versioning.md).  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-an-apache-hadoop-cluster"></a>Tworzenie klastra Apache Hadoop

W tej sekcji utworzysz klaster usługi Hadoop w usłudze HDInsight przy użyciu witryny Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. W górnym menu wybierz pozycję **+ Utwórz zasób**.

    ![Tworzenie klastra usługi HDInsight zasobów](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Tworzenie klastra usługi HDInsight zasobów")

1. Wybierz pozycję **Analiza**  >  **Azure HDInsight** , aby przejść do strony **Tworzenie klastra usługi HDInsight** .

1. Na karcie **podstawowe** podaj następujące informacje:

    |Właściwość  |Opis  |
    |---------|---------|
    |Subskrypcja    |  Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra. |
    |Grupa zasobów     | Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
    |Nazwa klastra   | Podaj globalnie unikatową nazwę. Nazwa może składać się z maksymalnie 59 znaków, w tym liter, cyfr i łączników. Pierwsze i ostatnie znaki nazwy nie mogą być łącznikami. |
    |Region    | Z listy rozwijanej wybierz region, w którym tworzony jest klaster.  Wybierz lokalizację znajdującą się blisko, aby zapewnić lepszą wydajność. |
    |Typ klastra| Wybierz pozycję **Wybierz typ klastra**. Następnie wybierz pozycję **Hadoop** jako typ klastra.|
    |Wersja|Z listy rozwijanej wybierz **wersję**. Użyj wersji domyślnej, jeśli nie wiesz, co wybrać.|
    |Nazwa użytkownika i hasło logowania do klastra    | Domyślna nazwa logowania to **admin**. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę oraz jeden znak inny niż alfanumeryczny (z wyjątkiem znaków "" " \) . Upewnij się, że **nie zostało podane** typowe hasło, takie jak „Pass@word1”.|
    |Nazwa użytkownika protokołu SSH (Secure Shell) | Domyślna nazwa użytkownika to **sshuser**.  Możesz podać inną nazwę użytkownika protokołu SSH. |
    |Użyj hasła logowania do klastra dla protokołu SSH| Zaznacz to pole wyboru, aby użyć tego samego hasła dla użytkownika SSH, który został podany dla użytkownika logowania klastra.|

    ![Wprowadzenie do usługi HDInsight w systemie Linux zapewnia podstawowe wartości klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Podaj podstawowe wartości na potrzeby tworzenia klastra usługi HDInsight")

    Wybierz kolejno pozycje **Następny: magazyn >>** , aby przejść do ustawień magazynu.

1. Na karcie **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wybierania|Użyj wartości domyślnej **Wybierz z listy**.|
    |Konto magazynu podstawowego|Użyj listy rozwijanej, aby wybrać istniejące konto magazynu, lub wybierz pozycję **Utwórz nowe**. Jeśli utworzysz nowe konto, nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery|
    |Kontener|Użyj autowypełnianej wartości.|

    ![Wprowadzenie do usługi HDInsight w systemie Linux zapewnia wartości magazynu klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Podaj wartości magazynu na potrzeby tworzenia klastra usługi HDInsight")

    Każdy klaster ma [konto usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md), [Azure Data Lake Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)lub [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  zależność. Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

    Wybierz kartę **Recenzja + tworzenie** .

1. Na karcie **Recenzja + tworzenie** Sprawdź wartości wybrane w poprzednich krokach.

    ![Podsumowanie klastra wprowadzenie do usługi HDInsight w systemie Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Podsumowanie klastra wprowadzenie do usługi HDInsight w systemie Linux")

1. Wybierz przycisk **Utwórz**. Utworzenie klastra trwa około 20 minut.

    Po utworzeniu klastra w witrynie Azure Portal zostanie wyświetlona strona przeglądu klastra.

    ![HDInsight Linux wprowadzenie do ustawień klastra](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Właściwości klastra usługi HDInsight")

## <a name="run-apache-hive-queries"></a>Uruchamianie zapytań technologii Apache Hive

[Apache Hive](hdinsight-use-hive.md) jest najbardziej popularnym składnikiem używanym w usłudze HDInsight Istnieje wiele sposobów uruchamiania zadań Hive w usłudze HDInsight. W tym przewodniku szybki start użyjesz widoku programu Hive Ambari z poziomu portalu. Aby poznać inne metody przesyłania zadań Hive, zobacz temat [Używanie Hive w usłudze HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Widok Apache Hive nie jest dostępny w usłudze HDInsight 4,0.

1. Aby otworzyć narzędzie Ambari, na ekranie pokazanym na poprzednim zrzucie ekranu wybierz pozycję **Pulpit nawigacyjny klastra**.  Możesz również przejść do  `https://ClusterName.azurehdinsight.net` lokalizacji, w której `ClusterName` klaster został utworzony w poprzedniej sekcji.

    ![Pulpit nawigacyjny klastra wprowadzenie do usługi HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Pulpit nawigacyjny klastra wprowadzenie do usługi HDInsight Linux")

2. Wprowadź nazwę użytkownika Hadoop i hasło określone w podczas tworzenia klastra. Domyślna nazwa użytkownika to **admin**.

3. Otwórz widok **Hive View** pokazany na poniższym zrzucie ekranu:

    ![Wybieranie widoku Hive z Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Menu podglądu usługi HDInsight Hive")

4. Na karcie **QUERY** (ZAPYTANIE) wklej poniższe instrukcje HiveQL do arkusza:

    ```sql
    SHOW TABLES;
    ```

    ![Edytor zapytań widoku Hive usługi HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Edytor zapytań widoku Hive usługi HDInsight")

5. Wybierz pozycję **Wykonaj**. Poniżej karty **QUERY** (ZAPYTANIE) zostanie wyświetlona karta **RESULTS** (WYNIKI) z informacjami o zadaniu. 

    Po zakończeniu przetwarzania zapytania na karcie **QUERY** (ZAPYTANIE) są wyświetlane wyniki operacji. Powinna być widoczna jedna tabela o nazwie **hivesampletable**. Ta przykładowa tabela składnika Hive jest dostarczana z wszystkimi klastrami usługi HDInsight.

    ![Wyniki widoku Apache Hive usługi HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Wyniki widoku Apache Hive usługi HDInsight")

6. Powtórz kroki 4 i 5, aby uruchomić następujące zapytanie:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Można także zapisać wyniki zapytania. Wybierz przycisk menu z prawej strony i określ, czy chcesz pobrać wyniki jako plik CSV, czy zapisać je na koncie magazynu skojarzonym z klastrem.

    ![Zapisz wynik zapytania Apache Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Zapisz wynik zapytania Apache Hive")

Po zakończeniu zadania programu Hive można [wyeksportować wyniki do bazy danych Azure SQL Database lub SQL Server](apache-hadoop-use-sqoop-mac-linux.md), można również [wizualizować wyniki przy użyciu programu Excel](apache-hadoop-connect-excel-power-query.md). Aby uzyskać więcej informacji o korzystaniu z programu Hive w usłudze HDInsight, zobacz artykuł [Używanie Apache Hive i HiveQL z usługą Apache Hadoop w usłudze HDInsight do analizy przykładowego pliku Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

> [!NOTE]  
> Jeśli masz *natychmiast* do następnego artykułu, aby dowiedzieć się, jak uruchamiać operacje ETL przy użyciu usługi Hadoop w usłudze HDInsight, możesz kontynuować działanie klastra. Jest to spowodowane tym, że w samouczku należy ponownie utworzyć klaster usługi Hadoop. Jeśli jednak nie przejdziesz od razu do następnego artykułu, musisz usunąć klaster teraz.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Usuwanie klastra i/lub domyślnego konta magazynu

1. Wróć do karty przeglądarki, na której znajduje się witryna Azure Portal. Musisz mieć otwartą stronę omówienia klastra. Jeśli chcesz tylko usunąć klaster, zachowując domyślne konto magazynu, wybierz pozycję **Usuń**.

    ![Usuwanie klastra usługi Azure HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Usuwanie klastra usługi Azure HDInsight")

2. Jeśli chcesz usunąć klaster oraz domyślne konto magazynu, wybierz nazwę grupy zasobów (wyróżnioną na poprzednim zrzucie ekranu), aby otworzyć stronę grupy zasobów.

3. Wybierz pozycję **Usuń grupę zasobów**, aby usunąć grupę zasobów zawierającą klaster i domyślne konto magazynu. Uwaga: usunięcie grupy zasobów powoduje usunięcie konta magazynu. Jeśli chcesz zachować konta magazynu, wybierz opcję usunięcia tylko klastra.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono, jak utworzyć klaster usługi HDInsight oparty na systemie Linux przy użyciu szablonu Menedżer zasobów i jak wykonywać podstawowe zapytania programu Hive. W następnym artykule dowiesz się, jak przeprowadzić operację wyodrębniania, transformacji i ładowania (ETL, extract, transform, and load) przy użyciu usługi Hadoop w usłudze HDInsight.

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
