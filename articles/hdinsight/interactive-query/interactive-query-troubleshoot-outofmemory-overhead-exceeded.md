---
title: Sprzężenia w Apache Hive prowadzi do błędu OutOfMemory — Azure HDInsight
description: Postępowanie z błędami OutOfMemory "Przekroczono limit narzutów GC"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895187"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenariusz: sprzężenia w Apache Hive prowadzi do błędu OutOfMemory w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania interakcyjnych składników zapytań w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Domyślne zachowanie Apache Hive sprzężeń polega na załadowaniu całej zawartości tabeli do pamięci, aby można było wykonać sprzężenie bez konieczności wykonywania kroków mapy/zmniejszenia. Jeśli tabela programu Hive jest zbyt duża, aby zmieścić ją w pamięci, zapytanie może zakończyć się niepowodzeniem.

## <a name="cause"></a>Przyczyna

Po uruchomieniu sprzężeń w gałęzi o wystarczającym rozmiarze wystąpi następujący błąd:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Rozwiązanie

Nie Zezwalaj na załadowanie tabel do pamięci przy użyciu sprzężeń (zamiast przeprowadzenia mapy/kroku zmniejszenia) przez ustawienie następujących wartości konfiguracji programu Hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienie tej wartości nie rozwiązało problemu, odwiedź jedną z następujących czynności...

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
