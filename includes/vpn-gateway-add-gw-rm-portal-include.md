---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78331304"
---
1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**. 

   ![Tworzenie zasobu w witrynie Azure Portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. W polu **Wyszukaj w witrynie Marketplace** wpisz "Virtual Network Gateway". Znajdź **bramę sieci wirtualnej** w zwracanym wyszukiwaniu i wybierz wpis. Na stronie **Brama sieci wirtualnej** wybierz pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.
3. Na karcie **podstawowe** wprowadź wartości dla bramy sieci wirtualnej.

   ![Utwórz pola strony bramy sieci wirtualnej](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Utwórz pola strony bramy sieci wirtualnej")

   ![Utwórz pola strony bramy sieci wirtualnej](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Utwórz pola strony bramy sieci wirtualnej")

   **Szczegóły projektu**

   - **Subskrypcja**: wybierz subskrypcję, której chcesz użyć z listy rozwijanej.
   - **Grupa zasobów**: to ustawienie jest wypełniane po wybraniu sieci wirtualnej na tej stronie.

   **Szczegóły wystąpienia**

   - **Nazwa**: Nadaj nazwę bramie. Nazwa bramy nie jest taka sama jak nazwa podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
   - **Region**: Wybierz region, w którym chcesz utworzyć ten zasób. Region bramy musi być taki sam jak w przypadku sieci wirtualnej.
   - **Typ bramy**: Wybierz pozycję **Sieć VPN**. Bramy sieci VPN używają typu bramy sieci wirtualnej **Sieć VPN**.
   - **Typ sieci VPN**: Wybierz typ sieci VPN określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania typu sieci VPN opartego na trasach.
   - **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).
   - **Generacja**: Aby uzyskać informacje o generowaniu VPN Gateway, zobacz [jednostki SKU bramy](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Sieć wirtualna**: wybierz z listy rozwijanej sieć wirtualną, do której chcesz dodać bramę.
   - **Zakres adresów podsieci bramy**: to pole pojawia się tylko wtedy, gdy sieć wirtualna nie ma podsieci bramy. Jeśli to możliwe, należy wprowadzić zakres/27 lub większy (/26,/25 itp.). Nie zalecamy tworzenia zakresu, który jest mniejszy niż/28. Jeśli masz już podsieć bramy, możesz wyświetlić szczegóły GatewaySubnet, przechodząc do sieci wirtualnej. Kliknij pozycję **podsieci** , aby wyświetlić zakres. Jeśli chcesz zmienić zakres, możesz usunąć i utworzyć ponownie GatewaySubnet.

   **Publiczny adres IP**: to ustawienie określa obiekt publicznego adresu IP, który zostaje skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

     - **Publiczny adres IP**: pozostaw opcję **Utwórz nowe** .
     - **Nazwa publicznego adresu IP**: w polu tekstowym wpisz nazwę dla wystąpienia publicznego adresu IP.
     - **Przypisanie**: Brama sieci VPN obsługuje tylko dynamiczne.

   **Tryb aktywny-aktywny**: wybierz opcję **Włącz tryb aktywny-aktywny** tylko w przypadku tworzenia konfiguracji bramy Active-Active. W przeciwnym razie pozostaw to ustawienie niewybrane.

   Pozostaw opcję **Skonfiguruj numer ASN protokołu BGP** niewybraną, chyba że Twoja konfiguracja wymaga tego ustawienia. Jeśli to ustawienie jest wymagane, domyślny numer ASN to 65515, ale można go zmienić.
4. Wybierz pozycję **Przegląd + Utwórz** , aby uruchomić walidację. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby wdrożyć bramę sieci VPN. Aby w pełni utworzyć i wdrożyć bramę, może upłynąć do 45 minut. Stan wdrożenia można sprawdzić na stronie Przegląd dla bramy.

Po utworzeniu bramy można znaleźć adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone.
