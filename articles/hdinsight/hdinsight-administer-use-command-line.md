---
title: Zarządzanie klastrami Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zarządzać klastrami Azure HDInsight klastrach. Typy klastrów obejmują usługi Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query i ML.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: 14b88700f3968e3bfdc788abb2fc9ce90634068e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770349"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Zarządzanie klastrami Azure HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Dowiedz się, jak używać [interfejsu wiersza polecenia](/cli/azure/) platformy Azure do zarządzania Azure HDInsight klastrami. Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure. Jeśli nie zainstalowano interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure,](/cli/azure/install-azure-cli) aby uzyskać instrukcje.

* Klaster Apache Hadoop w umacie HDInsight. Zobacz [Rozpocznij z hdinsight w systemie Linux.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Zaloguj się do subskrypcji platformy Azure. Jeśli planujesz używać Azure Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. W innym przypadku wprowadź poniższe polecenie:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Lista klastrów

Użyj [az hdinsight list,](/cli/azure/hdinsight#az_hdinsight_list) aby wyświetlić listę klastrów. Edytuj poniższe polecenia, zastępując wartość nazwą grupy zasobów, a `RESOURCE_GROUP_NAME` następnie wprowadź polecenia:

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

Użyj [az hdinsight show,](/cli/azure/hdinsight#az_hdinsight_show) aby wyświetlić informacje dotyczące określonego klastra. Edytuj poniższe polecenie, zastępując wartości i odpowiednimi informacjami, a `RESOURCE_GROUP_NAME` `CLUSTER_NAME` następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Usuwanie klastrów

Użyj [az hdinsight delete,](/cli/azure/hdinsight#az_hdinsight_delete) aby usunąć określony klaster. Edytuj poniższe polecenie, zastępując wartości i odpowiednimi informacjami, a `RESOURCE_GROUP_NAME` `CLUSTER_NAME` następnie wprowadź polecenie:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Klaster można również usunąć, usuwając grupę zasobów, która zawiera klaster. Pamiętaj, że spowoduje to usunięcie wszystkich zasobów w grupie, w tym domyślnego konta magazynu.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Skalowanie klastrów

Użyj [az hdinsight resize,](/cli/azure/hdinsight#az_hdinsight_resize) aby zmienić rozmiar określonego klastra usługi HDInsight na określony rozmiar. Edytuj poniższe polecenie, zastępując wartości `RESOURCE_GROUP_NAME` i `CLUSTER_NAME` odpowiednimi informacjami. Zastąp `WORKERNODE_COUNT` odpowiednią liczbą węzłów procesu roboczego dla klastra. Aby uzyskać więcej informacji na temat skalowania klastrów, zobacz [Skalowanie klastrów usługi HDInsight](./hdinsight-scaling-best-practices.md). Wprowadź polecenie:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób wykonywania różnych zadań administracyjnych klastra usługi HDInsight. Więcej informacji można znaleźć w następujących artykułach:

* [Zarządzanie klastrami Apache Hadoop w umacie HDInsight przy użyciu Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrowanie usługą HDInsight przy użyciu Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Wprowadzenie do usługi Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)
