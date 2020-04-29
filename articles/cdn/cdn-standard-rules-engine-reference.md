---
title: Dokumentacja aparatu reguł standardowych dla Azure CDN | Microsoft Docs
description: Dokumentacja referencyjna dotycząca warunków i akcji w aparacie standardowych reguł dla usługi Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259905"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Dokumentacja aparatu reguł standardowych dla usługi Azure CDN

W [aparacie reguł standardowych](cdn-standard-rules-engine.md) dla platformy Azure Content Delivery Network (Azure CDN) reguła zawiera co najmniej jeden warunek dopasowania i akcję. Ten artykuł zawiera szczegółowe opisy warunków dopasowania i funkcji, które są dostępne w aparacie reguł standardowych dla Azure CDN.

Aparat reguł został zaprojektowany z myślą o sposobie przetwarzania określonych typów żądań przez standardową Azure CDN.

**Typowe zastosowania reguł**:

- Przesłoń lub Zdefiniuj niestandardowe zasady pamięci podręcznej.
- Przekieruj żądania.
- Modyfikuj nagłówki żądań i odpowiedzi HTTP.

## <a name="terminology"></a>Terminologia

Aby zdefiniować regułę w aparacie reguł, ustaw odpowiednie [warunki](cdn-standard-rules-engine-match-conditions.md) i [Akcje](cdn-standard-rules-engine-actions.md):

 ![Struktura reguł Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Każda reguła może mieć maksymalnie cztery warunki dopasowania i trzy akcje. Każdy punkt końcowy Azure CDN może mieć maksymalnie pięć reguł. 

Uwzględniony w bieżącym limicie pięciu reguł dla punktu końcowego Azure CDN jest domyślną *regułą globalną*. Reguła globalna nie ma pasujących warunków, a akcje, które są zdefiniowane w regule, zawsze są wyzwalane.

## <a name="syntax"></a>Składnia

Sposób traktowania znaków specjalnych w regule różni się w zależności od sposobu, w jaki różne warunki dopasowania i akcje obsługują wartości tekstowe. Warunek dopasowania lub akcja może interpretować tekst w jeden z następujących sposobów:

- [Wartości literału](#literal-values)
- [Wartości symboli wieloznacznych](#wildcard-values)


### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne, *z wyjątkiem% symbol* jako część wartości, która musi być dopasowana w regule. Na przykład warunek dopasowania literału ustawiony na `'*'` jest spełniony tylko wtedy, gdy zostanie znaleziona dokładna wartość. `'*'`

Znak procentu jest używany do wskazania kodowania adresu URL (na przykład `%20`).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Tekst interpretowany jako wartość symbol wieloznaczny przypisuje dodatkowe znaczenie do znaków specjalnych. W poniższej tabeli opisano sposób interpretacji określonych znaków specjalnych w aparacie standardowych reguł:

Znak | Opis
----------|------------
\ | Ukośnik odwrotny jest używany do ucieczki znaków określonych w tej tabeli. Ukośnik odwrotny musi być określony bezpośrednio przed znakiem specjalnym, który powinien zostać zmieniony. Na przykład następująca składnia wyprowadza gwiazdkę:`\*`
% | Znak procentu jest używany do wskazania kodowania adresu URL (na przykład `%20`).
\* | Gwiazdka jest symbolem wieloznacznym reprezentującym jeden lub więcej znaków.
spacje | Znak spacji wskazuje, że warunek dopasowania może być spełniony przez jedną z określonych wartości lub wzorców.
znaki pojedynczego cudzysłowu | Pojedynczy cudzysłów nie ma specjalnego znaczenia. Jednak zestaw znaków pojedynczego cudzysłowu wskazuje, że wartość powinna być traktowana jako wartość literału. Znaki pojedynczego cudzysłowu mogą być używane w następujący sposób:<ul><li>Aby zezwolić na spełnienie warunku dopasowywania za każdym razem, gdy określona wartość pasuje do dowolnej części wartości porównania.  Na przykład `'ma'` dopasuje do żadnego z następujących ciągów: <ul><li>/Business/**ma**rathon/Asset.htm</li><li>**ma**p. gif</li><li>/business/template. **ma**p</li></ul><li>Aby zezwolić na określenie znaku specjalnego jako znaku literału. Na przykład można określić znak spacji literału, umieszczając znak spacji w zestawie znaków pojedynczego cudzysłowu (`' '` lub `'<sample value>'`).</li><li>, Aby umożliwić określenie pustej wartości. Określ wartość pustą, określając zestaw znaków pojedynczego cudzysłowu (**' '**).</li></ul>**Ważne**:<br /><ul><li>Jeśli określona wartość nie zawiera symbolu wieloznacznego, wartość zostanie automatycznie uznana za wartość literału. Nie trzeba określać zestawu znaków pojedynczego cudzysłowu dla wartości literału.</li><li>Jeśli ukośnik odwrotny nie jest używany do ucieczki innego znaku w tej tabeli, ukośnik odwrotny jest ignorowany, gdy jest określony w zestawie znaków pojedynczego cudzysłowu.</li><li>Innym sposobem określenia znaku specjalnego jako znaku literału jest wypróbowanie go przy użyciu ukośnika odwrotnego (`\`).</li></ul>

## <a name="next-steps"></a>Następne kroki

- [Warunki dopasowania w aparacie standardowych reguł](cdn-standard-rules-engine-match-conditions.md)
- [Akcje w aparacie reguł standardowych](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
- [Przegląd Azure CDN](cdn-overview.md)
