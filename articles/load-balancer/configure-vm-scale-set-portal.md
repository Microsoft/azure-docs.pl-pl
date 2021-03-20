---
title: Skonfiguruj zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer-Azure Portal
description: Dowiedz się, jak skonfigurować zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91439516"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Skonfiguruj zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu Azure Portal

W tym artykule dowiesz się, jak skonfigurować zestaw skalowania maszyn wirtualnych przy użyciu istniejącego Azure Load Balancer. 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Istniejący moduł równoważenia obciążenia standardowej jednostki SKU w subskrypcji, w której zostanie wdrożony zestaw skalowania maszyn wirtualnych.
- Virtual Network platformy Azure dla zestawu skalowania maszyn wirtualnych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Wdróż zestaw skalowania maszyn wirtualnych z istniejącym modułem równoważenia obciążenia

W tej sekcji utworzysz zestaw skalowania maszyn wirtualnych w Azure Portal z istniejącym modułem równoważenia obciążenia platformy Azure.

> [!NOTE]
> W poniższych krokach przyjęto założenie, że sieć wirtualna o nazwie **myVNet** i moduł równoważenia obciążenia platformy Azure o nazwie **myLoadBalancer** został wcześniej wdrożony.

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób**  >  **Oblicz**  >  **zestaw skalowania maszyn wirtualnych** lub Wyszukaj **zestaw skalowania maszyn wirtualnych** w obszarze wyszukiwania w portalu Marketplace.

2. Wybierz przycisk **Utwórz**.

3. W obszarze **Tworzenie zestawu skalowania maszyn wirtualnych** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie                        | Wartość                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Szczegóły projektu**            |                                                                                                       |
    | Subskrypcja                   | Wybierz subskrypcję platformy Azure                                                                        |
    | Grupa zasobów                 | Wybierz pozycję Utwórz nowy, wprowadź nazwę **zasobu**, a następnie wybierz pozycję OK lub wybierz istniejącą grupę zasobów. |
    | **Szczegóły zestawu skalowania**          |                                                                                                       |
    | Nazwa zestawu skalowania maszyn wirtualnych | Wprowadź **myVMSS**                                                                                      |
    | Region (Region)                         | Wybierz **Wschodnie stany USA 2**                                                                                    |
    | Strefa dostępności              | Nie zaznaczaj **niczego**                                                                                       |
    | **Szczegóły wystąpienia**           |                                                                                                       |
    | Image (Obraz)                          | Wybierz **serwer Ubuntu 18,04 LTS**                                                                    |
    | Wystąpienie usługi Azure Spot            | Wybierz pozycję **nie**                                                                                         |
    | Rozmiar                           | Pozostaw domyślnie                                                                                      |
    | **Konto administratora**      |                                                                                                       |
    | Typ uwierzytelniania            | Wybieranie **hasła**                                                                                   |
    | Nazwa użytkownika                       | Wprowadź nazwę użytkownika administratora        |
    | Hasło                       | Wprowadź hasło administratora    |
    | Potwierdź hasło               | Wprowadź ponownie hasło administratora |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Zrzut ekranu przedstawia kartę tworzenie podstawy zestawu skalowania maszyn wirtualnych." border="true":::

4. Wybierz kartę **Sieć**.

5. Wprowadź lub wybierz te informacje na karcie **Sieć** :

     Ustawienie                           | Wartość                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Konfiguracja sieci wirtualnej** |                                                          |
    | Sieć wirtualna                   | Wybierz **myVNet** lub istniejącą sieć wirtualną.      |
    | **Równoważenie obciążenia**                |                                                          |
    | Korzystanie z modułu równoważenia obciążenia               | Wybierz pozycję **Tak**                                           |
    | **Ustawienia równoważenia obciążenia**       |                                                          |
    | Opcje równoważenia obciążenia            | Wybieranie **modułu równoważenia obciążenia platformy Azure**                           |
    | Wybierz moduł równoważenia obciążenia            | Wybierz **myLoadBalancer** lub istniejący moduł równoważenia obciążenia |
    | Wybierz pulę zaplecza             | Wybierz **myBackendPool** lub istniejącą pulę zaplecza.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Zrzut ekranu przedstawia kartę tworzenie sieci zestawu skalowania maszyn wirtualnych." border="true":::

6. Wybierz kartę **Zarządzanie** .

7. Na karcie **Zarządzanie** ustaw opcję **Diagnostyka rozruchu** na **off**.

8. Wybierz przycisk niebieski **Przegląd + Utwórz** .

9. Przejrzyj ustawienia i wybierz przycisk **Utwórz** .

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wdrożenie zestawu skalowania maszyn wirtualnych z istniejącym Azure Load Balancer.  Aby dowiedzieć się więcej na temat usługi Virtual Machine Scale Sets i modułu równoważenia obciążenia, zobacz:

- [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)
- [Co to są zestawy skalowania maszyn wirtualnych?](../virtual-machine-scale-sets/overview.md)
