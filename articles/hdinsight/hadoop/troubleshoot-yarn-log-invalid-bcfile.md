---
title: Nie można odczytać dziennika usługi Apache przędzy w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas pracy z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776198"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenariusz: nie można odczytać dziennika usługi Apache przędz w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Dzienniki przędzy usługi Apache, które znajdują się na koncie magazynu, nie można odczytać przez człowieka. Analizator plików nie działa i generuje następujący komunikat o błędzie:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Przyczyna

Dziennik przędzy Apache jest agregowany do `IndexFile` formatu, który nie jest obsługiwany przez parser plików.

## <a name="resolution"></a>Rozwiązanie

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Z poziomu interfejsu użytkownika Ambari przejdź do konfiguracji **przędzy**  >  **Configs**  >  **Zaawansowane**  >  **zaawansowanej przędzy-lokacja**.

1. W przypadku magazynu WASB: wartość domyślna `yarn.log-aggregation.file-formats` to `IndexedFormat,TFile` . Zmień wartość na `TFile` .

1. W przypadku magazynu ADLS: wartość domyślna `yarn.nodemanager.log-aggregation.compression-type` to `gz` . Zmień wartość na `none` .

1. Zapisz zmiany i uruchom ponownie wszystkie usługi, których to dotyczy.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
