---
title: azcopy logout | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy logout.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 20d1ee643f9864fb95ebdc9215cd0dab13a0fb5c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503103"
---
# <a name="azcopy-logout"></a>azcopy logout

Wyloguje użytkownika i zakończy dostęp do zasobów usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

To polecenie spowoduje usunięcie wszystkich buforowanych informacji logowania dla bieżącego użytkownika.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --help|Pokaż zawartość pomocy dla polecenia wyloguj.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieco różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|Ciąg --trusted-microsoft-suffixes   |Określa dodatkowe sufiksy domeny, Azure Active Directory mogą być wysyłane tokeny logowania.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
