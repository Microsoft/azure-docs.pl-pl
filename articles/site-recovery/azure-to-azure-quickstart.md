---
title: Konfigurowanie odzyskiwania po awarii maszyny wirtualnej platformy Azure w regionie pomocniczym z Azure Site Recovery
description: Szybko Skonfiguruj odzyskiwanie po awarii w innym regionie platformy Azure dla maszyny wirtualnej platformy Azure przy użyciu usługi Azure Site Recovery.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "86135700"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Szybki Start: Konfigurowanie odzyskiwania po awarii w regionie pomocniczym platformy Azure dla maszyny wirtualnej platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii ciągłości działania i odzyskiwania po awarii (BCDR), utrzymując aplikacje biznesowe w trybie online podczas planowanych i nieplanowanych przestojów. Site Recovery zarządza i organizuje odzyskiwanie po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, w tym replikacji, trybu failover i odzyskiwania.

W tym przewodniku szybki start opisano sposób konfigurowania odzyskiwania po awarii dla maszyny wirtualnej platformy Azure przez replikację do pomocniczego regionu platformy Azure. Ogólnie rzecz biorąc, ustawienia domyślne są używane do włączania replikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest subskrypcja platformy Azure i maszyna wirtualna.

- Jeśli nie masz konta platformy Azure z aktywną subskrypcją, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Zalecana jest maszyna wirtualna z co najmniej 1 GB pamięci RAM. [Dowiedz się więcej](../virtual-machines/windows/quick-create-portal.md) na temat sposobu tworzenia maszyny wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Włączanie replikacji maszyny wirtualnej platformy Azure

Poniższe kroki umożliwiają replikację maszyny wirtualnej w lokacji dodatkowej.

1. Na Azure Portal z menu **Narzędzia główne**  >  **maszyny wirtualne** wybierz maszynę wirtualną do replikacji.
1. W obszarze **operacje** wybierz pozycję **odzyskiwanie po awarii**.
1. Z **Basics**  >  **regionu docelowego**podstawy wybierz region docelowy.
1. Aby wyświetlić ustawienia replikacji, wybierz pozycję **Przegląd + Rozpocznij replikację**. Jeśli musisz zmienić ustawienia domyślne, wybierz pozycję **Ustawienia zaawansowane**.
1. Aby uruchomić zadanie umożliwiające replikację maszyny wirtualnej, wybierz pozycję **Rozpocznij replikację**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Włącz replikację.":::

## <a name="verify-settings"></a>Weryfikowanie ustawień

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu Azure Portal wybierz pozycję **maszyny wirtualne** , a następnie wybierz zreplikowaną maszynę wirtualną.
1. W obszarze **operacje** wybierz pozycję **odzyskiwanie po awarii**.
1. Aby wyświetlić szczegóły replikacji z **przeglądu** , wybierz pozycję **Essentials**. Więcej szczegółów znajduje się w temacie **kondycja i stan**, **gotowość trybu failover**oraz mapa **widoku infrastruktury** .

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Włącz replikację.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby zatrzymać replikację maszyny wirtualnej w regionie podstawowym, należy wyłączyć replikację:

- Ustawienia replikacji źródła są automatycznie czyszczone.
- Rozszerzenie Site Recovery zainstalowane na maszynie wirtualnej podczas replikacji nie jest usuwane.
- Rozliczanie Site Recovery dla maszyny wirtualnej zostanie zatrzymane.

Aby wyłączyć replikację, wykonaj następujące czynności:

1. W menu Azure Portal wybierz pozycję **maszyny wirtualne** , a następnie wybierz zreplikowaną maszynę wirtualną.
1. W obszarze **operacje** wybierz pozycję **odzyskiwanie po awarii**.
1. Na stronie **Przegląd**wybierz pozycję **Wyłącz replikację**.
1. Aby odinstalować rozszerzenie Site Recovery, przejdź do rozszerzeń **ustawień**maszyny wirtualnej  >  **Extensions**.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Włącz replikację.":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku Szybki start zreplikowano pojedynczą maszynę wirtualną w regionie pomocniczym. Następnie skonfiguruj replikację dla wielu maszyn wirtualnych platformy Azure.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-tutorial-enable-replication.md)
