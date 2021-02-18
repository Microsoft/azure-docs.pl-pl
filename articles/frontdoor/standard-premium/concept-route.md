---
title: Co to jest trasa z przodu platformy Azure w warstwie Standardowa/Premium?
description: Ten artykuł pomaga zrozumieć, w jaki sposób platforma Azure Front-Standard/Premium dopasowuje regułę routingu do użycia dla żądania przychodzącego.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100051"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Co to jest trasa usługi Azure Front-Standard/Premium (wersja zapoznawcza)?

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Trasa platformy Azure z przodu Standard/Premium definiuje sposób obsługi ruchu w przypadku nadejścia żądania przychodzącego do środowiska z przodu platformy Azure. Za pomocą ustawień trasy definiuje się skojarzenie między domeną i grupą pierwotną zaplecza. Przez włączenie funkcji zaawansowanych, takich jak wzorzec, do zestawu reguł, bardziej szczegółowej kontroli nad ruchem jest osiągalny.

Konfiguracja routingu w warstwie Standardowa/Premium składa się z dwóch głównych części: "po lewej stronie" i "po prawej stronie". Dopasowujemy żądanie przychodzące po lewej stronie trasy, a po prawej stronie definiuje sposób przetwarzania żądania.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="incoming-match-left-hand-side"></a>Dopasowanie przychodzące (po lewej stronie)

Następujące właściwości określają, czy żądanie przychodzące jest zgodne z regułą routingu (lub po lewej stronie):

* **Protokoły http** (http/https)
* **Hosty** (na przykład www \. foo.com, \* . bar.com)
* **Ścieżki** (na przykład \* /users/ \* ,/file.gif)

Te właściwości są rozszerzane wewnętrznie w taki sposób, aby każda kombinacja protokołów/hosta/ścieżki była możliwym zestawem dopasowywania.

### <a name="route-data-right-hand-side"></a>Dane trasy (po prawej stronie)

Decyzja o sposobie przetwarzania żądania zależy od tego, czy buforowanie jest włączone, czy nie dla trasy. Jeśli zabuforowana odpowiedź jest niedostępna, żądanie jest przekazywane do odpowiedniego zaplecza.

## <a name="route-matching"></a>Dopasowanie trasy

Ta sekcja koncentruje się na tym, jak zgadzamy się z daną regułą routingu przed drzwiami. Podstawowa koncepcja polega na tym, że zawsze pasujemy do **najbardziej konkretnego dopasowania** , które najpierw szuka tylko po lewej stronie.  Najpierw pasujemy do protokołu HTTP, a następnie hosta frontonu, a następnie ścieżki.

### <a name="frontend-host-matching"></a>Dopasowanie hosta frontonu

W przypadku dopasowania hostów frontonu używana jest logika zdefiniowana poniżej:

1. Poszukaj dowolnej trasy z dokładnym dopasowaniem na hoście.
2. Jeśli żadne dokładne hosty frontonu nie pasują do siebie, Odrzuć żądanie i Wyślij błąd nieprawidłowego żądania 400.

Aby uzyskać więcej informacji na temat tego procesu, przyjrzyjmy się przykładowej konfiguracji tras z drzwiami przednimi (tylko po lewej stronie):

| Reguła routingu | Hosty frontonu | Ścieżka |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | \.fabrikam.com www, foo.Adventure-Works.com  | /\*, /images/\* |

Jeśli następujące żądania przychodzące zostały wysłane do przednich drzwi, są one zgodne z następującymi regułami routingu powyżej:

| Przychodzący Host frontonu | Dopasowane reguły routingu |
|---------------------|---------------|
| foo.contoso.com | A, B |
| \.fabrikam.com www | C |
| images.fabrikam.com | Błąd 400: Nieprawidłowe żądanie |
| foo.adventure-works.com | C |
| contoso.com | Błąd 400: Nieprawidłowe żądanie |
| \.Adventure-Works.com www | Błąd 400: Nieprawidłowe żądanie |
| \.northwindtraders.com www | Błąd 400: Nieprawidłowe żądanie |

