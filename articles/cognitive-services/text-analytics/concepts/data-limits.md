---
title: Limity danych dla interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Ograniczenia dotyczące danych interfejs API analizy tekstu z Cognitive Services platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 0dc79132ae75be511b53bf272e578ff0ce7a7775
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986818"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limity danych i szybkości dla interfejs API analizy tekstu
<a name="data-limits"></a>

Skorzystaj z tego artykułu, aby znaleźć limity rozmiaru i stawek, które umożliwiają wysyłanie danych do interfejs API analizy tekstu. 

## <a name="data-limits"></a>Limity danych

> [!NOTE]
> * Jeśli konieczne jest przeanalizowanie większych dokumentów niż zezwala na to limit, można podzielić tekst na mniejsze fragmenty tekstu przed wysłaniem ich do interfejsu API. 
> * Dokument jest pojedynczym ciągiem znaków tekstowych.  

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5 120 znaków mierzona przez [StringInfo. lengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maksymalny rozmiar całego żądania | 1 MB |

Maksymalna liczba dokumentów, które można wysłać w ramach pojedynczego żądania, będzie zależeć od używanej wersji interfejsu API i funkcji.

#### <a name="version-3"></a>[Wersja 3](#tab/version-3)

W wersji 3 interfejsu API wprowadzono następujące limity. Przekroczenie poniższych limitów spowoduje wygenerowanie kodu błędu HTTP 400.


| Cechy | Maksymalna liczba dokumentów na żądanie | 
|----------|-----------|
| Wykrywanie języka | 1000 |
| Analiza tonacji | 10 |
| Wyodrębnianie kluczowych fraz | 10 |
| Rozpoznawanie jednostek nazwanych | 5 |
| Łączenie jednostek | 5 |

#### <a name="version-2"></a>[Wersja 2](#tab/version-2)

| Cechy | Maksymalna liczba dokumentów na żądanie | 
|----------|-----------|
| Wykrywanie języka | 1000 |
| Analiza tonacji | 1000 |
| Wyodrębnianie kluczowych fraz | 1000 |
| Rozpoznawanie jednostek nazwanych | 1000 |
| Łączenie jednostek | 1000 |

---

## <a name="rate-limits"></a>Limity szybkości

Twoje limity szybkości różnią się w zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Te limity są takie same dla obu wersji interfejsu API.

| Warstwa          | Żądania na sekundę | Liczba żądań na minutę |
|---------------|---------------------|---------------------|
| S/wiele usług | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Żądania są mierzone osobno dla każdej funkcji analiza tekstu. Na przykład można wysłać maksymalną liczbę żądań dla warstwy cenowej do każdej funkcji w tym samym czasie.  


## <a name="see-also"></a>Zobacz także

* [Co to jest interfejs API analizy tekstu](../overview.md)
* [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
