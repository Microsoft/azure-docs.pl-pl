---
title: 'Moje drzwi platformy Azure: zestaw reguł'
description: Ten artykuł zawiera omówienie funkcji zestawu reguł Standard/Premium systemu Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 8e6ceebc9e92dabe66baeb9aeff0ae9692e2bdad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099971"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Co to jest zestaw reguł dla usług Azure Front-Standard/Premium (wersja zapoznawcza)?

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Zestaw reguł to dostosowany aparat reguł, który grupuje kombinację reguł w jeden zestaw, który można skojarzyć z wieloma trasami. Zestaw reguł umożliwia dostosowanie sposobu przetwarzania żądań na brzegu oraz sposobu, w jaki usługa Azure Front drzwiczki obsługuje te żądania.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Typowe obsługiwane scenariusze

* Implementowanie nagłówków zabezpieczeń w celu zapobiegania atakom opartym na przeglądarce, takich jak HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options i Access-Control-Allow-Origin dla scenariuszy udostępniania zasobów między źródłami (CORS). Atrybuty oparte na zabezpieczeniach można także definiować za pomocą plików cookie.

* Kierowanie żądań do wersji mobilnych lub klasycznych aplikacji na podstawie typu urządzenia klienckiego.

* Używanie funkcji przekierowania do zwracania do klienta 301, 302, 307 i 308 przekierowania, aby skierować je do nowych nazw hostów, ścieżek, ciągu zapytania lub protokołów.

* Dynamicznie Modyfikuj konfigurację buforowania trasy na podstawie żądań przychodzących.

* Zapisz ponownie ścieżkę URL żądania i przekaż żądanie do odpowiedniego źródła w skonfigurowanej grupie pochodzenia.

* Dodawanie, modyfikowanie lub usuwanie nagłówka żądania/odpowiedzi w celu ukrycia poufnych informacji lub przechwycenia ważnych informacji za poorednictwem nagłówków.

* Obsługuj Zmienne serwera, aby dynamicznie zmieniać nagłówki żądania/odpowiedzi lub ponownie pisać ścieżki/ciągi zapytań, na przykład po załadowaniu nowej strony lub po opublikowaniu formularza. Zmienna serwerowa jest obecnie obsługiwana tylko w **[akcjach zestawu reguł](concept-rule-set-actions.md)** .

## <a name="architecture"></a>Architektura

Zestaw reguł obsługuje żądania na krawędzi. Gdy żądanie dociera do punktu końcowego Standard/Premium na platformie Azure, WAF jest wykonywane jako pierwsze, a następnie ustawienia skonfigurowane w marszrucie. Te ustawienia obejmują zestaw reguł skojarzony z trasą. Zestawy reguł są przetwarzane od góry do dołu w marszrucie. To samo dotyczy reguł w zestawie reguł. Aby można było wykonać wszystkie akcje w każdej regule, należy spełnić wszystkie warunki dopasowania w regule. Jeśli żądanie nie jest zgodne z żadnym z warunków w konfiguracji zestawu reguł, zostaną wykonane tylko konfiguracje w marszrucie.

Jeśli **Zatrzymywanie oceny pozostałych reguł** jest zaznaczone, wszystkie pozostałe zestawy reguł skojarzone z trasą nie są wykonywane.  

### <a name="example"></a>Przykład

Na poniższym diagramie zasady WAF zostały wykonane jako pierwsze. Zestaw reguł jest skonfigurowany do dołączania nagłówka odpowiedzi. Następnie w nagłówku zostanie zmieniony maksymalny wiek kontrolki pamięci podręcznej w przypadku spełnienia warunku dopasowywania.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Diagram przedstawiający architekturę zestawu reguł." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminologia

Za pomocą zestawu reguł dla drzwi frontonu platformy Azure można utworzyć kombinację konfiguracji zestawu reguł, z których każdy składa się z zestawu reguł. Poniżej przedstawiono kilka przydatnych terminologii, które należy wykonać podczas konfigurowania zestawu reguł.

Aby uzyskać więcej informacji na temat limitu przydziału, zapoznaj się z tematem [subskrypcje i limitów usługi Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

* *Zestaw reguł*: zestaw reguł, które są skojarzone z jedną lub wieloma [trasami](concept-route.md). Każda konfiguracja jest ograniczona do 25 reguł. Można utworzyć maksymalnie 10 konfiguracji.

* *Reguła zestawu reguł*: reguła złożona z maksymalnie 10 warunków dopasowania i 5 akcji. Reguły są lokalne dla zestawu reguł i nie mogą być eksportowane do użycia w różnych zestawach reguł. Użytkownicy mogą utworzyć tę samą regułę w wielu zestawach reguł.

* *Warunek dopasowania*: istnieje wiele warunków dopasowania, których można użyć do analizowania żądań przychodzących. Reguła może zawierać maksymalnie 10 warunków dopasowywania. Warunki dopasowania są oceniane przy użyciu operatora **i** . *Wyrażenie regularne jest obsługiwane w warunkach*. Pełną listę warunków dopasowania można znaleźć w [warunku zestawu reguł](concept-rule-set-match-conditions.md).

* *Akcja*: akcje wskazują, jak AFD obsługuje przychodzące żądania na podstawie pasujących warunków. Można modyfikować zachowania pamięci podręcznej, modyfikować nagłówki żądań/nagłówki odpowiedzi, wykonywać ponowne zapisywanie adresów URL i przekierowania adresów URL. *Zmienne serwera są obsługiwane w akcji*. Reguła może zawierać maksymalnie 10 warunków dopasowywania. Pełną listę akcji można znaleźć w obszarze [Akcje zestawu reguł](concept-rule-set-actions.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
* Dowiedz się, jak skonfigurować swój pierwszy [zestaw reguł](how-to-configure-rule-set.md).
 
