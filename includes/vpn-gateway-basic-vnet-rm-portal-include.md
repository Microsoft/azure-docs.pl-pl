---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "92328446"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W obszarze **Wyszukaj zasoby, usługi i dokumenty (G +/)** wpisz *sieć wirtualną* .

   ![Lokalizowanie strony zasobów Virtual Network](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Strona lokalizowanie zasobu sieci wirtualnej")
1. Wybierz **Virtual Network** z wyników z **witryny Marketplace** .

   ![Wybierz sieć wirtualną](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Strona lokalizowanie zasobu sieci wirtualnej")
1. Na stronie **Virtual Network** wybierz pozycję **Utwórz** .

   ![Strona sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Wybieranie pozycji Utwórz")
1. Po wybraniu opcji **Utwórz** zostanie otwarta strona **Tworzenie sieci wirtualnej** .
1. Na karcie **podstawy** Skonfiguruj **szczegóły projektu** i **szczegóły wystąpienia** ustawienia sieci wirtualnej.

   ![Karta podstawy](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Karta Podstawowe") Po wypełnieniu pól zostanie wyświetlony zielony znacznik wyboru, gdy znaki wprowadzone w polu zostaną zweryfikowane. Niektóre wartości są wypełnione automatycznie — możesz je zastąpić własnymi wartościami:

   - **Subskrypcja** : Sprawdź, czy na liście znajduje się poprawna subskrypcja. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów** : wybierz istniejącą grupę zasobów lub kliknij pozycję **Utwórz nową** , aby utworzyć nową. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Nazwa** : Wprowadź nazwę sieci wirtualnej.
   - **Region** : Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.

1. Na karcie **adresy IP** Skonfiguruj wartości. Wartości podane w poniższych przykładach są przeznaczone do celów demonstracyjnych. Dostosuj te wartości zgodnie z ustawieniami, które są wymagane.

   ![Karta adresy IP](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Karta adresy IP")  
   - **Przestrzeń adresów IPv4** : domyślnie przestrzeń adresowa jest tworzona automatycznie. Możesz kliknąć przestrzeń adresową, aby dostosować ją do własnych wartości. Możesz również dodać dodatkowe przestrzenie adresowe.
   - **Podsieć** : w przypadku korzystania z domyślnej przestrzeni adresowej automatycznie zostanie utworzona podsieć. W przypadku zmiany przestrzeni adresowej należy dodać podsieć. Wybierz pozycję **+ Dodaj podsieć** , aby otworzyć okno **Dodawanie podsieci** . Skonfiguruj poniższe ustawienia, a następnie wybierz pozycję **Dodaj** , aby dodać wartości:
      - **Nazwa podsieci** : w tym przykładzie nazywamy podsiecią "frontonu".
      - **Zakres adresów podsieci** : zakres adresów dla tej podsieci.

1. Na karcie **zabezpieczenia** w tej chwili pozostaw wartości domyślne:

   - **Ochrona DDoS** : podstawowa
   - **Zapora** : wyłączone
1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić poprawność ustawień sieci wirtualnej.
1. Po sprawdzeniu poprawności ustawień wybierz pozycję **Utwórz** .
