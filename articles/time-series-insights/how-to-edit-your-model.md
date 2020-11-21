---
title: Modelowanie danych w środowiskach Gen2 — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej na temat modelowania danych w Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 89efc1d4f34b250d211f9fd7492588bd2896eb6e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016857"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Modelowanie danych w Azure Time Series Insights Gen2

W tym artykule opisano sposób pracy z modelem szeregów czasowych w Azure Time Series Insights Gen2. Zawiera szczegółowe informacje o kilku typowych scenariuszach dotyczących danych.

> [!TIP]
>
> * Przeczytaj więcej na temat [modelu szeregów czasowych](concepts-model-overview.md).
> * Dowiedz się więcej na temat nawigowania w [eksploratorze Azure Time Series Insights Gen2](./concepts-ux-panels.md).

## <a name="instances"></a>Wystąpienia

Eksplorator Azure Time Series Insights obsługuje operacje **tworzenia**, **odczytywania**, **aktualizowania** i **usuwania** wystąpień w przeglądarce.

Aby rozpocząć, wybierz widok **modelu** w widoku **analizy** Azure Time Series Insights Explorer.

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektora modelu szeregów czasowych, a następnie wybierz pozycję **wystąpienia** z menu. Zostaną wyświetlone wszystkie wystąpienia skojarzone z wybranym środowiskiem Azure Time Series Insights.

    [![Utwórz jedno wystąpienie, wybierając najpierw wystąpienia.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![Dodaj wystąpienie, wybierając przycisk + Dodaj.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Wprowadź szczegóły wystąpienia, wybierz pozycję typ i skojarzenie hierarchii, a następnie wybierz pozycję **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Przekaż zbiorczo co najmniej jedno wystąpienie

> [!TIP]
> Twoje wystąpienia można zapisać na pulpicie w formacie JSON. Pobrany plik JSON można następnie przekazać, wykonując poniższe kroki.

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień.

    [![Zbiorczo Przekaż wystąpienia za poorednictwem JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Wybierz pozycję **Przekaż**.

### <a name="edit-a-single-instance"></a>Edytuj pojedyncze wystąpienie

1. Zaznacz wystąpienie i wybierz ikonę **Edytuj** lub **ołówka**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytuj pojedyncze wystąpienie.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Usuwanie wystąpienia

1. Wybierz wystąpienie, a następnie wybierz ikonę **Usuń** lub **odlotowy pojemnik**.

   [![Usuń wystąpienie, wybierając pozycję Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

> [!NOTE]
> Wystąpienie musi pomyślnie przekazać sprawdzenie poprawności pola, które ma zostać usunięte.

## <a name="hierarchies"></a>Hierarchie

Eksplorator Azure Time Series Insights obsługuje operacje **tworzenia**, **odczytywania**, **aktualizowania** i **usuwania** hierarchii w przeglądarce.

Aby rozpocząć, wybierz widok **modelu** w widoku **analizy** Azure Time Series Insights Explorer.

### <a name="create-a-single-hierarchy"></a>Utwórz pojedynczą hierarchię

1. Przejdź do panelu selektora modelu szeregów czasowych, a następnie wybierz **hierarchie** z menu. Zostaną wyświetlone wszystkie hierarchie skojarzone z wybranym środowiskiem Azure Time Series Insights.

    [![Utwórz hierarchię za pomocą okienka.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![Przycisk hierarchia i Dodaj.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Wybierz pozycję **+ Dodaj poziom** w okienku po prawej stronie.

    [![Dodaj poziom do hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Wprowadź szczegóły hierarchii i wybierz pozycję **Zapisz**.

    [![Określ szczegóły hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Przekaż zbiorczo co najmniej jedną hierarchię

> [!TIP]
> Hierarchie można zapisać na pulpicie w formacie JSON. Pobrany plik JSON można następnie przekazać, wykonując poniższe kroki.

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Wybierz pozycję **Przekaż**.

    [![Zaznaczenia dotyczące zbiorczego przekazywania hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Edytowanie pojedynczej hierarchii

1. Wybierz hierarchię i wybierz ikonę **Edytuj** lub **ołówka**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Opcje edycji pojedynczej hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

1. Wybierz hierarchię, a następnie wybierz ikonę **Usuń** lub **odpady z pojemników**.

    [![Usuń hierarchię, wybierając przycisk Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

## <a name="types"></a>Types

Eksplorator Azure Time Series Insights obsługuje operacje **tworzenia**, **odczytu**, **aktualizacji** i **usuwania** w przeglądarce.

Aby rozpocząć, wybierz widok **modelu** w widoku **analizy** Azure Time Series Insights Explorer.

### <a name="create-a-single-type"></a>Tworzenie pojedynczego typu

1. Przejdź do panelu selektora modelu szeregów czasowych, a następnie wybierz pozycję **typy** z menu. Zostaną wyświetlone wszystkie typy skojarzone z wybranym środowiskiem Azure Time Series Insights.

    [![Okienko typów modelu szeregów czasowych.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj** , aby wyświetlić modalne **Dodawanie nowego typu** .
1. Wprowadź właściwości i zmienne dla typu. Po wprowadzeniu wybierz pozycję **Zapisz**.

    [![Ustawienia konfiguracji umożliwiające dodanie typu.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Przekaż zbiorczo jeden lub więcej typów

> [!TIP]
> Możesz zapisać swoje typy na pulpicie w formacie JSON. Pobrany plik JSON można następnie przekazać, wykonując poniższe kroki.

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek typu.
1. Wybierz pozycję **Przekaż**.

    [![Typy zbiorcze przekazywania opcji.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Edytuj pojedynczy typ

1. Wybierz typ i wybierz ikonę **Edytuj** lub **ołówka**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytuj typ w okienku.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Usuń typ

1. Wybierz typ, a następnie wybierz ikonę **usuwanie** lub **odpady z pojemników**.

   [![Usuń typ, wybierając pozycję Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat modelu szeregów czasowych, przeczytaj artykuł [Modelowanie danych](./concepts-model-overview.md).

* Aby dowiedzieć się więcej na temat Gen2, Odczytaj [dane wizualizacji w eksploratorze Azure Time Series Insights Gen2](./concepts-ux-panels.md).

* Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, Odczytaj [obsługiwane kształty JSON](./time-series-insights-send-events.md#supported-json-shapes).