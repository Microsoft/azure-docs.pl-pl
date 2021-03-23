---
title: Jak usunąć klaster usługi HDInsight — Azure
description: Informacje na temat różnych sposobów usuwania klastra usługi Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 2160cfcd1f12f5effe7bb182eb665f85fec863af
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863398"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane proporcjonalnie do liczby minut, więc należy usunąć klaster, gdy nie jest już używany. W tym dokumencie dowiesz się, jak usunąć klaster przy użyciu [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ module](/powershell/azure/)i [interfejsu wiersza polecenia platformy Azure](/cli/azure/).

> [!IMPORTANT]  
> Usunięcie klastra usługi HDInsight nie powoduje usunięcia kont magazynu Azure ani Data Lake Storage skojarzonych z klastrem. W przyszłości możesz ponownie użyć danych przechowywanych w tych usługach.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Z menu po lewej stronie wybierz kolejno pozycje **wszystkie usługi**  >  **Analiza** usług  >  **HDInsight** i klaster.

3. Z widoku domyślnego wybierz ikonę **Usuń** . Postępuj zgodnie z monitem, aby usunąć klaster.

    :::image type="content" source="./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png" alt-text="Przycisk usuwania klastra usługi HDInsight":::

## <a name="azure-powershell"></a>Azure PowerShell

Zastąp ciąg `CLUSTERNAME` nazwą klastra usługi HDInsight w poniższym kodzie. W wierszu polecenia programu PowerShell wprowadź następujące polecenie, aby usunąć klaster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Zastąp ciąg `CLUSTERNAME` nazwą klastra usługi HDInsight i `RESOURCEGROUP` nazwą grupy zasobów w poniższym kodzie.  W wierszu polecenia wprowadź następujące polecenie, aby usunąć klaster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
