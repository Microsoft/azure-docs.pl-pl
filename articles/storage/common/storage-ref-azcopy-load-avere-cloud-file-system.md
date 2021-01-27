---
title: AzCopy obciążenia CLFS | Microsoft Docs
titleSuffix: Azure Storage
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy Load CLFS.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878379"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Przenosi dane lokalne do kontenera i zapisuje je w formacie avere Cloud system plików (CLFS) firmy Microsoft.

## <a name="synopsis"></a>Streszczenie

Polecenie load kopiuje dane do kontenerów usługi Azure Blob Storage, a następnie przechowuje te dane w formacie avere Cloud system plików (CLFS) firmy Microsoft. Własny format CLFS jest używany przez pamięć podręczną platformy Azure HPC i avere vFXT dla produktów platformy Azure.

Aby użyć tego polecenia, Zainstaluj wymagane rozszerzenie za pośrednictwem: PIP3 Install clfsload ~ = 1.0.23. Upewnij się, że CLFSLoad.py znajduje się w ścieżce. Aby uzyskać więcej informacji na ten temat, odwiedź stronę [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

To polecenie jest prostą opcją przeniesienia istniejących danych do magazynu w chmurze w celu użycia z konkretnymi produktami pamięci podręcznej o wysokiej wydajności obliczeniowej firmy Microsoft. 

Ponieważ te produkty używają zastrzeżonego formatu systemu plików w chmurze do zarządzania danymi, te dane nie mogą zostać załadowane za pomocą polecenia kopiowania natywnego. 

Zamiast tego dane muszą zostać załadowane za pośrednictwem samego produktu pamięci podręcznej lub za pomocą tego polecenia ładowania, które używa poprawnego formatu własności.
To polecenie umożliwia transfer danych bez użycia pamięci podręcznej. Na przykład, aby wstępnie wypełnić magazyn lub dodać pliki do zestawu roboczego bez zwiększania obciążenia pamięci podręcznej.

Lokalizacją docelową jest pusty kontener usługi Azure Storage. Po zakończeniu transferu kontener docelowy może być używany z wystąpieniem pamięci podręcznej platformy Azure HPC lub avere vFXT dla klastra platformy Azure.

> [!NOTE] 
> To jest wersja zapoznawcza polecenia ładowania. Zgłoś wszelkie problemy w repozytorium GitHub AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Załaduj cały katalog do kontenera z sygnaturą dostępu współdzielonego w formacie CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opcje

**--ciąg typu kompresji** określa typ kompresji, który ma być używany do transferów. Dostępne wartości to: `DISABLED` , `LZ4` . (ustawienie domyślne `LZ4` )

**--Pomoc**    dla `azcopy load clfs` polecenia.

**--ciąg poziomu dziennika** definiuje szczegółowość dziennika dla pliku dziennika, dostępne poziomy: `DEBUG` ,, `INFO` `WARNING` , `ERROR` . (ustawienie domyślne `INFO` )

**--Max-errorss** Określ maksymalną dozwoloną liczbę niepowodzeń transferu. W przypadku wystąpienia wystarczającej ilości błędów Zatrzymaj zadanie natychmiast.

**--Nowa sesja**   Rozpocznij nowe zadanie, zamiast kontynuować już istniejące, którego informacje śledzenia są przechowywane w `--state-path` . (wartość domyślna to true)

**--Preserve-Hardlinks**    Zachowaj relacje twarde łącza.

parametr **--State-Path** wymaga ścieżki do katalogu lokalnego na potrzeby śledzenia stanu zadań. Ścieżka musi wskazywać istniejący katalog w celu wznowienia zadania. Dla nowego zadania musi być pusty.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   | Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
