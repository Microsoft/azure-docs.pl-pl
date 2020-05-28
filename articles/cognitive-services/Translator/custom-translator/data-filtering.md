---
title: Filtrowanie danych — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Gdy przesyłane są dokumenty, które mają być używane do szkolenia w systemie niestandardowym, dokumenty te są częścią serii kroków przetwarzania i filtrowania, aby przygotować się do szkoleń.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 67807e18559006b7d7eb6089a30370d614aefca3
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992848"
---
# <a name="data-filtering"></a>Filtrowanie danych

Gdy przesyłane są dokumenty, które mają być używane do szkolenia w systemie niestandardowym, dokumenty te są częścią serii kroków przetwarzania i filtrowania, aby przygotować się do szkoleń. Te kroki są wyjaśnione w tym miejscu. Znajomość filtrowania może pomóc zrozumieć liczbę zdań wyświetlaną w usłudze translator niestandardowy, a także kroki, które można wykonać, aby przygotować dokumenty do szkolenia przy użyciu translatora niestandardowego.

## <a name="sentence-alignment"></a>Wyrównanie zdań
Jeśli dokument nie jest w formacie XLIFF, TMX lub ALIGN, translator niestandardowy wyrównuje zdania źródłowe i docelowe dokumentów ze sobą, zdania według zdania. Translator nie wykonuje wyrównania dokumentu — następuje jego nazewnictwo w celu znalezienia pasującego dokumentu w innym języku. W ramach dokumentu translator niestandardowy próbuje znaleźć odpowiednie zdanie w innym języku. Używa znacznika dokumentu, takiego jak osadzone Tagi HTML, aby pomóc w wyrównaniu.  

Jeśli zobaczysz dużą rozbieżność między liczbą zdań w dokumentach źródłowych i docelowych, dokument może nie być równoległy w pierwszym miejscu lub z innych powodów, dla których nie można było wyrównać. Pary dokumentu z dużą różnicą (>10%) zdania po każdej stronie uzasadniają drugie spojrzenie, aby upewnić się, że są one rzeczywiście równoległe. W przypadku, gdy liczba zdań różni się podejrzanie, w przypadku usługi Custom translator zostanie wyświetlone ostrzeżenie obok dokumentu.  


## <a name="deduplication"></a>Deduplikacja
Translator niestandardowy usuwa zdania, które są obecne w testowaniu i dostrajania dokumentów z danych szkoleniowych.Usuwanie odbywa się dynamicznie w przebiegu szkoleniowym, a nie w kroku przetwarzania danych. Translator niestandardowy zgłasza liczbę zdań do użytkownika w przeglądzie projektu przed takim usunięciem.  

## <a name="length-filter"></a>Filtr długości
* Usuń zdania z tylko jednego wyrazu po obu stronach.
* Usuń zdania zawierające więcej niż 100 słów po obu stronach.Chiński, japoński, koreański jest wyłączony.
* Usuń zdania zawierające mniej niż 3 znaki. Chiński, japoński, koreański jest wyłączony.
* Usuń zdania z więcej niż 2000 znaków w języku chińskim, japońskim i koreańskim.
* Usuń zdania z mniej niż 1% znaków alfanumerycznych.
* Usuń wpisy słownika zawierające więcej niż 50 słów.

## <a name="white-space"></a>Biały znak
* Zastąp dowolną sekwencję znaków odstępu, w tym tabulatory i sekwencje CR/LF z pojedynczym znakiem odstępu.
* Usuń początkowe lub końcowe miejsce w zdaniu

## <a name="sentence-end-punctuation"></a>Interpunkcja końcowa zdania
Zastąp wiele znaków interpunkcyjnych kończących zdanie pojedynczym wystąpieniem.  

## <a name="japanese-character-normalization"></a>Normalizacja znaków japońskich
Konwertuj litery i cyfry o pełnej szerokości na znaki połówkowej szerokości.

## <a name="unescaped-xml-tags"></a>Niezmienione tagi XML
Filtrowanie przekształca znaczniki niezmienione na znaczniki unikowe:
* `&lt;`stanowi`&amp;lt;`
* `&gt;`stanowi`&amp;gt;`
* `&amp;`stanowi`&amp;amp;`

## <a name="invalid-characters"></a>Nieprawidłowe znaki
Niestandardowa translator usuwa zdania zawierające znak Unicode U + FFFD. Znak U + FFFD wskazuje, że konwersja kodowania nie powiodła się.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](how-to-train-model.md) w usłudze translator niestandardowym.
