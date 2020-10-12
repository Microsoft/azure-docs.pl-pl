---
title: plik dołączany
titleSuffix: Azure
description: plik dołączany
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678570"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie komunikacji równorzędnej — typ komunikacji równorzędnej programu Exchange](../media/setup-exchange-conf-tab.png)

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję **Exchange**.
    * Wybierz **jednostkę SKU** jako **bezpłatnie jako podstawową**.
    * Wybierz lokalizację **, w której chcesz** skonwertować komunikację równorzędną z zasobem platformy Azure. Jeśli masz połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** , która nie jest konwertowana na zasób platformy Azure, te połączenia będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** , jak pokazano. Teraz możesz przekonwertować te połączenia komunikacji równorzędnej na zasób platformy Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista połączeń komunikacji równorzędnej](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Nie można modyfikować ustawień starszych połączeń komunikacji równorzędnej. Jeśli chcesz dodać dodatkowe połączenia komunikacji równorzędnej do firmy Microsoft w wybranej lokalizacji **Metro** , wybierz pozycję **Utwórz nowy**. Aby uzyskać więcej informacji, zobacz [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](../howto-exchange-portal.md).
        >

1. Wybierz pozycję **Przeglądanie + tworzenie**. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *z uruchomioną weryfikacją ostateczną...*

    > [!div class="mx-imgBorder"]
    > ![Karta Walidacja komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Gdy wiadomość zostanie *przeniesiona na weryfikację*, sprawdź informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Jeśli musisz zmodyfikować żądanie, wybierz pozycję **Poprzednia** i powtórz te kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie wygląda tak, jak pokazano.

    > [!div class="mx-imgBorder"]
    > ![Pomyślne sukcesy komunikacji równorzędnej](../media/setup-direct-success.png)
