---
title: Tworzenie, importowanie i eksportowanie terminów słownika
description: Dowiedz się, jak tworzyć, importować i eksportować warunki słownika na platformie Azure kontrolą.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: a693761bcecab87e343014127ad37077c2569e21
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553546"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Tworzenie, importowanie i eksportowanie terminów słownika

W tym artykule opisano sposób tworzenia terminów słownika biznesowego w usłudze Azure kontrolą Data Catalog oraz importowania i eksportowania terminów słownika przy użyciu plików CSV.

## <a name="create-a-new-term"></a>Utwórz nowy termin

Aby utworzyć nowy termin słownika, wykonaj następujące czynności:

1. Wybierz ikonę słownika w lewym obszarze nawigacji na stronie głównej, aby przejść do strony Lista terminów.

2. Na stronie **warunki słownika** wybierz pozycję **+ nowy termin**. Zostanie otwarta strona z wybranym szablonem **domyślnym** . Wybierz szablon, w którym chcesz utworzyć słownik, i wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Zrzut ekranu przedstawiający tworzenie nowego terminu." border="true":::

3. Nadaj nowemu terminowi nazwę, która musi być unikatowa w wykazie. Nazwa terminu uwzględnia wielkość liter, co oznacza, że w wykazie może istnieć termin o nazwie **przykład** i **przykład** .

4. Dodaj **definicję**.

5. Ustaw **stan** terminu. Nowe warunki domyślne do stanu **wersji roboczej** .

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Zrzut ekranu przedstawiający Opcje stanu." border="true":::

   Te znaczniki stanu są metadanymi skojarzonymi z terminem. Obecnie można ustawić następujący stan dla każdego warunku:

   - **Wersja robocza**: ten termin nie jest jeszcze oficjalnie zaimplementowany.
   - **Zatwierdzone**: ten termin jest oficjalny/standardowy/zatwierdzony.
   - **Wygasłe**: ten termin nie powinien być już używany.
   - **Alert**: ten termin wymaga uwagi.

6. Dodaj **zasoby** i **akronim**. Jeśli termin jest częścią hierarchii, można dodać warunki nadrzędne w **obiekcie nadrzędnym** na karcie Przegląd.

7. Dodaj **synonimy** i **powiązane terminy** na karcie powiązanej.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Zrzut ekranu przedstawiający kartę pokrewną nowych warunków >." border="true":::

8. Opcjonalnie wybierz kartę **kontakty** , aby dodać ekspertów i Stewards do swojego terminu.

9. Wybierz pozycję **Utwórz** , aby utworzyć termin.

## <a name="import-terms-into-the-glossary"></a>Importuj terminy do słownika

Usługa Azure kontrolą Data Catalog zawiera plik Template. csv umożliwiający zaimportowanie warunków do słownika.

Warunki można importować w wykazie. Zduplikowane terminy w pliku zostaną nadpisywane.

Zauważ, że w nazwach terminów jest rozróżniana wielkość liter. Na przykład, `Sample` i `saMple` mogą znajdować się w tym samym słowniku.

### <a name="to-import-terms-follow-these-steps"></a>Aby zaimportować warunki, wykonaj następujące kroki

1. Gdy jesteś na stronie **warunki słownika** , wybierz pozycję **Importuj warunki**.

2. Zostanie otwarta strona szablon terminu. Dopasowuje szablon terminu do rodzaju. Wolumin CSV do zaimportowania.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Zrzut ekranu przedstawiający stronę warunki słownika, przycisk Importuj warunki.":::

3. Pobierz szablon CSV i użyj go, aby wprowadzić warunki, które chcesz dodać.

   > [!Important]
   > System obsługuje tylko importowanie kolumn, które są dostępne w szablonie. Szablon "domyślny system" będzie miał wszystkie atrybuty domyślne.
   > Jednak niestandardowe szablony terminów będą zawierać atrybuty Box i dodatkowe atrybuty niestandardowe zdefiniowane w szablonie. W związku z tym. Plik CSV różni się od łącznej liczby kolumn i nazw kolumn w zależności od wybranego szablonu terminu. Po przekazaniu pliku możesz również przejrzeć problemy.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Zrzut ekranu przedstawiający stronę warunki słownika wybierz plik do zaimportowania.":::

4. Po zakończeniu wypełniania pliku CSV wybierz plik do zaimportowania, a następnie wybierz przycisk **OK**.

5. System przekaże plik i doda wszystkie warunki do wykazu.

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Eksportuj terminy z słownika z atrybutami niestandardowymi

Należy mieć możliwość eksportowania terminów z słownika tak długo, jak wybrane warunki należą do tego samego szablonu terminu.

1. Gdy jesteś w słowniku, domyślnie przycisk **Eksportuj** jest wyłączony. Po wybraniu warunków do wyeksportowania przycisk **Eksportuj** jest aktywny, jeśli wybrane warunki należą do tego samego szablonu.

2. Wybierz pozycję **Eksportuj** , aby pobrać wybrane warunki.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczkiem: Utwórz i zaimportuj terminologię słownika](tutorial-import-create-glossary-terms.md) , aby dowiedzieć się więcej.
