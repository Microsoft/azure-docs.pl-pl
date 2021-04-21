---
title: Bezpieczny magazyn transferu & Apache Hadoop — Azure HDInsight
description: Dowiedz się, jak tworzyć klastry usługi HDInsight z kontami magazynu platformy Azure z bezpiecznym transferem.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 22804015ebf0344c00e60c88f780fe22ba440b52
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774993"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Klastry Apache Hadoop z kontami magazynu z bezpiecznym transferem na Azure HDInsight

Funkcja [Wymagany bezpieczny transfer](../storage/common/storage-require-secure-transfer.md) poprawia bezpieczeństwo konta usługi Azure Storage poprzez wymuszanie kierowania wszystkich zapytań do konta przez zabezpieczone połączenie. Funkcja ta oraz schemat wasbs są obsługiwane tylko w klastrze usługi HDInsight w wersji 3.6 lub nowszym.

> [!IMPORTANT]
> Włączenie bezpiecznego transferu magazynu po utworzeniu klastra może spowodować błędy podczas korzystania z konta magazynu i nie jest zalecane. Lepiej jest utworzyć nowy klaster przy użyciu konta magazynu z włączonym bezpiecznym transferem.

## <a name="storage-accounts"></a>Konta magazynu

### <a name="azure-portal"></a>Azure Portal

Domyślnie wymagana właściwość bezpiecznego transferu jest włączona podczas tworzenia konta magazynu w Azure Portal.

Aby zaktualizować istniejące konto magazynu za pomocą Azure Portal, zobacz [Require secure transfer with Azure Portal (Wymaganie](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)bezpiecznego transferu za pomocą Azure Portal ).

### <a name="powershell"></a>PowerShell

W przypadku polecenia cmdlet programu PowerShell [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)upewnij się, że parametr `-EnableHttpsTrafficOnly` jest ustawiony na wartość `1` .

Aby zaktualizować istniejące konto magazynu przy użyciu programu PowerShell, zobacz [Require secure transfer with PowerShell (Wymaganie bezpiecznego transferu za pomocą programu PowerShell).](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W przypadku polecenia interfejsu wiersza polecenia platformy Azure [az storage account create](/cli/azure/storage/account#az_storage_account_create)upewnij się, że parametr jest ustawiony na wartość `--https-only` `true` .

Aby zaktualizować istniejące konto magazynu za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Require secure transfer with Azure CLI (Wymaganie bezpiecznego transferu za pomocą interfejsu wiersza polecenia platformy Azure).](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>Dodawanie kolejnych kont magazynu

Dostępnych jest kilka opcji dodawania kolejnych kont magazynu z bezpiecznym transferem:

* Zmodyfikuj szablon usługi Azure Resource Manager w ostatniej sekcji.
* Utwórz klaster przy użyciu witryny [Azure Portal](https://portal.azure.com) i określ połączone konto magazynu.
* Użyj akcji skryptu, aby dodać kolejne konta magazynu z bezpiecznym transferem do istniejącego klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [Dodawanie kolejnych kont magazynu do usługi HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Następne kroki

* Użycie usługi Azure Storage (WASB) zamiast [systemu plików HDFS usługi Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako domyślnego magazynu danych
* Aby uzyskać informacje o sposobie używania usługi Azure Storage przez usługę HDInsight, zobacz [Używanie usługi Azure Storage z usługą HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Aby uzyskać informacje na temat przekazywania danych do usługi HDInsight, zobacz [Przekazywanie danych do usługi HDInsight](hdinsight-upload-data.md).