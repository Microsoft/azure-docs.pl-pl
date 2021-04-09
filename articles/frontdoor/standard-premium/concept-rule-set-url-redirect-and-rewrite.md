---
title: Przekierowywanie adresów URL i ponowne zapisywanie adresów URL przy użyciu platformy Azure Front-Standard/Premium (wersja zapoznawcza)
description: Ten artykuł pomaga zrozumieć, w jaki sposób platforma Azure front-drzwi obsługuje przekierowywanie adresów URL i ponowne zapisywanie adresów URL przy użyciu zestawu reguł dla drzwi platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100006"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Przekierowywanie adresów URL i ponowne zapisywanie adresów URL przy użyciu platformy Azure Front-Standard/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Ten artykuł pomaga zrozumieć, w jaki sposób wersja Standard/Premium platformy Azure obsługuje przekierowywanie adresów URL i ponowne zapisywanie adresów URL użytych w zestawie reguł.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Przekierowywanie adresów URL

Drzwi frontonu platformy Azure mogą przekierowywać ruch na każdym z następujących poziomów: protokołu, nazwy hosta, ścieżki, ciągu zapytania i fragmentu. Te funkcje można skonfigurować dla poszczególnych mikrousług, ponieważ przekierowanie jest oparte na ścieżce. Przekierowywanie adresów URL pozwala uprościć konfigurację aplikacji przez optymalizację użycia zasobów i obsługuje nowe scenariusze przekierowania, w tym globalne i oparte na ścieżce przekierowywanie.

Przekierowanie adresu URL można skonfigurować za pomocą zestawu reguł.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Zrzut ekranu przedstawiający tworzenie przekierowań adresów URL z zestawem reguł." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Typy przekierowań
Typ przekierowania ustawia kod stanu odpowiedzi dla klientów, aby zrozumieć przeznaczenie przekierowania. Obsługiwane są następujące typy przekierowań:

* **301 (trwale przeniesiono)**: wskazuje, że do zasobu docelowego przypisano nowy stały identyfikator URI. Wszystkie przyszłe odwołania do tego zasobu będą używać jednego z załączonych identyfikatorów URI. Użyj kodu stanu 301 do przekierowania HTTP do HTTPS.
* **302 (znaleziono)**: wskazuje, że zasób docelowy jest tymczasowo pod innym identyfikatorem URI. Ponieważ przekierowanie może ulec zmianie, klient powinien nadal używać obowiązującego identyfikatora URI żądania dla przyszłych żądań.
* **307 (tymczasowe przekierowanie)**: wskazuje, że zasób docelowy jest tymczasowo pod innym identyfikatorem URI. Agent użytkownika nie może zmienić metody żądania, jeśli wykonuje automatyczne przekierowanie do tego identyfikatora URI. Ponieważ przekierowanie może ulec zmianie z upływem czasu, klient powinien kontynuować korzystanie z oryginalnego identyfikatora URI żądania dla przyszłych żądań.
* **308 (stałe przekierowanie)**: wskazuje, że do zasobu docelowego przypisano nowy stały identyfikator URI. Wszystkie przyszłe odwołania do tego zasobu powinny używać jednego z załączonych identyfikatorów URI.

### <a name="redirection-protocol"></a>Protokół przekierowania
Można ustawić protokół, który będzie używany do przekierowywania. Najczęstszym przypadkiem użycia funkcji redirect jest ustawienie przekierowania HTTP na HTTPS.

* **Tylko https**: Ustaw tylko protokół https, jeśli chcesz przekierować ruch z protokołu HTTP do protokołu HTTPS. Przed drzwiami platformy Azure zaleca się, aby zawsze ustawiać przekierowanie tylko do protokołu HTTPS.
* **Tylko http**: przekierowuje żądanie przychodzące do protokołu HTTP. Użyj tej wartości tylko wtedy, gdy chcesz zachować ruch HTTP, który jest nieszyfrowany.
* **Żądanie dopasowania**: Ta opcja utrzymuje protokół używany przez żądanie przychodzące. W związku z tym żądanie HTTP pozostaje HTTP, a żądanie HTTPS pozostaje przekierowaniem post HTTPS.

### <a name="destination-host"></a>Host docelowy
W ramach konfigurowania routingu przekierowywania można także zmienić nazwę hosta lub domenę dla żądania przekierowania. Można ustawić to pole, aby zmienić nazwę hosta w adresie URL przekierowania lub w przeciwnym razie zachować nazwę hosta z żądania przychodzącego. Dlatego przy użyciu tego pola można przekierować wszystkie żądania wysłane `https://www.contoso.com/*` do usługi `https://www.fabrikam.com/*` .

