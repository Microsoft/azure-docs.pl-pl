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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681079"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** wypełnij pola, jak pokazano tutaj.

    * W przypadku **typu komunikacji równorzędnej**wybierz opcję **Bezpośrednie**.
    * W przypadku **sieci Firmy Microsoft**wybierz **AS8075**. Nie twórz komunikacji równorzędnej z ASN 8069. Jest zarezerwowany dla specjalnych aplikacji i używany tylko przez [komunikację równorzędną firmy Microsoft.](mailto:peering@microsoft.com)
    * Wybierz **jednostkę SKU** jako **bezpłatną opcję Basic**. Nie wybieraj Premium Free, ponieważ jest on zarezerwowany dla specjalnych aplikacji.
    * Wybierz lokalizację **metra,** w której chcesz skonfigurować komunikację równorzędnej.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **metro** i używasz portalu Azure po raz pierwszy, aby skonfigurować komunikację równorzędnych w tej lokalizacji, istniejące połączenia komunikacji równorzędnej zostaną wyświetlone w sekcji **Połączenia komunikacji równorzędnej,** jak pokazano na rysunku. Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasób platformy Azure, dzięki czemu można zarządzać nimi wszystkimi, wraz z nowymi połączeniami, w jednym miejscu. Aby uzyskać więcej informacji, zobacz [Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](../howto-legacy-direct-portal.md).
        >

1. W obszarze **Połączenia komunikacji równorzędnej**wybierz pozycję **Utwórz nowy,** aby dodać wiersz dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować lub zmodyfikować ustawienia połączenia, wybierz przycisk edycji wiersza.

        > [!div class="mx-imgBorder"]
        > ![Przycisk Edytuj](../media/setup-direct-conf-tab-edit.png)
    
    * Aby usunąć wiersz, wybierz **...**  >  **Usuń**plik .

        > [!div class="mx-imgBorder"]
        > ![Przycisk Usuń](../media/setup-direct-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![Bezpośrednia strona połączenia równorzędnego](../media/setup-direct-conf-tab-connection.png)

        1. Wybierz **obiekt komunikacji równorzędnej,** w której należy skonfigurować połączenie.
        1. **Dostawca adresów sesji** służy do określenia, kto udostępnia podsieć potrzebną do skonfigurowania sesji BGP między siecią a firmą Microsoft. Jeśli można podać podsieć, **wybierz**peer . W przeciwnym razie **wybierz** microsoft i microsoft [komunikacji równorzędnej skontaktuje](mailto:peering@microsoft.com) się z Tobą. Wybranie tej opcji będzie trwać dłużej, aby firma Microsoft przetworzyła żądanie komunikacji równorzędnej. W niektórych przypadkach firma Microsoft może nie być w stanie podać podsieci, co spowoduje odmowę żądania.
        1. Jeśli jako **elementów równorzędnych**wybrano opcję **Dostawca adresu sesji,** wprowadź adresy IPv4 i IPv6 wraz z maskami prefiksów w **prefiksach IPv4 sesji** i **prefiksach sesji IPv6.**
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w **polach Maksymalna anonsowana adresy IPv4** i **Maksymalna anonsowane adresy IPv6.**
        1. Dostosuj suwak **Całkowita przepustowość,** aby odzwierciedlić przepustowość połączenia.
        1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia połączenia.

1. Powtórz poprzedni krok, aby dodać więcej połączeń w dowolnym obiekcie, w którym firma Microsoft jest współlokowana z siecią, w usłudze **Metro,** która została wybrana wcześniej.

1. Po dodaniu wszystkich wymaganych połączeń wybierz pozycję **Przejrzyj + utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Karta Konfiguracja komunikacji równorzędnej wersja ostateczna](../media/setup-direct-conf-tab-final.png)

1. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *Trwa sprawdzanie poprawności końcowej...*.

    > [!div class="mx-imgBorder"]
    > ![Karta Sprawdzanie poprawności komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Po zmianie wiadomości na *Sprawdzanie poprawności sprawdź*swoje informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Aby zmodyfikować żądanie, wybierz **opcję Poprzedni** i powtórz kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-direct-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z [programem Microsoft w komunikacji równorzędnej](mailto:peering@microsoft.com). Pomyślne wdrożenie jest wyświetlane w następujący sposób.

    > [!div class="mx-imgBorder"]
    > ![Sukces rówieśników](../media/setup-direct-success.png)
