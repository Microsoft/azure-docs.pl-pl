---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy load clfs.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebf04531f29e18f9d120ca2efa17244c4282084c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503273"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Przesyła dane lokalne do kontenera i zapisuje je w formacie Avere Cloud FileSystem (CLFS) firmy Microsoft.

## <a name="synopsis"></a>Streszczenie

Polecenie ładowania kopiuje dane do kontenerów usługi Azure Blob Storage, a następnie zapisuje je w formacie Avere Cloud FileSystem (CLFS) firmy Microsoft. Zastrzeżony format CLFS jest używany przez Azure HPC Cache i Avere vFXT for Azure produktów.

Aby skorzystać z tego polecenia, zainstaluj wymagane rozszerzenie za pośrednictwem polecenia: pip3 install clfsload~=1.0.23. Upewnij się, CLFSLoad.py znajduje się w ścieżce PATH. Aby uzyskać więcej informacji na temat tego kroku, odwiedź stronę [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

To polecenie jest prostą opcją przenoszenia istniejących danych do magazynu w chmurze do użycia z konkretnymi produktami pamięci podręcznej obliczeń o wysokiej wydajności firmy Microsoft. 

Ponieważ te produkty używają zastrzeżonego formatu systemu plików w chmurze do zarządzania danymi, tych danych nie można załadować za pomocą natywnego polecenia kopiowania. 

Zamiast tego dane muszą zostać załadowane za pośrednictwem samego produktu pamięci podręcznej lub za pomocą tego polecenia ładowania, które używa poprawnego zastrzeżonego formatu.
To polecenie umożliwia przesyłanie danych bez użycia pamięci podręcznej. Na przykład w celu wstępnego wypełnienia magazynu lub dodania plików do zestawu roboczego bez zwiększania obciążenia pamięci podręcznej.

Miejsce docelowe jest pustym kontenerem usługi Azure Storage. Po zakończeniu transferu kontener docelowy może być używany z wystąpieniem Azure HPC Cache lub klastrem Avere vFXT for Azure klastra.

> [!NOTE] 
> Jest to wersja zapoznawcza polecenia ładowania. Zgłoś wszelkie problemy w repozytorium GitHub narzędzia AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="examples"></a>Przykłady

Załaduj cały katalog do kontenera z sygnaturą dostępu współdzielonego w formacie CLFS:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Opcje

**--compression-type** string określ typ kompresji do użycia na użytek transferów. Dostępne wartości to: `DISABLED` , `LZ4` . (wartość `LZ4` domyślna )

**--help**    pomoc dla `azcopy load clfs` polecenia.

**--log-level** string Define the log verbosity for the log file, available levels: `DEBUG` , , , `INFO` `WARNING` `ERROR` . (wartość `INFO` domyślna )

**--max-errors** uint32 Określ maksymalną liczbę dopuszczalnych niepowodzeń transferu. Jeśli wystąpi wystarczająca ilość błędów, zatrzymaj zadanie natychmiast.

**--new-session**   Rozpocznij nowe zadanie, zamiast kontynuować istniejące zadanie, którego informacje dotyczące śledzenia są przechowywane w lokalizacji `--state-path` . (wartość domyślna true)

**--preserve-hardlinks**    Zachowywanie twardych relacji linków.

**--state-path** string Wymagana ścieżka do katalogu lokalnego na potrzeby śledzenia stanu zadania. Ścieżka musi wskazać istniejący katalog w celu wznowienia zadania. Musi być pusty dla nowego zadania.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|Ciąg --trusted-microsoft-suffixes   | Określa dodatkowe sufiksy domeny, Azure Active Directory mogą być wysyłane tokeny logowania.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione tutaj wartości są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
