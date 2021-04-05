---
title: Niska wydajność w Apache Hive LLAP zapytania w usłudze Azure HDInsight
description: Zapytania w Apache Hive LLAP są wykonywane wolniej niż oczekiwano w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 80fe51c2131da5a6fcb69eb5526b5d4f33a45e03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930898"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenariusz: niska wydajność w Apache Hive LLAP zapytania w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślne konfiguracje klastrów nie są wystarczająco dostrojone dla obciążenia. Zapytania w usłudze Hive LLAP są wykonywane wolniej niż oczekiwano.

## <a name="cause"></a>Przyczyna

Przyczyną może być wiele przyczyn.

## <a name="resolution"></a>Rozwiązanie

LLAP jest zoptymalizowany pod kątem zapytań obejmujących sprzężenia i agregacje. Zapytania podobne do następujących nie działają w interaktywnym klastrze Hive:

```
select * from table where column = "columnvalue"
```

Aby poprawić wydajność zapytań punktów w programie Hive LLAP, ustaw następujące konfiguracje:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Możesz również zwiększyć użycie pamięci podręcznej LLAP, aby zwiększyć wydajność przy użyciu następującej zmiany konfiguracji:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]