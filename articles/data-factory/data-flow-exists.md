---
title: Istnieje transformacja w przepływie danych mapowania
description: Sprawdź istniejące wiersze przy użyciu transformacji istnieje w usłudze Azure Data Factory mapowanie przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: a303c8fa1e23460fb906232eedb6bfb1930b4bc9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606463"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Istnieje transformacja w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja istnieje jest transformacja filtrowania wiersza, który sprawdza, czy dane istnieją w innym źródle lub strumieniu. Strumień wyjściowy zawiera wszystkie wiersze w lewym strumieniu, które istnieją lub nie istnieją w prawym strumieniu. Transformacja istnieje jest ```SQL WHERE EXISTS``` podobna do i ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Konfigurowanie

1. Wybierz, który strumień danych sprawdzasz, czy istnieje w prawej listy rozwijanej **strumienia.**
1. Określ, czy szukasz danych, które mają istnieć, czy nie w ustawieniu **Typ Istnieje.**
1. Wybierz, czy chcesz **wyrażenie niestandardowe**.
1. Wybierz kolumny kluczy, które chcesz porównać jako istniejące warunki. Domyślnie przepływ danych wyszukuje równość między jedną kolumną w każdym strumieniu. Aby porównać za pomocą obliczonej wartości, umieść wskaźnik myszy na rozwijanej kolumnie i wybierz **kolumnę Obliczona**.

![Istnieją ustawienia](media/data-flow/exists.png "istnieje 1")

### <a name="multiple-exists-conditions"></a>Istnieje wiele warunków

Aby porównać wiele kolumn z każdego strumienia, dodaj nowy warunek istnieje, klikając ikonę plus obok istniejącego wiersza. Każdy dodatkowy warunek jest połączony przez instrukcję "i". Porównanie dwóch kolumn jest takie samo jak następujące wyrażenie:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Wyrażenie niestandardowe

Aby utworzyć wyrażenie swobodne, które zawiera operatory inne niż "i" i "równa się", wybierz pole **wyrażenia niestandardowego.** Wprowadź wyrażenie niestandardowe za pośrednictwem konstruktora wyrażeń przepływu danych, klikając niebieskie pole.

![Istnieje ustawienia niestandardowe](media/data-flow/exists1.png "istnieje niestandardowe")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład jest istnieje `checkForChanges` transformacja o `NameNorm2` nazwie, `TypeConversions`która ma strumień w lewo i w prawo.  Warunek istnieje jest wyrażeniem, `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` które zwraca `EMPID` `Region` wartość true, jeśli zarówno i kolumny w każdym strumieniu pasuje. Jak sprawdzamy istnienie, `negate` jest fałszywe. Nie włączamy żadnych transmisji w zakładce `broadcast` optymalizacji, `'none'`więc ma wartość .

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Istnieje przykład](media/data-flow/exists-script.png "Istnieje przykład")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Następne kroki

Podobne przekształcenia to [Odnośnik](data-flow-lookup.md) i [Dołącz](data-flow-join.md).
