---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362940"
---
1. Na stronie portalu wirtualnej sieci WAN w sekcji **połączenie** wybierz pozycję **Lokacje sieci VPN** , aby otworzyć stronę witryny sieci VPN.
1. Na stronie **Lokacje sieci VPN** kliknij pozycję **+Utwórz lokację**.
1. Na stronie **Tworzenie witryny sieci VPN** na karcie **podstawowe** wykonaj następujące pola:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Karta Podstawowe" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Region** — wcześniej nazywany lokalizacją. Jest to lokalizacja, w której chcesz utworzyć zasób lokacji.
    * **Nazwa** — nazwa, przez którą chcesz odwołać się do lokacji lokalnej.
    * **Dostawca urządzenia** — nazwa dostawcy urządzenia sieci VPN (na przykład: Citrix, Cisco, Barracuda). Dodanie dostawcy urządzenia może ułatwić zespołowi platformy Azure lepsze zrozumienie Twojego środowiska w celu dodania dodatkowych możliwości optymalizacji w przyszłości lub ułatwienia rozwiązywania problemów.
    * **Prywatna przestrzeń adresowa** — przestrzeń adresów IP znajdująca się w lokacji lokalnej. Ruch do tej przestrzeni adresowej jest kierowany do lokacji lokalnej. Jest to wymagane, gdy dla lokacji nie jest włączony protokół BGP.
    
      >[!NOTE]
      >Jeśli edytujesz przestrzeń adresową po utworzeniu lokacji (na przykład Dodaj dodatkową przestrzeń adresową), po ponownym utworzeniu składników może upłynąć 8-10 minut, aby zaktualizować obowiązujące trasy.
      >
1. Wybierz **linki** , aby dodać informacje o fizycznych linkach w gałęzi. Jeśli masz urządzenie z wirtualnym partnerem sieci WAN CPE, skontaktuj się z nimi, aby sprawdzić, czy te informacje są wymieniane z platformą Azure w ramach przekazywania informacji o gałęziach skonfigurowanych na podstawie ich systemów.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Karta łącza" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Nazwa łącza** — nazwa, która ma zostać połączona z łączem fizycznym w witrynie sieci VPN. Przykład: Mylink1.
   * **Szybkość łącza** — szybkość urządzenia sieci VPN w lokalizacji gałęzi. Przykład: 50, co oznacza, że 50 MB/s to szybkość urządzenia sieci VPN w lokacji oddziału.
   * **Nazwa dostawcy linku** — nazwa fizycznego linku w witrynie sieci VPN. Przykład: ATT, Verizon.
   * **Połącz adres IP/nazwę FQDN** — publiczny adres IP urządzenia lokalnego przy użyciu tego linku. Opcjonalnie możesz podać prywatny adres IP lokalnego urządzenia sieci VPN znajdującego się za ExpressRoute. Można również uwzględnić w pełni kwalifikowaną nazwę domeny. Na przykład *Something.contoso.com*. Nazwa FQDN powinna być rozpoznawalna z bramy sieci VPN. Jest to możliwe, jeśli serwer DNS hostujący tę nazwę FQDN jest dostępny za pośrednictwem Internetu. Adres IP ma pierwszeństwo w przypadku określenia zarówno adresu IP, jak i nazwy FQDN.

     >[!NOTE]
     >
     >* Obsługuje jeden adres IPv4 na nazwę FQDN. Jeśli nazwa FQDN została rozpoznana jako wiele adresów IP, Brama sieci VPN Pobiera pierwszy adres IP4 z listy. Adresy IPv6 nie są w tej chwili obsługiwane.
     >
     >* Brama sieci VPN obsługuje pamięć podręczną DNS, która jest odświeżana co 5 minut. Brama próbuje rozpoznać nazwy FQDN tylko dla odłączonych tuneli. Resetowanie bramy lub zmiana konfiguracji może również wyzwolić rozpoznawanie nazw FQDN.
     >
   * **Link Border Gateway Protocol** — Konfigurowanie protokołu BGP na wirtualnym łączu WAN jest równoznaczne z skonfigurowaniem protokołu BGP w sieci VPN bramy sieci wirtualnej platformy Azure. Adres lokalnego elementu równorzędnego protokołu BGP nie może być taki sam jak publiczny adres IP sieci VPN do urządzenia lub przestrzeni adresowej sieci wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Określ ten adres w odpowiedniej witrynie sieci VPN reprezentującej lokalizację.  Wymagania wstępne dotyczące protokołu BGP zawiera temat [Informacje o protokole BGP z platformą Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Można zawsze edytować połączenie połączenia sieci VPN, aby zaktualizować jego parametry BGP (adres IP komunikacji równorzędnej na linku i jako #).
1. Możesz dodać lub usunąć więcej linków. Obsługiwane są cztery linki na witrynę sieci VPN. Na przykład jeśli masz czterech usługodawców internetowych (usługodawcę internetowego) w lokalizacji gałęzi, możesz utworzyć cztery linki, jeden dla każdego usługodawcy internetowego i podać informacje dla każdego łącza.
1. Po zakończeniu wypełniania pól wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić i utworzyć lokację.
1. Przejdź do żądanego koncentratora wirtualnego i usuń zaznaczenie opcji **skojarzenie piasty** , aby połączyć swoją LOKACJĘ sieci VPN z centrum.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Połącz z tym centrum" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
