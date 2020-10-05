---
title: Szybki Start — Włączanie usługi Azure automanage dla maszyn wirtualnych w Azure Portal
description: Dowiedz się, jak szybko włączyć funkcję autozarządzania dla maszyn wirtualnych na nowej lub istniejącej maszynie wirtualnej w Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: d66c19ce3a9786a5ca0f1390acb398c2a9cf502f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91445787"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Szybki Start: Włączanie usługi Azure automanage dla maszyn wirtualnych w Azure Portal

Rozpocznij pracę z usługą Azure automanage dla maszyn wirtualnych przy użyciu Azure Portal, aby włączyć funkcję autozarządzania na nowej lub istniejącej maszynie wirtualnej.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

> [!IMPORTANT]
> Musisz mieć rolę **współautor** , aby włączyć Autozarządzanie przy użyciu istniejącego konta autozarządzania. Jeśli włączysz Autozarządzanie przy użyciu nowego konta Autozarządzanie, potrzebne są następujące uprawnienia: rola **właściciela** lub **współautor** wraz z rolami **administratora dostępu użytkowników** .


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Włączanie autozarządzania dla maszyn wirtualnych na istniejącej maszynie wirtualnej

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **Autozarządzanie — najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure**.

2. Wybierz pozycję **Włącz na istniejącej maszynie wirtualnej**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

3. W bloku **Wybieranie maszyn** :
    1. Filtrowanie listy maszyn wirtualnych według **subskrypcji** i **grupy zasobów**.
    1. Zaznacz pole wyboru każdej maszyny wirtualnej, którą chcesz dołączyć.
    1. Kliknij przycisk **Wybierz** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

4. W obszarze **profil konfiguracji**kliknij przycisk **Przeglądaj i Zmień profile i preferencje**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

5. W bloku **Wybierz profil konfiguracji + preferencje** :
    1. Wybierz profil po lewej stronie: Tworzenie */testowanie* na potrzeby testowania, *prod* dla produkcji.
    1. Kliknij przycisk **Wybierz** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

6. Kliknij przycisk **Włącz**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Włączanie autozarządzania dla maszyn wirtualnych na nowej maszynie wirtualnej

Zaloguj się do Azure Portal [tutaj](https://aka.ms/automanageportalnextstep) , aby utworzyć nową maszynę wirtualną i włączyć funkcję autozarządzania.

1. Postępuj zgodnie z instrukcjami tworzenia w [przewodniku szybki start — Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](..\virtual-machines\windows\quick-create-portal.md).

2. Po wdrożeniu maszyny wirtualnej na stronie stan wdrożenia zostanie wykorzystana zalecana **Następna część kroków** poniżej.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

3. W obszarze **następne kroki**wybierz pozycję **Włącz Autozarządzanie najlepsze rozwiązania dotyczące maszyn wirtualnych**.

4. Na stronie Automatyczne **Zarządzanie — najlepsze rozwiązania dla maszyn wirtualnych platformy Azure** **maszyny** będą automatycznie wypełniane przez nowo utworzoną maszynę wirtualną.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

5. W obszarze **profil konfiguracji**kliknij przycisk **Przeglądaj i Zmień profile i preferencje**.

6. W bloku **Wybierz profil konfiguracji + preferencje** :
    1. Wybierz profil po lewej stronie: Tworzenie */testowanie* na potrzeby testowania, *prod* dla produkcji.
    1. Kliknij przycisk **Wybierz** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

7. Kliknij przycisk **Włącz**.

## <a name="disable-automanage-for-vms"></a>Wyłącz Autozarządzanie dla maszyn wirtualnych

Szybko Zatrzymaj zarządzanie maszynami wirtualnymi przy użyciu usługi Azure automanage przez wyłączenie autozarządzania.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

1. Przejdź do strony **Autozarządzanie — najlepsze rozwiązania dotyczącej maszyn wirtualnych platformy Azure** , która zawiera listę wszystkich maszyn wirtualnych, które są zarządzane przez nią.
1. Zaznacz pole wyboru obok maszyny wirtualnej, którą chcesz wyłączyć.
1. Kliknij przycisk **Wyłącz automanagent** .
1. Uważnie przeczytaj wiadomości w wyskakującym okienku, aby wyrazić zgodę na **wyłączenie**.


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli została utworzona nowa grupa zasobów w celu wypróbowania usługi Azure automanage dla maszyn wirtualnych i nie będzie już potrzebna, możesz usunąć grupę zasobów. Usunięcie grupy spowoduje również usunięcie maszyny wirtualnej i wszystkich zasobów w grupie zasobów.

Usługa Azure automanage tworzy domyślne grupy zasobów do przechowywania zasobów w programie. Sprawdź grupy zasobów, które mają konwencję nazewnictwa "DefaultResourceGroupRegionName" i "AzureBackupRGRegionName", aby oczyścić wszystkie zasoby.

1. Wybierz **grupę zasobów**.
1. Na stronie grupy zasobów wybierz pozycję **Usuń**.
1. Po wyświetleniu monitu potwierdź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start włączono usługę Azure automanage dla maszyn wirtualnych. 

Odkryj, jak można tworzyć i stosować niestandardowe preferencje podczas włączania autozarządzania na maszynie wirtualnej. 

> [!div class="nextstepaction"]
> [Azure automanage dla maszyn wirtualnych — niestandardowy profil konfiguracji](virtual-machines-custom-preferences.md)
