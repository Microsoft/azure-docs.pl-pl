---
title: Zapisz ponownie nagłówki HTTP i adres URL przy użyciu usługi Azure Application Gateway | Microsoft Docs
description: Ten artykuł zawiera omówienie zapisywania nagłówków HTTP i adresów URL na platformie Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: surmb
ms.openlocfilehash: 81eaf95a4918590c6eaa2c17a45e6925a1a67992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726516"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Zapisz ponownie nagłówki HTTP i adres URL przy użyciu Application Gateway

 Application Gateway pozwala ponownie napisać wybraną zawartość żądań i odpowiedzi. Ta funkcja umożliwia translację adresów URL, parametrów ciągu zapytania oraz modyfikowanie nagłówków żądań i odpowiedzi. Pozwala również dodać warunki, aby upewnić się, że adres URL lub określone nagłówki są zapisywane tylko wtedy, gdy zostaną spełnione określone warunki. Te warunki są oparte na informacjach dotyczących żądania i odpowiedzi.

>[!NOTE]
>Funkcje ponownego zapisywania nagłówków i adresów HTTP są dostępne tylko dla [jednostki SKU Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Obsługiwane typy ponownego zapisywania

### <a name="request-and-response-headers"></a>Nagłówki żądania i odpowiedzi

Nagłówki HTTP umożliwiają klientowi i serwerowi przekazywanie dodatkowych informacji z żądaniem lub odpowiedzią. Pisząc te nagłówki, można wykonywać ważne zadania, takie jak dodawanie pól nagłówków związanych z zabezpieczeniami, takich jak HSTS/X-XSS-Protection, usuwanie pól nagłówka odpowiedzi, które mogą ujawniać poufne informacje i usuwania informacji o portach z X-Forwarded-For Headers.

Usługa Application Gateway umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP podczas przenoszenia pakietów żądań i odpowiedzi między pulami klientów i zaplecza.

Aby dowiedzieć się, jak ponownie napisać nagłówki żądań i odpowiedzi z Application Gateway przy użyciu Azure Portal, zobacz [tutaj](rewrite-url-portal.md).

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Obsługiwane nagłówki**

Możesz ponownie napisać wszystkie nagłówki w żądaniach i odpowiedziach, z wyjątkiem połączeń, a następnie Uaktualnij nagłówki. Za pomocą bramy aplikacji można także tworzyć niestandardowe nagłówki i dodawać je do żądań i odpowiedzi przesyłanych przez nią.

### <a name="url-path-and-query-string-preview"></a>Ścieżka adresu URL i ciąg zapytania (wersja zapoznawcza)

Dzięki możliwości ponownego zapisywania adresów URL w Application Gateway można:

* Ponownie Napisz nazwę hosta, ścieżkę i ciąg zapytania dla adresu URL żądania 

* Wybierz, aby ponownie napisać adres URL wszystkich żądań na odbiorniku lub tylko te żądania, które pasują do jednego lub kilku ustawionych warunków. Warunki te są oparte na właściwościach żądania i odpowiedzi (żądanie, nagłówek, nagłówek odpowiedzi i zmienne serwera).

* Wybierz kierowanie żądania (wybierz pulę zaplecza) w oparciu o oryginalny adres URL lub ponownie zapisany adres URL

Aby dowiedzieć się, jak ponownie napisać adres URL z Application Gateway przy użyciu Azure Portal, zobacz [tutaj](rewrite-url-portal.md).

![Diagram opisujący proces ponownego zapisywania adresu URL z Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

>[!NOTE]
> Funkcja ponownego zapisywania adresu URL jest w wersji zapoznawczej i jest dostępna tylko dla Standard_v2 i WAF_v2 jednostki SKU Application Gateway. Nie jest to zalecane do użycia w środowisku produkcyjnym. Aby dowiedzieć się więcej na temat wersji zapoznawczych, zobacz [warunki użytkowania tutaj](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rewrite-actions"></a>Akcje ponownego zapisu

Aby określić adres URL, nagłówki żądań lub nagłówki odpowiedzi, które mają zostać ponownie zapisane, i nową wartość, do której mają być zapisywane, należy użyć akcji ponownego zapisu. Wartość adresu URL lub nowego lub istniejącego nagłówka można ustawić na następujące typy wartości:

* Tekst
* Nagłówek żądania. Aby określić nagłówek żądania, należy użyć składni {http_req_ *headerName*}
* Nagłówek odpowiedzi. Aby określić nagłówek odpowiedzi, należy użyć składni {http_resp_ *headerName*}
* Zmienna serwera. Aby określić zmienną serwera, należy użyć składni {var_ *ServerVariables*}. Zobacz listę obsługiwanych zmiennych serwera
* Kombinacja tekstu, nagłówka żądania, nagłówka odpowiedzi i zmiennej serwerowej. 

## <a name="rewrite-conditions"></a>Warunki ponownego zapisu

Możesz użyć warunku ponownego zapisu, konfiguracji opcjonalnej, do oszacowania zawartości żądań i odpowiedzi HTTP (S) i wykonać ponowne zapisywanie tylko wtedy, gdy spełnione są co najmniej jeden warunek. Brama aplikacji używa tych typów zmiennych do oszacowania zawartości żądań i odpowiedzi:

* Nagłówki HTTP w żądaniu
* Nagłówki HTTP w odpowiedzi
* Zmienne serwera Application Gateway

Można użyć warunku, aby oszacować, czy określona zmienna jest obecna, czy określona zmienna jest zgodna z określoną wartością lub czy określona zmienna jest zgodna z określonym wzorcem. 


### <a name="pattern-matching"></a>Dopasowanie wzorca 

Application Gateway używa wyrażeń regularnych w celu dopasowania do wzorca w warunku. Można użyć [biblioteki wyrażeń regularnych zgodnej z językiem Perl (PCRE)](https://www.pcre.org/) , aby skonfigurować dopasowanie wzorca wyrażenia regularnego w warunkach. Aby dowiedzieć się więcej na temat składni wyrażeń regularnych, zobacz [stronę główną wyrażeń regularnych języka Perl](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Przechwytywania

Aby przechwycić podciąg do późniejszego użycia, Umieść nawiasy wokół wzorca, który pasuje do niego w definicji wyrażenia regularnego warunku. Pierwsza para nawiasów przechowuje jego podciąg w 1, drugą parę w 2 itd. Możesz użyć dowolnej liczby nawiasów. Język Perl po prostu definiuje bardziej numerowane zmienne, aby reprezentować te przechwycone ciągi. Przykłady z [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d) (\d)/# dopasowuje dwie cyfry, przechwytując je do grup 1 i 2 

* /(\d +)/# dopasowuje co najmniej jedną cyfrę, przechwytując je wszystkie do grupy 1 

* /(\d) +/# dopasowuje co najmniej jedną godzinę, przechwytując ostatnią do grupy 1

Po przechwyceniu można odwołać się do nich w ramach akcji zestawu przy użyciu następującego formatu:

* W przypadku przechwytywania nagłówka żądania należy użyć elementu {http_req_headerName_groupNumber}. Na przykład {http_req_User-Agent_1} lub {http_req_User-Agent_2}
* W przypadku przechwytywania nagłówka odpowiedzi należy użyć {http_resp_headerName_groupNumber}. Na przykład {http_resp_Location_1} lub {http_resp_Location_2}
* W przypadku zmiennej serwera należy użyć {var_serverVariableName_groupNumber}. Na przykład {var_uri_path_1} lub {var_uri_path_2}

Jeśli chcesz użyć całej wartości, nie należy wspominać o liczbie. Wystarczy użyć formatu {http_req_headerName} itp. bez groupNumber.

## <a name="server-variables"></a>Zmienne serwera

Application Gateway używa zmiennych serwera do przechowywania użytecznych informacji o serwerze, połączeniu z klientem i bieżącym żądaniu połączenia. Przykłady przechowywanych informacji obejmują adres IP klienta i typ przeglądarki sieci Web. Zmienne serwera zmieniają się dynamicznie, na przykład po załadowaniu nowej strony lub po opublikowaniu formularza. Możesz użyć tych zmiennych do oszacowania warunków ponownego zapisu i ponownego zapisywania nagłówków. Aby można było użyć wartości zmiennych serwera do ponownego zapisania nagłówków, należy określić te zmienne w składni {var_ *servervariablesname*}

Brama aplikacji obsługuje następujące zmienne serwera:

|   Nazwa zmiennej    |                   Opis                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Wartość pola nagłówka żądania X-Forward-for Client ze `client_ip` zmienną (zobacz wyjaśnienie w dalszej części tej tabeli), w formacie IP1, IP2, IP3 i tak dalej. Jeśli pole X-Forward-for nie znajduje się w nagłówku żądania klienta, `add_x_forwarded_for_proxy` zmienna jest równa `$client_ip` zmiennej.   Ta zmienna jest szczególnie przydatna w przypadku ponownego zapisania nagłówka X-forwardd-for, który jest ustawiony przez Application Gateway tak, aby nagłówek zawierał tylko adres IP bez informacji o porcie. |
| ciphers_supported         | Lista szyfrów obsługiwanych przez klienta.               |
| ciphers_used              | Ciąg szyfrów użyty dla ustanowionego połączenia TLS. |
| client_ip                 | Adres IP klienta, z którego Brama aplikacji otrzymała żądanie. Jeśli istnieje zwrotny serwer proxy przed bramą aplikacji i klientem inicjującym, `client_ip` zwróci adres IP zwrotnego serwera proxy. |
| client_port               | Port klienta.                                             |
| client_tcp_rtt            | Informacje o połączeniu TCP klienta. Dostępne w systemach obsługujących opcję TCP_INFO gniazda. |
| client_user               | Gdy używane jest uwierzytelnianie przy użyciu protokołu HTTP, nazwa użytkownika podana na potrzeby uwierzytelniania. |
| host                      | W tej kolejności pierwszeństwa: Nazwa hosta w wierszu żądania, nazwa hosta z pola nagłówka żądania hosta lub nazwa serwera zgodna z żądaniem. Przykład: w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wartość hosta będzie równa `contoso.com` |
| *nazwa* cookie_             | *Nazwa* pliku cookie.                                           |
| http_method               | Metoda używana do żądania adresu URL. Na przykład Pobierz lub Opublikuj. |
| http_status               | Stan sesji. Na przykład 200, 400 lub 403.           |
| http_version              | Protokół żądania. Zazwyczaj HTTP/1.0, HTTP/1.1 lub HTTP/2.0. |
| query_string              | Lista par zmienna/wartość, które następuje po "?" w żądanym adresie URL. Przykład: w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING wartość zostanie `id=123&title=fabrikam` |
| received_bytes            | Długość żądania (w tym wiersza żądania, nagłówka i treści żądania). |
| request_query             | Argumenty w wierszu żądania.                           |
| request_scheme            | Schemat żądania: http lub https.                           |
| request_uri               | Pełny identyfikator URI żądania (z argumentami). Przykład: w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` REQUEST_URI wartość zostanie `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Liczba bajtów wysłanych do klienta.                        |
| server_port               | Port serwera, który zaakceptował żądanie.              |
| ssl_connection_protocol   | Protokół ustanowionego połączenia TLS.               |
| ssl_enabled               | "Włączone", jeśli połączenie działa w trybie TLS. W przeciwnym razie pusty ciąg. |
| uri_path                  | Identyfikuje określony zasób na hoście, do którego klient sieci Web chce uzyskać dostęp. Jest to część identyfikatora URI żądania bez argumentów. Przykład: w żądaniu `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path wartość zostanie `/article.aspx` |

 

## <a name="rewrite-configuration"></a>Zapisz ponownie konfigurację

Aby skonfigurować regułę ponownego zapisywania, należy utworzyć zestaw reguł ponownego zapisywania i dodać do niej konfigurację reguły ponownego zapisywania.

Zestaw reguł ponownego zapisywania zawiera:

* **Skojarzenie reguły routingu żądania:** Konfiguracja ponownego zapisywania jest skojarzona z odbiornikiem źródłowym za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu konfiguracja ponownego zapisu jest skojarzona z odbiornikiem źródłowym i jest zapisywana w nagłówku globalnym. W przypadku korzystania z reguły routingu opartej na ścieżce, konfiguracja ponownego zapisu jest definiowana na mapie ścieżki URL. W takim przypadku ma zastosowanie tylko do obszaru określonej ścieżki w lokacji. Można utworzyć wiele zestawów wielokrotnego zapisu i zastosować każdy ponowny zapis ustawiony dla wielu odbiorników. Można jednak zastosować tylko jeden wielokrotny zapis do określonego odbiornika.

* **Warunek ponownego zapisu**: jest to opcjonalna konfiguracja. Warunki ponownego zapisu sprawdzają zawartość żądań i odpowiedzi HTTP (S). Akcja ponownego zapisu zostanie wykonana, jeśli żądanie HTTP (S) lub odpowiedź pasuje do warunku ponownego zapisu. Jeśli powiążesz więcej niż jeden warunek z akcją, Akcja występuje tylko wtedy, gdy wszystkie warunki są spełnione. Innymi słowy, operacja jest operacją logiczną i.

* **Typ ponownego zapisywania**: dostępne są 3 typy ponownego zapisywania:
   * Ponowne zapisywanie nagłówków żądań 
   * Ponowne zapisywanie nagłówków odpowiedzi.
   * Ponowne zapisywanie adresu URL: typ ponownego zapisywania adresu URL zawiera 3 składniki
      * **Ścieżka URL**: wartość, do której ma zostać zapisywana ścieżka. 
      * **Ciąg zapytania URL**: wartość, w której ma zostać ponownie zapisany ciąg zapytania. 
      * **Ponownie Oceń mapę ścieżek**: służy do określenia, czy mapa ścieżki adresu URL ma zostać ponownie oceniona. Jeśli pole nie jest zaznaczone, oryginalna ścieżka URL zostanie użyta w celu dopasowania jej do wzorca ścieżki w mapie ścieżki URL. W przypadku ustawienia wartości true Mapa ścieżki URL zostanie ponownie oceniona, aby sprawdzić zgodność z zapisaną ścieżką. Włączenie tego przełącznika ułatwia kierowanie żądania do innej puli zaplecza po ponownym zapisaniu.

### <a name="common-scenarios-for-header-rewrite"></a>Typowe scenariusze ponownego zapisywania nagłówka

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Usuń informacje o porcie z nagłówka X-forwardd-for

Application Gateway wstawia do nagłówka X-forwardd-for wszystkie żądania przed przekazaniem żądań do zaplecza. Ten nagłówek jest rozdzielaną przecinkami listą portów IP. Mogą istnieć scenariusze, w których serwery zaplecza potrzebują tylko nagłówków, aby zawierały adresy IP. Aby usunąć informacje o porcie z nagłówka X-Forwarded-For, można użyć zapisu w nagłówku. Jednym ze sposobów jest ustawienie dla nagłówka zmiennej serwera add_x_forwarded_for_proxy. Alternatywnie można również użyć zmiennej client_ip:

![Usuń port](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Modyfikowanie adresu URL przekierowania

Gdy aplikacja zaplecza wysyła odpowiedź przekierowania, możesz chcieć przekierować klienta na inny adres URL niż określony przez aplikację zaplecza. Można na przykład wykonać tę czynność, gdy usługa App Service jest hostowana za bramą aplikacji i wymaga, aby klient przetworzył przekierowanie do swojej ścieżki względnej. (Na przykład przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2).

Ponieważ App Service jest usługą wielodostępnym, używa nagłówka hosta w żądaniu, aby skierować żądanie do właściwego punktu końcowego. Usługa App Services ma domyślną nazwę domeny *. azurewebsites.net (Powiedz contoso.azurewebsites.net), która różni się od nazwy domeny bramy aplikacji (Powiedz contoso.com). Ponieważ oryginalne żądanie od klienta ma nazwę hosta usługi Application Gateway (contoso.com), Brama aplikacji zmienia nazwy hosta na contoso.azurewebsites.net. Wprowadza tę zmianę, dzięki czemu usługa App Service może kierować żądanie do właściwego punktu końcowego.

Gdy usługa App Service wyśle odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji swojej odpowiedzi jako tej w żądaniu odbieranym od bramy aplikacji. Klient wyśle żądanie bezpośrednio, aby nie przechodzić `contoso.azurewebsites.net/path2` przez bramę aplikacji ( `contoso.com/path2` ). Pomijanie bramy aplikacji nie jest pożądane.

Ten problem można rozwiązać przez ustawienie nazwy hosta w nagłówku lokalizacji na nazwę domeny bramy aplikacji.

Poniżej przedstawiono procedurę zamieniania nazwy hosta:

1. Utwórz regułę ponownego zapisywania z warunkiem, który oblicza, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Wprowadź wzorzec `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Wykonaj akcję, aby ponownie zapisać nagłówek lokalizacji tak, aby miał nazwę hosta bramy aplikacji. Zrób to, wprowadzając `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka. Alternatywnie można również użyć zmiennej serwera, `host` Aby ustawić nazwę hosta tak, aby odpowiadała oryginalnemu żądaniu.

![Modyfikowanie nagłówka lokalizacji](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Zaimplementuj nagłówki HTTP zabezpieczeń, aby zapobiec występowaniu luk w zabezpieczeniach

Można naprawić kilka luk w zabezpieczeniach, implementując niezbędne nagłówki w odpowiedzi aplikacji. Te nagłówki zabezpieczeń obejmują X-XSS-Protection, rygorystyczne Transport-Security i Content-Security-Policy. Możesz użyć Application Gateway, aby ustawić te nagłówki dla wszystkich odpowiedzi.

![Nagłówek zabezpieczeń](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Usuń niechciane nagłówki

Możesz chcieć usunąć nagłówki, które ujawniają poufne informacje z odpowiedzi HTTP. Na przykład możesz chcieć usunąć informacje, takie jak nazwa serwera zaplecza, system operacyjny lub szczegóły biblioteki. Możesz użyć bramy aplikacji, aby usunąć następujące nagłówki:

![Usuwanie nagłówka](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Sprawdź obecność nagłówka

Można oszacować żądanie HTTP lub nagłówek odpowiedzi dla obecności nagłówka lub zmiennej serwerowej. Ta ocena jest przydatna, gdy chcesz wykonać ponowne zapisywanie nagłówka tylko wtedy, gdy jest obecny określony nagłówek.

![Sprawdzanie obecności nagłówka](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Typowe scenariusze ponownego zapisywania adresów URL

#### <a name="parameter-based-path-selection"></a>Wybór ścieżki na podstawie parametru

Aby wykonać scenariusze, w których chcesz wybrać pulę zaplecza na podstawie wartości nagłówka, części adresu URL lub ciągu zapytania w żądaniu, możesz użyć kombinacji możliwości ponownego zapisywania adresów URL i routingu opartego na ścieżkach.  Na przykład jeśli masz witrynę internetową zakupów, a Kategoria produktu jest przenoszona jako ciąg zapytania w adresie URL i chcesz skierować żądanie do zaplecza na podstawie ciągu zapytania, a następnie:

**Krok 1:**  Utwórz mapę Path, jak pokazano na poniższej ilustracji

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Scenariusz ponownego zapisywania adresów URL 1-1.":::

**Krok 2 (a):** Utwórz zestaw do ponownego zapisywania, który ma 3 reguły ponownego zapisywania: 

* Pierwsza reguła ma warunek, który sprawdza zmienną *QUERY_STRING* dla *kategorii = buty* i ma akcję, która ponownie zapisuje ścieżkę URL do/*listing1* i ma włączoną **ponowną ocenę mapy ścieżki**

* Druga reguła ma warunek, który sprawdza zmienną *QUERY_STRING* dla *kategorii = Works* i ma akcję, która ponownie zapisuje ścieżkę URL do/*listing2* i ma włączoną **ponowną ocenę mapy ścieżki**

* Trzecia reguła ma warunek, który sprawdza zmienną *QUERY_STRING* dla *kategorii = Akcesoria* i ma akcję, która ponownie zapisuje ścieżkę URL do/*listing3* i ma włączoną **ponowną ocenę mapy ścieżki**

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Scenariusz ponownego zapisywania adresów URL 1-2.":::

 

**Krok 2 (b):** Skojarz ten zestaw ponownie z domyślną ścieżką powyższej reguły opartej na ścieżce

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Scenariusz ponownego zapisywania adresów URL 1-3.":::

Teraz, jeśli użytkownik zażąda *contoso.com/Listing?Category=any*, zostanie dopasowany do ścieżki domyślnej, ponieważ żaden z wzorców ścieżki w mapie ścieżki (/listing1,/listing2,/listing3) nie będzie zgodny. Ze względu na to, że powyższy Zestaw wielokrotnego zapisu zostanie skojarzony z tą ścieżką, ten zestaw do ponownego zapisu zostanie obliczony. Ponieważ ciąg zapytania nie będzie pasował do warunku w żadnej z trzech reguł ponownego zapisywania w tym zestawie wielokrotnego zapisu, nie zostanie wykonana żadna akcja ponownego zapisywania, w związku z czym żądanie zostanie przesłane bez zmian do zaplecza skojarzonego z ścieżką domyślną (czyli *GenericList*).

 Jeśli użytkownik zażąda *contoso.com/Listing?Category=Shoes,* wówczas zostanie dopasowana ścieżka domyślna. Jednak w tym przypadku warunek w pierwszej regule będzie pasować i w związku z tym Akcja skojarzona z warunkiem zostanie wykonana, co spowoduje ponowne zapisanie ścieżki adresu URL do/*listing1*  i ponowną ocenę mapy ścieżki. Gdy mapa ścieżki zostanie ponownie oceniona, żądanie będzie teraz zgodne ze ścieżką skojarzoną ze wzorcem */listing1* , a żądanie zostanie przekazane do zaplecza skojarzonego z tym wzorcem, który jest ShoesListBackendPool

>[!NOTE]
>Ten scenariusz można rozszerzyć do dowolnej wartości nagłówka lub pliku cookie, ścieżki URL, ciągu zapytania lub zmiennych serwerowych na podstawie warunku zdefiniowanego, a w praktyce pozwala na kierowanie żądań na podstawie tych warunków.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Ponownie Napisz parametry ciągu zapytania na podstawie adresu URL

Rozważmy scenariusz witryny internetowej do kupowania, gdzie link widoczny dla użytkownika powinien być prosty i czytelny, ale serwer zaplecza potrzebuje parametrów ciągu zapytania, aby wyświetlić odpowiednią zawartość.

W takim przypadku Application Gateway może przechwytywać parametry z adresu URL i dodawać pary klucz-wartość ciągu zapytania z adresów URL. Załóżmy na przykład, że użytkownik chce ponownie napisać, `https://www.contoso.com/fashion/shirts` Aby `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` można go było osiągnąć za pomocą następującej konfiguracji ponownego zapisywania adresu URL.

**Warunek** — Jeśli zmienna serwera `uri_path` jest równa wzorzec `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Scenariusz ponownego zapisywania adresów URL 2-1.":::

**Akcja** — Ustaw ścieżkę adresu URL `buy.aspx` i ciąg zapytania do `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Scenariusz ponownego zapisywania adresów URL 2-2.":::

Aby zapoznać się ze scenariuszem krok po kroku w celu osiągnięcia opisanego powyżej scenariusza, zobacz [Zapisywanie adresu URL za pomocą Application Gateway przy użyciu Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>Przekierowanie adresu URL a Rewrite

W przypadku ponownego zapisywania adresu URL Application Gateway ponownie zapisuje adres URL przed wysłaniem żądania do zaplecza. Nie zmieni to, co użytkownicy widzą w przeglądarce, ponieważ zmiany są ukryte od użytkownika.

W przypadku przekierowania adresu URL Application Gateway wysyła odpowiedź przekierowania do klienta z nowym adresem URL. Z kolei program wymaga od klienta ponownego wysłania żądania do nowego adresu URL podanego w przekierowaniu. Adres URL widziany przez użytkownika w przeglądarce zostanie zaktualizowany do nowego adresu URL

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Zapisz ponownie a przekierowanie a.":::

## <a name="limitations"></a>Ograniczenia

- Jeśli odpowiedź ma więcej niż jeden nagłówek o tej samej nazwie, wówczas zapisanie wartości jednego z tych nagłówków spowoduje porzucenie pozostałych nagłówków w odpowiedzi. Zwykle może się to zdarzyć z nagłówkiem Set-Cookie, ponieważ w odpowiedzi można mieć więcej niż jeden nagłówek Set-Cookie. Taki scenariusz ma zastosowanie w przypadku korzystania z usługi App Service z bramą aplikacji i skonfigurowania koligacji sesji na podstawie plików cookie na bramie aplikacji. W takim przypadku odpowiedź będzie zawierać dwa nagłówki Set-Cookie: jeden używany przez usługę App Service, na przykład: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` i drugi dla koligacji bramy aplikacji, na przykład `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Ponowne napisanie jednego z nagłówków Set-Cookie w tym scenariuszu może spowodować usunięcie innego nagłówka Set-Cookie z odpowiedzi.
- Ponowne zapisywanie nie jest obsługiwane, gdy Brama aplikacji jest skonfigurowana do przekierowywania żądań lub wyświetlania niestandardowej strony błędu.
- Nazwy nagłówków mogą zawierać dowolne znaki alfanumeryczne i określone symbole, zgodnie z definicją w [dokumencie RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Obecnie nie obsługujemy znaku podkreślenia (_) w nazwach nagłówków.
- Nie można ponownie zapisać nagłówków połączeń i uaktualnień

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak ponownie pisać nagłówki HTTP za pomocą Application Gateway przy użyciu Azure Portal](rewrite-http-headers-portal.md)
- [Dowiedz się, jak ponownie napisać adres URL za pomocą Application Gateway przy użyciu Azure Portal](rewrite-url-portal.md)
