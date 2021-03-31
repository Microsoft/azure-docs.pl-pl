---
title: Monitorowanie za pomocą usługi Azure Front-Routing — dopasowanie reguły | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, w jaki sposób platforma Azure front jest zgodna z regułą routingu do użycia w żądaniu przychodzącym
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449217"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Jak są dopasowywane żądania do reguły routingu

Po ustanowieniu połączenia i zakończeniu uzgadniania protokołu TLS, gdy żądanie jest pokrywane w środowisku czołowym, jedna z pierwszych elementów jest określana, która reguła routingu będzie zgodna z żądaniem, a następnie wykonać zdefiniowaną akcję w konfiguracji. W poniższym dokumencie wyjaśniono, jak drzwi z przodu określają konfigurację tras, która ma być używana podczas przetwarzania żądania HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Struktura konfiguracji trasy z drzwiami
Konfiguracja reguły routingu dla drzwi przednich składa się z dwóch głównych części: "po lewej stronie" i "po prawej stronie". Dopasowujemy żądanie przychodzące do lewej strony trasy, a po prawej stronie definiuje sposób przetwarzania żądania.

### <a name="incoming-match-left-hand-side"></a>Dopasowanie przychodzące (po lewej stronie)
Następujące właściwości określają, czy żądanie przychodzące jest zgodne z regułą routingu (lub po lewej stronie):

* **Protokoły http** (http/https)
* **Hosty** (na przykład www \. foo.com, \* . bar.com)
* **Ścieżki** (na przykład \* /users/ \* ,/file.gif)

Te właściwości są rozszerzane wewnętrznie w taki sposób, aby każda kombinacja protokołów/hosta/ścieżki była możliwym zestawem dopasowywania.

### <a name="route-data-right-hand-side"></a>Dane trasy (po prawej stronie)
Decyzja o sposobie przetwarzania żądania zależy od tego, czy buforowanie jest włączone, czy nie dla określonej trasy. Jeśli więc nie masz zbuforowanej odpowiedzi na żądanie, przekażemy żądanie do odpowiedniego zaplecza w skonfigurowanej puli zaplecza.

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
Po ustaleniu konkretnego hosta frontonu i zafiltrowaniu możliwych reguł routingu do tylko tras z tym hostem frontonu, a następnie przefiltruje reguły routingu na podstawie ścieżki żądania. Używamy podobnej logiki jako hostów frontonu:

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
Po dopasowaniu do jednej reguły routingu przed drzwiami należy wybrać sposób przetwarzania żądania. Jeśli dla dopasowanej reguły routingu, drzwi z przodu mają dostępną w pamięci podręcznej odpowiedź, ta sama wartość zostanie pobrana z powrotem do klienta. W przeciwnym razie kolejnym wynikiem jest to, czy skonfigurowano ponowne [Zapisywanie adresów URL (niestandardowa ścieżka przesyłania dalej)](front-door-url-rewrite.md) dla dopasowanej reguły routingu. Jeśli nie zdefiniowano niestandardowej ścieżki przesyłania dalej, żądanie zostanie przekazane do odpowiedniego zaplecza w skonfigurowanej puli zaplecza jako. W przeciwnym razie Ścieżka żądania jest aktualizowana zgodnie z definicją [niestandardowej ścieżki przekazywania](front-door-url-rewrite.md) , a następnie przesyłana dalej do zaplecza.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
