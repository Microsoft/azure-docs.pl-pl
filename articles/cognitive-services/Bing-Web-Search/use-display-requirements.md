---
title: Użyj i Wyświetl wymagania dla interfejsy API wyszukiwania Bing
titleSuffix: Azure Cognitive Services
description: Wymagania dotyczące wyświetlania wyników wyszukiwania z interfejsy API wyszukiwania Bing w aplikacjach.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 93be72f2afcda90dde1b74c5ee317a7ad3350be1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075691"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Wymagania dotyczące użycia i wyświetlania interfejsu API wyszukiwania Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Te wymagania dotyczące użycia i wyświetlania dotyczą wszelkich implementacji zawartości i skojarzonych informacji z następujących interfejsy API wyszukiwania Bing, w tym relacji, metadanych i innych sygnałów.

- Wyszukiwanie niestandardowe Bing
- Wyszukiwanie jednostek Bing
- Wyszukiwanie obrazów w usłudze Bing
- Wyszukiwanie wiadomości (Bing)
- Wyszukiwanie klipów wideo w usłudze Bing
- Wyszukiwanie wizualne Bing
- Wyszukiwanie w Internecie Bing
- Sprawdzanie pisowni Bing
- Automatyczne sugerowanie Bing

## <a name="definitions"></a>Definicje


|Termin  |Opis  |
|---------|---------|
|Odpowiedź     | Kategoria wyników zwróconych w odpowiedzi. Na przykład odpowiedź z interfejs API wyszukiwania w sieci Web Bing może zawierać odpowiedzi w kategoriach wyników, obrazów, wideo, wizualizacji i wiadomości strony sieci Web. |
|Reakcja     | Wszystkie odpowiedzi i skojarzone dane odebrane w odpowiedzi na jedno wywołanie interfejsu API wyszukiwania. |
|Wynik    | Element informacji w odpowiedzi. Na przykład zestaw danych połączonych z pojedynczym artykułem z wiadomościami jest wynikiem odpowiedzi na wiadomość. |
|Interfejsy API wyszukiwania    | zbiorowo wyszukiwanie niestandardowe Bing, wyszukiwanie jednostek, wyszukiwanie obrazów, wyszukiwanie wiadomości, wyszukiwanie wideo, wyszukiwanie wizualne, lokalne wyszukiwanie biznesowe i interfejsy API wyszukiwanie w sieci Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Ograniczenia sprawdzanie pisowni Bing i interfejs API automatycznego sugerowania Bing

Nie:

- Skopiuj, przechowuj lub Buforuj wszystkie dane otrzymane z sprawdzanie pisowni Bing lub automatyczne sugerowanie Bing interfejsów API.
- Użyj danych otrzymywanych z sprawdzanie pisowni Bing lub automatyczne sugerowanie Bing interfejsów API w ramach dowolnego uczenia maszynowego lub podobnej aktywności algorytmu. Tych danych nie należy używać do uczenia się, szacowania ani ulepszania nowych lub istniejących usług, które mogą oferować użytkownik lub osoby trzecie.

## <a name="bing-search-apis"></a>Interfejsy API wyszukiwania Bing

> [!NOTE]
> Wymagania zawarte w tej sekcji dotyczą tylko interfejsów API wyszukiwania, które nie zawierają sprawdzanie pisowni Bing ani automatyczne sugerowanie Bing. 

### <a name="internet-search-experience-requirements"></a>Wymagania dotyczące środowiska wyszukiwania w Internecie

Wszystkie dane zwrócone w odpowiedzi mogą być używane tylko w środowiskach wyszukiwania internetowego. Internetowa obsługa wyszukiwania oznacza zawartość wyświetlaną: 

- Jest istotne i odpowiada na zapytanie bezpośrednie użytkownika końcowego lub inne wskazanie ich zainteresowania i intencje wyszukiwania (na przykład zapytanie wyszukiwania wskazane przez użytkownika). 

