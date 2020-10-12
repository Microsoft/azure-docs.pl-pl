---
title: plik dołączany
titleSuffix: Azure
description: plik dołączany
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83846174"
---
1. Wybierz pozycję **Utwórz zasób**  >  **Zobacz wszystko**.

    > [!div class="mx-imgBorder"]
    > ![Wyszukaj komunikację równorzędną](../media/setup-seeall.png)

1. Wyszukaj **komunikację równorzędną** w polu wyszukiwania, a następnie wybierz pozycję **Enter** na klawiaturze. Z wyników wybierz zasób **komunikacji równorzędnej** .

    > [!div class="mx-imgBorder"]
    > ![Uruchom komunikację równorzędną](../media/setup-launch.png)

1. Po rozpoczęciu **komunikacji równorzędnej** zapoznaj się ze stroną, aby poznać szczegóły. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie komunikacji równorzędnej](../media/setup-create.png)

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **podstawowe** Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Karta podstawy komunikacji równorzędnej](../media/setup-basics-tab.png)

    * Wybierz swoją **subskrypcję**platformy Azure.
    * W obszarze **Grupa zasobów**można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, wybierając pozycję **Utwórz nową**. W tym przykładzie utworzymy nową grupę zasobów.
    * **Nazwa** odpowiada nazwie zasobu i może być dowolna.
    * **Region** jest wybierany w przypadku wybrania istniejącej grupy zasobów. Jeśli wybrano opcję utworzenia nowej grupy zasobów, należy również wybrać region platformy Azure, w którym ma się znajdować zasób.

        > [!NOTE]
        > Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędną z firmą Microsoft. Najlepszym rozwiązaniem jest zorganizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach świadczenia usługi Azure. Na przykład w przypadku komunikacji równorzędnej w Ashburn można utworzyć grupę zasobów w regionie Wschodnie stany USA lub Wschodnie stany USA 2.

    * W polu **równorzędny** numer ASN wybierz swój numer ASN.

        > [!IMPORTANT]
        > * Przed przesłaniem żądania komunikacji równorzędnej można wybrać tylko numer ASN z ValidationState jako zatwierdzony. Jeśli żądanie PeerAsn zostało przesłane, poczekaj przez 12 godzin lub aby można było zatwierdzić skojarzenie ASN. Jeśli wybrany numer ASN oczekuje na weryfikację, zobaczysz komunikat o błędzie. 
        > * Jeśli nie widzisz numeru ASN, który musisz wybrać, sprawdź, czy wybrano prawidłową subskrypcję. Jeśli tak jest, sprawdź, czy utworzono już PeerAsn za pomocą polecenia [Skojarz równorzędny numer ASN z subskrypcją platformy Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Napełnione podstawy komunikacji równorzędnej](../media/setup-direct-basics-filled-tab.png)

    * Wybierz pozycję **Dalej: >konfiguracji ** , aby kontynuować.
