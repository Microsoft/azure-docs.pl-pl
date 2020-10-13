---
title: Akcje aparatu reguł dla drzwi platformy Azure
description: Ten artykuł zawiera listę różnych akcji, które można wykonać za pomocą aparatu reguł dla drzwi platformy Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569777"
---
# <a name="azure-front-door-rules-engine-actions"></a>Akcje Aparatu reguł usługi Azure Front Door

W [aparacie reguł AFD](front-door-rules-engine.md)reguła składa się z zero lub więcej pasujących warunków i akcji. Ten artykuł zawiera szczegółowe opisy akcji, których można użyć w aparacie reguł AFD.

Akcja definiuje zachowanie stosowane do typu żądania, który identyfikuje warunek dopasowania lub zestaw warunków zgodności. W aparacie reguł AFD reguła może zawierać maksymalnie pięć akcji. Tylko jedna z nich może być akcją przesłaniania konfiguracji trasy (do przodu lub przekierowania).

Następujące akcje są dostępne do użycia w aparacie reguł dla drzwi platformy Azure.  

## <a name="modify-request-header"></a>Modyfikuj nagłówek żądania

Użyj tej akcji, aby zmodyfikować nagłówki, które są obecne w żądaniach wysyłanych do źródła.

### <a name="required-fields"></a>Pola wymagane

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Dołączanie | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** zostaje dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, wartość jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** zostaje dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, określona wartość zastępuje istniejącą wartość. | Ciąg
Usuwanie | Gdy ta opcja jest zaznaczona z pasującymi regułami, a nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z żądania. | Ciąg

## <a name="modify-response-header"></a>Modyfikuj nagłówek odpowiedzi

Użyj tej akcji, aby zmodyfikować nagłówki, które są obecne w odpowiedziach zwracanych do klientów.

### <a name="required-fields"></a>Pola wymagane

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Dołączanie | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** zostaje dodany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek jest już obecny, **wartość** jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek już istnieje, **wartość** zastępuje istniejącą wartość. | Ciąg
Usuwanie | Gdy ta opcja jest zaznaczona, a reguła pasuje do nagłówka określonego w regule, nagłówek zostanie usunięty z odpowiedzi. | Ciąg

## <a name="route-configuration-overrides"></a>Przesłonięcia konfiguracji trasy 

### <a name="route-type-redirect"></a>Typ trasy: przekierowanie

Użyj tej akcji, aby przekierować klientów do nowego adresu URL. 

#### <a name="required-fields"></a>Pola wymagane

Pole | Opis 
------|------------
Typ przekierowania | Wybierz typ odpowiedzi, aby powrócić do obiektu żądającego: znaleziono (302), przeniesione (301), tymczasowe przekierowanie (307) i trwałe przekierowanie (308).
Protokół przekierowania | Żądanie dopasowania, HTTP i HTTPS.
Host docelowy | Wybierz nazwę hosta, do którego chcesz przekierować żądanie. Pozostaw to pole puste, aby zachować hosta przychodzącego.
Ścieżka docelowa | Zdefiniuj ścieżkę do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować ścieżkę przychodzącą.  
Ciąg zapytania | Zdefiniuj ciąg zapytania używany w przekierowaniu. Pozostaw to pole puste, aby zachować ciąg zapytania przychodzącego. 
Fragment docelowy | Zdefiniuj fragment do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować fragment przychodzący. 


### <a name="route-type-forward"></a>Typ trasy: Prześlij dalej

Ta akcja umożliwia przekazanie klientom nowego adresu URL. Ta akcja zawiera również akcje podrzędne dotyczące ponownego zapisywania i buforowania adresów URL. 

Pole | Opis 
------|------------
Pula zaplecza | Wybierz pulę zaplecza, aby przesłonić i obsłużyć żądania, spowoduje to również wyświetlenie wszystkich wstępnie skonfigurowanych pul zaplecza w profilu frontonu. 
Protokół przekazywania | Żądanie dopasowania, HTTP i HTTPS.
Regenerowanie adresów URL | Użyj tej akcji, aby ponownie napisać ścieżkę żądania, które jest trasy do źródła. Jeśli ta funkcja jest włączona, zobacz następujące dodatkowe pola wymagane
Buforowanie | Enabled (Włączone), Disabled (Wyłączone). Zobacz następujące dodatkowe pola wymagane, jeśli są włączone. 

#### <a name="url-rewrite"></a>Regenerowanie adresów URL

To ustawienie służy do konfigurowania opcjonalnej **niestandardowej ścieżki przekazywania** , która będzie używana podczas konstruowania żądania do przesyłania dalej do zaplecza.

Pole | Opis 
------|------------
Ścieżka niestandardowego przesyłania dalej | Zdefiniuj ścieżkę do przekazywania żądań. 

#### <a name="caching"></a>Buforowanie

Te ustawienia służą do kontrolowania sposobu, w jaki pliki są buforowane dla żądań zawierających ciągi zapytań. Określa, czy zawartość jest buforowana na podstawie wszystkich parametrów, czy też dla wybranych parametrów. Możesz użyć dodatkowych ustawień, aby zastąpić wartość czasu wygaśnięcia (TTL), aby określić, jak długo zawartość pozostaje w pamięci podręcznej. Aby wymusić buforowanie jako akcję, ustaw dla pola buforowanie wartość "włączone". Po wymuszeniu buforowania są wyświetlane następujące opcje: 

Zachowanie pamięci podręcznej |  Opis              
---------------|----------------
Ignoruj ciągi zapytań | Gdy element zawartości zostanie zbuforowany, wszystkie żądania, które zostaną zignorowane, zignorują ciągi zapytania do momentu wygaśnięcia pamięci podręcznej.
Buforuj każdy unikatowy adres URL | Każde żądanie z unikatowym adresem URL, w tym ciąg zapytania, jest traktowane jako unikatowy element zawartości z własną pamięcią podręczną.
Ignoruj określone ciągi zapytań | Ciągi zapytania adresu URL żądania wymienione w ustawieniu "parametry zapytania" są ignorowane dla buforowania.
Dołącz określone ciągi zapytań | Ciągi zapytania adresu URL żądania wymienione w ustawieniu "parametry zapytania" są używane do buforowania.

Dodatkowe pola |  Opis 
------------------|---------------
Kompresja dynamiczna | Drzwi przednich umożliwiają dynamiczne kompresowanie zawartości na krawędzi, co skutkuje krótszą i szybszą odpowiedzią.
Parametry zapytania | Rozdzielana przecinkami lista dozwolonych (lub niedozwolonych) parametrów, które mają być używane jako podstawa buforowania.
Czas trwania pamięci podręcznej | Czas wygaśnięcia pamięci podręcznej w dniach, godzinach, minutach i sekundach. Wszystkie wartości muszą być typu int. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować pierwszy [aparat reguł](front-door-tutorial-rules-engine.md). 
- Dowiedz się więcej na temat [warunków dopasowania aparatu](front-door-rules-engine-match-conditions.md)
- Dowiedz się więcej o [aparacie reguł dla drzwi platformy Azure](front-door-rules-engine.md)
