---
title: Tworzenie usługi Azure HDInsight — Azure Data Lake Storage Gen2 — Portal
description: Dowiedz się, jak używać Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight przy użyciu portalu.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866373"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Tworzenie klastra z Data Lake Storage Gen2 przy użyciu Azure Portal

Azure Portal to oparte na sieci Web narzędzie do zarządzania usługami i zasobami hostowanymi w chmurze Microsoft Azure. W tym artykule dowiesz się, jak tworzyć klastry usługi Azure HDInsight oparte na systemie Linux przy użyciu portalu. Dodatkowe szczegóły są dostępne w obszarze [Tworzenie klastrów usługi HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby utworzyć klaster usługi HDInsight, który używa Data Lake Storage Gen2 dla magazynu, wykonaj następujące kroki, aby skonfigurować konto magazynu z hierarchiczną przestrzenią nazw.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz tożsamość zarządzaną przypisaną przez użytkownika, jeśli jeszcze jej nie masz.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu kliknij pozycję **Utwórz zasób**.
1. W polu wyszukiwania wpisz przypisane przez **użytkownika** , a następnie kliknij pozycję **tożsamość zarządzana przypisana przez użytkownika**.
1. Kliknij pozycję **Utwórz**.
1. Wprowadź nazwę tożsamości zarządzanej, wybierz odpowiednią subskrypcję, grupę zasobów i lokalizację.
1. Kliknij pozycję **Utwórz**.

Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Tworzenie tożsamości zarządzanej przypisanej przez użytkownika":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Utwórz konto magazynu do użycia z Data Lake Storage Gen2

Utwórz konto magazynu do użycia z Azure Data Lake Storage Gen2.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu kliknij pozycję **Utwórz zasób**.
1. W polu wyszukiwania wpisz **Storage** i kliknij pozycję **konto magazynu**.
1. Kliknij pozycję **Utwórz**.
1. Na ekranie **Tworzenie konta magazynu** :
    1. Wybierz prawidłową subskrypcję i grupę zasobów.
    1. Wprowadź nazwę konta magazynu z Data Lake Storage Gen2.
    1. Kliknij kartę **Zaawansowane** .
    1. Kliknij pozycję **włączone** obok **hierarchicznej przestrzeni nazw** w obszarze **Data Lake Storage Gen2**.
    1. Kliknij pozycję **Przejrzyj i utwórz**.
    1. Kliknij pozycję **Utwórz**

Aby uzyskać więcej informacji na temat innych opcji podczas tworzenia konta magazynu, zobacz [Szybki Start: Tworzenie konta magazynu dla Azure Data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Zrzut ekranu przedstawiający tworzenie konta magazynu w Azure Portal":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Skonfiguruj uprawnienia dla tożsamości zarządzanej na Data Lake Storage Gen2

Przypisz zarządzaną tożsamość do roli **właściciela danych obiektów blob magazynu** na koncie magazynu.

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.
1. Wybierz konto magazynu, a następnie wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla konta. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu":::

1. Wybierz przycisk **+ Dodaj przypisanie roli** , aby dodać nową rolę.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **właściciela danych obiektu blob magazynu** . Następnie wybierz subskrypcję, która ma zarządzaną tożsamość i konto magazynu. Następnie wyszukaj, aby zlokalizować utworzoną wcześniej tożsamość zarządzaną przypisaną przez użytkownika. Na koniec Wybierz zarządzaną tożsamość i zostanie ona wyświetlona w obszarze **wybranych członków**.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Zrzut ekranu przedstawiający sposób przypisywania roli platformy Azure":::

1. Wybierz pozycję **Zapisz**. Wybrana tożsamość przypisana przez użytkownika jest teraz wyświetlana w obszarze wybranej roli.
1. Po zakończeniu początkowej konfiguracji można utworzyć klaster za pomocą portalu. Klaster musi znajdować się w tym samym regionie świadczenia usługi Azure co konto magazynu. Na karcie **Magazyn** w menu Tworzenie klastra wybierz następujące opcje:

    * W obszarze **Typ magazynu podstawowego** wybierz pozycję **Azure Data Lake Storage Gen2**.
    * W obszarze **podstawowe konto magazynu** Wyszukaj i wybierz nowo utworzone konto magazynu z magazynem Data Lake Storage Gen2.

    * W obszarze **tożsamość** wybierz nowo utworzoną tożsamość zarządzaną przypisaną przez użytkownika.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Ustawienia magazynu na potrzeby używania Data Lake Storage Gen2 z usługą Azure HDInsight":::

    > [!NOTE]
    > * Aby dodać konto magazynu pomocniczego z Data Lake Storage Gen2, na poziomie konta magazynu wystarczy przypisać utworzoną wcześniej tożsamość zarządzaną do nowego Data Lake Storage Gen2, który ma zostać dodany. Zaleca się dodanie konta magazynu pomocniczego z Data Lake Storage Gen2 za pośrednictwem bloku "dodatkowe konta magazynu" w usłudze HDInsight nie jest obsługiwane.
    > * Na koncie usługi Azure Blob Storage, które jest używane przez usługę HDInsight, można włączyć RA-GRS lub RA-ZRS. Jednak tworzenie klastra za pomocą pomocniczego punktu końcowego RA-GRS lub RA-ZRS nie jest obsługiwane.

## <a name="delete-the-cluster"></a>Usuwanie klastra

Zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Klaster usługi HDInsight został pomyślnie utworzony. Teraz Dowiedz się, jak korzystać z klastra.

### <a name="apache-spark-clusters"></a>Klastry Apache Spark

* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
