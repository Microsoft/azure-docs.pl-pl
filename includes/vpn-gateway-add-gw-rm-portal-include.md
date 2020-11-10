---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6bc8b6cc18f4a4bc41cabf7f8fefe78a5aaf7827
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427889"
---
1. Z poziomu [Azure Portal](https://portal.azure.com)w obszarze **zasoby wyszukiwania, usługi i dokumenty (G +/)** wpisz **Brama sieci wirtualnej**. Znajdź **bramę sieci wirtualnej** w wynikach wyszukiwania i wybierz ją.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="Pole wyszukiwania" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. Na stronie **Brama sieci wirtualnej** wybierz pozycję **+ Dodaj**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="Strona bramy sieci wirtualnej":::
1. Na karcie **podstawowe** wprowadź wartości dla bramy sieci wirtualnej.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="Pola bramy":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="Dodatkowe pola bramy":::

   * **Subskrypcja** : wybierz subskrypcję, której chcesz użyć z listy rozwijanej.
   * **Grupa zasobów** : to ustawienie jest wypełniane po wybraniu sieci wirtualnej na tej stronie.

   **Szczegóły wystąpienia**

   * **Nazwa** : Nadaj nazwę bramie. Nazwa bramy nie jest taka sama jak nazwa podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
   * **Region** : Wybierz region, w którym chcesz utworzyć ten zasób. Region bramy musi być taki sam jak w przypadku sieci wirtualnej.
   * **Typ bramy** : Wybierz pozycję **Sieć VPN**. Bramy sieci VPN używają typu bramy sieci wirtualnej **Sieć VPN**.
   * **Typ sieci VPN** : Wybierz typ sieci VPN określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania typu sieci VPN opartego na trasach.
   * **Jednostka SKU** : Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).
   * **Generacja** : Aby uzyskać informacje o generowaniu VPN Gateway, zobacz [jednostki SKU bramy](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Sieć wirtualna** : wybierz z listy rozwijanej sieć wirtualną, do której chcesz dodać bramę.
   * **Zakres adresów podsieci bramy** : to pole pojawia się tylko wtedy, gdy sieć wirtualna nie ma podsieci bramy. Jeśli to możliwe, należy wprowadzić zakres/27 lub większy (/26,/25 itp.). Nie zalecamy tworzenia zakresu, który jest mniejszy niż/28. Jeśli masz już podsieć bramy, możesz wyświetlić szczegóły GatewaySubnet, przechodząc do sieci wirtualnej. Kliknij pozycję **podsieci** , aby wyświetlić zakres. Jeśli chcesz zmienić zakres, możesz usunąć i utworzyć ponownie GatewaySubnet.

   **Publiczny adres IP**

   To ustawienie określa obiekt publicznego adresu IP, który jest skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

     * **Publiczny adres IP** : pozostaw opcję **Utwórz nowe** .
     * **Nazwa publicznego adresu IP** : w polu tekstowym wpisz nazwę dla wystąpienia publicznego adresu IP.
     * **Przypisanie** : Brama sieci VPN obsługuje tylko dynamiczne.
     * **Włącz tryb aktywny-aktywny** : wybierz opcję **Włącz tryb aktywny-aktywny** tylko w przypadku tworzenia konfiguracji bramy Active-Active. W przeciwnym razie pozostaw to ustawienie **wyłączone**.
     * Pozostaw opcję **Skonfiguruj protokół BGP** jako **wyłączony** , chyba że konfiguracja wymaga tego ustawienia. Jeśli to ustawienie jest wymagane, domyślny numer ASN to 65515, ale można go zmienić.
1. Wybierz pozycję **Przegląd + Utwórz** , aby uruchomić walidację.
1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby wdrożyć bramę sieci VPN.

Aby w pełni utworzyć i wdrożyć bramę, może upłynąć do 45 minut. Stan wdrożenia można sprawdzić na stronie Przegląd dla bramy. Po utworzeniu bramy można znaleźć adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone.
