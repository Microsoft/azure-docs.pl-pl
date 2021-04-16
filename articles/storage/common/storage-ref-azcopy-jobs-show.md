---
title: Azcopy jobs show | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503358"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Przedstawia szczegółowe informacje dotyczące danego identyfikatora zadania.

## <a name="synopsis"></a>Streszczenie

Jeśli tylko identyfikator zadania jest podany bez flagi, zwracane jest podsumowanie postępu zadania.

Liczba bajtów i procent ukończenia wyświetlane po uruchomieniu tego polecenia odzwierciedlają tylko pliki, które zostały ukończone w zadaniu. Nie odzwierciedlają one częściowo ukończonych plików.

Jeśli `with-status` flaga jest ustawiona, zostanie wyświetlona lista transferów w zadaniu z podaną wartością.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="options"></a>Opcje

|Opcja|Opis|
|--|--|
|-h, --help|Wyświetla zawartość pomocy dla pokaż polecenia.|
|--with-status string|Wymień tylko transfery zadań o tym stanie i dostępnych wartościach: Rozpoczęto, Powodzenie, Niepowodzenie|

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|Ciąg --trusted-microsoft-suffixes   |Określa dodatkowe sufiksy domeny, Azure Active Directory mogą być wysyłane tokeny logowania.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione tutaj wartości są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy jobs](storage-ref-azcopy-jobs.md)
