---
title: azcopy env | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 71a4c27b84a16a4acb37c196ccd8ee571c2b2468
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503494"
---
# <a name="azcopy-env"></a>azcopy env

Przedstawia zmienne środowiskowe, które mogą konfigurować zachowanie programu AzCopy. Aby uzyskać pełną listę zmiennych środowiskowych, zobacz Ustawienia konfiguracji narzędzia [AzCopy w wersji 10 (Azure Storage).](storage-ref-azcopy-configuration-settings.md)

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Jeśli ustawisz zmienną środowiskową przy użyciu wiersza polecenia, ta zmienna będzie można odczytać w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych zawierających poświadczenia z historii wiersza polecenia. Aby nie wyświetlać zmiennych w historii, możesz użyć skryptu, aby monitować użytkownika o poświadczenia i ustawić zmienną środowiskową.

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --help|Wyświetla zawartość pomocy dla env polecenia. |
|--show-sensitive|Wyświetla poufne/tajne zmienne środowiskowe.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|Ciąg --trusted-microsoft-suffixes  | Określa dodatkowe sufiksy domeny, Azure Active Directory mogą być wysyłane tokeny logowania.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domeny. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
