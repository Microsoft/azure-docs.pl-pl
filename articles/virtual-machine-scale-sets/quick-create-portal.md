---
title: Szybki start — tworzenie zestawu skalowania maszyny wirtualnej w witrynie Azure portal
description: Rozpocznij wdrażanie, ucząc się, jak szybko utworzyć skalę maszyny wirtualnej w witrynie Azure portal.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: mvc, H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: jushiman
ms.openlocfilehash: 5855c53a73ac94985f34f229fbe5ab77067ef17d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680416"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Szybki start: tworzenie zestawu skalowania maszyn wirtualnych w witrynie Azure Portal

Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. Maszyny wirtualne w zestawie skalowania możesz skalować ręcznie lub możesz zdefiniować reguły skalowania automatycznego na podstawie użycia takich zasobów jak procesor CPU, zapotrzebowanie na pamięć lub ruch sieciowy. Moduł równoważenia obciążenia platformy Azure następnie dystrybuuje ruch do wystąpień maszyn wirtualnych w zestawie skalowania. W tym przewodniku Szybki start utworzysz zestaw skalowania maszyn wirtualnych w witrynie Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Moduł [równoważenia obciążenia](../load-balancer/load-balancer-overview.md) platformy Azure rozdziela ruch przychodzący między wystąpienia dobrej maszyny wirtualnej. 

Najpierw utwórz publiczny standardowy moduł równoważenia obciążenia przy użyciu portalu. Utworzona przez Ciebie nazwa i publiczny adres IP są automatycznie konfigurowane jako fronton modułu równoważenia obciążenia.

1. W polu wyszukiwania wpisz program **load balancer**. W obszarze **Marketplace** w wynikach wyszukiwania wybierz numer **Równoważenia obciążenia**.
1. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie                 | Wartość   |
    | ---| ---|
    | Subskrypcja  | Wybierz subskrypcję.    |    
    | Grupa zasobów | Wybierz **pozycję Utwórz nowy** i wpisz *myVMSSResourceGroup* w polu tekstowym.|
    | Nazwa           | *myLoadBalancer*         |
    | Region         | Wybierz pozycję **Wschodnie stany USA**.       |
    | Typ          | wybierz pozycję **Publiczny**.       |
    | SKU           | Wybierz **opcję Standardowy**.       |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP  | *MyPip ( MyPip )*   |
    | Przypisanie| Statyczny |

1. Po zakończeniu wybierz **pozycję Przejrzyj + utwórz** 
1. Po przejściu weryfikacji wybierz pozycję **Utwórz**. 

![Tworzenie modułu równoważenia obciążenia](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych
Możesz wdrożyć zestaw skalowania z obrazu systemu Windows Server lub obrazu systemu Linux, takiego jak RHEL, CentOS, Ubuntu i SLES.

1. Wpisz **zestaw skalowania** w polu wyszukiwania. W wynikach w obszarze **Marketplace**wybierz pozycję **Zestawy skalowania maszyny wirtualnej**. Zostanie otwarta strona **Utwórz zestaw skalowania maszyny wirtualnej.** 
1. Na karcie **Podstawowe**, w obszarze **Szczegóły projektu**, upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz opcję **Utwórz nową** grupę zasobów. Wpisz *myVMSSResourceGroup* dla nazwy, a następnie wybierz **przycisk OK** . 
1. Wpisz *myScaleSet* jako nazwę zestawu skalowania.
1. W **obszarze Region**wybierz region, który znajduje się blisko Obszaru.
1. Pozostaw domyślną wartość **ScaleSet VMs** dla **programu Orchestrator**.
1. Wybierz obraz z portalu Marketplace dla **obrazu**. W tym przykładzie wybraliśmy *Ubuntu Server 18.04 LTS*.
1. Wprowadź żądaną nazwę użytkownika, a następnie wybierz typ uwierzytelniania, który preferujesz.
   - **Hasło** musi mieć długość co najmniej 12 znaków i spełniać trzy z czterech następujących wymagań dotyczących złożoności: jedna mała litera, jedna wielka litera, jedna cyfra i jeden znak specjalny. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące nazwy użytkownika i hasła](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Jeśli wybierzesz obraz dysku systemu operacyjnego Linux, zamiast tego możesz wybrać **klucz publiczny SSH**. Podaj tylko swój klucz publiczny, taki jak *~/.ssh/id_rsa.pub*. Możesz użyć usługi Azure Cloud Shell z portalu, aby [tworzyć i używać kluczy SSH](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Tworzenie zestawu skalowania maszyn wirtualnych](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Wybierz **przycisk Dalej,** aby przenieść pozostałe strony. 
1. Pozostaw ustawienia domyślne dla stron **Wystąpienie** i **Dyski.**
1. Na stronie **Sieć** w obszarze **Równoważenie obciążenia**wybierz pozycję **Tak,** aby umieścić wystąpienia zestawu skalowania za modułem równoważenia obciążenia. 
1. W **obszarze Opcje równoważenia obciążenia**wybierz pozycję Azure load **balancer**.
1. W **obszarze Wybierz moduł równoważenia obciążenia**wybierz utworzony wcześniej *myLoadBalancer.*
1. W **obszarze Wybierz pulę zaplecza**wybierz pozycję **Utwórz nowy**, wpisz *myBackendPool*, a następnie wybierz pozycję **Utwórz**.
1. Po zakończeniu wybierz **pozycję Przejrzyj + utwórz**. 
1. Po przejściu sprawdzania poprawności wybierz pozycję **Utwórz,** aby wdrożyć zestaw skalowania.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy grupa zasobów, zestaw skalowania i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów dla zestawu skalowania, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono podstawowy zestaw skalowania w witrynie Azure Portal. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego sposobu tworzenia zestawów skalowania maszyn wirtualnych platformy Azure i zarządzania nimi.

> [!div class="nextstepaction"]
> [Tworzenie zestawów skalowania maszyn wirtualnych platformy Azure i zarządzanie nimi](tutorial-create-and-manage-powershell.md)
