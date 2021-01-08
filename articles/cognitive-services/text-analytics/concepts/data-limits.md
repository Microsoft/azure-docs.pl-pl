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
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 2adca03a820d02731bca252dee99c76debc85e2e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028135"
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
| Maksymalny rozmiar pojedynczego dokumentu | 5 120 znaków mierzona przez [StringInfo. lengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Ma również zastosowanie do analiza tekstu na potrzeby kondycji. |
| Maksymalny rozmiar pojedynczego dokumentu ( `/analyze` punkt końcowy)  | 125K znaki mierzone przez [StringInfo. lengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Nie ma zastosowania do analiza tekstu na potrzeby kondycji. |
| Maksymalny rozmiar całego żądania | 1 MB. Ma również zastosowanie do analiza tekstu na potrzeby kondycji. |


Jeśli dokument przekracza limit znaków, interfejs API będzie zachowywać się inaczej w zależności od używanego punktu końcowego:

* `/analyze` punktu końcowego
  * Interfejs API odrzuci całe żądanie i zwróci błąd, `400 bad request` Jeśli którykolwiek dokument w nim przekracza maksymalny rozmiar.
* Wszystkie pozostałe punkty końcowe:  
  * Interfejs API nie będzie przetwarzać dokumentu, który przekracza maksymalny rozmiar, i zwróci błąd nieprawidłowego dokumentu. Jeśli żądanie interfejsu API ma wiele dokumentów, interfejs API kontynuuje przetwarzanie ich, jeśli znajdują się w limicie znaków.

Maksymalna liczba dokumentów, które można wysłać w jednym żądaniu, zależy od używanej wersji interfejsu API i funkcji, która jest opisana w poniższej tabeli.

#### <a name="version-3"></a>[Wersja 3](#tab/version-3)

W przypadku bieżącego interfejsu API v3 obowiązują następujące limity. Przekroczenie poniższych limitów spowoduje wygenerowanie kodu błędu HTTP 400.


| Cechy | Maksymalna liczba dokumentów na żądanie | 
|----------|-----------|
| Wykrywanie języka | 1000 |
| Analiza tonacji | 10 |
| Wyszukiwanie opinii | 10 |
| Wyodrębnianie kluczowych fraz | 10 |
| Rozpoznawanie jednostek nazwanych | 5 |
| Łączenie jednostek | 5 |
| Analiza tekstu dla opieki zdrowotnej  | 10 dla interfejsu API opartego na sieci Web, 1000 dla kontenera. |
| Analizowanie punktu końcowego | 25 dla wszystkich operacji. |

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

Twoje limity szybkości różnią się w zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Te limity są takie same dla obu wersji interfejsu API. Te limity szybkości nie mają zastosowania do analiza tekstu dla kontenera kondycji, które nie mają ustalonego limitu szybkości.

| Warstwa          | Żądania na sekundę | Liczba żądań na minutę |
|---------------|---------------------|---------------------|
| S/wiele usług | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Stawki żądań są mierzone osobno dla każdej funkcji analiza tekstu. Można w tym samym czasie wysyłać maksymalną liczbę żądań dla warstwy cenowej do każdej funkcji. Na przykład jeśli jesteś w `S` warstwie i wysyłasz żądania 1000 w jeden raz, nie będzie możliwe wysłanie kolejnego żądania przez 59 sekund.


## <a name="see-also"></a>Zobacz też

* [Co to jest interfejs API analizy tekstu](../overview.md)
* [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
