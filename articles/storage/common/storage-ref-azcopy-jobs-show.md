---
title: AzCopy zadania Pokaż | Microsoft Docs
description: W tym artykule znajdują się informacje referencyjne dotyczące polecenia AzCopy zadania.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 57970371b947c5240be97e58c10299d2f4dfe525
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879075"
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
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia show.|
|--z ciągiem stanu|Tylko lista transferów zadań z tym stanem, dostępne wartości: rozpoczęto, sukces, Niepowodzenie|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [azcopy jobs](storage-ref-azcopy-jobs.md)
