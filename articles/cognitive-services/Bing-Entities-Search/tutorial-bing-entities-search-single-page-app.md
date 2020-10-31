---
title: 'Samouczek: jednostronicowa aplikacja internetowa korzystająca z wyszukiwania jednostek Bing'
titleSuffix: Azure Cognitive Services
description: W tym samouczku pokazano, jak używać interfejs API wyszukiwania jednostek Bing w jednostronicowej aplikacji sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 7fa09ee40ca646be6ee104c7b2d4428c92934337
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084633"
---
# <a name="tutorial-single-page-web-app"></a>Samouczek: jednostronicowa aplikacja internetowa

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania jednostek Bing umożliwia wyszukiwanie w Internecie informacji o *jednostkach* i *miejscach.* W danym zapytaniu można zażądać dowolnego z tych dwóch rodzajów wyników lub obu. Poniżej znajdują się definicje miejsc i jednostek.

| Wynik | Opis |
|-|-|
|Jednostki|Znane osoby, miejsca i rzeczy, które można znaleźć po nazwie.|
|Składa|Restauracje, hotele i inne lokalne firmy, które można znaleźć po nazwie *lub* typie (włoskie restauracje).|

W tym samouczku utworzymy jednostronicową aplikację internetową, która wyświetla wyniki wyszukiwania bezpośrednio na stronie przy użyciu interfejsu API wyszukiwania jednostek Bing. Aplikacja zawiera składniki HTML, CSS i JavaScript.

Interfejs API umożliwia określanie priorytetu wyników według lokalizacji. W aplikacji mobilnej możesz zapytać urządzenie o jego lokalizację. W aplikacji internetowej możesz użyć funkcji `getPosition()`. Jednak to wywołanie działa tylko w bezpiecznych kontekstach i może nie podawać dokładnej lokalizacji. Ponadto użytkownik może chcieć wyszukiwać jednostki w lokalizacji innej niż własna.

Dlatego nasza aplikacja wywołuje usługę Mapy Bing w celu uzyskania szerokości i długości geograficznej lokalizacji wprowadzonej przez użytkownika. Użytkownik może następnie wprowadzić nazwę charakterystycznego elementu krajobrazu („Pałac Kultury”) lub pełny bądź częściowy adres („Nowy Jork”), a interfejs API usługi Mapy Bing poda współrzędne.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Po kliknięciu nagłówków JSON i HTTP w dolnej części strony zostanie wyświetlona odpowiedź JSON i informacje o żądaniu HTTP. Informacje te są przydatne podczas eksplorowania usługi.

Aplikacja samouczka ilustruje sposób wykonywania następujących czynności:

> [!div class="checklist"]
> * Wywoływanie interfejsu API wyszukiwania jednostek Bing w języku JavaScript
> * Wykonywanie wywołania `locationQuery` interfejsu API usługi Mapy Bing w języku JavaScript
> * Przekazywanie opcji wyszukiwania do wywołań interfejsu API
> * Wyświetlanie wyników wyszukiwania
> * Obsługa identyfikatora klienta i klucza subskrypcji interfejsu API usługi Bing
> * Obsługa błędów, które mogą wystąpić

Strona samouczka jest całkowicie niezależna. Nie używa żadnych zewnętrznych struktur, arkuszy stylów ani nawet plików obrazów. Korzysta jedynie z powszechnie obsługiwanych funkcji języka JavaScript i działa w aktualnych wersjach wszystkich popularnych przeglądarek internetowych.

W tym samouczku omówimy tylko wybrane części kodu źródłowego. Pełny kod źródłowy jest dostępny [na oddzielnej stronie](tutorial-bing-entities-search-single-page-app-source.md). Skopiuj ten kod i wklej go do edytora tekstu, a następnie zapisz go jako plik `bing.html`.

