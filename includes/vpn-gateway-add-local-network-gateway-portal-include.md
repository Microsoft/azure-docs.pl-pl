---
title: plik dołączania
description: plik dołączania
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 059d15090204c2fce0fddd4b80f4954755ea8f65
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89449611"
---
1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**. 

   ![Tworzenie zasobu w witrynie Azure Portal](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. W polu **Wyszukaj w witrynie Marketplace** wpisz **Brama sieci lokalnej**, a następnie naciśnij klawisz **Enter** , aby wyszukać. Spowoduje to zwrócenie listy wyników. Kliknij pozycję **Brama sieci lokalnej**, a następnie kliknij przycisk **Utwórz**, aby otworzyć stronę **Utwórz bramę sieci lokalnej**.

   ![Tworzenie bramy sieci lokalnej](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Tworzenie bramy sieci lokalnej")

3. Na stronie **Utwórz bramę sieci lokalnej** określ wartości dla bramy sieci lokalnej.

   - **Nazwa:** określ nazwę obiektu bramy sieci lokalnej.
   - **Adres IP:** jest to publiczny adres IP urządzenia sieci VPN, z którym ma nawiązać połączenie platforma Azure. Określ prawidłowy publiczny adres IP. Jeśli w danej chwili nie masz adresu IP, możesz użyć wartości widocznych w przykładzie, ale konieczny będzie powrót i zamiana zastępczego adresu IP na publiczny adres IP urządzenia sieci VPN. W przeciwnym razie usługa Azure nie będzie mogła nawiązać połączenia.
   - **Przestrzeń adresowa** odnosi się do zakresów adresów sieci, które reprezentuje obiekt sieci lokalnej (sieć lokalna). Należy dodać przestrzenie adresowe, które mają być kierowane do sieci lokalnej. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz się łączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. *Jeśli chcesz nawiązać połączenie ze swoją lokacją lokalną, podaj w tym miejscu własne wartości, a nie wartości pokazane w przykładzie*.
   - **Skonfiguruj ustawienia protokołu BGP:** użyj tej opcji tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
   - **Subskrypcja:** sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
   - **Grupa zasobów** wybierz grupę zasobów, która ma być używana. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
   - **Lokalizacja:** Lokalizacja jest taka sama jak **region** w innych ustawieniach. Wybierz lokalizację, w której zostanie utworzony ten obiekt. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.

4. Po określeniu wartości kliknij przycisk **Utwórz** u dołu strony, aby utworzyć bramę sieci lokalnej.
