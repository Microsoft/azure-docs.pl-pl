---
title: 'Szybki Start: Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu szablonu Menedżer zasobów'
description: W tym przewodniku szybki start utworzysz Klaster Apache Hadoop w usłudze Azure HDInsight przy użyciu szablonu Menedżer zasobów
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 3d1860c629c492287d8caadcf79b0e136b8a3038
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863823"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-arm-template"></a>Szybki Start: Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu szablonu ARM

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (szablon ARM), aby utworzyć klaster [Apache Hadoop](./apache-hadoop-introduction.md) w usłudze Azure HDInsight. Usługa Hadoop była oryginalną strukturą typu "open source" na potrzeby rozproszonego przetwarzania i analizy zestawów danych Big Data w klastrach. Ekosystem usługi Hadoop obejmuje powiązane oprogramowanie i narzędzia, w tym Apache Hive, Apache HBase, Spark, Kafka i wiele innych.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]
  
Obecnie usługa HDInsight obejmuje [siedem różnych typów klastrów](../hdinsight-overview.md#cluster-types-in-hdinsight). Każdy typ klastra obsługuje inny zestaw składników. Wszystkie typy klastrów obsługują technologię Hive. Aby uzyskać listę obsługiwanych składników w usłudze HDInsight, zobacz artykuł [Nowości w wersjach klastra Hadoop dostarczanych z usługą HDInsight](../hdinsight-component-versioning.md)  

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Wdrażanie na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-linux-ssh-password/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Tworzenie konta usługi Azure Storage.
* [Microsoft. HDInsight/klaster](/azure/templates/microsoft.hdinsight/clusters): Tworzenie klastra usługi HDInsight.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz przycisk **Wdróż na platformie Azure** poniżej, aby zalogować się do platformy Azure i otworzyć szablon ARM.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Wdróż na platformie Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json)

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
    |Lokalizacja|Wartość zostanie automatycznie wypełniona przy użyciu lokalizacji używanej przez grupę zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. Dla tego szablonu należy używać tylko małych liter i cyfr.|
    |Typ klastra | Wybierz pozycję **Hadoop**. |
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika — wartość domyślna to **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką i jedną małą literę oraz jeden znak inny niż alfanumeryczny (z wyjątkiem znaków "" "). |
    |Nazwa użytkownika SSH|Podaj nazwę użytkownika, wartość domyślna to **sshuser**|
    |Hasło ssh|Podaj hasło.|

    Niektóre właściwości zostały umieszczone w kodzie w szablonie.  Te wartości można skonfigurować z szablonu. Aby uzyskać więcej informacji o tych właściwościach, zobacz artykuł [Create Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Tworzenie klastrów platformy Hadoop w usłudze HDInsight).

    > [!NOTE]  
    > Te wartości muszą być unikatowe i zgodne z wytycznymi dotyczącymi nazewnictwa. Szablon nie wykonuje testów walidacyjnych. Jeśli okaże się, że podane wartości są już używane lub nie są zgodne z wytycznymi, po przesłaniu szablonu wystąpi błąd.  

    :::image type="content" source="./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png " alt-text="Usługa HDInsight w systemie Linux umożliwia rozpoczęcie pracy z szablonem Menedżer zasobów w portalu" border="true":::

1. Przejrzyj **warunki i postanowienia**. Następnie wybierz opcję **Zgadzam się na powyższe warunki i** postanowienia, a następnie **Kup**. Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po utworzeniu klastra otrzymasz powiadomienie o **pomyślnym wdrożeniu** za pomocą linku **Przejdź do zasobu** . Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma konto [usługi Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md) , [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)lub  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) zależność. Jest ono określane jako domyślne konto magazynu. Klaster usługi HDInsight i jego domyślne konto magazynu muszą znajdować się w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

> [!NOTE]  
> Aby poznać inne metody tworzenia klastrów i zapoznać się z właściwościami używanymi w tym przewodniku Szybki Start, zobacz [Tworzenie klastrów usługi HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłata jest naliczana również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używana. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, sprawia to, że należy usunąć klastry, gdy nie są używane.

> [!NOTE]  
> Jeśli chcesz *natychmiast* przejść do następnego samouczka, aby dowiedzieć się, jak uruchomić operacje ETL przy użyciu usługi Hadoop w usłudze HDInsight, warto zachować działający klaster. Jest to spowodowane tym, że w samouczku należy ponownie utworzyć klaster usługi Hadoop. Jeśli jednak nie chcesz od razu przechodzić do następnego samouczka, musisz teraz usunąć klaster.

W Azure Portal przejdź do klastra, a następnie wybierz pozycję **Usuń**.

:::image type="content" source="./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png " alt-text="Usuwanie klastra z portalu usługi HDInsight" border="true":::

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, należy usunąć zarówno klaster usługi HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia klastra Apache Hadoop w usłudze HDInsight przy użyciu szablonu usługi ARM. W następnym artykule dowiesz się, jak przeprowadzić operację wyodrębniania, transformacji i ładowania (ETL, extract, transform, and load) przy użyciu usługi Hadoop w usłudze HDInsight.

> [!div class="nextstepaction"]
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interakcyjnych zapytań w usłudze HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
