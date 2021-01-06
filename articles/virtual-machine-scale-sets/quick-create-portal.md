---
title: Szybki Start — Tworzenie zestawu skalowania maszyn wirtualnych w Azure Portal
description: Rozpocznij pracę z wdrożeniami, Dowiedz się, jak szybko utworzyć maszynę wirtualną skalowanie Azure Portal.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 8243619bc7a32c5fa86b6e108d954674b54394e5
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913734"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Szybki start: tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure Portal

Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu automatycznego skalowania maszyn wirtualnych i zarządzanie nim. Maszyny wirtualne w zestawie skalowania możesz skalować ręcznie lub możesz zdefiniować reguły skalowania automatycznego na podstawie użycia takich zasobów jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. Moduł równoważenia obciążenia platformy Azure następnie dystrybuuje ruch do wystąpień maszyn wirtualnych w zestawie skalowania. W tym przewodniku Szybki start utworzysz zestaw skalowania maszyn wirtualnych w witrynie Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

[Moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md) platformy Azure dystrybuuje ruch przychodzący z wystąpieniami maszyn wirtualnych w dobrej kondycji. 

Najpierw utwórz publiczną usługa Load Balancer w warstwie Standardowa przy użyciu portalu. Utworzona przez Ciebie nazwa i publiczny adres IP są automatycznie konfigurowane jako fronton modułu równoważenia obciążenia.

1. W polu wyszukiwania wpisz **moduł równoważenia obciążenia**. W obszarze **Marketplace** w wynikach wyszukiwania wybierz pozycję **moduł równoważenia obciążenia**.
1. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie                 | Wartość   |
    | ---| ---|
    | Subskrypcja  | Wybierz subskrypcję.    |    
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy** i wpisz *myVMSSResourceGroup* w polu tekstowym.|
    | Nazwa           | *myLoadBalancer*         |
    | Region (Region)         | Wybierz pozycję **Wschodnie stany USA**.       |
    | Typ          | Wybierz pozycję **Publiczna**.       |
    | Jednostka SKU           | Wybierz pozycję **Standardowy**.       |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. |
    | Nazwa publicznego adresu IP  | *myPip*   |
    | Przypisanie| Static |
    | Strefa dostępności | Wybierz pozycję **Strefowo nadmiarowa**. |

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz** . 
1. Po przeprowadzeniu walidacji wybierz pozycję **Utwórz**. 

![Tworzenie modułu równoważenia obciążenia](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych
Możesz wdrożyć zestaw skalowania z obrazu systemu Windows Server lub obrazu systemu Linux, takiego jak RHEL, CentOS, Ubuntu i SLES.

1. Wpisz **zestaw skalowania** w polu wyszukiwania. W wynikach w obszarze **Marketplace** wybierz pozycję **zestawy skalowania maszyn wirtualnych**. Wybierz pozycję **Utwórz** na stronie **zestawy skalowania maszyn wirtualnych** , która spowoduje otwarcie strony **Tworzenie zestawu skalowania maszyn wirtualnych** . 
1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz *myVMSSResourceGroup* jako nazwę, a następnie wybierz przycisk **OK** . 
1. Wpisz *myScaleSet* jako nazwę zestawu skalowania.
1. W obszarze **region** wybierz region blisko Twojego obszaru.
1. Wybierz obraz z witryny Marketplace dla **obrazu**. W tym przykładzie wybrano *Ubuntu Server 18,04 LTS*.
1. Wprowadź żądaną nazwę użytkownika, a następnie wybierz typ uwierzytelniania, który preferujesz.
   - **Hasło** musi mieć długość co najmniej 12 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: jedna mała litera, jedna wielka litera, jedna cyfra i jeden znak specjalny. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące nazwy użytkownika i hasła](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Jeśli wybierzesz obraz dysku systemu operacyjnego Linux, zamiast tego możesz wybrać **klucz publiczny SSH**. Podaj tylko swój klucz publiczny, taki jak *~/.ssh/id_rsa.pub*. Możesz użyć usługi Azure Cloud Shell z portalu, aby [tworzyć i używać kluczy SSH](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    :::image type="content" source="./media/virtual-machine-scale-sets-create-portal/quick-create-scale-set.png" alt-text="Obraz przedstawia opcje tworzenia zestawów skalowania w Azure Portal.":::

1. Wybierz pozycję **dalej** , aby przenieść inne strony. 
1. Pozostaw wartości domyślne dla stron **wystąpienia** i **dyski** .
1. Na stronie **Sieć** w obszarze **równoważenie obciążenia** wybierz pozycję **tak** , aby umieścić wystąpienia zestawu skalowania za modułem równoważenia obciążenia. 
1. W obszarze **Opcje równoważenia obciążenia** wybierz pozycję **moduł równoważenia obciążenia platformy Azure**.
1. W obszarze **Wybierz moduł równoważenia obciążenia** wybierz pozycję *myLoadBalancer* , która została utworzona wcześniej.
1. W obszarze **wybierz pulę zaplecza** wybierz pozycję **Utwórz nową**, wpisz *myBackendPool*, a następnie wybierz pozycję **Utwórz**.
1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**. 
1. Po przeprowadzeniu walidacji wybierz pozycję **Utwórz** , aby wdrożyć zestaw skalowania.


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów dla zestawu skalowania, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono podstawowy zestaw skalowania w witrynie Azure Portal. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego sposobu tworzenia zestawów skalowania maszyn wirtualnych platformy Azure i zarządzania nimi.

> [!div class="nextstepaction"]
> [Tworzenie zestawów skalowania maszyn wirtualnych platformy Azure i zarządzanie nimi](tutorial-create-and-manage-powershell.md)
