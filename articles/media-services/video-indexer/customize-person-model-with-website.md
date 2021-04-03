---
title: Dostosowywanie modelu osoby za pomocą witryny sieci Web Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model osoby za pomocą witryny sieci Web Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2020
ms.author: juliako
ms.openlocfilehash: 890f8c159c3e8e8d0b4164cf218f320551ea63ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97628939"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Dostosowywanie modelu osoby za pomocą witryny sieci Web Video Indexer

Video Indexer obsługuje rozpoznawanie osobistości dla zawartości wideo. Funkcja rozpoznawania osobistości obejmuje około 1 000 000 twarzy na podstawie często zażądanego źródła danych, takiego jak IMDB, Wikipedia i najpopularniejszych wpływów serwisu LinkedIn. Aby zapoznać się z szczegółowym omówieniem, zobacz [Dostosowywanie modelu osoby w Video Indexer](customize-person-model-overview.md).

Za pomocą witryny sieci Web Video Indexer można edytować powierzchnie wykryte w filmie wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu osoby za pomocą interfejsów API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centralne zarządzanie modelami osób na Twoim koncie

1. Aby wyświetlać, edytować i usuwać modele osób na Twoim koncie, przejdź do witryny sieci Web Video Indexer i zaloguj się.
1. Wybierz przycisk Dostosowywanie modelu zawartości po lewej stronie.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Dostosuj model zawartości":::
1. Wybierz kartę osoby.

    Zobaczysz domyślny model osoby na Twoim koncie. Domyślny model osoby zawiera wszystkie powierzchnie, które mogły zostać zmodyfikowane lub zmienione w szczegółowych informacjach wideo, dla których nie został określony niestandardowy model osoby podczas indeksowania.

    Jeśli utworzono inne modele osób, zostaną one również wyświetlone na tej stronie.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/content-model-customization-people-tab.png" alt-text="Dostosuj osoby":::

## <a name="create-a-new-person-model"></a>Utwórz nowy model osoby

1. Wybierz przycisk **+ Dodaj model** po prawej stronie.
1. Wprowadź nazwę modelu. Nowe osoby i powierzchnie można teraz dodawać do nowego modelu osoby.
1. Wybierz przycisk menu listy i wybierz pozycję **+ Dodaj osobę**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/add-new-person.png" alt-text="Dodaj peron":::

## <a name="add-a-new-person-to-a-person-model"></a>Dodawanie nowej osoby do modelu osoby

> [!NOTE]
> Video Indexer umożliwia dodanie wielu osób o tej samej nazwie w modelu osoby. Zaleca się jednak nadawanie unikatowych nazw każdej osobie w modelu w celu zapewnienia użyteczności i przejrzystości.

1. Aby dodać nową miarę do modelu osoby, wybierz przycisk menu Lista obok modelu osoby, do której chcesz dodać miarę.
1. Wybierz pozycję **+ Dodaj osobę** z menu.

    W oknie podręcznym zostanie wyświetlony monit o wypełnienie szczegółów osoby. Wpisz nazwę osoby i wybierz przycisk wyboru.

    Następnie możesz wybrać z Eksploratora plików lub przeciągać i upuszczać obrazy z czołową stroną. Video Indexer będą przyjmować wszystkie standardowe typy plików obrazów (np. JPG, PNG itp.).

    Video Indexer może wykryć wystąpienia tej osoby w przyszłych klipach wideo, które są indeksowane, oraz bieżące wideo, które zostały już zindeksowane, przy użyciu modelu osoby, do którego dodano nową miarę. Rozpoznawanie osoby w Twoich bieżących filmach wideo może zająć trochę czasu, ponieważ jest to proces wsadowy.

## <a name="rename-a-person-model"></a>Zmiana nazwy modelu osoby

Można zmienić nazwę dowolnego modelu osoby na koncie, w tym domyślny model osoby. Nawet jeśli zmienisz nazwę domyślnego modelu osoby, będzie on nadal używany jako domyślny model osoby na Twoim koncie.

1. Wybierz przycisk menu listy obok modelu osoby, którego nazwę chcesz zmienić.
1. Wybierz pozycję **Zmień nazwę** z menu.
1. Wybierz bieżącą nazwę modelu i wpisz nową nazwę.
1. Wybierz przycisk Sprawdź, aby zmienić nazwę modelu.

## <a name="delete-a-person-model"></a>Usuń model osoby

Możesz usunąć dowolny model osoby utworzony na Twoim koncie. Nie można jednak usunąć domyślnego modelu osoby.

