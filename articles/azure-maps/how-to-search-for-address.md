---
title: Wyszukiwanie lokalizacji przy użyciu usług wyszukiwania Azure Maps
description: Dowiedz się więcej na temat usługi wyszukiwania Azure Maps. Zobacz, jak używać tego zestawu interfejsów API do geokodowania, odwracania geokodowania, wyszukiwania rozmytego i odwrotnego wyszukiwania krzyżowego.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/05/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 00ddb53276c052d538d658f2c40384e86cf72aee
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910988"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Wyszukiwanie lokalizacji przy użyciu usług wyszukiwania Azure Maps

[Azure Maps Search Service](/rest/api/maps/search) to zestaw interfejsów API RESTful zaprojektowanych w celu ułatwienia deweloperom wyszukiwania adresów, miejsc i aukcji w biznesie według nazw, kategorii i innych informacji geograficznych. Oprócz obsługi tradycyjnych geokodowania, usługi mogą również odwracać adresy geokodowe i wiele różnych ulic na podstawie Latitudes i długości geograficznej. Wartości szerokości i długości geograficznej zwrócone przez wyszukiwanie mogą służyć jako parametry w innych usługach Azure Maps, takich jak usługi [routingu](/rest/api/maps/route) i [pogody](/rest/api/maps/weather) .


Ten artykuł obejmuje następujące zagadnienia:

* Żądaj współrzędnej szerokości geograficznej i długości geograficznej adresu (lokalizacji geokodu) za pomocą [interfejsu API adresów wyszukiwania]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).
* Wyszukaj adres lub punkt zainteresowania (punkt POI) za pomocą [interfejsu API wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy).
* Utwórz [odwrotne wyszukiwanie adresu](/rest/api/maps/search/getsearchaddressreverse) , aby przetłumaczyć lokalizację współrzędnych na ulicę.
* Przetłumacz lokalizację współrzędnych na poznanie krzyżowe przez człowieka przy użyciu [adresu wyszukiwania odwrotnego interfejsu API](/rest/api/maps/search/getsearchaddressreversecrossstreet).  Najczęściej jest to konieczne w przypadku śledzenia aplikacji, które odbierają kanał GPS z urządzenia lub zasobu, i chcą wiedzieć, gdzie znajduje się Współrzędna.

## <a name="prerequisites"></a>Wymagania wstępne

1. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji.

