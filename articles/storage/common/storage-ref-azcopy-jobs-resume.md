---
title: Azcopy jobs resume | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy jobs resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 497aa3a77397a8a587badc0911176cd092eb1f23
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503341"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Wznawia istniejące zadanie o danym identyfikatorze zadania.

## <a name="synopsis"></a>Streszczenie

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|--destination-sas string|Docelowa sygnatura dostępu współdzielonego miejsca docelowego dla danego identyfikatora zadania.|
|--exclude string|Filtr: wyklucz te nieudane transfery podczas wznawiania zadania. Pliki powinny być oddzielone przecinkami ";".|
|-h, --help|Pokaż zawartość pomocy dla polecenia resume.|
|--include ciąg|Filtruj: uwzględnij tylko te nieudane transfery podczas wznawiania zadania. Pliki powinny być oddzielone przecinkami ";".|
|--source-sas string |źródłową sygnaturę dostępu współdzielonego źródła dla danego identyfikatora zadania.|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieco różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|Ciąg --trusted-microsoft-suffixes   |Określa dodatkowe sufiksy domeny, Azure Active Directory mogą być wysyłane tokeny logowania.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione tutaj wartości są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
