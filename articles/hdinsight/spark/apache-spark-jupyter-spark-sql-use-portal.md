---
title: 'Szybki Start: Tworzenie klastra Spark w usłudze HDInsight przy użyciu Azure Portal'
description: W tym przewodniku szybki start pokazano, jak używać Azure Portal do tworzenia klastra Apache Spark w usłudze Azure HDInsight i uruchamiania zapytania Spark SQL.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 18225703b2a390f64b4bebd58b0167dd77c454d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930384"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Szybki Start: Tworzenie klastra Apache Spark w usłudze Azure HDInsight przy użyciu Azure Portal

W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć klaster Apache Spark w usłudze Azure HDInsight. Następnie utworzysz Jupyter Notebook i użyjesz go do uruchamiania zapytań Spark SQL w odniesieniu do tabel Apache Hive. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Platforma Apache Spark dla usługi HDInsight umożliwia szybkie analizowanie danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Jupyter Notebook pozwala na posługiwanie się danymi, łączenie kodu z tekstem z promocji i wykonywanie prostych wizualizacji.

Szczegółowe wyjaśnienie dostępnych konfiguracji można znaleźć w temacie [Konfigurowanie klastrów w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Aby uzyskać więcej informacji na temat korzystania z portalu do tworzenia klastrów, zobacz [Tworzenie klastrów w portalu](../hdinsight-hadoop-create-linux-clusters-portal.md).

Jeśli używasz wielu klastrów razem, należy utworzyć sieć wirtualną, a jeśli używasz klastra Spark, należy również użyć łącznika magazynu programu Hive. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) i [integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive](../interactive-query/apache-hive-warehouse-connector.md).

> [!IMPORTANT]  
> Opłaty za klastry usługi HDInsight są naliczane proporcjonalnie za minutę, niezależnie od ich użycia. Pamiętaj o usunięciu klastra po zakończeniu korzystania z niego. Aby uzyskać więcej informacji, zobacz sekcję [Czyszczenie zasobów](#clean-up-resources) w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Tworzenie klastra platformy Apache Spark w usłudze HDInsight

Użyj Azure Portal, aby utworzyć klaster usługi HDInsight, który korzysta z obiektów BLOB w usłudze Azure Storage jako magazynu klastra. Aby uzyskać więcej informacji na temat korzystania z usługi Data Lake Storage 2. generacji, zobacz [Szybki start: konfigurowanie klastrów w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W górnym menu wybierz pozycję **+ Utwórz zasób**.

    ![Azure Portal utworzyć zasobu](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Tworzenie zasobu w witrynie Azure Portal")

1. Wybierz pozycję **Analiza**  >  **Azure HDInsight** , aby przejść do strony **Tworzenie klastra usługi HDInsight** .

1. Na karcie **podstawowe** podaj następujące informacje:

    |Właściwość  |Opis  |
    |---------|---------|
    |Subskrypcja  | Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra. |
    |Grupa zasobów | Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
    |Nazwa klastra | Podaj globalnie unikatową nazwę.|
    |Region (Region)   | Z listy rozwijanej wybierz region, w którym tworzony jest klaster. |
    |Typ klastra| Wybierz pozycję Wybierz typ klastra, aby otworzyć listę. Z listy wybierz pozycję **Spark**.|
    |Wersja klastra|Po wybraniu typu klastra pole zostanie automatycznie wypełnione przy użyciu domyślnej wersji.|
    |Nazwa użytkownika logowania klastra| Wprowadź nazwę użytkownika logowania klastra.  Nazwa domyślna to **admin**. To konto jest używane do logowania się do Jupyter Notebook w dalszej części przewodnika Szybki Start. |
    |Hasło logowania klastra| Wprowadź hasło logowania klastra. |
    |Nazwa użytkownika protokołu SSH (Secure Shell)| Wprowadź nazwę użytkownika protokołu SSH. Nazwą użytkownika protokołu SSH używaną w tym przewodniku Szybki start jest **sshuser**. Domyślnie to konto współdzieli hasło z kontem *Nazwa użytkownika logowania klastra*. |

    ![Zrzut ekranu przedstawia tworzenie klastra usługi H Insights z wybraną kartą podstawowe.](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Tworzenie klastra Spark w usłudze HDInsight konfiguracje podstawowe")

    Wybierz pozycję **Dalej: magazyn >>** , aby kontynuować na stronie **Magazyn** .

1. W obszarze **Magazyn** podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Podstawowy typ magazynu|Użyj wartości domyślnej **usługi Azure Storage**.|
    |Metoda wybierania|Użyj wartości domyślnej **Wybierz z listy**.|
    |Konto magazynu podstawowego|Użyj automatycznie wypełnionej wartości.|
    |Kontener|Użyj automatycznie wypełnionej wartości.|

    ![Zrzut ekranu przedstawia tworzenie klastra usługi H Insights z wybraną kartą magazyn.](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Tworzenie klastra Spark w usłudze HDInsight konfiguracje podstawowe")

    Wybierz pozycję **Recenzja + Utwórz** , aby kontynuować.

1. W obszarze **Recenzja + tworzenie** wybierz pozycję **Utwórz**. Utworzenie klastra trwa około 20 minut. Przed przejściem do następnej sesji należy utworzyć klaster.

Jeśli wystąpi problem z tworzeniem klastrów usługi HDInsight, może to oznaczać, że nie masz odpowiednich uprawnień, aby to zrobić. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

Jupyter Notebook to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes pozwala na interakcję z danymi, łączenie kodu z tekstem markdown i wykonywanie prostych wizualizacji.

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/jupyter` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

1. Wybierz pozycję **New**  >  **PySpark** , aby utworzyć Notes.

   ![Utwórz Jupyter Notebook do uruchamiania interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Utwórz Jupyter Notebook do uruchamiania interakcyjnego zapytania Spark SQL")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Uruchamianie Apache Spark instrukcji SQL

SQL (Structured Query Language) to najczęściej używany język służący do definiowania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Zrzut ekranu przedstawia okno Jupyter ze wskaźnikiem PySpark.](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Zapytanie programu Hive w usłudze HDInsight")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe.

1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    W przypadku korzystania z Jupyter Notebook z klastrem usługi HDInsight zostanie wyświetlone ustawienie wstępne, `sqlContext` za pomocą którego można uruchamiać zapytania Hive przy użyciu platformy Spark SQL. Wyrażenie `%%sql` informuje notes Jupyter o konieczności użycia ustawienia wstępnego `sqlContext` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Uzyskanie wyników zajmuje około 30 sekund. Dane wyjściowe wyglądają następująco:

    ![Zrzut ekranu przedstawia okno Jupyter dla notesu utworzonego w tym przewodniku Szybki Start.](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Zapytanie programu Hive w usłudze HDInsight")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Dane wyjściowe zapytania programu Hive w usłudze HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Dane wyjściowe zapytania programu Hive w usłudze HDInsight")

1. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu spowoduje zwolnienie zasobów klastra.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usługa HDInsight zapisuje dane w usłudze Azure Storage lub Azure Data Lake Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane. Jeśli planujesz natychmiastowe rozpoczęcie pracy z samouczkiem z listy [Następne kroki](#next-steps), warto zachować klaster.

Przejdź z powrotem do witryny Azure Portal, a następnie wybierz pozycję **Usuń**.

![Azure Portal usunąć klaster usługi HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, należy usunąć zarówno klaster usługi HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra Apache Spark w usłudze HDInsight i uruchamiania podstawowego zapytania Spark SQL. Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra usługi HDInsight.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)