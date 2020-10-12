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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81681079"
---
1. Na stronie **Tworzenie komunikacji równorzędnej** na karcie **Konfiguracja** Wypełnij pola, jak pokazano poniżej.

    * W obszarze **Typ komunikacji równorzędnej**wybierz pozycję **bezpośrednie**.
    * W przypadku **sieci firmy Microsoft**wybierz pozycję **AS8075**. Nie twórz komunikacji równorzędnej z numerem ASN 8069. Jest ona zarezerwowana dla specjalnych aplikacji i jest używana tylko przez [komunikację równorzędną firmy Microsoft](mailto:peering@microsoft.com).
    * Wybierz **jednostkę SKU** jako **bezpłatnie jako podstawową**. Nie wybieraj opcji Bezpłatna wersja Premium, ponieważ jest zarezerwowana dla aplikacji specjalnych.
    * Wybierz lokalizację **Metro** , w której chcesz skonfigurować komunikację równorzędną.

        > [!NOTE]
        > Jeśli masz już połączenia komunikacji równorzędnej z firmą Microsoft w wybranej lokalizacji **Metro** i używasz Azure Portal po raz pierwszy w celu skonfigurowania komunikacji równorzędnej w tej lokalizacji, istniejące połączenia komunikacji równorzędnej będą wyświetlane w sekcji **połączenia komunikacji równorzędnej** . Firma Microsoft automatycznie przekonwertuje te połączenia komunikacji równorzędnej na zasób platformy Azure, aby można było zarządzać nimi, wraz z nowymi połączeniami w jednym miejscu. Aby uzyskać więcej informacji, zobacz [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](../howto-legacy-direct-portal.md).
        >

1. W obszarze **połączenia komunikacji równorzędnej**wybierz pozycję **Utwórz nowy** , aby dodać wiersz dla każdego nowego połączenia, które chcesz skonfigurować.

    * Aby skonfigurować lub zmodyfikować ustawienia połączenia, wybierz przycisk Edytuj dla wiersza.

        > [!div class="mx-imgBorder"]
        > ![Przycisk Edytuj](../media/setup-direct-conf-tab-edit.png)
    
    * Aby usunąć linię, wybierz pozycję **...**  >  **Usuń**.

        > [!div class="mx-imgBorder"]
        > ![Przycisk Usuń](../media/setup-direct-conf-tab-delete.png)

    * Musisz podać wszystkie ustawienia połączenia, jak pokazano poniżej.

         > [!div class="mx-imgBorder"]
         > ![Strona połączenia bezpośredniego komunikacji równorzędnej](../media/setup-direct-conf-tab-connection.png)

        1. Wybierz funkcję **komunikacji równorzędnej** , w której ma zostać skonfigurowane połączenie.
        1. **Dostawca adresów sesji** służy do określania, kto zapewnia podsieć potrzebną do skonfigurowania sesji protokołu BGP między siecią i firmą Microsoft. Jeśli można podać podsieć, wybierz pozycję **równorzędne**. W przeciwnym razie wybierz pozycję **Microsoft** i [Komunikacja równorzędna firmy Microsoft](mailto:peering@microsoft.com) będą kontaktować się z Tobą. Wybranie tej opcji zajmie więcej czasu firmie Microsoft w celu przetworzenia żądania komunikacji równorzędnej. W niektórych przypadkach firma Microsoft może nie być w stanie zapewnić podsieci, co spowoduje odmowę żądania.
        1. W przypadku wybrania opcji **dostawca adresów sesji** jako **elementu równorzędnego**wprowadź adresy IPv4 i IPv6 wraz z maską prefiksu odpowiednio w polach **prefiks IPv4 sesji** i **prefiks IPv6 sesji** .
        1. Wprowadź liczbę prefiksów IPv4 i IPv6, które będą anonsowane w polach **Maksymalna liczba anonsowanych adresów IPv4** i **maksymalnych anonsowanych adresów IPv6** .
        1. Dostosuj suwak **łączną przepustowość** , aby odzwierciedlał przepustowość połączenia.
        1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia połączenia.

1. Powtórz poprzedni krok, aby dodać więcej połączeń w dowolnym miejscu, w którym firma Microsoft znajduje się w sieci, w ramach **linii metra** wybranej wcześniej.

1. Po dodaniu wszystkich wymaganych połączeń wybierz pozycję **Przegląd + Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Ostatnia Karta Konfiguracja komunikacji równorzędnej](../media/setup-direct-conf-tab-final.png)

1. Należy zauważyć, że portal uruchamia podstawowe sprawdzanie poprawności wprowadzonych informacji. Wstążka u góry wyświetla komunikat *z uruchomioną weryfikacją ostateczną...*

    > [!div class="mx-imgBorder"]
    > ![Karta Walidacja komunikacji równorzędnej](../media/setup-direct-review-tab-validation.png)

1. Gdy wiadomość zostanie *przeniesiona na weryfikację*, sprawdź informacje. Prześlij żądanie, wybierając pozycję **Utwórz**. Aby zmodyfikować żądanie, wybierz pozycję **Poprzednia** i powtórz kroki.

    > [!div class="mx-imgBorder"]
    > ![Przesyłanie komunikacji równorzędnej](../media/setup-direct-review-tab-submit.png)

1. Po przesłaniu żądania poczekaj na zakończenie wdrożenia. Jeśli wdrożenie nie powiedzie się, skontaktuj się z pomocą techniczną [firmy Microsoft](mailto:peering@microsoft.com). Pomyślne wdrożenie zostanie wyświetlone jak pokazano poniżej.

    > [!div class="mx-imgBorder"]
    > ![Pomyślne sukcesy komunikacji równorzędnej](../media/setup-direct-success.png)
