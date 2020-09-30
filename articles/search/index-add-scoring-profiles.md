---
title: Zwiększ rangę wyszukiwania przy użyciu profilów oceniania
titleSuffix: Azure Cognitive Search
description: Zwiększ wyniki oceny rangi wyszukiwania na platformie Azure Wyszukiwanie poznawcze przez dodanie profilów oceniania.
manager: nitinme
author: shmed
ms.author: ramero
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 97797e309c32c6ea996d5ae1901b9a266a683173
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537637"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Dodawanie profilów oceniania do indeksu usługi Azure Cognitive Search

*Ocenianie* oblicza wynik wyszukiwania dla każdego elementu w zestawie wyników uporządkowane według rangi. Każdy element w zestawie wyników wyszukiwania ma przypisany wynik wyszukiwania, a następnie rangę do najniższego.

 Usługa Azure Wyszukiwanie poznawcze używa domyślnego oceniania, aby obliczyć początkowy wynik, ale można dostosować obliczenia za pośrednictwem *profilu oceniania*. Profile oceniania zapewniają większą kontrolę nad klasyfikacją elementów w wynikach wyszukiwania. Na przykład możesz chcieć poprawić elementy w oparciu o potencjalną przychody, podwyższyć poziom nowych elementów lub ewentualnie poprawić elementy, które zostały zbyt długie.  

 Poniższy segment wideo szybko przekazuje do sposobu działania profilów oceniania w usłudze Azure Wyszukiwanie poznawcze.
 
> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=463&end=970]

## <a name="scoring-profile-definitions"></a>Definicje profilu oceniania

 Profil oceniania jest częścią definicji indeksu składającą się z pól ważonych, funkcji i parametrów.  

 Aby przedstawić pomysł dotyczący tego, jak wygląda profil oceniania, Poniższy przykład pokazuje prosty profil o nazwie "Geo". Ta wartość zwiększa elementy, które mają termin wyszukiwania w polu **hotelname** . Używa ona również `distance` funkcji, aby preferować elementy, które znajdują się w obrębie dziesięciu kilometrów bieżącej lokalizacji. Jeśli ktoś przeszukuje termin "Inn", a "Inn" będzie częścią nazwy hotelu, dokumenty, które zawierają Hotele z "Inn" w promieniu 10 kilometrów bieżącej lokalizacji, będą wyświetlane w wynikach wyszukiwania.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Aby użyć tego profilu oceniania, zapytanie jest formułowane w celu określenia profilu w ciągu zapytania. W poniższym zapytaniu Zwróć uwagę na parametr zapytania `scoringProfile=geo` w żądaniu.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2020-06-30 
