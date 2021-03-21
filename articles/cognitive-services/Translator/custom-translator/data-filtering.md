---
title: Filtrowanie danych — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Gdy przesyłane są dokumenty, które mają być używane do szkolenia w systemie niestandardowym, dokumenty te są częścią serii kroków przetwarzania i filtrowania, aby przygotować się do szkoleń.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 53dea20e356f735a521dec8c22edf8cb2aa7122d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895870"
---
# <a name="data-filtering"></a>Filtrowanie danych

Gdy przesyłane są dokumenty, które mają być używane do szkolenia w systemie niestandardowym, dokumenty te są częścią serii kroków przetwarzania i filtrowania, aby przygotować się do szkoleń. Te kroki są wyjaśnione w tym miejscu. Znajomość filtrowania może pomóc zrozumieć liczbę zdań wyświetlaną w usłudze translator niestandardowy, a także kroki, które można wykonać, aby przygotować dokumenty do szkolenia przy użyciu translatora niestandardowego.

## <a name="sentence-alignment"></a>Wyrównanie zdań
Jeśli dokument nie jest w formacie XLIFF, TMX lub ALIGN, translator niestandardowy wyrównuje zdania źródłowe i docelowe dokumentów ze sobą, zdania według zdania. Nie można wyrównać dokumentu przez translatora niestandardowego — następuje jego nazewnictwo w celu znalezienia pasującego dokumentu w innym języku. W ramach dokumentu translator niestandardowy próbuje znaleźć odpowiednie zdanie w innym języku. Używa znacznika dokumentu, takiego jak osadzone Tagi HTML, aby pomóc w wyrównaniu.  

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
* `&lt;` stanowi `&amp;lt;`
* `&gt;` stanowi `&amp;gt;`
* `&amp;` stanowi `&amp;amp;`

## <a name="invalid-characters"></a>Nieprawidłowe znaki
Niestandardowa translator usuwa zdania zawierające znak Unicode U + FFFD. Znak U + FFFD wskazuje, że konwersja kodowania nie powiodła się.

## <a name="next-steps"></a>Następne kroki

- [Uczenie modelu](how-to-train-model.md) w usłudze translator niestandardowym.
