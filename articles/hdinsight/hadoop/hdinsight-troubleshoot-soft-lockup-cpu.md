---
title: USTERKa nietrwałego zablokowania błędów procesora CPU z klastra usługi Azure HDInsight
description: W dziennikach systemu Azure HDInsight występuje procesor CPU nietrwałego zablokowania błędów licznika wydajności
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894106"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenariusz: "licznik alarm: USTERKa: blokowanie nietrwałe — procesor CPU" z klastra usługi Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Dziennik systemowy jądra zawiera komunikat o błędzie: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Przyczyna

[Usterka](https://bugzilla.kernel.org/show_bug.cgi?id=199437) w jądrze systemu Linux powoduje nietrwałe zawieszanie procesora.

## <a name="resolution"></a>Rozwiązanie

Zastosuj poprawkę jądra. Poniższy skrypt uaktualnia jądro systemu Linux i ponownie uruchamia maszyny w różnym czasie w ciągu 24 godzin. Wykonaj akcję skryptu w dwóch partiach. Pierwsza partia znajduje się na wszystkich węzłach poza węzłem głównym. Druga partia jest w węźle głównym. Nie uruchamiaj na węzłach głównych i w innych węzłach w tym samym czasie.

1. Przejdź do klastra usługi HDInsight z Azure Portal.

1. Przejdź do akcji skryptu.

1. Wybierz pozycję **Prześlij nowy** i wprowadź dane wejściowe w następujący sposób:

    | Właściwość | Wartość |
    | --- | --- |
    | Typ skryptu | -Niestandardowe |
    | Nazwa |Poprawka problemu dotyczącego nieelastycznego blokowania jądra |
    | Identyfikator URI skryptu bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Typy węzłów |Proces roboczy, dozorcy |
    | Parametry |Brak |

    Wybierz pozycję **Utrwalaj tę akcję skryptu..** ., jeśli chcesz wykonać skrypt po dodaniu nowych węzłów.

1. Wybierz przycisk **Utwórz**.

1. Poczekaj na pomyślne wykonanie.

1. Wykonaj akcję skryptu w węźle głównym, wykonując te same kroki co krok 3, ale tym razem z typami węzłów: główna.

1. Poczekaj na pomyślne wykonanie.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się za pomocą [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
