---
title: 'Samouczek: Eksplorowanie zestawów zasobów, szczegółów, schematów i klasyfikacji w usłudze Azure kontrolą (wersja zapoznawcza)'
description: W tym samouczku opisano sposób korzystania z zestawów zasobów, szczegółów zasobów, schematów i klasyfikacji.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c39ed746630c646b0ce305a9535ae6d610f5161c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555733"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Samouczek: Eksplorowanie zestawów zasobów, szczegółów, schematów i klasyfikacji w usłudze Azure kontrolą (wersja zapoznawcza)

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

W tym samouczku przedstawiono najważniejsze składniki wykazu: zestawy zasobów, szczegóły zasobów, schematy i klasyfikacje.

Ten samouczek jest *częścią 4 wieloczęściowej serii samouczków* , w której przedstawiono podstawowe informacje na temat zarządzania nadzorem danych w ramach obszaru danych przy użyciu usługi Azure kontrolą. W tym samouczku przedstawiono pracę wykonaną w [części 3: przeglądanie zasobów w usłudze Azure kontrolą (wersja zapoznawcza) i przeglądanie ich elementów](tutorial-browse-and-view-lineage.md)zależnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wyświetlanie zestawów zasobów.
> * Wyświetl szczegóły zasobu.
> * Edytuj schemat i Dodaj klasyfikacje.

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletny [Samouczek: przeglądanie zasobów w usłudze Azure kontrolą (wersja zapoznawcza) i przeglądanie ich elementów ich](tutorial-browse-and-view-lineage.md)pochodzenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="view-resource-sets"></a>Wyświetlanie zestawów zasobów

Zestaw zasobów jest pojedynczym obiektem w katalogu, który reprezentuje wiele obiektów fizycznych w magazynie. Obiekty zwykle udostępniają wspólny schemat i, w większości przypadków, konwencji nazewnictwa lub struktury folderów. Na przykład format daty to *rrrr/mm/dd*. Aby uzyskać więcej informacji na temat zestawów zasobów, zobacz [Omówienie zestawów zasobów](concept-resource-sets.md).

1. Przejdź do zasobu usługi Azure kontrolą w Azure Portal i wybierz pozycję **Otwórz kontrolą Studio**. Nastąpi automatyczne przekierowanie do strony głównej programu kontrolą Studio.

2. Wprowadź **contoso_staging_positivecashflow_ n** w polu **Wyszukaj zasoby** , a następnie wybierz pozycję **Contoso_Staging_PositiveCashFlow_ {n}. csv** z wyników wyszukiwania.

## <a name="view-asset-details"></a>Wyświetl szczegóły zasobu

1. Na karcie **Przegląd** jest wyświetlany **Opis**, **terminologia** i **Właściwości**, takie jak **kwalifikowana**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Zrzut ekranu przedstawiający kartę Przegląd na stronie zasobu zestawu zasobów.":::

1. W obszarze **Właściwości** należy zwrócić uwagę na następujące dwa pola:

   * **partitionCount**: wskazuje liczbę plików fizycznych skojarzonych z tym zestawem zasobów.
   * **schemaCount**: wskazuje liczbę odmian schematu znalezionych w ramach tego zestawu zasobów.

   Usługa Azure kontrolą wypełnia te właściwości w ciągu 24 godzin po zakończeniu skanowania w [części 1 tej serii samouczków](tutorial-scan-data.md).

1. Wybierz kartę **kontakty** , aby przejrzeć wartości **ekspertów** i **właściciele** .

## <a name="edit-the-schema-and-add-classifications"></a>Edytuj schemat i Dodaj klasyfikacje

1. Wybierz kartę **schemat** . Zwróć uwagę na nazwy kolumn i klasyfikacje, które są z nimi skojarzone. Należy zauważyć, że skanowanie automatycznie wypełnia właściwości.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Zrzut ekranu przedstawiający kartę schemat.":::

1. Aby edytować element zawartości, wybierz przycisk **Edytuj** w lewym górnym rogu. Następnie wybierz kartę **schemat** .

1. Dodaj **Opis** kolumny lub Zmień nazwę kolumny na bardziej przyjazną nazwę.

1. Dodaj klasyfikację na poziomie zasobu, wybierając listę rozwijaną **klasyfikacji na poziomie kolumny** dla nazwy kolumny, która nie ma ustawionej klasyfikacji.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Strona edytowania schematu":::

1. Po zakończeniu wprowadzania zmian wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wyświetlanie zestawów zasobów.
> * Wyświetl szczegóły zasobu.
> * Edytuj schemat i Dodaj klasyfikacje.

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat słownika oraz jak definiować i importować nowe warunki dla zasobów.

> [!div class="nextstepaction"]
> [Tworzenie i importowanie terminów słownika](tutorial-import-create-glossary-terms.md)