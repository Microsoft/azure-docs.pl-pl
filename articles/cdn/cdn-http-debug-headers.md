---
title: Nagłówki HTTP do debugowania X-we dla aparatu reguł Azure CDN | Microsoft Docs
description: Nagłówek żądania pamięci podręcznej debugowania X-we zawiera dodatkowe informacje dotyczące zasad pamięci podręcznej, które są stosowane do żądanego elementu zawartości. Te nagłówki są specyficzne dla Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 4154c6a1e739f935022271e7a101f39d3ee5c500
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343024"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Nagłówki HTTP do debugowania X-we dla aparatu reguł Azure CDN
Nagłówek żądania pamięci podręcznej debugowania, `X-EC-Debug` zawiera dodatkowe informacje dotyczące zasad pamięci podręcznej, które są stosowane do żądanego elementu zawartości. Te nagłówki są specyficzne dla **Azure CDN Premium z produktów Verizon** .

## <a name="usage"></a>Użycie
Odpowiedź wysłana z serwerów POP do użytkownika zawiera `X-EC-Debug` nagłówek tylko wtedy, gdy spełnione są następujące warunki:

- [Funkcja nagłówków odpowiedzi pamięci podręcznej debugowania](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) została włączona w aparacie reguł dla określonego żądania.
- Określone żądanie definiuje zestaw nagłówków odpowiedzi w pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi.

## <a name="requesting-debug-cache-information"></a>Żądanie informacji o pamięci podręcznej debugowania
Aby zdefiniować informacje o pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi, należy użyć następujących dyrektyw w określonym żądaniu:

