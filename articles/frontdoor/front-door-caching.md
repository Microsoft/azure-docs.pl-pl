---
title: Buforowanie z przodu platformy Azure | Microsoft Docs
description: Ten artykuł ułatwia zapoznanie się z zachowaniem czołowych drzwi z regułami routingu, które obsługują buforowanie.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: d001a7a24d44c46a19bde08051e21d3ae3c5acb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538055"
---
# <a name="caching-with-azure-front-door"></a>Buforowanie z usługami frontonu platformy Azure
Poniższy dokument określa zachowania dla drzwi zewnętrznych z regułami routingu, które mają włączone buforowanie. Przód drzwi to nowoczesne Content Delivery Network (CDN) z funkcją dynamicznego przyspieszania witryn i równoważenia obciążenia, ale również obsługuje zachowania buforowania, podobnie jak w przypadku innych sieci CDN.

## <a name="delivery-of-large-files"></a>Dostarczanie dużych plików
Drzwi frontonu platformy Azure zapewniają duże pliki bez limitu rozmiaru pliku. Drzwi przednich wykorzystują technikę nazywaną fragmentem obiektu. Jeśli są żądane duże pliki, usługa Front Door pobiera mniejsze części pliku z zaplecza. Po otrzymaniu żądania pliku o pełnym lub bajtowym rozmiarze środowisko z przodu zażąda pliku z zaplecza w fragmentach 8 MB.

Po nadejściu fragmentu w środowisku z drzwiami jest on buforowany i natychmiast obsługiwany przez użytkownika. Następnie przede wszystkim wstępnie pobiera Następny fragment równolegle. To wstępne pobranie zapewnia, że zawartość pozostaje jednym fragmentem przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany, dopóki cały plik nie zostanie pobrany (jeśli będzie to wymagane) lub klient zamknie połączenie.

