---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627719"
---
1. Na stronie portalu wirtualnej sieci WAN w sekcji **połączenie** wybierz pozycję **Lokacje sieci VPN** , aby otworzyć stronę witryny sieci VPN.
2. Na stronie **Lokacje sieci VPN** kliknij pozycję **+Utwórz lokację**.

   ![Zrzut ekranu przedstawia okno lokacja-lokacja z otwartym okienkiem Utwórz witrynę sieci VPN.](./media/virtual-wan-tutorial-site-include/basics.png "Podstawy")
3. Na stronie **Tworzenie witryny sieci VPN** na karcie **podstawowe** wykonaj następujące pola:

    * **Region** — wcześniej nazywany lokalizacją. Jest to lokalizacja, w której chcesz utworzyć zasób lokacji.
    * **Nazwa** — nazwa, przez którą chcesz odwołać się do lokacji lokalnej.
    * **Dostawca urządzenia** — nazwa dostawcy urządzenia sieci VPN (na przykład: Citrix, Cisco, Barracuda). Dodanie dostawcy urządzenia może ułatwić zespołowi platformy Azure lepsze zrozumienie Twojego środowiska w celu dodania dodatkowych możliwości optymalizacji w przyszłości lub ułatwienia rozwiązywania problemów.
    * **Border Gateway Protocol** — ustawienie **Włącz** oznacza, że wszystkie połączenia z lokacji będą włączone przy użyciu protokołu BGP. Ostatecznie skonfigurujemy informacje protokołu BGP dla każdego łącza z witryny sieci VPN w sekcji linki. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z konfiguracją protokołu BGP na sieci VPN bramy sieci wirtualnej platformy Azure. Adres lokalnego elementu równorzędnego protokołu BGP nie może być taki sam jak publiczny adres IP sieci VPN do urządzenia lub przestrzeni adresowej sieci wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Określ ten adres w odpowiedniej witrynie sieci VPN reprezentującej lokalizację. Wymagania wstępne dotyczące protokołu BGP zawiera temat [Informacje o protokole BGP z platformą Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Można zawsze edytować połączenie sieci VPN, aby zaktualizować jego parametry protokołu BGP (Komunikacja równorzędna w ramach łącza i jako #) po włączeniu ustawienia protokołu BGP witryny sieci VPN.
    * **Prywatna przestrzeń adresowa** — przestrzeń adresów IP znajdująca się w lokacji lokalnej. Ruch do tej przestrzeni adresowej jest kierowany do lokacji lokalnej. Jest to wymagane, gdy dla lokacji nie jest włączony protokół BGP.
    
      >[!NOTE]
      >Jeśli edytujesz przestrzeń adresową po utworzeniu lokacji (na przykład Dodaj dodatkową przestrzeń adresową), po ponownym utworzeniu składników może upłynąć 8-10 minut, aby zaktualizować obowiązujące trasy.
      >
    * **Hubs — centrum** , z którym chcesz połączyć się z lokacją. Lokację można podłączyć tylko do centrów, które mają VPN Gateway. Jeśli nie widzisz centrum, najpierw utwórz bramę sieci VPN w tym centrum.
4. Wybierz **linki** , aby dodać informacje o fizycznych linkach w gałęzi. Jeśli masz urządzenie z wirtualnym partnerem sieci WAN CPE, skontaktuj się z nimi, aby sprawdzić, czy te informacje są wymieniane z platformą Azure w ramach przekazywania informacji o gałęziach skonfigurowanych na podstawie ich systemów.

   ![Zrzut ekranu przedstawia okienko Tworzenie witryny sieci VPN z wybraną kartą łącza.](./media/virtual-wan-tutorial-site-include/links.png "Linki")

    * **Nazwa łącza** — nazwa, która ma zostać połączona z łączem fizycznym w witrynie sieci VPN. Przykład: Mylink1.
    * **Nazwa dostawcy** — nazwa fizycznego linku w witrynie sieci VPN. Przykład: ATT, Verizon.
    * **Szybkość** — szybkość urządzenia sieci VPN w lokalizacji gałęzi. Przykład: 50, co oznacza, że 50 MB/s to szybkość urządzenia sieci VPN w lokacji oddziału.
    * **Adres IP/nazwa FQDN** — publiczny adres IP urządzenia lokalnego przy użyciu tego linku. Opcjonalnie możesz podać prywatny adres IP lokalnego urządzenia sieci VPN znajdującego się za ExpressRoute. Można również uwzględnić w pełni kwalifikowaną nazwę domeny. Na przykład *Something.contoso.com*. Nazwa FQDN powinna być rozpoznawalna z bramy sieci VPN. Jest to możliwe, jeśli serwer DNS hostujący tę nazwę FQDN jest dostępny za pośrednictwem Internetu. Adres IP ma pierwszeństwo w przypadku określenia zarówno adresu IP, jak i nazwy FQDN.

      >[!NOTE]
      >
      >* Obsługuje jeden adres IPv4 na nazwę FQDN. Jeśli nazwa FQDN została rozpoznana jako wiele adresów IP, Brama sieci VPN Pobiera pierwszy adres IP4 z listy. Adresy IPv6 nie są w tej chwili obsługiwane.
      >
      >* Brama sieci VPN obsługuje pamięć podręczną DNS, która jest odświeżana co 5 minut. Brama próbuje rozpoznać nazwy FQDN tylko dla odłączonych tuneli. Resetowanie bramy lub zmiana konfiguracji może również wyzwolić rozpoznawanie nazw FQDN.
      >
5. Możesz użyć pola wyboru, aby usunąć lub dodać więcej linków. Obsługiwane są cztery linki na witrynę sieci VPN. Na przykład jeśli masz czterech usługodawców internetowych (usługodawcę internetowego) w lokalizacji gałęzi, możesz utworzyć cztery linki, jeden dla każdego usługodawcy internetowego i podać informacje dla każdego łącza.
6. Po zakończeniu wypełniania pól wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić i utworzyć lokację.
7. Wyświetl stan na stronie witryny sieci VPN. Lokacja przejdzie do **połączenia** , ponieważ lokacja nie została jeszcze podłączona do centrum.
