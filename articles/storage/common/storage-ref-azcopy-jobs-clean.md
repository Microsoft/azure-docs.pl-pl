---
title: azcopy jobs clean | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy jobs clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 01bb7e37965abacac8105689bcb5ae52c548d647
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503426"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Usuń wszystkie pliki dziennika i planu dla wszystkich zadań

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="examples"></a>Przykłady

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opcje

**— pomoc**                Pomoc w oczyszczeniu.

**--with-status** string Usuwaj tylko zadania o tym stanie, dostępne wartości: `Canceled` , , , , `Completed` `Failed` `InProgress` `All` (wartość domyślna `All` )

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--cap-mb/s float**      Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieco różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.

**--output-type** ciąg Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text". (domyślna wartość "text")

**--trusted-microsoft-suffixes** ciąg Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