W tym samouczku jest stosowana aplikacja programu [Poster](https://www.postman.com/) , ale można wybrać inne środowisko deweloperskie interfejsu API.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Żądaj szerokości geograficznej i długości geograficznej dla adresu (geokodowania)

W tym przykładzie użyjemy interfejsu API usługi Azure Maps [Get Address Search](/rest/api/maps/search/getsearchaddress) w celu przekonwertowania adresu na współrzędne geograficzne. Ten proces jest również nazywany *geokodowaniem* . Oprócz zwracania współrzędnych odpowiedź zwróci także szczegółowe właściwości adresu, takie jak ulica, kod pocztowy, gmina i informacje o kraju/regionie.

>[!TIP]
>Jeśli masz zestaw adresów na potrzeby geokodowania, możesz użyć [interfejsu API usługi Batch](/rest/api/maps/search/postsearchaddressbatch) w celu wysłania partii zapytań w jednym wywołaniu interfejsu API.

1. Otwórz aplikację Poster. W górnej części aplikacji Poster wybierz pozycję **Nowy** . W oknie **Tworzenie nowego** okna wybierz pozycję **Kolekcja** .  Nadaj kolekcji nazwę i wybierz przycisk **Utwórz** . Ta kolekcja zostanie użyta w pozostałej części przykładów tego dokumentu.

2. Aby utworzyć żądanie, wybierz pozycję **nowe** ponownie. W oknie **Tworzenie nowego** okna wybierz pozycję **Żądaj** . Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzednim kroku, a następnie wybierz pozycję **Zapisz** .

3. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W ramach tego żądania szukamy określonego adresu: `400 Braod St, Seattle, WA 98109` . W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Kliknij przycisk **Wyślij** niebieską. Treść odpowiedzi będzie zawierać dane dla pojedynczej lokalizacji.

5. Teraz przeszukamy adres, który ma więcej niż jedną możliwą lokalizację. W sekcji **Parametry** Zmień `query` klucz na `400 Broad, Seattle` . Kliknij przycisk **Wyślij** niebieską.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Wyszukaj adres":::

6. Następnie spróbuj ustawić klucz jako `query` `400 Broa` .

7. Kliknij przycisk **Wyślij** . Teraz można zobaczyć, że odpowiedź obejmuje odpowiedzi z wielu krajów. Aby uzyskać efekt geobias do odpowiedniego obszaru dla użytkowników, należy zawsze dodać tyle szczegółów lokalizacji jak to możliwe dla żądania.

## <a name="using-fuzzy-search-api"></a>Korzystanie z interfejsu API wyszukiwania rozmytego

[Interfejs API wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy) Azure Maps obsługuje standardowe wyszukiwanie przy użyciu pojedynczych wierszy i formularzy bezpłatnych. Zalecamy korzystanie z interfejsu API Azure Maps wyszukiwania rozmytego, gdy nie znasz typu danych wejściowych użytkownika dla żądania wyszukiwania.  Dane wejściowe zapytania mogą być pełnym lub częściowym adresem. Może to być również token punktu orientacyjnego (punkt POI), taki jak nazwa punkt POI, punkt POI kategorii lub nazwy marki. Ponadto, aby poprawić znaczenie wyników wyszukiwania, wyniki zapytania mogą być ograniczone przez lokalizację współrzędnych i promień lub przez zdefiniowanie pola ograniczenia.

>[!TIP]
>Większość zapytań wyszukiwania domyślnie maxFuzzyLevel = 1, aby uzyskać wydajność i zmniejszyć nietypowe wyniki. Poziomy rozmycia można dostosować za pomocą `maxFuzzyLevel` `minFuzzyLevel` parametrów lub. Aby uzyskać więcej informacji na temat `maxFuzzyLevel` i pełną listę wszystkich parametrów opcjonalnych, zobacz [Parametry URI wyszukiwania rozmytego](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Wyszukaj adres przy użyciu wyszukiwania rozmytego

W tym przykładzie użyjemy wyszukiwania rozmytego, aby przeszukać cały świat `pizza` .  Następnie pokażemy, jak przeszukiwać zakres określonego kraju. Na koniec pokazano, jak użyć lokalizacji współrzędnych i promienia, aby określić zakres wyszukiwania dla określonego obszaru i ograniczyć liczbę zwracanych wyników.

>[!IMPORTANT]
>Aby uzyskać efekt geobias do odpowiedniego obszaru dla użytkowników, należy zawsze dodać dowolną liczbę szczegółów lokalizacji. Aby dowiedzieć się więcej, zobacz [najlepsze rozwiązania dotyczące wyszukiwania](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Otwórz aplikację Poster, kliknij pozycję **New (nowy** ), a następnie wybierz pozycję **żądanie** . Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w poprzedniej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz** .

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >Atrybut _JSON_ w ścieżce URL określa format odpowiedzi. W tym artykule opisano łatwość użycia i czytelność w formacie JSON. Aby znaleźć inne obsługiwane formaty odpowiedzi, zobacz `format` definicję parametru w [dokumentacji referencyjnej parametru identyfikatora URI](/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Kliknij pozycję **Wyślij** i sprawdź treść odpowiedzi.

    Niejednoznaczny ciąg zapytania dla "Pizza" zwrócił 10 [punktów wyniku zainteresowania](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (punkt POI) zarówno w kategorii "Pizza", jak i "restauracji". Każdy wynik zawiera szczegóły, takie jak adres ulicy, wartości długości geograficznej, port widoku i punkty wejścia dla tej lokalizacji. Wyniki są teraz różne dla tego zapytania i nie są powiązane z żadną lokalizacją odniesienia.
  
    W następnym kroku użyjemy `countrySet` parametru, aby określić tylko kraje/regiony, dla których aplikacja wymaga pokrycia. Aby uzyskać pełną listę obsługiwanych krajów/regionów, zobacz [pokrycie wyszukiwania](./geocoding-coverage.md).

4. Domyślnym zachowaniem jest przeszukanie całego świata i potencjalnie zwrócenie niepotrzebnych wyników. Następnie wyszukamy Pizza tylko Stany Zjednoczone. Dodaj `countrySet` klucz do sekcji **paramss** i ustaw jego wartość na `US` . Ustawienie `countrySet` klucza w celu `US` powiązania wyników z Stany Zjednoczone.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Wyszukaj adres":::

    Wyniki są teraz ograniczone przez kod kraju, a zapytanie zwraca Pizza Restauracje w Stany Zjednoczone.

5. Aby uzyskać jeszcze dokładniejsze wyszukiwanie, możesz przeszukać zakres/Lon.. para współrzędnych. W tym przykładzie użyjemy tabeli/Lon.. Wskazówki dotyczącej obszaru Seattle. Ponieważ chcemy zwrócić wyniki tylko w ramach promienia 400-metrów, dodamy `radius` parametr. Ponadto dodamy `limit` parametr, aby ograniczyć wyniki do pięciu najbliższych miejsc Pizza.

    W sekcji **params** Dodaj następujące pary klucz/wartość:

     | Klucz | Wartość |
    |-----|------------|
    | usługę | 47,620525 |
    | Długość | -122,349274 |
    | promienie | 400 |
    | granice | 5|

6. Kliknij pozycję **Wyślij** . Odpowiedź zawiera wyniki dla Pizza Restauracje w okolicy wskazówki dotyczącej obszaru Seattle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Wyszukaj adres ulicy przy użyciu wyszukiwania odwrotnego adresu

Azure Maps [Pobierz adres wyszukiwania Odwróć interfejs API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) umożliwia przetłumaczenie współrzędnych na adresy, które można odczytać. Ten interfejs API jest często używany w przypadku aplikacji korzystających ze źródeł GPS i chcą odnajdywać adresy w określonych punktach współrzędnych.

>[!IMPORTANT]
>Aby uzyskać efekt geobias do odpowiedniego obszaru dla użytkowników, należy zawsze dodać dowolną liczbę szczegółów lokalizacji. Aby dowiedzieć się więcej, zobacz [najlepsze rozwiązania dotyczące wyszukiwania](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Jeśli masz zestaw lokalizacji współrzędnych w celu odwrócenia geokodu, możesz użyć [interfejsu API odwrotnej sekwencji adresów wyszukiwania](/rest/api/maps/search/postsearchaddressreversebatch) , aby wysłać partię zapytań w jednym wywołaniu interfejsu API.

W tym przykładzie przeprowadzimy wyszukiwanie wsteczne przy użyciu kilku opcjonalnych parametrów, które są dostępne. Aby zapoznać się z pełną listą parametrów opcjonalnych, zobacz [wyszukiwanie wsteczne parametrów wyszukiwania](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. W aplikacji Poster kliknij pozycję **Nowy** , a następnie wybierz pozycję **żądanie** . Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w pierwszej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz** .

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym. Żądanie powinno wyglądać następująco:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Kliknij przycisk **Wyślij** i sprawdź treść odpowiedzi. Powinien zostać wyświetlony jeden wynik zapytania. Odpowiedź zawiera informacje o adresie klucza dotyczące pola Safeco.
  
4. Teraz dodamy następujące pary klucz/wartość do sekcji **params** :

    | Klucz | Wartość | Zwraca
    |-----|------------|------|
    | liczba | 1 |Odpowiedź może zawierać stronę ulicy (lewą/prawą), a także pozycję przesunięcia dla tej liczby.|
    | returnSpeedLimit | true | Zwraca limit prędkości na adresie.|
    | returnRoadUse | true | Zwraca typy użycia drogowego pod adresem. Aby uzyskać wszystkie możliwe typy użycia dróg, zobacz [typy użycia drogowego](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Zwraca typ dopasowania. Aby uzyskać wszystkie możliwe wartości, zobacz [odwrotne wyniki wyszukiwania adresów](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Wyszukaj adres":::

5. Kliknij przycisk **Wyślij** i sprawdź treść odpowiedzi.

6. Następnie dodamy `entityType` klucz i ustawisz jego wartość na `Municipality` . `entityType`Klucz spowoduje zastąpienie `returnMatchType` klucza w poprzednim kroku. Należy również usunąć `returnSpeedLimit` i `returnRoadUse` ponieważ żądamy informacji o gminie.  Dla wszystkich możliwych typów jednostek, zobacz [typy jednostek](/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Wyszukaj adres":::

7. Kliknij pozycję **Wyślij** . Porównaj wyniki z wynikami zwróconymi w kroku 5.  Ponieważ żądany typ jednostki jest teraz `municipality` , odpowiedź nie obejmuje informacji o adresie ulicy. Ponadto zwracaną wartość `geometryId` można użyć do żądania wielokąta granicy przez Azure Maps uzyskać [interfejsu API wielokątów wyszukiwania](/rest/api/maps/search/getsearchpolygon).

>[!TIP]
>Aby uzyskać więcej informacji na temat tych parametrów, a także zapoznać się z innymi informacjami, zobacz [sekcję parametry wyszukiwania wstecznego](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Wyszukiwanie krzyżowe przy użyciu wyszukiwania odwrotnego adresu

W tym przykładzie wyszukamy krzyżyk na podstawie współrzędnych adresu.

1. W aplikacji Poster kliknij pozycję **Nowy** , a następnie wybierz pozycję **żądanie** . Wprowadź **nazwę żądania** dla żądania. Wybierz kolekcję utworzoną w pierwszej sekcji lub utworzoną nową, a następnie wybierz pozycję **Zapisz** .

2. Wybierz metodę **Get** http na karcie Konstruktor i wprowadź następujący adres URL. W przypadku tego żądania i innych żądań wymienionych w tym artykule Zastąp `{Azure-Maps-Primary-Subscription-key}` klucz subskrypcji podstawowym. Żądanie powinno wyglądać następująco:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Wyszukaj adres":::
  
3. Kliknij przycisk **Wyślij** i sprawdź treść odpowiedzi. Zauważ, że odpowiedź zawiera `crossStreet` wartość `Occidental Avenue South` .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejs API REST Search Service Azure Maps](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Azure Maps Search Service najlepsze rozwiązania](how-to-use-best-practices-for-search.md)