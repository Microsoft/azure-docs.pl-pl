---
title: Usługa Azure API Management diagnozowanie i rozwiązywanie problemów
description: Dowiedz się, jak rozwiązywać problemy z interfejsem API w usłudze Azure API Management przy użyciu narzędzia diagnozowanie i rozwiązywanie w Azure Portal.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652406"
---
# <a name="azure-api-management-diagnostics-overview"></a>Omówienie usługi Azure API Management Diagnostics

Podczas kompilowania i zarządzania interfejsem API w usłudze Azure API Management, chcesz przystąpić do wszelkich problemów, które mogą wystąpić, od 404 błędnych błędów bramy do 502. API Management Diagnostics to inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z interfejsem API opublikowanym w APIM bez konieczności konfigurowania. Gdy wystąpią problemy z opublikowanymi interfejsami API, program API Management Diagnostics wskazuje, co się nie dzieje, i prowadzi Cię do odpowiednich informacji w celu szybkiego rozwiązywania problemów i rozwiązywania problemu.

Chociaż to środowisko jest najbardziej przydatne w przypadku problemów z interfejsem API w ciągu ostatnich 24 godzin, wszystkie wykresy diagnostyczne są zawsze dostępne do analizy.

## <a name="open-api-management-diagnostics"></a>Otwórz diagnostykę API Management

Aby uzyskać dostęp do diagnostyki API Management, przejdź do wystąpienia usługi API Management w [Azure Portal](https://portal.azure.com). W lewym okienku nawigacji wybierz pozycję **Diagnozuj i rozwiąż problemy**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Zrzut ekranu pokazuje, jak przejść do diagnostyki.":::



## <a name="intelligent-search"></a>Inteligentne wyszukiwanie

Problemy lub problemy można wyszukiwać na pasku wyszukiwania u góry strony. Wyszukiwanie ułatwia również znalezienie narzędzi, które mogą pomóc w rozwiązywaniu problemów lub rozwiązywaniu problemów. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="zrzut ekranu przedstawiający inteligentne wyszukiwanie.":::


## <a name="troubleshooting-categories"></a>Kategorie rozwiązywania problemów

Problemy można rozwiązać w obszarze Kategorie. Typowe problemy dotyczące wydajności interfejsu API, bramy, zasad interfejsu API i warstwy usług można analizować w ramach każdej kategorii. Każda kategoria zawiera również bardziej szczegółowe testy diagnostyczne. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="zrzut ekranu przedstawiający przegląd kategorii.":::


### <a name="availability-and-performance"></a>Dostępność i wydajność

Sprawdź dostępność interfejsu API i problemy z wydajnością w tej kategorii. Po wybraniu tego kafelka kategorii zobaczysz kilka typowych testów zalecanych w interfejsie interaktywnym. Kliknij każdy z nich, aby szczegółowe głębokie informacje o poszczególnych problemach. W tym zadaniu zostanie również wyświetlony wykres przedstawiający wydajność interfejsu API oraz podsumowanie problemów z wydajnością. Na przykład usługa interfejsu API mogła mieć błąd 5xx i przekroczenie limitu czasu w ciągu ostatniej godziny w zapleczu. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Zrzut ekranu 1 sprawdzenia interfejsu interakcyjnego.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Zrzut ekranu 2 z interaktywnego sprawdzania interfejsu.":::

### <a name="api-policies"></a>Zasady interfejsu API

Ta kategoria wykrywa błędy i powiadamia użytkownika o problemach z zasadami. 

Podobny interfejs interaktywny prowadzi do metryk danych w celu ułatwienia rozwiązywania problemów z konfiguracją zasad interfejsu API.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="zrzut ekranu przedstawiający kafelek kategorii zasady interfejsu API.":::

### <a name="gateway-performance"></a>Wydajność bramy 

W przypadku żądań lub odpowiedzi bramy lub dowolnych błędów 4xx lub 5xx w bramie Użyj tej kategorii do monitorowania i rozwiązywania problemów. Podobnie, można wykorzystać interfejs interaktywny do szczegółowe głębokiego w określonym obszarze, który ma być sprawdzany pod kątem wydajności bramy interfejsu API. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="zrzut ekranu przedstawiający kafelek Kategoria wydajności bramy.":::

### <a name="service-upgrade"></a>Uaktualnienie usługi

Ta kategoria sprawdza, która warstwa usług jest aktualnie używana, i przypomina, aby przeprowadzić uaktualnienie, aby uniknąć problemów, które mogą być związane z tą warstwą. Ten sam interfejs interaktywny ułatwia dokładne przechodzenie do większej liczby grafik i wyników sprawdzenia podsumowania. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="zrzut ekranu przedstawiający kafelek kategorii uaktualnienia usługi.":::

## <a name="search-documentation"></a>Wyszukaj w dokumentacji

Dodatkowe narzędzia do diagnozowania i rozwiązywania problemów można znaleźć w dokumentacji dotyczącej rozwiązywania problemów związanych z problemem. Po uruchomieniu diagnostyki w usłudze wybierz pozycję **Szukaj dokumentacji** w interfejsie interaktywnym. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="zrzut ekranu 1 przedstawiający sposób użycia funkcji dokumentacji wyszukiwania.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="zrzut ekranu 2 przedstawiający sposób korzystania z dokumentacji wyszukiwania.":::


## <a name="next-steps"></a>Następne kroki

* Za pomocą usługi [API Analytics](howto-use-analytics.md) można także analizować użycie i wydajność interfejsów API. 
* Chcesz rozwiązać problemy związane z Web Appsą z diagnostyką? Przeczytaj to [tutaj](../app-service/overview-diagnostics.md)
* Skorzystaj z diagnostyki, aby sprawdzić problemy dotyczące usług Azure Kubernetes Services. Zobacz [Diagnostyka w systemie AKS](../aks/concepts-diagnostics.md)
* Opublikuj swoje pytania lub opinie w witrynie [UserVoice](https://feedback.azure.com/forums/248703-api-management) , dodając w tytule "[diag]".
