---
title: 'Samouczek: tworzenie jednostronicowej aplikacji internetowej — interfejs AP do wyszukiwania w sieci Web Bing'
titleSuffix: Azure Cognitive Services
description: Ta aplikacja jednostronicowa pokazuje, jak można używać interfejsu API wyszukiwania w sieci Web Bing do pobierania, analizowania i wyświetlania odpowiednich wyników w aplikacji jednostronicowej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 3c404f0028705754a68be113a331132eec02f7cb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304036"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Samouczek: tworzenie aplikacji jednostronicowej przy użyciu interfejsu API wyszukiwania w sieci Web Bing

Ta aplikacja jednostronicowa pokazuje, jak pobierać, analizować i wyświetlać wyniki wyszukiwania z interfejsu API wyszukiwania w sieci Web Bing. W samouczku użyto standardowego kodu HTML i CSS i skoncentrowano się na kodzie JavaScript. Pliki w formatach HTML, CSS i JS są dostępne w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) razem z instrukcjami przewodnika Szybki start.

Ta przykładowa aplikacja może wykonywać następujące czynności:

> [!div class="checklist"]
> * Wywoływanie interfejsu API wyszukiwania w sieci Web Bing z opcjami wyszukiwania
> * Wyświetlanie wyników wyszukiwania wideo, w sieci Web, w obrazach i w wiadomościach
> * Stronicowanie wyników
> * Zarządzanie kluczami subskrypcji
> * Obsługa błędów

