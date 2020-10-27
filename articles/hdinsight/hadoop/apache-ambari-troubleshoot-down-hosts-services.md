---
title: Interfejs użytkownika Apache Ambari pokazuje hosty i usługi w usłudze Azure HDInsight
description: Rozwiązywanie problemów z interfejsem użytkownika Apache Ambari podczas wyświetlania hostów i usług w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: e530d59feec2446679dbbfbc944e1bd0c53f3239
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546453"
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

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).