Nagłówek żądania | Opis |
---------------|-------------|
X-we-Debug: x-we-cache | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-we-Debug: x-we-cache-Remote | [Kod stanu pamięci podręcznej](#cache-status-code-information)
X-we-Debug: x-we-Check-pamięć podręczna | [Podlega buforowaniu](#cacheable-response-header)
X-we-Debug: klucz x-EC-cache-Key | [Klucz pamięci podręcznej](#cache-key-response-header)
X-we-Debug: x-EC-cache-State | [Stan pamięci podręcznej](#cache-state-response-header)

### <a name="syntax"></a>Składnia

Nagłówki odpowiedzi pamięci podręcznej debugowania mogą być wymagane przez dołączenie następującego nagłówka i określonych dyrektyw w żądaniu:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Przykładowy nagłówek X-we-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informacje o kodzie stanu pamięci podręcznej
Nagłówek odpowiedzi X-we-Debug może identyfikować serwer i sposób obsługi odpowiedzi przez następujące dyrektywy:

Header | Opis
-------|------------
X-we-Debug: x-we-cache | Ten nagłówek jest raportowany za każdym razem, gdy zawartość jest kierowana przez sieć CDN. Identyfikuje serwer POP, który spełnił żądanie.
X-we-Debug: x-we-cache-Remote | Ten nagłówek jest raportowany tylko wtedy, gdy żądana zawartość została buforowana na serwerze ochrony źródła lub serwerze bramy wdrożenie.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

Nagłówek X-we-Debug raportuje informacje o kodzie stanu pamięci podręcznej w następującym formacie:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Terminy używane w powyższej składni nagłówka odpowiedzi są zdefiniowane w następujący sposób:
- StatusCode: wskazuje, w jaki sposób żądana zawartość była obsługiwana przez usługę CDN, która jest reprezentowana przez kod stanu pamięci podręcznej.
    
    Kod stanu TCP_DENIED może być raportowany, a nie brak, gdy nieautoryzowane żądanie jest odrzucane z powodu Token-Based uwierzytelniania. Jednak kod stanu brak będzie nadal używany podczas wyświetlania raportów o stanie pamięci podręcznej lub nieprzetworzonych danych dziennika.

- Platforma: wskazuje platformę, w której zażądano zawartości. Następujące kody są prawidłowe dla tego pola:

    Kod  | Platforma
    ------| --------
    ECAcc | Duże HTTP
    ECS   | Niewielka HTTP
    ECD   | Application Delivery Network (wdrożenie)

- POP: wskazuje [punkt obecności](cdn-pop-abbreviations.md) , który obsłużył żądanie. 

### <a name="sample-response-headers"></a>Przykładowe nagłówki odpowiedzi

Następujące przykładowe nagłówki zawierają informacje o kodzie stanu pamięci podręcznej dla żądania:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Nagłówek odpowiedzi w pamięci podręcznej
`X-EC-Debug: x-ec-check-cacheable`Nagłówek odpowiedzi wskazuje, czy żądana zawartość mogła być buforowana.

Ten nagłówek odpowiedzi nie wskazuje, czy buforowanie zostało wykonane. Zamiast tego wskazuje, czy żądanie było uprawnione do buforowania.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

`X-EC-Debug`Nagłówek odpowiedzi zgłasza, czy żądanie mogło być buforowane, ma następujący format:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termin używany w powyższej składni nagłówka odpowiedzi jest zdefiniowany w następujący sposób:

Wartość  | Opis
-------| --------
TAK    | Wskazuje, że żądana zawartość była uprawniona do buforowania.
NO     | Wskazuje, że żądana zawartość nie kwalifikuje się do buforowania. Ten stan może być spowodowany jedną z następujących przyczyn: <br /> -Customer-Specific konfiguracja: Konfiguracja specyficzna dla Twojego konta może uniemożliwić serwerom pop buforowanie elementu zawartości. Na przykład aparat reguł może uniemożliwić buforowanie zasobu przez włączenie funkcji pomijania pamięci podręcznej dla żądań kwalifikujących.<br /> -Cache nagłówki odpowiedzi: żądany Cache-Control zasobów i nagłówki wygaśnięcia mogą uniemożliwiać serwerom POP buforowanie.
UNKNOWN | Wskazuje, że serwery nie mogły ocenić, czy żądany zasób był w pamięci podręcznej. Ten stan zwykle występuje, gdy żądanie jest odrzucane z powodu uwierzytelniania opartego na tokenie.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Następujący przykładowy nagłówek odpowiedzi wskazuje, czy żądana zawartość mogła zostać zbuforowana:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Nagłówek odpowiedzi Cache-Key
`X-EC-Debug: x-ec-cache-key`Nagłówek odpowiedzi wskazuje fizyczny klucz pamięci podręcznej skojarzony z żądaną zawartością. Fizyczna pamięć podręczna — klucz składa się ze ścieżki, która identyfikuje zasób na potrzeby buforowania. Innymi słowy, serwery będą sprawdzać, czy w pamięci podręcznej znajduje się podręczna wersja elementu zawartości, zgodnie z jego ścieżką zdefiniowaną w kluczu podręcznym.

Ta fizyczna pamięć podręczna — klucz rozpoczyna się od podwójnego ukośnika (//), po którym następuje protokół używany do żądania zawartości (HTTP lub HTTPS). Następuje ścieżka względna do żądanego zasobu, który rozpoczyna się od punktu dostępu do zawartości (na przykład _/000001/_).

Domyślnie platformy HTTP są skonfigurowane do korzystania z *pamięci podręcznej w warstwie Standardowa*, co oznacza, że ciągi zapytań są ignorowane przez mechanizm buforowania. Ten typ konfiguracji uniemożliwia użycie klucza pamięci podręcznej w celu uwzględnienia danych ciągu zapytania.

Jeśli ciąg zapytania jest rejestrowany w kluczu pamięci podręcznej, zostanie przekonwertowany na jego odpowiednik skrótu, a następnie wstawiony między nazwą żądanego elementu zawartości a jego rozszerzeniem pliku (na przykład &lt; wartość skrótu zasobu &gt; . html).

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

`X-EC-Debug`W nagłówku odpowiedzi są raportowane informacje o kluczach fizycznych pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Następujący przykładowy nagłówek odpowiedzi wskazuje fizyczny klucz pamięci podręcznej dla wymaganej zawartości:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Nagłówek odpowiedzi stanu pamięci podręcznej
`X-EC-Debug: x-ec-cache-state`Nagłówek odpowiedzi wskazuje stan pamięci podręcznej wymaganej zawartości w chwili, gdy zażądano.

### <a name="response-header-format"></a>Format nagłówka odpowiedzi

`X-EC-Debug`W nagłówku odpowiedzi są raportowane informacje o stanie pamięci podręcznej w następującym formacie:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Terminy używane w powyższej składni nagłówka odpowiedzi są zdefiniowane w następujący sposób:

- MASeconds: wskazuje maksymalny wiek (w sekundach) określony przez nagłówki Cache-Control żądanej zawartości.

- MATimePeriod: konwertuje wartość Max-Age (czyli MASeconds) na przybliżoną równowartość większej jednostki (na przykład dni). 

- UnixTime: wskazuje sygnaturę czasową pamięci podręcznej wymaganej zawartości w czasie systemu UNIX (znanej również jako czas POSIX lub Epoka systemu UNIX). Sygnatura czasowa pamięci podręcznej wskazuje początkową datę/godzinę, z której zostanie obliczony czas wygaśnięcia elementu zawartości. 

    Jeśli serwer źródłowy nie używa serwera buforowania HTTP innej firmy lub jeśli ten serwer nie zwraca nagłówka odpowiedzi o wieku, wówczas sygnatura czasowa pamięci podręcznej będzie zawsze datą/godziną, kiedy zasób został pobrany lub ponownie zweryfikowany. W przeciwnym razie serwery POP będą używać pola wiek do obliczania czasu wygaśnięcia elementu zawartości w następujący sposób: pobieranie/RevalidateDateTime — wiek.

- DDD, DD MMM rrrr HH: mm: SS GMT: wskazuje sygnaturę czasową pamięci podręcznej wymaganej zawartości. Aby uzyskać więcej informacji, zobacz powyższy termin UnixTime.

- CASeconds: wskazuje liczbę sekund, które upłynęły od sygnatury czasowej pamięci podręcznej.

- RTSeconds: wskazuje liczbę pozostałych sekund, dla których zawartość buforowana zostanie uznana za świeżą. Ta wartość jest obliczana w następujący sposób: RTSeconds = max-age — wiek pamięci podręcznej.

- RTTimePeriod: konwertuje pozostałą wartość czasu wygaśnięcia (czyli RTSeconds) na przybliżoną równowartość większej jednostki (na przykład dni).

- ExpiresSeconds: wskazuje liczbę sekund pozostałej przed datą/godziną określoną w `Expires` nagłówku odpowiedzi. Jeśli `Expires` Nagłówek odpowiedzi nie został uwzględniony w odpowiedzi, wartość tego terminu to *Brak*.

### <a name="sample-response-header"></a>Przykładowy nagłówek odpowiedzi

Następujący przykładowy nagłówek odpowiedzi wskazuje stan pamięci podręcznej żądanej zawartości w chwili, gdy zażądano:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

