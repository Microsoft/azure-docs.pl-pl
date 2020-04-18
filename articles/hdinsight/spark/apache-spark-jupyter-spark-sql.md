---
title: 'Szybki start: tworzenie klastra Platformy Spark Apache przy użyciu szablonu — Usługa Azure HDInsight'
description: Ten przewodnik Szybki start pokazuje, jak utworzyć klaster platformy Spark usługi Apache w usłudze Azure HDInsight za pomocą szablonu Resource Managera i uruchomić kwerendę SQL platformy Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: a92e1410b688dc3117cf28fee2ba9231641b65bb
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616868"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Szybki start: tworzenie klastra Platformy Spark apache w usłudze Azure HDInsight przy użyciu szablonu Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu usługi Azure Resource Manager do utworzenia klastra [Platformy Spark Apache](./apache-spark-overview.md) w usłudze Azure HDInsight. Następnie należy utworzyć notes Jupyter i używać go do uruchamiania zapytań Spark SQL względem tabel gałęzi Apache. Azure HDInsight jest zarządzaną usługą analityczną typu „open source” o szerokim zakresie, z przeznaczeniem dla przedsiębiorstw. Struktura Apache Spark dla usługi HDInsight umożliwia szybką analizę danych i przetwarzanie klastrów przy użyciu przetwarzania w pamięci. Notebook Jupyter umożliwia interakcję z danymi, łączenie kodu z tekstem znaczników i proste wizualizacje.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-apache-spark-cluster"></a>Tworzenie klastra platformy Apache Spark

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux).

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft.Storage/storageKonta:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)tworzenie konta usługi Azure Storage.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): tworzenie klastra HDInsight.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz przycisk **Wdrażanie na platformie Azure** poniżej, aby zalogować się na platformę Azure i otworzyć szablon Menedżera zasobów.

    [![Wdrażanie na platformie Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**.|
    |Lokalizacja|Wartość zostanie automatycznie wypełniona lokalizacją używaną dla grupy zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. W tym szablonie należy używać tylko małych liter i cyfr.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika, domyślnie jest **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi mieć co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką literę i jedną wielką literę, jedną znak niealtalumeryczny (z wyjątkiem znaków " " ). |
    |Nazwa użytkownika Ssh|Podaj nazwę użytkownika, domyślnie jest **sshuser**|
    |Hasło Ssh|Podaj hasło.|

    ![Tworzenie klastra platformy Spark w usłudze HDInsight przy użyciu szablonu usługi Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Tworzenie klastra platformy Spark w usłudze HDInsight przy użyciu szablonu usługi Azure Resource Manager")

1. Zapoznaj się z **regulaminem**. Następnie wybierz **zgadzam się z warunkami określonymi powyżej,** a następnie **zakup.** Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

Jeśli napotkasz problem z tworzeniem klastrów HDInsight, może to oznaczać, że nie masz odpowiednich uprawnień, aby to zrobić. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące kontroli dostępu](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po utworzeniu klastra otrzymasz powiadomienie **o pomyślnym wdrożeniu** z łączem **Przejdź do zasobu.** Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma konto [usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub zależność konta usługi Azure Data Lake [Storage.](../hdinsight-hadoop-use-data-lake-store.md) Jest on określany jako domyślne konto magazynu. Klaster USŁUGI HDInsight i jego domyślne konto magazynu muszą być współlokowane w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

## <a name="create-a-jupyter-notebook"></a>Tworzenie notesu Jupyter

[Jupyter Notebook](https://jupyter.org/) to interakcyjne środowisko notesu, które obsługuje różne języki programowania. Notes umożliwia interakcję z danymi, łączenie kodu z tekstem znaczników i wykonywanie prostych wizualizacji.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Klastry usługi HDInsight**, a następnie wybierz utworzony klaster.

    ![Otwieranie klastra usługi HDInsight w witrynie Azure portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. W portalu w sekcji **Pulpity nawigacyjne klastra** wybierz pozycję **Notes jupyter .** Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla klastra.

   ![Otwórz notes Jupyter, aby uruchomić interaktywną kwerendę SQL platformy Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Otwórz notes Jupyter, aby uruchomić interaktywną kwerendę SQL platformy Spark")

4. Wybierz **pozycję Nowy** > **pyspark,** aby utworzyć notes.

   ![Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Tworzenie notesu Jupyter w celu uruchomienia interaktywnej kwerendy SQL platformy Spark")

   Zostanie utworzony i otwarty nowy notes o nazwie Untitled (Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Uruchamianie instrukcji APACHE Spark SQL

SQL (Structured Query Language) to najczęściej używany język służący do przekształcania danych i wykonywania zapytań na tych danych. Rozwiązanie Spark SQL stanowi rozszerzenie platformy Apache Spark służące do przetwarzania danych strukturalnych za pomocą dobrze znanej składni języka SQL.

1. Sprawdź, czy jądro jest gotowe. Gotowość jądra jest sygnalizowana pustym okręgiem obok nazwy jądra w notesie. Pełne kółko oznacza, że jądro jest zajęte.

    ![Stan jądra](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Stan jądra")

    Podczas pierwszego uruchamiania notesu jądro wykonuje pewne zadania w tle. Poczekaj, aż jądro będzie gotowe.

1. Wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**, aby go uruchomić. Polecenie wyświetla listę tabel Hive w klastrze:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Korzystając z notesu Jupyter z klastrem HDInsight, `spark` otrzymasz wstępnie ustawioną sesję, której można użyć do uruchamiania zapytań hive przy użyciu programu Spark SQL. Wyrażenie `%%sql` informuje aplikację Jupyter Notebook o konieczności użycia sesji wstępnej `spark` do uruchomienia zapytania programu Hive. Zapytanie pobiera pierwszych 10 wierszy z tabeli programu Hive (**hivesampletable**), która jest dostępna domyślnie na wszystkich klastrach usługi HDInsight. Przy pierwszym przesłaniu zapytania jupyter utworzy aplikację Spark dla notesu. Utworzenie jej zajmuje około 30 sekund. Gdy aplikacja Spark jest gotowa, kwerenda jest wykonywana w około sekundę i daje wyniki. Dane wyjściowe wyglądają następująco:

    ![Zapytanie gałęzi Apache w programie HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Zapytanie gałęzi w programie HDInsight")

    Podczas każdego uruchomienia zapytania w programie Jupyter w tytule okna przeglądarki internetowej wyświetlany jest stan **(Busy)** (Zajęty) wraz z tytułem notesu. Widoczne jest także pełne kółko obok tekstu **PySpark** w prawym górnym rogu.

1. Uruchom inne zapytanie, aby wyświetlić dane z tabeli `hivesampletable`.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    Ekran zostanie odświeżony w celu wyświetlenia wyników zapytania.

    ![Dane wyjściowe zapytania gałęzi w programie HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Dane wyjściowe zapytania gałęzi w programie HDInsight")

1. W menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Zamykanie notesu zwalnia zasoby klastra, w tym aplikację Spark.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

W witrynie Azure Portal przejdź do klastra i wybierz pozycję **Usuń**.

![Usługa Azure portal usuwa klaster usługi HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, można usunąć zarówno klaster HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Spark Apache w programie HDInsight i uruchomić podstawową kwerendę SQL platformy Spark. Przejdź do następnego samouczka, aby dowiedzieć się, jak używać klastra HDInsight do uruchamiania zapytań interaktywnych na przykładowych danych.

> [!div class="nextstepaction"]
> [Uruchamianie interakcyjnych zapytań na platformie Apache Spark](./apache-spark-load-data-run-query.md)
