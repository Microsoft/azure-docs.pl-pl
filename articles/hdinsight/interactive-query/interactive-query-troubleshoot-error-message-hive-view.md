---
title: Komunikat o błędzie nie jest wyświetlany w widoku Apache Hive — usługa Azure HDInsight
description: Zapytanie kończy się niepowodzeniem w widoku Apache Hive bez żadnych szczegółów w klastrze usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288939"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenariusz: komunikat o błędzie zapytania nie jest wyświetlany w widoku Apache Hive w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Komunikat o błędzie zapytania widoku Apache Hive będzie wyglądać podobnie do tego, bez dalszych informacji:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Przyczyna

Czasami komunikat o błędzie błędu zapytania może być zbyt duży, aby można go było wyświetlić na stronie głównej widoku programu Hive.

## <a name="resolution"></a>Rozwiązanie

Sprawdź kartę powiadomienia w prawym górnym rogu Hive_view, aby wyświetlić pełną ślad stosu i komunikat o błędzie.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]