> [!NOTE]
> Ten samouczek jest bardzo podobny do [samouczka dotyczącego jednostronicowej aplikacji wyszukiwania w Internecie Bing](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), ale dotyczy tylko wyników wyszukiwania jednostek.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w samouczku, musisz dysponować kluczami subskrypcji dla interfejsu API Wyszukiwanie Bing i interfejsu API Maps usługi Bing. 

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure:
  * <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Utwórz zasób Wyszukiwanie Bing "  target="_blank"> Utwórz zasób wyszukiwanie Bing <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu** .
  * <a href="https://www.microsoft.com/maps/create-a-bing-maps-key.aspx"  title="Przetwarzanie obrazów Utwórz zasób usługi "  target="_blank"> mapy Bing <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu** .

## <a name="app-components"></a>Składniki aplikacji

Podobnie jak każda inna aplikacja internetowa, aplikacja w tym samouczku zawiera trzy części:

> [!div class="checklist"]
> * HTML — definiuje strukturę i zawartość strony
> * CSS — definiuje wygląd strony
> * JavaScript — określa zachowanie strony

Ten samouczek nie obejmuje szczegółowej prezentacji większości kodu HTML lub CSS, ponieważ jest on dosyć prosty.

Część HTML zawiera formularz wyszukiwania, w którym użytkownik wprowadza zapytanie i wybiera opcje wyszukiwania. Formularz jest połączony z kodem JavaScript, który wykonuje wyszukiwanie przy użyciu atrybutu `onsubmit` tagu `<form>`:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Procedura obsługi `onsubmit` zwraca wynik `false`, który zapobiega przesłaniu formularza na serwer. Kod JavaScript zbiera wymagane informacje z formularza i wykonuje wyszukiwanie.

Wyszukiwanie jest wykonywane w dwóch fazach. Najpierw, jeśli użytkownik wprowadził ograniczenie lokalizacji, wykonywane jest zapytanie usługi Mapy Bing w celu jego przekonwertowania na współrzędne. Wywołanie dla tego zapytania uruchamia następnie zapytanie wyszukiwania jednostek Bing.

Kod HTML zawiera także podziały (tagi `<div>`) tam, gdzie są wyświetlane wyniki wyszukiwania.

## <a name="managing-subscription-keys"></a>Zarządzanie kluczami subskrypcji

> [!NOTE]
> Ta aplikacja wymaga kluczy subskrypcji dla interfejsu API wyszukiwania Bing oraz interfejsu API usługi Mapy Bing.

Aby uniknąć konieczności umieszczania kluczy subskrypcji interfejsu API wyszukiwania Bing oraz interfejsu API usługi Mapy Bing w kodzie, używamy magazynu trwałego przeglądarki do przechowywania klucza. Jeśli żaden klucz nie został zapisany, wyświetlamy monit o jego wprowadzenie i zapisujemy go do późniejszego użycia. W przypadku późniejszego odrzucenia klucza przez interfejs API unieważniamy przechowywany klucz, aby użytkownikowi został ponownie wyświetlony monit podczas następnego wyszukiwania.

Definiujemy funkcje `storeValue` i `retrieveValue`, które używają obiektu `localStorage` (jeśli przeglądarka go obsługuje) lub pliku cookie. Nasza funkcja `getSubscriptionKey()` używa tych funkcji do przechowywania i pobierania klucza użytkownika. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

Tag HTML `<body>` zawiera atrybut `onload`, który wywołuje funkcje `getSearchSubscriptionKey()` i `getMapsSubscriptionKey()` po zakończeniu ładowania strony. Te wywołania natychmiast wyświetlają monit o wprowadzenie kluczy przez użytkownika, jeśli nie zostały jeszcze wprowadzone.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Wybieranie opcji wyszukiwania

![[Formularz wyszukiwania jednostek Bing]](media/entity-search-spa-form.png)

Ten formularz HTML zawiera następujące kontrolki:

