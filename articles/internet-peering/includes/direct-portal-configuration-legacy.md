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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678815"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** wypełnij pola, jak pokazano tutaj.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie karty Konfiguracja strony komunikacji równorzędnej](../media/setup-direct-conf-tab.png)

    * W przypadku **typu komunikacji równorzędnej**wybierz opcję **Bezpośrednie**.
    * W przypadku **sieci Firmy Microsoft**wybierz **AS8075**. Nie wybieraj ASN 8069. Jest zarezerwowany dla specjalnych aplikacji i używany tylko przez [komunikację równorzędną firmy Microsoft.](mailto:peering@microsoft.com)
    * Wybierz **jednostkę SKU** jako **bezpłatną opcję Basic**. Nie wybieraj Premium Free, ponieważ jest on zarezerwowany dla specjalnych aplikacji.
    * Wybierz lokalizację **metro,** w której chcesz przekonwertować komunikację równorzędną na zasób platformy Azure. Jeśli masz połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro,** które nie są konwertowane na zasób platformy Azure, te połączenia zostaną wyświetlone w sekcji **Połączenia równorzędne,** jak pokazano. Teraz można przekonwertować te połączenia komunikacji równorzędnej na zasób platformy Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista połączeń komunikacji równorzędnej](../media/setup-directlegacy-conf-tab.png)

1. Jeśli chcesz zaktualizować przepustowość, wybierz przycisk edycji dla wiersza, aby zmodyfikować ustawienia połączenia.

    > [!div class="mx-imgBorder"]
    > ![Przycisk Edytuj](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Jeśli chcesz dodać dodatkowe połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro,** wybierz pozycję **Utwórz nowy**. Aby uzyskać więcej informacji, zobacz [Tworzenie lub modyfikowanie bezpośredniego komunikacji równorzędnej przy użyciu portalu](../howto-direct-portal.md).
    >

1. Wybierz pozycję **Przegląd + utwórz**. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *Trwa sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po zmianie wiadomości na *Sprawdzanie poprawności sprawdź*swoje informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Aby zmodyfikować żądanie, wybierz **opcję Poprzedni** i powtórz kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-direct-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie jest wyświetlane w następujący sposób.

    > [!div class="mx-imgBorder"]
    > ![Sukces rówieśników](../media/setup-direct-success.png)