1. Z menu wybierz pozycję **Usuń** .

    Zostanie wyświetlone okno podręczne, a następnie zostanie wyświetlony komunikat z informacją, że ta akcja spowoduje usunięcie modelu osoby i wszystkich osób i plików, które zawiera. Tej akcji nie można cofnąć.
1. Jeśli masz pewność, wybierz pozycję Usuń ponownie.

> [!NOTE]
> Istniejące filmy wideo, które były indeksowane przy użyciu tego modelu osoby (teraz usunięty), nie obsługują możliwości aktualizowania nazw twarzy, które są wyświetlane w filmie wideo. Można edytować nazwy twarzy w tych filmach wideo dopiero po ponownym indeksie ich przy użyciu innego modelu osoby. W przypadku ponownego indeksowania bez określenia modelu osoby zostanie użyty domyślny model.

## <a name="manage-existing-people-in-a-person-model"></a>Zarządzanie istniejącymi osobami w modelu osoby

Aby przyjrzeć się zawartości dowolnego z modeli osób, wybierz strzałkę obok nazwy modelu osoby. Lista rozwijana zawiera wszystkie osoby należące do danego modelu osoby. Jeśli wybierzesz przycisk menu listy obok każdej osoby, zobaczysz opcje Zarządzanie, zmiana nazwy i usuwanie.  

