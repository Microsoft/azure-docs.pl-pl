---
title: koperta AzCopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące AzCopy ENV polecenia.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16ceddc8848df2f8e0456d2b0f4dab66a76e6eff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879126"
---
# <a name="azcopy-env"></a>azcopy env

Pokazuje zmienne środowiskowe, które mogą konfigurować zachowanie AzCopy.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> W przypadku ustawienia zmiennej środowiskowej przy użyciu wiersza polecenia ta zmienna zostanie odczytana w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne w historii, można użyć skryptu w celu wyświetlenia monitu o podanie poświadczeń użytkownika i ustawienia zmiennej środowiskowej.

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h,--pomoc|Wyświetla zawartość pomocy dla polecenia ENV. |
|--Pokaż-poufne|Pokazuje zmienne środowiskowe poufne/tajne.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft  | Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [AzCopy](storage-ref-azcopy.md)
