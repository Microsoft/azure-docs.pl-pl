---
title: Aparat reguł dla architektury i terminologii z przodu platformy Azure
description: Ten artykuł zawiera omówienie funkcji aparatu reguł dla drzwi platformy Azure.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 1332f4b21f971d23055c158c2aebdd3316f1bd39
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512218"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Co to jest aparat reguł dla drzwi frontonu platformy Azure? 

Aparat reguł umożliwia dostosowywanie sposobu obsługi żądań HTTP na brzegu i zapewnia większą kontrolę nad zachowaniem aplikacji sieci Web. Aparat reguł dla drzwi frontonu platformy Azure składa się z kilku kluczowych funkcji, w tym:

- Wymuś protokół HTTPS, upewnij się, że wszyscy użytkownicy końcowi pracują z zawartością za pośrednictwem bezpiecznego połączenia.
- Zaimplementuj nagłówki zabezpieczeń, aby zapobiec występowaniu luk w zabezpieczeniach przeglądarki, takich jak HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy, X-Frame-Options, a także nagłówków dostępu-Control-Allow-Origin dla scenariuszy udostępniania zasobów między źródłami (CORS). Atrybuty oparte na zabezpieczeniach można także definiować za pomocą plików cookie.
- Kierowanie żądań do wersji mobilnych lub klasycznych aplikacji na podstawie wzorców w zawartości nagłówków żądań, plików cookie lub ciągów zapytań.
- Funkcja przekierowania umożliwia zwracanie 301, 302, 307 i 308 przekierowań do klienta w celu przekierowania do nowych nazw hostów, ścieżek lub protokołów.
- Dynamicznie Modyfikuj konfigurację buforowania trasy na podstawie żądań przychodzących.
- Zapisz ponownie ścieżkę URL żądania i prześlij żądanie do odpowiedniego zaplecza w skonfigurowanej puli zaplecza.

## <a name="architecture"></a>Architektura 

Aparat reguł obsługuje żądania na krawędzi. Po skonfigurowaniu aparatu reguł, gdy żądanie trafi punkt końcowy od drzwi, WAF zostanie wykonane jako pierwsze, a następnie konfiguracja aparatu reguł skojarzona z frontonem/domeną. Gdy jest wykonywana konfiguracja aparatu reguł, oznacza to, że nadrzędna reguła routingu jest już zgodna. Czy wszystkie akcje w każdej z reguł w ramach konfiguracji aparatu reguł są wykonywane, podlegają wszystkim warunkom dopasowania w ramach tej reguły. Jeśli żądanie nie pasuje do żadnego z warunków konfiguracji aparatu reguł, zostanie wykonana domyślna reguła routingu. 

Na przykład w konfiguracji poniżej aparat reguł jest skonfigurowany do dołączania nagłówka odpowiedzi, który zmienia maksymalny wiek kontroli pamięci podręcznej w przypadku spełnienia warunku dopasowywania. 

![akcja nagłówka odpowiedzi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

W innym przykładzie widać, że aparat reguł jest skonfigurowany do wysyłania użytkownika do mobilnej wersji witryny, jeśli warunek dopasowania, typ urządzenia, ma wartość true. 

![przesłonięcie konfiguracji trasy](./media/front-door-rules-engine/rules-engine-architecture-1.png)

W obu tych przykładach, gdy żaden z warunków dopasowania nie jest spełniony, określona reguła trasy jest wykonywane. 

## <a name="terminology"></a>Terminologia 

Aparat reguł AFD umożliwia tworzenie serii reguł konfiguracji aparatu, z których każdy składa się z zestawu reguł. Poniżej przedstawiono najbardziej przydatną terminologię wykonywaną podczas konfigurowania aparatu reguł. 

- *Konfiguracja aparatu reguł*: zestaw reguł, które są stosowane do reguły pojedynczej trasy. Każda konfiguracja jest ograniczona do 25 reguł. Można utworzyć maksymalnie 10 konfiguracji. 
- *Reguła aparatu reguł*: reguła składająca się z maksymalnie 10 warunków zgodności i 5 akcji.
- *Warunek dopasowania*: istnieje wiele warunków dopasowania, których można użyć do analizowania żądań przychodzących. Reguła może zawierać maksymalnie 10 warunków dopasowywania. Warunki dopasowania są oceniane przy użyciu operatora **i** . Pełną listę warunków dopasowania można znaleźć [tutaj](front-door-rules-engine-match-conditions.md). 
- *Akcja*: akcje wskazują, co się dzieje z żądaniami przychodzącymi — akcje w nagłówku żądania/odpowiedzi, przekazywanie, przekierowania i ponowne zapisywanie są obecnie dostępne. Reguła może zawierać maksymalnie 5 akcji; jednak reguła może zawierać tylko 1 przesłonięcie konfiguracji trasy.  Pełną listę akcji można znaleźć [tutaj](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować pierwszą [konfigurację aparatu reguł](front-door-tutorial-rules-engine.md). 
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
