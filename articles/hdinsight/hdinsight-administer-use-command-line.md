---
title: Zarządzanie klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak zarządzać klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure. Typy klastrów obejmują Apache Hadoop, Spark, HBase, burzy, Kafka, interaktywne zapytania i usługi ML.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84699366"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Zarządzanie klastrami usługi Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak zarządzać klastrami usługi Azure HDInsight przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) . Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w celu wykonania kroków.

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz opcję **Wypróbuj** w prawym górnym rogu bloku kodu. W przeciwnym razie wprowadź poniższe polecenie:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Wyświetl listę klastrów

Użyj [AZ HDInsight list](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) , aby wyświetlić listę klastrów. Edytuj poniższe polecenia, zastępując je `RESOURCE_GROUP_NAME` nazwą grupy zasobów, a następnie wprowadź następujące polecenia:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Pokaż klaster

Użyj [AZ HDInsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) , aby wyświetlić informacje dla określonego klastra. Edytuj poniższe polecenie, zastępując `RESOURCE_GROUP_NAME` i `CLUSTER_NAME` z odpowiednimi informacjami, a następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Użyj [AZ HDInsight Delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) , aby usunąć określony klaster. Edytuj poniższe polecenie, zastępując `RESOURCE_GROUP_NAME` i `CLUSTER_NAME` z odpowiednimi informacjami, a następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Możesz również usunąć klaster, usuwając grupę zasobów zawierającą klaster. Pamiętaj, że spowoduje to usunięcie wszystkich zasobów w grupie, w tym domyślnego konta magazynu.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Użyj [AZ HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) size, aby zmienić rozmiar określonego klastra usługi HDInsight na określony rozmiar. Edytuj poniższe polecenie, zastępując je zastępując `RESOURCE_GROUP_NAME` i `CLUSTER_NAME` odpowiednimi informacjami. Zamień na `WORKERNODE_COUNT` żądaną liczbę węzłów procesu roboczego w klastrze. Aby uzyskać więcej informacji na temat skalowania klastrów, zobacz [skalowanie klastrów usługi HDInsight](./hdinsight-scaling-best-practices.md). Wprowadź polecenie:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wykonywania różnych zadań administracyjnych klastra usługi HDInsight. Więcej informacji można znaleźć w następujących artykułach:

* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie usługą HDInsight przy użyciu Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
