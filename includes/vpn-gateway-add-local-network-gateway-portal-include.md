---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "91024844"
---
1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**.

   ![Zasób](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. W polu **Wyszukaj w witrynie Marketplace** wpisz **Brama sieci lokalnej**, a następnie naciśnij klawisz **Enter** , aby wyszukać. Spowoduje to zwrócenie listy wyników. Kliknij pozycję **Brama sieci lokalnej**, a następnie kliknij przycisk **Utwórz**, aby otworzyć stronę **Utwórz bramę sieci lokalnej**.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Utwórz adres IP bramy sieci lokalnej":::

3. Na stronie **Utwórz bramę sieci lokalnej** określ wartości dla bramy sieci lokalnej.

   - **Nazwa:** określ nazwę obiektu bramy sieci lokalnej.
   - **Punkt końcowy:** Wybierz typ punktu końcowego dla lokalnego urządzenia sieci VPN — **adres IP** lub **nazwa FQDN (w pełni kwalifikowana nazwa domeny)**.
      - **Adres IP**: Jeśli masz statyczny publiczny adres IP przydzielony przez usługodawcę internetowego do urządzenia sieci VPN, wybierz opcję adres IP i wprowadź adres IP, jak pokazano w przykładzie. Jest to publiczny adres IP urządzenia sieci VPN, z którym ma zostać nawiązane połączenie z usługą Azure VPN Gateway. Jeśli w danej chwili nie masz adresu IP, możesz użyć wartości widocznych w przykładzie, ale konieczny będzie powrót i zamiana zastępczego adresu IP na publiczny adres IP urządzenia sieci VPN. W przeciwnym razie usługa Azure nie będzie mogła nawiązać połączenia.
      - **Nazwa FQDN**: Jeśli masz dynamiczny publiczny adres IP, który może ulec zmianie po określonym czasie, zwykle określanym przez dostawcę usług internetowych, możesz użyć stałej nazwy DNS z dynamiczną usługą DNS, aby wskazać bieżący publiczny adres IP urządzenia sieci VPN. Brama sieci VPN platformy Azure rozpozna nazwę FQDN, aby określić publiczny adres IP, z którym ma zostać nawiązane połączenie. Poniższy zrzut ekranu przedstawia przykład użycia nazwy FQDN zamiast adresu IP.
   - **Przestrzeń adresowa** odwołuje się do zakresów adresów sieci, które reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz się łączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. *Jeśli chcesz nawiązać połączenie ze swoją lokacją lokalną, podaj w tym miejscu własne wartości, a nie wartości pokazane w przykładzie*.
   - **Skonfiguruj ustawienia protokołu BGP:** użyj tej opcji tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
   - **Subskrypcja:** sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
   - **Grupa zasobów** wybierz grupę zasobów, która ma być używana. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
   - **Lokalizacja:** Lokalizacja jest taka sama jak **region** w innych ustawieniach. Wybierz lokalizację, w której zostanie utworzony ten obiekt. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.

   Jest to ta sama strona, ale z wyróżnioną nazwą FQDN:

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Utwórz adres IP bramy sieci lokalnej":::

   > [!NOTE]
   >
   > * Usługa Azure VPN obsługuje tylko jeden adres IPv4 dla każdej nazwy FQDN. Jeśli nazwa domeny jest rozpoznawana jako wiele adresów IP, usługa Azure VPN Gateway będzie używać pierwszego adresu IP zwróconego przez serwery DNS. Aby wyeliminować niepewność, zalecamy, aby nazwa FQDN była zawsze rozpoznawana jako pojedynczy adres IPv4. Protokół IPv6 nie jest obsługiwany.
   > * Usługa Azure VPN Gateway obsługuje pamięć podręczną DNS odświeżaną co 5 minut. Brama próbuje rozpoznać nazwy FQDN tylko dla odłączonych tuneli. Zresetowanie bramy spowoduje również wyzwolenie rozpoznawania nazw FQDN.
   >

4. Po zakończeniu określania wartości wybierz przycisk **Utwórz** u dołu strony, aby utworzyć bramę sieci lokalnej.