| Kontrola | Opis |
|-|-|
|`where`|Menu rozwijane umożliwiające wybranie rynku (lokalizacji i języka) używanego na potrzeby wyszukiwania.|
|`query`|Pole tekstowe do wprowadzania terminów wyszukiwania.|
|`safe`|Pole wyboru wskazujące, czy funkcja Bezpieczne wyszukiwanie jest włączona (wyklucza ze zwracanych wyników treści „dla dorosłych”).|
|`what`|Menu wyboru wyszukiwania jednostek, miejsc lub obu typów wyników.|
|`mapquery`|Pole tekstowe, w którym użytkownik może wprowadzić pełny lub częściowy adres, nazwę charakterystycznego elementu krajobrazu itd., aby ułatwić zwracanie odpowiedniejszych wyników przez usługę wyszukiwania jednostek Bing.|

> [!NOTE]
> Wyniki wyszukiwania miejsc są obecnie dostępne tylko w Stanach Zjednoczonych. Menu `where` i `what` zawierają kod służący do wymuszania tego ograniczenia. W przypadku wybrania rynku innego niż USA, gdy wybrana jest opcja Places (Miejsca) w menu `what`, pozycja menu `what` zostanie zmieniona na Anything (Wszystko). W przypadku wybrania opcji Places (Miejsca), gdy wybrany jest rynek inny niż USA w menu `where`, pozycja menu `where` zostanie zmieniona na USA.

Nasza funkcja JavaScript `bingSearchOptions()` konwertuje te pola na ciąg zapytania częściowego interfejsu API wyszukiwania Bing.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Na przykład funkcja Bezpieczne wyszukiwanie może mieć wartości `strict`, `moderate` lub `off`, z których wartość `moderate` jest ustawiona domyślnie. Jednak w naszym formularzu jest używane pole wyboru, które ma tylko dwa stany. Kod JavaScript konwertuje to ustawienie do wartości `strict` lub `off` (wartość `moderate` nie jest używana).

Pole `mapquery` nie jest obsługiwane przez funkcję `bingSearchOptions()`, ponieważ jest używane w zapytaniu lokalizacji usługi Mapy Bing, a nie wyszukiwania jednostek Bing.

## <a name="obtaining-a-location"></a>Uzyskiwanie lokalizacji

