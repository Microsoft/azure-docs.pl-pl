---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji sieci Web przy użyciu interfejs API wyszukiwania wiadomości Bing'
titleSuffix: Azure Cognitive Services
description: Przy użyciu tego samouczka możesz utworzyć jednostronicową aplikację internetową, która wysyła zapytania wyszukiwania do interfejsu API wyszukiwania wiadomości Bing i wyświetla wyniki na stronie internetowej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: 225ab3a8ab03c507132bdfcb176aeeb706d6b115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304053"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Samouczek: Tworzenie jednostronicowej aplikacji sieci Web

Interfejs API wyszukiwania wiadomości Bing umożliwia wyszukiwanie w Internecie i uzyskiwanie wyników wiadomości odpowiadających zapytaniu wyszukiwania. W tym samouczku skompilujemy jednostronicową aplikację internetową, która wyświetla wyniki wyszukiwania na stronie przy użyciu interfejsu API wyszukiwania wiadomości Bing. Aplikacja zawiera składniki HTML, CSS i JavaScript. Kod źródłowy dla tego przykładu jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html).

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> Po kliknięciu nagłówków JSON i HTTP w dolnej części strony zostanie wyświetlona odpowiedź JSON i informacje o żądaniu HTTP. Informacje te mogą być przydatne podczas eksplorowania usługi.

Aplikacja samouczka ilustruje sposób wykonywania następujących czynności:
> [!div class="checklist"]
> * Wywoływanie interfejsu API wyszukiwania wiadomości Bing w języku JavaScript
> * Przekazywanie opcji wyszukiwania do interfejsu API wyszukiwania wiadomości Bing
> * Wyświetlanie wyników wyszukiwania wiadomości z czterech kategorii: dowolny typ, biznes, zdrowie lub polityka, z następujących przedziałów czasowych: 24 godziny, ostatni tydzień, miesiąc lub cały dostępny czas
> * Dzielenie wyników wyszukiwania na strony
> * Obsługa identyfikatora klienta i klucza subskrypcji interfejsu API usługi Bing
> * Obsługa błędów, które mogą wystąpić

Strona samouczka jest całkowicie niezależna. Nie używa żadnych zewnętrznych struktur, arkuszy stylów ani plików obrazów. Korzysta jedynie z powszechnie obsługiwanych funkcji języka JavaScript i działa w aktualnych wersjach wszystkich popularnych przeglądarek internetowych.


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w samouczku, należy dysponować kluczami subskrypcji dla interfejsu API Wyszukiwanie Bing. Jeśli ich nie masz, musisz je utworzyć:

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Utwórz zasób wyszukiwanie Bing "  target="_blank"> utwórz zasób Wyszukiwanie Bing <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.

## <a name="app-components"></a>Składniki aplikacji
Podobnie jak każda inna aplikacja internetowa, aplikacja w tym samouczku zawiera trzy części:

> [!div class="checklist"]
> * HTML — definiuje strukturę i zawartość strony
> * CSS — definiuje wygląd strony
> * JavaScript — określa zachowanie strony

Większość kodu HTML i CSS jest konwencjonalna, dlatego nie omówiono ich w samouczku. Część HTML zawiera formularz wyszukiwania, w którym użytkownik wprowadza zapytanie i wybiera opcje wyszukiwania. Formularz jest połączony z kodem JavaScript, który faktycznie wykonuje wyszukiwanie przy użyciu atrybutu `onsubmit` tagu `<form>`:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
Procedura obsługi `onsubmit` zwraca wynik `false`, który zapobiega przesłaniu formularza na serwer. Kod JavaScript zbiera wymagane informacje z formularza i wykonuje wyszukiwanie.

Kod HTML zawiera także podziały (tagi `<div>`) tam, gdzie są wyświetlane wyniki wyszukiwania.

## <a name="managing-subscription-key"></a>Zarządzanie kluczem subskrypcji

Aby uniknąć konieczności umieszczania klucza subskrypcji interfejsu API wyszukiwania Bing w kodzie, korzystamy z magazynu trwałego przeglądarki w celu przechowywania klucza. Zanim klucz zostanie zachowany, wyświetlany jest monit o podanie klucza użytkownika. Jeśli klucz zostanie później odrzucony przez interfejs API, unieważniamy przechowywany klucz, aby użytkownikowi ponownie został wyświetlony monit.

