---
title: Szybki start — włączanie Azure Automanage wirtualnych w Azure Portal
description: Dowiedz się, jak szybko włączyć automatycznemanage dla maszyn wirtualnych na nowej lub istniejącej maszynie wirtualnej w Azure Portal.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 7121d83f9401fe985966324afe6a61cf8396b2bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534077"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Szybki start: włączanie Azure Automanage maszyn wirtualnych w Azure Portal

Rozpoczynanie pracy z Azure Automanage maszynami wirtualnymi przy użyciu Azure Portal, aby włączyć automatycznemanagement na nowej lub istniejącej maszynie wirtualnej.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnej wersji próbnej nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przechodz do subskrypcji z płatnością zgodnie z platformą.

> [!IMPORTANT]
> Aby włączyć  automatyczne zarządzanie przy użyciu istniejącego konta automatycznego zarządzania, musisz mieć rolę Współautor w grupie zasobów zawierającej maszyny wirtualne. Jeśli w przypadku włączania funkcji Automatycznego wykonywania przy użyciu nowego konta automatycznegomanage  potrzebne są następujące uprawnienia: **Rola** właściciela lub **Współautor** wraz z rolami administratora dostępu użytkowników w ramach subskrypcji.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Włączanie automatycznegomanage dla pojedynczej maszyny wirtualnej

1. Przejdź do maszyny wirtualnej, która ma być włączyć.

2. Kliknij wpis **Automatyczne zarządzania (wersja zapoznawcza)** w spisie treści w obszarze **Operacje**.

3. Wybierz **Rozpocznij**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Wprowadzenie do pojedynczej maszyny wirtualnej.":::

4. Wybierz ustawienia Automatycznego blokowania (Środowisko, Preferencje, Konto automatycznegomanage) i wybierz pozycję **Włącz.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Włącz na pojedynczej maszynie wirtualnej.":::

## <a name="enable-automanage-for-multiple-vms"></a>Włączanie automatycznegomanage dla wielu maszyn wirtualnych

1. Na pasku wyszukiwania wyszukaj i wybierz pozycję **Automanage – Azure machine best practices (Automatycznemanage — najlepsze rozwiązania dotyczące maszyn platformy Azure).**

2. Wybierz pozycję **Włącz na istniejącej maszynie wirtualnej.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Włącz na istniejącej maszynie wirtualnej.":::

3. W bloku **Wybieranie maszyn:**
    1. Przefiltruj listę maszyn wirtualnych według **subskrypcji i** **grupy zasobów.**
    1. Zaznacz pole wyboru każdej maszyny wirtualnej, która ma zostać do dosłana.
    1. Kliknij przycisk **Wybierz.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Wybierz istniejącą maszynę wirtualną z listy dostępnych maszyn wirtualnych.":::

4. W **obszarze Środowisko** wybierz typ środowiska: **Dev/Test** lub **Production**. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Wybierz środowiska.":::

   Kliknij **pozycję Compare Environment Details (Porównaj** szczegóły środowiska), aby zobaczyć różnice między środowiskami.
    1. Wybierz środowisko z listy rozwijanej: *Tworzenia i* testowania w celu testowania, *Produkcja* dla środowiska produkcyjnego.
    1. Kliknij przycisk **OK.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Przeglądanie środowiska produkcyjnego.":::

5. Domyślnie dla preferencji **konfiguracji jest wybierana** preferencja Najlepsze rozwiązania platformy Azure. Aby to zmienić, utwórz nową preferencję lub wybierz istniejącą. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Utwórz preferencję.":::

6. Kliknij przycisk **Włącz**.


## <a name="enable-automanage-for-a-new-vm"></a>Włączanie automatycznego tworzenia nowej maszyny wirtualnej

Zaloguj się do Azure Portal [tutaj,](https://aka.ms/AzureAutomanagePreview) aby utworzyć nową maszynę wirtualną i włączyć automatyczne tworzenie.

1. Wypełnij **kartę Podstawowe,** aby podać szczegóły maszyny wirtualnej.

> [!NOTE]
> Zapoznaj się z obsługiwanymi regionami [automatycznego zarządzania](automanage-virtual-machines.md#supported-regions) i dystrybucjami systemu [Linux](automanage-linux.md#supported-linux-distributions-and-versions) obsługiwanymi przez funkcję Automatycznego zarządzania i [wersjami systemu Windows Server.](automanage-windows-server.md#supported-windows-server-versions)

2. Przejdź do karty **Zarządzanie** i wybierz środowisko **automatycznego zarządzania.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Włącz automatyczne zarządzanie na karcie Zarządzanie.":::

3. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.

4. Po wyświetleniu komunikatu z potwierdzeniem, że weryfikacja została pomyślnie przekazana, wybierz pozycję **Utwórz**.

## <a name="disable-automanage-for-vms"></a>Wyłącz automatycznemanage dla maszyn wirtualnych

Szybko przestań używać Azure Automanage dla maszyn wirtualnych, wyłączając automatycznemanagement.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Wyłączanie automatycznegomanage na maszynie wirtualnej.":::

1. Przejdź do strony **Automanage – Azure virtual machine best practices** (Automatyczne zarządzanie — najlepsze rozwiązania dla maszyn wirtualnych platformy Azure), która zawiera listę wszystkich maszyn wirtualnych zarządzanych automatycznie.
1. Zaznacz pole wyboru obok maszyny wirtualnej, którą chcesz wyłączyć.
1. Kliknij przycisk **Wyłącz automanagent.**
1. Przeczytaj uważnie komunikaty w wynikowych okienkach wyskakujących przed wyrażeniem zgody na **wyłączenie .**


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli utworzono nową grupę zasobów w celu Azure Automanage dla maszyn wirtualnych i nie jest już ona potrzebna, możesz usunąć tę grupę zasobów. Usunięcie grupy powoduje również usunięcie maszyny wirtualnej i wszystkich zasobów w grupie zasobów.

Azure Automanage domyślne grupy zasobów do przechowywania zasobów. Sprawdź grupy zasobów z konwencją nazewnictwa "DefaultResourceGroupRegionName" i "AzureBackupRGRegionName", aby wyczyścić wszystkie zasoby.

1. Wybierz **grupę zasobów**.
1. Na stronie grupy zasobów wybierz pozycję **Usuń**.
1. Po wyświetleniu monitu potwierdź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.**


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start włączono obsługę Azure Automanage wirtualnych.

Dowiedz się, jak tworzyć i stosować niestandardowe preferencje podczas włączania funkcji Automanage na maszynie wirtualnej.

> [!div class="nextstepaction"]
> [Azure Automanage dla maszyn wirtualnych — niestandardowe preferencje konfiguracji](virtual-machines-custom-preferences.md)
