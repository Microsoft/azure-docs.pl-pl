---
title: Aparat reguł dla architektury i terminologii z przodu platformy Azure
description: Ten artykuł zawiera omówienie funkcji aparatu reguł dla drzwi platformy Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/29/2020
ms.author: duau
ms.openlocfilehash: 110d0a347020e2bf2c1e957ed6dab65ffe13e2f0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056515"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Co to jest aparat reguł dla drzwi frontonu platformy Azure? 

Aparat reguł pozwala dostosować sposób, w jaki żądania HTTP są obsługiwane na krawędzi i zapewniają bardziej kontrolowane zachowanie aplikacji sieci Web. Aparat reguł dla drzwi frontonu platformy Azure ma kilka kluczowych funkcji, w tym:

* Wymusza użycie protokołu HTTPS, aby upewnić się, że wszyscy użytkownicy końcowi współpracują z zawartością za pośrednictwem bezpiecznego połączenia.
* Implementuje nagłówki zabezpieczeń, aby zapobiec występowaniu luk w przeglądarce, takich jak HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options i Access-Control-Allow-Origin dla scenariuszy udostępniania zasobów między źródłami (CORS). Atrybuty oparte na zabezpieczeniach można także definiować za pomocą plików cookie.
* Kierowanie żądań do wersji mobilnych lub klasycznych aplikacji na podstawie wzorców zawartości nagłówka żądania, plików cookie lub ciągów zapytań.
* Funkcja przekierowania umożliwia zwracanie 301, 302, 307 i 308 przekierowań do klienta, aby skierować nowe nazwy hostów, ścieżki lub protokoły.
- Dynamicznie Modyfikuj konfigurację buforowania trasy na podstawie żądań przychodzących.
- Zapisz ponownie ścieżkę URL żądania i prześlij żądanie do odpowiedniego zaplecza w skonfigurowanej puli zaplecza.

## <a name="architecture"></a>Architektura 

Aparat reguł obsługuje żądania na krawędzi. Gdy żądanie trafi punkt końcowy z drzwiami, WAF jest wykonywane jako pierwsze, a następnie konfiguracja aparatu reguł skojarzona z frontonem/domeną. Jeśli konfiguracja aparatu reguł jest wykonywana, oznacza to, że reguła routingu nadrzędnego jest już zgodna. Aby można było wykonać wszystkie akcje w każdej regule, należy spełnić wszystkie warunki dopasowania w regule. Jeśli żądanie nie jest zgodne z żadnym z warunków konfiguracji aparatu reguł, zostanie wykonana domyślna reguła routingu. 

Na przykład na poniższym diagramie aparat reguł jest skonfigurowany do dołączania nagłówka odpowiedzi. Nagłówek zmienia maksymalny wiek kontrolki pamięci podręcznej w przypadku spełnienia warunku dopasowywania. 

![akcja nagłówka odpowiedzi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

W innym przykładzie widać, że aparat reguł jest skonfigurowany do wysyłania użytkownika do mobilnej wersji witryny, jeśli warunek dopasowania, typ urządzenia, ma wartość true. 

![przesłonięcie konfiguracji trasy](./media/front-door-rules-engine/rules-engine-architecture-1.png)

W obu tych przykładach, gdy żaden z warunków dopasowania nie jest spełniony, określona reguła trasy jest wykonywane. 

## <a name="terminology"></a>Terminologia 

Aparat reguł AFD umożliwia tworzenie kombinacji konfiguracji aparatu reguł, z których każdy składa się z zestawu reguł. Poniżej przedstawiono najbardziej przydatną terminologię wykonywaną podczas konfigurowania aparatu reguł. 

- *Konfiguracja aparatu reguł*: zestaw reguł, które są stosowane do reguły pojedynczej trasy. Każda konfiguracja jest ograniczona do 25 reguł. Można utworzyć maksymalnie 10 konfiguracji. 
- *Reguła aparatu reguł*: reguła składająca się z maksymalnie 10 warunków zgodności i 5 akcji.
- *Warunek dopasowania*: istnieje wiele warunków dopasowania, których można użyć do analizowania żądań przychodzących. Reguła może zawierać maksymalnie 10 warunków dopasowywania. Warunki dopasowania są oceniane przy użyciu operatora **i** . Pełną listę warunków dopasowania można znaleźć [tutaj](front-door-rules-engine-match-conditions.md). 
- *Akcja*: akcje wskazują, co się dzieje z żądaniami przychodzącymi — akcje w nagłówku żądania/odpowiedzi, przekazywanie, przekierowania i ponowne zapisywanie są obecnie dostępne. Reguła może zawierać maksymalnie pięć akcji; jednak reguła może zawierać tylko jedno przesłonięcie konfiguracji trasy.  Pełną listę akcji można znaleźć [tutaj](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować pierwszą [konfigurację aparatu reguł](front-door-tutorial-rules-engine.md). 
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
