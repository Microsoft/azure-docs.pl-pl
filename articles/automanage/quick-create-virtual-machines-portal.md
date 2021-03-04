---
title: Szybki Start — Włączanie usługi Azure automanage dla maszyn wirtualnych w Azure Portal
description: Dowiedz się, jak szybko włączyć funkcję autozarządzania dla maszyn wirtualnych na nowej lub istniejącej maszynie wirtualnej w Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 5f5f1e70d9ae309c90291ccac1e6dd61e7a9d056
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038427"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Szybki Start: Włączanie usługi Azure automanage dla maszyn wirtualnych w Azure Portal

Rozpocznij pracę z usługą Azure automanage dla maszyn wirtualnych przy użyciu Azure Portal, aby włączyć funkcję autozarządzania na nowej lub istniejącej maszynie wirtualnej.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

> [!IMPORTANT]
> Aby włączyć Autozarządzanie przy użyciu istniejącego konta Autozarządzanie, musisz mieć rolę **współautor** w grupie zasobów zawierającej swoje maszyny wirtualne. Jeśli włączysz Autozarządzanie przy użyciu nowego konta Autozarządzanie, potrzebne są następujące uprawnienia: rola **właściciela** lub **współautor** wraz z rolami **administratora dostępu użytkowników** w Twojej subskrypcji.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Włączanie autozarządzania dla jednej maszyny wirtualnej

1. Przejdź do maszyny wirtualnej, która ma zostać włączona.

2. Kliknij wpis **automanage (wersja zapoznawcza)** w spisie treści w obszarze **operacje**.

3. Wybierz pozycję **Rozpocznij**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Rozpocznij pojedynczą maszynę wirtualną.":::

4. Wybierz ustawienia autozarządzania (środowisko, preferencje, Autozarządzanie kontem) i **Włącz** trafienie.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Włącz dla pojedynczej maszyny wirtualnej.":::

## <a name="enable-automanage-for-multiple-vms"></a>Włącz Autozarządzanie dla wielu maszyn wirtualnych

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **Autozarządzanie — najlepsze rozwiązania dotyczące usługi Azure Machine**.

2. Wybierz pozycję **Włącz na istniejącej maszynie wirtualnej**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

3. W bloku **Wybieranie maszyn** :
    1. Filtrowanie listy maszyn wirtualnych według **subskrypcji** i **grupy zasobów**.
    1. Zaznacz pole wyboru każdej maszyny wirtualnej, którą chcesz dołączyć.
    1. Kliknij przycisk **Wybierz** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Wybierz istniejącą maszynę wirtualną z listy dostępnych maszyn wirtualnych.":::

4. W obszarze **środowisko** wybierz typ środowiska: Programowanie **/testowanie** lub **produkcja**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Wybierz pozycję środowiska.":::

   Kliknij pozycję **PORÓWNAJ szczegóły środowiska** , aby zobaczyć różnice między środowiskami.
    1. Wybierz środowisko na liście rozwijanej: Tworzenie */testowanie* do testowania, *produkcja* dla produkcji.
    1. Kliknij przycisk **OK** .

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Przeglądanie środowiska produkcyjnego.":::

5. Domyślnie preferencje dotyczące najlepszych rozwiązań dla **platformy Azure** są wybrane dla preferencji konfiguracji. Aby to zmienić, Utwórz nową preferencję lub wybierz istniejącą. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Utwórz preferencję.":::

6. Kliknij przycisk **Włącz**.


## <a name="enable-automanage-for-a-new-vm"></a>Włącz Autozarządzanie dla nowej maszyny wirtualnej

Zaloguj się do Azure Portal [tutaj](https://aka.ms/AzureAutomanagePreview) , aby utworzyć nową maszynę wirtualną i włączyć funkcję autozarządzania.

1. Wypełnij kartę **podstawowe** , podając szczegóły maszyny wirtualnej.

> [!NOTE]
> Sprawdź [Obsługiwane regiony](automanage-virtual-machines.md#supported-regions) w obszarze Autozarządzanie oraz obsługiwane wersje systemu [Linux dystrybucje](automanage-linux.md#supported-linux-distributions-and-versions) i [Windows Server](automanage-windows-server.md#supported-windows-server-versions).

2. Przejdź do karty **Zarządzanie** i wybierz **środowisko autozarządzania**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Włącz Autozarządzanie na karcie Zarządzanie.":::

3. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.

4. Gdy zobaczysz komunikat, że Walidacja zakończyła się pomyślnie, wybierz pozycję **Utwórz**.

## <a name="disable-automanage-for-vms"></a>Wyłącz Autozarządzanie dla maszyn wirtualnych

Szybko Zatrzymaj zarządzanie maszynami wirtualnymi przy użyciu usługi Azure automanage przez wyłączenie autozarządzania.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Wyłączanie autozarządzania na maszynie wirtualnej.":::

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
> [Azure automanage dla maszyn wirtualnych — preferencje konfiguracji niestandardowej](virtual-machines-custom-preferences.md)
