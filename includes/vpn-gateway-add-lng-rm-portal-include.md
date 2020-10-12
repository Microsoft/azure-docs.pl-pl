---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025187"
---
1. W portalu kliknij pozycję **+Utwórz zasób**.
2. W polu wyszukiwania wpisz wartość **Brama sieci lokalnej**, a następnie naciśnij klawisz **Enter**, aby wyszukać. Spowoduje to zwrócenie listy wyników. Kliknij pozycję **Brama sieci lokalnej**, a następnie kliknij przycisk **Utwórz**, aby otworzyć stronę **Utwórz bramę sieci lokalnej**.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="Tworzenie bramy sieci lokalnej":::

3. Na stronie **Utwórz bramę sieci lokalnej** określ wartości dla bramy sieci lokalnej.

   - **Nazwa:** określ nazwę obiektu bramy sieci lokalnej.
   - **Punkt końcowy:** Wybierz typ punktu końcowego dla lokalnego urządzenia sieci VPN — **adres IP** lub **nazwa FQDN (w pełni kwalifikowana nazwa domeny)**.
      - **Adres IP**: Jeśli masz statyczny publiczny adres IP przydzielony przez usługodawcę internetowego do urządzenia sieci VPN, wybierz opcję adres IP i wprowadź adres IP, jak pokazano w przykładzie. Jest to publiczny adres IP urządzenia sieci VPN, z którym ma zostać nawiązane połączenie z usługą Azure VPN Gateway. Jeśli w danej chwili nie masz adresu IP, możesz użyć wartości widocznych w przykładzie, ale konieczny będzie powrót i zamiana zastępczego adresu IP na publiczny adres IP urządzenia sieci VPN. W przeciwnym razie usługa Azure nie będzie mogła nawiązać połączenia.
      - **Nazwa FQDN**: Jeśli masz dynamiczny publiczny adres IP, który może ulec zmianie po określonym czasie, zwykle określanym przez dostawcę usług internetowych, możesz użyć stałej nazwy DNS z dynamiczną usługą DNS, aby wskazać bieżący publiczny adres IP urządzenia sieci VPN. Brama sieci VPN platformy Azure rozpozna nazwę FQDN, aby określić publiczny adres IP, z którym ma zostać nawiązane połączenie. Poniższy zrzut ekranu przedstawia przykład użycia nazwy FQDN zamiast adresu IP.
   - **Przestrzeń adresowa** odwołuje się do zakresów adresów sieci, które reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz się łączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. *Jeśli chcesz nawiązać połączenie ze swoją lokacją lokalną, podaj w tym miejscu własne wartości, a nie wartości pokazane w przykładzie*.
   - **Skonfiguruj ustawienia protokołu BGP:** użyj tej opcji tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
   - **Subskrypcja:** sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
   - **Grupa zasobów** wybierz grupę zasobów, która ma być używana. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
   - **Lokalizacja:** wybierz lokalizację, w której ten obiekt zostanie utworzony. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.

    Jest to ta sama strona, ale z wyróżnioną nazwą FQDN:
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="Tworzenie bramy sieci lokalnej":::
   
   > [!NOTE]
   >
   > * Usługa Azure VPN obsługuje tylko jeden adres IPv4 dla każdej nazwy FQDN. Jeśli nazwa domeny jest rozpoznawana jako wiele adresów IP, usługa Azure VPN Gateway będzie używać pierwszego adresu IP zwróconego przez serwery DNS. Aby wyeliminować niepewność, zaleca się, aby nazwa FQDN była zawsze rozpoznawana jako pojedynczy adres IPv4. Protokół IPv6 nie jest obsługiwany.
   > * Usługa Azure VPN Gateway obsługuje pamięć podręczną DNS odświeżaną co 5 minut. Brama próbuje rozpoznać nazwy FQDN tylko dla odłączonych tuneli. Zresetowanie bramy spowoduje również wyzwolenie rozpoznawania nazw FQDN.
   >

4. Po zakończeniu określania wartości wybierz przycisk **Utwórz** , aby utworzyć bramę.
