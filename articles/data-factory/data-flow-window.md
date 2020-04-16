---
title: Transformacja okna przepływu danych mapowania
description: Transformacja okna przepływu danych usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 67e966e3b3dd274e993797ed37e17c5490d632c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416490"
---
# <a name="azure-data-factory-window-transformation"></a>Transformacja okna fabryki danych platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformacja okna jest, gdzie można zdefiniować agregacji opartych na oknach kolumn w strumieniach danych. W Konstruktorze wyrażeń można zdefiniować różne typy agregacji, które są oparte na danych lub oknach czasu (sql over klauzuli), takich jak LEAD, LAG, NTILE, CUMEDIST, RANK, itp.). Nowe pole zostanie wygenerowane w danych wyjściowych, które zawiera te agregacje. Można również dołączyć opcjonalne pola grupy według.

![Opcje okna](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Ponad
Ustaw partycjonowanie danych kolumn dla transformacji okna. Odpowiednik SQL jest ```Partition By``` w over klauzuli w języku SQL. Jeśli chcesz utworzyć obliczenia lub utworzyć wyrażenie do użycia do partycjonowania, możesz to zrobić, najeżdżając kursorem na nazwę kolumny i wybierając "kolumna obliczona".

![Opcje okna](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Sortowanie
Inną częścią klauzuli Over ```Order By```jest ustawienie . Spowoduje to ustawienie kolejności sortowania danych. Można również utworzyć wyrażenie dla wartości obliczania w tym polu kolumny do sortowania.

![Opcje okna](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Zakres według
Następnie ustaw ramkę okna jako Niezwiązana lub Ograniczona. Aby ustawić niezwiązana ramka okna, ustaw suwak na Niezwiązany na obu końcach. Jeśli wybierzesz ustawienie między niezwiązanym i bieżącym wierszem, musisz ustawić wartości początkowe i końcowe odsunięcia. Obie wartości będą dodatnimi wartościami całkowitymi. Można użyć względnych liczb lub wartości z danych.

Suwak okna ma dwie wartości do ustawionia: wartości przed bieżącym wierszem i wartości po bieżącym wierszu. Przesunięcie Start i End dopasowuje dwa selektory na suwaku.

![Opcje okna](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Kolumny okienne
Na koniec użyj Konstruktora wyrażeń, aby zdefiniować agregacje, których chcesz używać z oknami danych, takimi jak RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG itp.

![Opcje okna](media/data-flow/windows7.png "windows 7")

Pełna lista funkcji agregacji i funkcji analitycznych dostępnych do użycia w języku wyrażenia przepływu https://aka.ms/dataflowexpressionsdanych ADF za pośrednictwem Konstruktora wyrażeń znajduje się tutaj: .

## <a name="next-steps"></a>Następne kroki

Jeśli szukasz prostej agregacji grupowej, użyj [transformacji agregującej](data-flow-aggregate.md)
