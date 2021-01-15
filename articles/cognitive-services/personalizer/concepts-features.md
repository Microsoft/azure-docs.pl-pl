---
title: 'Funkcje: Akcja i kontekst — Personalizacja'
titleSuffix: Azure Cognitive Services
description: Personalizacja używa funkcji, informacji o akcjach i kontekście, aby uzyskiwać lepsze sugestie dotyczące klasyfikacji. Funkcje mogą być bardzo ogólne lub specyficzne dla elementu.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 55d1b7171201c962278d7c526528b36848c19449
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217893"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkcje to informacje o akcjach i kontekście

Usługa personalizowania działa przez uczenie się, co aplikacja powinna pokazać użytkownikom w danym kontekście.

Personalizacja używa **funkcji**, które są informacjami o **bieżącym kontekście** , aby wybrać najlepszą **akcję**. Funkcje reprezentują wszystkie informacje, które mogą pomóc spersonalizować, aby osiągnąć wyższy poziom korzyści. Funkcje mogą być bardzo ogólne lub specyficzne dla elementu. 

Na przykład może być dostępna **Funkcja** :

* _Osoba użytkownika_ , taka jak `Sports_Shopper` . Nie powinna to być indywidualny identyfikator użytkownika. 
* _Zawartość_ , taka jak w przypadku filmu wideo `Documentary` , a lub lub `Movie` `TV Series` czy element detaliczny jest dostępny w sklepie.
* _Bieżący_ okres, taki jak dzień tygodnia.

Personalizacja nie określa, nie ogranicza ani nie naprawia funkcji, które można wysyłać dla akcji i kontekstu:

* Jeśli ich nie masz, możesz wysłać kilka funkcji dla niektórych akcji, a nie dla innych. Na przykład, w przypadku serii TV mogą znajdować się filmy o atrybutach.
* Niektóre funkcje mogą być dostępne tylko kilka razy. Aplikacja mobilna może na przykład dostarczyć więcej informacji niż strona sieci Web. 
* Wraz z upływem czasu można dodawać i usuwać funkcje dotyczące kontekstu i akcji. Personalizacja kontynuuje naukę od dostępnych informacji.
* Dla kontekstu musi istnieć co najmniej jedna funkcja. Program personalizujer nie obsługuje pustego kontekstu. W przypadku wysyłania tylko stałego kontekstu za każdym razem Personalizacja wybierze akcję do klasyfikacji tylko dla funkcji w akcjach.
* W przypadku funkcji kategorii nie trzeba definiować możliwych wartości i nie trzeba wstępnie definiować zakresów dla wartości liczbowych.

## <a name="supported-feature-types"></a>Obsługiwane typy funkcji

Personalizacja obsługuje funkcje typu String, numeric i Boolean. Bardzo prawdopodobnie aplikacja będzie używać funkcji ciągów z kilkoma wyjątkami.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Jak wybór typu funkcji ma wpływ na Machine Learning w programie Personalizacja

