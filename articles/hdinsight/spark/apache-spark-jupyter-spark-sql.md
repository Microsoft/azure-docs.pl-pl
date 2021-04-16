---
title: 'Szybki start: tworzenie Apache Spark klastra przy użyciu szablonu — Azure HDInsight'
description: W tym przewodniku Szybki start pokazano, jak używać Resource Manager do tworzenia klastra Apache Spark klastra w Azure HDInsight i uruchamiania zapytania Spark SQL.
ms.date: 03/13/2020
ms.topic: quickstart
ms.service: hdinsight
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6d3f8c3313d9e6be38acdbecb683899ea8dd5376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538022"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Szybki start: tworzenie klastra Apache Spark w Azure HDInsight użyciu szablonu usługi ARM

W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (arm) do utworzenia Apache Spark [klastra](./apache-spark-overview.md) w Azure HDInsight. Następnie utworzysz plik Jupyter Notebook i użyjemy go do uruchamiania zapytań Spark SQL względem Apache Hive tabel. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Ta Apache Spark dla usługi HDInsight umożliwia szybką analizę danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Jupyter Notebook umożliwia interakcję z danymi, łączenie kodu z tekstem markdown i tworzenie prostych wizualizacji.

Jeśli używasz wielu klastrów jednocześnie, warto utworzyć sieć wirtualną, a jeśli używasz klastra Spark, możesz również użyć klastra Hive Warehouse Connector. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej](../hdinsight-plan-virtual-network-deployment.md) na Azure HDInsight i Integracja Apache Spark [i Apache Hive z](../interactive-query/apache-hive-warehouse-connector.md)Hive Warehouse Connector .

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

W szablonie zdefiniowano dwa zasoby platformy Azure:

* [Microsoft.Storage/storageAccounts:](/azure/templates/microsoft.storage/storageaccounts)utwórz konto usługi Azure Storage.
* [Microsoft.HDInsight/cluster:](/azure/templates/microsoft.hdinsight/clusters)utwórz klaster usługi HDInsight.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy **przycisk Deploy to Azure (Wd** wdrażaj na platformie Azure), aby zalogować się do platformy Azure i otworzyć szablon usługi ARM.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną dla klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz **pozycję Utwórz nową.**|
    |Lokalizacja|Wartość zostanie automatycznie zasypana lokalizacją używaną dla grupy zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. W tym szablonie należy używać tylko małych liter i cyfr.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika, a wartość domyślna to **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi mieć co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielkie litery i jedną małą literę, jeden znak nieal alfanumeryczny (z wyjątkiem znaków " " " "). |
    |Nazwa użytkownika SSH|Podaj nazwę użytkownika, a wartość domyślna to **sshuser**|
    |Hasło SSH|Podaj hasło.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png " alt-text="Tworzenie klastra Spark w umacie HDInsight przy użyciu Azure Resource Manager szablonu" border="true":::

1. Zapoznaj się z **WARUNKAMI I POSTANOWIENIAMI.** Następnie wybierz **pozycję Wyrażam zgodę na powyższe warunki i postanowienia,** a następnie **pozycję Kup.** Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

Jeśli podczas tworzenia klastrów usługi HDInsight występuje problem, może to oznaczać, że nie masz odpowiednich uprawnień. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po utworzeniu klastra otrzymasz powiadomienie Wdrożenie zakończyło się **pomyślnie** z linkiem **Przejdź do** zasobu. Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma [usługę Azure Storage,](../hdinsight-hadoop-use-blob-storage.md) [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)lub [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) zależność. Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie świadczenia usługi Azure. Usunięcie klastrów nie powoduje usunięcia zależności konta magazynu. Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie świadczenia usługi Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

## <a name="create-a-jupyter-notebook-file"></a>Tworzenie pliku notesu Jupyter Notebook

[Jupyter Notebook](https://jupyter.org/) to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Możesz użyć pliku Jupyter Notebook, aby wchodzić w interakcje z danymi, łączyć kod z tekstem markdown i wykonywać proste wizualizacje.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz utworzony klaster.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png" alt-text="Otwórz klaster usługi HDInsight w Azure Portal" border="true":::

3. W portalu w sekcji **Pulpity nawigacyjne klastra** wybierz pozycję **Jupyter Notebook**. Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Otwórz Jupyter Notebook, aby uruchomić interakcyjne zapytanie Spark SQL" border="true":::

4. Wybierz **pozycję Nowy**  >  **PySpark,** aby utworzyć notes.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Tworzenie pliku Jupyter Notebook w celu uruchomienia interakcyjnego zapytania Spark SQL" border="true":::

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Uruchamianie Apache Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do przekształcania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Stan jądra" border="true"::: alt-text="Stan jądra" border="true":::

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe.

1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Jeśli używasz pliku Jupyter Notebook klastrem usługi HDInsight, możesz pobrać wstępnie ustawioną sesję, za pomocą których można uruchamiać zapytania programu Hive przy `spark` użyciu programu Spark SQL. Wyrażenie `%%sql` informuje aplikację Jupyter Notebook o konieczności użycia sesji wstępnej `spark` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Przy pierwszym przesłaniu zapytania aplikacja Jupyter utworzy aplikację platformy Spark dla notesu. Utworzenie jej zajmuje około 30 sekund. Gdy aplikacja Spark jest gotowa, zapytanie jest wykonywane w ciągu około sekundy i generuje wyniki. Dane wyjściowe wyglądają następująco:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png " alt-text="Apache Hive query in HDInsight" border="true":::y in HDInsight" border="true":::

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png " alt-text="Dane wyjściowe zapytania programu Hive w umacie HDInsight" border="true"::: Insight" border="true":::

1. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamknięcie notesu zwalnia zasoby klastra, w tym aplikację platformy Spark.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu tego przewodnika Szybki start możesz usunąć klaster. W usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest on w użyciu. Opłaty są również naliczane za klaster usługi HDInsight, nawet jeśli nie jest on w użyciu. Ponieważ opłaty za klaster są wielokrotnie wyższe niż opłaty za magazyn, ekonomicznie warto usunąć klastry, gdy nie są one w użyciu.

Z Azure Portal przejdź do klastra, a następnie wybierz pozycję **Usuń.**

:::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal klaster wzroku klastra usługi HDInsight&quot;" border="true":::border="true":::

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usunięcie grupy zasobów powoduje usunięcie zarówno klastra usługi HDInsight, jak i domyślnego konta magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano, jak utworzyć klaster Apache Spark u usługi HDInsight i uruchomić podstawowe zapytanie Spark SQL. W następnym samouczku dowiesz się, jak uruchamiać interakcyjne zapytania na przykładowych danych za pomocą klastra usługi HDInsight.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
