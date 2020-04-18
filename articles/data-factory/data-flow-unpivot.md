---
title: Transformacja odwzorowania przepływu danych mapowania
description: Transformacja przepływu danych usługi Azure Data Factory mapowanie unpivot
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: c3e769334beb6a5739eebb8d7e8dc370533c2dc6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606243"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformacja unpivot fabryki danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj unpivot w przepływie danych mapowania podajnika ADF jako sposób, aby przekształcić nienormalizowanego zestawu danych w bardziej znormalizowanej wersji, rozwijając wartości z wielu kolumn w jednym rekordzie do wielu rekordów o tych samych wartościach w jednej kolumnie.

![Unpivot transformacji](media/data-flow/unpivot1.png "Opcje unpivot 1")

## <a name="ungroup-by"></a>Rozgrupuj według

![Unpivot transformacji](media/data-flow/unpivot5.png "Opcje unpivot 2")

Najpierw ustaw kolumny, według których chcesz pogrupować dla agregacji przestawnej. Ustaw jedną lub więcej kolumn do rozgrupowania ze znakiem + obok listy kolumn.

## <a name="unpivot-key"></a>Unpivot klucz

![Unpivot transformacji](media/data-flow/unpivot6.png "Opcje unpivot 3")

Klawisz przestawny to kolumna, którą podajnik ADF będzie obracał z wiersza na kolumnę. Domyślnie każda unikatowa wartość w zestawie danych dla tego pola zostanie prześliona do kolumny. Można jednak opcjonalnie wprowadzić wartości z zestawu danych, które mają być przestawiane do wartości kolumn.

## <a name="unpivoted-columns"></a>Nieprzewidyowane kolumny

![Unpivot transformacji](media/data-flow//unpivot7.png "Unpivot opcje 4")

Na koniec wybierz agregację, która ma być używana dla wartości przestawnych i jak chcesz kolumny mają być wyświetlane w nowej projekcji wyjściowej z transformacji.

(Opcjonalnie) Można ustawić wzorzec nazewnictwa z prefiksem, środkiem i sufiksem, który ma zostać dodany do każdej nowej nazwy kolumny z wartości wierszy.

Na przykład przestawianie "Sprzedaż" przez "Region" po prostu daje nowe wartości kolumn z każdej wartości sprzedaży. Na przykład: "25", "50", "1000", ... Jeśli jednak ustawisz wartość prefiksu "Sprzedaż", wartość "Sprzedaż" zostanie poprzedzony wartościami.

<img src="media/data-flow/unpivot3.png" width="400">

Ustawienie układu kolumn na "Normalny" spowoduje zgrupowanie wszystkich kolumn przestawnych z ich zagregowanymi wartościami. Ustawienie układu kolumn na "Boczne" będzie naprzemiennie między kolumną a wartością.

![Unpivot transformacji](media/data-flow//unpivot7.png "Opcje unpivot 5")

Końcowy zestaw wyników niepiślanych pokazuje sumy kolumn teraz niepichowe do oddzielnych wartości wierszy.

## <a name="next-steps"></a>Następne kroki

[Transformacja przestawna](data-flow-pivot.md) służy do obracania wierszy do kolumn.
