---
title: Liczba znaków — Translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak Azure Cognitive Services Translator zlicza znaki, aby zrozumieć, jak pozyska zawartość.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 53fc22e1dbdac3240f72e8d64fbaee690597950f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373931"
---
# <a name="how-the-translator-counts-characters"></a>Jak translator zlicza znaki

Translator zlicza każdy punkt kodu Unicode tekstu wejściowego jako znak. Każde tłumaczenie tekstu na język jest liczone jako osobne tłumaczenie, nawet jeśli żądanie zostało wykonane w jednym wywołaniu interfejsu API tłumacza na wiele języków. Długość odpowiedzi nie ma znaczenia.

To, co jest ważne:

* Tekst przekazywany do usługi Translator w treści żądania
   * `Text` w przypadku korzystania z metod Translate, Transliterate i Dictionary Lookup
   * `Text` i `Translation` w przypadku korzystania z metody Dictionary Examples
* Wszystkie znaczniki: HTML, tagi XML itp. w polu tekstowym treści żądania. Notacja JSON używana do kompilowania żądania (na przykład "Text:") nie jest zliczona.
* Pojedyncza litera
* Znaki interpunkcyjne
* Spacja, tabulator, znacznik i dowolny rodzaj znaku odstępu
* Każdy punkt kodu zdefiniowany w standardzie Unicode
* Powtórzone tłumaczenie, nawet jeśli wcześniej przetłumaczono ten sam tekst

W przypadku skryptów opartych na ideogramach, takich jak chiński i japoński kanji, usługa Translator nadal zlicza punkty kodu Unicode, po jednym znaku na ideogram. Wyjątek: surogaty Unicode są liczone jako dwa znaki.

Liczba żądań, słów, bajtów lub zdań nie ma znaczenia w liczbie znaków.

Wywołania metod Detect i BreakSentence nie są liczone w zużyciu znaków. Oczekujemy jednak, że wywołania metod Detect i BreakSentence są w rozsądnym stosunku do użycia innych funkcji, które są liczone. Jeśli liczba wywołań Detect lub BreakSentence przekracza liczbę innych metod policzonych 100 razy, firma Microsoft zastrzega sobie prawo do ograniczenia użycia metod Detect i BreakSentence.

Każdy znak przesłany do funkcji translate jest liczony nawet wtedy, gdy zawartość nie zostanie zmieniona lub gdy język źródłowy i docelowy są takie same.

Więcej informacji na temat liczby znaków znajduje się w artykule [Translator FAQ (Często zadawane pytania dotyczące usługi Translator).](https://www.microsoft.com/en-us/translator/faq.aspx)
