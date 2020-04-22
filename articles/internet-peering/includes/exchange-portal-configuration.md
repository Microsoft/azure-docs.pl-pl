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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680941"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** wypełnij pola w sposób pokazany na rysunku.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie typu komunikacji równorzędnej programu Exchange](../media/setup-exchange-conf-tab.png)

    * W przypadku **typu komunikacji równorzędnej**wybierz pozycję **Exchange**.
    * Wybierz **jednostkę SKU** jako **bezpłatną opcję Basic**.
    * Wybierz lokalizację **metra,** w której chcesz skonfigurować komunikację równorzędnej.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro** i po raz pierwszy używasz portalu do konfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej zostaną wyświetlone w sekcji **Połączenia komunikacji równorzędnej,** jak pokazano na rysunku. Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasób platformy Azure, dzięki czemu można nimi zarządzać wraz z nowymi połączeniami w jednym miejscu. Aby uzyskać więcej informacji, zobacz [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu portalu](../howto-legacy-exchange-portal.md).
        >

1. W obszarze **Połączenia komunikacji równorzędnej**wybierz pozycję **Utwórz nowy,** aby dodać wiersz dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować lub zmodyfikować ustawienia połączenia, wybierz przycisk edycji wiersza.

        > [!div class="mx-imgBorder"]
        > ![Przycisk Edytuj](../media/setup-exchange-conf-tab-edit.png)

    * Aby usunąć wiersz, wybierz **...**  >  **Usuń**plik .

        > [!div class="mx-imgBorder"]
        > ![Przycisk Usuń](../media/setup-exchange-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![Strona Połączenie komunikacji równorzędnej programu Exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Wybierz **obiekt komunikacji równorzędnej,** w której należy skonfigurować połączenie.
        1. W polach **adresowych IPv4** i **IPv6** wprowadź adresy IPv4 i IPv6, które byłyby skonfigurowane w routerach firmy Microsoft za pomocą polecenia sąsiada.
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w **polach Maksymalna anonsowana adresy IPv4** i **Maksymalna anonsowane adresy IPv6.**
        1. Wybierz **przycisk OK,** aby zapisać ustawienia połączenia.

1. Powtórz ten krok, aby dodać więcej połączeń w dowolnym obiekcie, w którym firma Microsoft jest współlokowana z siecią, w ramach wcześniej wybranego **metra.**

1. Po dodaniu wszystkich wymaganych połączeń wybierz pozycję **Przejrzyj + utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Karta Konfiguracja komunikacji równorzędnej](../media/setup-exchange-conf-tab-final.png)

1. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *Trwa sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po zmianie wiadomości na *Sprawdzanie poprawności sprawdź*swoje informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Aby zmodyfikować żądanie, wybierz **opcję Poprzedni** i powtórz kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-exchange-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie jest wyświetlane w następujący sposób.

    > [!div class="mx-imgBorder"]
    > ![Sukces rówieśników](../media/setup-direct-success.png)
