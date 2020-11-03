---
title: Alerty katalogów Apache Ambari w usłudze Azure HDInsight
description: Dyskusja i analiza możliwych przyczyn i rozwiązań dotyczących alertów dotyczących katalogów Apache Ambari w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: d8b1f705eb08d1c58b32e0cbd7c57722a6de93cc
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285529"
---
# <a name="scenario-apache-ambari-directory-alerts-in-azure-hdinsight"></a>Scenariusz: alerty katalogów Apache Ambari w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

W programie Apache Ambari są wyświetlane błędy podobne do:

```
1/1 local-dirs have errors: [ /mnt/resource/hadoop/yarn/local : Cannot create directory: /mnt/resource/hadoop/yarn/local ]
1/1 log-dirs have errors: [ /mnt/resource/hadoop/yarn/log : Cannot create directory: /mnt/resource/hadoop/yarn/log ]
```

## <a name="cause"></a>Przyczyna

W węzłach procesu roboczego nie ma żadnego z wymienionych katalogów z alertu Ambari.

## <a name="resolution"></a>Rozwiązanie

Ręcznie Utwórz brakujące katalogi na węzłach procesu roboczego, których to dotyczy.

1. Protokół SSH do odpowiedniego węzła procesu roboczego.

1. Pobierz użytkownika root: `sudo su` .

1. Tworzenie rekursywnie wymaganych katalogów.

1. Zmień właściciela i grupę dla tych katalogów.

    ```bash
    chown -R yarn /mnt/resource/hadoop/yarn/local
    chgrp -R hadoop /mnt/resource/hadoop/yarn/local
    chown -R yarn /mnt/resource/hadoop/yarn/log
    chgrp -R hadoop /mnt/resource/hadoop/yarn/log
    ```

1. W interfejsie użytkownika Apache Ambari Wyłącz opcję, a następnie Włącz alert.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]