- Pomaga użytkownikom znaleźć źródła danych odpowiedzi i przechodzić do nich. Można na przykład udostępniać linki do kliknięcia z hiperlinków w odpowiedzi.

- Zawiera wiele wyników, które użytkownik może wybrać. 

- Znajdują się w rozmieszczeniu, które umożliwia użytkownikom wyszukiwanie.

- Zawiera widoczne wskazanie, że zawartość jest wynikiem wyszukiwania w Internecie. Na przykład instrukcja, której zawartość to "z sieci Web".

- Obejmuje wszelkie inne odpowiednie środki, aby zapewnić, że dane interfejsu API Wyszukiwanie Bing nie naruszają obowiązujących przepisów lub praw innych firm. Zapoznaj się z doradcami prawnymi, aby określić, jakie środki mogą być odpowiednie.

Jedynym wyjątkiem od tych wymagań w zakresie wyszukiwania internetowego jest odnajdowanie adresów URL, zgodnie z opisem w dalszej części tego artykułu. 

### <a name="restrictions"></a>Ograniczenia

Nie:

- Kopiowanie, przechowywanie lub buforowanie danych z odpowiedzi (z wyjątkiem przechowywania w zakresie dozwolonym przez [ciągłość usługi](#continuity-of-service). 

- Użyj danych otrzymanych z interfejsów API wyszukiwania jako części dowolnego uczenia maszynowego lub podobnej aktywności algorytmu. Tych danych nie należy używać do uczenia się, szacowania ani ulepszania nowych lub istniejących usług, które mogą oferować użytkownik lub osoby trzecie.

- Zmodyfikuj zawartość wyników (inne niż w celu ich sformatowania w taki sposób, że nie narusza żadnego innego wymogu), chyba że jest to wymagane przez prawo ani uzgodnione przez firmę Microsoft. 

- Pomiń informacje o przypisywaniu i adresy URL skojarzone z zawartością wyniku.

- Zmiana kolejności, w tym przez pominięcie, wyniki wyświetlane w odpowiedzi w przypadku podanej kolejności lub klasyfikacji, chyba że jest to wymagane przez prawo lub uzgodnione przez firmę Microsoft. 

    > [!NOTE]
    > To wymaganie nie dotyczy zmiany kolejności zaimplementowanej w portalu dla interfejs API wyszukiwania niestandardowego Bing.

- Wyświetlanie innej zawartości w ramach dowolnej części odpowiedzi w taki sposób, aby użytkownik mógł uznać, że druga zawartość jest częścią odpowiedzi. 

- Wyświetlaj reklamy, które nie są dostarczane przez firmę Microsoft na żadnej stronie, która wyświetla jakąkolwiek część odpowiedzi. 

- Wyświetlaj reklamy na stronach z odpowiedziami:
    - Z poziomu interfejsów API wyszukiwanie wiadomości, wyszukiwanie wideo lub wyszukiwanie wizualne z obrazu Bing
    - Są one filtrowane lub ograniczone (lub wyłącznie) do wyników wyszukiwania obrazów, wiadomości i/lub wideo lub wizualizacji.

### <a name="notices-and-branding"></a>Powiadomienia i znakowanie 
Nie

- W widocznym miejscu dołączysz funkcjonalne hiperłącze do [zasad zachowania poufności informacji firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839)w każdym miejscu środowiska użytkownika, które oferuje użytkownikowi możliwość wprowadzenia zapytania wyszukiwania. Oznacz hiperłącze **zasad zachowania poufności informacji firmy Microsoft** .

- W widocznym miejscu Wykorzystaj znakowanie Bing, zgodnie z [wytycznymi dotyczącymi użycia znaku towarowego Bing](https://go.microsoft.com/fwlink/?linkid=833278), w dowolnym miejscu w środowisku użytkownika, które oferuje użytkownikowi możliwość wprowadzenia zapytania wyszukiwania. Takie oznakowanie musi jasno stwierdzać użytkownikowi, że firma Microsoft umożliwia korzystanie z funkcji wyszukiwania internetowego.

- Do firmy Microsoft można przypisywać każdą odpowiedź (lub część odpowiedzi) wyświetlaną w interfejsie API wyszukiwanie w sieci Web Bing, wyszukiwanie obrazów, wyszukiwanie wiadomości, wyszukiwanie wideo i wyszukiwanie wizualne, chyba że firma Microsoft określi inaczej do użycia. Jest to opisane w [wytycznych dotyczących użycia znaku towarowego Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Nie:

- Odpowiedzi na atrybuty (lub fragmenty odpowiedzi) wyświetlane w interfejs API wyszukiwania niestandardowego Bing do firmy Microsoft, chyba że firma Microsoft określi inaczej w przypadku konkretnego użycia.

### <a name="transferring-responses"></a>Transferowanie odpowiedzi

W przypadku umożliwienia użytkownikowi przetransferowania odpowiedzi z interfejsu API wyszukiwania do innego użytkownika, na przykład za pośrednictwem aplikacji do obsługi wiadomości lub ogłaszania mediów społecznościowych, należy zastosować następujące czynności: 

- Przesłane odpowiedzi muszą:
  - Składają się z zawartości, która nie została zmodyfikowana z zawartości odpowiedzi wyświetlanej dla użytkownika przekazującego. Dozwolone jest wprowadzanie zmian w formatowaniu.
  - Nie dołączaj żadnych danych do formularza metadanych.
  - W przypadku odpowiedzi z interfejsów API sieci Web, obrazów, wiadomości, wideo i wizualizacji usługi Bing język wyświetlania wskazujący, że odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania internetowego obsługiwanego przez usługę Bing. Na przykład możesz wyświetlić język taki jak "obsługiwany przez Bing" lub "dowiedzieć się więcej na temat tego obrazu w usłudze Bing" lub użyć logo Bing.
  - W przypadku odpowiedzi z interfejs API wyszukiwania niestandardowego Bing, język wyświetlania wskazujący, że odpowiedź została uzyskana za pośrednictwem internetowego środowiska wyszukiwania. Na przykład możesz wyświetlić język, taki jak "Dowiedz się więcej na temat tego wyniku wyszukiwania".
  - Wyświetlenie pełnego zapytania użytego do wygenerowania odpowiedzi.
  - Dołącz widoczne łącze lub podobne przypisanie do bazowego źródła odpowiedzi, bezpośrednio lub za pomocą aparatu wyszukiwania (bing.com, m.bing.com lub niestandardowej usługi wyszukiwania).
- Nie można zautomatyzować transferu odpowiedzi. Przekazanie musi być inicjowane przez akcję użytkownika jasno evidencing zamiar przetransferowania odpowiedzi.
- Użytkownik może zezwolić tylko na transfer odpowiedzi, które były wyświetlane w odpowiedzi na kwerendę transferu użytkownika.

### <a name="continuity-of-service"></a>Ciągłość usługi 

Nie Kopiuj, przechowuj ani nie Buforuj żadnych danych z odpowiedzi interfejsu API wyszukiwania. Jednak w celu zapewnienia ciągłości dostępu do usługi i renderowania danych można zachować wyniki wyłącznie w następujących warunkach:

#### <a name="device"></a>Urządzenie

Możesz umożliwić użytkownikowi zachowanie wyników na urządzeniu przez krótsze (i) 24 godziny od czasu zapytania lub (II), dopóki użytkownik nie wyśle kolejnego zapytania o zaktualizowane wyniki, pod warunkiem, że zachowane wyniki mogą być używane tylko:

- Aby umożliwić użytkownikowi uzyskanie dostępu do wyników wcześniej zwróconych do tego użytkownika na tym urządzeniu (na przykład w przypadku przerwania działania usługi).
- Do przechowywania wyników zwróconych dla proaktywnego zapytania spersonalizowanych w oczekiwaniu na potrzeby użytkowników, na podstawie sygnałów tego użytkownika (na przykład w przypadku przewidywanych przerw w działaniu usługi).

#### <a name="server"></a>Serwer

Wyniki specyficzne dla pojedynczego użytkownika mogą być bezpiecznie przechowywane na serwerze, który kontrolujesz, i wyświetlają tylko zachowane wyniki:

- Aby umożliwić użytkownikowi dostęp do historycznego raportu wyników wcześniej zwróconych do tego użytkownika w rozwiązaniu. Wyniki nie mogą być (i) przechowywane przez ponad 21 dni od momentu początkowego zapytania użytkownika końcowego i (II) wyświetlane w odpowiedzi na nowe lub powtórzone zapytanie użytkownika.
- Do przechowywania wyników zwróconych dla proaktywnego zapytania spersonalizowanego w oczekiwaniu na potrzeby użytkowników, na podstawie sygnałów tego użytkownika. Można przechowywać te wyniki dla krótszych (i) 24 godzin od czasu zapytania lub (II), dopóki użytkownik nie wyśle kolejnego zapytania pod kątem zaktualizowanych wyników.

Wyniki dla określonego użytkownika nie mogą być commingled z wynikami dla innego użytkownika. Oznacza to, że wyniki poszczególnych użytkowników muszą zostać zachowane i dostarczone osobno.

### <a name="general"></a>Ogólne 

Dla wszystkich prezentacji zachowanych wyników:

- Dołącz jasne, widoczne informacje o czasie wysłania zapytania.
- Zaprezentowanie użytkownikowi przycisku lub podobnej metody w celu ponownego wykonania zapytania i uzyskania zaktualizowanych wyników. 
- Zachowaj znakowanie Bing w prezentacji wyników.
- Usuń (i w razie konieczności Odświeżaj nowe zapytanie) zapisane wyniki w określonych przedziale czasowym.

### <a name="non-display-url-discovery"></a>Odnajdywanie adresu URL bez wyświetlania 

Odpowiedzi wyszukiwania można używać tylko w przypadku wyszukiwania niepochodzącego z Internetu, tylko w celu odnajdywania adresów URL źródeł informacji, które odpowiadają na zapytanie od użytkownika lub klienta. Możesz skopiować takie adresy URL w raporcie lub podobnej odpowiedzi:

- Tylko dla tego użytkownika lub klienta, w odpowiedzi na zapytanie.
- Tylko wtedy, gdy zawiera ona znaczną dodatkową zawartość, istotną dla zapytania.

Poprzednie sekcje interfejsów API wyszukiwania wykorzystują i nie mają zastosowania do tego, które nie są wyświetlane, z wyjątkiem następujących: 

- Nie Buforuj, nie Kopiuj ani nie przechowuj żadnych danych ani zawartości z lub pochodzą z odpowiedzi wyszukiwania, inne niż opisane powyżej ograniczonego kopiowania adresów URL.
- Upewnij się, że używane dane (w tym adresy URL) otrzymane z interfejsów API wyszukiwania nie naruszają obowiązujących przepisów ani praw innych firm.
- Nie należy używać danych (łącznie z adresami URL) otrzymywanych z interfejsów API wyszukiwania w ramach dowolnego indeksu wyszukiwania lub uczenia maszynowego lub podobnej aktywności algorytmu. Nie używaj tych danych do tworzenia uczenia, szacowania ani ulepszania usług, które mogą oferować użytkownik lub osoby trzecie.

## <a name="gdpr-compliance"></a>Zgodność z RODO  

W odniesieniu do wszelkich danych osobowych podlegających Unii Europejskiej Ogólne rozporządzenie o ochronie danych (Rodo) i przetwarzanych w związku z wywołaniami interfejsów API wyszukiwania, sprawdzanie pisowni Bing interfejsu API lub interfejs API automatycznego sugerowania Bing, rozumiesz, że ty i firma Microsoft są niezależnymi kontrolerami danych w ramach Rodo. Użytkownik jest odpowiedzialny za zgodność z Rodo.  

