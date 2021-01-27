---
title: Lista AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia list AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7c70807d7365619ef4c6b2312f773c999b0872e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879058"
---
# <a name="azcopy-list"></a>azcopy list

Wyświetla listę jednostek w danym zasobie.

## <a name="synopsis"></a>Streszczenie

Tylko kontenery obiektów BLOB są obsługiwane w bieżącej wersji.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Pokaż zawartość pomocy dla polecenia list.|
|--do odczytu maszynowego|Wyświetla listę rozmiarów plików w bajtach.|
|--megapiksela|Wyświetla jednostki w zamówieniach 1000, nie 1024.|
|--uruchomiono-Zgadzam się|Zlicza łączną liczbę plików i ich rozmiary.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
