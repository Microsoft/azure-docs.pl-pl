---
title: Przepisywanie nagłówków HTTP za pomocą bramy aplikacji platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie przepisywania nagłówków HTTP w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132995"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Przepisywanie nagłówków HTTP za pomocą bramy aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji z żądaniem lub odpowiedzią. Przepisując te nagłówki, można wykonać ważne zadania, takie jak dodawanie pól nagłówków związanych z zabezpieczeniami, takich jak HSTS / X-XSS-Protection, usuwanie pól nagłówka odpowiedzi, które mogą ujawniać poufne informacje, i usuwanie informacji o porcie z nagłówków X-Forwarded-For.

Usługa Application Gateway umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP podczas przenoszenia pakietów żądań i odpowiedzi między pulami klientów i zaplecza. Pozwala ona również na dodawanie warunków w celu zapewnienia, że określone nagłówki będą ponownie zapisywane tylko po spełnieniu pewnych warunków.

Brama aplikacji obsługuje również kilka [zmiennych serwera,](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) które ułatwią przechowywanie dodatkowych informacji o żądaniach i odpowiedziach. Ułatwia to tworzenie zaawansowanych reguł przepisywania.

> [!NOTE]
>
> Obsługa ponownego zapisu nagłówka HTTP jest dostępna tylko dla [Standard_V2 i WAF_v2 jednostek SKU](application-gateway-autoscaling-zone-redundant.md).

