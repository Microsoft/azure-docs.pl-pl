---
title: Ponowne przepisanie nagłówków HTTP i adresów URL przy użyciu Azure Application Gateway | Microsoft Docs
description: Ten artykuł zawiera omówienie ponownego zapisywania nagłówków HTTP i adresów URL w Azure Application Gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: b7cf7c98e71da215eb30dcab556a88d6d2701591
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789451"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Ponowne przepisanie nagłówków HTTP i adresów URL przy użyciu Application Gateway

Application Gateway umożliwia ponowne przepisanie wybranej zawartości żądań i odpowiedzi. Dzięki tej funkcji można tłumaczyć adresy URL, parametry ciągu zapytania, a także modyfikować nagłówki żądań i odpowiedzi. Umożliwia również dodawanie warunków w celu zapewnienia, że adres URL lub określone nagłówki zostaną przepisane tylko wtedy, gdy zostaną spełnione określone warunki. Te warunki są oparte na informacjach dotyczących żądania i odpowiedzi.

>[!NOTE]
>Funkcje ponownego agwania nagłówka HTTP i adresu URL są dostępne tylko dla [Application Gateway wersji 2](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Obsługiwane typy ponownego pisali

### <a name="request-and-response-headers"></a>Nagłówki żądania i odpowiedzi

Nagłówki HTTP umożliwiają klientowi i serwerowi podanie dodatkowych informacji wraz z żądaniem lub odpowiedzią. Ponowne zapisywanie tych nagłówków umożliwia wykonywanie ważnych zadań, takich jak dodawanie pól nagłówka związanych z zabezpieczeniami, takich jak HSTS/ X-XSS-Protection, usuwanie pól nagłówka odpowiedzi, które mogą ujawniać poufne informacje, oraz usuwanie informacji o portach z nagłówków X-Forwarded-For.

Usługa Application Gateway umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP podczas przenoszenia pakietów żądań i odpowiedzi między pulami klientów i zaplecza.

Aby dowiedzieć się, jak ponownie napisać nagłówki żądań i odpowiedzi za pomocą Application Gateway użyciu Azure Portal, [zobacz tutaj](rewrite-url-portal.md).

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Obsługiwane nagłówki**

Możesz ponownie napisać wszystkie nagłówki w żądaniach i odpowiedziach, z wyjątkiem nagłówków Połączenie i Uaktualnij. Brama aplikacji umożliwia również tworzenie nagłówków niestandardowych i dodawanie ich do żądań i odpowiedzi, które są przez nie kierowane.

### <a name="url-path-and-query-string"></a>Ścieżka adresu URL i ciąg zapytania

Możliwość ponownego agowania adresów URL w Application Gateway umożliwia:

* Ponownie napisaj nazwę hosta, ścieżkę i ciąg zapytania adresu URL żądania 

* Wybierz ponowne przepisanie adresu URL wszystkich żądań w odbiorniku lub tylko tych żądań, które pasują do co najmniej jednego z ustawionych warunków. Te warunki są oparte na właściwościach żądania i odpowiedzi (zmienne żądania, nagłówka, nagłówka odpowiedzi i serwera).

* Wybierz trasę żądania (wybierz pulę zaplecza) na podstawie oryginalnego adresu URL lub ponownie napisanego adresu URL

Aby dowiedzieć się, jak ponownie napisać adres URL za pomocą Application Gateway użyciu Azure Portal, [zobacz tutaj](rewrite-url-portal.md).

![Diagram opisujący proces ponownego tworzenia adresu URL za pomocą Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Ponowne przepisanie akcji

Akcje ponownego pisali, aby określić adres URL, nagłówki żądań lub nagłówki odpowiedzi, które chcesz ponownie napisać, oraz nową wartość, do której zamierzasz je ponownie napisać. Wartość adresu URL albo nowego lub istniejącego nagłówka można ustawić na następujące typy wartości:

* Tekst
* Nagłówek żądania. Aby określić nagłówek żądania, należy użyć składni {http_req_ *headerName*}
* Nagłówek odpowiedzi. Aby określić nagłówek odpowiedzi, należy użyć składni {http_resp_ *headerName*}
* Zmienna serwera. Aby określić zmienną serwera, należy użyć składni {var_ *serverVariable*}. Zobacz listę obsługiwanych zmiennych serwera
* Kombinacja tekstu, nagłówka żądania, nagłówka odpowiedzi i zmiennej serwera. 

## <a name="rewrite-conditions"></a>Ponowne przepisanie warunków

Można użyć warunków ponownego agwania, opcjonalnej konfiguracji, aby ocenić zawartość żądań i odpowiedzi HTTP(S) i wykonać ponowne przepisanie tylko wtedy, gdy zostanie spełniony co najmniej jeden z warunków. Brama aplikacji używa tych typów zmiennych do oceny zawartości żądań i odpowiedzi:

* Nagłówki HTTP w żądaniu
* Nagłówki HTTP w odpowiedzi
* Application Gateway zmiennych serwera

Warunek umożliwia ocenę, czy określona zmienna jest obecna, czy określona zmienna pasuje do określonej wartości, czy też określona zmienna pasuje do określonego wzorca. 


### <a name="pattern-matching"></a>Dopasowanie wzorca 

Application Gateway używa wyrażeń regularnych do dopasowywania do wzorca w warunku. Biblioteki [PCRE (Perl Compatible Regular Expressions)](https://www.pcre.org/) można użyć do skonfigurowania dopasowywania wzorca wyrażeń regularnych w warunkach. Aby dowiedzieć się więcej na temat składni wyrażeń regularnych, zobacz stronę główną [wyrażeń regularnych perl](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Przechwytywanie

Aby przechwycić podciąg do późniejszego użycia, umieść nawiasy wokół podwładnia, który pasuje do niego, w definicji wyrażenia regularnego warunku. Pierwsza para nawiasów przechowuje swój podciąg w 1, druga para w 2 i tak dalej. Możesz użyć tylu nawiasów, ile chcesz; Perl po prostu zachowuje definiowanie większej liczby zmiennych do reprezentowania tych przechwyconych ciągów. Kilka przykładów z [ref:](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm) 

*  /(\d)(\d)/ # Dopasuj dwie cyfry, przechwytując je do grup 1 i 2 

* /(\d+)/ # Dopasuj co najmniej jedną cyfrę, przechwytując je wszystkie do grupy 1 

* /(\d)+/ # Dopasowanie cyfry co najmniej raz, przechwytując ostatnią w grupie 1

Po przechwyceniom można odwoływać się do nich w zestawie akcji przy użyciu następującego formatu:

* W przypadku przechwytywania nagłówka żądania należy użyć elementu {http_req_headerName_groupNumber}. Na przykład {http_req_User-Agent_1} lub {http_req_User-Agent_2}
* W przypadku przechwytywania nagłówka odpowiedzi należy użyć elementu {http_resp_headerName_groupNumber}. Na przykład {http_resp_Location_1} lub {http_resp_Location_2}
* W przypadku zmiennej serwera należy użyć wartości {var_serverVariableName_groupNumber}. Na przykład {var_uri_path_1} lub {var_uri_path_2}

Jeśli chcesz użyć całej wartości, nie należy wymieniać liczby. Po prostu użyj formatu {http_req_headerName}, itp. bez groupNumber.

## <a name="server-variables"></a>Zmienne serwera

Application Gateway zmienne serwera do przechowywania przydatnych informacji o serwerze, połączeniu z klientem i bieżącym żądaniu połączenia. Przykłady przechowywanych informacji obejmują adres IP klienta i typ przeglądarki internetowej. Zmienne serwera zmieniają się dynamicznie, na przykład podczas ładowania nowej strony lub gdy formularz jest publikowany. Możesz użyć tych zmiennych, aby ocenić warunki ponownego pisali i ponownie pisać nagłówki. Aby można było ponownie napisać nagłówki przy użyciu wartości zmiennych serwera, należy określić te zmienne w składni {var_ *serverVariableName*}

Brama aplikacji obsługuje następujące zmienne serwera:

|   Nazwa zmiennej    |                   Opis                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Pole nagłówka żądania klienta X-Forwarded-For ze zmienną (zobacz wyjaśnienie w dalszej części tej tabeli) dołączona do niego w formacie `client_ip` IP1, IP2, IP3 i tak dalej. Jeśli pole X-Forwarded-For nie znajduje się w nagłówku żądania klienta, zmienna `add_x_forwarded_for_proxy` jest równa `$client_ip` zmiennej .   Ta zmienna jest szczególnie przydatna, gdy chcesz ponownie napisać nagłówek X-Forwarded-For ustawiony przez Application Gateway tak, aby nagłówek zawierał tylko adres IP bez informacji o porcie. |
| ciphers_supported         | Lista szyfrów obsługiwanych przez klienta.               |
| ciphers_used              | Ciąg szyfrów używany do nawiązanego połączenia TLS. |
| client_ip                 | Adres IP klienta, z którego brama aplikacji odebrała żądanie. Jeśli przed bramą aplikacji i klientem inicjatora istnieje zwrotny serwer proxy, program zwróci `client_ip` adres IP zwrotnego serwera proxy. |
| client_port               | Port klienta.                                             |
| client_tcp_rtt            | Informacje o połączeniu TCP klienta. Dostępne w systemach, które obsługują TCP_INFO gniazda. |
| client_user               | W przypadku uwierzytelniania HTTP jest używana nazwa użytkownika podana do uwierzytelniania. |
| host                      | W takiej kolejności pierwszeństwa: nazwa hosta z wiersza żądania, nazwa hosta z pola Nagłówek żądania hosta lub nazwa serwera pasującą do żądania. Przykład: w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wartość hosta to `contoso.com` |
| cookie_ nazwa *użytkownika*             | Nazwa *pliku* cookie.                                           |
| http_method               | Metoda używana do żądania adresu URL. Na przykład GET lub POST. |
| http_status               | Stan sesji. Na przykład 200, 400 lub 403.           |
| http_version              | Protokół żądania. Zazwyczaj HTTP/1.0, HTTP/1.1 lub HTTP/2.0. |
| Query_string              | Lista par zmienna/wartość, która następuje po wartości "?" w żądanym adresie URL. Przykład: w `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` żądaniu wartość query_string będzie `id=123&title=fabrikam` |
| received_bytes            | Długość żądania (w tym wierszu żądania, nagłówku i treści żądania). |
| request_query             | Argumenty w wierszu żądania.                           |
| request_scheme            | Schemat żądania: http lub https.                           |
| request_uri               | Pełny oryginalny adres URI żądania (z argumentami). Przykład: w `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` żądaniu request_uri będzie `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Liczba bajtów wysłanych do klienta.                        |
| server_port               | Port serwera, który zaakceptował żądanie.              |
| ssl_connection_protocol   | Protokół nawiązanego połączenia TLS.               |
| ssl_enabled               | "Wł.", jeśli połączenie działa w trybie TLS. W przeciwnym razie pusty ciąg. |
| uri_path                  | Identyfikuje określony zasób na hoście, do których klient internetowy chce uzyskać dostęp. Jest to część URI żądania bez argumentów. Przykład: w `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` żądaniu wartość uri_path będzie `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Zmienne serwera wzajemnego uwierzytelniania (wersja zapoznawcza)

Application Gateway obsługuje następujące zmienne serwera dla scenariuszy wzajemnego uwierzytelniania. Użyj tych zmiennych serwera w taki sam sposób jak powyżej z innymi zmiennymi serwera. 

|   Nazwa zmiennej    |                   Opis                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Certyfikat klienta w formacie PEM dla ustanowionego połączenia SSL. |
| client_certificate_end_date| Data zakończenia certyfikatu klienta. |
| client_certificate_fingerprint| Odcisk palca SHA1 certyfikatu klienta dla nawiązanego połączenia SSL. |
| client_certificate_issuer | Ciąg "nazwa WYRÓŻNIAJĄCA wystawcy" certyfikatu klienta dla nawiązanego połączenia SSL. |
| client_certificate_serial | Numer seryjny certyfikatu klienta dla nawiązanego połączenia SSL.  |
| client_certificate_start_date| Data rozpoczęcia certyfikatu klienta. |
| client_certificate_subject| Ciąg "podmiotu dn" certyfikatu klienta dla nawiązanego połączenia SSL. |
| client_certificate_verification| Wynik weryfikacji certyfikatu klienta: *POWODZENIE,* *<reason> NIEPOWODZENIE:* lub *BRAK,* jeśli certyfikat nie był obecny. | 

## <a name="rewrite-configuration"></a>Ponowne przepisanie konfiguracji

Aby skonfigurować regułę ponownego tworzenia, należy utworzyć zestaw reguł ponownego tworzenia i dodać do niego konfigurację reguły ponownego tworzyć.

Zestaw reguł ponownego pisali zawiera:

* **Żądanie skojarzenia reguły routingu:** Konfiguracja ponownego skojarzenia jest skojarzona z odbiornikiem źródłowym za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu konfiguracja ponownego skojarzenia jest skojarzona z odbiornikiem źródłowym i jest globalnym rewrite nagłówka. W przypadku używania reguły routingu opartej na ścieżkach konfiguracja ponownego agowania jest definiowana na mapie ścieżek URL. W takim przypadku ma zastosowanie tylko do obszaru określonej ścieżki lokacji. Można utworzyć wiele zestawów ponownego tworzenia i zastosować każdy zestaw rewrite do wielu odbiorników. Można jednak zastosować tylko jeden zestaw rewrite do określonego odbiornika.

* **Ponownie napisać warunek:** jest to konfiguracja opcjonalna. Rewrite conditions evaluate the content of the HTTP(S) requests and responses. Akcja ponownego na napisać wystąpi, jeśli żądanie LUB odpowiedź HTTP(S) pasuje do warunku ponownego napisali. Jeśli skojarzysz z akcją więcej niż jeden warunek, ta akcja będzie odbywać się tylko wtedy, gdy wszystkie warunki zostaną spełnione. Innymi słowy, operacja jest operacją logiczną AND.

* **Typ ponownego pisania:** Dostępne są 3 typy ponownego pisania:
   * Ponowne zapisywanie nagłówków żądań 
   * Ponowne zapisywanie nagłówków odpowiedzi
   * Ponowne zapisywanie składników adresu URL
      * **Ścieżka URL:** wartość, do której ma zostać przepisana ścieżka. 
      * **Ciąg zapytania adresu URL:** wartość, do której ma zostać przepisany ciąg zapytania. 
      * **Ponownie oceń mapę ścieżek:** służy do określania, czy mapa ścieżek URL ma być ponownie oceniana. Jeśli pole wyboru nie jest zaznaczone, oryginalna ścieżka adresu URL będzie używana w celu dopasowania do wzorca ścieżki na mapie ścieżek URL. W przypadku ustawienia wartości true mapa ścieżek URL zostanie ponownie oceniona w celu sprawdzenia dopasowania ze ścieżką przepisaną. Włączenie tego przełącznika ułatwia kierowanie żądania do innej puli zaplecza po ponownej zmianie.

## <a name="rewrite-configuration-common-pitfalls"></a>Typowe pułapki podczas ponownego guguj konfiguracji

* Włączenie opcji "Re-evaluate path map" (Ponownie oceń mapę ścieżki) jest niedozwolone w przypadku podstawowych reguł routingu żądań. Ma to na celu zapobieganie nieskończonej pętli oceny dla podstawowej reguły routingu.

* Aby zapobiec nieskończonej pętli oceny dla reguły routingu opartego na ścieżkach, musi być co najmniej 1 reguła ponownego agwania warunkowego lub 1 reguła ponownego agwania, która nie ma włączonej opcji "Re-evaluate path map" (Ponownie oceń mapę ścieżek) dla reguł routingu opartych na ścieżkach.

* Żądania przychodzące zostaną zakończone z kodem błędu 500 w przypadku, gdy pętla jest tworzona dynamicznie na podstawie danych wejściowych klienta. Proces Application Gateway będzie nadal obsługiwać inne żądania bez pogorszenia wydajności w takim scenariuszu.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Używanie ponownego pisać adresów URL lub ponownego pisać nagłówka hosta z Web Application Firewall (WAF_v2 SKU)

Podczas konfigurowania ponownego napiszenia adresu URL lub ponownego nacjonowania nagłówka hosta ocena serwera WAF będzie odbywać się po modyfikacji nagłówka żądania lub parametrów adresu URL (po ponownej modyfikacji). Jeśli usuniesz konfigurację ponownego zakodowania adresu URL lub nagłówka hosta na komputerze Application Gateway, ocena serwera WAF zostanie wykonana przed ponownym przepisaniem nagłówka (ponownie napisać). Ta kolejność zapewnia, że reguły WAF są stosowane do końcowego żądania, które zostałoby odebrane przez pulę zaplecza.

Załóżmy na przykład, że masz następującą regułę ponownego agwania nagłówka dla nagłówka — jeśli wartość nagłówka jest równa , a następnie ponownie napisać `"Accept" : "text/html"` `"Accept"` wartość na `"text/html"` `"image/png"` .

W tym przypadku, po skonfigurowaniu tylko ponownego agowania nagłówka, ocena aplikacji sieci WAF zostanie wykonana na stronie `"Accept" : "text/html"` . Jednak w przypadku skonfigurowania ponownego agowania adresu URL lub ponownego nacjonowania nagłówka hosta ocena serwera WAF zostanie wykonana na stronie `"Accept" : "image/png"` .

>[!NOTE]
> Oczekuje się, że operacje ponownego zapisu adresów URL będą powodować niewielkie zwiększenie wykorzystania procesora przez twoją sieć WAF Application Gateway. Zaleca się monitorowanie metryki wykorzystania procesora [CPU](high-traffic-support.md) przez krótki czas po włączeniu reguł ponownego programowania adresów URL w aplikacji internetowej Application Gateway.

### <a name="common-scenarios-for-header-rewrite"></a>Typowe scenariusze ponownego awarii nagłówka

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Usuwanie informacji o porcie z nagłówka X-Forwarded-For

Application Gateway wstawia nagłówek X-Forwarded-For do wszystkich żądań przed przekazywaniem żądań do zaplecza. Ten nagłówek to rozdzielana przecinkami lista portów IP. Mogą wystąpić scenariusze, w których serwery back-end potrzebują tylko nagłówków zawierających adresy IP. Aby usunąć informacje o porcie z nagłówka X-Forwarded-For, można użyć ponownego napisali nagłówka. Jednym ze sposobów jest ustawienie nagłówka na zmienną add_x_forwarded_for_proxy serwera. Alternatywnie można również użyć zmiennej client_ip:

![Usuwanie portu](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Modyfikowanie adresu URL przekierowania

Gdy aplikacja back-end wysyła odpowiedź przekierowania, możesz chcieć przekierować klienta do innego adresu URL niż określony przez aplikację tego typu. Można to zrobić na przykład wtedy, gdy usługa aplikacji jest hostowana za bramą aplikacji i wymaga od klienta przekierowania do jej ścieżki względnej. (Na przykład przekierowanie z witryny contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2).

Ponieważ App Service jest usługą wielowątkową, używa nagłówka hosta w żądaniu do przekierowania żądania do poprawnego punktu końcowego. Usługi App Services mają domyślną nazwę domeny azurewebsites.net (np. contoso.azurewebsites.net), która jest inna niż nazwa domeny bramy aplikacji \* (np. contoso.com). Ponieważ oryginalne żądanie klienta ma nazwę domeny (contoso.com) bramy aplikacji jako nazwę hosta, brama aplikacji zmienia nazwę hosta na contoso.azurewebsites.net. Wprowadza tę zmianę, dzięki czemu usługa App Service może przekierowyć żądanie do poprawnego punktu końcowego.

Gdy usługa App Service wysyła odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji odpowiedzi, co w żądaniu odbieranej z bramy aplikacji. Dlatego klient będzie wysyłać żądanie bezpośrednio do usługi , zamiast `contoso.azurewebsites.net/path2` przechodzić przez bramę aplikacji ( `contoso.com/path2` ). Pomijanie bramy aplikacji nie jest pożądane.

Ten problem można rozwiązać, ustawiając nazwę hosta w nagłówku lokalizacji na nazwę domeny bramy aplikacji.

Poniżej podano procedurę zastępowania nazwy hosta:

1. Utwórz ponownie regułę z warunkiem, który ocenia, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Wprowadź wzorzec `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Wykonaj akcję, aby ponownie napisać nagłówek lokalizacji tak, aby zawierał nazwę hosta bramy aplikacji. W tym celu należy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` wprowadzić wartość nagłówka . Alternatywnie można również użyć zmiennej serwera, aby ustawić nazwę hosta tak, `host` aby odpowiadała oryginalnemu żądaniu.

![Modyfikowanie nagłówka lokalizacji](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementowanie nagłówków HTTP zabezpieczeń w celu zapobiegania lukom w zabezpieczeniach

Możesz naprawić kilka luk w zabezpieczeniach, implementując niezbędne nagłówki w odpowiedzi aplikacji. Nagłówki zabezpieczeń obejmują X-XSS-Protection, Strict-Transport-Security i Content-Security-Policy. Możesz użyć Application Gateway, aby ustawić te nagłówki dla wszystkich odpowiedzi.

![Nagłówek zabezpieczeń](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Usuwanie niechcianych nagłówków

Możesz usunąć nagłówki, które ujawniają poufne informacje z odpowiedzi HTTP. Możesz na przykład usunąć informacje, takie jak nazwa serwera, system operacyjny lub szczegóły biblioteki. Aby usunąć te nagłówki, możesz użyć bramy aplikacji:

![Usuwanie nagłówka](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Sprawdzanie obecności nagłówka

Można ocenić nagłówek żądania LUB odpowiedzi HTTP pod kątem obecności nagłówka lub zmiennej serwera. Ta ocena jest przydatna, gdy chcesz ponownie napisać nagłówek tylko wtedy, gdy istnieje określony nagłówek.

![Sprawdzanie obecności nagłówka](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Typowe scenariusze ponownego testowania adresów URL

#### <a name="parameter-based-path-selection"></a>Wybór ścieżki opartej na parametrach

Aby zrealizować scenariusze, w których chcesz wybrać pulę zaplecza na podstawie wartości nagłówka, części adresu URL lub ciągu zapytania w żądaniu, możesz użyć kombinacji możliwości ponownego pisać adresów URL i routingu opartego na ścieżkach. Jeśli na przykład masz witrynę internetową zakupów, a kategoria produktu jest przekazywana jako ciąg zapytania w adresie URL i chcesz przekierować żądanie do zaplecza na podstawie ciągu zapytania, to:

**Krok 1.**  Utwórz mapę ścieżek, jak pokazano na poniższej ilustracji

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Scenariusz ponownego 1–1 w adresie URL.":::

**Krok 2 (a):** Utwórz zestaw ponownego agwania, który ma 3 reguły ponownego tworzenia: 

* Pierwsza reguła ma warunek, który sprawdza zmienną *query_string* *category=shoes* i ma akcję, która ponownie zapisuje ścieżkę adresu URL do */listing1* i ma włączoną ponownie ocenę **mapy** ścieżek

* Druga reguła ma warunek, który sprawdza zmienną *query_string* *category=bags* i ma akcję, która ponownie zapisuje ścieżkę adresu URL do */listing2*  i ma włączoną ponownie ocenę **mapy** ścieżek

* Trzecia reguła ma warunek, który sprawdza zmienną *query_string* *category=accessories* i ma akcję, która ponownie zapisuje ścieżkę adresu URL do */listing3* i ma włączoną ponownie ocenę **mapy** ścieżek

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Scenariusz ponownego 1–2 w adresie URL.":::

 

**Krok 2 (b):** Skojarz ten zestaw ponownego agwania z domyślną ścieżką powyższej reguły opartej na ścieżkach

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Scenariusz ponownego 1–3 w adresie URL.":::

Teraz, jeśli użytkownik zażąda contoso.com/listing?category=any *,* zostanie ona dopasowana do ścieżki domyślnej, ponieważ żaden z wzorców ścieżki na mapie ścieżek (/listing1, /listing2, /listing3) nie będzie zgodne. Ponieważ powyższy zestaw ponownego skojarzeń został skojarzony z tą ścieżką, ten zestaw ponownego agwania zostanie oceniony. Ponieważ ciąg zapytania nie będzie odpowiadać warunekowi w żadnej z 3 reguł ponownego pisali w tym zestawie ponownego agwania, akcja ponownego agwania nie będzie miała miejsca i w związku z tym żądanie będzie kierowane bez zmian do zaplecza skojarzonego ze ścieżką domyślną (czyli *GenericList*).

Jeśli użytkownik zażąda *contoso.com/listing?category=shoes*, ponownie zostanie dopasowana ścieżka domyślna. Jednak w tym przypadku warunek w pierwszej regułie będzie odpowiadać i w związku z tym zostanie wykonana akcja skojarzona z warunkiem, która ponownie zapisuje ścieżkę adresu URL do */listing1*  i ponownie ocenia mapę ścieżki. Po ponownej ocenie mapy ścieżki żądanie będzie teraz zgodne ze ścieżką skojarzoną ze wzorcem */listing1,* a żądanie zostanie kierowane do zaplecza skojarzonego z tym wzorcem, czyli ShoesListBackendPool.

>[!NOTE]
>Ten scenariusz można rozszerzyć na dowolną wartość nagłówka lub pliku cookie, ścieżkę adresu URL, ciąg zapytania lub zmienne serwera na podstawie zdefiniowanych warunków i zasadniczo umożliwia trasę żądań na podstawie tych warunków.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Ponowne napisanie parametrów ciągu zapytania na podstawie adresu URL

Rozważmy scenariusz witryny internetowej z zakupami, w której link widoczny dla użytkownika powinien być prosty i czytelny, ale serwer zaplecza potrzebuje parametrów ciągu zapytania, aby pokazać właściwą zawartość.

W takim przypadku Application Gateway przechwytywać parametry z adresu URL i dodawać pary klucz-wartość ciągu zapytania z tych z adresu URL. Załóżmy na przykład, że użytkownik chce ponownie napisać do adresu , można to osiągnąć za pomocą następującej konfiguracji ponownego `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` napiszenia adresu URL.

**Warunek** — jeśli zmienna serwera `uri_path` jest równa wzorcowi `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Scenariusz ponownego 2-1 dla adresów URL.":::

**Akcja** — ustaw ścieżkę adresu URL na i `buy.aspx` ciąg zapytania na `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Scenariusz ponownego 2–2 w adresie URL.":::

Aby uzyskać przewodnik krok po kroku, aby osiągnąć opisany powyżej scenariusz, zobacz Ponowne opisanie adresu URL za pomocą Application Gateway [przy użyciu Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>Ponowne przepisanie adresu URL a przekierowywanie adresu URL

W przypadku ponownego napisali adres URL Application Gateway adres URL przed wysłaniem żądania do zaplecza. Nie zmieni to tego, co użytkownicy zobaczą w przeglądarce, ponieważ zmiany są ukryte przed użytkownikiem.

W przypadku przekierowania adresu URL program Application Gateway do klienta z nowym adresem URL. To z kolei wymaga od klienta ponownego wysłania żądania na nowy adres URL podany w przekierowaniu. Adres URL, który widzi użytkownik w przeglądarce, zostanie zaktualizowany do nowego adresu URL.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Ponowne przepisanie a przekierowywanie.":::

## <a name="limitations"></a>Ograniczenia

- Jeśli odpowiedź ma więcej niż jeden nagłówek o tej samej nazwie, ponowne napisanie wartości jednego z tych nagłówków spowoduje usunięcie innych nagłówków w odpowiedzi. Zwykle może się to zdarzyć Set-Cookie nagłówka, ponieważ w odpowiedzi może Set-Cookie więcej niż jeden nagłówek. Jednym z takich scenariuszy jest użycie usługi App Service z bramą aplikacji i skonfigurowanie koligacji sesji na podstawie plików cookie w bramie aplikacji. W takim przypadku odpowiedź będzie zawierać dwa nagłówki Set-Cookie: jeden używany przez usługę App Service, na przykład: i drugi dla koligacji bramy `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` aplikacji, na przykład `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Ponowne napisanie jednego z nagłówków Set-Cookie w tym scenariuszu może spowodować usunięcie innego nagłówka Set-Cookie z odpowiedzi.
- Ponowne zapisuje nie są obsługiwane, gdy brama aplikacji jest skonfigurowana do przekierowywania żądań lub do pokazywania niestandardowej strony błędu.
- Nazwy nagłówków mogą zawierać dowolne znaki alfanumeryczne i określone symbole zgodnie z definicją [w dokumencie RFC 7230.](https://tools.ietf.org/html/rfc7230#page-27) Obecnie nie obsługujemy znaku podkreślenia ( \_ ) w nazwach nagłówków.
- Nie można zapisać ponownie nagłówków połączeń i uaktualnień

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak ponownie pisać nagłówki HTTP za pomocą Application Gateway użyciu Azure Portal](rewrite-http-headers-portal.md)
- [Dowiedz się, jak ponownie napisać adres URL za pomocą Application Gateway użyciu Azure Portal](rewrite-url-portal.md)
