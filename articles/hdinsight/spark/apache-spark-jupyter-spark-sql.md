---
title: 'Szybki Start: Tworzenie klastra Apache Spark przy użyciu szablonu — Azure HDInsight'
description: W tym przewodniku szybki start pokazano, jak za pomocą szablonu Menedżer zasobów utworzyć klaster Apache Spark w usłudze Azure HDInsight i uruchomić zapytanie Spark SQL.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 1bbf3cbffe2ab51f10e86dc3b9fe4ea6040a7911
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930266"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Szybki Start: Tworzenie klastra Apache Spark w usłudze Azure HDInsight przy użyciu szablonu ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć klaster [Apache Spark](./apache-spark-overview.md) w usłudze Azure HDInsight. Następnie utworzysz plik Jupyter Notebook i użyjesz go do uruchamiania zapytań Spark SQL w odniesieniu do tabel Apache Hive. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Platforma Apache Spark dla usługi HDInsight umożliwia szybkie analizowanie danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Jupyter Notebook pozwala na posługiwanie się danymi, łączenie kodu z tekstem z promocji i wykonywanie prostych wizualizacji.

Jeśli używasz wielu klastrów razem, należy utworzyć sieć wirtualną, a jeśli używasz klastra Spark, należy również użyć łącznika magazynu programu Hive. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej dla usługi Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) i [integrowanie Apache Spark i Apache Hive z łącznikiem magazynu Hive](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Tworzenie konta usługi Azure Storage.
* [Microsoft. HDInsight/klaster](/azure/templates/microsoft.hdinsight/clusters): Tworzenie klastra usługi HDInsight.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz przycisk **Wdróż na platformie Azure** poniżej, aby zalogować się do platformy Azure i otworzyć szablon ARM.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
    |Lokalizacja|Wartość zostanie automatycznie wypełniona przy użyciu lokalizacji używanej przez grupę zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. Dla tego szablonu należy używać tylko małych liter i cyfr.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika — wartość domyślna to **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę oraz jeden znak inny niż alfanumeryczny (z wyjątkiem znaków "" "). |
    |Nazwa użytkownika SSH|Podaj nazwę użytkownika, wartość domyślna to **sshuser**|
    |Hasło ssh|Podaj hasło.|

    ![Tworzenie klastra Spark w usłudze HDInsight przy użyciu szablonu Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Tworzenie klastra Spark w usłudze HDInsight przy użyciu szablonu Azure Resource Manager")

1. Przejrzyj **warunki i postanowienia**. Następnie wybierz opcję **Zgadzam się na powyższe warunki i** postanowienia, a następnie **Kup**. Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

Jeśli wystąpi problem z tworzeniem klastrów usługi HDInsight, może to oznaczać, że nie masz odpowiednich uprawnień, aby to zrobić. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po utworzeniu klastra otrzymasz powiadomienie o **pomyślnym wdrożeniu** za pomocą linku **Przejdź do zasobu** . Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma [Magazyn platformy Azure](../hdinsight-hadoop-use-blob-storage.md), [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)lub [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) zależność. Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia zależności kont magazynu. Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

## <a name="create-a-jupyter-notebook-file"></a>Tworzenie pliku notesu Jupyter Notebook

[Jupyter Notebook](https://jupyter.org/) to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Za pomocą pliku Jupyter Notebook można korzystać z danych, łączyć kod z tekstem z promocji i przeprowadzenia prostych wizualizacji.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz utworzony klaster.

    ![Otwórz klaster usługi HDInsight w Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. W portalu w sekcji **pulpity nawigacyjne klastra** wybierz pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

   ![Otwórz Jupyter Notebook, aby uruchomić interakcyjne zapytanie Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otwórz Jupyter Notebook, aby uruchomić interakcyjne zapytanie Spark SQL")

4. Wybierz pozycję **New**  >  **PySpark** , aby utworzyć Notes.

   ![Tworzenie pliku Jupyter Notebook w celu uruchomienia interakcyjnego zapytania Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Utwórz Jupyter Notebook do uruchamiania interakcyjnego zapytania Spark SQL")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Uruchamianie Apache Spark instrukcji SQL

SQL (Structured Query Language) to najczęściej używany język służący do przekształcania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Stan jądra](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Stan jądra")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe.

1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Gdy używasz pliku Jupyter Notebook z klastrem usługi HDInsight, uzyskasz wstępnie ustawioną `spark` sesję, której można użyć do uruchamiania zapytań Hive przy użyciu platformy Spark SQL. Wyrażenie `%%sql` informuje aplikację Jupyter Notebook o konieczności użycia sesji wstępnej `spark` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Podczas pierwszego przesyłania zapytania Jupyter utworzy aplikację Spark dla notesu. Utworzenie jej zajmuje około 30 sekund. Gdy aplikacja platformy Spark będzie gotowa, zapytanie jest wykonywane w około sekundzie i tworzy wyniki. Dane wyjściowe wyglądają następująco:

    ![Zapytanie Apache Hive w usłudze HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Zapytanie programu Hive w usłudze HDInsight")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Dane wyjściowe zapytania programu Hive w usłudze HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Dane wyjściowe zapytania programu Hive w usłudze HDInsight")

1. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamykanie notesu zwalnia zasoby klastra, w tym aplikacje Spark.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

W Azure Portal przejdź do klastra, a następnie wybierz pozycję **Usuń**.

![Azure Portal usunąć klaster usługi HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, należy usunąć zarówno klaster usługi HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra Apache Spark w usłudze HDInsight i uruchamiania podstawowego zapytania Spark SQL. Przejdź do następnego samouczka, aby dowiedzieć się, jak uruchamiać interakcyjne zapytania dotyczące przykładowych danych za pomocą klastra usługi HDInsight.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
