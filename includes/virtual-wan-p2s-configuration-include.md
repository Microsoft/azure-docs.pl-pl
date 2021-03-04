---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 567c0bb75c30a1f0ccdcde7ec1b0f04f5d6e54c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048265"
---
[!INCLUDE [Portal feature rollout](virtual-wan-portal-feature-rollout.md)]

1. Przejdź do opcji **wszystkie zasoby** i wybierz utworzoną wirtualną sieć WAN, a następnie wybierz pozycję **konfiguracje sieci VPN użytkownika** z menu po lewej stronie.
1. Na stronie **konfiguracje sieci VPN użytkowników** wybierz pozycję **+ Utwórz konfigurację sieci VPN użytkownika** w górnej części strony, aby otworzyć stronę **Konfiguracja sieci VPN tworzenia nowego użytkownika** .

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Zrzut ekranu strony konfiguracje sieci VPN użytkownika.":::

1. Na karcie **podstawowe** w obszarze **szczegóły wystąpienia** wprowadź **nazwę** , którą chcesz przypisać do konfiguracji sieci VPN.
1. W polu **Typ tunelu** z listy rozwijanej wybierz odpowiedni typ tunelu. Opcje typu tunelu to: **IKEV2 VPN**, **OpenVPN** i **OpenVPN i IKEv2**.
1. Wykonaj następujące kroki odpowiadające wybranemu typowi tunelu. Po określeniu wszystkich wartości kliknij przycisk **Przegląd + Utwórz**, a następnie **Utwórz** , aby utworzyć konfigurację.

   **Sieć VPN z protokołem IKEv2**

   * **Wymagania:** Po wybraniu typu tunelu **IKEv2** zostanie wyświetlony komunikat z prośbą o wybranie metody uwierzytelniania. W przypadku protokołu IKEv2 można określić tylko jedną metodę uwierzytelniania. Możesz wybrać pozycję certyfikat platformy Azure, Azure Active Directory lub uwierzytelnianie oparte na usłudze RADIUS.

   * **Parametry niestandardowe protokołu IPSec:** Aby dostosować parametry dla usługi IKE phase 1 i IKE Phase 2, Przełącz przełącznik IPsec na **niestandardowy** i wybierz wartości parametrów. Więcej informacji na temat dostosowywalnych parametrów można znaleźć w artykule dotyczącym [niestandardowego protokołu IPSec](../articles/virtual-wan/point-to-site-ipsec.md) .

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Zrzut ekranu przedstawiający przełącznik IPsec z niestandardowym.":::

   * **Uwierzytelnianie:** Przejdź do mechanizmu uwierzytelniania, który ma być używany przez kliknięcie przycisku **dalej** w dolnej części strony, aby przejść do metody uwierzytelniania, lub kliknij odpowiednią kartę w górnej części strony. Przełącz przełącznik na **wartość tak** , aby wybrać metodę.

     W tym przykładzie wybrano opcję uwierzytelnianie usługi RADIUS. W przypadku uwierzytelniania opartego na usłudze RADIUS można podać dodatkowy adres IP serwera RADIUS i klucz tajny serwera.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Zrzut ekranu usługi IKE.":::

   **OpenVPN**

   * **Wymagania:** Po wybraniu typu tunelu **OpenVPN** zostanie wyświetlony komunikat z prośbą o wybranie mechanizmu uwierzytelniania. Jeśli wybrano opcję OpenVPN jako typ tunelu, można określić wiele metod uwierzytelniania. Można wybrać dowolny podzbiór certyfikatów platformy Azure, Azure Active Directory lub uwierzytelniania opartego na usłudze RADIUS. W przypadku uwierzytelniania opartego na usłudze RADIUS można podać dodatkowy adres IP serwera RADIUS i klucz tajny serwera.

   * **Uwierzytelnianie:** Przejdź do metod uwierzytelniania, które mają być używane przez kliknięcie **przycisku Dalej** w dolnej części strony, aby przejść do metody uwierzytelniania, lub kliknij odpowiednią kartę w górnej części strony.
   Dla każdej metody, którą chcesz wybrać, Przełącz przełącznik na **wartość Yes (tak** ) i wprowadź odpowiednie wartości.

     W tym przykładzie wybrano Azure Active Directory.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Zrzut ekranu przedstawiający stronę OpenVPN.":::
