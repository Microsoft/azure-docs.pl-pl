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
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 9279b3e77147449ae0ede0cc0b76e57f130c9a44
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398035"
---
# <a name="caching-with-azure-front-door"></a>Buforowanie z usługami frontonu platformy Azure
Poniższy dokument określa zachowanie dla drzwi z przodu z regułami routingu, które mają włączone buforowanie. Przód drzwi to nowoczesne Content Delivery Network (CDN) i tak, jak za pomocą funkcji przyspieszania witryn dynamicznych i równoważenia obciążenia, obsługuje również zachowania buforowania tak samo jak w przypadku innych sieci CDN.

## <a name="delivery-of-large-files"></a>Dostarczanie dużych plików
Drzwi frontonu platformy Azure zapewniają duże pliki bez limitu rozmiaru pliku. Drzwi przednich wykorzystują technikę nazywaną fragmentem obiektu. Jeśli są żądane duże pliki, usługa Front Door pobiera mniejsze części pliku z zaplecza. Po otrzymaniu żądania dotyczącego całego pliku lub zakresu bajtów pliku usługa Front Door żąda pliku z zaplecza we fragmentach po 8 MB.

</br>Po nadejściu fragmentu w środowisku z drzwiami jest on buforowany i natychmiast obsługiwany przez użytkownika. Następnie przede wszystkim wstępnie pobiera Następny fragment równolegle. To wstępne pobranie zapewnia, że zawartość pozostaje jednym fragmentem przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli będzie to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient przerwał połączenie.

