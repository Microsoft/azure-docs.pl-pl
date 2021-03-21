---
title: Konfigurowanie akcji zestawu reguł Standard/Premium platformy Azure
description: Ten artykuł zawiera listę różnych akcji, które można wykonać przy użyciu zestawu reguł dla drzwi platformy Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100046"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Akcje zestawu reguł Standard/Premium platformy Azure

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

[Zestaw reguł](concept-rule-set.md) drzwi frontonu platformy Azure składa się z reguł z kombinacją warunków i akcji dopasowywania. Ten artykuł zawiera szczegółowy opis akcji, których można użyć w zestawie reguł. Akcja definiuje zachowanie, które jest stosowane do typu żądania identyfikowanego przez warunek zgodności. W zestawie reguł dla drzwi frontonu platformy Azure reguła może zawierać maksymalnie pięć akcji. Zmienna serwerowa jest obsługiwana we wszystkich akcjach.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Następujące akcje są dostępne do użycia w zestawie reguł dla drzwi platformy Azure.  

## <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej

Użyj tej akcji, aby zastąpić wartość czasu wygaśnięcia (TTL) punktu końcowego dla żądań, które są zgodne z warunkami określonymi przez reguły.

### <a name="required-fields"></a>Pola wymagane

Następujący opis ma zastosowanie w przypadku wybrania tych zachowań pamięci podręcznej i dopasowania reguły:

Zachowanie pamięci podręcznej |  Opis              
---------------|----------------
Pomiń pamięć podręczną | Zawartość nie jest buforowana.
Zastąpienie | Wartość czasu wygaśnięcia zwracana z źródła jest zastępowana wartością określoną w akcji. Takie zachowanie będzie stosowane tylko w przypadku, gdy odpowiedź jest w pamięci podręcznej. W przypadku nagłówka odpowiedzi Cache-Control z wartościami "no-cache" ("Private", "No-Store") akcja nie będzie stosowana.
Ustaw, jeśli brakuje | Jeśli wartość czasu wygaśnięcia nie zostanie zwrócona ze źródła, reguła ustawia czas wygaśnięcia na wartość określoną w akcji. Takie zachowanie będzie stosowane tylko w przypadku, gdy odpowiedź jest w pamięci podręcznej. W przypadku nagłówka odpowiedzi Cache-Control z wartościami "no-cache" ("Private", "No-Store") akcja nie będzie stosowana.

### <a name="additional-fields"></a>Dodatkowe pola

Dni | Godziny | Minuty | Sekundy
-----|-------|---------|--------
int | int | int | int 

## <a name="cache-key-query-string"></a>Ciąg zapytania klucza pamięci podręcznej

Użyj tej akcji, aby zmodyfikować klucz pamięci podręcznej na podstawie ciągów zapytań.

### <a name="required-fields"></a>Pola wymagane

Następujący opis ma zastosowanie w przypadku wybrania tych zachowań i dopasowania reguły:

Zachowanie | Opis
---------|------------
Uwzględnij | Ciągi zapytania określone w parametrach Get są uwzględniane podczas generowania klucza pamięci podręcznej. 
Buforuj każdy unikatowy adres URL | Każdy unikatowy adres URL ma swój własny klucz pamięci podręcznej. 
Exclude | Ciągi zapytania określone w parametrach zostaną wykluczone, gdy zostanie wygenerowany klucz pamięci podręcznej.
Ignoruj ciągi zapytań | Ciągi zapytań nie są brane pod uwagę podczas generowania klucza pamięci podręcznej. 

## <a name="modify-request-header"></a>Modyfikuj nagłówek żądania

Użyj tej akcji, aby zmodyfikować nagłówki, które są obecne w żądaniach wysyłanych do źródła.

### <a name="required-fields"></a>Pola wymagane

Następujący opis ma zastosowanie w przypadku wybrania tych akcji i dopasowania reguły:

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Append | Nagłówek określony w **nazwie nagłówka** zostanie dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, wartość jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Nagłówek określony w **nazwie nagłówka** zostanie dodany do żądania o określonej wartości. Jeśli nagłówek już istnieje, określona wartość zastępuje istniejącą wartość. | Ciąg
Usuń | Jeśli nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z żądania. | Ciąg

## <a name="modify-response-header"></a>Modyfikuj nagłówek odpowiedzi

Użyj tej akcji, aby zmodyfikować nagłówki, które są obecne w odpowiedziach zwracanych do klientów.

### <a name="required-fields"></a>Pola wymagane

Następujący opis ma zastosowanie w przypadku wybrania tych akcji i dopasowania reguły:

Akcja | Nazwa nagłówka HTTP | Wartość
-------|------------------|------
Append | Nagłówek określony w **nazwie nagłówka** zostaje dodany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek jest już obecny, **wartość** jest dołączana do istniejącej wartości. | Ciąg
Zastąp | Nagłówek określony w **nazwie nagłówka** zostaje dodany do odpowiedzi przy użyciu określonej **wartości**. Jeśli nagłówek już istnieje, **wartość** zastępuje istniejącą wartość. | Ciąg
Usuń | Jeśli nagłówek określony w regule jest obecny, nagłówek zostanie usunięty z odpowiedzi. | Ciąg

