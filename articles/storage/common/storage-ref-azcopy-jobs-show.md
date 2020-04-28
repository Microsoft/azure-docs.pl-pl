---
title: AzCopy zadania Pokaż | Microsoft Docs
description: W tym artykule znajdują się informacje referencyjne dotyczące polecenia AzCopy zadania.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74034133"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Pokazuje szczegółowe informacje o danym IDENTYFIKATORze zadania.

## <a name="synopsis"></a>Streszczenie

Jeśli tylko identyfikator zadania jest dostarczany bez flagi, zostanie zwrócone podsumowanie postępu zadania.

Liczba bajtów i procent wykonania, które pojawiają się po uruchomieniu tego polecenia, odzwierciedlają tylko pliki, które są wykonywane w zadaniu. Nie odzwierciedlają one częściowo ukończonych plików.

Jeśli `with-status` flaga jest ustawiona, zostanie wyświetlona lista transferów w zadaniu o podanej wartości.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia show.|
|--z ciągiem stanu|Tylko lista transferów zadań z tym stanem, dostępne wartości: rozpoczęto, sukces, Niepowodzenie|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s UInt32|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|

## <a name="see-also"></a>Zobacz także

- [azcopy jobs](storage-ref-azcopy-jobs.md)