![Zrzut ekranu przedstawia menu kontekstowe z opcjami, które umożliwiają zarządzanie, zmiana nazwy i usuwanie.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Zmiana nazwy osoby

1. Aby zmienić nazwę osoby w modelu osoby, wybierz przycisk menu listy i wybierz polecenie **Zmień nazwę** z menu listy.
1. Wybierz bieżącą nazwę osoby i wpisz nową nazwę.
1. Wybierz przycisk Sprawdź, a nazwa osoby zostanie zmieniona.

### <a name="delete-a-person"></a>Usuwanie osoby

1. Aby usunąć osobę z modelu osoby, wybierz przycisk menu listy i wybierz polecenie **Usuń** z menu listy.
1. W oknie podręcznym zostanie wyświetlona informacja, że ta akcja spowoduje usunięcie osoby i nie można cofnąć tej akcji.
1. Wybierz pozycję **Usuń** ponownie. spowoduje to usunięcie osoby z modelu osoby.

### <a name="manage-a-person"></a>Zarządzanie osobą

Jeśli wybierzesz pozycję **Zarządzaj**, zobaczysz okno **szczegóły osoby** ze wszystkimi powierzchniami, z których jest szkolony ten model osoby. Te powierzchnie pochodzą z wystąpień tej osoby w wideo korzystających z tego modelu osoby lub z obrazów, które zostały ręcznie przekazane.

> [!TIP]
> Aby przejść do okna **szczegóły osoby** , kliknij nazwę osoby lub kliknij pozycję **Zarządzaj**, jak pokazano powyżej.

#### <a name="add-a-face"></a>Dodawanie kroju

Możesz dodać więcej twarzy do osoby, wybierając pozycję **Dodaj obrazy**.

#### <a name="delete-a-face"></a>Usuwanie kroju

Wybierz obraz, który chcesz usunąć, a następnie kliknij przycisk **Usuń**.

#### <a name="rename-and-delete-the-person"></a>Zmiana nazwy i usuwanie osoby 

Możesz użyć okienka zarządzanie, aby zmienić nazwę osoby i usunąć osobę z modelu osoby.

## <a name="use-a-person-model-to-index-a-video"></a>Używanie modelu osoby do indeksowania wideo

Za pomocą modelu osoby można indeksować nowe wideo, przypisując modelowi osoby podczas przekazywania wideo.

Aby skorzystać z modelu osoby na nowym filmie wideo, wykonaj następujące czynności:

1. Wybierz przycisk **Przekaż** po prawej stronie.
1. Porzuć plik wideo lub Przeglądaj w poszukiwaniu pliku.
1. Wybierz strzałkę **Opcje zaawansowane** .
1. Wybierz listę rozwijaną i wybierz utworzony przez siebie model osoby.
1. Wybierz opcję **przekazywania** w dolnej części strony, a nowe wideo zostanie indeksowane przy użyciu modelu osoby.

Jeśli model osoby nie zostanie określony podczas przekazywania, Video Indexer będzie indeksować wideo przy użyciu domyślnego modelu osoby na Twoim koncie.

## <a name="use-a-person-model-to-reindex-a-video"></a>Używanie modelu osoby do ponownego indeksowania wideo

Aby użyć modelu osoby do ponownego indeksowania wideo w kolekcji, przejdź do swoich filmów wideo na stronie głównej Video Indexer i umieść kursor nad nazwą filmu wideo, który chcesz ponownie zindeksować.

Zobaczysz opcje umożliwiające edytowanie, usuwanie i ponowne indeksowanie wideo.

1. Wybierz opcję ponownego indeksowania wideo.

    ![Zrzut ekranu przedstawia wideo z konta i opcję ponownego indeksowania wideo.](./media/customize-face-model/reindex.png)

    Teraz możesz wybrać model osoby, aby ponownie zindeksować wideo za pomocą programu.
1. Wybierz listę rozwijaną i wybierz model osoby, którego chcesz użyć.
1. Wybierz przycisk ponownie **Indeksuj** , a Twoje wideo zostanie ponownie indeksowane przy użyciu modelu osoby.

Wszelkie nowe zmiany wprowadzane do twarzy i rozpoznawane przez Ciebie w filmie wideo zostaną zapisane w modelu osoby, który został użyty do ponownego indeksowania wideo.

## <a name="managing-people-in-your-videos"></a>Zarządzanie osobami w wideo

Możesz zarządzać wykrytymi powierzchniami i osobami, które są rozpoznawane w filmach wideo, które są indeksowane przez edytowanie i usuwanie twarzy.

Usunięcie kroju usuwa konkretną miarę z wglądu w dane wideo.

Edytowanie kroju zmienia nazwę elementu, który został wykryty i prawdopodobnie rozpoznany w filmie wideo. Gdy edytujesz miarę w filmie wideo, ta nazwa jest zapisywana jako wpis osoby w modelu osoby, który został przypisany do filmu wideo podczas przekazywania i indeksowania.

Jeśli model osoby nie zostanie przypisany do wideo podczas przekazywania, Edycja zostanie zapisana na domyślnym modelu osoby Twojego konta.

### <a name="edit-a-face"></a>Edytowanie kroju

> [!NOTE]
> Jeśli model osoby ma dwie lub więcej różnych osób o tej samej nazwie, nie będzie można oznaczyć tej nazwy w klipach wideo korzystających z tego modelu osoby. Można wprowadzać zmiany tylko dla osób, które współużytkują tę nazwę, na karcie osoby na stronie dostosowywania modelu zawartości w Video Indexer. Z tego powodu zaleca się nadawanie unikatowych nazw każdej osobie w modelu osoby.

1. Przejdź do witryny internetowej Video Indexer i zaloguj się.
1. Wyszukaj wideo, które chcesz wyświetlić i edytować na swoim koncie.
1. Aby edytować swoją część wideo, przejdź do karty Insights (szczegółowe informacje) i wybierz ikonę ołówka w prawym górnym rogu okna.

    ![Zrzut ekranu przedstawia wideo z nieznaną stroną do wybrania.](./media/customize-face-model/edit-face.png)

1. Wybierz dowolne wykryte twarze i zmień ich nazwy z "nieznane #X" (lub nazwę, która została wcześniej przypisana do twarzy).
1. Po wpisaniu nowej nazwy, zaznacz ikonę wyboru obok nowej nazwy. Ta akcja zapisuje nową nazwę i rozpoznaje i nazywa wszystkie wystąpienia tej możliwości w innych bieżących klipach wideo i w przyszłych przeszukiwanych klipach wideo. Rozpoznawanie twarz w innych bieżących filmach wideo może zająć trochę czasu, ponieważ jest to proces wsadowy.

Jeśli nazwa użytkownika jest nazwą istniejącej osoby w modelu osoby, z której korzysta wideo, wykryte obrazy kroju z tego filmu wideo tej osoby zostaną scalone z tym, co już istnieje w modelu. W przypadku wybrania nazwy kroju z nową nazwą zostanie utworzony nowy wpis osoby w modelu osoby, którego używa wideo.

### <a name="delete-a-face"></a>Usuwanie kroju

Aby usunąć wykryte możliwości w filmie wideo, przejdź do okienka szczegółowe informacje i wybierz ikonę ołówka w prawym górnym rogu okienka. Wybierz opcję **Usuń** pod nazwą kroju. Ta akcja spowoduje usunięcie wykrytej reakcji z filmu wideo. Powierzchnie osoby będą nadal wykrywane w innych filmach wideo, w których pojawiają się, ale można je również usunąć z tych filmów.

Osoba, jeśli miała nazwę, również nadal istnieje w modelu osoby, który został użyty do indeksowania filmu wideo, z którego została usunięta, chyba że użytkownik usunie osobę z modelu osoby.

## <a name="next-steps"></a>Następne kroki

[Dostosuj model osoby przy użyciu interfejsów API](customize-person-model-with-api.md)
