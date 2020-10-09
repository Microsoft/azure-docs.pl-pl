---
title: Korzystanie z usługi SCP z usługą Apache Hadoop w usłudze Azure HDInsight
description: Ten dokument zawiera informacje dotyczące nawiązywania połączenia z usługą HDInsight przy użyciu poleceń SSH i SCP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 44c87a3415a236f614510aa3163ad2e9cbd1f64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856181"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Korzystanie z usługi SCP z usługą Apache Hadoop w usłudze Azure HDInsight

Ten artykuł zawiera informacje dotyczące bezpiecznego transferu plików z klastrem usługi HDInsight.

## <a name="copy-files"></a>Kopiowanie plików

Narzędzia `scp` można użyć do kopiowania plików do i z poszczególnych węzłów w klastrze. Na przykład następujące polecenie kopiuje katalog `test.txt` z systemu lokalnego do podstawowego węzła głównego:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Ponieważ po znaku `:` nie określono ścieżki, plik zostanie umieszczony w katalogu macierzystym użytkownika `sshuser`.

Poniższy przykład kopiuje plik `test.txt` z katalogu macierzystego użytkownika `sshuser` w podstawowym węźle głównym do systemu lokalnego:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

Narzędzie `scp` może uzyskać dostęp tylko do systemu plików poszczególnych węzłów w klastrze. Nie można jej użyć w celu uzyskania dostępu do danych w magazynie zgodnym z systemem plików HDFS dla klastra.

Użyj narzędzia `scp`, jeśli musisz przekazać zasób do użycia z sesji SSH. Na przykład przekaż skrypt języka Python, a następnie uruchom go z sesji SSH.

Aby uzyskać informacje o bezpośrednim ładowaniu danych do magazynu zgodnego z systemem HDFS, zobacz następujące dokumenty:

* [Usługa HDInsight korzystająca z usługi Azure Storage](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight przy użyciu Azure Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Następne kroki

* [Używanie protokołu SSH z usługą HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Używanie węzłów krawędzi w usłudze HDInsight)
