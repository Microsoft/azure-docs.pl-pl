---
title: 'Szybki Start: Tworzenie hosta bastionu na podstawie maszyny wirtualnej i nawiązywanie połączenia za pośrednictwem prywatnego adresu IP'
titleSuffix: Azure Bastion
description: W tym artykule dowiesz się, jak utworzyć hosta usługi Azure bastionu z poziomu maszyny wirtualnej i bezpiecznie połączyć się przy użyciu prywatnego adresu IP.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 0ff85e6ceb6867db417d8d8202ff3281478e5687
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996977"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Szybki Start: Nawiązywanie połączenia z maszyną wirtualną przy użyciu prywatnego adresu IP i usługi Azure bastionu

Ten artykuł Szybki Start przedstawia sposób nawiązywania połączenia z maszyną wirtualną przy użyciu prywatnego adresu IP za pośrednictwem przeglądarki za pomocą usługi Azure bastionu i Azure Portal. Kroki opisane w tym artykule ułatwiają wdrożenie bastionu w sieci wirtualnej z poziomu maszyny wirtualnej, a następnie nawiązanie połączenia z MASZYNą wirtualną. Korzystanie z zalet tworzenia hosta bastionu dla sieci wirtualnej bezpośrednio z maszyny wirtualnej polega na tym, że wiele ustawień jest wstępnie wypełniona.

Po aprowizacji usługi środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [co to jest Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Wymagania wstępne

* Sieć wirtualna.
* Maszyna wirtualna z systemem Windows w sieci wirtualnej.
* Następujące wymagane role:
  * Rola czytelnika na maszynie wirtualnej.
  * Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej.

* Porty: Aby nawiązać połączenie z maszyną wirtualną, na maszynie wirtualnej muszą być otwarte następujące porty:
  * Porty przychodzące: RDP (3389)

### <a name="example-values"></a>Przykładowe wartości

|**Nazwa** | **Wartość** |
| --- | --- |
| Nazwa |  Sieci testvnet1 — bastionu |
| Sieć wirtualna |  Sieci testvnet1 (na podstawie maszyny wirtualnej) |
| + Nazwa podsieci | AzureBastionSubnet |
| Adresy AzureBastionSubnet |  10.1.254.0/27 |
| Publiczny adres IP |  Tworzenie nowego elementu |
| Nazwa publicznego adresu IP | VNet1BastionPIP  |
| Jednostka SKU publicznego adresu IP |  Standardowa  |
| Przypisanie  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Tworzenie hosta bastionu

Po utworzeniu hosta bastionu w Azure Portal przy użyciu istniejącej maszyny wirtualnej, różne ustawienia będą automatycznie odpowiadały maszynom wirtualnym i/lub sieci wirtualnej.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com). Przejdź do maszyny wirtualnej, a następnie wybierz pozycję **Połącz**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="ustawienia maszyny wirtualnej" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Z listy rozwijanej wybierz pozycję **bastionu**.
1. Na **TestVM | Połącz się ze stroną**, wybierz pozycję **Użyj bastionu**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="ustawienia maszyny wirtualnej" border="false":::

1. Na stronie **bastionu** wypełnij następujące pola ustawień:

   * **Nazwa**: Nazwij hosta bastionu.
   * **Podsieć**: podsieć w sieci wirtualnej, do której zostanie wdrożony zasób bastionu. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Nazwa pozwala platformie Azure wiedzieć, która podsieć, w której ma zostać wdrożony zasób bastionu. Jest to inna niż podsieć bramy. Użyj podsieci o wartości co najmniej/27 lub większej (/27,/26,/25 itd.).
   
      * Wybierz pozycję **Zarządzaj konfiguracją podsieci**.
      * Wybierz **AzureBastionSubnet**.
      * W razie potrzeby Dostosuj zakres adresów w notacji CIDR. Na przykład 10.1.254.0/27.
      * Nie dostosowuj żadnych innych ustawień. Wybierz **przycisk OK** , aby zaakceptować i zapisać zmiany w podsieci, lub wybierz pozycję **x** w górnej części strony, jeśli nie chcesz wprowadzać żadnych zmian.
1. Kliknij przycisk Wstecz w przeglądarce, aby przejść z powrotem do strony **bastionu** i kontynuować Określanie wartości.
   * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
   * **Publiczny adres IP**: jest to Public IP zasobu bastionu, w którym będzie dostępny protokół RDP/SSH (za pośrednictwem portu 443). Utwórz nowy publiczny adres IP.
1. Wybierz pozycję **Utwórz** , aby utworzyć hosta bastionu. Platforma Azure weryfikuje ustawienia, a następnie tworzy hosta. Tworzenie i wdrażanie hosta i jego zasobów trwa około 5 minut.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="ustawienia maszyny wirtualnej":::

## <a name="connect"></a><a name="connect"></a>Połącz

Po wdrożeniu bastionu w sieci wirtualnej, ekran zmieni się na stronę Połącz.

1. Wpisz nazwę użytkownika i hasło dla swojej maszyny wirtualnej. Następnie wybierz pozycję **Połącz**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="ustawienia maszyny wirtualnej":::
1. Połączenie RDP z tą maszyną wirtualną za pośrednictwem bastionu zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="ustawienia maszyny wirtualnej":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz korzystać z sieci wirtualnej i maszyn wirtualnych, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź nazwę grupy zasobów w polu **wyszukiwania** w górnej części portalu i wybierz ją z wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wprowadź grupę zasobów, aby **wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Host bastionu dla sieci wirtualnej, a następnie jest on bezpiecznie połączony z maszyną wirtualną za pośrednictwem hosta bastionu.

* Aby dowiedzieć się więcej o usłudze Azure bastionu, zobacz artykuł [co to jest Azure bastionu?](bastion-overview.md) i [bastionu często zadawane pytania](bastion-faq.md).
* Aby nawiązać połączenie z zestawem skalowania maszyn wirtualnych, zobacz [nawiązywanie połączenia z zestawem skalowania maszyn wirtualnych przy użyciu usługi Azure bastionu](bastion-connect-vm-scale-set.md).