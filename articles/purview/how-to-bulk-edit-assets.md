---
title: Jak oznakować wiele terminów słownika do listy wybranych zasobów
description: Dowiedz się więcej na temat zbiorczej edycji zasobów na platformie Azure kontrolą.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: c42a6894c33993dc9aee5a9fdd10b1c3a3627320
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97372149"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>Jak zbiorczo edytować elementy zawartości do słownika tagów

W tym artykule opisano, jak oznaczyć wiele terminów słownika do listy wybranych zasobów w ramach jednej akcji.

### <a name="add-assets-to-view-selected-list-using-search"></a>Dodaj zasoby, aby wyświetlić wybrane listy przy użyciu wyszukiwania

1. Wyszukaj zasób danych, który chcesz dodać do listy na potrzeby edycji zbiorczej.

2. Na stronie wynik wyszukiwania Umieść wskaźnik myszy na elemencie zawartości, który chcesz dodać do **wybranej** listy edycji zbiorczej, aby wyświetlić pole wyboru.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Zrzut ekranu przedstawiający pole wyboru.":::

3. Zaznacz pole wyboru, aby dodać je do wybranej listy **Widok** edycji zbiorczej. Po dodaniu zobaczysz ikonę wybrane elementy w dolnej części strony.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Zrzut ekranu przedstawiający listę.":::

4. Powtórz powyższe kroki, aby dodać do listy wszystkie zasoby danych.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Dodaj zasoby, aby wyświetlić wybraną listę z strony szczegółów zasobu

1. Na stronie Szczegóły zasobu zaznacz pole wyboru w prawym górnym rogu, aby dodać element zawartości do **wybranej listy widok** edycji zbiorczej.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Zrzut ekranu przedstawiający element zawartości.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Edytuj zbiorczo zasoby na liście widok, aby dodać, zamienić lub usunąć warunki słownika.

1. Jeden z nich jest używany do identyfikacji wszystkich zasobów danych, które muszą być edytowane zbiorczo, wybierz opcję **Wyświetl wybraną** listę na stronie wyników wyszukiwania lub stronie Szczegóły zasobu.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Zrzut ekranu przedstawiający widok.":::

2. Przejrzyj listę i wybierz pozycję **Usuń** , jeśli chcesz usunąć wszystkie warunki.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Zrzut ekranu przedstawiający usunięcie.":::

3. Wybierz pozycję Edytuj zbiorczo, aby dodać, usunąć lub zamienić warunki słownika dla wszystkich wybranych zasobów.

4. Aby dodać terminy słownika, wybierz operację jako **dodanie**. Zaznacz wszystkie terminy słownika, które chcesz dodać do nowej wartości. Wybierz pozycję Zastosuj po zakończeniu.
    - Operacja dodawania spowoduje dołączenie nowej wartości do listy terminów słownika, które są już otagowane do zasobów danych.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Zrzut ekranu przedstawiający dodanie.":::

5. Aby zamienić warunki słownika, wybierz operację jako **Zastąp**. Zaznacz wszystkie terminy słownika, które chcesz zastąpić w nowej wartości. Wybierz pozycję Zastosuj po zakończeniu.
    - Operacja Replace spowoduje zastąpienie wszystkich terminów słownika dla wybranych zasobów danych przez warunki wybrane w obszarze Nowa wartość.
   
6. Aby usunąć warunki słownika, wybierz operację jako **usuniętą**. Wybierz pozycję Zastosuj po zakończeniu.
    - Operacja usuwania spowoduje usunięcie wszystkich terminów słownika dla wybranych zasobów danych.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Zrzut ekranu przedstawiający warunki usuwania.":::

7. Po zakończeniu zamknij blok edycji zbiorczej, wybierając pozycję **Zamknij** lub **Usuń wszystkie i Zamknij**. Wartość Zamknij nie spowoduje usunięcia wybranych zasobów. usunięcie wszystkich i zamknięcie spowoduje usunięcie wszystkich wybranych zasobów.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Zrzut ekranu przedstawiający zamknięcie.":::

   > [!Important]
   > Zalecana liczba zasobów do edycji zbiorczej to 15. Wybranie więcej niż 15 może spowodować problemy z wydajnością.
   > **Wybrane pole widok** będzie widoczne tylko wtedy, gdy wybrano co najmniej jeden zasób.


Postępuj zgodnie z [samouczkiem: Utwórz i zaimportuj terminologię słownika](how-to-create-import-export-glossary.md) , aby dowiedzieć się więcej.
