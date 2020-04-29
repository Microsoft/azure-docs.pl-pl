---
title: Informacje o aparacie Azure CDN reguł | Microsoft Docs
description: Dokumentacja referencyjna dotycząca aparatu reguł Azure CDN zgodności z warunkami i funkcjami.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: bda817712faf1f54287e880dc62ef2b08273ff42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253394"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN z referencyjnego aparatu reguł Verizon Premium

W tym artykule przedstawiono szczegółowe opisy warunków i funkcji zgodnych z [aparatem reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Aparat reguł został zaprojektowany z myślą o sposobie przetwarzania określonych typów żądań przez sieć CDN.

**Typowe zastosowania**:

- Przesłoń lub Zdefiniuj niestandardowe zasady pamięci podręcznej.
- Zabezpieczanie lub odrzucanie żądań dotyczących poufnej zawartości.
- Przekieruj żądania.
- Przechowuj niestandardowe dane dziennika.

## <a name="terminology"></a>Terminologia

Reguła jest definiowana przy użyciu [**wyrażeń warunkowych**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**warunków dopasowania**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)i [**funkcji**](cdn-verizon-premium-rules-engine-reference-features.md). Te elementy zostały wyróżnione na poniższej ilustracji:

 ![Warunek dopasowania sieci CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Składnia

Sposób, w jaki znaki specjalne są traktowane, różni się w zależności od tego, jak warunek dopasowania lub funkcja obsługuje wartości tekstowe. Warunek dopasowania lub funkcja może interpretować tekst w jeden z następujących sposobów:

1. [**Wartości literału**](#literal-values)
2. [**Wartości symboli wieloznacznych**](#wildcard-values)
3. [**Wyrażenia regularne**](#regular-expressions)

### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne, z wyjątkiem symbolu%, jako część wartości, która musi być dopasowana. Inaczej mówiąc, warunek dopasowania literału ustawiony na `\'*'\` jest spełniony tylko wtedy, gdy zostanie znaleziona dokładna wartość `\'*'\`(to jest,).

Symbol procentu jest używany do wskazania kodowania adresów URL (na przykład `%20`).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Tekst, który jest interpretowany jako wartość symbolu wieloznacznego, przypisuje dodatkowe znaczenie do znaków specjalnych. W poniższej tabeli opisano sposób interpretowania następującego zestawu znaków:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny jest używany do ucieczki znaków określonych w tej tabeli. Ukośnik odwrotny musi być określony bezpośrednio przed znakiem specjalnym, który powinien zostać zmieniony.<br/>Na przykład następująca składnia wyprowadza gwiazdkę:`\*`
% | Symbol procentu jest używany do wskazania kodowania adresów URL (na przykład `%20`).
\* | Gwiazdka jest symbolem wieloznacznym reprezentującym jeden lub więcej znaków.
Miejsce | Znak spacji wskazuje, że warunek dopasowania może być spełniony przez jedną z określonych wartości lub wzorców.
wartościami | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw pojedynczych cudzysłowów służy do wskazywania, że wartość powinna być traktowana jako wartość literału. Może być używana w następujący sposób:<br><br/>-Pozwala na spełnienie warunku dopasowywania za każdym razem, gdy określona wartość pasuje do dowolnej części wartości porównania.  Na przykład `'ma'` dopasuje do żadnego z następujących ciągów: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />— Umożliwia określenie znaku specjalnego jako znaku literału. Na przykład można określić znak spacji literału, umieszczając znak spacji w zestawie pojedynczych cudzysłowów (czyli `' '` lub `'sample value'`).<br/>-Umożliwia określenie wartości pustej. Określ wartość pustą, określając zestaw pojedynczych cudzysłowów (czyli "").<br /><br/>**Ważne:**<br/>— Jeśli określona wartość nie zawiera symbolu wieloznacznego, zostanie automatycznie uznana za wartość literału, co oznacza, że nie jest konieczne określenie zestawu pojedynczych cudzysłowów.<br/>-Jeśli ukośnik odwrotny nie powoduje ucieczki innego znaku w tej tabeli, jest on ignorowany, gdy jest określony w ramach zestawu pojedynczych cudzysłowów.<br/>Innym sposobem określenia znaku specjalnego jako znaku literału jest wypróbowanie go przy użyciu ukośnika odwrotnego (czyli, `\`).

### <a name="regular-expressions"></a>Wyrażenia regularne

Wyrażenia regularne definiują wzorzec, który jest wyszukiwany w obrębie wartości tekstowej. Notacja wyrażenia regularnego definiuje określone znaczenie do różnych symboli. Poniższa tabela zawiera informacje o sposobie traktowania znaków specjalnych przez dopasowanie warunków i funkcji, które obsługują wyrażenia regularne.

Znak specjalny | Opis
------------------|------------
\ | Ukośnik odwrotny oznacza znak, który powoduje, że ten znak jest traktowany jako wartość literału, a nie przy jego wyrażeniu regularnym. Na przykład następująca składnia wyprowadza gwiazdkę:`\*`
% | Znaczenie symbolu procentu zależy od jego użycia.<br/><br/> `%{HTTPVariable}`: Ta składnia identyfikuje zmienną HTTP.<br/>`%{HTTPVariable%Pattern}`: Ta składnia używa symbolu procentu do identyfikacji zmiennej HTTP i jako ogranicznika.<br />`\%`: Ucieczki symbolu procentu pozwala na użycie go jako wartości literału lub wskazywanie kodowania adresu URL (na przykład `\%20`).
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
