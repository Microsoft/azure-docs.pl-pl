---
title: Transformacja przestawna przepływu danych mapowania
description: Dane przestawne z wierszy do kolumn przy użyciu transformacji przestawnej przepływu danych usługi Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 980d7c3e1b1f69e76c091e2a4a74c8e5a4d0bb64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606357"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformacja przestawna fabryki danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Przestań używać w przepływie danych ADF jako agregacji, w której jedna lub więcej kolumn grupowania ma swoje odrębne wartości wierszy przekształcone w poszczególne kolumny. Zasadniczo można przestawiać wartości wierszy na nowe kolumny (przekształcać dane w metadane).

![Opcje przestawne](media/data-flow/pivot1.png "pivot 1")

## <a name="group-by"></a>Grupuj według

![Opcje przestawne](media/data-flow/pivot2.png "przestawianie 2")

Najpierw ustaw kolumny, według których chcesz pogrupować dla agregacji przestawnej. Możesz ustawić więcej niż 1 kolumnę tutaj ze znakiem + obok listy kolumn.

## <a name="pivot-key"></a>Klawisz przestawny

![Opcje przestawne](media/data-flow/pivot3.png "przestawianie 3")

Klawisz przestawny to kolumna, którą podajnik ADF będzie obracał z wiersza na kolumnę. Domyślnie każda unikatowa wartość w zestawie danych dla tego pola zostanie prześliona do kolumny. Można jednak opcjonalnie wprowadzić wartości z zestawu danych, które mają być przestawiane do wartości kolumn. Jest to kolumna, która określi nowe kolumny, które zostaną utworzone.

## <a name="pivoted-columns"></a>Kolumny przestawne

![Opcje przestawne](media/data-flow/pivot4.png "przestawianie 4")

Na koniec wybierzesz agregację, która ma być używana dla wartości przestawnych i jak chcesz, aby kolumny były wyświetlane w nowej projekcji wyjściowej z transformacji.

(Opcjonalnie) Można ustawić wzorzec nazewnictwa z prefiksem, środkiem i sufiksem, który ma zostać dodany do każdej nowej nazwy kolumny z wartości wierszy.

Na przykład, obracanie "Sprzedaż" przez "Region" spowodowałoby nowe wartości kolumn z każdej wartości sprzedaży, czyli "25", "50", "1000" itp. Jeśli jednak ustawisz wartość prefiksu "Sprzedaż-", każda wartość kolumny doda "Sprzedaż-" na początku wartości.

![Opcje przestawne](media/data-flow/pivot5.png "pivot 5")

Ustawienie układu kolumn na "Normalny" spowoduje zgrupowanie wszystkich kolumn przestawnych z ich zagregowanymi wartościami. Ustawienie układu kolumn na "Boczne" będzie naprzemiennie między kolumną a wartością.

### <a name="aggregation"></a>Agregacja

Aby ustawić agregację, której chcesz użyć dla wartości przestawnych, kliknij pole u dołu okienka Kolumny przestawne. Wprowadzisz do konstruktora wyrażeń przepływ danych ADF, gdzie można utworzyć wyrażenie agregacji i podać opisową nazwę aliasu dla nowych zagregowanych wartości.

Użyj języka wyrażenia przepływu danych ADF, aby opisać przekształcenia https://aka.ms/dataflowexpressionskolumn przestawnych w Konstruktorze wyrażeń: .

## <a name="pivot-metadata"></a>Metadane przestawne

Transformacja przestawna spowoduje tworzenie nowych nazw kolumn, które są dynamiczne na podstawie danych przychodzących. Klawisz przestawny tworzy wartości dla każdej nowej nazwy kolumny. Jeśli nie określisz poszczególnych wartości i chcesz utworzyć dynamiczne nazwy kolumn dla każdej unikatowej wartości w kluczu przestawnym, interfejs użytkownika nie będzie wyświetlać metadanych w inspect i nie będzie propagacji kolumn do transformacji sink. Jeśli ustawisz wartości dla klawisza przestawnego, a następnie ADF może określić nowe nazwy kolumn i te nazwy kolumn będą dostępne w inspekcji i ujścia mapowania.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Generowanie nowego modelu z kolumn dynamicznych

Pivot generuje nowe nazwy kolumn dynamicznie na podstawie wartości wierszy. Możesz przekształcić te nowe kolumny w metadane, do których można się odwoływać później w przepływie danych. Aby to zrobić, kliknij kartę Podgląd danych. Wszystkie nowe kolumny generowane przez transformację przestawną są wyświetlane z ikoną "dryfował" w nagłówku tabeli. Kliknij przycisk "Mapa dryfowała", aby przekształcić te nowe kolumny w metadane, czyniąc je częścią modelu przepływu danych.

![Przestawianie kolumn](media/data-flow/newpivot1.png "Mapa dryfowała kolumnach przestawnych")

### <a name="landing-new-columns-in-sink"></a>Lądowanie nowych kolumn w zlewie

Nawet w przypadku dynamicznych nazw kolumn w przestawianiu możesz nadal przeniknąć nowe nazwy kolumn i wartości do magazynu docelowego. Wystarczy ustawić "Zezwalaj na dryf schematu" w ustawieniach zlewu. Nowe nazwy dynamiczne nie będą widoczne w metadanych kolumn, ale opcja dryfu schematu pozwoli na wyładowanie danych.

### <a name="view-metadata-in-design-mode"></a>Wyświetlanie metadanych w trybie projektowania

Jeśli chcesz wyświetlić nowe nazwy kolumn jako metadane w inspekcji i chcesz zobaczyć kolumny propagować jawnie do transformacji sink, a następnie ustawić jawne wartości na karcie Klucz przestawny.

### <a name="how-to-rejoin-original-fields"></a>Jak ponownie dołączyć do oryginalnych pól
Transformacja przestawna będzie wyświetlać tylko kolumny używane w akcji agregacji, grupowania i przestawnej. Jeśli chcesz dołączyć inne kolumny z poprzedniego kroku w przepływie, użyj Nowej gałęzi z poprzedniego kroku i użyj wzorca samozłączenia, aby połączyć przepływ z oryginalnymi metadanymi.

## <a name="next-steps"></a>Następne kroki

Spróbuj [przekształcenia unpivot,](data-flow-unpivot.md) aby przekształcić wartości kolumn w wartości wierszy. 
