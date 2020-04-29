---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73488844"
---
1. Na stronie portalu wirtualnej sieci WAN w sekcji **połączenie** wybierz pozycję **Lokacje sieci VPN** , aby otworzyć stronę witryny sieci VPN.
2. Na stronie **Lokacje sieci VPN** kliknij pozycję **+Utwórz lokację**.

   ![Podstawy](./media/virtual-wan-tutorial-site-include/basics.png "Podstawy")
3. Na stronie **Tworzenie witryny sieci VPN** na karcie **podstawowe** wykonaj następujące pola:

    * **Region** — wcześniej nazywany lokalizacją. Jest to lokalizacja, w której chcesz utworzyć zasób lokacji.
    * **Nazwa** — nazwa, przez którą chcesz odwołać się do lokacji lokalnej.
    * **Dostawca urządzenia** — nazwa dostawcy urządzenia sieci VPN (na przykład: Citrix, Cisco, Barracuda). Dzięki temu zespół platformy Azure może lepiej zrozumieć swoje środowisko w celu dodania do niego dodatkowych możliwości optymalizacji lub ułatwienia rozwiązywania problemów.
    * Wartość Włącz **Border Gateway Protocol** oznacza, że wszystkie połączenia z lokacji będą włączone przy użyciu protokołu BGP. Ostatecznie skonfigurujemy informacje protokołu BGP dla każdego łącza z witryny sieci VPN w sekcji linki. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z konfiguracją protokołu BGP na sieci VPN bramy sieci wirtualnej platformy Azure. Adres lokalnego elementu równorzędnego protokołu BGP nie może być taki sam jak publiczny adres IP sieci VPN do urządzenia lub przestrzeni adresowej sieci wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Jednak nie może być adresem APIPA (169.254. x. x). Określ ten adres w odpowiedniej witrynie sieci VPN reprezentującej lokalizację. Wymagania wstępne dotyczące protokołu BGP zawiera temat [Informacje o protokole BGP z platformą Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Można zawsze edytować połączenie sieci VPN, aby zaktualizować jego parametry protokołu BGP (Komunikacja równorzędna w ramach łącza i jako #) po włączeniu ustawienia protokołu BGP witryny sieci VPN.
    * **Prywatna przestrzeń adresowa** — przestrzeń adresów IP znajdująca się w lokacji lokalnej. Ruch do tej przestrzeni adresowej jest kierowany do lokacji lokalnej. Jest to wymagane, gdy dla lokacji nie jest włączony protokół BGP.
    * **Hubs — centrum** , z którym chcesz połączyć się z lokacją. Lokację można podłączyć tylko do centrów, które mają VPN Gateway. Jeśli nie widzisz centrum, najpierw utwórz bramę sieci VPN w tym centrum.
4. Wybierz **linki** , aby dodać informacje o fizycznych linkach w gałęzi. Jeśli masz urządzenie z wirtualnym partnerem sieci WAN CPE, skontaktuj się z nimi, aby sprawdzić, czy te informacje są wymieniane z platformą Azure w ramach przekazywania informacji o gałęziach skonfigurowanych na podstawie ich systemów.

   ![linki](./media/virtual-wan-tutorial-site-include/links.png "Linki")

    * **Nazwa łącza** — nazwa, która ma zostać połączona z łączem fizycznym w witrynie sieci VPN. Przykład: Mylink1.
    * **Nazwa dostawcy** — nazwa fizycznego linku w witrynie sieci VPN. Przykład: ATT, Verizon.
    * **Szybkość** — szybkość urządzenia sieci VPN w lokalizacji gałęzi. Przykład: 50, co oznacza, że 50 MB/s to szybkość urządzenia sieci VPN w lokacji oddziału.
    * **Adres IP** — publiczny adres IP urządzenia Premium za pomocą tego linku. Opcjonalnie możesz podać prywatny adres IP lokalnego urządzenia sieci VPN znajdującego się za ExpressRoute.
5. Możesz użyć pola wyboru, aby usunąć lub dodać dodatkowe linki. Obsługiwane są cztery linki na witrynę sieci VPN. Na przykład jeśli masz czterech USŁUGODAWCów internetowych (usługodawców internetowych) w lokalizacji gałęzi, możesz utworzyć cztery linki. jeden dla każdego usługodawcy internetowego i podaj informacje dla każdego łącza.
6. Po zakończeniu wypełniania pól wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić i utworzyć lokację.
7. Wyświetl stan na stronie witryny sieci VPN. Lokacja przejdzie do **połączenia** , ponieważ lokacja nie została jeszcze podłączona do centrum.