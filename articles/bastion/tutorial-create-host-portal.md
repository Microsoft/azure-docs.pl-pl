---
title: 'Samouczek: Tworzenie hosta usługi Azure bastionu: maszyna wirtualna z systemem Windows: Portal'
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu i połączyć się z maszyną wirtualną z systemem Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 3b365e347802824e855797afb8c68e5249bf0adb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579628"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Samouczek: Konfigurowanie bastionu i nawiązywanie połączenia z maszyną wirtualną z systemem Windows za pomocą przeglądarki

W tym samouczku pokazano, jak nawiązać połączenie z maszyną wirtualną za pośrednictwem przeglądarki za pomocą usługi Azure bastionu i Azure Portal. W Azure Portal wdrażasz bastionu w sieci wirtualnej. Po wdrożeniu bastionu Nawiąż połączenie z maszyną wirtualną za pośrednictwem swojego prywatnego adresu IP przy użyciu Azure Portal. Maszyna wirtualna nie potrzebuje publicznego adresu IP ani specjalnego oprogramowania. Po aprowizacji usługi środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [co to jest Azure bastionu?](bastion-overview.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie hosta bastionu dla sieci wirtualnej
> * Connect to a Windows virtual machine (Łączenie z maszyną wirtualną z systemem Windows)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna.
* Maszyna wirtualna z systemem Windows w sieci wirtualnej.
* Następujące wymagane role:
  * Rola czytelnika na maszynie wirtualnej.
  * Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej.
  * Rola czytelnika w zasobie Azure bastionu.

* Porty: Aby nawiązać połączenie z maszyną wirtualną z systemem Windows, należy otworzyć następujące porty na maszynie wirtualnej z systemem Windows:
  * Porty przychodzące: RDP (3389)

 >[!NOTE]
 >Korzystanie z usługi Azure bastionu z strefami Prywatna strefa DNS platformy Azure nie jest w tej chwili obsługiwane. Przed rozpoczęciem upewnij się, że sieć wirtualna, w której planujesz wdrożyć zasób bastionu, nie jest połączona z prywatną strefą DNS.
 >

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja pomaga utworzyć obiekt bastionu w sieci wirtualnej. Jest to wymagane w celu utworzenia bezpiecznego połączenia z maszyną wirtualną w sieci wirtualnej.

1. Na stronie **głównej** wybierz pozycję **+ Utwórz zasób**.
1. Na **nowej** stronie w polu wyszukiwania wpisz **bastionu**, a następnie wybierz **klawisz ENTER** , aby przejść do wyników wyszukiwania. W wyniku **bastionu** upewnij się, że Wydawca jest firmą Microsoft.
1. Wybierz przycisk **Utwórz**.
1. Na stronie **Tworzenie bastionu** Skonfiguruj nowy zasób bastionu.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Tworzenie hosta usługi Bastion" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Subskrypcja**: subskrypcja platformy Azure, która ma zostać użyta do utworzenia nowego zasobu bastionu.
    * **Grupa zasobów**: Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób bastionu. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Name**: Nazwa nowego zasobu bastionu.
    * **Region**: region publiczny platformy Azure, w którym zostanie utworzony zasób.
    * **Sieć wirtualna**: Sieć wirtualna, w której zostanie utworzony zasób bastionu. Możesz utworzyć nową sieć wirtualną w portalu w trakcie tego procesu lub użyć istniejącej sieci wirtualnej. Jeśli używasz istniejącej sieci wirtualnej, upewnij się, że istniejąca sieć wirtualna ma wystarczającą ilość wolnej przestrzeni adresowej, aby pomieścić wymagania dotyczące podsieci bastionu. Jeśli nie widzisz sieci wirtualnej z listy rozwijanej, upewnij się, że wybrano poprawną grupę zasobów.
    * **Podsieć**: po utworzeniu lub wybraniu sieci wirtualnej zostanie wyświetlone pole podsieć. Podsieć w sieci wirtualnej, w której zostanie wdrożony nowy host bastionu. Podsieć będzie przeznaczona dla hosta bastionu. Wybierz pozycję **Zarządzaj konfiguracją podsieci** i Utwórz podsieć usługi Azure bastionu. Wybierz pozycję **+ podsieć** i Utwórz podsieć, korzystając z następujących wskazówek:

         * Podsieć musi mieć nazwę **AzureBastionSubnet**.
         * Podsieć musi mieć co najmniej/27 lub większą.

      Nie musisz wypełniać dodatkowych pól. Wybierz pozycję **OK** , a następnie w górnej części strony wybierz pozycję **Utwórz bastionu** , aby powrócić do strony Konfiguracja bastionu.
    * **Publiczny adres IP**: publiczny IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu. Ten adres IP nie ma niczego do wykonania z dowolnymi maszynami wirtualnymi, z którymi chcesz nawiązać połączenie. Jest to publiczny adres IP dla zasobu hosta bastionu.
    * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP. W tym samouczku można pozostawić wartość domyślną.
    * **Jednostka SKU publicznego adresu IP**: to ustawienie jest wstępnie wypełniane domyślnie **standardem**. Usługa Azure bastionu używa/obsługuje tylko w przypadku standardowej jednostki SKU publicznego adresu IP.
    * **Przypisanie**: to ustawienie jest wstępnie wypełniane domyślnie **statycznie**.

1. Po zakończeniu określania ustawień wybierz pozycję **Przegląd + Utwórz**. Spowoduje to zweryfikowanie wartości. Po zakończeniu walidacji można utworzyć zasób bastionu.
1. Wybierz przycisk **Utwórz**.
1. Zobaczysz komunikat informujący o tym, że wdrożenie jest w toku. Stan będzie wyświetlany na tej stronie podczas tworzenia zasobów. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

## <a name="connect-to-a-vm"></a>Łączenie z maszyną wirtualną

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby, wykonując następujące czynności:

1. Wprowadź nazwę grupy zasobów w polu **wyszukiwania** w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów, wybierz ją.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. Wprowadź nazwę grupy zasobów w **polu wpisz nazwę grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono hosta bastionu i skojarzono go z siecią wirtualną, a następnie jest on podłączony do maszyny wirtualnej z systemem Windows. Możesz zdecydować się na korzystanie z sieciowych grup zabezpieczeń z podsiecią usługi Azure bastionu. Aby to zrobić, zobacz:

> [!div class="nextstepaction"]
> [Praca z sieciowymi grupami zabezpieczeń](bastion-nsg.md)
