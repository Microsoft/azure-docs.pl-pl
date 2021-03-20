---
title: USTERKa nietrwałego zablokowania błędów procesora CPU z klastra usługi Azure HDInsight
description: W dziennikach systemu Azure HDInsight występuje procesor CPU nietrwałego zablokowania błędów licznika wydajności
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944345"
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
    | Parametry |Nie dotyczy |

    Wybierz pozycję **Utrwalaj tę akcję skryptu..** ., jeśli chcesz wykonać skrypt po dodaniu nowych węzłów.

1. Wybierz przycisk **Utwórz**.

1. Poczekaj na pomyślne wykonanie.

1. Wykonaj akcję skryptu w węźle głównym, wykonując te same kroki co krok 3, ale tym razem z typami węzłów: główna.

1. Poczekaj na pomyślne wykonanie.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]