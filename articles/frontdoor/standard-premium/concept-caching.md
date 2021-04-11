---
title: 'Moje drzwi platformy Azure: buforowanie'
description: Ten artykuł pomaga zrozumieć zachowanie standardu/Premium platformy Azure przy użyciu reguł routingu z włączonym buforowaniem.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 63ea252a4b4c673ae3028adb7ab793ac21fb2e99
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564589"
---
# <a name="caching-with-azure-front-door-standardpremium-preview"></a>Buforowanie przy użyciu standardu Front/Premium platformy Azure (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

W tym artykule dowiesz się, jak trasy i zestawy reguł w warstwie Standardowa/Premium (wersja zapoznawcza) są zachowywane po włączeniu buforowania. Azure Front Drzwiczks to nowoczesne Content Delivery Network (CDN) z funkcją dynamicznego przyspieszania witryn i równoważenia obciążenia.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="request-methods"></a>Metody żądań

Tylko Metoda GET Request może generować zawartość z pamięci podręcznej w ramach zewnętrznych drzwi platformy Azure. Wszystkie pozostałe metody żądań są zawsze przekazywane za pomocą sieci.

## <a name="delivery-of-large-files"></a>Dostarczanie dużych plików

Warstwy przód i warstwa Premium (wersja zapoznawcza) zapewniają duże pliki bez limitu rozmiaru pliku. Drzwi przednich wykorzystują technikę nazywaną fragmentem obiektu. Po zażądaniu dużego pliku drzwi do przodu pobierają mniejsze fragmenty pliku ze źródła. Po otrzymaniu żądania pliku o pełnym lub bajtowym rozmiarze środowisko z przodu żąda pliku od źródeł w fragmentach 8 MB.

Po nadejściu fragmentu w środowisku z drzwiami jest on buforowany i natychmiast obsługiwany przez użytkownika. Następnie przede wszystkim wstępnie pobiera Następny fragment równolegle. To wstępne pobranie zapewnia, że zawartość pozostaje jednym fragmentem przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany, dopóki cały plik nie zostanie pobrany (jeśli będzie to wymagane) lub klient zamknie połączenie.

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, przeczytaj artykuł [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Drzwi z przodu buforują wszystkie fragmenty po odebraniu, aby cały plik nie musiał być buforowany w pamięci podręcznej z przodu. Żądania dotyczące plików lub zakresów bajtów są obsługiwane z pamięci podręcznej. Jeśli fragmenty nie są przechowywane w pamięci podręcznej, wstępne pobieranie jest używane do żądania fragmentów z zaplecza. Ta optymalizacja opiera się na możliwościach źródła do obsługi żądań zakresu bajtów. Jeśli źródło nie obsługuje żądań zakresu bajtów, Ta optymalizacja nie jest skuteczna.

## <a name="file-compression"></a>Kompresja pliku

Zapoznaj się z tematem poprawa wydajności dzięki kompresji plików na platformie Azure.

## <a name="query-string-behavior"></a>Zachowanie ciągu zapytania

Za pomocą przednich drzwi można kontrolować sposób, w jaki pliki są buforowane dla żądania sieci Web, które zawiera ciąg zapytania. W żądaniu sieci Web za pomocą ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać co najmniej jedną parę klucz-wartość, w której nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona znakiem handlowego "i" (&). Na przykład `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Jeśli istnieje więcej niż jedna para klucz-wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia.

* **Ignoruj ciągi zapytań**: w tym trybie tylne drzwiczki przekazują ciągi zapytania od osoby żądającej do źródła przy pierwszym żądaniu i buforują element zawartości. Wszystkie żądania dotyczące elementu zawartości, które są obsługiwane przez środowisko z przodu, Ignoruj ciągi zapytania do momentu wygaśnięcia zbuforowanego elementu zawartości.

* **Buforuj każdy unikatowy adres URL**: w tym trybie każde żądanie z unikatowym adresem URL, łącznie z ciągiem zapytania, jest traktowane jako unikatowy element zawartości z własną pamięcią podręczną. Na przykład odpowiedź z lokalizacji źródłowej dla żądania `www.example.ashx?q=test1` jest buforowana w środowisku przednim i zwracana do wykonywania operacji w pamięci podręcznej z tym samym ciągiem zapytania. Żądanie dla `www.example.ashx?q=test2` jest zapisywane w pamięci podręcznej jako osobny zasób z własnym ustawieniem czasu wygaśnięcia.
* Można również użyć zestawu reguł, aby określić zachowanie **ciągu zapytania klucza pamięci podręcznej** , aby uwzględnić lub wykluczyć określone parametry po wygenerowaniu klucza pamięci podręcznej. Na przykład domyślny klucz pamięci podręcznej to:/foo/Image/asset.html, a przykładowe żądanie to `https://contoso.com//foo/image/asset.html?language=EN&userid=100&sessionid=200` . Istnieje reguła zestawu reguł służąca do wykluczania ciągu zapytania "userid". Następnie klucz buforu ciągu zapytania `/foo/image/asset.html?language=EN&sessionid=200` .

## <a name="cache-purge"></a>Przeczyszczanie pamięci podręcznej

Zapoznaj się z przeczyszczaniem pamięci podręcznej.

## <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej
Następująca kolejność nagłówków służy do określenia, jak długo element będzie przechowywany w pamięci podręcznej:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Cache-Control: max-age =\<seconds>
3. Wygasł \<http-date>

Cache-Control nagłówki odpowiedzi wskazujące, że odpowiedź nie będzie buforowana, taka jak Cache-Control: Private, Cache-Control: Brak pamięci podręcznej i kontrola pamięci podręcznej: żaden magazyn nie jest uznawany za.  Jeśli Cache-Control nie istnieje, zachowanie domyślne polega na tym, że drzwi z przodu będą buforować zasób przez X ilość czasu. Gdzie X jest losowo pobierana z zakresu od 1 do 3 dni.

## <a name="request-headers"></a>Nagłówki żądań

Następujące nagłówki żądania nie będą przekazywane do pochodzenia podczas korzystania z buforowania.
* Długość zawartości
* Transfer-Encoding

## <a name="cache-duration"></a>Czas trwania pamięci podręcznej

Czas trwania pamięci podręcznej można skonfigurować w zestawie reguł. Czas trwania pamięci podręcznej ustawiony za pośrednictwem reguł jest przesłonięciem prawdziwej pamięci podręcznej. Oznacza to, że zostanie użyta wartość zastąpienia niezależnie od tego, co jest nagłówkiem odpowiedzi pochodzenia.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [warunkach dopasowania zestawu reguł](concept-rule-set-match-conditions.md)
* Dowiedz się więcej o [akcjach zestawu reguł](concept-rule-set-actions.md)