Definiujemy funkcje `storeValue` i `retrieveValue`, które używają obiektu `localStorage` (nie wszystkie przeglądarki go obsługują) lub pliku cookie. Funkcja `getSubscriptionKey()` używa tych funkcji do przechowywania i pobierania klucza użytkownika. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```
Tag HTML `<form>``onsubmit` wywołuje funkcję `bingWebSearch`, aby zwrócić wyniki wyszukiwania. Funkcja `bingWebSearch` używa funkcji `getSubscriptionKey()` w celu uwierzytelnienia każdego zapytania. Jak pokazano w poprzedniej definicji, funkcja `getSubscriptionKey` monituje użytkownika o klucz, jeśli klucz nie został wprowadzony. Klucz jest następnie przechowywany w celu ciągłego używania przez aplikację.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Wybieranie opcji wyszukiwania
Na poniższej ilustracji przedstawiono pole tekstowe zapytania i opcje, które definiują wyszukiwanie wiadomości dotyczących finansowania szkoły.

![Opcje wyszukiwania wiadomości Bing](media/news-search-categories.png)

Formularz HTML zawiera elementy o następujących nazwach:

|Element|Opis|
|-|-|
| `where` | Menu rozwijane umożliwiające wybranie rynku (lokalizacji i języka) używanego na potrzeby wyszukiwania. |
| `query` | Pole tekstowe do wprowadzania terminów wyszukiwania. |
| `category` | Pola wyboru do promowania konkretnych typów wyników. Przykładowo promowanie zdrowia zwiększa ranking wiadomości związanych ze zdrowiem. |
| `when` | Menu rozwijane umożliwiające opcjonalne ograniczenie wyszukiwania do ostatniego dnia, tygodnia lub miesiąca. |
| `safe` | Pole wyboru wskazujące, czy użyć funkcji Bezpieczne wyszukiwanie Bing, aby odfiltrować wyniki zawierające „treści dla dorosłych”. |
| `count` | Ukryte pole. Liczba wyników wyszukiwania, jaka ma być zwrócona dla każdego żądania. Zmień, aby wyświetlić mniej lub więcej wyników na stronie. |
| `offset`|  Ukryte pole. Przesunięcie pierwszego wyniku wyszukiwania w żądaniu; używane na potrzeby stronicowania. Jest resetowane do wartości `0` dla nowego żądania. |

> [!NOTE]
> Wyszukiwanie w sieci Web Bing oferuje inne parametry zapytania. Używamy tylko kilku z nich.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Na przykład parametr `SafeSearch` w rzeczywistym wywołaniu interfejsu API może mieć wartość `strict`, `moderate`, lub `off`, przy czym `moderate` jest wartością domyślną. Jednak w naszym formularzu jest używane pole wyboru, które ma tylko dwa stany. Kod JavaScript konwertuje to ustawienie do wartości `strict` lub `off` (wartość `moderate` nie jest używana).

## <a name="performing-the-request"></a>Wykonywanie żądania
Mając podane zapytanie, ciąg opcji i klucz interfejsu API, funkcja `BingNewsSearch` używa obiektu `XMLHttpRequest`, aby wysłać żądanie do punktu końcowego wyszukiwania wiadomości Bing.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```
Po pomyślnym zakończeniu żądania HTTP język JavaScript wywołuje obsługę zdarzeń `load`, funkcję `handleBingResponse()`, aby obsłużyć pomyślne żądanie GET kodu HTTP do interfejsu API. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "News") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Pomyślne żądanie HTTP *nie* oznacza wcale, że samo wyszukiwanie zakończyło się powodzeniem. Jeśli w operacji wyszukiwania wystąpi błąd, interfejs API wyszukiwania wiadomości Bing zwraca kod stanu HTTP inny niż 200 i umieszcza informacje o błędzie w odpowiedzi JSON. Ponadto, jeśli żądanie podlegało ograniczeniu przepustowości, interfejs API zwraca pustą odpowiedź.

Większość kodu w obu poprzednich funkcjach jest przeznaczona do obsługi błędów. Błędy mogą wystąpić na następujących etapach:

