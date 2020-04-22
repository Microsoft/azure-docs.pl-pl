---
title: Plik dyrektywy include
titleSuffix: Azure
description: Plik dyrektywy include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678591"
---
1. Wybierz **pozycję Utwórz zasób** > **Zobacz wszystkie**.

    > [!div class="mx-imgBorder"]
    > ![Komunikacja równorzędna wyszukiwania](../media/setup-seeall.png)

1. Wyszukaj w polu wyszukiwania **opcję Komunikacja równorzędna,** a następnie wybierz pozycję **Wprowadź** na klawiaturze. Z wyników wybierz zasób **komunikacji równorzędnej.**

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie komunikacji równorzędnej](../media/setup-launch.png)

1. Po **rozpoczęciu komunikacji równorzędnej** przejrzyj stronę, aby zrozumieć szczegóły. Gdy będziesz gotowy, wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie komunikacji równorzędnej](../media/setup-create.png)

1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Podstawy** wypełnij pola, jak pokazano tutaj.

    > [!div class="mx-imgBorder"]
    > ![Karta Podstawowe informacje równorzędne](../media/setup-basics-tab.png)

    * Wybierz **subskrypcję**platformy Azure .
    * W przypadku **grupy zasobów**można wybrać istniejącą grupę zasobów z listy rozwijanej lub utworzyć nową grupę, wybierając **pozycję Utwórz nowy**. W tym przykładzie utworzymy nową grupę zasobów.
    * **Nazwa** odpowiada nazwie zasobu i może być wszystkim, co wybierzesz.
    * **Region** jest wybierany automatycznie, jeśli wybrano istniejącą grupę zasobów. Jeśli zdecydujesz się utworzyć nową grupę zasobów, należy również wybrać region platformy Azure, w którym ma się rezydować zasób.

        > [!NOTE]
        > Region, w którym znajduje się grupa zasobów, jest niezależny od lokalizacji, w której chcesz utworzyć komunikację równorzędną z firmą Microsoft. Najlepszym rozwiązaniem jest jednak organizowanie zasobów komunikacji równorzędnej w grupach zasobów znajdujących się w najbliższych regionach platformy Azure. Na przykład dla komunikacji równorzędnej w Ashburn można utworzyć grupę zasobów we wschodnich stanach USA lub wschodnich us2.

    * Wybierz asn w **peer ASN** pole.

        > [!IMPORTANT]
        > * Przed przesłaniem żądania komunikacji równorzędnej można wybrać asn z certyfikatem sprawdzania poprawności jako zatwierdzony. Jeśli właśnie przesłałeś żądanie PeerAsn, poczekaj około 12 godzin na zatwierdzenie skojarzenia ASN. Jeśli wybrana asn oczekuje na weryfikację, zostanie wyświetlony komunikat o błędzie. 
        > * Jeśli nie widzisz asn trzeba wybrać, sprawdź, czy wybrano poprawną subskrypcję. Jeśli tak, sprawdź, czy utworzono już peerasn przy użyciu [skojarzenia elementów równorzędnych ASN do subskrypcji platformy Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Wypełnione podstawy komunikacji równorzędnej](../media/setup-direct-basics-filled-tab.png)

    * Wybierz **dalej: >konfiguracji,** aby kontynuować.
