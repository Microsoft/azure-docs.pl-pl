---
title: Zastosuj klasyfikacje do zasobów (wersja zapoznawcza)
description: W tym dokumencie opisano sposób stosowania klasyfikacji do zasobów.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554868"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Stosowanie klasyfikacji zasobów na platformie Azure kontrolą

W tym artykule omówiono sposób stosowania klasyfikacji do zasobów.

## <a name="introduction"></a>Wprowadzenie

Klasyfikacje mogą być typami systemowymi lub niestandardowymi. Klasyfikacje systemu są domyślnie obecne w kontrolą. Klasyfikacje niestandardowe można tworzyć na podstawie wzorca wyrażenia regularnego. Klasyfikacje mogą być stosowane do zasobów automatycznie lub ręcznie.

W tym dokumencie wyjaśniono, jak stosować klasyfikacje do danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Twórz niestandardowe klasyfikacje w zależności od potrzeb.
- Skonfiguruj skanowanie źródeł danych.

## <a name="apply-classifications"></a>Stosowanie klasyfikacji
W usłudze Azure kontrolą można stosować klasyfikacje systemowe lub niestandardowe dla pliku, tabeli lub kolumny. W tym artykule opisano kroki ręcznego stosowania klasyfikacji do zasobów.

### <a name="apply-classification-to-a-file-asset"></a>Zastosuj klasyfikację do zasobu pliku
Usługa Azure kontrolą umożliwia skanowanie i automatyczne klasyfikowanie plików dokumentacji. Na przykład jeśli masz plik o nazwie *multiple.docx* i ma on numer identyfikacyjny krajowej w swojej zawartości, usługa Azure kontrolą dodaje **Narodowy numer identyfikacyjny UE** do strony szczegółów zasobu pliku.

W niektórych scenariuszach warto ręcznie dodać klasyfikacje do zasobów plików. Jeśli masz wiele plików pogrupowanych w zestaw zasobów, Dodaj klasyfikacje na poziomie zestawu zasobów.

Wykonaj następujące kroki, aby dodać klasyfikację niestandardową lub systemową do zestawu zasobów partycji:

1. Wyszukaj lub Przeglądaj partycję i przejdź do strony szczegółów zasobu.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Zrzut ekranu przedstawiający stronę szczegółów zasobu.":::

1. Na karcie **Przegląd** sprawdź sekcję **klasyfikacje** , aby sprawdzić, czy istnieją jakieś klasyfikacje. Wybierz pozycję **Edytuj**.

1. Z listy rozwijanej **klasyfikacje** wybierz interesujące Cię klasyfikacje. Na przykład **numer karty kredytowej**, który jest klasyfikacją systemu i **CustomerAccountID**, która jest klasyfikacją niestandardową.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania klasyfikacji, które mają zostać dodane do elementu zawartości.":::

1. Wybierz pozycję **Zapisz**

1. Na karcie **Omówienie** upewnij się, że wybrane klasyfikacje są wyświetlane w sekcji **klasyfikacje** .

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Zrzut ekranu przedstawiający sposób potwierdzania klasyfikacji zostały dodane do elementu zawartości.":::

### <a name="apply-classification-to-a-table-asset"></a>Zastosuj klasyfikację do zasobu tabeli

Gdy usługa Azure kontrolą skanuje źródła danych, nie przydziela automatycznie klasyfikacji do zasobów tabeli. Jeśli chcesz, aby zasób tabeli miał klasyfikację, należy dodać ją ręcznie.

Aby dodać klasyfikację do zasobu tabeli:

1. Znajdź zasób tabeli, który Cię interesuje. Na przykład tabela **Customer** .

1. Upewnij się, że żadne klasyfikacje nie są przypisane do tabeli. Wybierz pozycję **Edytuj**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania i edytowania klasyfikacji zasobu tabeli.":::

1. Z listy rozwijanej **klasyfikacje** wybierz co najmniej jedną klasyfikację. W tym przykładzie zastosowano klasyfikację niestandardową o nazwie **CustomerInfo**, ale można wybrać dowolne klasyfikacje dla tego kroku.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania klasyfikacji do dodania do zasobu tabeli.":::

1. Wybierz pozycję **Zapisz** , aby zapisać klasyfikacje.

1. Na stronie **Przegląd** Sprawdź, czy usługa Azure kontrolą dodała nowe klasyfikacje.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Zrzut ekranu przedstawiający sposób sprawdzenia, czy klasyfikacje zostały dodane do zasobu tabeli.":::

### <a name="add-classification-to-a-column-asset"></a>Dodaj klasyfikację do zasobu kolumny

Usługa Azure kontrolą automatycznie skanuje i dodaje klasyfikacje do wszystkich zasobów kolumn. Jeśli jednak chcesz zmienić klasyfikację, możesz to zrobić na poziomie kolumny.

Aby dodać klasyfikację do kolumny:

1. Znajdź i wybierz pozycję zasób kolumny, a następnie wybierz pozycję **Edytuj** na karcie **Przegląd** .

1. Wybierz kartę **schemat**

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Zrzut ekranu pokazujący, jak edytować schemat kolumny.":::

1. Zidentyfikuj interesujące Cię kolumny i wybierz pozycję **Dodaj klasyfikację**. Ten przykład dodaje wspólną klasyfikację **haseł** do kolumny **PasswordHash** .

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania klasyfikacji do kolumny.":::

1. Wybierz pozycję **Zapisz**

1. Wybierz kartę **schemat** i sprawdź, czy klasyfikacja została dodana do kolumny.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Zrzut ekranu pokazujący, jak potwierdzić, że klasyfikacja została dodana do schematu kolumn.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Wpływ skanowania na istniejące klasyfikacje

Klasyfikacje są stosowane po raz pierwszy, na podstawie przykładowego sprawdzenia zestawu danych i dopasowania go względem wzorca wyrażenia regularnego. W momencie ponownego skanowania w przypadku zastosowania nowych klasyfikacji w kolumnie są pobierane dodatkowe klasyfikacje. Istniejące klasyfikacje pozostają w kolumnie i należy je usunąć ręcznie.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak utworzyć klasyfikację niestandardową, zobacz [Tworzenie klasyfikacji niestandardowej](create-a-custom-classification-and-classification-rule.md).