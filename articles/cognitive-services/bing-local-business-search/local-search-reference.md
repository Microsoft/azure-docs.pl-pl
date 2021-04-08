---
title: Dokumentacja wersji 7 interfejsu API wyszukiwania lokalnego usługi Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera szczegółowe informacje techniczne dotyczące obiektów odpowiedzi oraz parametry i nagłówki zapytania, które mają wpływ na wyniki wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 9791d99598fe3d043c42a37e2f4993edd6c5b3ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96487137"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Dokumentacja wersji 7 interfejsu API wyszukiwania lokalnego usługi Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Lokalny interfejs API wyszukiwania biznesowego wysyła zapytanie wyszukiwania do usługi Bing w celu uzyskania wyników obejmujących Restauracje, Hotele lub inne lokalne firmy. W przypadku miejsc zapytanie może określać nazwę lokalnej firmy lub kategorię (na przykład Restauracje blisko mnie). Wyniki dotyczące jednostek to między innymi osoby, miejsca i rzeczy. Miejsce w tym kontekście to jednostki biznesowe, Stany, kraje/regiony itd.  

Ta sekcja zawiera szczegółowe informacje techniczne dotyczące obiektów odpowiedzi oraz parametry i nagłówki zapytania, które mają wpływ na wyniki wyszukiwania. Aby zapoznać się z przykładami pokazującymi, jak wykonywać żądania, zobacz [Local Business Search C# — szybki start](quickstarts/local-quickstart.md) lub [Local Business Search Java — szybki start](quickstarts/local-search-java-quickstart.md). 
  
Aby uzyskać informacje o nagłówkach, które powinny obejmować żądania, zobacz [nagłówki](#headers).  
  
Aby uzyskać informacje na temat parametrów zapytania, które powinny zawierać żądania, zobacz [parametry zapytania](#query-parameters).  
  
Aby uzyskać informacje na temat obiektów JSON, które zawiera odpowiedź, zobacz [obiekty odpowiedzi](#response-objects).

Aby uzyskać informacje na temat dozwolonych użycia i wyświetlania wyników, zobacz [użycie i wyświetlanie wymagań](../bing-web-search/use-display-requirements.md).


  
## <a name="endpoint"></a>Punkt końcowy  
Aby zażądać lokalnych wyników firmy, Wyślij żądanie GET do: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Żądanie musi korzystać z protokołu HTTPS.  
  
> [!NOTE]
> Maksymalna długość adresu URL to 2 048 znaków. Aby zapewnić, że długość adresu URL nie przekracza limitu, Maksymalna długość parametrów zapytania powinna być krótsza niż 1 500 znaków. Jeśli adres URL przekracza 2 048 znaków, serwer zwraca 404 nie znaleziono.  
  
  
## <a name="headers"></a>Nagłówki  
Poniżej znajdują się nagłówki, których może dotyczyć żądanie i odpowiedź.  
  
|Nagłówek|Opis|  
|------------|-----------------|  
|Zaakceptuj|Opcjonalny nagłówek żądania.<br /><br /> Domyślnym typem nośnika jest Application/JSON. Aby określić, że odpowiedź ma używać [JSON-LD](https://json-ld.org/), ustaw nagłówek Accept na Application/LD + JSON.|  
|<a name="acceptlanguage"></a>Accept-Language|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana przecinkami lista języków dla ciągów interfejsu użytkownika. Lista jest zorganizowana w malejącym porządku preferencji. Aby uzyskać więcej informacji, łącznie z oczekiwanym formatem, zobacz [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Ten nagłówek i parametr zapytania [setLang](#setlang) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Jeśli ustawisz ten nagłówek, musisz także określić parametr zapytania cc. Aby ustalić rynek, dla którego mają zostać zwrócone wyniki, usługa Bing używa pierwszego obsługiwanego języka znalezionego na liście i łączy go z wartością parametru `cc`. Jeśli lista nie zawiera obsługiwanego języka, usługa Bing znajduje najbliższy język i rynek, które obsługują żądanie, lub używa rynku zagregowanego bądź domyślnego. Aby sprawdzić, jakiego rynku użyła usługa Bing, zobacz nagłówek BingAPIs-Market.<br /><br /> Używaj tego nagłówka i parametru zapytania `cc` tylko wtedy, gdy określasz wiele języków. W przeciwnym razie użyj parametrów zapytania [mkt](#mkt) i [setLang](#setlang).<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|  
|<a name="market"></a>BingAPIs-Market|Nagłówek odpowiedzi.<br /><br /> Rynek używany przez żądanie. Formularz to \<languageCode\> - \<countryCode\> . Na przykład en-US.|  
|<a name="traceid"></a>BingAPIs-TraceId|Nagłówek odpowiedzi.<br /><br /> Identyfikator wpisu dziennika, który zawiera szczegółowe informacje o żądaniu. Gdy wystąpi błąd, przechwyć ten identyfikator. Jeśli nie możesz określić i rozwiązać problemu, dołącz ten identyfikator wraz z innymi informacjami, które podasz zespołowi pomocy technicznej.|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|Wymagany nagłówek żądania.<br /><br /> Klucz subskrypcji otrzymany podczas tworzenia konta dla tej usługi w usługach [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma"></a>Pragma|Opcjonalny nagłówek żądania<br /><br /> Usługa Bing domyślnie zwraca zawartość buforowaną, jeśli jest dostępna. Aby zapobiec zwracaniu zawartości buforowanej przez usługę Bing, dla nagłówka Pragma ustaw wartość no-cache (na przykład Pragma: no-cache).
|<a name="useragent"></a>User-Agent|Opcjonalny nagłówek żądania.<br /><br /> Agent użytkownika, od którego pochodzi żądanie. Usługa Bing korzysta z agenta użytkownika, aby zapewnić zoptymalizowane środowisko obsługi dla użytkowników urządzeń przenośnych. Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać.<br /><br /> Nagłówek user-agent powinien być takim samym ciągiem, jaki wysyła każda powszechnie używana przeglądarka. Aby uzyskać informacje na temat agentów użytkownika, zobacz [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Poniżej przedstawiono przykłady ciągów nagłówka user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (zgodne; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; jak Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 jak Mac OS X) AppleWebKit/536.26 (KHTML; jak Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>Komputer PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) jak Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 jak Mac OS X) AppleWebKit/537.51.1 (KHTML, jak Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|Opcjonalny nagłówek żądania i odpowiedzi.<br /><br /> Usługa Bing używa tego nagłówka, aby zapewnić użytkownikom spójne zachowanie dla wywołań interfejsu API usługi Bing. Usługa Bing często testuje nowe funkcje i ulepszenia i używa identyfikatora klienta jako klucza do przypisywania ruchu dla różnych pakietów testowych. Jeśli nie będziesz używać tego samego identyfikatora klienta dla użytkownika w wielu żądaniach, usługa Bing może przypisać użytkownika do różnych, konfliktowych pakietów testowych. Przypisanie do wielu konfliktowych pakietów testowych może prowadzić do niespójnego środowiska użytkownika. Jeśli na przykład drugie żądanie ma przypisany inny pakiet testowy, niż pierwsze, środowisko obsługi może być nieoczekiwane. Ponadto usługa Bing może używać identyfikatora klienta, aby dopasować wyniki internetowe do historii wyszukiwania tego identyfikatora, zapewniając użytkownikowi bogatsze środowisko obsługi.<br /><br /> Usługa Bing używa także tego nagłówka, aby ulepszyć pozycjonowanie wyników, analizując aktywność generowaną przez identyfikator klienta. Ulepszenia istotności pomagają zwiększyć jakość wyników dostarczanych przez interfejsy API usługi Bing, co w rezultacie daje wyższą częstotliwość kliknięć dla użytkownika interfejsu API.<br /><br /> **WAŻNE:** Mimo że ten nagłówek jest opcjonalny, można go uważać za wymagany. Utrwalenie identyfikatora klienta dla wielu żądań dla połączenia tego samego użytkownika końcowego i urządzenia umożliwia (1) zapewnienie użytkownikowi interfejsu API spójnego środowiska obsługi oraz (2) uzyskanie wyższej częstotliwości kliknięć dzięki lepszej jakość wyników z interfejsów API usługi Bing.<br /><br /> Poniżej przedstawiono podstawowe reguły użycia, które mają zastosowanie do tego nagłówka.<br /><ul><li>Każdy użytkownik, który korzysta z Twojej aplikacji na urządzeniu, musi mieć unikatowy identyfikator klienta wygenerowany przez usługę Bing.<br /><br/>Jeśli nie uwzględnisz tego nagłówka w żądaniu, usługa Bing wygeneruje identyfikator i zwróci go w nagłówku odpowiedzi X-MSEdge-ClientID. Jedyną sytuacją, w której NIE należy uwzględniać tego nagłówka w żądaniu, jest pierwsze użycie Twojej aplikacji przez danego użytkownika na danym urządzeniu.<br /><br/></li><li>Używaj identyfikatora klienta dla każdego żądania interfejsu API usługi Bing, które Twoja aplikacja wykonuje dla tego użytkownika na danym urządzeniu.<br /><br/></li><li>**Uwaga:** Należy upewnić się, że ten identyfikator klienta nie jest połączony z żadnym z uwierzytelnianymi informacjami o koncie użytkownika.</li><br/><li>Utrwal identyfikator klienta. Aby utrwalić identyfikator w aplikacji przeglądarki, użyj trwałego pliku cookie HTTP, aby mieć pewność, że dany identyfikator będzie używany we wszystkich sesjach. Nie należy używać plików cookie sesji. W przypadku innych aplikacji, takich jak aplikacje mobilne, użyj magazynu trwałego urządzenia, aby utrwalić identyfikator.<br /><br/>Następnym razem, gdy użytkownik będzie używać Twojej aplikacji na tym urządzeniu, uzyskaj utrwalony identyfikator klienta.</li></ul><br /> **UWAGA:** Odpowiedzi usługi Bing mogą zawierać ten nagłówek lub nie. Jeśli odpowiedź zawiera ten nagłówek, przechwyć identyfikator klienta i używaj go dla wszystkich kolejnych żądań usługi Bing dla tego użytkownika na tym urządzeniu.<br /><br /> **UWAGA:** jeśli dodasz nagłówek X-MSEdge-ClientID, nie możesz uwzględnić w żądaniu plików cookie.|  
|<a name="clientip"></a>X-MSEdge-ClientIP|Opcjonalny nagłówek żądania.<br /><br /> Adres IPv4 lub IPv6 na urządzeniu klienckim. Adres IP jest używany w celu odnalezienia lokalizacji użytkownika. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania.<br /><br /> **UWAGA:** Mimo że ten nagłówek jest opcjonalny, zachęcamy, aby go zawsze określać wraz z nagłówkiem X-Search-Location.<br /><br /> Nie należy zaciemniać adres (na przykład zamieniając ostatni oktet na 0). Zaciemnianie adresu powoduje, że ustalona lokalizacja nie jest nawet przybliżona do rzeczywistej lokalizacji urządzenia, przez co usługa Bing może zwracać błędne wyniki.|  
|<a name="location"></a>X-Search-Location|Opcjonalny nagłówek żądania.<br /><br /> Rozdzielana średnikami lista par klucz/wartość, które opisują geograficzną lokalizację klienta. Usługa Bing używa informacji o lokalizacji, aby określić sposób bezpiecznego wyszukiwania i aby zwracać odpowiednią zawartość lokalną. Określ parę klucz/wartość jako \<key\> : \<value\> . Poniżej przedstawiono klucze, które służą do określania lokalizacji użytkownika.<br /><br /><ul><li>Szerokość &mdash; geograficzna lokalizacji klienta (w stopniach). Szerokość geograficzna musi być większa niż lub równa -90.0 i mniejsza niż lub równa +90.0. Wartości ujemne wskazują południową szerokość geograficzną, a wartości dodatnie wskazują północną szerokość geograficzną.<br /><br /></li><li>Długość &mdash; długości geograficznej lokalizacji klienta (w stopniach). Długość geograficzna musi być większa niż lub równa -180.0 i mniejsza niż lub równa +180.0. Wartości ujemne wskazują zachodnią długość geograficzną, a wartości dodatnie wskazują wschodnią długość geograficzną.<br /><br /></li><li>odnowić &mdash; promień w licznikach, który określa poziom dokładności współrzędnych. Przekaż wartość zwróconą przez usługę lokalizacji urządzenia. Typowe wartości mogą wynosić 22 m dla sieci GPS/Wi-Fi, 380 m dla triangulacji wieży sieci komórkowej i 18 000 m dla wstecznego wyszukiwania adresu IP.<br /><br /></li><li>&mdash;czas znacznika czasu UTC systemu UNIX w przypadku, gdy klient znajdował się w lokalizacji. (Sygnatura czasowa systemu UNIX to liczba sekund od 1 stycznia 1970 r.).<br /><br /></li><li>head&mdash;Opcjonalne. Względny kierunek przemieszczania się klienta. Określ kierunek ruchu jako liczbę stopni z zakresu od 0 do 360, zliczanych zgodnie z ruchem wskazówek zegara względem prawdziwej północy. Określ ten klucz tylko wtedy, gdy wartość klucza `sp` jest różna od zera.<br /><br /></li><li>&mdash;przyrządy (prędkość) w mikrosekundach na sekundę, które urządzenie klienckie jest podróżujące.<br /><br /></li><li>Alt &mdash; wysokość urządzenia klienckiego w metrach.<br /><br /></li><li>are&mdash;Opcjonalne. Promień, w metrach, który określa pionową dokładność współrzędnych. Wartość domyślna usługi RADIUS to 50 kilometrów. Określ ten klucz tylko wtedy, gdy wartość klucza `alt` także została określona.<br /><br /></li></ul> **Uwaga:** Chociaż te klucze są opcjonalne, im więcej informacji zawiera użytkownik, tym dokładniejsze są wyniki lokalizacji.<br /><br /> **Uwaga:** Zaleca się, aby zawsze określać lokalizację geograficzną użytkownika. Podanie lokalizacji jest szczególnie ważne, jeśli adres IP klienta nie odzwierciedla precyzyjnie fizycznej lokalizacji użytkownika (na przykład, jeśli klient korzysta z sieci VPN). Aby uzyskać optymalne wyniki należy uwzględnić ten nagłówek i nagłówek X-MSEdge-ClientIP, a co najmniej należy uwzględnić ten nagłówek.|

> [!NOTE] 
> Pamiętaj, że Warunki użytkowania wymagają zgodności ze wszystkimi obowiązującymi przepisami prawa, w tym dotyczącymi korzystania z tych nagłówków. W niektórych systemach prawnych, na przykład w Europie, wymagane jest uzyskania zgody użytkownika przed umieszczeniem narzędzi śledzących na urządzeniu użytkownika.
  

## <a name="query-parameters"></a>Parametry zapytania  
Żądanie może zawierać następujące parametry zapytania. Sprawdź wymaganą kolumnę dla wymaganych parametrów. Należy zakodować parametry zapytania w adresie URL.  
  
  
|Nazwa|Wartość|Typ|Wymagane|  
|----------|-----------|----------|--------------|
|<a name="count"></a>count|Liczba wyników do zwrócenia, rozpoczynając od indeksu określonego przez `offset` parametr.|Ciąg|Nie|   
|<a name="localCategories"></a>localCategories|Lista opcji definiujących wyszukiwanie według kategorii biznesowej.  Zobacz [Wyszukiwanie lokalnych kategorii firmowych](local-categories.md)|Ciąg|Nie|  
|<a name="mkt"></a>mkt|Rynek, z którego pochodzą wyniki. <br /><br />Aby uzyskać listę możliwych wartości rynkowych, zobacz Kody rynku.<br /><br /> **Uwaga:** Lokalny interfejs API wyszukiwania biznesowego obecnie obsługuje tylko rynek en-us i język.<br /><br />|Ciąg|Tak|
|<a name="offset"></a>przesunięcie|Indeks do uruchamiania wyników określonego przez `count` parametr.|Liczba całkowita|Nie|  
|<a name="query"></a>pytania|Termin wyszukiwania użytkownika.|Ciąg|Nie|  
|<a name="responseformat"></a>responseFormat|Typ nośnika, który ma być używany na potrzeby odpowiedzi. Poniżej przedstawiono możliwe wartości bez uwzględniania wielkości liter.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Wartość domyślna to JSON. Aby uzyskać informacje na temat obiektów JSON zawartych w odpowiedzi, zobacz temat [obiekty odpowiedzi](#response-objects).<br /><br />  Jeśli określisz JsonLd, treść odpowiedzi zawiera obiekty JSON-LD, które zawierają wyniki wyszukiwania. Aby uzyskać informacje na temat JSON-LD, zobacz [JSON-LD](https://json-ld.org/).|Ciąg|Nie|  
|<a name="safesearch"></a>safeSearch|Filtr używany do odfiltrowania zawartości dla dorosłych. Poniżej przedstawiono możliwe wartości filtru bez uwzględniania wielkości liter.<br /><ul><li>Wyłącz &mdash; zwracanie stron sieci Web z tekstem dla dorosłych, obrazami lub klipami wideo.<br /><br/></li><li>Umiarkowanie &mdash; zwracaj strony sieci Web z tekstem dla dorosłych, ale nie z obrazami lub wideo dla dorosłych.<br /><br/></li><li>Ścisłe &mdash; nie zwracają stron internetowych z tekstem dla dorosłych, obrazami lub klipami wideo.</li></ul><br /> Wartość domyślna to Moderate.<br /><br /> **Uwaga:** Jeśli żądanie pochodzi ze rynku, że zasady dla dorosłych usługi Bing wymagają `safeSearch` Ustawienia Strict, Bing ignoruje `safeSearch` wartość i używa rygorystyczne.<br/><br/>**UWAGA:** Jeśli używasz operatora zapytania `site:`, istnieje ryzyko, że odpowiedź będzie zawierać zawartość dla dorosłych niezależnie od ustawienia parametru zapytania `safeSearch`. Operatora `site:` używaj tylko wtedy, gdy znasz zawartość witryny i w swoim scenariuszu uwzględniasz możliwość pojawienia się zawartości dla dorosłych. |Ciąg|Nie|  
|<a name="setlang"></a>setLang|Język ciągów interfejsu użytkownika. Określ język przy użyciu 2-literowego kodu języka w standardzie ISO 639-1. Na przykład kod języka dla języka angielskiego to EN. Wartość domyślna to EN (język angielski).<br /><br /> Mimo, że jest to opcjonalne, należy zawsze określić język. Na ogół dla parametru `setLang` ustawia się język określony przez parametr `mkt`, chyba że użytkownik chce, aby ciągi interfejsu użytkownika były wyświetlane w innym języku.<br /><br /> Ten parametr i nagłówek [Accept-Language](#acceptlanguage) wykluczają się wzajemnie&mdash;nie określaj ich jednocześnie.<br /><br /> Ciąg interfejsu użytkownika to ciąg, który jest używany jako etykieta w interfejsie użytkownika. W obiektach odpowiedzi JSON istnieje kilka ciągów interfejsu użytkownika. Ponadto wszelkie linki do właściwości witryny Bing.com w obiektach odpowiedzi także stosują określony język.|Ciąg|Nie| 


## <a name="response-objects"></a>Obiekty odpowiedzi  
Poniżej znajdują się obiekty odpowiedzi JSON, które może zawierać odpowiedź. Jeśli żądanie zakończy się powodzeniem, obiekt najwyższego poziomu w odpowiedzi jest obiektem [SearchResponse](#searchresponse) . Jeśli żądanie nie powiedzie się, obiekt najwyższego poziomu jest obiektem [zwrócono](#errorresponse) .


|Obiekt|Opis|  
|------------|-----------------|  
|[Przesuwa](#place)|Definiuje informacje o lokalnej firmie, np. w restauracji lub hotelu.|  

  
### <a name="error"></a>Błąd  
Określa błąd, który wystąpił.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>kodu|Kod błędu, który identyfikuje kategorię błędu. Listę możliwych kodów można znaleźć w temacie [kody błędów](#error-codes).|Ciąg|  
|<a name="error-message"></a>message|Opis błędu.|Ciąg|  
|<a name="error-moredetails"></a>moreDetails|Opis, który zawiera dodatkowe informacje o błędzie.|Ciąg|  
|<a name="error-parameter"></a>konstruktora|Parametr zapytania w żądaniu, który spowodował błąd.|Ciąg|  
|<a name="error-subcode"></a>podkod|Kod błędu, który identyfikuje błąd. Na przykład jeśli `code` jest InvalidRequest, `subCode` może to być ParameterInvalid lub ParameterInvalidValue. |Ciąg|  
|<a name="error-value"></a>wartościami|Wartość parametru zapytania, która jest nieprawidłowa.|Ciąg|  
  

### <a name="errorresponse"></a>Zwrócono  
Obiekt najwyższego poziomu, który odpowiedź zawiera, gdy żądanie nie powiedzie się.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu.|Ciąg|  
|<a name="errors"></a>Błędy|Lista błędów opisujących przyczyny niepowodzenia żądania.|[Błąd](#error)[]|  

  
  
### <a name="license"></a>Licencja  
Definiuje licencję, pod którą można używać tekstu lub fotografii.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa licencji.|Ciąg|  
|url|Adres URL witryny sieci Web, w której użytkownik może uzyskać więcej informacji na temat licencji.<br /><br /> Użyj nazwy i adresu URL, aby utworzyć hiperłącze.|Ciąg|  


### <a name="link"></a>Link  
Definiuje składniki hiperłącza.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu.|Ciąg|  
|tekst|Wyświetlany tekst.|Ciąg|  
|url|Adres URL. Użyj adresu URL i tekstu wyświetlanego, aby utworzyć hiperłącze.|Ciąg|  
  


  
### <a name="organization"></a>Organizacja  
Definiuje wydawcę.  
  
Należy pamiętać, że Wydawca może podać jego nazwę lub witrynę sieci Web.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|name|Nazwa wydawcy.|Ciąg|  
|url|Adres URL witryny sieci Web wydawcy.<br /><br /> Zwróć uwagę, że Wydawca może nie dostarczyć witryny sieci Web.|Ciąg|  
  
  

### <a name="place"></a>Miejsce  
Definiuje informacje o lokalnej firmie, np. w restauracji lub hotelu.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wskazówkę typu, która może być ustawiona na jedną z następujących wartości:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restauracja</ul><li>|Ciąg|  
|adres|Adres pocztowy miejsca, w którym znajduje się jednostka.|PostalAddress|  
|entityPresentationInfo|Dodatkowe informacje na temat jednostki, takie jak wskazówki, których można użyć do określenia typu jednostki. Na przykład niezależnie od tego, czy jest to restauracja, czy Hotel. `entityScenario`Pole jest ustawione na wartość ListItem.|EntityPresentationInfo|  
|name|Nazwa jednostki.|Ciąg|  
|telefon|Numer telefonu jednostki.|Ciąg|  
|url|Adres URL witryny sieci Web jednostki.<br /><br /> Użyj tego adresu URL wraz z nazwą jednostki, aby utworzyć hiperłącze, które po kliknięciu przenosi użytkownika do witryny sieci Web jednostki.|Ciąg|  
|webSearchUrl|Adres URL dla tego miejsca wyszukiwania w usłudze Bing.|Ciąg| 
  
  
### <a name="querycontext"></a>Kontekst zapytania  
Definiuje kontekst zapytania, który jest używany przez usługę Bing dla żądania.  
  
|Element|Opis|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Wartość logiczna wskazująca, czy określone zapytanie ma zamiar dla dorosłych. Wartość jest **równa true** , jeśli zapytanie ma dla dorosłych cel; w przeciwnym razie **false**.|Wartość logiczna|  
|alterationOverrideQuery|Ciąg zapytania, który ma zostać użyty w celu wymuszenia użycia oryginalnego ciągu przez usługę Bing. Na przykład jeśli ciąg zapytania to *Saling downwind*, przesłonięcie ciągu zapytania będzie *+ Saling downwind*. Pamiętaj, aby zakodować ciąg zapytania, którego wynikiem jest *% 2Bsaling + downwind*.<br /><br /> To pole jest dostępne tylko wtedy, gdy pierwotny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|alteredQuery|Ciąg zapytania używany przez usługę Bing do wykonania zapytania. Bing używa zmienionego ciągu zapytania, jeśli oryginalny ciąg zapytania zawiera błędy pisowni. Na przykład jeśli ciąg zapytania to `saling downwind` , zmieniony ciąg zapytania będzie `sailing downwind` .<br /><br /> To pole jest dostępne tylko wtedy, gdy pierwotny ciąg zapytania zawiera błąd pisowni.|Ciąg|  
|askUserForLocation|Wartość logiczna wskazująca, czy Bing wymaga lokalizacji użytkownika, aby zapewnić dokładne wyniki. Jeśli określono lokalizację użytkownika przy użyciu nagłówków [x-MSEdge-ClientIP](#clientip) i [X-Search-Location](#location) , można zignorować to pole.<br /><br /> W przypadku zapytań dotyczących lokalizacji, takich jak "Pogoda dzisiaj" lub "Restauracje w pobliżu", które potrzebują lokalizacji użytkownika, aby zapewnić dokładne wyniki, to pole jest ustawione na **wartość true**.<br /><br /> W przypadku zapytań dotyczących lokalizacji, które zawierają lokalizację (na przykład "Pogoda pogodowa"), to pole jest ustawione na **wartość false**. To pole jest również ustawiane na **wartość false** dla zapytań, które nie obsługują lokalizacji, takich jak "Najlepsza sprzedaż".|Wartość logiczna|  
|originalQuery|Ciąg zapytania określony w żądaniu.|Ciąg|  

### <a name="identifiable"></a>Identyfikowan

|Nazwa|Wartość|Typ|  
|-------------|-----------------|----------|
|identyfikator|Identyfikator zasobu|Ciąg|
 
### <a name="rankinggroup"></a>Klasyfikacja
Definiuje grupę wyników wyszukiwania, taką jak linii głównej.

|Nazwa|Wartość|Typ|  
|-------------|-----------------|----------|
|produktów|Lista wyników wyszukiwania, które mają być wyświetlane w grupie.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definiuje element wyników wyszukiwania do wyświetlenia.

|Nazwa|Wartość|Typ|  
|-------------|-----------------|----------|
|resultIndex|Indeks (liczony od zera) elementu w odpowiedzi, który ma być wyświetlany. Jeśli element nie zawiera tego pola, Wyświetl wszystkie elementy w odpowiedzi. Na przykład Wyświetl wszystkie artykuły z wiadomościami w odpowiedzi na wiadomości.|Liczba całkowita|
|odpowiedź|Odpowiedź zawierająca element do wyświetlenia. Na przykład wiadomości.<br /><br />Użyj typu, aby znaleźć odpowiedź w obiekcie SearchResponse. Typ jest nazwą pola SearchResponse.<br /><br /> Należy jednak użyć typu odpowiedzi tylko wtedy, gdy ten obiekt zawiera pole Value; w przeciwnym razie zignoruj ją.|Ciąg|
|textualIndex|Indeks odpowiedzi w textualAnswers do wyświetlenia.| Liczba całkowita bez znaku|
|wartość|Identyfikator identyfikujący odpowiedź do wyświetlenia lub element odpowiedzi do wyświetlenia. Jeśli identyfikator identyfikuje odpowiedź, Wyświetl wszystkie elementy odpowiedzi.|Identyfikowan|

### <a name="rankingresponse"></a>RankingResponse  
Definiuje, gdzie powinna zostać umieszczona zawartość strony wyników wyszukiwania i w jakiej kolejności.  
  
|Nazwa|Wartość|  
|----------|-----------|  
|<a name="ranking-mainline"></a>linii głównej|Wyniki wyszukiwania do wyświetlenia w linii głównej.|  
|<a name="ranking-pole"></a>słup|Wyniki wyszukiwania, które powinny mieć zapewnione najbardziej widoczne traktowanie (na przykład widoczne nad linii głównej i paskiem bocznym).|  
|<a name="ranking-sidebar"></a>—|Wyniki wyszukiwania do wyświetlenia na pasku bocznym.| 

### <a name="searchresponse"></a>SearchResponse  
Definiuje obiekt najwyższego poziomu, który odpowiedź zawiera, gdy żądanie zakończy się pomyślnie.  
  
Należy pamiętać, że jeśli usługa podejrzewa atak typu "odmowa usługi", żądanie zakończy się pomyślnie (kod stanu HTTP to 200 OK); Jednakże treść odpowiedzi będzie pusta.  
  
|Nazwa|Wartość|Typ|  
|----------|-----------|----------|  
|_type|Wskazówka dotycząca typu, która jest ustawiona na SearchResponse.|Ciąg|  
|składa|Lista jednostek, które są istotne dla zapytania wyszukiwania.|Obiekt JSON|  
|Kontekst zapytania|Obiekt, który zawiera ciąg zapytania, który jest używany przez usługę Bing dla żądania.<br /><br /> Ten obiekt zawiera ciąg zapytania wprowadzony przez użytkownika. Może również zawierać zmieniony ciąg zapytania, który jest używany przez usługę Bing dla zapytania, jeśli ciąg zapytania zawiera błąd pisowni.|[Kontekst zapytania](#querycontext)|  


## <a name="error-codes"></a>Kody błędów

Oto możliwe kody stanu HTTP zwracane przez żądanie.  
  
|Kod stanu|Opis|  
|-----------------|-----------------|  
|200|Powodzenie.|  
|400|Brakuje jednego z parametrów zapytania lub jest on nieprawidłowy.|  
|401|Brak klucza subskrypcji lub jest on nieprawidłowy.|  
|403|Użytkownik jest uwierzytelniany (na przykład użył prawidłowego klucza subskrypcji), ale nie ma uprawnień do żądanego zasobu.<br /><br /> Jeśli obiekt wywołujący przekroczył limit przydziału zapytań na miesiąc, może również zwrócić ten stan.|  
|410|Żądanie używało protokołu HTTP zamiast protokołu HTTPS. Jedynym obsługiwanym protokołem jest protokół HTTPS.|  
|429|Obiekt wywołujący przekroczył liczbę zapytań na sekundę.|  
|500|Nieoczekiwany błąd serwera.|

Jeśli żądanie nie powiedzie się, odpowiedź zawiera obiekt [zwrócono](#errorresponse) , który zawiera listę obiektów [błędów](#error) , które opisują przyczynę błędu. Jeśli błąd jest związany z parametrem, `parameter` pole Identyfikuje parametr, który jest problemem. A jeśli błąd jest związany z wartością parametru, `value` pole określa nieprawidłową wartość.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Poniżej przedstawiono możliwy kod błędu i wartości kodu błędu podrzędnego.

|Kod|Podkod|Opis
|-|-|-
|Błąd servererror|UnexpectedError<br/>ResourceError<br/>Nie zaimplementowano|Kod stanu HTTP to 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Zablokowane|Bing zwraca InvalidRequest, gdy jakakolwiek część żądania jest nieprawidłowa. Na przykład brakuje wymaganego parametru lub wartość parametru jest nieprawidłowa.<br/><br/>Jeśli błąd to ParameterMissing lub ParameterInvalidValue, kod stanu HTTP to 400.<br/><br/>Jeśli używasz protokołu HTTP zamiast HTTPS, Bing zwraca HttpNotAllowed, a kod stanu HTTP to 410.
|RateLimitExceeded|Brak kodów podrzędnych|Bing zwraca RateLimitExceeded za każdym razem, gdy przekroczą limit przydziału zapytań na sekundę (zapytań) lub zapytania miesięcznie (QPM).<br/><br/>W przypadku przekroczenia zapytań, Bing zwraca kod stanu HTTP 429 i w przypadku przekroczenia QPM, Bing zwróci 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing zwraca InvalidAuthorization, gdy Bing nie może uwierzytelnić obiektu wywołującego. Na przykład `Ocp-Apim-Subscription-Key` brakuje nagłówka lub klucz subskrypcji jest nieprawidłowy.<br/><br/>Nadmiarowość występuje, jeśli określono więcej niż jedną metodę uwierzytelniania.<br/><br/>Jeśli błąd to InvalidAuthorization, kod stanu HTTP to 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Usługa Bing zwraca InsufficientAuthorization, gdy obiekt wywołujący nie ma uprawnień dostępu do zasobu. Taka sytuacja może wystąpić, jeśli klucz subskrypcji został wyłączony lub wygasł. <br/><br/>Jeśli błąd to InsufficientAuthorization, kod stanu HTTP to 403.

## <a name="next-steps"></a>Następne kroki
- [Lokalne wyszukiwanie biznesowe — Szybki Start](quickstarts/local-quickstart.md)
- [Lokalne wyszukiwanie biznesowe w języku Java — Szybki Start](quickstarts/local-search-java-quickstart.md)
- [Lokalny węzeł wyszukiwania biznesowego — Szybki Start](quickstarts/local-search-node-quickstart.md)
- [Lokalne wyszukiwanie biznesowe w języku Python — Szybki Start](quickstarts/local-search-python-quickstart.md)