* **Ciągi**: dla typów ciągów, każda kombinacja klucza i wartości jest traktowana jako funkcja One-Hot (np. gatunek: "ScienceFiction" i gatunek: "Dokumentacja" utworzy dwie nowe funkcje wejściowe dla modelu uczenia maszynowego.
* Wartość **liczbowa**: należy używać wartości liczbowych, gdy liczba jest wartością, która powinna mieć proporcjonalnie wpływ na wynik personalizacji. Jest to bardzo zależne. W uproszczonym przykładzie, np. w przypadku personalizowania środowiska sprzedaży detalicznej, NumberOfPetsOwned może być funkcją, która jest wartością numeryczną, ponieważ osoby mające 2 lub 3 zwierzęta domowe mają wpływ na wynik personalizacji dwa razy lub trzy razy dziennie tak samo, jak w przypadku 1 PET. Funkcje, które są oparte na jednostkach liczbowych, ale gdzie znaczenie nie jest liniowe, takie jak wiek, temperatura lub wysokość osoby — są najlepiej kodowane jako ciągi. Na przykład DayOfMonth będzie ciągiem z "1", "2"... "31". Jeśli masz wiele kategorii, jakość funkcji można zazwyczaj ulepszyć za pomocą zakresów. Na przykład wiek może być zakodowany jako "wiek": "0-5", "wiek": "6-10" itd.
* Wartości **logiczne** wysyłane z wartością "false" działają tak, jakby nie zostały wysłane.

Nieobecne funkcje powinny być pominięte w żądaniu. Należy unikać wysyłania funkcji o wartości null, ponieważ zostaną one przetworzone jako istniejące i mają wartość "null" podczas uczenia modelu.

## <a name="categorize-features-with-namespaces"></a>Kategoryzowanie funkcji za pomocą przestrzeni nazw

Personalizacja wykonuje funkcje zorganizowane w przestrzenie nazw. W aplikacji należy określić, czy są używane przestrzenie nazw i jakie powinny być. Przestrzenie nazw służą do grupowania funkcji dotyczących podobnego tematu lub funkcji pochodzących z określonego źródła.

Poniżej przedstawiono przykłady przestrzeni nazw funkcji używanych przez aplikacje:

* User_Profile_from_CRM
* Godzina
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Pogoda
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Przestrzenie nazw funkcji można nazwać po własnych konwencjach, o ile są one prawidłowymi kluczami JSON. Przestrzenie nazw służą do organizowania funkcji w różne zestawy oraz do odróżniania funkcji o podobnych nazwach. Przestrzenie nazw można traktować jako prefiks, który jest dodawany do nazw funkcji. Przestrzenie nazw nie mogą być zagnieżdżane.


W poniższym kodzie JSON, `user` , `state` , i `device` są przestrzeniami nazw funkcji. 

> [!Note]
> Obecnie zdecydowanie zalecamy używanie nazw dla przestrzeni nazw funkcji, które są oparte na kodowaniu UTF-8 i zaczynają się od różnych liter. Na przykład, `user` , `state` i `device` zaczyna się od `u` , `s` , i `d` . Obecnie przestrzenie nazw zawierające te same pierwsze znaki mogą powodować kolizje w indeksach używanych do uczenia maszynowego.

Obiekty JSON mogą zawierać zagnieżdżone obiekty JSON oraz proste właściwości/wartości. Tablica może być uwzględniana tylko wtedy, gdy elementy tablicy są liczbami. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": ["47.6", "-122.1"]
            }
        },
        {
            "environment": {
                "dayOfMonth": "28",
                "monthOfYear": "8",
                "timeOfDay": "13:00",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        },
        {
            "userActivity" : {
                "itemsInCart": 3,
                "cartValue": 250,
                "appliedCoupon": true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Ograniczenia w zestawach znaków dla przestrzeni nazw

Ciąg używany do nazywania przestrzeni nazw musi spełniać pewne ograniczenia: 
* Nie może być Unicode.
* Można użyć niektórych symboli drukowalnych z kodami < 256 dla nazw przestrzeni nazw. 
* Nie można użyć symboli z kodami < 32 (nie do drukowania), 32 (Space), 58 (dwukropek), 124 (potok) i 126 – 140.
* Nie powinna zaczynać się od znaku podkreślenia "_" lub funkcja zostanie zignorowana.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Jak zwiększyć efektywność zestawów funkcji dla personalizacji

Dobry zestaw funkcji ułatwia personalizację, aby dowiedzieć się, jak prognozować akcję, która zwiększy najwyższy poziom. 

Rozważ wysłanie funkcji do interfejsu API rangi narzędzia personalizacji, który przestrzega następujących zaleceń:

* Używaj kategorii i typów ciągów dla funkcji, które nie są wielkością. 

* Dostępna jest wystarczająca liczba funkcji do personalizacji dysków. Im bardziej precyzyjnie skierowana jest zawartość, tym więcej funkcji są potrzebne.

* Dostępna jest wystarczająca liczba funkcji o różnych *gęstościach*. Funkcja jest *gęsta* , jeśli wiele elementów jest zgrupowanych w kilku zasobnikach. Na przykład tysiące wideo może być sklasyfikowane jako "Long" (ponad 5 min Long) i "krótkie" (poniżej 5 min Long). Jest to *bardzo gęsta* funkcja. Z drugiej strony, te same tysiące elementów mogą mieć atrybut o nazwie "title", który niemal nigdy nie będzie miał takiej samej wartości z jednego elementu. Jest to bardzo gęsta lub *rozrzedzona* funkcja.  

Funkcja wysokiej gęstości ułatwia personalizację ekstrapolację z jednego elementu na inny. Ale jeśli istnieje tylko kilka funkcji i są one zbyt gęste, Personalizowanie próbuje precyzyjnie określić zawartość docelową, korzystając z tylko kilku zasobników.

### <a name="improve-feature-sets"></a>Popraw zestawy funkcji 

Analizuj zachowanie użytkowników, wykonując ocenę w trybie offline. Dzięki temu można wyszukać przeszłe dane, aby zobaczyć, jakie funkcje są intensywnie przyczyniające się do pozytywnych korzyści, a także tych, które mają mniejszą przyczynę. Możesz zobaczyć, jakie funkcje są pomocne, a ty i aplikację, aby znaleźć lepsze funkcje do wysyłania do narzędzia personalizacji, aby jeszcze bardziej poprawić wyniki.

Poniższe sekcje są typowymi rozwiązaniami dotyczącymi ulepszania funkcji wysyłanych do narzędzia Personalizacja.

#### <a name="make-features-more-dense"></a>Bardziej gęste funkcje

Można poprawić zestawy funkcji, edytując je tak, aby były większe i bardziej gęste.

Na przykład sygnatura czasowa do drugiego jest funkcją bardzo rozrzedzoną. Może być bardziej gęsty (efektywny) przez klasyfikowanie godzin do "rano", "środku dnia", "południka" itd.

Informacje o lokalizacji również zwykle mają zalety tworzenia szerszych klasyfikacji. Na przykład Współrzędna Latitude-Longitude, taka jak szerokość: 47,67402 ° N, Long: 122,12154 ° W, jest zbyt dokładna i wymusza, aby model pouczył się Szerokość geograficzna i Długość geograficzna jako różne wymiary. Podczas próby personalizacji na podstawie informacji o lokalizacji pomocne jest grupowanie informacji o lokalizacji w większych sektorach. Prostym sposobem jest wybranie odpowiedniej dokładności zaokrąglania dla numerów Lat-Long i połączenie szerokości i długości geograficznej do "obszarów" przez umieszczenie ich w jednym ciągu. Na przykład dobry sposób reprezentowania 47,67402 ° N, Long: 122,12154 ° w regionach około kilku kilometrów o szerokości "Location": "34.3, 12,1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Rozwiń zestawy funkcji z ekstrapolacją informacji

Możesz również uzyskać więcej funkcji, zastanawiając się, że niezbadane atrybuty mogą pochodzić od już posiadanych informacji. Na przykład na potrzeby personalizacji fikcyjnej listy filmów jest możliwe, że weekendy a Weekday mają różne zachowanie od użytkowników? Czas można rozszerzyć, aby miał atrybut "weekend" lub "Weekday". Czy krajowe święta kulturowe mają na uwadze niektóre typy filmów? Na przykład atrybut "Halloween" jest przydatny w miejscach, w których ma zastosowanie. Czy jest możliwe, że deszczowa pogoda ma znaczący wpływ na wybór filmu dla wielu osób? Z czasem i miejscem usługa pogody może zapewnić te informacje i dodać ją jako dodatkową funkcję. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Rozwiń zestawy funkcji za pomocą sztucznej analizy i usług poznawczych

Sztuczna inteligencja i gotowe do uruchomienia Cognitive Services mogą być bardzo wydajnym dodatkiem do narzędzia Personalizacja. 

Wstępnie przetwarzając elementy przy użyciu sztucznych usług analizy, można automatycznie wyodrębniać informacje, które mogą być odpowiednie do personalizacji.

Na przykład:

* Plik filmowy można uruchomić za pomocą [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) , aby wyodrębnić elementy sceny, tekst, tonacji i wiele innych atrybutów. Te atrybuty mogą następnie stać się bardziej gęste w celu odzwierciedlenia cech, które nie miały metadanych oryginalnego elementu. 
* Obrazy można uruchamiać za poorednictwem wykrywania obiektów, twarzy przez tonacji itp.
* Informacje w tekście można rozszerzyć przez wyodrębnienie jednostek, tonacji, rozwijanie jednostek przy użyciu Grafu wiedzy Bing itd.

Możesz użyć kilku innych [Cognitive Services platformy Azure](https://www.microsoft.com/cognitive-services), takich jak

* [Łączenie jednostek](../text-analytics/index.yml)
* [Analiza tekstu](../text-analytics/overview.md)
* [Emocja](../face/overview.md)
* [Przetwarzanie obrazów](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>Akcje reprezentują listę opcji

Każda akcja:

* Ma identyfikator _zdarzenia_ . Jeśli masz już identyfikator zdarzenia, należy go przesłać. Jeśli nie masz identyfikatora zdarzenia, nie wysyłaj go, Personalizujer tworzy go dla Ciebie i zwraca go w odpowiedzi na żądanie rangi. Identyfikator jest skojarzony ze zdarzeniem rangi, a nie użytkownikiem. W przypadku utworzenia identyfikatora identyfikator GUID działa najlepiej. 
* Zawiera listę funkcji.
* Lista funkcji może być duża (setki), ale zalecamy ocenę skuteczności funkcji w celu usunięcia funkcji, które nie przyczyniają się do uzyskania korzyści. 
* Funkcje w **akcjach** mogą lub nie mają żadnej korelacji z funkcjami w **kontekście** używanym przez program personalizujer.
* Funkcje dla akcji mogą być obecne w niektórych akcjach, a nie w innych. 
* Funkcje dla określonego identyfikatora akcji mogą być dostępne jeden dzień, ale później staną się niedostępne. 

Algorytmy uczenia maszynowego dla personalizacji będą działać lepiej, gdy są dostępne stabilne zestawy funkcji, ale wywołania rangi nie powiodą się, jeśli funkcja ustawi zmiany w czasie.

Nie wysyłaj więcej niż 50 akcji podczas klasyfikowania akcji. Mogą to być te same akcje (50) co godzinę lub mogą ulec zmianie. Jeśli na przykład masz katalog produktów o 10 000 elementach dla aplikacji handlu elektronicznego, możesz użyć zalecenia lub aparatu filtrowania, aby określić najwyższe 40, a następnie użyć personalizacji, aby znaleźć ten, który wygeneruje najbardziej wynagrodzenie (na przykład użytkownik zostanie dodany do koszyka) dla bieżącego kontekstu.


### <a name="examples-of-actions"></a>Przykłady akcji

Akcje wysyłane do interfejsu API rangi zależą od tego, co próbujesz spersonalizować.

Oto kilka przykładów:

|Przeznaczenie|Akcja|
|--|--|
|Personalizowanie artykułu wyróżnionego w witrynie sieci Web z wiadomościami.|Każda akcja jest potencjalnym artykułem z wiadomościami.|
|Optymalizacja umieszczania w usłudze AD w witrynie sieci Web.|Każda akcja będzie układem lub regułami, aby utworzyć układ dla reklamy (na przykład w górnej części, w prawym, małych obrazach, Big images).|
|Wyświetl spersonalizowaną klasyfikację zalecanych elementów w witrynie internetowej dotyczącej zakupów.|Każda akcja jest określonym produktem.|
|Sugeruj elementy interfejsu użytkownika, takie jak filtry, aby zastosować je do określonego zdjęcia.|Każda akcja może być innym filtrem.|
|Wybierz odpowiedź bot rozmowy, aby wyjaśnić intencję użytkownika lub zasugerować akcję.|Każda akcja jest opcją interpretacji odpowiedzi.|
|Wybierz elementy, które mają być wyświetlane u góry listy wyników wyszukiwania|Każda akcja to jeden z najważniejszych wyników wyszukiwania.|


### <a name="examples-of-features-for-actions"></a>Przykłady funkcji dla akcji

Poniżej przedstawiono dobre przykłady funkcji dla akcji. Będą one zależne od poszczególnych aplikacji.

* Funkcje o cechach akcji. Na przykład jest to film lub seria TV?
* Funkcje dotyczące sposobu, w jaki użytkownicy mogli korzystać z tej akcji w przeszłości. Na przykład ten film jest najczęściej widziany przez osoby w demograficznych A lub B, zwykle jest odtwarzany nie więcej niż jeden raz.
* Funkcje dotyczące cech charakterystycznych sposobu, w jaki użytkownik *widzi* akcje. Na przykład, czy plakat dla filmu widoczny na miniaturze obejmuje twarze, samochody lub Landscapes?

### <a name="load-actions-from-the-client-application"></a>Ładowanie akcji z aplikacji klienckiej

Funkcje z akcji mogą zazwyczaj pochodzić z systemów zarządzania zawartością, wykazów i systemów zalecanych. Aplikacja jest odpowiedzialna za ładowanie informacji o działaniach z odpowiednich baz danych i systemów. Jeśli akcje nie zmieniają się ani nie są ładowane przy każdym przypadku, gdy ma niezbędny wpływ na wydajność, można dodać logikę w aplikacji, aby buforować te informacje.

### <a name="prevent-actions-from-being-ranked"></a>Zapobiegaj klasyfikowaniu akcji

W niektórych przypadkach istnieją akcje, które nie mają być wyświetlane użytkownikom. Najlepszym sposobem, aby zapobiec klasyfikacji akcji jako najwyższego poziomu nie jest uwzględnianie jej na liście akcji do interfejsu API rangi w pierwszym miejscu.

W niektórych przypadkach można je określić później tylko w logice biznesowej, Jeśli wynikowa _Akcja_ wywołania interfejsu API rangi ma być pokazywana użytkownikowi. W takich przypadkach należy używać _zdarzeń nieaktywnych_.

## <a name="json-format-for-actions"></a>Format JSON dla akcji

Podczas wywoływania rangi będziesz wysyłać wiele akcji do wyboru:

Obiekty JSON mogą zawierać zagnieżdżone obiekty JSON oraz proste właściwości/wartości. Tablica może być uwzględniana tylko wtedy, gdy elementy tablicy są liczbami. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Przykłady informacji kontekstowych

Informacje dla _kontekstu_ są zależne od poszczególnych aplikacji i przypadków użycia, ale zazwyczaj mogą zawierać informacje takie jak:

* Informacje demograficzne i profilowe dotyczące użytkownika.
* Informacje wyodrębnione z nagłówków HTTP, takich jak agent użytkownika, lub pochodzące z informacji HTTP, takich jak odwrotne wyszukiwanie geograficzne na podstawie adresów IP.
* Informacje o bieżącej godzinie, takie jak dzień tygodnia, weekend lub nie, rano lub południa, sezon świąteczny lub nie itp.
* Informacje wyodrębnione z aplikacji mobilnych, takie jak lokalizacja, przenoszenie lub poziom baterii.
* Historyczne zagregowane informacje o zachowaniu użytkowników — takie jak to, co jest widoczne dla tego użytkownika.

Aplikacja jest odpowiedzialna za ładowanie informacji dotyczących kontekstu z odpowiednich baz danych, czujników i systemów, które mogą mieć. Jeśli informacje kontekstowe nie ulegają zmianie, można dodać logikę w aplikacji, aby buforować te informacje przed wysłaniem ich do interfejsu API rangi.

## <a name="json-format-for-context"></a>Format JSON dla kontekstu 

Kontekst jest wyrażony jako obiekt JSON, który jest wysyłany do interfejsu API rangi:

Obiekty JSON mogą zawierać zagnieżdżone obiekty JSON oraz proste właściwości/wartości. Tablica może być uwzględniana tylko wtedy, gdy elementy tablicy są liczbami. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

[Uczenie przez wzmacnianie](concepts-reinforcement-learning.md)
