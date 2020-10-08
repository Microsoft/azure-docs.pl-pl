---
title: Przekształcanie UNPIVOT w przepływie danych mapowania
description: Przekształcenie Unpivot przepływu danych mapowania Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 38986c3f93856981e903ae93ed7788ae01fc6d5b
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823585"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Przekształcanie UNPIVOT w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj wyrażenia UNPIVOT w przepływie danych mapowania ADF jako metody przekształcenia nieznormalizowanego zestawu danych w bardziej znormalizowaną wersję, rozszerzając wartości z wielu kolumn w pojedynczym rekordzie na wiele rekordów z tymi samymi wartościami w jednej kolumnie.

![Przekształcanie UNPIVOT](media/data-flow/unpivot1.png "Opcje UNPIVOT 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Rozgrupuj według

![Przekształcanie UNPIVOT](media/data-flow/unpivot5.png "Opcje UNPIVOT 2")

Najpierw ustaw kolumny, według których chcesz grupować dla agregacji tabeli przestawnej. Ustaw co najmniej jedną kolumnę do rozgrupowania przy użyciu znaku + obok listy kolumn.

## <a name="unpivot-key"></a>Klucz UNPIVOT

![Przekształcanie UNPIVOT](media/data-flow/unpivot6.png "Opcje UNPIVOT 3")

Klucz przestawny jest kolumną przestawianą przez ADF z wierszy do kolumny. Domyślnie każda unikatowa wartość w zestawie danych dla tego pola będzie przestawiana do kolumny. Można jednak opcjonalnie wprowadzić wartości z zestawu danych, który ma być przestawny na wartości kolumn.

## <a name="unpivoted-columns"></a>Kolumny przestawiane

![Przekształcanie UNPIVOT](media/data-flow//unpivot7.png "Opcje (UNPIVOT) 4")

Na koniec wybierz agregację, która ma być używana dla wartości przestawnych, i sposób, w jaki kolumny mają być wyświetlane w nowej projekcji wyjściowej z transformacji.

Obowiązkowe Można ustawić wzorzec nazewnictwa z prefiksem, środkową i sufiksem, który ma zostać dodany do każdej nowej kolumny Nazwa z wartości wierszy.

Na przykład przestawianie "Sales" według "region" po prostu daje nowe wartości kolumny na podstawie każdej wartości sprzedaży. Na przykład: "25", "50", "1000",... Jeśli jednak ustawisz wartość prefiksu "Sales", "Sales" zostanie poprzedzona wartościami.

![Obraz przedstawiający kolumny ZZ, dostawcy i owoce przed i po unipivot transformację przy użyciu kolumny owoce jako klucza unipivot.](media/data-flow/unpivot3.png)

Ustawienie kolumny "normalny" spowoduje grupowanie wszystkich kolumn przestawnych z wartościami zagregowanymi. Ustawienie kolumn rozmieszczenia na "boczne" będzie odróżniać się od kolumn i wartości.

![Przekształcanie UNPIVOT](media/data-flow//unpivot7.png "Opcje UNPIVOT 5")

W końcowym zestawie wyników nieprzestawionych danych są wyświetlane sumy kolumn, które są teraz oddzielone do oddzielnych wartości wierszy.

## <a name="next-steps"></a>Następne kroki

Użyj [transformacji przestawnej](data-flow-pivot.md) , aby przestawić wiersze do kolumn.