![Przepisywanie nagłówków](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Obsługiwane nagłówki

Można przepisać wszystkie nagłówki w żądaniach i odpowiedziach, z wyjątkiem nagłówków Host, Connection i Upgrade. Można również użyć bramy aplikacji do tworzenia nagłówków niestandardowych i dodawać je do żądań i odpowiedzi kierowanych za jej pośrednictwem.

## <a name="rewrite-conditions"></a>Przepisz warunki

Można użyć przepisać warunki do oceny zawartości http(S) żądań i odpowiedzi i wykonać nagłówek przepisać tylko wtedy, gdy jeden lub więcej warunków są spełnione. Brama aplikacji używa tych typów zmiennych do oceny zawartości żądań i odpowiedzi HTTP(S):The application gateway uses these types of variables to evaluate the content of HTTP(S) requests and responses:

- nagłówki HTTP w żądaniu.
- nagłówki HTTP w odpowiedzi.
- Zmienne serwera bramy aplikacji.

Warunek służy do oceny, czy określona zmienna jest obecny, czy określona zmienna pasuje do określonej wartości lub czy określona zmienna pasuje do określonego wzorca. [Biblioteka wyrażeń regularnych (PCRE) zgodna z Perl](https://www.pcre.org/) umożliwia skonfigurowanie dopasowania wzorca wyrażeń regularnych w warunkach. Aby dowiedzieć się więcej o składni wyrażenia regularnego, zobacz [stronę główną wyrażeń regularnych Perla](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Przepisywanie akcji

Możesz użyć ponownie przyreduj akcje, aby określić nagłówki żądań i odpowiedzi, które chcesz przepisać i nową wartość nagłówków. Można utworzyć nowy nagłówek, zmodyfikować wartość istniejącego nagłówka lub usunąć istniejący nagłówek. Wartość nowego nagłówka lub istniejącego nagłówka można ustawić na następujące typy wartości:

- Tekst.
- Nagłówek żądania. Aby określić nagłówek żądania, należy użyć składni {http_req_*headerName*}.
- Nagłówek odpowiedzi. Aby określić nagłówek odpowiedzi, należy użyć składni {http_resp_*headerName*}.
- Zmienna serwera. Aby określić zmienną serwera, należy użyć składni {var_*serverVariable*}.
- Kombinacja tekstu, nagłówka żądania, nagłówka odpowiedzi i zmiennej serwera.

## <a name="server-variables"></a>Zmienne serwera

Brama aplikacji używa zmiennych serwera do przechowywania przydatnych informacji o serwerze, połączeniu z klientem i bieżącym żądaniu połączenia. Przykładami przechowywanych informacji są adres IP klienta i typ przeglądarki internetowej. Zmienne serwera zmieniają się dynamicznie, na przykład podczas ładowania nowej strony lub księgowania formularza. Za pomocą tych zmiennych można ocenić warunki ponownego zapisu i przepisać nagłówki.

Brama aplikacji obsługuje następujące zmienne serwera:

| Nazwa zmiennej | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Pole nagłówka żądania klienta X-Forwarded-For ze zmienną `client_ip` (patrz wyjaśnienie w dalszej części tej tabeli) zostało do niego dołączone w formacie IP1, IP2, IP3 itd. Jeśli pole X-Forwarded-For nie znajduje się w `add_x_forwarded_for_proxy` nagłówku żądania `$client_ip` klienta, zmienna jest równa zmiennej. Ta zmienna jest szczególnie przydatna, gdy chcesz przepisać nagłówek X-Forwarded-For ustawiony przez bramę aplikacji, tak aby nagłówek zawierał tylko adres IP bez informacji o porcie. |
| ciphers_supported          | Lista szyfrów obsługiwanych przez klienta.          |
| ciphers_used               | Ciąg szyfrów używany dla ustanowionego połączenia TLS. |
| client_ip                  | Adres IP klienta, od którego brama aplikacji odebrała żądanie. Jeśli istnieje odwrotny serwer proxy przed bramą aplikacji i klientem źródłowym, *client_ip* zwróci adres IP odwrotnego serwera proxy. |
| client_port                | Port klienta.                                                  |
| client_tcp_rtt             | Informacje o połączeniu TCP klienta. Dostępne w systemach obsługujących opcję gniazda TCP_INFO. |
| client_user                | Gdy używane jest uwierzytelnianie HTTP, nazwa użytkownika dostarczona do uwierzytelniania. |
| host                       | W następującej kolejności pierwszeństwa: nazwa hosta z wiersza żądania, nazwa hosta z pola nagłówka żądania hosta lub nazwa serwera pasująca do żądania. |
| *nazwa* cookie_              | Plik cookie *nazwy.*                                            |
| http_method                | Metoda używana do żądania adresu URL. Na przykład GET lub POST. |
| http_status                | Stan sesji. Na przykład 200, 400 lub 403.                       |
| http_version               | Protokół żądania. Zwykle HTTP/1.0, HTTP/1.1 lub HTTP/2.0. |
| Query_string               | Lista par zmiennych/wartości, która następuje po "?" w żądanym adresie URL. |
| received_bytes             | Długość żądania (w tym wiersz żądania, nagłówek i treść żądania). |
| request_query              | Argumenty w wierszu żądania.                                |
| request_scheme             | Schemat żądania: http lub https.                            |
| request_uri                | Pełny oryginalny identyfikator URI żądania (z argumentami).                   |
| sent_bytes                 | Liczba bajtów wysłanych do klienta.                             |
| server_port                | Port serwera, który zaakceptował żądanie.                 |
| ssl_connection_protocol    | Protokół ustanowionego połączenia TLS.        |
| ssl_enabled                | "On", jeśli połączenie działa w trybie TLS. W przeciwnym razie pusty ciąg. |

## <a name="rewrite-configuration"></a>Przepisz konfigurację

Aby skonfigurować przepisywanie nagłówka HTTP, należy wykonać te kroki.

1. Utwórz obiekty, które są wymagane do ponownego zapisu nagłówka HTTP:

   - **Ponownie przepisz akcję:** Służy do określania pól nagłówka żądania i żądania, które chcesz przepisać, oraz nowej wartości nagłówków. Można skojarzyć jeden lub więcej warunków przepisywania z akcją ponownego zapisu.

   - **Warunek przepisania:** Konfiguracja opcjonalna. Przepisz warunki oceny zawartości żądań i odpowiedzi HTTP(S). Akcja ponownego zapisu nastąpi, jeśli żądanie HTTP(S) lub odpowiedź jest zgodna z warunkiem ponownego zapisu.

     Jeśli skojarzysz więcej niż jeden warunek z akcją, akcja występuje tylko wtedy, gdy spełnione są wszystkie warunki. Innymi słowy operacja jest logiczną operacją AND.

   - **Przepisz regułę**: Zawiera wiele kombinacji warunków przepisywania akcji/ przepisywania.

   - **Sekwencja reguł:** Pomaga określić kolejność wykonywania reguł ponownego zapisu. Ta konfiguracja jest przydatna, gdy masz wiele reguł przepisywania w zestawie przepisywania. Reguła ponownego zapisu, która ma niższą wartość sekwencji reguł, jest uruchamiana jako pierwsza. Jeśli przypisać tę samą sekwencję reguł do dwóch reguł przepisywania, kolejność wykonywania jest niedeterministyczne.

   - **Przepisz zestaw:** Zawiera wiele reguł przepisywania, które będą skojarzone z regułą routingu żądań.

2. Dołącz zestaw ponownego zapisu *(rewriteRuleSet)* do reguły routingu. Konfiguracja ponownego zapisu jest dołączona do odbiornika źródłowego za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu konfiguracja ponownego zapisu nagłówka jest skojarzona z odbiornikiem źródłowym i jest regułą na nowo mówiącą o nagłówku globalnym. W przypadku korzystania z reguły routingu opartej na ścieżce konfiguracja przepisywania nagłówka jest definiowana na mapie ścieżki adresu URL. W takim przypadku ma ona zastosowanie tylko do określonego obszaru ścieżki witryny.
   > [!NOTE]
   > Przepisanie adresu URL zmienia nagłówki; nie zmienia adresu URL ścieżki.

Można utworzyć wiele zestawów ponownego zapisu nagłówka HTTP i zastosować każdy zestaw ponownego zapisu do wielu odbiorników. Ale można zastosować tylko jeden zestaw przepisać do określonego odbiornika.

## <a name="common-scenarios"></a>Typowe scenariusze

Oto kilka typowych scenariuszy przy użyciu ponownego zapisu nagłówka.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Usuwanie informacji o porcie z nagłówka X-Forwarded-For

Brama aplikacji wstawia nagłówek X-Forwarded-For do wszystkich żądań, zanim przekaże żądania do wewnętrznej bazy danych. Ten nagłówek jest oddzieloną przecinkami listą portów IP. Mogą istnieć scenariusze, w których serwery zaplecza potrzebują tylko nagłówków, aby zawierały adresy IP. Można użyć ponownego zapisu nagłówka, aby usunąć informacje o porcie z nagłówka X-Forwarded-For. Jednym ze sposobów jest ustawienie nagłówka na add_x_forwarded_for_proxy zmienną serwera:

![Usuń port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modyfikowanie adresu URL przekierowania

Gdy aplikacja zaplecza wysyła odpowiedź przekierowania, można przekierować klienta do innego adresu URL niż określony przez aplikację zaplecza. Na przykład można to zrobić, gdy usługa aplikacji jest hostowana za bramą aplikacji i wymaga od klienta przekierowania do ścieżki względnej. (Na przykład przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2).

Ponieważ usługa App Service jest usługą wielodostępną, używa nagłówka hosta w żądaniu do kierowania żądania do właściwego punktu końcowego. Usługi aplikacji mają domyślną nazwę domeny *.azurewebsites.net (powiedzmy contoso.azurewebsites.net), która różni się od nazwy domeny bramy aplikacji (powiedzmy contoso.com). Ponieważ oryginalne żądanie od klienta ma nazwę domeny bramy aplikacji (contoso.com) jako nazwę hosta, brama aplikacji zmienia nazwę hosta na contoso.azurewebsites.net. Wprowadza tę zmianę, dzięki czemu usługa aplikacji może kierować żądanie do prawidłowego punktu końcowego.

Gdy usługa aplikacji wysyła odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji jego odpowiedzi, jak w żądaniu, które otrzymuje z bramy aplikacji. Dlatego klient będzie żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzić przez bramę aplikacji (contoso.com/path2). Pomijanie bramy aplikacji nie jest pożądane.

Ten problem można rozwiązać, ustawiając nazwę hosta w nagłówku lokalizacji na nazwę domeny bramy aplikacji.

Oto kroki zastępowania nazwy hosta:

1. Utwórz regułę ponownego zapisu z warunkiem, który ocenia, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Wprowadź wzór `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Wykonaj akcję, aby przepisać nagłówek lokalizacji, tak aby miał on adres hosta bramy aplikacji. W tym celu `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` należy wprowadzić jako wartość nagłówka.

![Modyfikowanie nagłówka lokalizacji](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementowanie zabezpieczeń nagłówków HTTP, aby zapobiec lukom w zabezpieczeniach

Można naprawić kilka luk w zabezpieczeniach, implementując niezbędne nagłówki w odpowiedzi aplikacji. Te nagłówki zabezpieczeń obejmują X-XSS-Protection, Strict-Transport-Security i Content-Security-Policy. Za pomocą bramy aplikacji można ustawić te nagłówki dla wszystkich odpowiedzi.

![Nagłówek zabezpieczeń](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Usuwanie niechcianych nagłówków

Można usunąć nagłówki, które ujawniają poufne informacje z odpowiedzi HTTP. Na przykład można usunąć informacje, takie jak nazwa serwera zaplecza, system operacyjny lub szczegóły biblioteki. Za pomocą bramy aplikacji można usunąć następujące nagłówki:

![Usuwanie nagłówka](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Sprawdzanie obecności nagłówka

Można ocenić nagłówek żądania lub odpowiedzi HTTP pod kątem obecności zmiennej nagłówka lub serwera. Ta ocena jest przydatna, gdy chcesz wykonać przepisanie nagłówka tylko wtedy, gdy występuje określony nagłówek.

![Sprawdzanie obecności nagłówka](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Ograniczenia

- Jeśli odpowiedź ma więcej niż jeden nagłówek o tej samej nazwie, a następnie przepisanie wartości jednego z tych nagłówków spowoduje upuszczenie innych nagłówków w odpowiedzi. Zwykle może się to zdarzyć w nagłówku Set-Cookie, ponieważ w odpowiedzi możesz mieć więcej niż jeden nagłówek Set-Cookie. Jednym z takich scenariuszy jest, gdy używasz usługi aplikacji z bramą aplikacji i skonfigurowano koligacji sesji opartej na plikach cookie na bramie aplikacji. W takim przypadku odpowiedź będzie zawierać dwa nagłówki Set-Cookie: jeden używany `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` przez usługę aplikacji, na przykład: `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`i inny dla koligacji bramy aplikacji, na przykład . Przepisanie jednego z nagłówków Set-Cookie w tym scenariuszu może spowodować usunięcie innego nagłówka Set-Cookie z odpowiedzi.

- Przepisywanie nagłówków Połączenia, Uaktualnienia i Hosta nie jest obecnie obsługiwane.

- Nazwy nagłówków mogą zawierać dowolne znaki alfanumeryczne i określone symbole zdefiniowane w [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Obecnie nie obsługujemy znaku specjalnego\_podkreślenia ( ) w nazwach nagłówków.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak przepisać nagłówki HTTP, zobacz:

- [Przepisywanie nagłówków HTTP przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Przepisywanie nagłówków HTTP przy użyciu programu Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
