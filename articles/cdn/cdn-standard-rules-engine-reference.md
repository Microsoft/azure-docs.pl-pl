---
title: Dokumentacja aparatu reguł standardowych dla Azure CDN | Microsoft Docs
description: Dokumentacja referencyjna dotycząca warunków i akcji w aparacie standardowych reguł dla usługi Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242212"
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

Każda reguła może mieć do dziesięciu warunków dopasowania i pięć akcji. Każdy punkt końcowy Azure CDN może mieć do 25 reguł. 

Uwzględniony w tym limicie jest domyślną *regułą globalną*. Reguła globalna nie ma pasujących warunków; akcje, które są zdefiniowane w regule globalnej, są zawsze wyzwalane.

   > [!IMPORTANT]
   > Kolejność, w której są wyświetlane wiele reguł, ma wpływ na sposób obsługi reguł. Akcje określone w regule mogą zostać zastąpione przez kolejną regułę.

## <a name="limits-and-pricing"></a>Limity i ceny 

Każdy punkt końcowy Azure CDN może mieć do 25 reguł. Każda reguła może mieć do dziesięciu warunków dopasowania i pięć akcji. Cennik aparatu reguł ma następujące wymiary: 
- Reguły: $1 na regułę miesięcznie 
- Przetworzone żądania: $0,60 na milion żądań
- Pierwsze 5 reguł pozostanie bezpłatnie

## <a name="syntax"></a>Składnia

Sposób traktowania znaków specjalnych w regule różni się w zależności od sposobu, w jaki różne warunki dopasowania i akcje obsługują wartości tekstowe. Warunek dopasowania lub akcja może interpretować tekst w jeden z następujących sposobów:

- [Wartości literału](#literal-values)
- [Wartości symboli wieloznacznych](#wildcard-values)


### <a name="literal-values"></a>Wartości literału

Tekst interpretowany jako wartość literału traktuje wszystkie znaki specjalne, *z wyjątkiem% symbol* jako część wartości, która musi być dopasowana w regule. Na przykład warunek dopasowania literału ustawiony na `'*'` jest spełniony tylko wtedy, gdy `'*'` zostanie znaleziona dokładna wartość.

Znak procentu jest używany do wskazania kodowania adresu URL (na przykład `%20` ).

### <a name="wildcard-values"></a>Wartości symboli wieloznacznych

Obecnie obsługujemy symbol wieloznaczny w **warunku URLPath Match** w aparacie reguł standardowych. \*Znak jest symbolem wieloznacznym reprezentującym jeden lub więcej znaków. 

## <a name="next-steps"></a>Następne kroki

- [Warunki dopasowania w aparacie standardowych reguł](cdn-standard-rules-engine-match-conditions.md)
- [Akcje w aparacie reguł standardowych](cdn-standard-rules-engine-actions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
- [Przegląd Azure CDN](cdn-overview.md)
