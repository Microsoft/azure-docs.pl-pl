---
title: Nie można dodać węzłów do klastra usługi Azure HDInsight
description: Rozwiązywanie problemów z nie można dodać węzłów do Apache Hadoop klastra w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b8e8bf0a8a0555b23799600c3958fbbfd5a510dd
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540571"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenariusz: nie można dodać węzłów do klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można dodać węzłów do klastra usługi Azure HDInsight.

## <a name="cause"></a>Przyczyna

Przyczyny mogą się różnić.

## <a name="resolution"></a>Rozwiązanie

Korzystając z funkcji [rozmiar klastra](../hdinsight-scaling-best-practices.md) , Oblicz liczbę dodatkowych rdzeni wymaganych przez klaster. Zależy to od łącznej liczby rdzeni w nowych węzłach procesów roboczych. Następnie spróbuj wykonać co najmniej jedną z następujących czynności:

* Sprawdź, czy są dostępne rdzenie w lokalizacji klastra.

* Sprawdź liczbę dostępnych rdzeni w innych lokalizacjach. Rozważ ponowne utworzenie klastra w innej lokalizacji z odpowiednią liczbą dostępnych rdzeni.

* Aby zwiększyć limit przydziału rdzeni dla określonej lokalizacji, wyślij bilet pomocy technicznej dotyczący zwiększenia limitu przydziału rdzeni w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).