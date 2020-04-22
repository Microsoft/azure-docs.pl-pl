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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678570"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** wypełnij pola, jak pokazano tutaj.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie typu komunikacji równorzędnej programu Exchange](../media/setup-exchange-conf-tab.png)

    * W przypadku **typu komunikacji równorzędnej**wybierz pozycję **Exchange**.
    * Wybierz **jednostkę SKU** jako **bezpłatną opcję Basic**.
    * Wybierz lokalizację **metro,** w której chcesz przekonwertować komunikację równorzędną na zasób platformy Azure. Jeśli masz połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro,** które nie są konwertowane na zasób platformy Azure, te połączenia zostaną wyświetlone w sekcji **Połączenia równorzędne,** jak pokazano. Teraz można przekonwertować te połączenia komunikacji równorzędnej na zasób platformy Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista połączeń komunikacji równorzędnej](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nie można modyfikować ustawień starszych połączeń komunikacji równorzędnej. Jeśli chcesz dodać dodatkowe połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro,** wybierz pozycję **Utwórz nowy**. Aby uzyskać więcej informacji, zobacz [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](../howto-exchange-portal.md).
        >

1. Wybierz pozycję **Przegląd + utwórz**. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *Trwa sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po zmianie wiadomości na *Sprawdzanie poprawności sprawdź*swoje informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Jeśli chcesz zmodyfikować żądanie, wybierz **poprzedni** i powtórz kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie jest wyświetlane w sposób pokazany.

    > [!div class="mx-imgBorder"]
    > ![Sukces rówieśników](../media/setup-direct-success.png)
