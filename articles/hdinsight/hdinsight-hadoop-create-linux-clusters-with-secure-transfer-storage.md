---
title: Apache Hadoop & bezpiecznego transferu magazynu — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi HDInsight z kontami magazynu platformy Azure z bezpiecznym transferem.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: a02da7237252811d89e2c19a29f49f0bf9bb3804
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945732"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop klastrów z kontami magazynu Secure transfer w usłudze Azure HDInsight

Funkcja [Wymagany bezpieczny transfer](../storage/common/storage-require-secure-transfer.md) poprawia bezpieczeństwo konta usługi Azure Storage poprzez wymuszanie kierowania wszystkich zapytań do konta przez zabezpieczone połączenie. Funkcja ta oraz schemat wasbs są obsługiwane tylko w klastrze usługi HDInsight w wersji 3.6 lub nowszym.

> [!IMPORTANT]
> Włączenie bezpiecznego transferu magazynu po utworzeniu klastra może spowodować błędy przy użyciu konta magazynu i nie jest to zalecane. Lepszym rozwiązaniem jest utworzenie nowego klastra przy użyciu konta magazynu z już włączonym bezpiecznym transferem.

## <a name="storage-accounts"></a>Konta magazynu

### <a name="azure-portal"></a>Azure Portal

Domyślnie właściwość Required Secure transfer jest włączona podczas tworzenia konta magazynu w programie Azure Portal.

Aby zaktualizować istniejące konto magazynu za pomocą Azure Portal, zobacz [Wymagaj bezpiecznego transferu z Azure Portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

W przypadku polecenia cmdlet programu PowerShell [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)upewnij się, że parametr `-EnableHttpsTrafficOnly` jest ustawiony na `1` .

Aby zaktualizować istniejące konto magazynu za pomocą programu PowerShell, zobacz [Wymagaj bezpiecznego transferu przy użyciu programu PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W przypadku polecenia Azure CLI polecenie [AZ Storage account Create](/cli/azure/storage/account#az-storage-account-create)upewnij się, że parametr `--https-only` jest ustawiony na `true` .

Aby zaktualizować istniejące konto magazynu za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Wymagaj bezpiecznego transferu przy użyciu interfejsu wiersza polecenia platformy Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Dostępnych jest kilka opcji dodawania kolejnych kont magazynu z bezpiecznym transferem:

* Zmodyfikuj szablon usługi Azure Resource Manager w ostatniej sekcji.
* Utwórz klaster przy użyciu witryny [Azure Portal](https://portal.azure.com) i określ połączone konto magazynu.
* Użyj akcji skryptu, aby dodać kolejne konta magazynu z bezpiecznym transferem do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Następne kroki

* Korzystanie z usługi Azure Storage (WASB) zamiast [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako domyślnego magazynu danych
* Aby uzyskać informacje o sposobie używania usługi Azure Storage przez usługę HDInsight, zobacz [Używanie usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Aby uzyskać informacje na temat przekazywania danych do usługi HDInsight, zobacz [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md).