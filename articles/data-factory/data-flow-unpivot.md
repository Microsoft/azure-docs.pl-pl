---
title: Przekształcanie UNPIVOT w przepływie danych mapowania
description: Przekształcenie Unpivot przepływu danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040199"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Przekształcanie UNPIVOT w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj wyrażenia UNPIVOT w przepływie danych mapowania ADF jako metody przekształcenia nieznormalizowanego zestawu danych w bardziej znormalizowaną wersję, rozszerzając wartości z wielu kolumn w pojedynczym rekordzie na wiele rekordów z tymi samymi wartościami w jednej kolumnie.

![Zrzut ekranu przedstawia wybrany Operator UNPIVOT z menu.](media/data-flow/unpivot1.png "Opcje UNPIVOT 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Rozgrupuj według

![Zrzut ekranu przedstawia ustawienia UNPIVOT z wybraną kartą Rozgrupuj według.](media/data-flow/unpivot5.png "Opcje UNPIVOT 2")

Najpierw ustaw kolumny, które chcesz rozgrupować według agregacji UNPIVOT. Ustaw co najmniej jedną kolumnę do rozgrupowania przy użyciu znaku + obok listy kolumn.

## <a name="unpivot-key"></a>Klucz UNPIVOT

![Zrzut ekranu przedstawia ustawienia UNPIVOT z wybraną kartą klucz UNPIVOT.](media/data-flow/unpivot6.png "Opcje UNPIVOT 3")

Klucz UNPIVOT jest kolumną przestawianą przez ADF z kolumny na wiersz. Domyślnie każda unikatowa wartość w zestawie danych dla tego pola będzie przestawiana do wiersza. Można jednak opcjonalnie wprowadzić wartości z zestawu danych, który ma być przestawny na wartości wierszy.

## <a name="unpivoted-columns"></a>Kolumny przestawiane

![Zrzut ekranu przedstawia ustawienia UNPIVOT z wybraną kartą Podgląd danych.](media/data-flow//unpivot7.png "Opcje (UNPIVOT) 4")

Na koniec wybierz nazwę kolumny do przechowywania wartości przestawianych kolumn, które są przekształcone w wiersze.

Obowiązkowe Można upuścić wiersze z wartościami null.

Na przykład SumCost jest nazwą kolumny, która jest wybierana w powyższym przykładzie.

![Obraz przedstawiający kolumny ZZ, dostawcy i owoce przed i po unipivot transformację przy użyciu kolumny owoce jako klucza unipivot.](media/data-flow/unpivot3.png)

Ustawienie kolumny "normalny" spowoduje grupowanie wszystkich nowych kolumn, które nie zostały przestawiane z jednej wartości. Ustawienie kolumn rozmieszczenia na "boczne" spowoduje grupowanie nowych kolumn, które nie są wystawiane, wygenerowanych na podstawie istniejącej kolumny.

![Zrzut ekranu przedstawia wynik transformacji.](media/data-flow//unpivot7.png "Opcje UNPIVOT 5")

W końcowym zestawie wyników nieprzestawionych danych są wyświetlane sumy kolumn, które są teraz oddzielone do oddzielnych wartości wierszy.

## <a name="next-steps"></a>Następne kroki

Użyj [transformacji przestawnej](data-flow-pivot.md) , aby przestawić wiersze do kolumn.
