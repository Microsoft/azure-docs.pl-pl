---
title: Replikowanie maszyn wirtualnych platformy Azure z Bezpośrednie miejsca do magazynowania przy użyciu Azure Site Recovery
description: Dowiedz się, jak replikować maszyny wirtualne platformy Azure z systemem Bezpośrednie miejsca do magazynowania przy użyciu Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424832"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikowanie maszyn wirtualnych platformy Azure z systemem Bezpośrednie miejsca do magazynowania do innego regionu

W tym artykule opisano sposób włączania odzyskiwania po awarii maszyn wirtualnych platformy Azure z uruchomioną funkcją bezpośrednie miejsca do magazynowania.

>[!NOTE]
>W przypadku klastrów bezpośrednich miejsc do magazynowania są obsługiwane tylko spójne punkty odzyskiwania.
>

[Bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) to magazyn zdefiniowany przez oprogramowanie, który umożliwia tworzenie [klastrów gościa](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) na platformie Azure.  Klaster gościa w Microsoft Azure to klaster trybu failover składający się z maszyn wirtualnych IaaS. Umożliwia obsługę obciążeń hostowanych maszyn wirtualnych w trybie failover w ramach klastrów gościa, zapewniając wyższą umowę SLA dostępności dla aplikacji, niż może zapewnić pojedynczą maszynę wirtualną platformy Azure. Jest to przydatne w scenariuszach, w których maszyna wirtualna obsługuje krytyczne aplikacje, takie jak SQL lub serwer plików skalowalny w poziomie.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Odzyskiwanie po awarii za pomocą funkcji bezpośrednie miejsca do magazynowania

Typowym scenariuszem może być klaster gościa maszyn wirtualnych na platformie Azure w celu uzyskania większej odporności aplikacji, takiej jak serwer plików skalowalny w poziomie. Chociaż może to zapewnić wysoką dostępność aplikacji, należy chronić te aplikacje przy użyciu Site Recovery dla dowolnego błędu poziomu regionu. Site Recovery replikuje dane z jednego regionu do innego regionu platformy Azure i łączy klaster w regionie odzyskiwania po awarii w przypadku przejścia w tryb failover.

Poniższy diagram przedstawia klaster trybu failover maszyny wirtualnej platformy Azure z dwoma węzłami przy użyciu funkcji bezpośrednie miejsca do magazynowania.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Dwie maszyny wirtualne platformy Azure w klastrze trybu failover systemu Windows i każda maszyna wirtualna mają co najmniej dwa dyski danych.
- Funkcja S2D synchronizuje dane na dysku danych i przedstawia zsynchronizowany magazyn jako pulę magazynów.
- Pula magazynów prezentuje jako udostępniony wolumin klastra (CSV) klaster trybu failover.
- Klaster trybu failover używa woluminu CSV dla dysków danych.

**Zagadnienia dotyczące odzyskiwania po awarii**

1. Podczas konfigurowania [monitora chmury](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) dla klastra należy zachować monitor w regionie odzyskiwania po awarii.
2. Jeśli przejdziesz do trybu failover maszyny wirtualne w podsieci w regionie odzyskiwania po awarii, który różni się od regionu źródłowego, należy zmienić adres IP klastra po przejściu w tryb pracy awaryjnej.  Aby zmienić adres IP klastra, należy użyć [skryptu planu odzyskiwania Site Recovery.](./site-recovery-runbook-automation.md)</br>
[Przykładowy skrypt](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) służący do wykonywania polecenia wewnątrz maszyny wirtualnej przy użyciu rozszerzenia niestandardowego skryptu 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Włączanie Site Recovery dla klastra programu S2D:

1. W magazynie usługi Recovery Services kliknij pozycję "+ Replikuj"
1. Zaznacz wszystkie węzły w klastrze i uczyń je częścią [grupy spójności z obsługą wiele maszyn wirtualnych](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Wybierz zasady replikacji z zachowaniem spójności aplikacji * (dostępna jest tylko Obsługa spójności awarii)
1. Włącz replikację

   ![Zrzut ekranu pokazujący lokalizację konfigurowania ustawień replikacji.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Przejdź do pozycji zreplikowane elementy i zobaczysz stan maszyny wirtualnej.
3. Obie maszyny wirtualne są chronione i są również wyświetlane jako część grupy spójności z obsługą kilku maszyn wirtualnych.

   ![Zrzut ekranu przedstawiający maszyny wirtualne są chronione i częścią grupy spójności obejmującej wiele maszyn wirtualnych.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla wielowarstwowej aplikacji sieci Web wykonaj kroki opisane w temacie [Tworzenie planu odzyskiwania przy użyciu Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover

1.  Utwórz plan odzyskiwania, dodając maszyny wirtualne.
2.  Kliknij przycisk "Dostosuj", aby zgrupować maszyny wirtualne. Domyślnie wszystkie maszyny wirtualne są częścią grupy 1.


### <a name="add-scripts-to-the-recovery-plan"></a>Dodawanie skryptów do planu odzyskiwania
Aby aplikacje działały prawidłowo, może być konieczne wykonanie pewnych operacji na maszynach wirtualnych platformy Azure po przejściu do trybu failover lub w trakcie testu pracy w trybie failover. Można zautomatyzować niektóre operacje wykonywane po przejściu w tryb failover. Można na przykład dołączyć moduł równoważenia obciążenia i zmienić adres IP klastra.


### <a name="failover-of-the-virtual-machines"></a>Tryb failover maszyn wirtualnych 
Oba węzły maszyn wirtualnych muszą przełączać się w tryb failover przy użyciu [planu odzyskiwania](./site-recovery-create-recovery-plans.md) Site Recovery 

![Ochrona storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover
1.  W Azure Portal wybierz swój magazyn Recovery Services.
2.  Wybierz utworzony plan odzyskiwania.
3.  Wybierz pozycję **Test pracy w trybie failover**.
4.  Aby rozpocząć proces testowego przełączania do trybu failover, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
5.  Gdy środowisko pomocnicze jest w pełni, wykonaj walidację.
6.  Po zakończeniu walidacji w celu oczyszczenia środowiska trybu failover wybierz pozycję **Oczyść test pracy w trybie failover**.

Aby uzyskać więcej informacji, zobacz [test pracy w trybie failover na platformie Azure w Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1.  W Azure Portal wybierz swój magazyn Recovery Services.
2.  Wybierz plan odzyskiwania, który został utworzony dla aplikacji SAP.
3.  Wybierz pozycję **Tryb failover**.
4.  Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [tryb failover w Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](./azure-to-azure-tutorial-failover-failback.md) o uruchamianiu powrotu po awarii.
