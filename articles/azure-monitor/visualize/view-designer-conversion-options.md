---
title: Opcje konwersji Azure Monitor View na skoroszyty
description: Opcje konwersji dotyczące przejścia z widoków do skoroszytów w Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b8b6b8b41c729c3cbb6cf4589d679e93149e5314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043409"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Opcje konwersji Azure Monitor View na skoroszyty
[Projektant widoków](view-designer.md) jest funkcją Azure monitor, która umożliwia tworzenie niestandardowych widoków, które ułatwiają wizualizację danych w obszarze roboczym log Analytics, z wykresami, listami i osiami czasu. Są one wycofywane i zastępowane skoroszytami, które zapewniają dodatkową funkcjonalność. W tym artykule porównano podstawowe koncepcje między dwoma i opcjami konwersji widoków na skoroszyty.

## <a name="basic-workbook-designs"></a>Podstawowe projekty skoroszytu

Projektant widoków ma stały statyczny styl reprezentacji, podczas gdy skoroszyty umożliwiają swobodne dołączanie i modyfikowanie sposobu reprezentowania danych. Poniższe obrazy przedstawiają dwa przykłady, w których można rozmieścić skoroszyty podczas konwertowania widoków.

[](view-designer-conversion-examples.md#vertical) 
 Skoroszyt ![ pionowy Pionow](media/view-designer-conversion-options/view-designer-vertical.png)

[](view-designer-conversion-examples.md#tabbed) 
 Skoroszyt ![ z kartami Dane karty rozkładu ](media/view-designer-conversion-options/distribution-tab.png)
 ![ typów danych w karcie czas](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Konwersja kafelka
Projektant widoków używa funkcji kafelka przeglądu do reprezentowania i podsumowywania ogólnego stanu. Są one reprezentowane w siedmiu kafelkach, od liczb do wykresów. W skoroszytach użytkownicy mogą tworzyć podobne wizualizacje i przypinać je do oryginalnego stylu kafelków przegląd. 

![Galeria](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Wyświetl konwersję pulpitu nawigacyjnego
Kafelki projektanta widoków zwykle składają się z dwóch sekcji, wizualizacji i listy, które pasują do danych z wizualizacji, na przykład kafelka **listy & pierścieni** .

![Pierścień](media/view-designer-conversion-options/donut-example.png)

Za pomocą skoroszytów zezwalasz użytkownikowi na wybór kwerendy co najmniej jednej sekcji widoku. Opracowywanie zapytań w skoroszytach jest prostym procesem dwuetapowym. Najpierw dane są generowane na podstawie zapytania, a drugie, dane są renderowane jako Wizualizacja.  Przykład sposobu odtworzenia tego widoku w skoroszytach jest następujący:

![Convert](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Następne kroki
- [Uzyskiwanie dostępu do skoroszytów & uprawnień](view-designer-conversion-access.md)