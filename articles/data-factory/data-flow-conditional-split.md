---
title: Przekształcenie podziału warunkowego w mapowaniu przepływu danych
description: Dzielenie danych na różne strumienie przy użyciu transformacji podziału warunkowego w przepływie danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606484"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Przekształcenie podziału warunkowego w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja podziału warunkowego przekierowuje wiersze danych do różnych strumieni na podstawie pasujących warunków. Przekształcenie podziału warunkowego jest podobne do struktury decyzji CASE w języku programowania. Transformacja oblicza wyrażenia, a na podstawie wyników kieruje wiersz danych do określonego strumienia.

## <a name="configuration"></a>Konfiguracja

Ustawienie **Split on** określa, czy wiersz danych jest przepływem do pierwszego zgodnego strumienia lub każdego strumienia, do którego jest zgodny.

Użyj konstruktora wyrażeń przepływu danych, aby wprowadzić wyrażenie dla warunku podziału. Aby dodać nowy warunek, kliknij ikonę znaku plus w istniejącym wierszu. Domyślny strumień można również dodać do wierszy, które nie pasują do żadnego warunku.

![podział warunkowy](media/data-flow/conditionalsplit1.png "Opcje podziału warunkowego")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Przykład

Poniższy przykład to transformacja podziału warunkowego o nazwie `SplitByYear` , która przyjmuje strumień `CleanData`przychodzący. Ta transformacja ma dwa warunki `year < 1960` podziału i `year > 1980`. `disjoint`ma wartość false, ponieważ dane przechodzą do pierwszego warunku dopasowywania. Każdy wiersz pasujący do pierwszego warunku przechodzi do `moviesBefore1960`strumienia wyjściowego. Wszystkie pozostałe wiersze zgodne z drugim warunkiem przechodzą do `moviesAFter1980`strumienia wyjściowego. Wszystkie inne wiersze przepływają przez domyślny `AllOtherMovies`strumień.

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![podział warunkowy](media/data-flow/conditionalsplit1.png "Opcje podziału warunkowego")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Następne kroki

Typowe przekształcenia przepływu danych używane z podziałem warunkowym to [transformacja sprzężenia](data-flow-join.md), [transformacja wyszukiwania](data-flow-lookup.md)i [wybór transformacji](data-flow-select.md) .