Aby uzyskać więcej informacji na temat żądania zakresu bajtów, przeczytaj artykuł [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Drzwi z przodu buforują wszystkie fragmenty po odebraniu, aby cały plik nie musiał być buforowany w pamięci podręcznej z przodu. Żądania dotyczące plików lub zakresów bajtów są obsługiwane z pamięci podręcznej. Jeśli fragmenty nie są przechowywane w pamięci podręcznej, wstępne pobieranie jest używane do żądania fragmentów z zaplecza. Ta optymalizacja polega na możliwości obsługi żądań o zakresie bajtów przez wewnętrzną bazę danych. Jeśli zaplecze nie obsługuje żądań zakresu bajtów, Ta optymalizacja nie obowiązuje.

## <a name="file-compression"></a>Kompresja pliku
Drzwi tylne umożliwiają dynamiczne kompresowanie zawartości na krawędzi, co powoduje skrócenie i krótsze czas odpowiedzi dla klientów. Aby plik mógł być objęty kompresją, należy włączyć buforowanie, a plik musi być typu MIME, aby kwalifikować się do kompresji. Obecnie przed drzwiami nie zezwala się na zmianę tej listy. Bieżąca lista:
- "Application/EOT"
- "aplikacja/czcionka"
- "Application/Font-sfnt"
- "aplikacja/JavaScript"
- „application/json”
- "Application/OpenType"
- "Application/OTF"
- "Application/PKCS7-MIME"
- "Application/TrueType"
- "Application/ttf",
- "application/vnd. MS-fontobject"
- "Application/XHTML + XML"
- "Application/XML"
- "Application/XML + RSS"
- "application/x-Font-OpenType"
- "application/x-Font-TrueType"
- "application/x-Font-ttf"
- "application/x-http-CGI"
- "application/x-mpegurl"
- "application/x-OpenType"
- "application/x-OTF"
- "application/x-Perl"
- "application/x-ttf"
- "application/x-JavaScript"
- "Font/EOT"
- "Font/ttf"
- "Font/OTF"
- "Font/OpenType"
- "Image/SVG + XML"
- "tekst/CSS"
- "tekst/CSV"
- "text/html"
- "tekst/JavaScript"
- "text/js", "text/zwykły"
- "tekst/RTF"
- "tekst/tabulator — wartości rozdzielane"
- "text/xml"
- "tekst/x-Script"
- "tekst/x-składnik"
- "tekst/x-Java-Source"

Ponadto plik musi mieć rozmiar od 1 KB do 8 MB włącznie.

Te profile obsługują następujące kodowania kompresji:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Jeśli żądanie obsługuje kompresję gzip i Brotli, ma pierwszeństwo kompresja Brotli.</br>
Gdy żądanie dotyczące elementu zawartości określa kompresję, a żądanie spowoduje odrzucenie pamięci podręcznej, jego drzwi są ściskane bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej. Wynikowy element jest zwracany przy użyciu kodowania Transfer-Encoding:.

## <a name="query-string-behavior"></a>Zachowanie ciągu zapytania
Za pomocą przednich drzwi można kontrolować sposób, w jaki pliki są buforowane dla żądania sieci Web, które zawiera ciąg zapytania. W żądaniu sieci Web za pomocą ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać co najmniej jedną parę klucz-wartość, w której nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona znakiem handlowego "i" (&). Na przykład `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Jeśli istnieje więcej niż jedna para klucz-wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia.
- **Ignoruj ciągi zapytań**: w tym trybie tylne drzwiczki przekazują ciągi zapytania od osoby żądającej do zaplecza przy pierwszym żądaniu i buforują element zawartości. Wszystkie żądania dotyczące elementu zawartości, które są obsługiwane przez środowisko z przodu, Ignoruj ciągi zapytania do momentu wygaśnięcia zbuforowanego elementu zawartości.

- **Buforuj każdy unikatowy adres URL**: w tym trybie każde żądanie z unikatowym adresem URL, łącznie z ciągiem zapytania, jest traktowane jako unikatowy element zawartości z własną pamięcią podręczną. Na przykład odpowiedź z zaplecza dla żądania `www.example.ashx?q=test1` jest buforowana w środowisku przednim i zwrócona dla operacji w pamięci podręcznej z tym samym ciągiem zapytania. Żądanie dla `www.example.ashx?q=test2` jest zapisywane w pamięci podręcznej jako osobny zasób z własnym ustawieniem czasu wygaśnięcia.

## <a name="cache-purge"></a>Przeczyszczanie pamięci podręcznej

Zasoby w pamięci podręcznej są umieszczane do momentu wygaśnięcia czasu wygaśnięcia elementu zawartości. Za każdym razem, gdy klient zażąda elementu zawartości z wygasłym czasem wygaśnięcia, środowisko z przodu pobiera nową zaktualizowaną kopię zasobu do realizacji żądania, a następnie przechowuje odświeżoną pamięć podręczną.

Najlepszym rozwiązaniem, aby upewnić się, że użytkownicy zawsze uzyskują najnowszą kopię zasobów, są w wersji zasobów dla każdej aktualizacji i publikują je jako nowe adresy URL. Drzwi z przodu natychmiast spowodują pobranie nowych zasobów dla kolejnych żądań klientów. Czasami możesz chcieć przeczyścić zawartość pamięci podręcznej ze wszystkich węzłów brzegowych i wymusić, aby wszystkie nowe zasoby były pobierane. Przyczyną może być aktualizacja aplikacji sieci Web lub szybkie aktualizowanie zasobów zawierających nieprawidłowe informacje.

Wybierz zasoby, które chcesz przeczyścić z węzłów krawędzi. Aby wyczyścić wszystkie zasoby, wybierz pozycję **Przeczyść wszystko**. W przeciwnym razie w polu **ścieżka** wprowadź ścieżkę do każdego zasobu, który ma zostać przeczyszczony.

Te formaty są obsługiwane na listach ścieżek do przeczyszczenia:

- **Przeczyszczanie pojedynczej ścieżki**: Przeczyść pojedyncze zasoby, określając pełną ścieżkę elementu zawartości (bez protokołu i domeny), z rozszerzeniem pliku, na przykład/Pictures/strasbourg.png;
- **Przeczyszczanie symboli wieloznacznych**: gwiazdka ( \* ) może być używana jako symbol wieloznaczny. Przeczyść wszystkie foldery, podfoldery i pliki w punkcie końcowym ze \* ścieżką lub Przeczyść wszystkie podfoldery i pliki w określonym folderze, określając folder \* , a na przykład/Pictures/ \* .
- **Przeczyszczanie domeny głównej**: Przeczyść element główny punktu końcowego z "/" w ścieżce.

> [!NOTE]
> **Przeczyszczanie domen symboli wieloznacznych**: określenie zbuforowanych ścieżek do przeczyszczania, zgodnie z opisem w tej sekcji, nie dotyczy żadnych domen wieloznacznych, które są skojarzone z drzwiami przednimi. Obecnie nie obsługujemy bezpośrednio przeczyszczania domen wieloznacznych. Ścieżki z określonych poddomen można przeczyścić, określając tę poddomenę specfic i ścieżkę przeczyszczania. Na przykład, jeśli moje tylne drzwi mają `*.contoso.com` , można przeczyścić zasoby mojej poddomeny `foo.contoso.com` przez wpisanie `foo.contoso.com/path/*` . Obecnie Określanie nazw hostów w ścieżce zawartości przeczyszczania jest imited do poddomen domen symboli wieloznacznych, jeśli ma zastosowanie.
>

Przeczyszczanie pamięci podręcznej w przypadku drzwi przednich nie uwzględnia wielkości liter. Ponadto są to ciąg zapytania niezależny od, co oznacza, że przeczyszczanie adresu URL spowoduje przeczyszczenie wszystkich odmian ciągu zapytania. 

## <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej
Następująca kolejność nagłówków służy do określenia, jak długo element będzie przechowywany w pamięci podręcznej:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Cache-Control: max-age =\<seconds>
3. Wygasł \<http-date>

Cache-Control nagłówki odpowiedzi wskazujące, że odpowiedź nie będzie buforowana, taka jak Cache-Control: Private, Cache-Control: Brak pamięci podręcznej i kontrola pamięci podręcznej: żaden magazyn nie jest uznawany za.  Jeśli żadna Cache-Control nie jest obecna, domyślne zachowanie polega na tym, że drzwi z przodu będą buforować zasób przez X ilość czasu, gdzie X zostanie losowo pobrany z przedziału od 1 do 3 dni.

## <a name="request-headers"></a>Nagłówki żądań

Następujące nagłówki żądania nie będą przekazywane do zaplecza podczas korzystania z pamięci podręcznej.
- Długość zawartości
- Transfer-Encoding

## <a name="cache-duration"></a>Czas trwania pamięci podręcznej

Czas trwania pamięci podręcznej można skonfigurować zarówno w projektancie przednim, jak i w aparacie reguł. Czas trwania pamięci podręcznej ustawiony w projektancie drzwi przednich to minimalny czas trwania pamięci podręcznej. To przesłonięcie nie będzie działało, jeśli nagłówek kontroli pamięci podręcznej ze źródła ma większy czas wygaśnięcia niż wartość zastąpienia. 

Czas trwania pamięci podręcznej ustawiony przez aparat reguł to prawdziwe przesłonięcie pamięci podręcznej, co oznacza, że zostanie użyta wartość zastąpienia niezależnie od tego, jaki jest nagłówek odpowiedzi pochodzenia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