</br>Aby uzyskać więcej informacji na temat żądania zakresu bajtów, przeczytaj artykuł [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Drzwi z przodu buforują wszystkie fragmenty, gdy są odbierane, i dlatego cały plik nie musi być buforowany w pamięci podręcznej z przodu. Kolejne żądania dla plików lub zakresów bajtów są obsługiwane z pamięci podręcznej. Jeśli nie wszystkie fragmenty są buforowane, wstępne pobieranie jest używane do żądania fragmentów z zaplecza. Ta optymalizacja opiera się na możliwości wewnętrznej bazy danych obsługującej żądania zakresu bajtów; Jeśli zaplecze nie obsługuje żądań zakresu bajtów, Ta optymalizacja nie obowiązuje.

## <a name="file-compression"></a>Kompresja pliku
Drzwi przednich umożliwiają dynamiczne kompresowanie zawartości na krawędzi, co pozwala na wypróbowanie klientów w krótszej i krótszej reakcji. Wszystkie pliki kwalifikują się do kompresji. Jednak plik musi być typu MIME, który kwalifikuje się do listy kompresji. Obecnie przed drzwiami nie zezwala się na zmianę tej listy. Bieżąca lista:</br>
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
Gdy żądanie dotyczące elementu zawartości określa kompresję, a żądanie spowoduje odrzucenie pamięci podręcznej, do przodu wykonuje kompresję elementu zawartości bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej. Wynikowy element jest zwracany przy użyciu kodowania Transfer-Encoding:.

## <a name="query-string-behavior"></a>Zachowanie ciągu zapytania
Za pomocą przednich drzwi można kontrolować sposób, w jaki pliki są buforowane dla żądania sieci Web, które zawiera ciąg zapytania. W żądaniu sieci Web za pomocą ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać co najmniej jedną parę klucz-wartość, w której nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona znakiem handlowego "i" (&). Na przykład `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Jeśli w ciągu zapytania żądania występuje więcej niż jedna para klucz-wartość, ich kolejność nie ma znaczenia.
- **Ignoruj ciągi zapytań**: w tym trybie tylne drzwiczki przekazują ciągi zapytania od osoby żądającej do zaplecza przy pierwszym żądaniu i buforują element zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są obsługiwane przez środowisko front-drzwi, ignorują ciągi zapytania do momentu wygaśnięcia pamięci podręcznej.

- **Buforuj każdy unikatowy adres URL**: w tym trybie każde żądanie z unikatowym adresem URL, łącznie z ciągiem zapytania, jest traktowane jako unikatowy element zawartości z własną pamięcią podręczną. Na przykład odpowiedź z zaplecza dla żądania `www.example.ashx?q=test1` jest buforowana w środowisku przednim i zwracana dla kolejnych pamięci podręcznych z tym samym ciągiem zapytania. Żądanie dla `www.example.ashx?q=test2` jest zapisywane w pamięci podręcznej jako osobny zasób z własnym ustawieniem czasu wygaśnięcia.

## <a name="cache-purge"></a>Przeczyszczanie pamięci podręcznej

Zasoby w pamięci podręcznej są umieszczane do momentu wygaśnięcia czasu wygaśnięcia elementu zawartości. Po upływie czasu wygaśnięcia zasobu, gdy klient zażąda zasobu, środowisko z przodu pobiera nową zaktualizowaną kopię elementu zawartości, która będzie obsługiwała żądanie klienta i zapisuje Odświeżanie pamięci podręcznej.

Najlepszym rozwiązaniem, aby upewnić się, że użytkownicy zawsze uzyskują najnowszą kopię zasobów, są w wersji zasobów dla każdej aktualizacji i publikują je jako nowe adresy URL. Drzwi z przodu natychmiast spowodują pobranie nowych zasobów dla kolejnych żądań klientów. Czasami możesz chcieć przeczyścić zawartość pamięci podręcznej ze wszystkich węzłów brzegowych i wymusić, aby wszystkie nowe zasoby były pobierane. Może to być spowodowane aktualizacjami aplikacji sieci Web lub szybkiej aktualizacji zasobów zawierających nieprawidłowe informacje.

Wybierz zasoby, które chcesz przeczyścić z węzłów krawędzi. Aby wyczyścić wszystkie zasoby, wybierz pozycję **Przeczyść wszystko**. W przeciwnym razie w polu **ścieżka**wprowadź ścieżkę do każdego zasobu, który ma zostać przeczyszczony.

Te formaty są obsługiwane na listach ścieżek do przeczyszczenia:

- **Przeczyszczanie pojedynczej ścieżki**: Przeczyść pojedyncze zasoby, określając pełną ścieżkę elementu zawartości (bez protokołu i domeny), z rozszerzeniem pliku, na przykład/Pictures/strasbourg.png;
- **Przeczyszczanie symboli wieloznacznych**: gwiazdka ( \* ) może być używana jako symbol wieloznaczny. Przeczyść wszystkie foldery, podfoldery i pliki w punkcie końcowym ze \* ścieżką lub Przeczyść wszystkie podfoldery i pliki w określonym folderze, określając folder \* , a na przykład/Pictures/ \* .
- **Przeczyszczanie domeny głównej**: Przeczyść element główny punktu końcowego z "/" w ścieżce.

> [!NOTE]
> **Przeczyszczanie domen symboli wieloznacznych**: określenie zbuforowanych ścieżek do przeczyszczania, zgodnie z opisem w tej sekcji, nie dotyczy żadnych domen wieloznacznych, które są skojarzone z drzwiami przednimi. Obecnie nie obsługujemy bezpośrednio przeczyszczania domen wieloznacznych. Ścieżki z określonych poddomen można przeczyścić, określając tę poddomenę specfic i ścieżkę przeczyszczania. Na przykład, jeśli moje tylne drzwi mają `*.contoso.com` , można przeczyścić zasoby mojej poddomeny `foo.contoso.com` przez wpisanie `foo.contoso.com/path/*` . Obecnie Określanie nazw hostów w ścieżce zawartości przeczyszczania jest imited do poddomen domen symboli wieloznacznych, jeśli ma zastosowanie.
>

Przeczyszczanie pamięci podręcznej w przypadku drzwi przednich nie uwzględnia wielkości liter. Ponadto są to ciąg zapytania niezależny od, co oznacza, że przeczyszczanie adresu URL spowoduje przeczyszczenie wszystkich odmian ciągu zapytania. 

## <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej
Następująca kolejność nagłówków jest używana w celu określenia czasu przechowywania elementu w pamięci podręcznej:</br>
1. Cache-Control: s-maxage =\<seconds>
2. Cache-Control: max-age =\<seconds>
3. Wygasł \<http-date>

Nagłówki odpowiedzi kontroli pamięci podręcznej, wskazujące, że odpowiedź nie będzie buforowana, taka jak Cache-Control: Private, Cache-Control: Brak pamięci podręcznej i kontrola pamięci podręcznej: żaden magazyn nie jest uznawany.  Jeśli żadna kontrola pamięci podręcznej nie jest obecna, domyślnym zachowaniem jest to, że AFD będzie buforować zasób dla X czasu, gdzie X jest losowo wybierany z przedziału od 1 do 3 dni.

## <a name="request-headers"></a>Nagłówki żądań

Następujące nagłówki żądania nie będą przekazywane do zaplecza podczas korzystania z pamięci podręcznej.
- Długość zawartości
- Transfer-Encoding

## <a name="cache-duration"></a>Czas trwania pamięci podręcznej

Czas trwania pamięci podręcznej można skonfigurować zarówno w projektancie przednim, jak i w aparacie reguł. Czas trwania pamięci podręcznej ustawiony w programie usługa frontdoor Designer to minimalny czas trwania pamięci podręcznej. To przesłonięcie nie będzie działało, jeśli nagłówek kontroli pamięci podręcznej ze źródła ma większy czas wygaśnięcia niż wartość zastąpienia. 

Czas trwania pamięci podręcznej ustawiony przez aparat reguł to prawdziwe przesłonięcie pamięci podręcznej, co oznacza, że zostanie użyta wartość zastąpienia, niezależnie od tego, jaki jest nagłówek odpowiedzi pochodzenia.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
