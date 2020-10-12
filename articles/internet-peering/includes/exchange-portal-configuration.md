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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680941"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, tak jak pokazano.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie komunikacji równorzędnej — typ komunikacji równorzędnej programu Exchange](../media/setup-exchange-conf-tab.png)

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję **Exchange**.
    * Wybierz **jednostkę SKU** jako **bezpłatnie jako podstawową**.
    * Wybierz lokalizację **Metro** , w której chcesz skonfigurować komunikację równorzędną.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** i używasz portalu po raz pierwszy w celu skonfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** . Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasób platformy Azure, aby można było zarządzać nimi razem z nowymi połączeniami w jednym miejscu. Aby uzyskać więcej informacji, zobacz [konwertowanie starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu portalu](../howto-legacy-exchange-portal.md).
        >

1. W obszarze **połączenia komunikacji równorzędnej**wybierz pozycję **Utwórz nowy** , aby dodać wiersz dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować lub zmodyfikować ustawienia połączenia, wybierz przycisk Edytuj dla wiersza.

        > [!div class="mx-imgBorder"]
        > ![Przycisk Edytuj](../media/setup-exchange-conf-tab-edit.png)

    * Aby usunąć linię, wybierz pozycję **...**  >  **Usuń**.

        > [!div class="mx-imgBorder"]
        > ![Przycisk Usuń](../media/setup-exchange-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![Strona połączenia komunikacji równorzędnej programu Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Wybierz funkcję **komunikacji równorzędnej** , w której ma zostać skonfigurowane połączenie.
        1. W polach **adres IPv4** i **adres IPv6** wprowadź odpowiednio adresy IPv4 i IPv6, które zostaną skonfigurowane w routerach firmy Microsoft przy użyciu polecenia Neighbor.
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w polach **Maksymalna liczba anonsowanych adresów IPv4** i **maksymalnych anonsowanych adresów IPv6** .
        1. Wybierz **przycisk OK** , aby zapisać ustawienia połączenia.

1. Powtórz ten krok, aby dodać więcej połączeń w dowolnym miejscu, w którym firma Microsoft znajduje się w sieci w ramach wybranej wcześniej **linii metra** .

1. Po dodaniu wszystkich wymaganych połączeń wybierz pozycję **Przegląd + Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Karta Konfiguracja komunikacji równorzędnej](../media/setup-exchange-conf-tab-final.png)

1. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *z uruchomioną weryfikacją ostateczną...*

    > [!div class="mx-imgBorder"]
    > ![Karta Walidacja komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Gdy wiadomość zostanie *przeniesiona na weryfikację*, sprawdź informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Aby zmodyfikować żądanie, wybierz pozycję **Poprzednia** i powtórz kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Pomyślne sukcesy komunikacji równorzędnej](../media/setup-direct-success.png)