### <a name="path-matching"></a>Dopasowanie ścieżki

Po pomyślnym przeprowadzeniu przez platformę Azure Front-Premium i przefiltrowaniu określonych reguł routingu na tylko trasy z tym hostem frontonu. Przód drzwi następnie filtruje reguły routingu na podstawie ścieżki żądania. Używamy podobnej logiki jako hostów frontonu:

1. Poszukaj dowolnej reguły routingu z dokładne dopasowanie ścieżki
2. Jeśli nie ma dokładnego dopasowania ścieżki, poszukaj reguł routingu ze ścieżką wieloznaczną pasującą do
3. Jeśli nie odnaleziono żadnych reguł routingu ze zgodną ścieżką, Odrzuć żądanie i zwróć 400: zły błąd żądania HTTP odpowiedzi.

>[!NOTE]
> Wszystkie ścieżki bez symbolu wieloznacznego są traktowane jako ścieżki dokładnego dopasowania. Nawet jeśli ścieżka zostanie zakończona ukośnikiem, nadal jest uważana za dokładne dopasowanie.

Aby jeszcze bardziej wyjaśnić, przyjrzyjmy się innemu zestawowi przykładów:

| Reguła routingu | Host frontonu    | Ścieżka     |
|-------|---------|----------|
| A     | \.contoso.com www | /        |
| B     | \.contoso.com www | /\*      |
| C     | \.contoso.com www | /ab      |
| D     | \.contoso.com www | /abc     |
| E     | \.contoso.com www | ABC    |
| F     | \.contoso.com www | ABC\*  |
| G     | \.contoso.com www | /abc/def |
| H     | \.contoso.com www | /Path   |

Zgodnie z konfiguracją Poniższy przykład tabeli będzie miał wynik:

| Żądanie przychodzące    | Dopasowana trasa |
|---------------------|---------------|
| \.contoso.com/www            | A             |
| \.contoso.com/a www           | B             |
| \.contoso.com/AB www          | C             |
| \.contoso.com/ABC www         | D             |
| \.contoso.com/abzzz www       | B             |
| \.contoso.com/ABC/www        | E             |
| \.contoso.com/ABC/d www       | F             |
| \.contoso.com/ABC/DEF www     | G             |
| \.contoso.com/ABC/defzzz www  | F             |
| \.contoso.com/ABC/DEF/ghi www | F             |
| \.contoso.com/Path www        | B             |
| \.contoso.com/Path/www       | H             |
| \.contoso.com/Path/ZZZ www    | B             |

>[!WARNING]
> </br> Jeśli nie ma żadnych reguł routingu dla hosta frontonu dla dokładnego dopasowania ze ścieżką catch-all ( `/*` ), nie będzie można dopasować do żadnej reguły routingu.
>
> Przykładowa konfiguracja:
>
> | Trasa | Host             | Ścieżka    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /API\* |
>
> Zgodna tabela:
>
> | Żądanie przychodzące       | Dopasowana trasa |
> |------------------------|---------------|
> | profile.domain.com/other | Brak. Błąd 400: Nieprawidłowe żądanie |

### <a name="routing-decision"></a>Decyzja dotycząca routingu

Po dopasowaniu do jednej reguły routingu platformy Azure w warstwie Standardowa/Premium należy wybrać sposób przetwarzania żądania. Jeśli w warstwie Standardowa/Premium platformy Azure dla dopasowanej reguły routingu dostępna jest buforowana odpowiedź, żądanie zostanie obsłużone z powrotem do klienta. W następnej kolejności na platformie Azure przód w warstwie Standardowa/Premium są oceniane ustawienia dotyczące dopasowanej reguły routingu. Jeśli nie zdefiniowano zestawu reguł, żądanie zostanie przekazane do puli zaplecza w postaci, w jakiej jest. W przeciwnym razie zestaw reguł zostanie wykonany w kolejności, w jakiej są skonfigurowane.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
