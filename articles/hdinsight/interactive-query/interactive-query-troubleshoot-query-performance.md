---
title: Niska wydajność w Apache Hive LLAP zapytania w usłudze Azure HDInsight
description: Zapytania w Apache Hive LLAP są wykonywane wolniej niż oczekiwano w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288880"
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