|Etap|Potencjalne błędy|Obsługiwane przez|
|-|-|-|
|Tworzenie obiektu żądania języka JavaScript|Nieprawidłowy adres URL|Blok `try`/`catch`|
|Wykonywanie żądania|Błędy sieci, przerwane połączenia|Obsługa zdarzeń `error` i `abort`|
|Wykonywanie wyszukiwania|Nieprawidłowe żądanie, nieprawidłowy kod JSON, limity przepustowości|Testy obsługi zdarzeń `load`|

Błędy są obsługiwane przez wywołanie funkcji `renderErrorMessage()` z wszystkimi znanymi szczegółowymi informacjami o błędzie. Jeśli odpowiedź przejdzie pomyślnie pełen zestaw testów błędów, wywołujemy funkcję `renderSearchResults()`, aby wyświetlić wyniki wyszukiwania na stronie.

## <a name="displaying-search-results"></a>Wyświetlanie wyników wyszukiwania
Główna funkcja służąca do wyświetlania wyników wyszukiwania to `renderSearchResults()`. Ta funkcja przyjmuje obiekt JSON zwracany przez usługę wyszukiwania wiadomości Bing i renderuje wyniki wiadomości oraz powiązane wyszukiwania, jeśli istnieją.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
Główne wyniki wyszukiwania są zwracane jako obiekt `value` najwyższego poziomu w odpowiedzi JSON. Przekazujemy je do naszej funkcji `renderResults()`, która wykonuje na nich iterację oraz wywołuje oddzielną funkcję w celu renderowania każdego elementu na kod HTML. Wynikowy kod HTML jest zwracany do funkcji `renderSearchResults()`, gdzie jest wstawiany do podziału `results` na stronie.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
Interfejs API wyszukiwania wiadomości Bing zwraca maksymalnie cztery różne rodzaje powiązanych wyników, każdy we własnym obiekcie najwyższego poziomu. Są to:

|Relacja|Opis|
|-|-|
|`pivotSuggestions`|Zapytania, które zamieniają wyraz przestawny w oryginalnym wyszukiwaniu na inny. Jeśli na przykład wyszukujesz frazę „czerwone kwiaty”, wyrazem przestawnym może być „czerwone” i sugestia przestawna może brzmieć „żółte kwiaty”.|
|`queryExpansions`|Zapytania, które zawężają oryginalne wyszukiwanie, dodając więcej terminów. Jeśli na przykład wyszukujesz frazę „Microsoft Surface”, rozszerzeniem zapytania może być fraza „Microsoft Surface Pro”.|
|`relatedSearches`|Zapytania, które również zostały wprowadzone przez innych użytkowników, którzy wprowadzili oryginalne wyszukiwanie. Jeśli na przykład wyszukujesz frazę „Mount Rainier”, powiązanym wyszukiwaniem może być fraza „Mt. Saint Helens”.|
|`similarTerms`|Zapytania, które mają podobne znaczenie, co oryginalne wyszukiwanie. Jeśli na przykład wyszukujesz wyraz „szkoły”, podobnym terminem może być „edukacja”.|

Jak pokazaliśmy poprzednio dla funkcji `renderSearchResults()`, renderujemy tylko sugestie `relatedItems` i umieszczamy wynikowe linki na pasku bocznym strony.

## <a name="rendering-result-items"></a>Renderowanie elementów wyniku

W kodzie JavaScript obiekt `searchItemRenderers` zawiera *funkcje renderujące:* funkcje, które generują kod HTML dla każdego rodzaju wyniku wyszukiwania.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Funkcja renderująca może akceptować następujące parametry:

|Parametr|Opis|
|-|-|
|`item`| Obiekt JavaScript, który zawiera właściwości elementu, takie jak adres URL i opis.|
|`index`| Indeks elementu wyniku w ramach jego kolekcji.|
|`count`| Liczba elementów w kolekcji elementów wyników wyszukiwania.|

Parametry `index` i `count` mogą służyć do numerowania wyników, do generowania specjalnego kodu HTML wstawianego na początku lub końcu kolekcji, do wstawiania podziałów wiersza po określonej liczbie elementów i tak dalej. Jeśli funkcja renderująca nie wymaga takiej funkcjonalności, nie musi akceptować tych dwóch parametrów.

