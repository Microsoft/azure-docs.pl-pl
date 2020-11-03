---
title: Za mało miejsca na dysku w węźle klastra w usłudze Azure HDInsight
description: Rozwiązywanie problemów dotyczących miejsca na dysku węzła klastra Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: f1a994ad07980c67e37d00bffb7e605ed610bb08
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289082"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenariusz: brak miejsca na dysku w węźle klastra w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zadanie może zakończyć się niepowodzeniem z komunikatem o błędzie podobnym do: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Możesz też otrzymywać alerty Apache Ambari podobne do: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Przyczyna

Pamięć podręczna aplikacji Apache przędza mogła korzystać z całego dostępnego miejsca na dysku. Aplikacja Spark prawdopodobnie działa wydajnie.

## <a name="resolution"></a>Rozwiązanie

1. Użyj interfejsu użytkownika Ambari, aby określić, który węzeł kończy miejsce na dysku.

1. Ustal, który folder w węźle niepokojące przyczynia się do większości miejsca na dysku. Najpierw Użyj protokołu SSH do węzła, a następnie uruchom polecenie `df` , aby wyświetlić listę użycia dysku dla wszystkich instalacji. Zwykle jest to `/mnt` dysk tymczasowy używany przez OSS. Możesz wprowadzić do folderu, a następnie wpisz, `sudo du -hs` Aby wyświetlić podsumowywane rozmiary plików w folderze. Jeśli zobaczysz folder podobny do `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , oznacza to, że aplikacja nadal działa. Przyczyną może być RDD trwałość lub pośrednie losowe pliki.

1. Aby wyeliminować problem, Kasuj aplikację, która zwolni miejsce na dysku używane przez tę aplikację.

1. Jeśli problem występuje często w węzłach procesu roboczego, można dostosować ustawienia lokalnej pamięci podręcznej PRZĘDZy w klastrze.

    Otwórz interfejs użytkownika Ambari Nawiguj do PRZĘDZy--> configs--> Advanced.  
    Dodaj następujące 2 właściwości do sekcji yarn-site.xml niestandardowe i Zapisz:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Jeśli powyższe rozwiązanie nie rozwiąże problemu, Zoptymalizuj aplikację.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]