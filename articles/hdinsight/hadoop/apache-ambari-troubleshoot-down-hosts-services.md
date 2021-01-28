---
title: Interfejs użytkownika Apache Ambari pokazuje hosty i usługi w usłudze Azure HDInsight
description: Rozwiązywanie problemów z interfejsem użytkownika Apache Ambari podczas wyświetlania hostów i usług w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943262"
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