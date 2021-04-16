---
title: azcopy list | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ca436ee440ad7c1dd2caee3a65790f8b630fd949
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503290"
---
# <a name="azcopy-list"></a>azcopy list

Wyświetla listę jednostek w danym zasobie.

## <a name="synopsis"></a>Streszczenie

W bieżącej wersji są obsługiwane tylko kontenery obiektów blob.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="examples"></a>Przykłady

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --help|Pokaż zawartość pomocy dla polecenia listy.|
|--machine-readable|Wyświetla listę rozmiarów plików w bajtach.|
|--mega-units|Wyświetla jednostki w zamówieniach 1000, a nie 1024.|
|--running-tally|Zlicza łączną liczbę plików i ich rozmiary.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność chwila po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|--trusted-microsoft-suffixes string   |Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
