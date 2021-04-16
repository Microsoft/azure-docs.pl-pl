---
title: azcopy | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b3b4f7737320cc0359192f947271a0f4beb3c478
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503001"
---
# <a name="azcopy"></a>azcopy

AzCopy to narzędzie wiersza polecenia, które przenosi dane do i z usługi Azure Storage. Zobacz artykuł [Wprowadzenie do programu AzCopy,](storage-use-azcopy-v10.md) aby pobrać program AzCopy i poznać sposoby poświadczeń autoryzacji w usłudze magazynu.

## <a name="synopsis"></a>Streszczenie

Ogólny format poleceń to : `azcopy [command] [arguments] --[flag-name]=[flag-value]` .

Aby zgłosić problemy lub dowiedzieć się więcej o narzędziu, zobacz [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) .

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Samouczek: migrowanie danych lokalnych do magazynu w chmurze za pomocą programu AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="options"></a>Opcje

**--cap-mb/s** (float) Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.

**— pomoc** Pomoc dla azcopy
      
**--output-type**  (ciąg) Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to `text`. (wartość `text` domyślna )

**--trusted-microsoft-suffixes** (ciąg) Określa dodatkowe sufiksy domeny, Azure Active Directory mogą być wysyłane tokeny logowania.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.

## <a name="see-also"></a>Zobacz też

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobs list](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobs resume](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobs show](storage-ref-azcopy-jobs-show.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)