`news`Moduł renderowania jest przedstawiony w poniższym fragmencie kodu JavaScript:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Funkcja renderująca wiadomości:
> [!div class="checklist"]
> * tworzy tag akapitu, przypisuje go do klasy `news` i wypycha go do tablicy html;
> * oblicza rozmiar miniatury obrazu (szerokość jest stała i wynosi 60 pikseli, wysokość jest obliczana proporcjonalnie);
> * tworzy tag `<img>` kodu HTML, aby wyświetlić miniaturę obrazu; 
> * tworzy tagi `<a>` kodu HTML, które prowadzą do obrazu i do zawierającej go strony;
> * tworzy opis, który wyświetla informacje dotyczące obrazu i witryny, w której się znajduje.

Rozmiar miniatury jest używany zarówno w tagu `<img>`, jak i w polach `h` oraz `w` w adresie URL miniatury. Następnie [usługa miniatur Bing](../bing-web-search/resize-and-crop-thumbnails.md) dostarcza miniaturę dokładnie tego rozmiaru.

## <a name="persisting-client-id"></a>Trwały identyfikator klienta
Odpowiedzi z interfejsów API wyszukiwania Bing mogą zawierać nagłówek `X-MSEdge-ClientID`, który powinien być wysyłany z powrotem do interfejsu API z kolejnymi żądaniami. Jeśli jest używanych wiele interfejsów API wyszukiwania Bing, dla każdego z nich powinien być stosowany ten sam identyfikator klienta, jeśli jest to możliwe.

Podanie nagłówka `X-MSEdge-ClientID` umożliwia interfejsom API usługi Bing skojarzenie wszystkich wyszukiwań użytkownika, co ma dwie ważne korzyści.

Po pierwsze umożliwia aparatowi wyszukiwania Bing zastosowanie do wyszukiwań wcześniejszego kontekstu, co pozwala znaleźć bardziej satysfakcjonujące użytkownika wyniki. Jeśli na przykład użytkownik wcześniej wyszukiwał terminy związane z żeglowaniem, późniejsze wyszukiwanie terminu „węzły” może preferencyjnie zwrócić informacje na temat węzłów używanych w żeglarstwie.

Po drugie usługa Bing może losowo wybierać użytkowników, którzy będą korzystali z nowych funkcji, zanim zostaną one udostępnione publicznie. Podanie tego samego identyfikatora klienta z każdym żądaniem gwarantuje, że użytkownicy, którzy widzą tę funkcję, zawsze będą ją widzieć. Bez identyfikatora klienta użytkownik może widzieć, jak funkcja pojawia się i znika w wynikach wyszukiwania, pozornie losowo.

Zasady zabezpieczeń przeglądarki (CORS) mogą powodować, że nagłówek `X-MSEdge-ClientID` będzie niedostępny dla kodu JavaScript. To ograniczenie występuje, gdy odpowiedź wyszukiwania ma inne źródło niż strona, z której pochodzi żądanie. W środowisku produkcyjnym, aby rozwiązać ten problem, należy udostępnić skrypt po stronie serwera, który wykonuje wywołanie interfejsu API w tej samej domenie, co strona internetowa. Ponieważ skrypt ma to samo źródło co strona internetowa, nagłówek `X-MSEdge-ClientID` jest dostępny dla kodu JavaScript.

> [!NOTE]
> W aplikacji internetowej w środowisku produkcyjnym należy wykonać to żądanie po stronie serwera. W przeciwnym razie należy dołączyć klucz interfejsu API wyszukiwania Bing do strony internetowej, aby był on dostępny dla każdego, kto wyświetli źródło. Płacisz za wszystkie użycia związane z Twoim kluczem subskrypcji interfejsu API, nawet za żądania wykonane przez osoby nieupoważnione, zatem ważne jest, aby nie ujawniać swojego klucza.

W celach programistycznych możesz wykonywać żądania interfejsu API wyszukiwania w sieci Web Bing za pośrednictwem serwera proxy CORS. Odpowiedź z takiego serwera proxy ma `Access-Control-Expose-Headers` nagłówek, który umożliwia nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Zainstalowanie serwera proxy CORS w celu zezwolenia naszej aplikacji samouczka na dostęp do nagłówka identyfikatora klienta jest łatwe. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Następnie wykonaj następujące polecenie w oknie polecenia:

```console
npm install -g cors-proxy-server
```

Następnie zmień wyszukiwanie w sieci Web Bing punkt końcowy w pliku HTML na: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

```console
cors-proxy-server
```

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API wyszukiwania wiadomości Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)
