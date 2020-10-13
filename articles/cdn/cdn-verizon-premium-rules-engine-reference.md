---
title: Informacje o aparacie Azure CDN reguł | Microsoft Docs
description: Dokumentacja referencyjna dotycząca aparatu reguł Azure CDN zgodności z warunkami i funkcjami.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362584"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN z referencyjnego aparatu reguł Verizon Premium

W tym artykule przedstawiono szczegółowe opisy warunków i funkcji zgodnych z [aparatem reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Aparat reguł został zaprojektowany z myślą o sposobie przetwarzania określonych typów żądań przez sieć CDN.

**Typowe zastosowania**:

- Przesłoń lub Zdefiniuj niestandardowe zasady pamięci podręcznej.
- Zabezpieczanie lub odrzucanie żądań dotyczących poufnej zawartości.
- Przekieruj żądania.
- Przechowuj niestandardowe dane dziennika.
## <a name="key-concepts"></a>Kluczowe pojęcia
Najważniejsze pojęcia dotyczące konfigurowania aparatu reguł opisano poniżej.
### <a name="draft"></a>Wersja robocza
Wersja robocza zasad składa się z co najmniej jednej reguły przeznaczonej do identyfikowania żądań i zestawu akcji, które będą stosowane do nich. Wersja robocza to trwające prace, które umożliwiają częste aktualizacje konfiguracji bez wpływu na ruch w lokacji. Gdy wersja robocza będzie gotowa do sfinalizowania, należy ją przekonwertować na zasadę tylko do odczytu.

### <a name="rule"></a>Reguła
Reguła identyfikuje jeden lub więcej typów żądań oraz zestaw akcji, które będą stosowane do nich.

Składa się z: 

- Zestaw wyrażeń warunkowych, które definiują logikę, za pomocą której są identyfikowane żądania.
- Zestaw warunków dopasowania, które definiują kryteria używane do identyfikowania żądań.
- Zestaw funkcji, które definiują, w jaki sposób Usługa CDN będzie obsługiwać powyższe żądania.
Te elementy są identyfikowane na poniższej ilustracji.

![Zrzut ekranu z etykietami zawiera wyrażenie warunkowe, dopasowanie i funkcje reguły.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Zasady
Zasada, która składa się z zestawu reguł tylko do odczytu, zapewnia:

- Twórz i przechowuj wiele wariantów reguł oraz zarządzaj nimi.
- Wróć do wcześniej wdrożonej wersji.
- Przygotuj reguły specyficzne dla zdarzeń z wyprzedzeniem (np. reguła, która przekierowuje ruch w wyniku konserwacji pochodzenie przez klienta).

> [!NOTE]
> Chociaż dozwolone są tylko pojedyncze zasady dla każdego środowiska, zasady mogą zostać wdrożone zgodnie z wymaganiami.

### <a name="deploy-request"></a>Wdróż żądanie
Żądanie wdrożenia zapewnia prostą i uproszczoną procedurę, za pomocą której zasady można szybko zastosować do środowiska przejściowego lub produkcyjnego. Historia żądań wdrażania jest zapewniana w celu ułatwienia śledzenia zmian stosowanych w tych środowiskach.

> [!NOTE]
> Tylko żądania, które nie przekazują naszego systemu zautomatyzowanej weryfikacji i wykrywania błędów, będą wymagały ręcznego przeglądu i zatwierdzenia.

### <a name="rule-precedence"></a>Pierwszeństwo reguł
Reguły zawarte w zasadach są zwykle przetwarzane w kolejności, w jakiej są wyświetlane (tj. od góry do dołu). Jeśli żądanie jest zgodne z regułami powodującymi konflikt, pierwszeństwo ma Ostatnia reguła do przetworzenia.

### <a name="policy-deployment-workflow"></a>Przepływ pracy wdrażania zasad
Poniżej przedstawiono przepływ pracy, za pomocą którego można zastosować zasady do środowiska produkcyjnego lub przejściowego.

![Przepływ pracy wdrażania zasad](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Krok |Opis |
|---------|---------|
|[Utwórz wersję roboczą](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Wersja robocza składa się z zestawu reguł, które definiują sposób obsługi żądań dotyczących zawartości przez sieć CDN.     |
|Zablokuj wersję roboczą   |     Gdy wersja robocza zostanie sfinalizowana, należy ją zablokować i przekonwertować na zasady tylko do odczytu.    |
|[Prześlij żądanie wdrożenia](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Żądanie wdrożenia umożliwia stosowanie zasad do ruchu testowego lub produkcyjnego.</br> <br>Prześlij żądanie wdrożenia w środowisku przejściowym lub produkcyjnym.</br>     |
|Wdróż przegląd żądania   |    <br>Żądanie wdrożenia przeprowadzi automatyczne sprawdzanie poprawności i wykrywanie błędów.</br><br>Mimo że większość żądań wdrożenia jest automatycznie zatwierdzana, w przypadku bardziej złożonych zasad wymagane jest ręczne sprawdzenie.</br>   |
|Wdrażanie zasad ([przemieszczanie](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Po zatwierdzeniu żądania wdrożenia do środowiska przejściowego zasady zostaną zastosowane do środowiska przejściowego. To środowisko umożliwia testowanie zasad na potrzeby makiety ruchu w witrynie.</br><br>Gdy zasady są gotowe do zastosowania do ruchu w miejscu na żywo, należy przesłać nowe żądanie wdrożenia dla środowiska produkcyjnego.</br>      |
|Wdrażanie zasad ([produkcja](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Po zatwierdzeniu żądania wdrożenia do środowiska produkcyjnego zasady zostaną zastosowane do środowiska produkcyjnego. To środowisko umożliwia zasadom działanie jako końcowy urząd określający, jak sieć CDN powinna obsługiwać ruch na żywo.     |
## <a name="syntax"></a>Składnia

Sposób, w jaki znaki specjalne są traktowane, różni się w zależności od tego, jak warunek dopasowania lub funkcja obsługuje wartości tekstowe. Warunek dopasowania lub funkcja może interpretować tekst w jeden z następujących sposobów:

- [**Wartości literału**](#literal-values)
- [**Wartości symboli wieloznacznych**](#wildcard-values)
- [**Wyrażenia regularne**](#regular-expressions)

### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne, z wyjątkiem symbolu%, jako część wartości, która musi być dopasowana. Inaczej mówiąc, warunek dopasowania literału ustawiony na `\'*'\` jest spełniony tylko wtedy, gdy zostanie znaleziona dokładna wartość (to jest, `\'*'\` ).

Symbol procentu jest używany do wskazania kodowania adresów URL (na przykład `%20` ).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Tekst, który jest interpretowany jako wartość symbolu wieloznacznego, przypisuje dodatkowe znaczenie do znaków specjalnych. W poniższej tabeli opisano sposób interpretowania następującego zestawu znaków:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny jest używany do ucieczki znaków określonych w tej tabeli. Ukośnik odwrotny musi być określony bezpośrednio przed znakiem specjalnym, który powinien zostać zmieniony.<br/>Na przykład następująca składnia wyprowadza gwiazdkę: `\*`
% | Symbol procentu jest używany do wskazania kodowania adresów URL (na przykład `%20` ).
\* | Gwiazdka jest symbolem wieloznacznym reprezentującym jeden lub więcej znaków.
Miejsce | Znak spacji wskazuje, że warunek dopasowania może być spełniony przez jedną z określonych wartości lub wzorców.
wartościami | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw pojedynczych cudzysłowów służy do wskazywania, że wartość powinna być traktowana jako wartość literału. Może być używana w następujący sposób:<br><br/>-Pozwala na spełnienie warunku dopasowywania za każdym razem, gdy określona wartość pasuje do dowolnej części wartości porównania.  Na przykład `'ma'` dopasuje do żadnego z następujących ciągów: <br/><br/>/Business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />— Umożliwia określenie znaku specjalnego jako znaku literału. Na przykład można określić znak spacji literału, umieszczając znak spacji w zestawie pojedynczych cudzysłowów (czyli `' '` lub `'sample value'` ).<br/>-Umożliwia określenie wartości pustej. Określ wartość pustą, określając zestaw pojedynczych cudzysłowów (czyli "").<br /><br/>**Ważne:**<br/>— Jeśli określona wartość nie zawiera symbolu wieloznacznego, zostanie automatycznie uznana za wartość literału, co oznacza, że nie jest konieczne określenie zestawu pojedynczych cudzysłowów.<br/>-Jeśli ukośnik odwrotny nie powoduje ucieczki innego znaku w tej tabeli, jest on ignorowany, gdy jest określony w ramach zestawu pojedynczych cudzysłowów.<br/>Innym sposobem określenia znaku specjalnego jako znaku literału jest wypróbowanie go przy użyciu ukośnika odwrotnego (czyli, `\` ).

### <a name="regular-expressions"></a>Wyrażenia regularne

Wyrażenia regularne definiują wzorzec, który jest wyszukiwany w obrębie wartości tekstowej. Notacja wyrażenia regularnego definiuje określone znaczenie do różnych symboli. Poniższa tabela zawiera informacje o sposobie traktowania znaków specjalnych przez dopasowanie warunków i funkcji, które obsługują wyrażenia regularne.

Znak specjalny | Opis
------------------|------------
\ | Ukośnik odwrotny oznacza znak, który powoduje, że ten znak jest traktowany jako wartość literału, a nie przy jego wyrażeniu regularnym. Na przykład następująca składnia wyprowadza gwiazdkę: `\*`
% | Znaczenie symbolu procentu zależy od jego użycia.<br/><br/> `%{HTTPVariable}`: Ta składnia identyfikuje zmienną HTTP.<br/>`%{HTTPVariable%Pattern}`: Ta składnia używa symbolu procentu do identyfikacji zmiennej HTTP i jako ogranicznika.<br />`\%`: Ucieczki symbolu procentu pozwala na użycie go jako wartości literału lub wskazywanie kodowania adresu URL (na przykład `\%20` ).
\* | Gwiazdka umożliwia dopasowanie znaku poprzedzającego zero lub więcej razy.
Miejsce | Znak spacji jest zwykle traktowany jako znak literału.
wartościami | Pojedyncze cudzysłowy są traktowane jako znaki literału. Zestaw pojedynczych cudzysłowów nie ma specjalnego znaczenia.

Dopasuj warunki i funkcje, które obsługują wyrażenia regularne akceptują wzorce zdefiniowane przez wyrażenia regularne zgodne z językiem Perl (PCRE).



## <a name="next-steps"></a>Następne kroki

- [Warunki dopasowania aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Przegląd Azure CDN](cdn-overview.md)
