---
title: Utwórz preferencję niestandardową w usłudze Azure automanage dla maszyn wirtualnych
description: Dowiedz się, jak dostosować konfigurację środowiska w usłudze Azure Autozarządzanie i ustawić własne preferencje.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647970"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Utwórz preferencję niestandardową w usłudze Azure automanage dla maszyn wirtualnych

Usługa Azure automanage dla najlepszych rozwiązań dotyczących maszyn wirtualnych ma domyślne środowiska, które można dostosować w razie potrzeby. W tym artykule wyjaśniono, jak można ustawić własne preferencje po włączeniu autozarządzania na nowej lub istniejącej maszynie wirtualnej.

Obecnie obsługujemy dostosowania dotyczące [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) i [ochrony przed złośliwym oprogramowaniem firmy Microsoft](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Nie można zmienić środowiska ani preferencji na maszynie wirtualnej, gdy jest włączona funkcja autozarządzania. Należy wyłączyć Autozarządzanie dla tej maszyny wirtualnej, a następnie włączyć ją ponownie przy użyciu wymaganego środowiska konfiguracji i preferencji.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

> [!IMPORTANT]
> Następujące uprawnienie usługi Azure RBAC jest konieczne do włączenia funkcji Autozarządzanie: rola **właściciela** lub **współautora** wraz z rolami **administratora dostępu użytkowników** .


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Włączanie autozarządzania dla maszyn wirtualnych na istniejącej maszynie wirtualnej

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **Autozarządzanie — najlepsze rozwiązania dotyczące usługi Azure Machine**.

2. Wybierz pozycję **Włącz na istniejącej maszynie wirtualnej**.

3. W bloku **Wybieranie maszyn** :
    1. Filtrowanie listy maszyn wirtualnych według **subskrypcji** i **grupy zasobów**.
    1. Zaznacz pole wyboru każdej maszyny wirtualnej, którą chcesz dołączyć.
    1. Kliknij przycisk **Wybierz** .

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Wybierz istniejącą maszynę wirtualną z listy dostępnych maszyn wirtualnych.":::

    > [!NOTE]
    > Kliknij pozycję **Pokaż nieuprawnionych maszyn** , aby wyświetlić listę nieobsługiwanych maszyn i przyczynę. 

4. W obszarze **Konfiguracja** kliknij pozycję **PORÓWNAJ środowiska**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Porównaj środowiska.":::

5. W bloku **szczegóły środowiska** Wybierz środowisko z menu rozwijanego: Tworzenie */testowanie* na potrzeby testowania, *prod* dla środowiska produkcyjnego i kliknij przycisk **OK** .

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Przeglądanie środowiska produkcyjnego.":::

6. Po wybraniu środowiska możesz wybrać pozycję **Preferencje konfiguracji**. Domyślnie zostanie użyte preferencje najlepszych rozwiązań dotyczących platformy Azure. Ta preferencja zawiera użycie zalecanych ustawień dla każdej usługi. Zmodyfikuj te ustawienia, tworząc preferencję niestandardową: 
    1. Kliknij przycisk **Utwórz nowe preferencje**.
    1. W bloku **Tworzenie preferencji konfiguracji** Wypełnij kartę podstawowe:
        1. Subskrypcja
        1. Grupa zasobów
        1. Nazwa preferencji
        1. Region (Region)

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Wypełnij preferencje konfiguracji.":::

7. Dostosuj odpowiednie preferencje konfiguracji.
        
    > [!NOTE]
    > W przypadku zmiany konfiguracji środowiska dozwolone są tylko te zmiany, które nadal pasują do naszych najlepszych rozwiązań.

8. Przejrzyj szczegóły konfiguracji.
9. Kliknij przycisk **Utwórz**.

10. Kliknij przycisk **Włącz**.


## <a name="disable-automanage-for-vms"></a>Wyłącz Autozarządzanie dla maszyn wirtualnych

Szybko Zatrzymaj zarządzanie maszynami wirtualnymi przy użyciu usługi Azure automanage przez wyłączenie autozarządzania.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Wyłączanie autozarządzania na maszynie wirtualnej.":::

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

Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi pytań i odpowiedzi. 

> [!div class="nextstepaction"]
> [Często zadawane pytania](faq.md)