---
title: Tworzenie klastrów Apache Hadoop przy użyciu programu PowerShell — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry Apache Hadoop, Apache HBase, Apache Storm lub Apache Spark w systemie Linux dla usługi HDInsight przy użyciu Azure PowerShell.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 86622bf96d4b59537a2946073fdc638e51c3852d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945838"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell to zaawansowane środowisko tworzenia skryptów, które służy do kontrolowania i automatyzowania wdrażania obciążeń i zarządzania nimi w programie Microsoft Azure. Ten dokument zawiera informacje dotyczące sposobu tworzenia klastra usługi HDInsight opartej na systemie Linux przy użyciu Azure PowerShell. Zawiera również przykładowy skrypt.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) AZ module.

## <a name="create-cluster"></a>Tworzenie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Aby utworzyć klaster usługi HDInsight przy użyciu Azure PowerShell, należy wykonać następujące procedury:

* Tworzenie grupy zasobów platformy Azure
* Tworzenie konta usługi Azure Storage
* Tworzenie kontenera obiektów blob platformy Azure
* Tworzenie klastra HDInsight

> [!NOTE]
> Tworzenie klastra usługi HDInsight przy użyciu programu PowerShell Azure Data Lake Storage Gen2 nie jest obecnie obsługiwane.

Poniższy skrypt pokazuje, jak utworzyć nowy klaster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Wartości określone dla logowania klastra są używane do tworzenia konta użytkownika Hadoop dla klastra. Użyj tego konta, aby nawiązać połączenie z usługami hostowanymi w klastrze, takimi jak interfejsów użytkownika sieci Web lub interfejsy API REST.

Wartości określone dla użytkownika SSH są używane do tworzenia użytkownika SSH dla klastra. To konto służy do uruchamiania zdalnej sesji SSH w klastrze i uruchamiania zadań. Aby uzyskać więcej informacji, zobacz dokument [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Jeśli planujesz użycie więcej niż 32 węzłów procesu roboczego (podczas tworzenia klastra lub skalowania klastra po utworzeniu), należy również określić rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
> Aby uzyskać więcej informacji o rozmiarach węzła i powiązanych kosztach, zobacz [Cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Utworzenie klastra może potrwać do 20 minut.

## <a name="create-cluster-configuration-object"></a>Tworzenie klastra: obiekt konfiguracji

Możesz również utworzyć obiekt konfiguracji usługi HDInsight przy użyciu [`New-AzHDInsightClusterConfig`](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) polecenia cmdlet. Następnie można zmodyfikować ten obiekt konfiguracji w celu włączenia dodatkowych opcji konfiguracji klastra. Na koniec Użyj `-Config` parametru [`New-AzHDInsightCluster`](/powershell/module/az.hdinsight/new-azhdinsightcluster) polecenia cmdlet, aby użyć konfiguracji.

Poniższy skrypt tworzy obiekt konfiguracji w celu skonfigurowania R Server w typie klastra usługi HDInsight. Konfiguracja umożliwia węzłowi krawędzi, RStudio i dodatkowe konto magazynu.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Użycie konta magazynu w innej lokalizacji niż klaster usługi HDInsight nie jest obsługiwane. Korzystając z tego przykładu, Utwórz dodatkowe konto magazynu w tej samej lokalizacji, w której znajduje się serwer.

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* Zobacz [Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Usuwanie klastra

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy klaster usługi HDInsight został pomyślnie utworzony, Skorzystaj z następujących zasobów, aby dowiedzieć się, jak korzystać z klastra.

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Klastry burzy

* [Tworzenie topologii języka Java na potrzeby burzy w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w środowisku burzy w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii przy użyciu burzy w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Klastry Apache Spark

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark z usługą BI: wykonywanie interaktywnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)