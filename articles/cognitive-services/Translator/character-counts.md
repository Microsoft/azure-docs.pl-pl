---
title: Liczba znaków — translator
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak usługa Azure Cognitive Services translator zlicza znaki, aby zrozumieć, jak pozyskuje zawartość.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7fad90cf47591b5ffab4232c2be3180138738f01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83992837"
---
# <a name="how-the-translator-counts-characters"></a>Jak translator zlicza znaki

Translator zlicza każdy punkt kodu Unicode tekstu wejściowego jako znak. Każde tłumaczenie tekstu na język jest traktowane jako oddzielne tłumaczenie, nawet jeśli żądanie zostało wykonane w jednym wywołaniu interfejsu API translacji w wielu językach. Długość odpowiedzi nie ma znaczenia.

Jakie są następujące zliczenia:

* Tekst przesłany do usługi Translator w treści żądania
   * `Text` w przypadku korzystania z metod przeszukiwania, transliteracji i słownika
   * `Text` i `Translation` w przypadku korzystania z przykładów słownika
* Wszystkie znaczniki: HTML, tagi XML itp. w polu tekstowym treści żądania. Nie zliczane jest notacja JSON użyta do skompilowania żądania (na przykład "Text:").
* Pojedyncza litera
* Znaki interpunkcyjne
* Spacja, tabulator, znacznik i dowolny znak znaku odstępu
* Każdy punkt kodowy zdefiniowany w kodzie Unicode
* Wielokrotne tłumaczenie, nawet jeśli wcześniej przetłumaczy ten sam tekst

W przypadku skryptów opartych na ideograms, takich jak chiński i japoński Kanji, usługa translatora nadal zlicza liczbę punktów kodowych Unicode, jeden znak na ideogram. Wyjątek: Liczba surogatów Unicode jest liczona jako dwa znaki.

Liczba żądań, słów, bajtów lub zdań jest nieistotna w liczbie znaków.

Wywołania metod wykrywania i BreakSentence nie są zliczane w zużyciu znaków. Jednak oczekujemy, że wywołania metod wykrywania i BreakSentence są w rozsądny sposób proporcjonalne do korzystania z innych funkcji, które są zliczane. Jeśli liczba wykonywanych wywołań wykrywania lub BreakSentence przekracza liczbę innych zliczonych metod o 100 razy, firma Microsoft zastrzega sobie prawo do ograniczenia użycia metod wykrywania i BreakSentence.

Więcej informacji o liczbie znaków znajduje się w [sekcji często zadawane pytania dotyczące translatora](https://www.microsoft.com/en-us/translator/faq.aspx).
