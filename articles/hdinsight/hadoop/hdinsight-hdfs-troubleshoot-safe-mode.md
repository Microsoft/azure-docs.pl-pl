---
title: Lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight
description: Rozwiązywanie problemów z lokalnym systemem Apache HDFS zablokowany w trybie awaryjnym w klastrze Apache w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944452"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>Scenariusz: lokalny system plików HDFS zablokowany w trybie awaryjnym w klastrze usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Lokalny rozproszony system plików Apache Hadoop (HDFS) jest zablokowany w trybie awaryjnym w klastrze usługi HDInsight. Zostanie wyświetlony komunikat o błędzie podobny do poniższego:

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>Przyczyna

Klaster usługi HDInsight został przeskalowany w dół do bardzo kilku węzłów poniżej lub liczba węzłów znajduje się w pobliżu współczynnika replikacji systemu plików HDFS.

## <a name="resolution"></a>Rozwiązanie

1. Zgłoś stan systemu plików HDFS w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. Sprawdź integralność systemu plików HDFS w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. W przypadku określenia braku, uszkodzenia lub w replikowanych blokach lub można zignorować te bloki, uruchom następujące polecenie, aby przełączyć węzeł nazwy z trybu awaryjnego:

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]