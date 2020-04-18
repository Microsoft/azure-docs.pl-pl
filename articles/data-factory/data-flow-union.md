---
title: Mapowanie transformacji unii przepływu danych
description: Przepływ danych usługi Azure Data Factory — nowa transformacja gałęzi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: 35f6b74f3b6cce5a0af812d4613f9da2f9fbd552
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606266"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Transformacja unii przepływu danych mapowania usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Unia połączy wiele strumieni danych w jeden, z SQL Union tych strumieni jako nowe dane wyjściowe z transformacji Unii. Cały schemat z każdego strumienia wejściowego zostaną połączone wewnątrz przepływu danych, bez konieczności posiadania klucza sprzężenia.

N liczby strumieni w tabeli ustawień można połączyć, wybierając ikonę "+" obok każdego skonfigurowanego wiersza, w tym zarówno dane źródłowe, jak i strumienie z istniejących przekształceń w przepływie danych.

![Transformacja Unii](media/data-flow/union.png "Unia")

W takim przypadku można połączyć różne metadane z wielu źródeł (w tym przykładzie trzy różne pliki źródłowe) i połączyć je w jeden strumień:

![Omówienie transformacji Unii](media/data-flow/union111.png "Unia 1")

Aby to osiągnąć, należy dodać dodatkowe wiersze w ustawieniach Unii, dołączając wszystkie źródła, które chcesz dodać. Nie ma potrzeby wspólnego wyszukiwania lub klucza dołączenia:

![Ustawienia transformacji Unii](media/data-flow/unionsettings.png "Ustawienia Unii")

Jeśli ustawisz transformację Select po unii, będzie można zmienić nazwę nakładających się pól lub pól, które nie zostały nazwane ze źródeł bez nagłówków. Kliknij "Sprawdź", aby wyświetlić metadane połączenia ze 132 kolumnami w tym przykładzie z trzech różnych źródeł:

![Ostateczna transformacja Unii](media/data-flow/union333.png "Unia 3")

## <a name="name-and-position"></a>Nazwa i pozycja

Po wybraniu "union by name", każda wartość kolumny spadnie do odpowiedniej kolumny z każdego źródła, z nowym schematem metadanych łączonych.

Jeśli wybierzesz "union by position", każda wartość kolumny spadnie do pierwotnej pozycji z każdego odpowiedniego źródła, co spowoduje nowy połączony strumień danych, w którym dane z każdego źródła są dodawane do tego samego strumienia:

![Produkcja unijna](media/data-flow/unionoutput.png "Produkcja unijna")

## <a name="next-steps"></a>Następne kroki

Eksploruj podobne przekształcenia, w tym [Dołącz](data-flow-join.md) i [Istnieje](data-flow-exists.md).
