---
title: Dostosowywanie modelu marek przy użyciu witryny sieci Web Video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model marek przy użyciu witryny sieci Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586123"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Dostosowywanie modelu marek przy użyciu witryny sieci Web Video Indexer

Video Indexer obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowanych przez bazę danych marek usługi Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli zostanie wyświetlona w tekście wizualnym w filmie wideo, Video Indexer wykrywa ją jako markę zawartości.

Niestandardowy model marek umożliwia:

- Wybierz, jeśli chcesz, aby Video Indexer wykrywać marki z bazy danych marek Bing.
- Wybierz, jeśli chcesz, aby Video Indexer wykluczać Niektóre marki z wykrycia (głównie przy tworzeniu listy Odmów marek).
- Wybierz, jeśli chcesz, aby Video Indexer uwzględniać marki, które powinny być częścią modelu, który nie należy do bazy danych marek usługi Bing (głównie w przypadku tworzenia listy akceptowanych marek).

Aby zapoznać się z szczegółowym omówieniem, zobacz to [Omówienie](customize-brands-model-overview.md).

Za pomocą witryny sieci Web Video Indexer można tworzyć, używać i edytować niestandardowe modele marek wykryte w filmie wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md).

> [!NOTE]
> Jeśli Twoje wideo zostało zindeksowane przed dodaniem marki, należy je ponownie zindeksować. Element **ponownie Indeksuj** zostanie znaleziony w menu rozwijanym skojarzonym z wideo. Wybierz   ->  **kategorię marki** opcje zaawansowane i sprawdź **wszystkie marki**.

## <a name="edit-brands-model-settings"></a>Edytuj ustawienia modeli marek

Możesz określić, czy chcesz wykryć marki z bazy danych marek Bing. Aby ustawić tę opcję, należy edytować ustawienia modelu marki. Wykonaj następujące kroki:

1. Przejdź do witryny sieci Web [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Aby dostosować model na koncie, wybierz przycisk **Dostosowywanie modelu zawartości** po lewej stronie.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Dostosuj model zawartości w Video Indexer":::
1. Aby edytować marki, wybierz kartę **marki** .

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="Zrzut ekranu przedstawia kartę marki okna dialogowego Dostosowywanie modelu zawartości":::
1. Zaznacz opcję **Pokaż marki sugerowane przez Bing** , jeśli chcesz, aby Video Indexer wykrywać marki sugerowane przez usługę Bing, pozostawiając opcję niezaznaczone, jeśli nie.

## <a name="include-brands-in-the-model"></a>Uwzględnij marki w modelu

Sekcja **include marek** reprezentuje znaki niestandardowe, które mają być wykrywane Video Indexer, nawet jeśli nie są sugerowane przez usługę Bing.  

### <a name="add-a-brand-to-include-list"></a>Dodaj markę do listy dołączania

1. Wybierz pozycję **+ Utwórz nową markę**.

    Podaj nazwę (wymagane), kategorię (opcjonalnie), opis (opcjonalnie) i adres URL odwołania (opcjonalnie).
    Pole kategoria ma pomóc w oznaczeniu marki. To pole jest wyświetlane jako *Tagi* marki przy użyciu interfejsów API Video Indexer. Na przykład znak "Azure" może być oznaczony jako "Chmura" lub skategoryzowany.

    Pole adres URL odwołania może być dowolną witryną referencyjną dla marki (na przykład łączem do strony Wikipedia).

2. Wybierz pozycję **Zapisz** , aby zobaczyć, że marka została dodana do listy **Dołącz marki** .

### <a name="edit-a-brand-on-the-include-list"></a>Edytuj markę na liście dołączania

1. Wybierz ikonę ołówka obok znaku, który chcesz edytować.

    Możesz zaktualizować kategorię, opis lub adres URL odwołania marki. Nie można zmienić nazwy marki, ponieważ nazwy marek są unikatowe. Jeśli musisz zmienić nazwę marki, Usuń całą markę (zobacz następną sekcję) i Utwórz nową markę z nową nazwą.

2. Wybierz przycisk **Aktualizuj** , aby zaktualizować markę o nowe informacje.

### <a name="delete-a-brand-on-the-include-list"></a>Usuń markę z listy dołączania

1. Wybierz ikonę kosza obok znaku, który chcesz usunąć.
2. Wybierz pozycję **Usuń** , a marka nie będzie już widoczna na liście *Uwzględnij marki* .

## <a name="exclude-brands-from-the-model"></a>Wyklucz marki z modelu

Sekcja **exclude marek** reprezentuje marki, które nie mają być wykrywane przez Video Indexer.

### <a name="add-a-brand-to-exclude-list"></a>Dodaj markę do listy wykluczeń

1. Wybierz pozycję **+ Utwórz nową markę.**

    Podaj nazwę (wymagane), kategorię (opcjonalnie).

2. Wybierz pozycję **Zapisz** , aby zobaczyć, że marka została dodana do listy *wykluczone marki* .

### <a name="edit-a-brand-on-the-exclude-list"></a>Edytuj markę na liście wykluczeń

1. Wybierz ikonę ołówka obok znaku, który chcesz edytować.

    Można aktualizować tylko kategorię marki. Nie można zmienić nazwy marki, ponieważ nazwy marek są unikatowe. Jeśli musisz zmienić nazwę marki, Usuń całą markę (zobacz następną sekcję) i Utwórz nową markę z nową nazwą.

2. Wybierz przycisk **Aktualizuj** , aby zaktualizować markę o nowe informacje.

### <a name="delete-a-brand-on-the-exclude-list"></a>Usuń markę z listy wykluczeń

1. Wybierz ikonę kosza obok znaku, który chcesz usunąć.
2. Wybierz pozycję **Usuń** , a marka nie będzie już widoczna na liście *wykluczone marki* .

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md)
