---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041537"
---
>[!NOTE]
>Musisz mieć uprawnienia administratora na komputerze klienckim z systemem Windows, z którym nawiązujesz połączenie.
>

1. Aby nawiązać połączenie z siecią wirtualną, na komputerze klienckim przejdź do ustawień sieci VPN i Znajdź utworzone połączenie sieci VPN. Nazywa się to taką samą nazwą jak sieć wirtualna. Wybierz pozycję **Połącz**. Może pojawić się komunikat podręczny, który odwołuje się do użycia certyfikatu. Wybierz pozycję **Kontynuuj** , aby użyć podwyższonych uprawnień.

1. Na stronie stanu **Połączenie** wybierz przycisk **Połącz**, aby rozpocząć połączenie. Jeśli widzisz ekran **Wybierz certyfikat**, sprawdź, czy wyświetlany certyfikat klienta to ten, który ma zostać użyty do nawiązania połączenia. Jeśli tak nie jest, użyj strzałki listy rozwijanej, aby wybrać poprawny certyfikat, a następnie wybierz przycisk **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Nawiązywanie połączenia z komputera z systemem Windows":::

1. Połączenie zostało ustanowione.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Łączenie się z komputera z diagramem połączeń sieci wirtualnej platformy Azure":::
