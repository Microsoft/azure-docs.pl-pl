---
title: Zarządzanie szablonami terminów dla słownika biznesowego
description: Dowiedz się, jak zarządzać szablonami terminów dla słownika biznesowego w usłudze Azure kontrolą Data Catalog.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555336"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Zarządzanie szablonami terminów dla słownika biznesowego

Usługa Azure kontrolą umożliwia utworzenie słownika warunków, które są ważne w przypadku wzbogacania danych. Każdy nowy termin dodany do kontrolą Data Catalog słownik jest oparty na szablonie terminu, który określa pola dla danego terminu. W tym artykule opisano sposób tworzenia szablonu terminu i atrybutów niestandardowych, które można kojarzyć z warunkami słownika.

## <a name="manage-term-templates-and-custom-attributes"></a>Zarządzanie szablonami terminów i atrybutami niestandardowymi

Za pomocą szablonów terminów można tworzyć niestandardowe atrybuty, grupować je razem i stosować szablon podczas tworzenia warunków. Po utworzeniu terminu nie można zmienić szablonu skojarzonego z tym terminem.

1. Na stronie **warunki słownika** wybierz pozycję **Zarządzaj szablonami terminów**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Zrzut ekranu przedstawiający warunki słownika > Zarządzanie szablonami terminów.":::

2. Na stronie są wyświetlane zarówno atrybuty systemowe, jak i niestandardowe. Wybierz kartę **niestandardową** , aby utworzyć lub edytować szablony terminów.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Zrzut ekranu przedstawiający terminologię > stronie Zarządzanie szablonami terminów.":::

3. Wybierz pozycję **+ nowy szablon terminu** i wprowadź nazwę i opis szablonu.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Zrzut ekranu przedstawiający warunki słownika > Zarządzanie szablonami terminów > nowych szablonów terminów":::

4. Wybierz pozycję **+ Nowy atrybut** , aby utworzyć nowy atrybut niestandardowy dla szablonu terminu. Wprowadź nazwę atrybutu, opis. Nazwa atrybutu niestandardowego musi być unikatowa w ramach szablonu terminu, ale może być używana ponownie w różnych szablonach.

   Wybierz typ pola z listy opcji **tekst**, **pojedynczy wybór**, wybór **wielokrotny** lub  **Data**. Możesz również podać domyślny ciąg wartości dla typów pól tekstowych.  Atrybut może być również oznaczony jako **wymagany**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Zrzut ekranu przedstawiający warunki słownika > nowej strony atrybutu.":::

5. Po utworzeniu wszystkich atrybutów niestandardowych wybierz pozycję **Podgląd** , aby rozmieścić sekwencję atrybutów niestandardowych. Możesz przeciągać i upuszczać atrybuty niestandardowe w żądanej kolejności.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Zrzut ekranu przedstawiający warunki słownika > szablonu terminu wersji zapoznawczej.":::

6. Po zdefiniowaniu wszystkich atrybutów niestandardowych wybierz pozycję **Utwórz** , aby utworzyć szablon terminu z atrybutami niestandardowymi.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Zrzut ekranu przedstawiający terminologię > nowy termin szablonu — przycisk Utwórz.":::

7. Istniejące atrybuty niestandardowe mogą być oznaczone jako wygasłe, sprawdzając **znacznik jako wygasły**. Po wygaśnięciu nie można ponownie aktywować atrybutu. Wygasły atrybut będzie wyszarzony dla istniejących warunków. Przyszłe nowe terminy utworzone przy użyciu tego szablonu terminu nie będą już pokazywały atrybutu, który został oznaczony jako wygasły.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Zrzut ekranu przedstawiający warunki słownika > Edytuj atrybut, aby oznaczyć go jako wygasły.":::

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczkiem: Utwórz i zaimportuj terminologię słownika](tutorial-import-create-glossary-terms.md) , aby dowiedzieć się więcej.
