---
title: 'Tworzenie hosta usługi Azure bastionu: Portal'
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu, który zapewnia łączność protokołu RDP/SSH ze wszystkimi maszynami wirtualnymi w sieci wirtualnej.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270754"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Tworzenie hosta usługi Azure bastionu przy użyciu portalu

W tym artykule opisano sposób tworzenia hosta usługi Azure bastionu przy użyciu Azure Portal. Po udostępnieniu usługi Azure bastionu w sieci wirtualnej, bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie usługi Azure bastionu odbywa się na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Nowy zasób hosta bastionu można utworzyć w portalu, określając wszystkie ustawienia ręcznie lub używając ustawień, które odpowiadają istniejącej maszynie wirtualnej. Aby utworzyć hosta bastionu przy użyciu ustawień maszyny wirtualnej, zobacz artykuł [Szybki Start](quickstart-host-portal.md) . Opcjonalnie możesz użyć [Azure PowerShell](bastion-create-host-powershell.md) , aby utworzyć hosta usługi Azure bastionu.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Bastionu jest dostępny w następujących regionach publicznych platformy Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu usługi Azure bastionu z poziomu Azure Portal.

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** w polu Wyszukaj w *witrynie Marketplace* wpisz **bastionu**, a następnie kliknij przycisk **Enter** , aby przejść do wyników wyszukiwania.

1. Na podstawie wyników kliknij pozycję **bastionu**. Upewnij się, że Wydawca jest *firmą Microsoft* , a kategoria to *Sieć*.

1. Na stronie **bastionu** kliknij przycisk **Utwórz** , aby otworzyć stronę **Tworzenie bastionu** .

1. Na stronie **Tworzenie bastionu** Skonfiguruj nowy zasób bastionu. Określ ustawienia konfiguracji dla zasobu bastionu.

    ![Utwórz bastionu](./media/bastion-create-host-portal/settings.png)

    * **Subskrypcja**: subskrypcja platformy Azure, która ma zostać użyta do utworzenia nowego zasobu bastionu.
    * **Grupa zasobów**: Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób bastionu. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Name**: Nazwa nowego zasobu bastionu
    * **Region**: region publiczny platformy Azure, w którym zostanie utworzony zasób.
    * **Sieć wirtualna**: Sieć wirtualna, w której zostanie utworzony zasób bastionu. Możesz utworzyć nową sieć wirtualną w portalu w trakcie tego procesu lub użyć istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość wolnej przestrzeni adresowej, aby pomieścić wymagania dotyczące podsieci bastionu.
    * **Podsieć**: podsieć w sieci wirtualnej, w której zostanie wdrożony nowy host bastionu. Podsieć będzie przeznaczona dla hosta bastionu i musi być nazwana jako **AzureBastionSubnet**. Ta podsieć musi mieć co najmniej/27 lub większą.
    
       Usługa **AzureBastionSubnet** nie obsługuje [tras zdefiniowanych przez użytkownika](../virtual-network/virtual-networks-udr-overview.md#custom-routes), ale obsługuje [sieciowe grupy zabezpieczeń](bastion-nsg.md).
    * **Publiczny adres IP**: publiczny IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
    * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
    * **Jednostka SKU publicznego adresu IP**: to ustawienie jest wstępnie wypełniane domyślnie **standardem**. Usługa Azure bastionu używa/obsługuje tylko w przypadku standardowej jednostki SKU publicznego adresu IP.
    * **Przypisanie**: to ustawienie jest wstępnie wypełniane domyślnie **statycznie**.

1. Po zakończeniu określania ustawień kliknij przycisk **Przegląd + Utwórz**. Spowoduje to zweryfikowanie wartości. Po zakończeniu walidacji można rozpocząć proces tworzenia.
1. Na stronie **Tworzenie bastionu** kliknij pozycję **Utwórz**.
1. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [często zadawane pytania](bastion-faq.md) dotyczące usługi bastionu, aby uzyskać dodatkowe informacje.

* Aby użyć sieciowych grup zabezpieczeń z podsiecią usługi Azure bastionu, zobacz [Work with sieciowych grup zabezpieczeń](bastion-nsg.md).
