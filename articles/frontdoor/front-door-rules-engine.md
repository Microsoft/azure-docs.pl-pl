---
title: Moje drzwi platformy Azure | Microsoft Docs
description: Ten artykuł zawiera omówienie usługi Azure Front Door. Sprawdź, czy jest to właściwy wybór dla ruchu użytkowników z równoważeniem obciążenia dla aplikacji.
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
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515552"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Co to jest aparat reguł dla drzwi frontonu platformy Azure? 

Aparat reguł umożliwia dostosowywanie sposobu obsługi żądań HTTP na brzegu i zapewnia większą kontrolę nad zachowaniem aplikacji sieci Web. Aparat reguł dla drzwi frontonu platformy Azure składa się z kilku kluczowych funkcji, w tym:

- Routing oparty na nagłówkach — kierowanie żądań na podstawie wzorców w zawartości nagłówków żądań, plików cookie i ciągów zapytań.
- Routing oparty na parametrach — korzystaj z serii warunków zgodności, takich jak post args, ciągi zapytań, pliki cookie i metody żądań, aby kierować żądania na podstawie parametrów żądania HTTP. 
- Ustawienia zastąpień konfiguracji tras: 
    - Użyj funkcji przekierowania, aby zwrócić 301/302/307/308 przekierowania do klienta w celu przekierowania do nowych nazw hostów, ścieżek i protokołów. 
    - Użyj funkcji przekazywania, aby ponownie napisać ścieżkę URL żądania bez wykonywania tradycyjnego przekierowania i przekazywać żądanie do odpowiedniego zaplecza w skonfigurowanej puli zaplecza. 
    - Dostosuj konfigurację buforowania i dynamicznie Zmień trasę z przekazywania na buforowanie na podstawie warunków dopasowania. 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architektura 

Aparat reguł obsługuje żądania na krawędzi. Po skonfigurowaniu aparatu reguł, gdy żądanie trafi punkt końcowy od drzwi, WAF zostanie wykonane jako pierwsze, a następnie konfiguracja aparatu reguł skojarzona z frontonem/domeną. Gdy jest wykonywana konfiguracja aparatu reguł, oznacza to, że nadrzędna reguła routingu jest już zgodna. Czy wszystkie akcje w każdej z reguł w ramach konfiguracji aparatu reguł są wykonywane, podlegają wszystkim warunkom dopasowania w ramach tej reguły. Jeśli żądanie nie pasuje do żadnego z warunków konfiguracji aparatu reguł, zostanie wykonana domyślna reguła routingu. 

Na przykład w konfiguracji poniżej aparat reguł jest skonfigurowany do dołączania nagłówka odpowiedzi, który zmienia maksymalny wiek kontroli pamięci podręcznej w przypadku spełnienia warunku dopasowywania. 

![akcja nagłówka odpowiedzi](./media/front-door-rules-engine/rules-engine-architecture-3.png)

W innym przykładzie widać, że aparat reguł jest skonfigurowany do wysyłania użytkownika do mobilnej wersji witryny, jeśli warunek dopasowania, typ urządzenia, ma wartość true. 

![przesłonięcie konfiguracji trasy](./media/front-door-rules-engine/rules-engine-architecture-1.png)

W obu tych przykładach, gdy żaden z warunków dopasowania nie jest spełniony, określona reguła trasy jest wykonywane. 

## <a name="terminology"></a>Terminologia 

Aparat reguł AFD umożliwia tworzenie serii reguł konfiguracji aparatu, z których każdy składa się z zestawu reguł. Poniżej przedstawiono najbardziej przydatną terminologię wykonywaną podczas konfigurowania aparatu reguł. 

- *Konfiguracja aparatu reguł*: zestaw reguł, które są stosowane do reguły pojedynczej trasy. Każda konfiguracja jest ograniczona do 5 reguł. Można utworzyć maksymalnie 10 konfiguracji. 
- *Reguła aparatu reguł*: reguła składająca się z maksymalnie 10 warunków zgodności i 5 akcji.
- *Warunek dopasowania*: istnieje wiele warunków dopasowania, których można użyć do analizowania żądań przychodzących. Reguła może zawierać maksymalnie 10 warunków dopasowywania. Warunki dopasowania są oceniane przy użyciu operatora **i** . Pełną listę warunków dopasowania można znaleźć [tutaj](front-door-rules-engine-match-conditions.md). 
- *Akcja*: akcje wskazują, co się dzieje z żądaniami przychodzącymi — akcje w nagłówku żądania/odpowiedzi, przekazywanie, przekierowania i ponowne zapisywanie są obecnie dostępne. Reguła może zawierać maksymalnie 5 akcji; jednak reguła może zawierać tylko 1 przesłonięcie konfiguracji trasy.  Pełną listę akcji można znaleźć [tutaj](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować pierwszą [konfigurację aparatu reguł](front-door-tutorial-rules-engine.md). 
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
