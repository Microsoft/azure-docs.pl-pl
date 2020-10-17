---
title: 'Szybki Start: Konfigurowanie usługi Azure bastionu i nawiązywanie połączenia z maszyną wirtualną za pomocą prywatnego adresu IP i przeglądarki'
titleSuffix: Azure Bastion
description: W tym artykule szybkiego startu dowiesz się, jak utworzyć hosta usługi Azure bastionu z maszyny wirtualnej i bezpiecznie połączyć się z MASZYNą wirtualną za pośrednictwem przeglądarki za pomocą prywatnego adresu IP.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150430"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Szybki Start: łączenie z maszyną wirtualną bezpiecznie za pośrednictwem przeglądarki za pomocą prywatnego adresu IP

Możesz nawiązać połączenie z maszyną wirtualną za pośrednictwem przeglądarki, korzystając z Azure Portal i usługi Azure bastionu. Ten artykuł szybkiego startu pokazuje, jak skonfigurować usługę Azure bastionu na podstawie ustawień maszyny wirtualnej, a następnie połączyć się z maszyną wirtualną za pośrednictwem portalu. Maszyna wirtualna nie wymaga publicznego adresu IP, oprogramowania klienckiego, agenta ani specjalnej konfiguracji. Po aprowizacji usługi środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [co to jest Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, [Utwórz go bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Aby móc nawiązać połączenie z maszyną wirtualną za pośrednictwem przeglądarki za pomocą bastionu, musisz mieć możliwość zalogowania się do Azure Portal.

* Maszyna wirtualna z systemem Windows w sieci wirtualnej. Jeśli nie masz maszyny wirtualnej, utwórz ją za pomocą [przewodnika Szybki Start: Tworzenie maszyny wirtualnej](../virtual-machines/windows/quick-create-portal.md).

  * Jeśli potrzebujesz przykładowych wartości, zobacz podane [przykładowe wartości](#values).
  * Jeśli masz już sieć wirtualną, upewnij się, że wybrano ją na karcie Sieć podczas tworzenia maszyny wirtualnej.
  * Jeśli nie masz jeszcze sieci wirtualnej, możesz ją utworzyć podczas tworzenia maszyny wirtualnej.
  * Nie trzeba mieć publicznego adresu IP dla tej maszyny wirtualnej, aby można było nawiązać połączenie za pośrednictwem usługi Azure bastionu.

* Wymagane role maszyny wirtualnej:
  * Rola czytelnika na maszynie wirtualnej.
  * Rola czytelnika na karcie sieciowej z prywatnym adresem IP maszyny wirtualnej.
  
* Wymagane porty maszyn wirtualnych:
  * Porty przychodzące: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Przykładowe wartości

Podczas tworzenia tej konfiguracji można użyć następujących przykładowych wartości lub można podstawić własne.

**Podstawowe wartości sieci wirtualnej i maszyny wirtualnej:**

|**Nazwa** | **Wartość** |
| --- | --- |
| Maszyna wirtualna| TestVM |
| Grupa zasobów | TestRG |
| Region | East US |
| Sieć wirtualna | TestVNet1 |
| Przestrzeń adresowa | 10.0.0.0/16 |
| Podsieci | Fronton: 10.0.0.0/24 |

**Wartości bastionu platformy Azure:**

|**Nazwa** | **Wartość** |
| --- | --- |
| Nazwa | Sieci testvnet1 — bastionu |
| + Nazwa podsieci | AzureBastionSubnet |
| Adresy AzureBastionSubnet | Podsieć w przestrzeni adresowej sieci wirtualnej z maską podsieci/27. Na przykład 10.0.1.0/27.  |
| Publiczny adres IP |  Tworzenie nowego elementu |
| Nazwa publicznego adresu IP | VNet1BastionPIP  |
| Jednostka SKU publicznego adresu IP |  Standardowa  |
| Przypisanie  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Tworzenie hosta bastionu

Istnieje kilka różnych sposobów konfigurowania hosta bastionu. W poniższych krokach utworzysz hosta bastionu w Azure Portal bezpośrednio z poziomu maszyny wirtualnej. W przypadku tworzenia hosta z poziomu maszyny wirtualnej różne ustawienia będą automatycznie wypełniane odpowiadającą maszynom wirtualnym i/lub sieci wirtualnej.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do maszyny wirtualnej, z którą chcesz nawiązać połączenie, a następnie wybierz pozycję **Połącz**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="ustawienia maszyny wirtualnej" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. Z listy rozwijanej wybierz pozycję **bastionu**.
1. Na **TestVM | Połącz się ze stroną**, wybierz pozycję **Użyj bastionu**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="ustawienia maszyny wirtualnej" border="false":::

1. Na stronie **bastionu** wypełnij następujące pola ustawień:

   * **Nazwa**: Nazwij hosta bastionu.
   * **Podsieć**: jest to przestrzeń adresowa sieci wirtualnej, w której zostanie wdrożony zasób bastionu. Należy utworzyć podsieć o nazwie **AzureBastionSubnet**. Użyj podsieci o wartości co najmniej/27 lub większej (/27,/26,/25 itd.).
   * Wybierz pozycję **Zarządzaj konfiguracją podsieci**.
1. Na stronie **podsieci** wybierz pozycję **+ podsieć**.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="ustawienia maszyny wirtualnej":::
    
1. Na stronie **Dodawanie podsieci** wpisz **nazwę** **AzureBastionSubnet**.
   * W polu Zakres adresów podsieci wybierz adres podsieci znajdujący się w przestrzeni adresowej sieci wirtualnej.
   * Nie dostosowuj żadnych innych ustawień. Wybierz **przycisk OK** , aby zaakceptować i zapisać zmiany w podsieci.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="ustawienia maszyny wirtualnej":::
1. Kliknij przycisk Wstecz w przeglądarce, aby przejść z powrotem do strony **bastionu** , i Kontynuuj Określanie wartości.
   * **Publiczny adres IP**: pozostaw to pole **Utwórz nowy**.
   * **Nazwa publicznego adresu IP**: nazwa zasobu publicznego adresu IP.
   * **Przypisanie**: wartość domyślna to static. Nie można użyć dynamicznego przypisywania dla usługi Azure bastionu.
   * **Grupa zasobów**: ta sama Grupa zasobów co maszyna wirtualna.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="ustawienia maszyny wirtualnej":::
1. Wybierz pozycję **Utwórz** , aby utworzyć hosta bastionu. Platforma Azure weryfikuje ustawienia, a następnie tworzy hosta. Tworzenie i wdrażanie hosta i jego zasobów trwa około 5 minut.

## <a name="connect"></a><a name="connect"></a>Połącz

Po wdrożeniu bastionu w sieci wirtualnej, ekran zmieni się na stronę Połącz.

1. Wpisz nazwę użytkownika i hasło dla swojej maszyny wirtualnej. Następnie wybierz pozycję **Połącz**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="ustawienia maszyny wirtualnej":::
1. Połączenie RDP z tą maszyną wirtualną zostanie otwarte bezpośrednio w Azure Portal (za pośrednictwem HTML5) przy użyciu portu 443 i usługi bastionu.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="ustawienia maszyny wirtualnej":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz korzystać z sieci wirtualnej i maszyn wirtualnych, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź nazwę grupy zasobów w polu **wyszukiwania** w górnej części portalu i wybierz ją z wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wprowadź grupę zasobów, aby **wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Host bastionu dla sieci wirtualnej, a następnie jest on bezpiecznie połączony z maszyną wirtualną za pośrednictwem usługi bastionu. Aby połączyć się z zestawem skalowania maszyn wirtualnych, możesz przejść do następnego kroku.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z zestawem skalowania maszyn wirtualnych przy użyciu usługi Azure bastionu](bastion-connect-vm-scale-set.md)