Do używania tej aplikacji wymagane jest konto w usłudze [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsami API wyszukiwania Bing.

## <a name="prerequisites"></a>Wymagania wstępne

Składniki wymagane do uruchomienia aplikacji:

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Utwórz zasób wyszukiwanie Bing "  target="_blank"> utwórz zasób Wyszukiwanie Bing <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.

* Środowisko Node.js 8 lub nowsze

Pierwszą czynnością jest sklonowanie repozytorium z kodem źródłowym przykładowej aplikacji.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Następnie należy uruchomić polecenie `npm install`. W tym samouczku Express.js jest jedyną zależnością.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>Składniki aplikacji

Przykładowa aplikacja, którą tworzymy, składa się z czterech części:

* `bing-web-search.js` — aplikacja Express.js. Obsługuje logikę i routing żądania/odpowiedzi.
* `public/index.html` — szkielet aplikacji; określa sposób prezentowania danych użytkownikowi.
* `public/css/styles.css` — określa style stron, takie jak czcionki, kolory, rozmiar tekstu.
* `public/js/scripts.js` — zawiera logikę wysyłania żądań do interfejsu API wyszukiwania w sieci Web Bing, zarządzania kluczami subskrypcji, obsługi i analizy odpowiedzi oraz wyświetlania wyników.

W tym samouczku skoncentrowano się na `scripts.js` i logice wymaganych do wywoływania interfejsu API wyszukiwania w sieci Web Bing i obsługi odpowiedzi.

## <a name="html-form"></a>Formularz HTML

`index.html` zawiera formularz, który umożliwia użytkownikom wyszukiwanie i wybieranie opcji wyszukiwania. Atrybut `onsubmit` jest wyzwalany po przesłaniu formularza i wywołuje metodę `bingWebSearch()` zdefiniowaną w `scripts.js`. Ma ona trzy argumenty:

* Zapytanie wyszukiwania
* Wybrane opcje
* Klucz subskrypcji

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Opcje zapytań

Formularz HTML zawiera opcje, które mapują do parametrów zapytań w [interfejsie API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters). W tej tabeli przedstawiono poszczególne czynności, które użytkownicy mogą wykonać w celu filtrowania wyników wyszukiwania przy użyciu przykładowej aplikacji:

| Parametr | Opis |
|-----------|-------------|
| `query` | Pole tekstowe do wprowadzenia ciągu zapytania. |
| `where` | Menu rozwijane do wybrania rynku (lokalizacji i języka). |
| `what` | Pola wyboru do promowania określonych typów wyników. Na przykład promowanie obrazów zwiększa pozycjonowanie obrazów w wynikach wyszukiwania. |
| `when` | Menu rozwijane, które umożliwia użytkownikowi ograniczenie wyników wyszukiwania do dzisiaj, tego tygodnia lub tego miesiąca. |
| `safe` | Pole wyboru do włączenia bezpiecznego wyszukiwania w usłudze Bing, które pozwala na filtrowanie treści dla dorosłych. |
| `count` | Ukryte pole. Liczba wyników wyszukiwania, jaka ma być zwrócona dla każdego żądania. Zmień tę wartość, aby wyświetlić mniej lub więcej wyników na stronie. |
| `offset` | Ukryte pole. Przesunięcie pierwszego wyniku wyszukiwania w żądaniu używane na potrzeby stronicowania. Jest resetowane do wartości `0` dla każdego nowego żądania. |

> [!NOTE]
> Interfejs API wyszukiwania w sieci Web Bing oferuje dodatkowe parametry zapytania, które mogą pomóc uściślić wyniki wyszukiwania. W tym przykładzie użyto tylko kilku. Aby uzyskać pełną listę dostępnych parametrów, zobacz [informacje dotyczące interfejsu API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters).

Funkcja `bingSearchOptions()` konwertuje te opcje, aby dostosować je do formatu wymaganego przez interfejs API wyszukiwania Bing.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Opcja `SafeSearch` może być ustawiona na `strict`, `moderate` lub `off`, gdzie `moderate` będzie ustawieniem domyślnym dla interfejsu API wyszukiwania w sieci Web Bing. Ten formularz używa pola wyboru, które ma dwa stany: `strict` lub `moderate` .

Jeśli zaznaczono którekolwiek pole wyboru **Podwyższ poziom**, parametr `answerCount` zostanie dodany do zapytania. Parametr `answerCount` jest wymagany w przypadku korzystania z parametru `promote`. W tym fragmencie kodu wartość została ustawiona na `9`, aby zwracać wszystkie dostępne typy wyników.
> [!NOTE]
> Podwyższenie poziomu typu wyniku nie *gwarantuje*, że zostanie on uwzględniony w wynikach wyszukiwania. Przeciwnie, podwyższenie poziomu podwyższa klasyfikację wyników tego typu względem ich klasyfikacji typowej. Aby ograniczyć wyszukiwanie do wyników określonego typu, użyj parametru zapytania `responseFilter` lub wywołaj bardziej konkretny punkt końcowy, taki jak interfejs API wyszukiwania obrazów Bing lub interfejs API wyszukiwania wiadomości Bing.

Parametry zapytania `textDecoration` i `textFormat` zostały zapisane na stałe w skrypcie i powodują, że termin wyszukiwania będzie pogrubiony w wynikach wyszukiwania. Te parametry nie są wymagane.

## <a name="manage-subscription-keys"></a>Zarządzanie kluczami subskrypcji

Aby uniknąć trwałego kodowania klucza subskrypcji interfejsu API wyszukiwania Bing, ta przykładowa aplikacja przechowuje klucz subskrypcji w magazynie trwałym przeglądarki. Jeśli żaden klucz subskrypcji nie jest przechowywany, zostanie wyświetlone powiadomienie o konieczności jego wprowadzenia. Jeśli klucz subskrypcji zostanie odrzucony przez interfejs API, wyświetli się powiadomienie o konieczności ponownego wprowadzenia klucza subskrypcji.

Funkcja `getSubscriptionKey()` do przechowywania i pobierania klucza subskrypcji użytkownika używa funkcji `storeValue` i `retrieveValue`. Te funkcje używają obiektu `localStorage`, jeśli jest obsługiwany, lub plików cookie.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Jak opisano wcześniej, podczas przesyłania formularza zostaje wyzwolony parametr `onsubmit`, wywołując `bingWebSearch`. Ta funkcja inicjuje i wysyła żądanie. Funkcja `getSubscriptionKey` jest wywoływana po każdym przesłaniu w celu uwierzytelnienia żądania.

## <a name="call-bing-web-search"></a>Wywoływanie interfejsu API wyszukiwania w sieci Web Bing

Na podstawie zapytania, ciągu opcji i klucza subskrypcji funkcja `BingWebSearch` tworzy obiekt `XMLHttpRequest`, aby wywołać punkt końcowy wyszukiwania w sieci Web Bing.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Po wysłaniu żądania zakończonego powodzeniem obsługa zdarzeń `load` wyzwala i wywołuje funkcję `handleBingResponse`. `handleBingResponse` analizuje obiekt wyników i wyświetla wyniki, a także zawiera logikę błędów dla żądań zakończonych niepowodzeniem.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Żądanie HTTP zakończone powodzeniem *nie* oznacza, że samo wyszukiwanie zakończyło się powodzeniem. Jeśli w operacji wyszukiwania wystąpi błąd, interfejs API wyszukiwania w sieci Web Bing zwraca kod stanu HTTP inny niż 200 i umieszcza informacje o błędzie w odpowiedzi JSON. Jeśli żądanie podlegało ograniczeniu przepustowości, interfejs API zwraca pustą odpowiedź.

Większość kodu w obu poprzednich funkcjach jest przeznaczona do obsługi błędów. Błędy mogą wystąpić na następujących etapach:

| Etap | Potencjalne błędy | Obsługiwane przez |
|-------|--------------------|------------|
| Tworzenie obiektu żądania | Nieprawidłowy adres URL | Blok `try` / `catch` |
| Wykonywanie żądania | Błędy sieci, przerwane połączenia | Obsługa zdarzeń `error` i `abort` |
| Wykonywanie wyszukiwania | Nieprawidłowe żądanie, nieprawidłowy kod JSON, limity przepustowości | Testy w obsłudze zdarzeń `load` |

Błędy są obsługiwane przez wywołanie metody `renderErrorMessage()`. Jeśli odpowiedź przejdzie wszystkie testy błędów, metoda `renderSearchResults()` zostanie wywołana do wyświetlenia wyników wyszukiwania.

## <a name="display-search-results"></a>Wyświetlanie wyników wyszukiwania

Dla wyników zwracanych przez interfejs API wyszukiwania w sieci Web Bing istnieją [wymagania dotyczące użycia i wyświetlania](useanddisplayrequirements.md). Ze względu na to, że odpowiedź może zawierać wyniki różnego typu, nie wystarczy wykonać iteracji w kolekcji `WebPages` najwyższego poziomu. Zamiast tego przykładowa aplikacja używa `RankingResponse`, aby uporządkować wyniki do specyfikacji.

> [!NOTE]
> Jeśli potrzebujesz wyników tylko jednego typu, użyj parametru zapytania `responseFilter` lub rozważ użycie jednego z pozostałych punktów końcowych interfejsu API wyszukiwania Bing, np. interfejsu API wyszukiwania obrazów Bing.

Każda odpowiedź ma obiekt `RankingResponse`, który może zawierać maksymalnie trzy kolekcje: `pole`, `mainline` i `sidebar`. Kolekcja `pole`, jeśli występuje, jest najbardziej istotnym wynikiem wyszukiwania i musi być dobrze widoczna. Kolekcja `mainline` zawiera większość wyników wyszukiwania i jest wyświetlana bezpośrednio po `pole`. `sidebar` obejmuje pomocnicze wyniki wyszukiwania. Jeśli to możliwe, wyniki te powinny być wyświetlane na pasku bocznym. Jeśli z powodu ograniczeń ekranu pasek boczny jest niepraktyczny, wyniki te powinny zostać wyświetlone po wynikach `mainline`.

Każdy obiekt `RankingResponse` zawiera tablicę `RankingItem`, która określa, jak muszą być uporządkowane wyniki. Ta przykładowa aplikacja używa do zidentyfikowania wyniku parametrów `answerType` i `resultIndex`.

> [!NOTE]
> Istnieją inne sposoby identyfikacji i klasyfikacji wyników. Więcej informacji zawiera temat [How to use ranking to display Bing Web Search API results](rank-results.md) (Jak używać klasyfikacji do wyświetlania wyników interfejsu API wyszukiwania w sieci Web Bing).

Spójrzmy na kod:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

Funkcja `renderResultsItems()` wykonuje iterację na elementach w każdej kolekcji `RankingResponse`, mapuje wszystkie wyniki klasyfikacji do wyniku wyszukiwania przy użyciu wartości `answerType` i `resultIndex` oraz wywołuje odpowiednią funkcję renderowania, aby wygenerować kod HTML. Jeśli wartość `resultIndex` nie jest określona dla elementu, funkcja `renderResultsItems()` wykonuje iterację we wszystkich wynikach tego typu i wywołuje funkcję renderowania dla każdego elementu. Wynikowy kod HTML zostaje wstawiony do odpowiedniego elementu `<div>` w `index.html`.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>Przegląd funkcji programów renderujących

W naszej przykładowej aplikacji obiekt `searchItemRenderers` zawiera funkcje, które generują kod HTML dla wszystkich typów wyników wyszukiwania.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> Przykładowa aplikacja zawiera programy renderujące do stron sieci Web, wiadomości, obrazów, wideo i powiązanych wyszukiwań. Twoja aplikacja będzie wymagać programów renderujących do dowolnego typu wyników, które mogą zostać wyświetlone, takich jak obliczenia, sugestie dotyczące pisowni, jednostki, strefy czasowe i definicje.

Niektóre funkcje renderowania akceptują tylko parametr `item`. Inne akceptują dodatkowe parametry, które mogą służyć do renderowania elementów w inny sposób w zależności od kontekstu. Program renderujący, który nie używa tych informacji, nie musi akceptować tych parametrów.

Argumenty kontekstu to:

| Parametr  | Opis |
|------------|-------------|
| `section` | Sekcja wyników (`pole`, `mainline` lub `sidebar`), w której zostaje wyświetlony element. |
| `index`<br>`count` | Dostępny, gdy element `RankingResponse` określa, że powinny być wyświetlane wszystkie wyniki w danej kolekcji; `undefined` w pozostałych przypadkach. Indeks elementu w kolekcji i łączna liczba elementów w tej kolekcji. Tych informacji można użyć do ponumerowania wyników, wygenerowania innego kodu HTML dla pierwszego lub ostatniego wyniku itd. |

W przykładowej aplikacji oba programy renderujące `images` i `relatedSearches` używają argumentów kontekstu, aby dostosować wygenerowany kod HTML. Przyjrzyjmy się bliżej programowi renderującemu `images`:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

Program renderujący do obrazów:

* oblicza rozmiar miniatury obrazu (szerokość jest zmienna, a wysokość stała i wynosi 60 pikseli);
* wstawia kod HTML, który poprzedza wynik obrazów w zależności od kontekstu;
* tworzy tag `<a>` kodu HTML prowadzący do strony, która zawiera obraz;
* tworzy tag `<img>` kodu HTML, aby wyświetlić miniaturę obrazu;

Program renderujący do obrazów używa zmiennych `section` i `index` do wyświetlania wyników w różny sposób w zależności od tego, gdzie występują. Podział wiersza (tag `<br>`) jest wstawiany między wynikami obrazów na pasku bocznym, na którym zostaje wyświetlona kolumna obrazów. W pozostałych sekcjach pierwszy wynik obrazów `(index === 0)` jest poprzedzony tagiem `<p>`.

Rozmiar miniatury jest używany zarówno w tagu `<img>`, jak i w polach `h` oraz `w` w adresie URL miniatury. Atrybuty `title` i `alt` (tekstowy opis obrazu) są tworzone na podstawie nazwy i nazwy hosta obrazu w adresie URL.

Poniżej przedstawiono jeden ze sposobów wyświetlania obrazów w przykładowej aplikacji:

![[wyniki obrazów Bing]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Utrwalanie identyfikatora klienta

Odpowiedzi z interfejsów API wyszukiwania Bing mogą zawierać nagłówek `X-MSEdge-ClientID`, który powinien być wysyłany z powrotem do interfejsu API z każdym kolejnym żądaniem. Jeśli aplikacja używa więcej niż jednego interfejsu API wyszukiwania Bing, upewnij się, że z każdym żądaniem w ramach usług jest wysyłany ten sam identyfikator klienta.

Podanie nagłówka `X-MSEdge-ClientID` umożliwia interfejsom API usługi Bing skojarzenie wyszukiwań użytkownika. Po pierwsze umożliwia aparatowi wyszukiwania Bing zastosowanie do wyszukiwań wcześniejszego kontekstu i znalezienie wyników lepiej dopasowanych do żądania. Jeśli na przykład użytkownik wcześniej wyszukiwał terminy związane z żeglowaniem, późniejsze wyszukiwanie terminu „węzły” może preferencyjnie zwrócić informacje na temat węzłów używanych w żeglarstwie. Po drugie usługa Bing może losowo wybierać użytkowników, którzy będą korzystali z nowych funkcji, zanim zostaną one udostępnione publicznie. Podanie tego samego identyfikatora klienta w każdym żądaniu gwarantuje, że użytkownicy, którzy zostali wybrani do przeglądania wyników, zawsze będą je widzieć. Bez identyfikatora klienta użytkownik może widzieć, jak funkcja pojawia się i znika w wynikach wyszukiwania, pozornie losowo.

Zasady zabezpieczeń przeglądarki, takie jak współużytkowanie zasobów między źródłami (CORS), mogą uniemożliwić przykładowej aplikacji uzyskanie dostępu do nagłówka `X-MSEdge-ClientID`. To ograniczenie występuje, gdy odpowiedź wyszukiwania ma inne źródło niż strona, z której pochodzi żądanie. W środowisku produkcyjnym, aby rozwiązać ten problem, należy udostępnić skrypt po stronie serwera, który wykonuje wywołanie interfejsu API w tej samej domenie, co strona internetowa. Ponieważ skrypt ma to samo źródło co strona internetowa, nagłówek `X-MSEdge-ClientID` jest dostępny dla kodu JavaScript.

> [!NOTE]
> W aplikacji internetowej w środowisku produkcyjnym należy mimo to wykonać to żądanie po stronie serwera. W przeciwnym razie należy dołączyć klucz subskrypcji interfejsu API wyszukiwania Bing do strony sieci Web, aby był dostępny dla każdego, kto wyświetli źródło. Płacisz za wszystkie użycia związane z Twoim kluczem subskrypcji interfejsu API, nawet za żądania wykonane przez osoby nieupoważnione, zatem ważne jest, aby nie ujawniać swojego klucza.

W celach programistycznych możesz wykonywać żądania za pośrednictwem serwera proxy CORS. Odpowiedź z tego typu serwera proxy ma `Access-Control-Expose-Headers` nagłówek, który filtruje nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Zainstalowanie serwera proxy CORS w celu zezwolenia naszej przykładowej aplikacji na dostęp do nagłówka identyfikatora klienta jest proste. Uruchom następujące polecenie:

```console
npm install -g cors-proxy-server
```

Następnie zmień punkt końcowy wyszukiwania w sieci Web Bing w pliku `script.js` na:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Uruchom serwer proxy mechanizmu CORS za pomocą następującego polecenia:

```console
cors-proxy-server
```

Podczas korzystania z przykładowej aplikacji pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W sekcji rozwijanej nagłówków HTTP poniżej wyników wyszukiwania powinien być widoczny nagłówek `X-MSEdge-ClientID`. Sprawdź, czy jest taki sam dla każdego żądania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API wyszukiwania w sieci Web Bing w wersji 7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
