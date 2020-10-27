---
title: Nie można odczytać dziennika usługi Apache przędzy w usłudze Azure HDInsight
description: Kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas pracy z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: b6bd7d807916ef53177b11df6ed9ce0b22f530be
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533346"
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

1. Z poziomu interfejsu użytkownika Ambari przejdź do konfiguracji **przędzy**  >  **Configs**  >  **Zaawansowane**  >  **zaawansowanej przędzy-lokacja** .

1. W przypadku magazynu WASB: wartość domyślna `yarn.log-aggregation.file-formats` to `IndexedFormat,TFile` . Zmień wartość na `TFile` .

1. W przypadku magazynu ADLS: wartość domyślna `yarn.nodemanager.log-aggregation.compression-type` to `gz` . Zmień wartość na `none` .

1. Zapisz zmiany i uruchom ponownie wszystkie usługi, których to dotyczy.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z programem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).