```  

 To zapytanie wyszukuje termin "Inn" i przekazuje w bieżącej lokalizacji. Zwróć uwagę, że to zapytanie zawiera inne parametry, takie jak `scoringParameter` . Parametry zapytania są opisane w [dokumentach wyszukiwania &#40;&#41;interfejsu API REST platformy Azure wyszukiwanie poznawcze ](/rest/api/searchservice/Search-Documents).  

 Kliknij [przykład](#bkmk_ex) , aby zapoznać się z bardziej szczegółowym przykładem profilu oceniania.  

## <a name="what-is-default-scoring"></a>Co to jest ocena domyślna?  
 Ocenianie oblicza wynik wyszukiwania dla każdego elementu w zestawie wyników uporządkowane według rangi. Każdy element w zestawie wyników wyszukiwania ma przypisany wynik wyszukiwania, a następnie rangę do najniższego. Elementy o wyższych wynikach są zwracane do aplikacji. Domyślnie są zwracane górne 50, ale można użyć `$top` parametru, aby zwrócić mniejszą lub większą liczbę elementów (do 1000 w jednej odpowiedzi).  

Wynik wyszukiwania jest obliczany na podstawie właściwości statystycznych danych i zapytania. Usługa Azure Wyszukiwanie poznawcze odnajduje dokumenty, które zawierają terminy wyszukiwania w ciągu zapytania (niektóre lub wszystkie, w zależności od `searchMode` ), preferując dokumenty zawierające wiele wystąpień wyszukiwanego terminu. Wynik wyszukiwania jest nawet wyższy, jeśli termin jest rzadki w indeksie danych, ale jest często używany w dokumencie. Podstawą tego podejścia do obliczania istotności jest znana wartość [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) lub Term częstotliwość odwracania dokumentu.  

 Przy założeniu, że nie ma sortowania niestandardowego, wyniki są klasyfikowane według wyniku wyszukiwania przed zwróceniem do aplikacji wywołującej. Jeśli nie określono $top, zwracane są elementy 50 o najwyższej punktacji wyszukiwania.  

 Wartości wyniku wyszukiwania można powtarzać w zestawie wyników. Przykładowo może być 10 elementów z wynikiem 1,2, 20 elementów z wynikiem 1,0 i 20 elementów z wynikiem 0,5. Gdy wiele trafień ma ten sam wynik wyszukiwania, kolejność tego samego oceny elementów nie jest zdefiniowana i nie jest stabilna. Uruchom ponownie zapytanie i możesz zobaczyć pozycje zmiany położenia. W przypadku dwóch elementów o identycznym wyniku nie ma gwarancji, która jest wyświetlana w pierwszej kolejności.  

## <a name="when-to-add-scoring-logic"></a>Kiedy dodać logikę oceniania 
 Należy utworzyć co najmniej jeden profil oceniania, gdy domyślne zachowanie klasyfikacji nie jest wystarczająco dużo zgodne z celami biznesowymi. Na przykład możesz zdecydować, że istotność wyszukiwania powinna preferować nowo dodane elementy. Podobnie może istnieć pole zawierające marżę zysku lub inne pole wskazujące potencjalną przychody. Zwiększenie możliwości zwiększania korzyści dla Twojej firmy może być ważnym czynnikiem decydującym o korzystaniu z profilów oceniania.  

 Kolejność oparta na dokładność jest również wdrażana za pomocą profilów oceniania. Zapoznaj się ze stronami wyników wyszukiwania, które były używane w przeszłości, aby posortować według ceny, daty, oceny lub istotności. W usłudze Azure Wyszukiwanie poznawcze profile oceniania mają opcję "istotność". Definicja istotności jest kontrolowana przez użytkownika, predykaty z celami biznesowymi i typem środowiska wyszukiwania, które chcesz dostarczyć.  

##  <a name="example"></a><a name="bkmk_ex"></a> Przyklad  
 Jak wspomniano wcześniej, dostosowana Ocena jest implementowana przez co najmniej jeden profil oceniania zdefiniowany w schemacie indeksu.  

 Ten przykład przedstawia schemat indeksu z dwoma profilami oceniania ( `boostGenre` , `newAndHighlyRated` ). Każde zapytanie względem tego indeksu, które zawiera profil jako parametr zapytania, będzie używać profilu do oceny zestawu wyników.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Przepływ pracy  
 Aby zaimplementować niestandardowe zachowanie oceniania, Dodaj profil oceniania do schematu, który definiuje indeks. W indeksie mogą znajdować się maksymalnie 100 profile oceniania (zobacz [limity usługi](search-limits-quotas-capacity.md)), ale w danym momencie można określić tylko jeden profil w danym zapytaniu.  

 Zacznij od [szablonu](#bkmk_template) podanego w tym temacie.  

 Podaj nazwę Profile oceniania są opcjonalne, ale jeśli je dodajesz, nazwa jest wymagana. Upewnij się, że przestrzegasz konwencji nazewnictwa dla pól (rozpoczyna się od litery, unika znaków specjalnych i słów zarezerwowanych). Aby uzyskać pełną listę, zobacz [reguły nazewnictwa &#40;Azure Wyszukiwanie poznawcze&#41;](/rest/api/searchservice/naming-rules) .  

 Treść profilu oceniania jest tworzona na podstawie ważonych pól i funkcji.  

|||  
|-|-|  
|**Ważenie**|Określ pary nazwa-wartość, które przypisują względną wagę do pola. W [przykładzie](#bkmk_ex)pola albumTitle, gatunek i artyściname są podwyższane odpowiednio do 1,5, 5 i 2. Dlaczego gatunek jest podwyższany o wiele więcej niż inne? Jeśli wyszukiwanie jest przeprowadzane nad danymi, które są dość jednorodne (podobnie jak w przypadku "gatunek" w `musicstoreindex` ), może być potrzebna większa Wariancja w odniesieniu do wag względnych. Na przykład w `musicstoreindex` , "Rock" pojawia się zarówno jako gatunek, jak i w identycznie sformułowanych opisach gatunku. Jeśli chcesz, aby gatunek miał wartość Opis gatunku, pole gatunek będzie wymagało znacznie wyższej wagi względnej.|  
|**Funkcje**|Używane, gdy dodatkowe obliczenia są wymagane dla określonych kontekstów. Prawidłowe wartości to `freshness` , `magnitude` , `distance` , i `tag` . Każda funkcja ma parametry, które są unikatowe dla niego.<br /><br /> -   `freshness` powinien być używany, gdy chcesz zwiększyć, jak ma być nowy lub stary element. Tej funkcji można używać tylko z `datetime` polami (EDM. DataTimeOffset). Zauważ, że `boostingDuration` atrybut jest używany tylko z `freshness` funkcją.<br />-   `magnitude` powinien być używany, gdy chcesz wzrosnąć w zależności od tego, jak wysoka lub niska wartość liczbowa jest. Scenariusze, które wywołują tę funkcję, obejmują zwiększenie wydajności według marży zysku, najwyższej ceny, najniższej ceny lub liczby pobrań. Tej funkcji można używać tylko z polami Double i Integer.<br />     W przypadku `magnitude` funkcji można odwrócić zakres, wysoki do niskiej, jeśli chcesz, aby wzorzec odwrotny (na przykład w celu zwiększenia liczby elementów o niższych cenach przekraczających wyższą cenę). Uwzględniając asortyment cen od $100 do $1, należy ustawić `boostingRangeStart` na wartość 100 i `boostingRangeEnd` o 1, aby zwiększyć poziom niższych cen.<br />-   `distance` powinien być używany, gdy chcesz zwiększyć się według lokalizacji w sąsiedztwie lub geograficznym. Tej funkcji można używać tylko z `Edm.GeographyPoint` polami.<br />-   `tag` należy używać, gdy chcesz zwiększyć zgodność tagów wspólnych między dokumentami i kwerendami wyszukiwania. Tej funkcji można używać tylko z `Edm.String` polami i `Collection(Edm.String)` .<br /><br /> **Reguły korzystania z funkcji**<br /><br /> Typ funkcji ( `freshness` , `magnitude` ,, `distance` ) `tag` musi być małymi literami.<br /><br /> Funkcje nie mogą zawierać wartości null ani pustych. W przypadku uwzględnienia wartości pola NazwaPola należy ustawić ją na coś.<br /><br /> Funkcje mogą być stosowane tylko do pól z możliwością filtrowania. Aby uzyskać więcej informacji na temat pól z możliwością filtrowania, zobacz [Create Index &#40;Azure wyszukiwanie POZNAWCZE API REST&#41;](/rest/api/searchservice/create-index) .<br /><br /> Funkcje mogą być stosowane tylko do pól, które są zdefiniowane w kolekcji Fields indeksu.|  

 Po zdefiniowaniu indeksu Skompiluj indeks, przekazując schemat indeksu, a następnie dokumenty. Aby uzyskać instrukcje dotyczące tych operacji, zobacz [Tworzenie indeksu &#40;Azure wyszukiwanie poznawcze interfejs API rest&#41;](/rest/api/searchservice/create-index) oraz [Dodawanie, aktualizowanie lub usuwanie dokumentów &#40;Azure wyszukiwanie POZNAWCZE interfejs API REST&#41;](/rest/api/searchservice/addupdate-or-delete-documents) . Po skompilowaniu indeksu powinien istnieć funkcjonalny profil oceniania, który współpracuje z danymi wyszukiwania.  

##  <a name="template"></a><a name="bkmk_template"></a> Formularza  
 W tej sekcji przedstawiono składnię i szablon dla profilów oceniania. Opisy atrybutów można znaleźć w dokumentacji dotyczącej [atrybutów indeksu](#bkmk_indexref) w następnej sekcji.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a> Odwołania do atrybutów indeksu  

> [!NOTE]  
>  Funkcję oceniania można stosować tylko do pól, które są filtrowane.  

|Atrybut|Opis|  
|---------------|-----------------|  
|`name`|Wymagany. To jest nazwa profilu oceniania. Jest ona zgodna z tymi samymi konwencjami nazewnictwa pola. Musi rozpoczynać się od litery, nie może zawierać kropek, dwukropków lub znaków @ symboli i nie może rozpoczynać się od frazy "azureSearch" (z uwzględnieniem wielkości liter).|  
|`text`|Zawiera właściwość wag.|  
|`weights`|Opcjonalny. Zawiera pary nazwa-wartość, które określają nazwę pola i względną wagę. Względna waga musi być dodatnią liczbą całkowitą lub liczbą zmiennoprzecinkową.<br /><br /> Wagi są używane do wskazania znaczenia jednego pola z możliwością wyszukiwania względem innego.|  
|`functions`|Opcjonalny. Funkcję oceniania można stosować tylko do pól, które są filtrowane.|  
|`type`|Wymagane dla funkcji oceniania. Wskazuje typ funkcji, która ma zostać użyta. Prawidłowe wartości to wielkość, świeżość, odległość i tag. W każdym profilu oceniania można uwzględnić więcej niż jedną funkcję. Nazwa funkcji musi zawierać małe litery.|  
|`boost`|Wymagane dla funkcji oceniania. Liczba dodatnia używana jako mnożnik dla nieprzetworzonego wyniku. Wartość nie może być równa 1.|  
|`fieldname`|Wymagane dla funkcji oceniania. Funkcja oceniania może zostać zastosowana tylko do pól, które są częścią kolekcji pól indeksu, i które są do filtrowania. Ponadto każdy typ funkcji wprowadza dodatkowe ograniczenia (świeżość jest używana z polami DateTime, wielkości z liczbami całkowitymi lub polami podwójnymi oraz odległości z polami lokalizacji). Można określić tylko jedno pole dla każdej definicji funkcji. Na przykład, aby użyć wielkości dwa razy w tym samym profilu, należy uwzględnić dwie definicje, jeden dla każdego pola.|  
|`interpolation`|Wymagane dla funkcji oceniania. Definiuje nachylenie, dla którego wzrost wyniku zwiększa się od początku zakresu do końca zakresu. Prawidłowe wartości to liniowe (domyślne), stałe, kwadratowe i logarytmiczne. Aby uzyskać szczegółowe informacje, zobacz [Ustawianie interpolacji](#bkmk_interpolation) .|  
|`magnitude`|Funkcja oceny wielkości służy do zmiany klasyfikacji na podstawie zakresu wartości dla pola liczbowego. Poniżej przedstawiono niektóre typowe przykłady użycia:<br /><br /> -   **Klasyfikacje gwiazdkowe:** Zmień ocenianie na podstawie wartości w polu "Klasyfikacja gwiazdkowa". Gdy są istotne dwa elementy, zostanie wyświetlony pierwszy element o wyższej ocenie.<br />-   **Margines:** Jeśli istnieją dwa dokumenty, sprzedawca detaliczny może chcieć poprawić dokumenty, które mają wyższe marginesy.<br />-   **Liczba elementów do kliknięcia:** W przypadku aplikacji, które śledzą akcje klikania dla produktów lub stron, można użyć wielkości, aby zwiększyć elementy, które mają na celu uzyskanie większości ruchu.<br />-   **Liczba pobrań:** W przypadku aplikacji, które śledzą pobieranie, funkcja o wielkości umożliwia zwiększenie poziomu elementów, które mają najwięcej pobrań.|  
|`magnitude` &#124; `boostingRangeStart`|Ustawia wartość początkową zakresu, w którym ma być oceniane znaczenie. Wartość musi być liczbą całkowitą lub zmiennoprzecinkową. W przypadku klasyfikacji z gwiazdką od 1 do 4 będzie to 1. W przypadku marginesów powyżej 50% będzie to 50.|  
|`magnitude` &#124; `boostingRangeEnd`|Ustawia wartość końcową zakresu, w którym ma być oceniane znaczenie. Wartość musi być liczbą całkowitą lub zmiennoprzecinkową. W przypadku klasyfikacji z gwiazdką od 1 do 4 będzie to 4.|  
|`magnitude` &#124; `constantBoostBeyondRange`|Prawidłowe wartości to true lub false (wartość domyślna). Po ustawieniu na wartość true, pełne podwyższenie poziomu będzie nadal stosowane do dokumentów, które mają wartość pola docelowego wyższego niż górny koniec zakresu. W przypadku wartości false zwiększenie tej funkcji nie zostanie zastosowane do dokumentów mających wartość pola docelowego, które wykracza poza zakres.|  
|`freshness`|Funkcja oceny Aktualności służy do zmiany rankingu ocen dla elementów na podstawie wartości w `DateTimeOffset` polach. Na przykład element o późniejszej dacie może być wyższy niż starsze elementy.<br /><br /> Istnieje również możliwość ustalania rangi elementów, takich jak zdarzenia kalendarza w przyszłych terminach, takich jak elementy bliżej obecnego elementu mogą być bardziej uporządkowane niż dalsze w przyszłości.<br /><br /> W bieżącej wersji usługi jeden koniec zakresu zostanie ustalony do bieżącego czasu. Druga końcowa to godzina w przeszłości oparta na `boostingDuration` . Aby zwiększyć liczbę razy w przyszłości, użyj wartości ujemnej `boostingDuration` .<br /><br /> Częstotliwość, z jaką zmiany zwiększające się z zakresu maksymalnego i minimalnego są określane przez interpolację zastosowana do profilu oceniania (zobacz rysunek poniżej). Aby odwrócić współczynnik zwiększania poziomu, wybierz współczynnik zwiększania wartości mniejszej niż 1.|  
|`freshness` &#124; `boostingDuration`|Ustawia okres, po upływie którego podwyższanie poziomu zostanie zatrzymane dla określonego dokumentu. Zobacz [Ustaw boostingDuration](#bkmk_boostdur) w poniższej sekcji, aby zapoznać się ze składnią i przykładami.|  
|`distance`|Funkcja oceniania odległości służy do wpływania na wynik dokumentu w zależności od tego, jak blisko lub daleko odnoszą się do lokalizacji geograficznej odniesienia. Lokalizacja odniesienia jest określana jako część zapytania w parametrze (przy użyciu `scoringParameterquery` opcji String) jako długość argumentu w formacie.|  
|`distance` &#124; `referencePointParameter`|Parametr, który ma zostać przesłany w zapytaniach, aby można go było użyć jako lokalizacji odwołania. `scoringParameter` jest parametrem zapytania. Opisy parametrów zapytań można znaleźć w temacie [Wyszukiwanie dokumentów &#40;Azure wyszukiwanie POZNAWCZE API REST&#41;](/rest/api/searchservice/Search-Documents) .|  
|`distance` &#124; `boostingDistance`|Liczba, która wskazuje odległość w kilometrach od lokalizacji odniesienia, w której upływa zakres zwiększania.|  
|`tag`|Funkcja oceniania tagów służy do wpływania na wyniki dokumentów w oparciu o Tagi w dokumentach i wyszukiwania zapytań. Dokumenty zawierające Tagi wspólne z zapytaniem wyszukiwania zostaną podwyższane. Tagi dla zapytania wyszukiwania są dostarczane jako parametr oceniania w każdym żądaniu wyszukiwania (przy użyciu `scoringParameterquery` opcji String).|  
|`tag` &#124; `tagsParameter`|Parametr, który ma zostać przesłany w zapytaniach, aby określić Tagi dla określonego żądania. `scoringParameter` jest parametrem zapytania. Opisy parametrów zapytań można znaleźć w temacie [Wyszukiwanie dokumentów &#40;Azure wyszukiwanie POZNAWCZE API REST&#41;](/rest/api/searchservice/Search-Documents) .|  
|`functionAggregation`|Opcjonalny. Stosuje się tylko wtedy, gdy są określone funkcje. Prawidłowe wartości to: sum (wartość domyślna), Average, minimum, maksimum i firstMatching. Wynikiem wyszukiwania jest pojedyncza wartość, która jest obliczana na podstawie wielu zmiennych, w tym wielu funkcji. Ten atrybut wskazuje, w jaki sposób wzrosty wszystkich funkcji są łączone w pojedynczej agregacji zagregowanej, która następnie jest stosowana do oceny dokumentu podstawowego. Wynik podstawowy jest oparty na wartości [TF-IDF](http://www.tfidf.com/) obliczonej na podstawie dokumentu i zapytania wyszukiwania.|  
|`defaultScoringProfile`|W przypadku wykonywania żądania wyszukiwania, jeśli nie określono żadnego profilu oceniania, zostanie użyte domyślne ocenianie (tylko w programie[TF-IDF](http://www.tfidf.com/) ).<br /><br /> W tym miejscu można ustawić domyślną nazwę profilu oceniania, co spowoduje, że usługa Azure Wyszukiwanie poznawcze będzie używać tego profilu, gdy w żądaniu wyszukiwania nie zostanie określony konkretny profil.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a> Ustaw interpolacje  
 Interpolacje umożliwiają ustawienie kształtu skośnego używanego do oceniania. Ze względu na to, że ocena jest wysoka do niska, nachylenie jest zawsze zmniejszane, ale Interpolacja określa krzywą skośnego spadku. Następujące interpolacje mogą być używane:  

| Interpolacji | Opis |  
|-|-|  
|`linear`|W przypadku elementów, które znajdują się w zakresie maksymalnym i minimalnym, podwyższanie poziomu zastosowana do elementu zostanie wykonane w sposób stale zmniejszany. Skala liniowa jest domyślną interpolacją dla profilu oceniania.|  
|`constant`|Dla elementów, które znajdują się w zakresie początkowym i końcowym, do wyników rangi zostanie zastosowana stała podwyższanie poziomu.|  
|`quadratic`|W porównaniu do interpolacji liniowej, która ma ciągle zmniejszający się wzrost, kwadrat będzie początkowo zmniejszany w mniejszym tempie, a następnie, jak zbliża się do zakresu końcowego, zmniejsza się w znacznie większym interwale. Ta opcja interpolacji nie jest dozwolona w funkcjach oceniania tagów.|  
|`logarithmic`|W porównaniu do interpolacji liniowej, która ma ciągle zmniejszający się wzrost, LOGARYTM logarytmiczny będzie początkowo zmniejszany w większym tempie, a następnie, jak zbliża się do zakresu końcowego, zmniejsza się w znacznie mniejszym interwale. Ta opcja interpolacji nie jest dozwolona w funkcjach oceniania tagów.|  

 ![Stałe, liniowe, kwadratowe, log10 — linie na grafie](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a> Ustaw boostingDuration  
 `boostingDuration` jest atrybutem `freshness` funkcji. Służy on do ustawiania okresu ważności, po upływie którego podwyższanie poziomu zostanie zatrzymane dla określonego dokumentu. Na przykład aby zwiększyć linię produktu lub markę dla 10-dniowego okresu promocyjnego, należy określić 10-dniowy okres jako "P10D" dla tych dokumentów.  

 `boostingDuration` musi być sformatowana jako wartość XSD "dayTimeDuration" (ograniczony podzbiór wartości Duration ISO 8601). Wzorzec dla tego elementu to: "P [nD] [T [nH] [nM] [nS]]".  

 W poniższej tabeli przedstawiono kilka przykładów.  

|Czas trwania|boostingDuration|  
|--------------|----------------------|  
|1 dzień|"P1D"|  
|2 dni i 12 godzin|"P2DT12H"|  
|15 minut|"PT15M"|  
|30 dni, 5 godzin, 10 minut i 6,334 sekund|"P30DT5H10M 6.334 S"|  

 Aby uzyskać więcej przykładów, zobacz [schemat XML: typy danych (witryna sieci web w3.org)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration).  

## <a name="see-also"></a>Zobacz też  

+ [Dokumentacja interfejsu API REST](/rest/api/searchservice/)
+ [Tworzenie interfejsu API indeksu](/rest/api/searchservice/create-index)
+ [Azure Wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search?)