---
title: Akcje w aparacie reguł standardowych dla Azure CDN | Microsoft Docs
description: Dokumentacja referencyjna dotycząca akcji w aparacie standardowych reguł dla platformy Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760128"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Akcje w aparacie reguł standardowych dla Azure CDN

W [aparacie reguł standardowych](cdn-standard-rules-engine.md) dla platformy Azure Content Delivery Network (Azure CDN) reguła zawiera co najmniej jeden warunek dopasowania i akcję. Ten artykuł zawiera szczegółowe opisy akcji, których można użyć w aparacie reguł standardowych dla Azure CDN.

Druga część reguły jest akcją. Akcja definiuje zachowanie stosowane do typu żądania, który identyfikuje warunek dopasowania lub zestaw warunków zgodności.

## <a name="actions"></a>Akcje

Następujące akcje są dostępne do użycia w aparacie reguł standardowych dla Azure CDN. 

### <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej

Użyj tej akcji, aby zastąpić wartość czasu wygaśnięcia (TTL) punktu końcowego dla żądań, które są zgodne z warunkami określonymi przez reguły.

#### <a name="required-fields"></a>Pola wymagane

Zachowanie pamięci podręcznej |  Opis              
---------------|----------------
Pomiń pamięć podręczną | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, zawartość nie jest buforowana.
Zastąpienie | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, wartość czasu wygaśnięcia zwracana z pochodzenia jest zastępowana wartością określoną w akcji. Takie zachowanie będzie stosowane tylko w przypadku, gdy odpowiedź jest w pamięci podręcznej. W przypadku nagłówka odpowiedzi Cache-Control z wartościami "no-cache" ("Private", "No-Store") akcja nie będzie stosowana.
Ustaw, jeśli brakuje | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, jeśli wartość czasu wygaśnięcia nie została zwrócona ze źródła, reguła ustawia czas wygaśnięcia na wartość określoną w akcji. Takie zachowanie będzie stosowane tylko w przypadku, gdy odpowiedź jest w pamięci podręcznej. W przypadku nagłówka odpowiedzi Cache-Control z wartościami "no-cache" ("Private", "No-Store") akcja nie będzie stosowana.

#### <a name="additional-fields"></a>Dodatkowe pola

Dni | Godziny | Minuty | Sekundy
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>Ciąg zapytania klucza pamięci podręcznej

Użyj tej akcji, aby zmodyfikować klucz pamięci podręcznej na podstawie ciągów zapytań.

#### <a name="required-fields"></a>Pola wymagane

Zachowanie | Opis
---------|------------
Uwzględnij | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytania określone w parametrach są uwzględniane podczas generowania klucza pamięci podręcznej. 
Buforuj każdy unikatowy adres URL | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, każdy unikatowy adres URL ma swój własny klucz pamięci podręcznej. 
Exclude | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytania określone w parametrach są wykluczone podczas generowania klucza pamięci podręcznej.
Ignoruj ciągi zapytań | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, ciągi zapytania nie są brane pod uwagę podczas generowania klucza pamięci podręcznej. 

### <a name="modify-request-header"></a>Modyfikuj nagłówek żądania

Użyj tej akcji, aby zmodyfikować nagłówki, które są obecne w żądaniach wysyłanych do źródła.

#### <a name="required-fields"></a>Pola wymagane

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Dołączanie | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do żądania o określonej wartości. Jeśli nagłówek już istnieje, wartość jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do żądania o określonej wartości. Jeśli nagłówek już istnieje, określona wartość zastępuje istniejącą wartość. | Ciąg
Usuwanie | Gdy ta opcja jest zaznaczona, reguła jest zgodna, a nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z żądania. | Ciąg

### <a name="modify-response-header"></a>Modyfikuj nagłówek odpowiedzi

Użyj tej akcji, aby zmodyfikować nagłówki, które są obecne w odpowiedziach zwracanych do klientów.

#### <a name="required-fields"></a>Pola wymagane

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Dołączanie | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek jest już obecny, **wartość** jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Gdy ta opcja jest zaznaczona, a reguła jest zgodna, nagłówek określony w **nazwie nagłówka** jest dodawany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek już istnieje, **wartość** zastępuje istniejącą wartość. | Ciąg
Usuwanie | Gdy ta opcja jest zaznaczona, reguła jest zgodna, a nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z odpowiedzi. | Ciąg

### <a name="url-redirect"></a>Przekierowywanie adresów URL

Użyj tej akcji, aby przekierować klientów do nowego adresu URL. 

#### <a name="required-fields"></a>Pola wymagane

Pole | Opis 
------|------------
Type | Wybierz typ odpowiedzi, aby powrócić do obiektu żądającego: znaleziono (302), przeniesione (301), tymczasowe przekierowanie (307) i trwałe przekierowanie (308).
Protokół | Żądanie dopasowania, HTTP i HTTPS.
Hostname (Nazwa hosta) | Wybierz nazwę hosta, do którego chcesz przekierować żądanie. Pozostaw to pole puste, aby zachować hosta przychodzącego.
Ścieżka | Zdefiniuj ścieżkę do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować ścieżkę przychodzącą.  
Ciąg zapytania | Zdefiniuj ciąg zapytania używany w przekierowaniu. Pozostaw to pole puste, aby zachować ciąg zapytania przychodzącego. 
Fragment | Zdefiniuj fragment do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować fragment przychodzący. 

Zdecydowanie zalecamy używanie bezwzględnego adresu URL. Użycie względnego adresu URL może przekierować adresy URL Azure CDN do nieprawidłowej ścieżki. 

### <a name="url-rewrite"></a>Regenerowanie adresów URL

Użyj tej akcji, aby ponownie napisać ścieżkę żądania, które jest trasy do źródła.

#### <a name="required-fields"></a>Pola wymagane

Pole | Opis 
------|------------
Wzorzec źródła | Zdefiniuj wzorzec źródłowy w ścieżce URL, który ma zostać zamieniony. Obecnie wzorzec źródłowy używa dopasowania opartego na prefiksie. Aby dopasować wszystkie ścieżki URL, użyj ukośnika ( **/** ) jako wartości wzorca źródła.
Element docelowy | Zdefiniuj ścieżkę docelową do użycia podczas ponownego zapisywania. Ścieżka docelowa zastępuje wzorzec źródłowy.
Zachowaj niedopasowaną ścieżkę | W przypadku ustawienia wartości **tak**pozostała ścieżka po wzorcu źródłowym zostanie dołączona do nowej ścieżki docelowej. 

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure CDN](cdn-overview.md)
- [Dokumentacja aparatu reguł standardowych](cdn-standard-rules-engine-reference.md)
- [Warunki dopasowania w aparacie standardowych reguł](cdn-standard-rules-engine-match-conditions.md)
- [Wymuszanie protokołu HTTPS za pomocą aparatu reguł standardowych](cdn-standard-rules-engine.md)