Interfejs API usługi mapy Bing oferuje [ `locationQuery` metodę](//msdn.microsoft.com/library/ff701711.aspx), która służy do znajdowania szerokości i długości geograficznej lokalizacji, którą wprowadzi użytkownik. Te współrzędne są następnie przekazywane do interfejsu API wyszukiwania jednostek Bing z żądaniem użytkownika. W wynikach wyszukiwania priorytet mają jednostki i miejsca znajdujące się blisko określonej lokalizacji.

Nie można uzyskać dostępu do interfejsu API usługi Mapy Bing przy użyciu zwykłego zapytania `XMLHttpRequest` w aplikacji internetowej, ponieważ ta usługa nie obsługuje zapytań między źródłami. Na szczęście usługa obsługuje dane JSONP („P” oznacza uzupełnione; ang. „padded”). Odpowiedź JSONP jest standardową odpowiedzią JSON opakowaną w wywołanie funkcji. Żądanie jest wykonywane przez wstawienie w dokumencie przy użyciu tagu `<script>`. (Ładowanie skryptów nie podlega zasadom zabezpieczeń przeglądarki).

Funkcja `bingMapsLocate()` tworzy i wstawia tag `<script>` zapytania. Segment `jsonp=bingMapsCallback` ciągu zapytania określa nazwę funkcji, która ma być wywoływana z odpowiedzią.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Jeśli interfejs API usługi Mapy Bing nie odpowie, funkcja `bingMapsCallBack()` nie zostanie nigdy wywołana. Zwykle oznacza to, że funkcja `bingEntitySearch()` nie zostanie wywołana, a wyniki wyszukiwania jednostek nie zostaną wyświetlone. Aby temu zapobiec, funkcja `bingMapsLocate()` ustawia również czasomierz w celu wywołania funkcji `bingEntitySearch()` po upływie pięciu sekund. W funkcji wywołania zwrotnego istnieje logika zapobiegająca dwukrotnemu wykonaniu wyszukiwania jednostek.

Po wykonaniu zapytania funkcja `bingMapsCallback()` jest wywoływana zgodnie z żądaniem.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Oprócz szerokości i długości geograficznej zapytanie wyszukiwania jednostek Bing wymaga wartości *promienia* , która oznacza dokładność informacji o lokalizacji. Promień jest obliczany przy użyciu wartości *pola ograniczenia* podanej w odpowiedzi usługi Mapy Bing. Pole ograniczenia jest prostokątem, który otacza całą lokalizację. Jeśli na przykład użytkownik wprowadzi ciąg `NYC`, wynik zawiera przybliżone współrzędne centrum Nowego Jorku oraz pole ograniczenia obejmujące miasto. 

Najpierw obliczamy odległości od głównych współrzędnych do każdego z czterech rogów pola ograniczenia przy użyciu funkcji `haversineDistance()` (nie została pokazana). Używamy największej z tych czterech odległości jako promienia. Minimalna wartość promienia to jeden kilometr. Ta wartość jest równie używana jako domyślna, jeśli odpowiedź nie zawiera wartości pola ograniczenia.

Po uzyskaniu współrzędnych i promienia wywołujemy funkcję `bingEntitySearch()`, aby wykonać rzeczywiste wyszukiwanie.

## <a name="performing-the-search"></a>Wykonywanie wyszukiwania

Mając zapytanie, lokalizację, ciąg opcji oraz klucz interfejsu API, funkcja `BingEntitySearch()` wykonuje żądanie wyszukiwania jednostek Bing.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

Po pomyślnym zakończeniu żądania HTTP język JavaScript wywołuje naszą procedurę obsługi zdarzeń `load`, `handleBingResponse()`, aby obsłużyć pomyślne żądanie GET kodu HTTP do interfejsu API. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Pomyślne żądanie HTTP *nie* oznacza wcale, że samo wyszukiwanie zakończyło się powodzeniem. Jeśli w operacji wyszukiwania wystąpi błąd, interfejs API wyszukiwania jednostek Bing zwraca kod stanu HTTP inny niż 200 i umieszcza informacje o błędzie w odpowiedzi JSON. Ponadto, jeśli żądanie podlegało ograniczeniu przepustowości, interfejs API zwraca pustą odpowiedź.

Większość kodu w obu poprzednich funkcjach jest przeznaczona do obsługi błędów. Błędy mogą wystąpić na następujących etapach:

|Etap|Potencjalne błędy|Obsługiwane przez|
|-|-|-|
|Tworzenie obiektu żądania języka JavaScript|Nieprawidłowy adres URL|Blok `try`/`catch`|
|Wykonywanie żądania|Błędy sieci, przerwane połączenia|Obsługa zdarzeń `error` i `abort`|
|Wykonywanie wyszukiwania|Nieprawidłowe żądanie, nieprawidłowy kod JSON, limity przepustowości|Testy obsługi zdarzeń `load`|

Błędy są obsługiwane przez wywołanie funkcji `renderErrorMessage()` z wszystkimi znanymi szczegółowymi informacjami o błędzie. Jeśli odpowiedź przejdzie pomyślnie pełen zestaw testów błędów, wywołujemy funkcję `renderSearchResults()`, aby wyświetlić wyniki wyszukiwania na stronie.

## <a name="displaying-search-results"></a>Wyświetlanie wyników wyszukiwania

Interfejs API wyszukiwania jednostek Bing [wymaga wyświetlania wyników w określonej kolejności](use-display-requirements.md). Ponieważ interfejs API może zwracać dwa różne rodzaje odpowiedzi, nie wystarczy wykonać iteracji przez najwyższy poziom `Entities` lub `Places` kolekcję w odpowiedzi JSON i wyświetlić te wyniki. (Aby uzyskać tylko jeden typ wyniku, należy użyć parametru zapytania `responseFilter`).

Zamiast tego używamy kolekcji `rankingResponse` w wynikach wyszukiwania, aby uporządkować wyniki do wyświetlenia. Ten obiekt odwołuje się do elementów w kolekcjach `Entitiess` i/lub `Places`.

Obiekt `rankingResponse` może zawierać do trzech kolekcji wyników wyszukiwania oznaczonych `pole`, `mainline` i `sidebar`. 

Kolekcja `pole`, jeśli występuje, jest najbardziej istotnym wynikiem wyszukiwania i musi być odpowiednio widoczna. Kolekcja `mainline` odnosi się do zbiorczych wyników wyszukiwania. Wyniki kolekcji głównej powinny być wyświetlane bezpośrednio po kolekcji `pole` (lub w pierwszej kolejności w przypadku braku kolekcji `pole`). 

I na koniec. Kolekcja `sidebar` odnosi się do pomocniczych wyników wyszukiwania. Mogą one być faktycznie wyświetlane na pasku bocznym lub po prostu po wynikach głównych. W naszej aplikacji samouczka wybraliśmy drugą opcję.

Każdy element w kolekcji `rankingResponse` odnosi się do rzeczywistych elementów wyników wyszukiwania na różny, ale równoważny sposób.

| Element | Opis |
|-|-|
|`id`|Element `id` wygląda jak adres URL, ale nie powinien być używany dla linków. Typ elementu `id` w wyniku klasyfikacji odpowiada wartości `id` elementu wyników wyszukiwania w kolekcji odpowiedzi *lub* całej kolekcji odpowiedzi (na przykład `Entities`).
|`answerType`<br>`resultIndex`|Element `answerType` odnosi się do kolekcji odpowiedzi najwyższego poziomu, która zawiera wynik (na przykład `Entities`). Element `resultIndex` odnosi się do indeksu wyniku w tej kolekcji. Jeśli element `resultIndex` zostanie pominięty, klasyfikacja wyników odwołuje się do całej kolekcji.

> [!NOTE]
> Aby uzyskać więcej informacji na temat tej części odpowiedzi wyszukiwania, zobacz temat [Klasyfikacja wyników](rank-results.md).

Możesz użyć dowolnej metody lokalizowania elementu wyników wyszukiwania, którego dotyczy odwołanie, najodpowiedniejszej dla aplikacji. W naszym kodzie samouczka używamy metod `answerType` i `resultIndex` do lokalizowania poszczególnych wyników wyszukiwania.

Na koniec przyjrzymy się funkcji `renderSearchResults()`. Ta funkcja wykonuje iterację na trzech kolekcjach `rankingResponse`, które reprezentują trzy sekcje wyników wyszukiwania. Dla każdej sekcji wywołujemy funkcję `renderResultsItems()`, aby zrenderować wyniki tej sekcji.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Renderowanie elementów wyniku

W kodzie JavaScript obiekt `searchItemRenderers` może zawierać *funkcje renderujące:* funkcje, które generują kod HTML dla każdego rodzaju wyniku wyszukiwania.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Funkcja renderująca może akceptować następujące parametry:

| Parametr | Opis |
|-|-|
|`item`|Obiekt JavaScript, który zawiera właściwości elementu, takie jak adres URL i opis.|
|`index`|Indeks elementu wyniku w ramach jego kolekcji.|
|`count`|Liczba elementów w kolekcji elementów wyników wyszukiwania.|

Parametry `index` i `count` mogą służyć do numerowania wyników, do generowania specjalnego kodu HTML wstawianego na początku lub końcu kolekcji, do wstawiania podziałów wiersza po określonej liczbie elementów i tak dalej. Jeśli funkcja renderująca nie wymaga takiej funkcjonalności, nie musi akceptować tych dwóch parametrów. W rzeczywistości nie używamy ich w funkcjach renderujących w naszej aplikacji samouczka.

Przyjrzyjmy się bliżej programowi renderującemu `entities`:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Nasza funkcja renderująca jednostki:

> [!div class="checklist"]
> * tworzy tag `<img>` kodu HTML, aby wyświetlić miniaturę obrazu, jeśli istnieje; 
> * tworzy tag `<a>` kodu HTML prowadzący do strony, która zawiera obraz;
> * tworzy opis, który wyświetla informacje dotyczące obrazu i witryny, w której się znajduje.
> * wprowadza klasyfikację jednostki przy użyciu wskazówek wyświetlania, jeśli istnieje;
> * zawiera link do wyszukiwania Bing umożliwiający uzyskanie dodatkowych informacji na temat jednostki;
> * wyświetla wszystkie informacje o licencjach lub uznaniu autorstwa wymagane przez źródła danych.

## <a name="persisting-client-id"></a>Trwały identyfikator klienta

Odpowiedzi z interfejsów API wyszukiwania Bing mogą zawierać nagłówek `X-MSEdge-ClientID`, który powinien być wysyłany z powrotem do interfejsu API z kolejnymi żądaniami. Jeśli jest używanych wiele interfejsów API wyszukiwania Bing, dla każdego z nich powinien być stosowany ten sam identyfikator klienta, jeśli jest to możliwe.

Udostępnienie `X-MSEdge-ClientID` nagłówka pozwala interfejsom API Bing skojarzyć wszystkie wyszukiwania użytkownika, które mają dwie ważne zalety.

Po pierwsze umożliwia aparatowi wyszukiwania Bing zastosowanie do wyszukiwań wcześniejszego kontekstu, co pozwala znaleźć bardziej satysfakcjonujące użytkownika wyniki. Jeśli na przykład użytkownik wcześniej wyszukiwał terminy związane z żeglowaniem, późniejsze wyszukiwanie terminu „doki” może preferencyjnie zwrócić informacje na temat miejsc dokowania łodzi.

Po drugie usługa Bing może losowo wybierać użytkowników, którzy będą korzystali z nowych funkcji, zanim zostaną one udostępnione publicznie. Podanie tego samego identyfikatora klienta z każdym żądaniem gwarantuje, że użytkownicy, którzy widzą tę funkcję, zawsze będą ją widzieć. Bez identyfikatora klienta użytkownik może widzieć, jak funkcja pojawia się i znika w wynikach wyszukiwania, pozornie losowo.

Zasady zabezpieczeń przeglądarki (CORS) mogą powodować, że nagłówek `X-MSEdge-ClientID` będzie niedostępny dla kodu JavaScript. To ograniczenie występuje, gdy odpowiedź wyszukiwania ma inne źródło niż strona, z której pochodzi żądanie. W środowisku produkcyjnym, aby rozwiązać ten problem, należy udostępnić skrypt po stronie serwera, który wykonuje wywołanie interfejsu API w tej samej domenie, co strona internetowa. Ponieważ skrypt ma to samo źródło co strona internetowa, nagłówek `X-MSEdge-ClientID` jest dostępny dla kodu JavaScript.

> [!NOTE]
> W aplikacji internetowej w środowisku produkcyjnym należy mimo to wykonać to żądanie po stronie serwera. W przeciwnym razie należy dołączyć klucz interfejsu API wyszukiwania Bing do strony internetowej, aby był on dostępny dla każdego, kto wyświetli źródło. Płacisz za wszystkie użycia związane z Twoim kluczem subskrypcji interfejsu API, nawet za żądania wykonane przez osoby nieupoważnione, zatem ważne jest, aby nie ujawniać swojego klucza.

W celach programistycznych możesz wykonywać żądania interfejsu API wyszukiwania w sieci Web Bing za pośrednictwem serwera proxy CORS. Odpowiedź z takiego serwera proxy ma `Access-Control-Expose-Headers` nagłówek, który zezwala na listy nagłówków odpowiedzi i udostępnia je dla języka JavaScript.

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
> [Informacje interfejs API wyszukiwania jednostek Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi Mapy Bing](//msdn.microsoft.com/library/dd877180.aspx)
