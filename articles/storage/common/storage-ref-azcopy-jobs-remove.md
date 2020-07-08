---
title: AzCopy zadania usuwania | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia AzCopy zadania usuwania.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9f544e479dd21b4c1f7f2e289e91b506aab7eff2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220054"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Usuń wszystkie pliki skojarzone z danym IDENTYFIKATORem zadania.

> [!NOTE] 
> Można dostosować lokalizację, w której zapisywane są pliki dziennika i planowania. Aby dowiedzieć się więcej, zobacz [AzCopy ENV](storage-ref-azcopy-env.md) .

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](storage-use-azcopy-blobs.md)
- [Transferowanie danych za pomocą AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opcje

**-h,--pomoc**                Pomoc dotycząca usuwania.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

**--Cap-MB/s UInt32**      Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.

**--** format ciągu typu danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text". (domyślny "tekst")

**--Zaufane — ciąg sufiksów firmy Microsoft** określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.

## <a name="see-also"></a>Zobacz także

- [azcopy jobs](storage-ref-azcopy-jobs.md)
