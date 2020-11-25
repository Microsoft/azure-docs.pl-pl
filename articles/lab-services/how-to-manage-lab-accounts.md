---
title: Zarządzanie kontami laboratorium w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak utworzyć konto laboratorium, wyświetlić wszystkie konta laboratorium lub usunąć konto laboratorium w ramach subskrypcji platformy Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 33e5e42f65fdd34bb37b12947b5173700ad7970c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999505"
---
# <a name="create-and-manage-lab-accounts"></a>Tworzenie kont laboratoriów i zarządzanie nimi
W Azure Lab Services konto laboratorium jest kontenerem dla zarządzanych typów laboratorium, takich jak pracownie. Administrator konfiguruje konto laboratorium przy użyciu Azure Lab Services i zapewnia dostęp do właścicieli laboratorium, którzy mogą tworzyć laboratoria na koncie. W tym artykule opisano sposób tworzenia konta laboratorium, wyświetlania wszystkich kont laboratorium lub usuwania konta laboratorium.

## <a name="create-a-lab-account"></a>Tworzenie konta laboratorium
Następujące kroki ilustrują tworzenie konta laboratorium w usłudze Azure Lab Services przy użyciu witryny Azure Portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie. Wybierz pozycję **konta laboratorium** w sekcji **DevOps** . Jeśli wybierzesz gwiazdkę (`*`) obok pozycji **Konta laboratorium**, zostanie ona dodana do sekcji **ULUBIONE** w menu po lewej stronie. Od następnego razu będziesz wybierać pozycję **Konta laboratorium** w sekcji **ULUBIONE**.

    ![Wszystkie usługi -> Konta laboratorium](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na stronie **konta laboratorium** wybierz pozycję **Dodaj** na pasku narzędzi lub **Utwórz konto laboratorium** na stronie. 

    ![Wybieranie pozycji Dodaj na stronie Konta laboratorium](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na karcie **podstawy** na stronie **Tworzenie konta laboratorium** wykonaj następujące czynności: 
    1. W obszarze **Nazwa konta laboratorium** wprowadź nazwę. 
    2. Wybierz **subskrypcję platformy Azure**, w której chcesz utworzyć konto laboratorium.
    3. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** i wprowadź nazwę grupy zasobów.
    4. W obszarze **Lokalizacja** wybierz lokalizację/region, w których chcesz utworzyć konto laboratorium.
    5. W polu **Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium** określ, czy twórcy laboratoriów powinni mieć możliwość wybierania lokalizacji laboratorium. Domyślnie opcja ta jest wyłączona. W przypadku jej włączenia twórcy laboratoriów nie mogą określać lokalizacji dla tworzonych przez siebie laboratoriów. Laboratoria są tworzone w najbliższej lokalizacji geograficznej względem konta laboratorium. W przypadku włączenia tej opcji twórca laboratorium może wybrać lokalizację podczas tworzenia laboratorium. Aby uzyskać więcej informacji, zobacz [Zezwalanie na twórcę laboratorium do wybierania lokalizacji dla laboratorium](allow-lab-creator-pick-lab-location.md). 

        ![Utwórz konto laboratorium — podstawowe informacje >](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Wybierz pozycję **Dalej: Zaawansowane** w dolnej części strony, aby przejść do karty **Zaawansowane** , a następnie wykonaj następujące czynności: 
    1. Wybierz istniejącą **galerię obrazów udostępnionych** lub utwórz ją. Możesz zapisać maszynę wirtualną szablonu w galerii obrazów udostępnionych, aby mogła ona zostać ponownie użyta przez inne osoby. Aby uzyskać szczegółowe informacje na temat udostępnionych galerii obrazów, zobacz [Używanie galerii obrazów udostępnionych w Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Określ, czy chcesz **automatycznie zamykać maszyny wirtualne z systemem Windows** , gdy użytkownicy odłączają się od nich. Określ, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem. 
    3. W przypadku **równorzędnej sieci wirtualnej** wybierz równorzędną sieć wirtualną (VNET) dla sieci laboratorium. Laboratoria utworzone w ramach tego konta są połączone z wybraną siecią wirtualną i mają dostęp do zasobów w wybranej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [łączenie sieci wirtualnej laboratorium z równorzędną siecią wirtualną](how-to-connect-peer-virtual-network.md).    
    8. Określ **zakres adresów** dla maszyn wirtualnych w laboratorium. Zakres adresów powinien znajdować się w notacji Classless Inter-Domain Routing (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów. Aby uzyskać więcej informacji, zobacz [Określanie zakresu adresów dla maszyn wirtualnych w laboratorium](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > Właściwość **zakres adresów** ma zastosowanie tylko wtedy, gdy dla laboratorium jest włączona **równorzędna Sieć wirtualna** .

        ![Utwórz konto laboratorium — > zaawansowane](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Wybierz pozycję **Dalej: Tagi** w dolnej części strony, aby przełączyć się na kartę **Tagi** . Dodaj wszystkie Tagi, które chcesz skojarzyć z kontem laboratorium. Tagi to pary nazwa/wartość, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu dla wielu zasobów i grup zasobów. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../azure-resource-manager/management/tag-resources.md).

    ![Zrzut ekranu przedstawiający stronę "Tworzenie konta laboratorium" z wyróżnioną kartą znaczników.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Wybierz pozycję **Recenzja + Utwórz** u dołu strony, aby przełączyć się na kartę **Recenzja + tworzenie** . 
4. Przejrzyj informacje podsumowujące na tej stronie i wybierz pozycję **Utwórz**. 

    ![Utwórz konto laboratorium — > Tagi](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Zaczekaj na zakończenie wdrożenia, rozwiń pozycję **następne kroki**, a następnie wybierz pozycję **Przejdź do zasobu** , jak pokazano na poniższej ilustracji: 

    Możesz również wybrać **ikonę dzwonka** na pasku narzędzi (**powiadomienia**), potwierdzić, że wdrożenie zakończyło się pomyślnie, a następnie wybierz pozycję **Przejdź do zasobu**. 

    Możesz również wybrać pozycję **Odśwież** na stronie **Konta laboratorium**, a następnie wybierz utworzone konto laboratorium. 

    ![Okno Tworzenie konta laboratorium](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Zobaczysz następującą stronę **konta laboratorium**:

    ![Strona konta laboratorium](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Wyświetlanie kont laboratorium
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** z menu. 
3. Wybierz opcję **konta laboratorium** dla tego **typu**. 
    Można również filtrować według subskrypcji, grupy zasobów, lokalizacji i tagów. 

    ![Wszystkie zasoby — > konta laboratorium](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Usuwanie konta laboratorium
Postępuj zgodnie z instrukcjami podanymi w poprzedniej sekcji, która wyświetla konta laboratorium na liście. Aby usunąć konto laboratorium, wykonaj następujące instrukcje: 

1. Wybierz **konto laboratorium** , które chcesz usunąć. 
2. Na pasku narzędzi wybierz pozycję **Usuń** . 

    ![Konta laboratorium — > przycisk usuwania](./media/how-to-manage-lab-accounts/delete-button.png)
1. Wpisz **tak** w celu potwierdzenia.
1. Wybierz pozycję **Usuń**. 

    ![Usuwanie konta laboratorium — potwierdzenie](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Aby zarządzać kontami laboratorium, można również użyć polecenia AZ. LabServices PowerShell module (wersja zapoznawcza). Aby uzyskać więcej informacji, zobacz [stronę główną AZ. LabServices w witrynie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi artykułami w sekcji **przewodniki**  ->  **: Tworzenie i Konfigurowanie kont laboratorium (właściciela konta laboratorium)** w spisie treści (spis treści). 