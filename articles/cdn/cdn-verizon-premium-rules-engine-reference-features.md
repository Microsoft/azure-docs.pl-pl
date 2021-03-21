---
title: Azure CDN z funkcji aparatu reguł Verizon Premium
description: Dokumentacja referencyjna Azure CDN z funkcji aparatu reguł Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020419"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN z funkcji aparatu reguł Verizon Premium

W tym artykule przedstawiono szczegółowe opisy funkcji [aparatu reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Trzecią częścią reguły jest funkcja. Funkcja definiuje typ akcji, która jest stosowana do typu żądania, który jest identyfikowany przez zestaw warunków dopasowywania.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Informacje o Azure CDN z funkcji aparatu reguł Verizon Premium

Dostępne typy funkcji to:

* [Dostęp](#access)
* [Buforowanie](#caching)
* [Komentarz](#comment)
* [Nagłówki](#headers)
* [Dzienniki](#logs)
* [Zoptymalizować](#optimize)
* [Źródł](#origin)
* [Specjalizacja](#specialty)
* [Adres URL](#url)
* [Web Application Firewall](#waf)

### <a name="access"></a><a name="access"></a>Access

Te funkcje zostały zaprojektowane w celu kontrolowania dostępu do zawartości.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Odmów dostępu (403)](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Określa, czy wszystkie żądania są odrzucane z niedostępną odpowiedzią 403. |
| [Uwierzytelnianie tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Określa, czy do żądania zostanie zastosowane uwierzytelnianie Token-Based. |
| [Kod odmowy uwierzytelniania tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Określa typ odpowiedzi, która zostanie zwrócona użytkownikowi, gdy żądanie zostanie odrzucone z powodu Token-Based uwierzytelniania. |
| [Adres URL ignorowania tokenu uwierzytelniania](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Określa, czy porównania adresów URL dokonywane przy użyciu uwierzytelniania Token-Based będą rozróżniane wielkości liter. |
| [Parametr uwierzytelniania tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Określa, czy należy zmienić nazwę parametru ciągu zapytania uwierzytelniania Token-Based. |

**[Powrót do początku](#top)**

### <a name="caching"></a><a name="caching"></a>Buforowanie

Te funkcje zostały zaprojektowane w celu dostosowania, kiedy i w jaki sposób zawartość jest buforowana.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Parametry przepustowości](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Określa, czy parametry ograniczenia przepustowości (tj. ec_rate i ec_prebuf) będą aktywne. |
| [Ograniczanie przepustowości](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Ogranicza przepustowość odpowiedzi udostępnionej przez serwery brzegowe. |
| [Pomiń pamięć podręczną](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Określa, czy żądanie może korzystać z naszej technologii buforowania. |
| [Przetwarzanie nagłówka kontroli pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Steruje generowaniem nagłówków Cache-Control przez serwer graniczny, gdy funkcja Max-Age zewnętrzna jest aktywna. |
| [Ciąg zapytania klucza pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Określa, czy **klucz pamięci podręcznej** _ będzie uwzględniał lub wyklucza parametry ciągu zapytania skojarzone z żądaniem. <br> __ Ścieżka względna, która jednoznacznie identyfikuje zasób na potrzeby buforowania.  Nasze serwery brzegowe używają tej ścieżki względnej podczas sprawdzania zawartości w pamięci podręcznej.  Domyślnie klucz pamięci podręcznej nie będzie zawierał ciągu zapytania parameters._ |
| [Pamięć podręczna — ponowne zapisywanie klucza](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | Ponownie zapisuje klucz pamięci podręcznej skojarzony z żądaniem. |
| [Wypełnienie kompletnej pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Określa, co się dzieje, gdy żądanie spowoduje przeznaczenie częściowej pamięci podręcznej na serwerze brzegowym. |
| [Kompresuj typy plików](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Definiuje formaty plików, które zostaną skompresowane na serwerze. | 
| [Domyślny maksymalny wiek wewnętrzny](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Określa domyślny interwał maksymalnego wieku serwera brzegowego na potrzeby ponownej walidacji pamięci podręcznej serwera. |
| [Wygasa traktowanie nagłówków](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Steruje generowaniem nagłówków wygasania przez serwer graniczny, gdy funkcja zewnętrzna Max-Age jest aktywna. |
| [Zewnętrzny maksymalny wiek](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Określa maksymalny okres sprawdzania poprawności pamięci podręcznej serwera programu Edge przez przeglądarkę. |
| [Wymuszaj wewnętrzny maksymalny wiek](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Określa maksymalny okres sprawdzania poprawności pamięci podręcznej serwera usługi Edge dla serwera brzegowego. |
| [Obsługa H. 264 (pobieranie progresywne HTTP)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Określa typy formatów plików H. 264, które mogą być używane do przesyłania strumieniowego zawartości. |
| [H. 264 — parametry wyszukiwania wideo](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Zastępuje nazwy przypisane do parametrów kontrolujących wyszukiwanie za pomocą nośnika H. 264 przy użyciu pobierania progresywnego HTTP. |
| [Honor No-Cache żądania](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Określa, czy żądania braku pamięci podręcznej klienta HTTP będą przekazywane do serwera pochodzenia. |
| [Ignoruj Źródło bez pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Określa, czy nasza sieć CDN zignoruje niektóre dyrektywy, które są obsługiwane przez serwer pochodzenia. |
| [Ignoruj zakresy Unsatisfiable](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Określa odpowiedź, która zostanie zwrócona do klientów, gdy żądanie 416 generuje kod stanu niewłaściwego. |
| [Wewnętrzna maksymalna — nieodświeżona](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Określa, jak długo przeszło czas normalnego czasu wygaśnięcia w pamięci podręcznej, gdy serwer graniczny nie może ponownie sprawdzić poprawności pamięci podręcznej z serwerem pochodzenia. |
| [Częściowe udostępnianie pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Określa, czy żądanie może generować częściowo buforowaną zawartość. |
| [Wstępne Weryfikowanie zawartości w pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Określa, czy zawartość pamięci podręcznej będzie kwalifikować się do wczesnego sprawdzania poprawności przed upływem czasu wygaśnięcia. |
| [Odświeżanie plików pamięci podręcznej Zero-Byte](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Określa, w jaki sposób żądanie klienta HTTP dla zasobu pamięci podręcznej 0-bajtowej jest obsługiwane przez nasze serwery brzegowe. |
| [Ustawianie kodów stanu pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Definiuje zestaw kodów stanu, które mogą prowadzić do zawartości w pamięci podręcznej. |
| [Nieodświeżone dostarczanie zawartości w przypadku błędu](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Określa, czy wygasła buforowana zawartość zostanie dostarczona, gdy wystąpi błąd podczas ponownej walidacji pamięci podręcznej lub gdy pobiera żądaną zawartość z serwera pochodzenia klienta. | 
| [Nieodświeżone podczas weryfikacji](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Zwiększa wydajność dzięki umożliwieniu serwerom brzegowym nieodświeżonemu klientowi żądającemu podczas ponownej walidacji. |

**[Powrót do początku](#top)**

### <a name="comment"></a><a name="comment"></a>Komentarz

Funkcja Comment umożliwia dodanie notatki w ramach reguły.

**[Powrót do początku](#top)**

### <a name="headers"></a><a name="headers"></a>Nagłówki

Te funkcje są przeznaczone do dodawania, modyfikowania lub usuwania nagłówków z żądania lub odpowiedzi.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Nagłówek odpowiedzi dla wieku](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Określa, czy nagłówek odpowiedzi w wieku zostanie uwzględniony w odpowiedzi wysyłanej do osoby żądającej. |
| [Nagłówki odpowiedzi w pamięci podręcznej debugowania](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Określa, czy odpowiedź może zawierać [Nagłówek odpowiedzi X-we-Debug](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) , który zawiera informacje dotyczące zasad pamięci podręcznej dla żądanego elementu zawartości. |
| [Modyfikowanie nagłówka żądania klienta](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | Zastępuje, dołącza lub usuwa nagłówek z żądania. |
| [Modyfikowanie nagłówka odpowiedzi klienta](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | Zastępuje, dołącza lub usuwa nagłówek z odpowiedzi. |
| [Ustawianie niestandardowego nagłówka adresu IP klienta](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Umożliwia dodanie adresu IP żądającego klienta do żądania jako niestandardowego nagłówka żądania. |

**[Powrót do początku](#top)**

### <a name="logs"></a><a name="logs"></a>Dzienniki

Te funkcje są przeznaczone do dostosowywania danych przechowywanych w pierwotnych plikach dziennika.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Pole dziennika niestandardowego 1](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Określa format i zawartość, która zostanie przypisana do pola dziennika niestandardowego w nieprzetworzonym pliku dziennika. |
| [Ciąg zapytania dziennika](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Określa, czy ciąg zapytania będzie przechowywany wraz z adresem URL w dziennikach dostępu. |

**[Powrót do początku](#top)**

### <a name="optimize"></a><a name="optimize"></a>Zoptymalizować

Te funkcje określają, czy żądanie zostanie poddane optymalizacji zapewnianej przez optymalizator brzegowy.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Optymalizator krawędzi](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Określa, czy Optymalizator krawędzi może zostać zastosowany do żądania. |
| [Optymalizator krawędzi — konfiguracja wystąpienia](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Tworzy wystąpienia lub aktywuje konfigurację Optymalizatora krawędzi skojarzoną z lokacją. |

**[Powrót do początku](#top)**

### <a name="origin"></a><a name="origin"></a>Źródł

Te funkcje zostały zaprojektowane w celu kontrolowania, jak sieć CDN komunikuje się z serwerem pochodzenia.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Maksymalna liczba żądań Keep-Alive](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Określa maksymalną liczbę żądań połączenia Keep-Alive, zanim zostanie zamknięty. |
| [Specjalne nagłówki serwera proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Definiuje zestaw [nagłówków żądań specyficznych](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) dla sieci CDN, które będą przekazywane z serwera graniczny do serwera źródłowego. |

**[Powrót do początku](#top)**

### <a name="specialty"></a><a name="specialty"></a>Specjalizacja

Te funkcje zapewniają zaawansowane funkcje, które powinny być używane tylko przez zaawansowanych użytkowników.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Metody HTTP w pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w naszej sieci. |
| [Rozmiar treści żądania pamięci podręcznej](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Definiuje próg określania, czy odpowiedź na wpis może być buforowana. |
| [Protokół QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Określa, czy klient zostanie poinformowany, że nasza Usługa CDN obsługuje protokół QUIC. |
| [Optymalizacja przesyłania strumieniowego](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Dostraja konfigurację buforowania w celu zoptymalizowania wydajności strumieni na żywo i zmniejszenia obciążenia na serwerze źródłowym. |
| [Zmienna użytkownika](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Przypisuje wartość do zmiennej zdefiniowanej przez użytkownika, która jest przesyłana do rozwiązania przetwarzania ruchu Bespoke. |

**[Powrót do początku](#top)**

### <a name="url"></a><a name="url"></a>Adres URL

Te funkcje umożliwiają przekierowanie lub zapisanie żądania w innym adresie URL.

| Nazwa       | Przeznaczenie                                                           |
|------------|-------------------------------------------------------------------|
| [Wykonaj przekierowania](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Określa, czy żądania mogą być przekierowywane do nazwy hosta zdefiniowanej w nagłówku lokalizacji zwróconej przez serwer pochodzenia klienta. |
| [Przekierowanie adresu URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Przekierowuje żądania za pośrednictwem nagłówka lokalizacji. |
| [Ponowne zapisywanie adresów URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | Ponownie zapisuje adres URL żądania. |

**[Powrót do początku](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

Funkcja [Zapora aplikacji sieci Web](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) określa, czy żądanie będzie sprawdzane przez zaporę aplikacji sieci Web.

**[Powrót do początku](#top)**

Najnowsze funkcje można znaleźć w [dokumentacji aparatu reguł Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Przegląd Azure CDN](cdn-overview.md)
