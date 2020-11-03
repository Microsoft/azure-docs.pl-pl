---
title: Interfejs użytkownika Apache Ambari pokazuje hosty i usługi w usłudze Azure HDInsight
description: Rozwiązywanie problemów z interfejsem użytkownika Apache Ambari podczas wyświetlania hostów i usług w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 9c9676fa1457be014098b8190986c617262e628d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286498"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scenariusz: interfejs użytkownika Apache Ambari pokazuje hosty i usługi w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Interfejs użytkownika Apache Ambari jest dostępny, ale w interfejsie użytkownika są widoczne niemal wszystkie usługi, a wszystkie hosty przedstawiające utratę pulsu.

## <a name="cause"></a>Przyczyna

W większości scenariuszy jest to problem z serwerem Ambari nieuruchomionym na aktywnym węzła głównego. Sprawdź, który węzła głównego jest aktywnym węzła głównego i upewnij się, że Ambari-serwer działa w jednym z nich. Nie uruchamiaj ręcznie programu Ambari-Server, zezwól usłudze kontrolera trybu failover na uruchomienie usługi Ambari-Server w prawym węzła głównego. Aby wymusić przejście w tryb failover, uruchom ponownie aktywny węzła głównego.

Problemy dotyczące sieci mogą również powodować ten problem. W każdym węźle klastra sprawdź, czy można wykonać polecenie ping `headnodehost` . Istnieje rzadki przypadek, w którym żaden węzeł klastra nie może połączyć się z `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Rozwiązanie

Zwykle ponowny rozruch aktywnego węzła głównego będzie wyeliminować ten problem. Jeśli nie, skontaktuj się z zespołem pomocy technicznej usługi HDInsight.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]