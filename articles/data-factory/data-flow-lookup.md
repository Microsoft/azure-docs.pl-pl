---
title: Transformacja wyszukiwania w przepływie danych mapowania
description: Odwołuje się do danych z innego źródła przy użyciu transformacji wyszukiwania w mapowaniu przepływu danych.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: af4e33e2653aebe5d1c979aa314463e4beb7b0d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233401"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformacja wyszukiwania w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj transformacji wyszukiwania, aby odwoływać się do danych z innego źródła w strumieniu przepływu danych. Transformacja wyszukiwania dołącza kolumny z dopasowanych danych do danych źródłowych.

Transformacja wyszukiwania jest podobna do lewego sprzężenia zewnętrznego. Wszystkie wiersze ze strumienia podstawowego będą znajdować się w strumieniu wyjściowym z dodatkowymi kolumnami ze strumienia wyszukiwania. 

## <a name="configuration"></a>Konfiguracja

![Transformacja wyszukiwania](media/data-flow/lookup1.png "Wyszukiwanie")

**Strumień podstawowy:** Strumień danych przychodzących. Ten strumień jest równoważny z lewą stroną sprzężenia.

**Strumień wyszukiwania:** Dane, które są dołączane do strumienia podstawowego. Dodawane dane są określane przez warunki wyszukiwania. Ten strumień jest równoważny z prawą stroną sprzężenia.

**Dopasuj wiele wierszy:** Jeśli ta funkcja jest włączona, wiersz z wieloma dopasowań w strumieniu podstawowym zwróci wiele wierszy. W przeciwnym razie tylko jeden wiersz zostanie zwrócony w oparciu o warunek "Dopasuj na".

**Dopasowanie:** Widoczne tylko wtedy, gdy jest włączone dopasowanie "Dopasuj wiele wierszy". Wybierz, czy chcesz dopasować każdy wiersz, pierwsze dopasowanie czy ostatnie dopasowanie. Każdy wiersz jest zalecany, ponieważ wykonuje najszybszą. W przypadku wybrania pierwszego wiersza lub ostatniego wiersza konieczne będzie określenie warunków sortowania.

**Warunki wyszukiwania:** Wybierz kolumny, które mają być zgodne. Jeśli warunek równości zostanie spełniony, wiersze zostaną uznane za zgodne. Aktywuj i wybierz pozycję "kolumna obliczana", aby wyodrębnić wartość przy użyciu [języka wyrażenia przepływu danych](data-flow-expression-functions.md).

Transformacja wyszukiwania obsługuje tylko dopasowania równości. Aby dostosować wyrażenie wyszukiwania w taki sposób, aby zawierało inne operatory, takie jak większe niż, zaleca się użycie [sprzężenia krzyżowego w transformacji sprzężenia](data-flow-join.md#custom-cross-join). Sprzężenie krzyżowe pozwoli uniknąć ewentualnych błędów produktu kartezjańskiego podczas wykonywania.

Wszystkie kolumny z obu strumieni są zawarte w danych wyjściowych. Aby usunąć zduplikowane lub niepożądane kolumny, Dodaj [wybór transformacji](data-flow-select.md) po przekształceniu wyszukiwania. W transformację ujścia można również porzucić kolumny lub zmienić ich nazwy.

## <a name="analyzing-matched-rows"></a>Analizowanie dopasowanych wierszy

Po przekształceniu wyszukiwania funkcja `isMatch()` może być używana do sprawdzenia, czy wyszukiwanie pasuje do poszczególnych wierszy.

![Wzorzec wyszukiwania](media/data-flow/lookup111.png "Wzorzec wyszukiwania")

Przykładem tego wzorca jest użycie transformacji podziału warunkowego do podziału na `isMatch()` funkcję. W powyższym przykładzie pasujące wiersze przechodzą przez strumień najwyższego strumienia i niezgodnych ```NoMatch``` wierszy.

## <a name="testing-lookup-conditions"></a>Testowanie warunków wyszukiwania

Podczas testowania transformacji wyszukiwania z podglądem danych w trybie debugowania należy użyć małego zestawu znanych danych. Podczas próbkowania wierszy z dużego zestawu danych nie można przewidzieć, które wiersze i klucze mają być odczytywane w celu przetestowania. Wynik jest niejednoznaczny, co oznacza, że warunki sprzężenia mogą nie zwracać żadnych dopasowań.

## <a name="broadcast-optimization"></a>Optymalizacja rozgłaszania

![Sprzężenie emisji](media/data-flow/broadcast.png "Sprzężenie emisji")

W sprzężeniach, wyszukiwaniu i przekształceniu istnieje, jeśli jeden lub oba strumienie danych mieszczą się w pamięci węzła procesu roboczego, można zoptymalizować wydajność, włączając **emisję**. Domyślnie aparat Spark automatycznie zdecyduje, czy ma być emitowana jedna strona. Aby ręcznie wybrać opcję emisji, wybierz pozycję **naprawione**.

Nie zaleca się wyłączania emisji za pośrednictwem opcji **off** , chyba że sprzężenia są uruchamiane w przypadku błędów limitu czasu.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Przykład

![Transformacja wyszukiwania](media/data-flow/lookup-dsl-example.png "Wyszukiwanie")

Skrypt przepływu danych dla powyższej konfiguracji wyszukiwania znajduje się w poniższym fragmencie kodu.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Następne kroki

* Przekształcenia [Join](data-flow-join.md) i [EXISTS](data-flow-exists.md) są wykonywane w wielu danych wejściowych strumienia
* Użyj [przekształcenia podziału warunkowego](data-flow-conditional-split.md) z ```isMatch()``` do dzielenia wierszy na wartości zgodne i niezgodne
