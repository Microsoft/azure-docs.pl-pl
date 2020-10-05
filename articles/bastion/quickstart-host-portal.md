---
title: 'Szybki Start: Nawiązywanie połączenia z maszyną wirtualną przy użyciu prywatnego adresu IP: Azure bastionu'
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu z poziomu maszyny wirtualnej i bezpiecznie połączyć się przy użyciu prywatnego adresu IP.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90985585"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Szybki Start: Nawiązywanie połączenia z maszyną wirtualną przy użyciu prywatnego adresu IP i usługi Azure bastionu

Ten artykuł Szybki Start przedstawia sposób nawiązywania połączenia z maszyną wirtualną przy użyciu prywatnego adresu IP. Po nawiązaniu połączenia za pośrednictwem bastionu maszyny wirtualne nie potrzebują publicznego adresu IP. Kroki opisane w tym artykule ułatwiają wdrożenie bastionu w sieci wirtualnej za pośrednictwem maszyny wirtualnej w portalu. Po aprowizacji usługi środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej.

## <a name="prerequisites"></a><a name="prereq"></a>Wymagania wstępne

* Sieć wirtualna platformy Azure.
* Maszyna wirtualna platformy Azure znajdująca się w sieci wirtualnej z otwartym portem 3389.

### <a name="example-values"></a>Przykładowe wartości

|**Nazwa** | **Wartość** |
| --- | --- |
| Nazwa |  VNet1Bastion |
| Region | eastus |
| Sieć wirtualna |  Sieć wirtualna 1 |
| + Nazwa podsieci | AzureBastionSubnet |
| Adresy AzureBastionSubnet |  10.1.254.0/27 |
| Publiczny adres IP |  Tworzenie nowego elementu |
| Nazwa publicznego adresu IP | VNet1BastionPIP  |
| Jednostka SKU publicznego adresu IP |  Standardowa  |
| Przypisanie  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Tworzenie hosta bastionu

Po utworzeniu hosta usługi bastionu w portalu przy użyciu istniejącej maszyny wirtualnej różne ustawienia będą automatycznie odpowiadały maszynom wirtualnym i/lub sieci wirtualnej.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, a następnie kliknij przycisk **Połącz**.

   ![ustawienia maszyny wirtualnej](./media/quickstart-host-portal/vm-settings.png)
1. Z listy rozwijanej wybierz pozycję **bastionu**.
1. Na stronie Połącz wybierz pozycję **Użyj bastionu**.

   ![Wybierz bastionu](./media/quickstart-host-portal/select-bastion.png)

1. Na stronie bastionu wypełnij następujące pola ustawień:

   * **Name**: Nazwij hosta bastionu
   * **Podsieć**: podsieć w sieci wirtualnej, do której zostanie wdrożony zasób bastionu. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Nazwa pozwala platformie Azure wiedzieć, która podsieć, w której ma zostać wdrożony zasób bastionu. Jest to inna niż podsieć bramy. Użyj podsieci o wartości co najmniej/27 lub większej (/27,/26,/25 itd.).
   
      * Wybierz pozycję **Zarządzaj konfiguracją podsieci**, a następnie wybierz pozycję **+ podsieć**.
      * Na stronie Dodawanie podsieci wpisz **AzureBastionSubnet**.
      * Określ zakres adresów w notacji CIDR. Na przykład 10.1.254.0/27.
      * Wybierz **przycisk OK** , aby utworzyć podsieć. W górnej części strony Wróć do bastionu, aby ukończyć pozostałe ustawienia.

         ![Przejdź do ustawień bastionu](./media/quickstart-host-portal/navigate-bastion.png)
   * **Publiczny adres IP**: jest to Public IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP lub Użyj istniejącego. Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.
   * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
1. Na ekranie walidacji kliknij pozycję **Utwórz**. Poczekaj około 5 minut na utworzenie i wdrożenie zasobu bastionu.

   ![Utwórz hosta bastionu](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Łączone

Po wdrożeniu bastionu w sieci wirtualnej, ekran zmieni się na stronę Połącz.

1. Wpisz nazwę użytkownika i hasło dla swojej maszyny wirtualnej. Następnie wybierz pozycję **Połącz**.

   ![Zrzut ekranu przedstawia okno dialogowe nawiązywanie połączenia przy użyciu usługi Azure bastionu, w którym jest wyświetlany komunikat o nazwie użytkownika i hasła.](./media/quickstart-host-portal/connect.png)
1. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

   ![Połączenie RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz korzystać z sieci wirtualnej i maszyn wirtualnych, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź *TestRG1* w polu **wyszukiwania** w górnej części portalu i wybierz pozycję **TestRG1** z wyników wyszukiwania.

2. Wybierz pozycję **Usuń grupę zasobów**.

3. Wprowadź *TestRG1* w **polu wpisz nazwę grupy zasobów** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Host bastionu dla sieci wirtualnej, a następnie jest on bezpiecznie połączony z maszyną wirtualną za pośrednictwem hosta bastionu.

* Aby dowiedzieć się więcej o usłudze Azure bastionu, zapoznaj się z [omówieniem bastionu](bastion-overview.md) i [zapytaniami bastionu](bastion-faq.md).
* Aby użyć sieciowych grup zabezpieczeń z podsiecią usługi Azure bastionu, zobacz [Work with sieciowych grup zabezpieczeń](bastion-nsg.md).
* Instrukcje zawierające wyjaśnienia dotyczące ustawień hosta usługi Azure bastionu można znaleźć w [samouczku](bastion-create-host-portal.md).
* Aby nawiązać połączenie z zestawem skalowania maszyn wirtualnych, zobacz [nawiązywanie połączenia z zestawem skalowania maszyn wirtualnych przy użyciu usługi Azure bastionu](bastion-connect-vm-scale-set.md).