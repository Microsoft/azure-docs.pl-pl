---
title: 'Samouczek: tworzenie jednostronicowej aplikacji internetowej — interfejs API wyszukiwania obrazów Bing'
titleSuffix: Azure cognitive services
description: Interfejs API wyszukiwania obrazów Bing umożliwia wyszukiwanie w Internecie odpowiednich obrazów o wysokiej jakości. Przy użyciu tego samouczka możesz utworzyć jednostronicową aplikację internetową, która wysyła zapytania wyszukiwania do interfejsu API i wyświetla wyniki na stronie internetowej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: bcb82ffcf4c4ad861c6523f507c816c666a71772
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351388"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Samouczek: tworzenie aplikacji jednostronicowej przy użyciu interfejsu API wyszukiwania obrazów Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania obrazów Bing umożliwia wyszukiwanie w Internecie odpowiednich obrazów o wysokiej jakości. Przy użyciu tego samouczka możesz utworzyć jednostronicową aplikację internetową, która wysyła zapytania wyszukiwania do interfejsu API i wyświetla wyniki na stronie internetowej. Ten samouczek jest podobny do [odpowiadającego mu samouczka](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) dotyczącego wyszukiwania w sieci Web Bing.

Aplikacja samouczka ilustruje sposób wykonywania następujących czynności:

> [!div class="checklist"]
> * Wywołanie interfejsu API wyszukiwania obrazów Bing w języku JavaScript
> * Poprawianie wyników wyszukiwania za pomocą opcji wyszukiwania
> * Wyświetlanie wyników wyszukiwania i dzielenie ich na strony
> * Żądanie i obsługa klucza subskrypcji interfejsu API oraz identyfikatora klienta usługi Bing

