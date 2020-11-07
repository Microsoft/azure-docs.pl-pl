---
title: Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak skonfigurować harmonogram stosowania poprawek systemu operacyjnego dla klastrów usługi HDInsight opartych na systemie Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 7b789bf01a043b167d6740f09df935d9b683c48f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357763"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurowanie harmonogramu poprawek systemu operacyjnego dla klastrów usługi HDInsight opartych na systemie Linux

> [!IMPORTANT]
> Obrazy Ubuntu są dostępne dla nowego klastra usługi Azure HDInsight w ciągu trzech miesięcy od opublikowania. Uruchamianie klastrów nie jest zautomatyzowane. Aby zastosować poprawki do działającego klastra, klienci muszą używać akcji skryptu lub innych mechanizmów. Najlepszym rozwiązaniem jest uruchomienie tych akcji skryptu i zastosowanie aktualizacji zabezpieczeń bezpośrednio po utworzeniu klastra.

Usługa HDInsight zapewnia obsługę typowych zadań w klastrze, takich jak instalowanie poprawek systemu operacyjnego, aktualizacji zabezpieczeń i węzłów ponownego uruchamiania. Te zadania są wykonywane przy użyciu następujących dwóch skryptów, które mogą być uruchamiane jako [Akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md)i skonfigurowane z parametrami:

- `schedule-reboots.sh` -Wykonaj natychmiastowe ponowne uruchomienie lub Zaplanuj ponowne uruchomienie w węzłach klastra.
- `install-updates-schedule-reboots.sh` -Zainstaluj wszystkie aktualizacje, tylko aktualizacje jądra + zabezpieczenia lub tylko aktualizacje jądra.

> [!NOTE]  
> Akcje skryptu nie będą automatycznie stosowały aktualizacji dla wszystkich przyszłych cykli aktualizacji. Uruchom skrypty za każdym razem, gdy trzeba zastosować nowe aktualizacje, aby zainstalować aktualizacje, a następnie uruchom ponownie maszynę wirtualną.

## <a name="preparation"></a>Przygotowanie

Przed wdrożeniem w środowisku produkcyjnym należy zastosować poprawkę do reprezentatywnego środowiska nieprodukcyjnego. Opracowywanie planu w celu odpowiedniego przetestowania systemu przed rzeczywistą poprawką.

Od czasu do czasu z sesji SSH z klastrem może zostać wyświetlony komunikat informujący o tym, że aktualizacje zabezpieczeń są dostępne. Komunikat może wyglądać mniej więcej tak:

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

Stosowanie poprawek jest opcjonalne i według własnego uznania.

## <a name="restart-nodes"></a>Uruchom ponownie węzły
  
Harmonogram skryptów [—](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)ponowny rozruch, ustawia typ ponownego uruchomienia, który będzie wykonywany na maszynach w klastrze. Podczas przesyłania akcji skryptu ustaw jej wartość na wszystkie trzy typy węzłów: węzeł główny, węzeł procesu roboczego i dozorcy. Jeśli skrypt nie zostanie zastosowany do typu węzła, maszyny wirtualne dla tego typu węzła nie zostaną zaktualizowane ani uruchomione ponownie.

`schedule-reboots script`Akceptuje jeden parametr liczbowy:

| Parametr | Dopuszczalne wartości | Definicja |
| --- | --- | --- |
| Typ ponownego uruchomienia do wykonania | 1 lub 2 | Wartość 1 włącza zaplanowanie ponownego uruchomienia (zaplanowane w 12-24 godzinach). Wartość 2 włącza natychmiastowe ponowne uruchomienie (w ciągu 5 minut). Jeśli parametr nie zostanie określony, wartość domyślna to 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instalowanie aktualizacji i ponowne uruchamianie węzłów

Skrypt [Install-Updates-Schedule-Reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) zawiera opcje instalowania różnych typów aktualizacji i ponownego uruchamiania maszyny wirtualnej.

`install-updates-schedule-reboots`Skrypt akceptuje dwa parametry liczbowe, zgodnie z opisem w poniższej tabeli:

| Parametr | Dopuszczalne wartości | Definicja |
| --- | --- | --- |
| Typ aktualizacji do zainstalowania | 0, 1 lub 2 | Wartość 0 powoduje zainstalowanie tylko aktualizacji jądra. Wartość 1 powoduje zainstalowanie aktualizacji jądra + zabezpieczenia i 2 powoduje zainstalowanie wszystkich aktualizacji. Jeśli parametr nie zostanie podany, wartość domyślna to 0. |
| Typ ponownego uruchomienia do wykonania | 0, 1 lub 2 | Wartość 0 powoduje wyłączenie ponownego uruchomienia. Wartość 1 włącza ponowne uruchomienie harmonogramu, a 2 włącza natychmiastowe ponowne uruchomienie. Jeśli parametr nie zostanie podany, wartość domyślna to 0. Użytkownik musi zmienić parametr wejściowy 1 na parametr wejściowy 2. |

> [!NOTE]
> Skrypt należy oznaczyć jako utrwalony po zastosowaniu go w istniejącym klastrze. W przeciwnym razie wszystkie nowe węzły utworzone za pomocą operacji skalowania będą używały domyślnego harmonogramu poprawek. Jeśli zastosujesz skrypt w ramach procesu tworzenia klastra, zostanie on utrwalony automatycznie.

> [!NOTE]
> Opcja zaplanowane ponowne uruchomienie powoduje automatyczne ponowne uruchomienie aktualizacji węzłów klastra w okresie od 12 do 24 godzin i uwzględnia zagadnienia dotyczące wysokiej dostępności, domeny aktualizacji i domeny błędów. Zaplanowane ponowne uruchomienie nie kończy pracy z uruchomionymi obciążeniami, ale może odrzucać wydajność klastra w tymczasowym czasie, gdy węzły są niedostępne, co prowadzi do dłuższego czasu przetwarzania. 

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z konkretnymi krokami dotyczącymi używania akcji skryptu, zobacz następujące sekcje w temacie [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md):

- [Używanie akcji skryptu podczas tworzenia klastra](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Zastosuj akcję skryptu do działającego klastra](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