## <a name="url-redirect"></a>Przekierowywanie adresów URL

Użyj tej akcji, aby przekierować klientów do nowego adresu URL. 

### <a name="required-fields"></a>Pola wymagane

Pole | Opis 
------|------------
Typ przekierowania | Wybierz typ odpowiedzi, aby powrócić do obiektu żądającego: znaleziono (302), przeniesione (301), tymczasowe przekierowanie (307) i trwałe przekierowanie (308).
Protokół przekierowania | Żądanie dopasowania, HTTP i HTTPS.
Host docelowy | Wybierz nazwę hosta, do którego chcesz przekierować żądanie. Pozostaw to pole puste, aby zachować hosta przychodzącego.
Ścieżka docelowa | Zdefiniuj ścieżkę do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować ścieżkę przychodzącą.  
Ciąg zapytania | Zdefiniuj ciąg zapytania używany w przekierowaniu. Pozostaw to pole puste, aby zachować ciąg zapytania przychodzącego. 
Fragment docelowy | Zdefiniuj fragment do użycia w przekierowaniu. Pozostaw to pole puste, aby zachować fragment przychodzący. 

## <a name="url-rewrite"></a>Regenerowanie adresów URL

Użyj tej akcji, aby ponownie napisać ścieżkę żądania, które jest trasy do źródła.

### <a name="required-fields"></a>Pola wymagane

Pole | Opis 
------|------------
Wzorzec źródła | Zdefiniuj wzorzec źródłowy w ścieżce URL, który ma zostać zamieniony. Obecnie wzorzec źródłowy używa dopasowania opartego na prefiksie. Aby dopasować wszystkie ścieżki URL, użyj ukośnika ( **/** ) jako wartości wzorca źródła.
Element docelowy | Zdefiniuj ścieżkę docelową do użycia podczas ponownego zapisywania. Ścieżka docelowa zastępuje wzorzec źródłowy.
Zachowaj niedopasowaną ścieżkę | W przypadku ustawienia wartości **tak** pozostała ścieżka po wzorcu źródłowym zostanie dołączona do nowej ścieżki docelowej. 

## <a name="server-variable"></a>Zmienna serwera

### <a name="supported-variables"></a>Obsługiwane zmienne

| Nazwa zmiennej | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | Adres IP bezpośredniego połączenia z krawędzią frontu drzwi platformy Azure. Jeśli Klient użył serwera proxy HTTP lub modułu równoważenia obciążenia do wysłania żądania, wartość SocketIp jest adresem IP serwera proxy lub modułu równoważenia obciążenia. |
| client_ip                  | Adres IP klienta, który dokonał oryginalnego żądania. Jeśli w żądaniu wystąpiło wyrażenie X-Forwarded-For, adres IP klienta zostanie pobrany z tego samego. |
| client_port                | Port IP klienta, który wykonał żądanie. |
| nazw                      | Nazwa hosta w żądaniu od klienta. |
| geo_country                     | Wskazuje kraj/region osoby żądającej za pomocą kodu kraju/regionu. |
| http_method                | Metoda używana do żądania adresu URL. Na przykład Pobierz lub Opublikuj. |
| http_version               | Protokół żądania. Zazwyczaj HTTP/1.0, HTTP/1.1 lub HTTP/2.0. |
| query_string               | Lista par zmienna/wartość, które następuje po "?" w żądanym adresie URL. Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING wartość będzie równa *ID = 123&title = Fabrikam* |
| request_scheme             | Schemat żądania: http lub https. |
| request_uri                | Pełny identyfikator URI żądania (z argumentami). Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI wartość będzie */article.aspx? id = 123&title = Fabrikam* |
| server_port                | Port serwera, który zaakceptował żądanie. |
| ssl_protocol    | Protokół ustanowionego połączenia TLS. |
| url_path                   | Identyfikuje określony zasób na hoście, do którego klient sieci Web chce uzyskać dostęp. Jest to część identyfikatora URI żądania bez argumentów. Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path wartość zostanie */article.aspx* |

### <a name="server-variable-format"></a>Format zmiennej serwera    

**Format:** {Variable: offset}, {Variable: offset: length}, {Variable}

### <a name="supported-server-variable-actions"></a>Obsługiwane akcje zmiennych serwera

* Nagłówek żądania
* Nagłówek odpowiedzi
* Ciąg zapytania klucza pamięci podręcznej
* Regenerowanie adresów URL
* Przekierowywanie adresów URL

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zestawie reguł stanard/Premium platformy Azure](concept-rule-set.md).
* Dowiedz się więcej o [warunkach dopasowania zestawu reguł](concept-rule-set-match-conditions.md).