Pełny kod źródłowy dla tego samouczka jest dostępny w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/).
* Struktura [Express.js](https://expressjs.com/) dla środowiska Node.js. Instrukcje instalacji kodu źródłowego są dostępne w pliku readme przykładu w serwisie GitHub.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Przechowywanie kluczy subskrypcji użytkownika i zarządzanie nimi

W tej aplikacji do przechowywania kluczy subskrypcji interfejsu API jest używany magazyn trwały przeglądarki internetowej. Jeśli żaden klucz nie jest przechowywany, strona internetowa wyświetli monit o podanie klucza użytkownika i zachowa go do późniejszego użytku. Jeśli klucz zostanie później odrzucony przez interfejs API, aplikacja usunie go z magazynu. Ten przykład używa globalnego punktu końcowego. Możesz również użyć niestandardowego punktu końcowego [domeny](../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu.


Zdefiniuj funkcje `storeValue` i `retrieveValue` tak, aby używały obiektu `localStorage` (jeśli przeglądarka go obsługuje) lub pliku cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

Funkcja `getSubscriptionKey()` spróbuje pobrać wcześniej przechowywany klucz za pomocą obiektu `retrieveValue`. Jeśli klucza nie będzie, funkcja wyświetli monit o podanie klucza użytkownika i przechowa go przy użyciu obiektu `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

Tag HTML `<form>``onsubmit` wywołuje funkcję `bingWebSearch`, aby zwrócić wyniki wyszukiwania. Funkcja `bingWebSearch` używa funkcji `getSubscriptionKey` w celu uwierzytelnienia każdego zapytania. Jak pokazano w poprzedniej definicji, funkcja `getSubscriptionKey` monituje użytkownika o klucz, jeśli klucz nie został wprowadzony. Klucz jest następnie przechowywany w celu ciągłego używania przez aplikację.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Wysyłanie żądań wyszukiwania

Ta aplikacja korzysta z kodu HTML `<form>`, aby początkowo wysyłać żądania wyszukiwania użytkowników, używając atrybutu `onsubmit` w celu wywołania funkcji `newBingImageSearch()`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Domyślnie procedura obsługi `onsubmit` zwraca wynik `false`, który zapobiega przesłaniu formularza.

## <a name="select-search-options"></a>Wybieranie opcji wyszukiwania

![[Formularz wyszukiwania obrazów Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Interfejs API wyszukiwania obrazów Bing oferuje kilka [parametrów w zapytaniu filtru](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters) w celu zawężenia i przefiltrowania wyników wyszukiwania. Formularz HTML w tej aplikacji używa i wyświetla następujące opcje parametrów:

| Opcja | Opis |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Menu rozwijane umożliwiające wybranie rynku (lokalizacji i języka) używanego na potrzeby wyszukiwania.                                                                                             |
| `query`      | Pole tekstowe do wprowadzania terminów wyszukiwania.                                                                                                                                 |
| `aspect`     | Przyciski radiowe do wybierania proporcji znalezionych obrazów: prawie kwadratowe, szerokie lub wąskie.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Menu rozwijane umożliwiające opcjonalne ograniczenie wyszukiwania do ostatniego dnia, tygodnia lub miesiąca.                                                                                          |
| `safe`       | Pole wyboru wskazujące, czy użyć funkcji Bezpieczne wyszukiwanie Bing, aby odfiltrować wyniki zawierające „treści dla dorosłych”.                                                                                      |
| `count`      | Ukryte pole. Liczba wyników wyszukiwania, jaka ma być zwrócona dla każdego żądania. Zmień, aby wyświetlić mniej lub więcej wyników na stronie.                                                            |
| `offset`     | Ukryte pole. Przesunięcie pierwszego wyniku wyszukiwania w żądaniu; używane na potrzeby stronicowania. Jest resetowane do wartości `0` dla nowego żądania.                                                           |
| `nextoffset` | Ukryte pole. Po otrzymaniu wyników wyszukiwania to pole jest ustawione na wartość `nextOffset` w odpowiedzi. Użycie tego pola pozwala uniknąć nakładających się wyników na kolejnych stronach. |
| `stack`      | Ukryte pole. Zakodowana w formacie JSON lista przesunięć poprzednich stron wyników wyszukiwania na potrzeby przechodzenia wstecz do poprzednich stron.                                                      |

Funkcja `bingSearchOptions()` formatuje te opcje jako częściowy ciąg zapytania, którego można użyć w żądaniach interfejsu API aplikacji.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>Wykonywanie żądania

Korzystając z zapytania wyszukiwania, ciągu opcji i klucza interfejsu API, funkcja `BingImageSearch()` używa obiektu XMLHttpRequest, aby wysłać żądanie do punktu końcowego wyszukiwania obrazów Bing.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

Po pomyślnym zakończeniu żądania HTTP język JavaScript wywołuje obsługę zdarzeń „load”, `handleBingResponse()`, aby obsłużyć pomyślne żądanie GET kodu HTTP.

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> Pomyślne żądania HTTP mogą zawierać informacje o wyszukiwaniu zakończonym niepowodzeniem. Jeśli podczas operacji wyszukiwania wystąpi błąd, interfejs API wyszukiwania obrazów Bing zwraca kod stanu HTTP inny niż 200 i umieszcza informacje o błędzie w odpowiedzi JSON. Ponadto, jeśli żądanie podlegało ograniczeniu przepustowości, interfejs API zwróci pustą odpowiedź.

## <a name="display-the-search-results"></a>Wyświetlanie wyników wyszukiwania

Wyniki wyszukiwania są wyświetlane przez funkcję `renderSearchResults()`, która pobiera odpowiedź JSON zwróconą przez usługę wyszukiwania obrazów Bing i wywołuje odpowiednią funkcję modułu renderowania na wszystkich zwróconych obrazach i powiązanych wyszukiwaniach.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Wyniki wyszukiwania obrazów są zawarte w obiekcie `value` najwyższego poziomu w odpowiedzi JSON. Są one przekazywane do funkcji `renderImageResults()`, która iteruje wyniki i konwertuje każdy element na kod HTML.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Interfejs API wyszukiwania obrazów Bing może zwrócić cztery rodzaje sugestii wyszukiwania, aby ułatwić użytkownikom wyszukiwanie — każda we własnym obiekcie najwyższego poziomu:

| Sugestia         | Opis                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Zapytania, które zamieniają wyraz przestawny w oryginalnym wyszukiwaniu na inny. Jeśli na przykład wyszukujesz frazę „czerwone kwiaty”, wyrazem przestawnym może być „czerwone” i sugestia przestawna może brzmieć „żółte kwiaty”. |
| `queryExpansions`  | Zapytania, które zawężają oryginalne wyszukiwanie, dodając więcej terminów. Jeśli na przykład wyszukujesz frazę „Microsoft Surface”, rozszerzeniem zapytania może być fraza „Microsoft Surface Pro”.                                   |
| `relatedSearches`  | Zapytania, które również zostały wprowadzone przez innych użytkowników, którzy wprowadzili oryginalne wyszukiwanie. Jeśli na przykład wyszukujesz frazę „Mount Rainier”, powiązanym wyszukiwaniem może być fraza „Mt. Saint Helens”.                       |
| `similarTerms`     | Zapytania, które mają podobne znaczenie, co oryginalne wyszukiwanie. Jeśli na przykład wyszukujesz wyraz „kotki”, podobnym terminem może być „milusie”.                                                                   |

Ta aplikacja renderuje tylko sugestie `relatedItems` i umieszcza wynikowe linki na pasku bocznym strony.

## <a name="rendering-search-results"></a>Renderowanie wyników wyszukiwania

W tej aplikacji obiekt `searchItemRenderers` zawiera funkcje modułu renderowania, które generują kod HTML dla każdego rodzaju wyniku wyszukiwania.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Te funkcje modułu renderowania akceptują następujące parametry:

| Parametr         | Opis                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | Obiekt JavaScript, który zawiera właściwości elementu, takie jak adres URL i opis. |
| `index` | Indeks elementu wyniku w ramach jego kolekcji.                                          |
| `count` | Liczba elementów w kolekcji elementów wyników wyszukiwania.                                  |

Parametry `index` i `count` są używane do numerowania wyników, generowania kodu HTML dla kolekcji i organizowania zawartości. W szczególności:

* oblicza rozmiar miniatury obrazu (szerokość jest różna i wynosi co najmniej 120 pikseli, a wysokość jest stała i wynosi 90 pikseli);
* tworzy tag `<img>` kodu HTML, aby wyświetlić miniaturę obrazu;
* tworzy tagi `<a>` kodu HTML, które prowadzą do obrazu i do zawierającej go strony;
* tworzy opis, który wyświetla informacje dotyczące obrazu i witryny, w której się znajduje.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Wymiary `height` i `width` obrazu miniatury są używane zarówno w tagu `<img>`, jak i w polach `h` oraz `w` w adresie URL miniatury. Dzięki temu usługa Bing może zwrócić [miniaturę](../bing-web-search/resize-and-crop-thumbnails.md) dokładnie tego rozmiaru.

## <a name="persisting-client-id"></a>Trwały identyfikator klienta

Odpowiedzi z interfejsów API wyszukiwania Bing mogą zawierać nagłówek `X-MSEdge-ClientID`, który powinien być wysyłany z powrotem do interfejsu API z kolejnymi żądaniami. Jeśli jest używanych wiele interfejsów API wyszukiwania Bing, dla każdego z nich powinien być stosowany ten sam identyfikator klienta, jeśli jest to możliwe.

Podanie nagłówka `X-MSEdge-ClientID` umożliwia interfejsom API usługi Bing skojarzenie wszystkich wyszukiwań użytkownika, co jest użyteczne z następujących względów.

Po pierwsze umożliwia aparatowi wyszukiwania Bing zastosowanie do wyszukiwań wcześniejszego kontekstu, co pozwala znaleźć bardziej satysfakcjonujące użytkownika wyniki. Jeśli na przykład użytkownik wcześniej wyszukiwał terminy związane z żeglowaniem, późniejsze wyszukiwanie terminu „węzły” może preferencyjnie zwrócić informacje na temat węzłów używanych w żeglarstwie.

Po drugie usługa Bing może losowo wybierać użytkowników, którzy będą korzystali z nowych funkcji, zanim zostaną one udostępnione publicznie. Podanie tego samego identyfikatora klienta z każdym żądaniem gwarantuje, że użytkownicy, którzy widzą tę funkcję, zawsze będą ją widzieć. Bez identyfikatora klienta użytkownik może widzieć, jak funkcja pojawia się i znika w wynikach wyszukiwania, pozornie losowo.

Zasady zabezpieczeń przeglądarki (CORS) mogą powodować, że nagłówek `X-MSEdge-ClientID` będzie niedostępny dla kodu JavaScript. To ograniczenie występuje, gdy odpowiedź wyszukiwania ma inne źródło niż strona, z której pochodzi żądanie. W środowisku produkcyjnym, aby rozwiązać ten problem, należy udostępnić skrypt po stronie serwera, który wykonuje wywołanie interfejsu API w tej samej domenie, co strona internetowa. Ponieważ skrypt ma to samo źródło co strona internetowa, nagłówek `X-MSEdge-ClientID` jest dostępny dla kodu JavaScript.

> [!NOTE]
> W aplikacji internetowej w środowisku produkcyjnym należy mimo to wykonać to żądanie po stronie serwera. W przeciwnym razie należy dołączyć klucz interfejsu API wyszukiwania Bing do strony internetowej, aby był on dostępny dla każdego, kto wyświetli źródło. Płacisz za wszystkie użycia związane z Twoim kluczem subskrypcji interfejsu API, nawet za żądania wykonane przez osoby nieupoważnione, zatem ważne jest, aby nie ujawniać swojego klucza.

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
> [Wyodrębnianie szczegółów obrazu przy użyciu interfejsu API wyszukiwania obrazów Bing](tutorial-image-post.md)

## <a name="see-also"></a>Zobacz też

* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)