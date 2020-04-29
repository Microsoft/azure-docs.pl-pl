---
title: Przesunięcia tekstu w interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat przesunięć spowodowanych przez kodowania wielojęzyczne i Emoji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219237"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Przesunięcie tekstu w danych wyjściowych interfejs API analizy tekstu

Obsługa wielojęzycznych i Emoji prowadzi do kodowania Unicode, które używają więcej niż jednego [punktu kodu](https://wikipedia.org/wiki/Code_point) do reprezentowania pojedynczego wyświetlanego znaku o nazwie Grapheme. Na przykład znaki emoji, takie jak 🌷 👍 i mogą używać kilku znaków do redagowania kształtu z dodatkowymi znakami dla atrybutów wizualnych, takich jak odcienie skórki. Podobnie słowo `अनुच्छेद` w języku hindi jest kodowane jako pięć liter i trzy znaki łączące.

Ze względu na różne długości możliwych kodowań wielojęzycznych i emoji, interfejs API analizy tekstu mogą zwracać przesunięcia w odpowiedzi.

## <a name="offsets-in-the-api-response"></a>Przesunięcia w odpowiedzi interfejsu API. 

Za każdym razem, gdy przesunięcia są zwracane odpowiedzi interfejsu API, takie jak [rozpoznawanie jednostek nazwanych](../how-tos/text-analytics-how-to-entity-linking.md) lub [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md), należy pamiętać o następujących kwestiach:

* Elementy w odpowiedzi mogą być specyficzne dla punktu końcowego, który został wywołany. 
* Ładunki POST/GET protokołu HTTP są kodowane w [formacie UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), co może być domyślne lub nie może być domyślnym kodowaniem znaków w kompilatorze po stronie klienta lub systemie operacyjnym.
* Przesunięcia odnoszą się do liczby Grapheme na podstawie standardu [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) , a nie liczby znaków.

## <a name="extracting-substrings-from-text-with-offsets"></a>Wyodrębnianie podciągów z tekstu z przesunięciami

Przesunięcia mogą spowodować problemy podczas korzystania z metod podciągów opartych na znakach, na przykład w przypadku metody [podciągu .NET ()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) . Jeden z problemów polega na tym, że przesunięcie może spowodować zakończenie metody podciągu w środku wieloznakowego kodowania Grapheme zamiast na końcu.

W programie .NET Rozważ użycie klasy [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) , która umożliwia współpracę z ciągiem jako serią elementów tekstowych, a nie pojedynczymi obiektami znaków. Możesz również wyszukać biblioteki rozdzielacza Grapheme w preferowanym środowisku oprogramowania. 

Interfejs API analizy tekstu zwraca te elementy tekstowe również dla wygody.

## <a name="see-also"></a>Zobacz także

* [Przegląd analiza tekstu](../overview.md)
* [Analiza tonacji](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznawanie jednostek](../how-tos/text-analytics-how-to-entity-linking.md)
* [Wykrywanie języka](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Rozpoznawanie języka](../how-tos/text-analytics-how-to-language-detection.md)