### <a name="destination-path"></a>Ścieżka docelowa
W przypadku, gdy chcesz zastąpić segment ścieżki adresu URL w ramach przekierowania, możesz ustawić to pole z nową wartością ścieżki. W przeciwnym razie można zachować wartość ścieżki w ramach przekierowania. Dlatego przy użyciu tego pola można przekierować wszystkie żądania wysyłane do `https://www.contoso.com/\*` programu  `https://www.contoso.com/redirected-site` .

### <a name="query-string-parameters"></a>Parametry ciągu zapytania
Parametry ciągu zapytania można również zastąpić w przekierowanym adresie URL. Aby zastąpić wszystkie istniejące parametry zapytania z przychodzącego adresu URL żądania, należy ustawić to pole na wartość "Zamień", a następnie ustawić odpowiednie wartości. W przeciwnym razie można zachować oryginalny zestaw ciągów zapytania, ustawiając pole na wartość "preserve". Na przykład przy użyciu tego pola można przekierować cały ruch wysyłany do usługi `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

### <a name="destination-fragment"></a>Fragment docelowy
Fragment docelowy jest częścią adresu URL po "#", który jest używany przez przeglądarkę do gruntów w określonej sekcji strony sieci Web. Można ustawić to pole, aby dodać fragment do adresu URL przekierowania.

## <a name="url-rewrite"></a>Regenerowanie adresów URL

Usługa Azure Front-drzwiczk obsługuje ponowne zapisywanie adresów URL w celu ponownego zapisania ścieżki żądania, które jest trasy do źródła. Ponowne zapisywanie adresów URL pozwala dodawać warunki w celu upewnienia się, że adres URL lub określone nagłówki zostaną zapisane tylko wtedy, gdy zostaną spełnione określone warunki. Te warunki są oparte na informacjach dotyczących żądania i odpowiedzi.

Za pomocą tej funkcji można przekierować użytkowników do różnych źródeł na podstawie scenariusza, typu urządzenia i żądanego typu pliku.

Przekierowanie adresu URL można skonfigurować za pomocą zestawu reguł.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Zrzut ekranu przedstawiający tworzenie adresu URL do ponownego zapisu z zestawem reguł." lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Wzorzec źródła

Wzorzec źródłowy jest ścieżką URL w żądaniu źródłowym do zastąpienia. Obecnie wzorzec źródłowy używa dopasowania opartego na prefiksie. Aby dopasować wszystkie ścieżki URL, użyj ukośnika (/) jako wartości wzorca źródła.

### <a name="destination"></a>Element docelowy

Można zdefiniować ścieżkę docelową, która będzie używana podczas ponownego zapisywania. Ścieżka docelowa zastępuje wzorzec źródłowy.

### <a name="preserve-unmatched-path"></a>Zachowaj niedopasowaną ścieżkę

Zachowanie niedopasowanej ścieżki umożliwia dołączenie pozostałej ścieżki po wzorcu źródłowym do nowej ścieżki.

Na przykład, jeśli ustawiłem opcję **Zachowaj niedopasowaną ścieżkę na wartość tak**.
* Jeśli żądanie przychodzące ma `www.contoso.com/sub/1.jpg` wartość, wzorzec źródłowy jest ustawiany na `/` , miejsce docelowe jest ustawione na `/foo/` , a zawartość jest obsługiwana z `/foo/sub/1` . jpg ze źródła.

* Jeśli żądanie przychodzące ma `www.contoso.com/sub/image/1.jpg` wartość, wzorzec źródłowy zostanie ustawiony na `/sub/` , a miejsce docelowe zostanie ustawione na wartość `/foo/` , zawartość będzie obsługiwana z `/foo/image/1.jpg` poziomu źródła.

Na przykład, jeśli ustawiłem opcję **Zachowaj niedopasowaną ścieżkę na wartość nie**.
* Jeśli żądanie przychodzące ma `www.contoso.com/sub/image/1.jpg` wartość, wzorzec źródłowy zostanie ustawiony na `/sub/` , a miejsce docelowe zostanie ustawione na wartość `/foo/2.jpg` , zawartość będzie zawsze obsługiwana od pochodzenia niezależnie od tego, `/foo/2.jpg` jakie ścieżki występują `wwww.contoso.com/sub/` .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zestawie reguł Standard/Premium platformy Azure](concept-rule-set.md).
