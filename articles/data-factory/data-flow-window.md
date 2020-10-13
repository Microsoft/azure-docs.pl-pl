---
title: Przekształcanie okna w mapowaniu przepływu danych
description: Przekształcenie okna przepływu danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 0231fc8919444558abcbc965ad127f7372eceb66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823604"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Przekształcanie okna w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Przekształcenie okna polega na określeniu agregacji kolumn w strumieniach danych. W Konstruktorze wyrażeń można definiować różne typy agregacji oparte na danych lub czasie w systemie Windows (klauzula SQL OVER), takich jak ołów, LAG, NTILE, CUMEDIST, RANGa itp.). W danych wyjściowych zostaną wygenerowane nowe pole zawierające te agregacje. Można również dołączyć opcjonalne pola grupowe.

![Opcje okna](media/data-flow/windows1.png "System Windows 1")

## <a name="over"></a>Potok
Ustaw Partycjonowanie danych kolumn dla transformacji okna. Odpowiednikiem SQL jest ```Partition By``` w klauzuli over w SQL. Jeśli chcesz utworzyć obliczenia lub utworzyć wyrażenie, które ma być używane na potrzeby partycjonowania, możesz to zrobić, umieszczając kursor na nazwie kolumny i wybierając pozycję "kolumna obliczana".

![Opcje okna](media/data-flow/windows4.png "System Windows 4")

## <a name="sort"></a>Sortowanie
Inną częścią klauzuli over jest ustawienie ```Order By``` . Spowoduje to ustawienie kolejności sortowania danych. Można również utworzyć wyrażenie dla wartości obliczanej w tym polu kolumny w celu sortowania.

![Opcje okna](media/data-flow/windows5.png "System Windows 5")

## <a name="range-by"></a>Zakres według
Następnie ustaw obramowanie okna jako niepowiązane lub ograniczone. Aby ustawić nieograniczone obramowanie okna, ustaw dla suwaka wartość Unbound na obu końcach. W przypadku wybrania ustawienia między niezwiązanym i bieżącym wierszem należy ustawić przesunięcie wartości początkowej i końcowej. Obie wartości będą dodatnimi liczbami całkowitymi. Możesz użyć względnych liczb lub wartości z danych.

Suwak okna ma dwie wartości do ustawienia: wartości przed bieżącym wiersz i wartości po bieżącym wierszu. Przesunięcie początkowe i końcowe dopasowuje się do dwóch selektorów na suwaku.

![Opcje okna](media/data-flow/windows6.png "System Windows 6")

## <a name="window-columns"></a>Kolumny okna
Na koniec użyj konstruktora wyrażeń do definiowania agregacji, które mają być używane z oknami danych, takimi jak RANGa, liczba, MIN, MAX, GĘSTa RANGa, ołów, ZWŁOKa itd.

![Opcje okna](media/data-flow/windows7.png "System Windows 7")

Pełna lista funkcji agregujących i analitycznych dostępnych do użycia w języku wyrażeń przepływu danych ADF za pośrednictwem konstruktora wyrażeń jest wymieniona tutaj: https://aka.ms/dataflowexpressions .

## <a name="next-steps"></a>Następne kroki

Jeśli szukasz prostej agregacji grupowej, użyj [przekształcenia agregacji](data-flow-aggregate.md)
