---
title: Napisz ponownie nagłówki HTTP za pomocą usługi Azure Application Gateway | Microsoft Docs
description: Ten artykuł zawiera omówienie zapisywania nagłówków HTTP w usłudze Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 3e8eb79d519e2f7bfbf006b852f0c5294976b727
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397155"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Zapisz ponownie nagłówki HTTP przy użyciu Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji z żądaniem lub odpowiedzią. Pisząc te nagłówki, można wykonywać ważne zadania, takie jak dodawanie pól nagłówków związanych z zabezpieczeniami, takich jak HSTS/X-XSS-Protection, usuwanie pól nagłówka odpowiedzi, które mogą ujawniać poufne informacje i usuwania informacji o portach z X-Forwarded-For Headers.

Usługa Application Gateway umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP podczas przenoszenia pakietów żądań i odpowiedzi między pulami klientów i zaplecza. Pozwala ona również na dodawanie warunków w celu zapewnienia, że określone nagłówki będą ponownie zapisywane tylko po spełnieniu pewnych warunków.

Application Gateway obsługuje również kilka [zmiennych serwera](#server-variables) , które ułatwiają przechowywanie dodatkowych informacji na temat żądań i odpowiedzi. Ułatwia to tworzenie zaawansowanych reguł ponownego zapisywania.

> [!NOTE]
>
> Obsługa ponownego zapisywania nagłówka HTTP jest dostępna tylko dla [Standard_V2 i WAF_v2 jednostki SKU](application-gateway-autoscaling-zone-redundant.md).

![Ponowne zapisywanie nagłówków](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Obsługiwane nagłówki

Można ponownie napisać wszystkie nagłówki w żądaniach i odpowiedziach, z wyjątkiem nagłówków host, Connection i upgrade. Za pomocą bramy aplikacji można także tworzyć niestandardowe nagłówki i dodawać je do żądań i odpowiedzi przesyłanych przez nią.

## <a name="rewrite-conditions"></a>Warunki ponownego zapisu

Możesz użyć warunku ponownego zapisu, aby oszacować zawartość żądań HTTP (S) i odpowiedzi, i wykonać ponowne zapisywanie nagłówka tylko wtedy, gdy spełnione są co najmniej jeden warunek. Brama aplikacji używa tych typów zmiennych do oszacowania zawartości żądań HTTP (S) i odpowiedzi:

- Nagłówki HTTP w żądaniu.
- Nagłówki HTTP w odpowiedzi.
- Zmienne serwera Application Gateway.

Można użyć warunku, aby oszacować, czy określona zmienna jest obecna, czy określona zmienna jest zgodna z określoną wartością lub czy określona zmienna jest zgodna z określonym wzorcem. Aby skonfigurować dopasowanie wzorca wyrażenia regularnego w warunkach, należy użyć [biblioteki wyrażeń regularnych zgodnej z językiem Perl (PCRE)](https://www.pcre.org/) . Aby dowiedzieć się więcej na temat składni wyrażeń regularnych, zobacz [stronę główną wyrażeń regularnych języka Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Akcje ponownego zapisu

Aby określić nagłówki żądania i odpowiedzi, które mają zostać ponownie zapisane, i nową wartość dla nagłówków, należy użyć akcji ponownego zapisywania. Można utworzyć nowy nagłówek, zmodyfikować wartość istniejącego nagłówka lub usunąć istniejący nagłówek. Wartość nowego nagłówka lub istniejącego nagłówka można ustawić na następujące typy wartości:

- Tekst.
- Nagłówek żądania. Aby określić nagłówek żądania, należy użyć składni {http_req_ *headerName* }.
- Nagłówek odpowiedzi. Aby określić nagłówek odpowiedzi, należy użyć składni {http_resp_ *headerName* }.
- Zmienna serwera. Aby określić zmienną serwera, należy użyć składni {var_ *ServerVariables* }.
- Kombinacja tekstu, nagłówka żądania, nagłówka odpowiedzi i zmiennej serwerowej.

## <a name="server-variables"></a>Zmienne serwera

Application Gateway używa zmiennych serwera do przechowywania użytecznych informacji o serwerze, połączeniu z klientem i bieżącym żądaniu połączenia. Przykłady przechowywanych informacji obejmują adres IP klienta i typ przeglądarki sieci Web. Zmienne serwera zmieniają się dynamicznie, na przykład po załadowaniu nowej strony lub po opublikowaniu formularza. Możesz użyć tych zmiennych do oszacowania warunków ponownego zapisu i ponownego zapisywania nagłówków. Aby można było użyć wartości zmiennych serwera do ponownego zapisania nagłówków, należy określić te zmienne w składni {var_ *ServerVariables* }

Brama aplikacji obsługuje te zmienne serwera:

| Nazwa zmiennej | Opis                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Wartość pola nagłówka żądania X-Forward-for Client ze `client_ip` zmienną (zobacz wyjaśnienie w dalszej części tej tabeli), w formacie IP1, IP2, IP3 i tak dalej. Jeśli pole X-Forward-for nie znajduje się w nagłówku żądania klienta, `add_x_forwarded_for_proxy` zmienna jest równa `$client_ip` zmiennej. Ta zmienna jest szczególnie przydatna w przypadku ponownego zapisania nagłówka X-forwardd-for, który jest ustawiony przez Application Gateway tak, aby nagłówek zawierał tylko adres IP bez informacji o porcie. |
| ciphers_supported          | Lista szyfrów obsługiwanych przez klienta.          |
| ciphers_used               | Ciąg szyfrów użyty dla ustanowionego połączenia TLS. |
| client_ip                  | Adres IP klienta, z którego Brama aplikacji otrzymała żądanie. Jeśli istnieje zwrotny serwer proxy przed bramą aplikacji i klientem inicjującym, *client_ip* zwróci adres IP zwrotnego serwera proxy. |
| client_port                | Port klienta.                                                  |
| client_tcp_rtt             | Informacje o połączeniu TCP klienta. Dostępne w systemach obsługujących opcję TCP_INFO gniazda. |
| client_user                | Gdy używane jest uwierzytelnianie przy użyciu protokołu HTTP, nazwa użytkownika podana na potrzeby uwierzytelniania. |
| host                       | W tej kolejności pierwszeństwa: Nazwa hosta w wierszu żądania, nazwa hosta z pola nagłówka żądania hosta lub nazwa serwera zgodna z żądaniem. Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* wartość hosta będzie *contoso.com* |
| *nazwa* cookie_              | *Nazwa* pliku cookie.                                            |
| http_method                | Metoda używana do żądania adresu URL. Na przykład Pobierz lub Opublikuj. |
| http_status                | Stan sesji. Na przykład 200, 400 lub 403.                       |
| http_version               | Protokół żądania. Zazwyczaj HTTP/1.0, HTTP/1.1 lub HTTP/2.0. |
| query_string               | Lista par zmienna/wartość, które następuje po "?" w żądanym adresie URL. Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING wartość będzie równa *ID = 123&title = Fabrikam* |
| received_bytes             | Długość żądania (w tym wiersza żądania, nagłówka i treści żądania). |
| request_query              | Argumenty w wierszu żądania.                                |
| request_scheme             | Schemat żądania: http lub https.                            |
| request_uri                | Pełny identyfikator URI żądania (z argumentami). Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI wartość będzie */article.aspx? id = 123&title = Fabrikam*   |
| sent_bytes                 | Liczba bajtów wysłanych do klienta.                             |
| server_port                | Port serwera, który zaakceptował żądanie.                 |
| ssl_connection_protocol    | Protokół ustanowionego połączenia TLS.        |
| ssl_enabled                | "Włączone", jeśli połączenie działa w trybie TLS. W przeciwnym razie pusty ciąg. |
| uri_path                   | Identyfikuje określony zasób na hoście, do którego klient sieci Web chce uzyskać dostęp. Jest to część identyfikatora URI żądania bez argumentów. Przykład: w żądaniu *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* uri_path wartość zostanie */article.aspx*  |

## <a name="rewrite-configuration"></a>Zapisz ponownie konfigurację

Aby skonfigurować ponowne zapisywanie nagłówka HTTP, należy wykonać te kroki.

1. Utwórz obiekty wymagane do ponownego zapisania nagłówka HTTP:

   - **Akcja ponownego zapisu** : służy do określania pól żądania i nagłówka żądania, które mają być ponownie zapisane, oraz do nowej wartości nagłówków. Możliwe jest skojarzenie jednego lub więcej warunków ponownego zapisu z akcją ponownego zapisu.

   - **Warunek ponownego zapisu** : opcjonalna konfiguracja. Warunki ponownego zapisu sprawdzają zawartość żądań i odpowiedzi HTTP (S). Akcja ponownego zapisu zostanie wykonana, jeśli żądanie HTTP (S) lub odpowiedź pasuje do warunku ponownego zapisu.

     Jeśli powiążesz więcej niż jeden warunek z akcją, Akcja występuje tylko wtedy, gdy wszystkie warunki są spełnione. Innymi słowy, operacja jest operacją logiczną i.

   - **Reguła ponownego zapisywania** : zawiera kilka kombinacji warunku akcji ponownego zapisu/ponownego zapisywania.

   - **Sekwencja reguł** : pomaga określić kolejność wykonywania reguł ponownego zapisywania. Ta konfiguracja jest przydatna, jeśli masz wiele reguł ponownego zapisywania w zestawie do wielokrotnego zapisu. Reguła ponownego zapisu, która ma niższą wartość sekwencji reguł, jest uruchamiana jako pierwsza. Jeśli ta sama sekwencja reguł zostanie przypisana do dwóch reguł ponownego zapisywania, kolejność wykonywania nie jest deterministyczna.

   - **Zestaw do ponownego zapisu** : zawiera wiele reguł ponownego zapisywania, które zostaną skojarzone z regułą routingu żądania.

2. Dołącz zestaw do ponownego zapisu ( *rewriteRuleSet* ) do reguły routingu. Konfiguracja ponownego zapisywania jest dołączona do odbiornika źródłowego za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu, konfiguracja ponownego zapisywania nagłówka jest skojarzona z odbiornikiem źródłowym i jest ponownym zapisem nagłówka globalnego. W przypadku korzystania z reguły routingu opartej na ścieżce, konfiguracja ponownego zapisywania nagłówka jest definiowana na mapie ścieżki URL. W takim przypadku ma zastosowanie tylko do obszaru określonej ścieżki w lokacji.
   > [!NOTE]
   > Ponowne zapisywanie adresów URL zmiana nagłówków; nie powoduje zmiany adresu URL ścieżki.

Można utworzyć wiele zestawów wielokrotnego zapisu nagłówka HTTP i zastosować każdy ponowny zapis ustawiony dla wielu odbiorników. Można jednak zastosować tylko jeden wielokrotny zapis do określonego odbiornika.

## <a name="common-scenarios"></a>Typowe scenariusze

Poniżej przedstawiono niektóre typowe scenariusze używania ponownego zapisywania nagłówka.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Usuń informacje o porcie z nagłówka X-forwardd-for

Application Gateway wstawia do nagłówka X-forwardd-for wszystkie żądania przed przekazaniem żądań do zaplecza. Ten nagłówek jest rozdzielaną przecinkami listą portów IP. Mogą istnieć scenariusze, w których serwery zaplecza potrzebują tylko nagłówków, aby zawierały adresy IP. Aby usunąć informacje o porcie z nagłówka X-Forwarded-For, można użyć zapisu w nagłówku. Jednym ze sposobów jest ustawienie dla nagłówka zmiennej serwera add_x_forwarded_for_proxy:

![Usuń port](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modyfikowanie adresu URL przekierowania

Gdy aplikacja zaplecza wysyła odpowiedź przekierowania, możesz chcieć przekierować klienta na inny adres URL niż określony przez aplikację zaplecza. Można na przykład wykonać tę czynność, gdy usługa App Service jest hostowana za bramą aplikacji i wymaga, aby klient przetworzył przekierowanie do swojej ścieżki względnej. (Na przykład przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2).

Ponieważ App Service jest usługą wielodostępnym, używa nagłówka hosta w żądaniu, aby skierować żądanie do właściwego punktu końcowego. Usługa App Services ma domyślną nazwę domeny *. azurewebsites.net (Powiedz contoso.azurewebsites.net), która różni się od nazwy domeny bramy aplikacji (Powiedz contoso.com). Ponieważ oryginalne żądanie od klienta ma nazwę hosta usługi Application Gateway (contoso.com), Brama aplikacji zmienia nazwy hosta na contoso.azurewebsites.net. Wprowadza tę zmianę, dzięki czemu usługa App Service może kierować żądanie do właściwego punktu końcowego.

Gdy usługa App Service wyśle odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji swojej odpowiedzi jako tej w żądaniu odbieranym od bramy aplikacji. Klient wyśle żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzenia przez bramę Application Gateway (contoso.com/path2). Pomijanie bramy aplikacji nie jest pożądane.

Ten problem można rozwiązać przez ustawienie nazwy hosta w nagłówku lokalizacji na nazwę domeny bramy aplikacji.

Poniżej przedstawiono procedurę zamieniania nazwy hosta:

1. Utwórz regułę ponownego zapisywania z warunkiem, który oblicza, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Wprowadź wzorzec `(https?):\/\/.*azurewebsites\.net(.*)$` .
1. Wykonaj akcję, aby ponownie zapisać nagłówek lokalizacji tak, aby miał nazwę hosta bramy aplikacji. Zrób to, wprowadzając `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka.

![Modyfikowanie nagłówka lokalizacji](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Zaimplementuj nagłówki HTTP zabezpieczeń, aby zapobiec występowaniu luk w zabezpieczeniach

Można naprawić kilka luk w zabezpieczeniach, implementując niezbędne nagłówki w odpowiedzi aplikacji. Te nagłówki zabezpieczeń obejmują X-XSS-Protection, rygorystyczne Transport-Security i Content-Security-Policy. Możesz użyć Application Gateway, aby ustawić te nagłówki dla wszystkich odpowiedzi.

![Nagłówek zabezpieczeń](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Usuń niechciane nagłówki

Możesz chcieć usunąć nagłówki, które ujawniają poufne informacje z odpowiedzi HTTP. Na przykład możesz chcieć usunąć informacje, takie jak nazwa serwera zaplecza, system operacyjny lub szczegóły biblioteki. Możesz użyć bramy aplikacji, aby usunąć następujące nagłówki:

![Usuwanie nagłówka](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Sprawdź obecność nagłówka

Można oszacować żądanie HTTP lub nagłówek odpowiedzi dla obecności nagłówka lub zmiennej serwerowej. Ta ocena jest przydatna, gdy chcesz wykonać ponowne zapisywanie nagłówka tylko wtedy, gdy jest obecny określony nagłówek.

![Sprawdzanie obecności nagłówka](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Ograniczenia

- Jeśli odpowiedź ma więcej niż jeden nagłówek o tej samej nazwie, wówczas zapisanie wartości jednego z tych nagłówków spowoduje porzucenie pozostałych nagłówków w odpowiedzi. Zwykle może się to zdarzyć z nagłówkiem Set-Cookie, ponieważ w odpowiedzi można mieć więcej niż jeden nagłówek Set-Cookie. Taki scenariusz ma zastosowanie w przypadku korzystania z usługi App Service z bramą aplikacji i skonfigurowania koligacji sesji na podstawie plików cookie na bramie aplikacji. W takim przypadku odpowiedź będzie zawierać dwa nagłówki Set-Cookie: jeden używany przez usługę App Service, na przykład: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` i drugi dla koligacji bramy aplikacji, na przykład `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Ponowne napisanie jednego z nagłówków Set-Cookie w tym scenariuszu może spowodować usunięcie innego nagłówka Set-Cookie z odpowiedzi.

- Ponowne zapisywanie nie jest obsługiwane, gdy Brama aplikacji jest skonfigurowana do przekierowywania żądań lub wyświetlania niestandardowej strony błędu.

- Ponowne zapisywanie nagłówków połączenia, uaktualnienia i hosta nie jest obecnie obsługiwane.

- Nazwy nagłówków mogą zawierać dowolne znaki alfanumeryczne i określone symbole, zgodnie z definicją w [dokumencie RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Obecnie nie obsługujemy znaku podkreślenia ( \_ ) w nazwach nagłówków.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak ponownie napisać nagłówki HTTP, zobacz:

- [Ponowne zapisywanie nagłówków HTTP przy użyciu witryny Azure Portal](./rewrite-http-headers-portal.md)
- [Zapisz ponownie nagłówki HTTP przy użyciu Azure PowerShell](add-http-header-rewrite-rule-powershell.md)