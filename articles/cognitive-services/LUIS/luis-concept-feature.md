---
title: Funkcje uczenia maszynowego z LUIS
description: Dodaj funkcje do modelu języka, aby przedstawić wskazówki dotyczące sposobu rozpoznawania danych wejściowych, które mają być oznaczone etykietami lub klasyfikacją.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 08ab71375171d4bb4167c725bc7118bec2e1ebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372028"
---
# <a name="machine-learning-features"></a>Funkcje uczenia maszynowego

W uczeniu maszynowym *Funkcja*   jest odróżnianą cechą lub atrybutem danych, które są używane przez system i są przez nie poznani.

Funkcje uczenia maszynowego umożliwiają LUIS ważnych wskazówek dotyczących lokalizacji, w których należy wyszukać koncepcje. Są to wskazówki, których mogą używać LUIS, ale nie są one trudne. LUIS używa tych wskazówek w połączeniu z etykietami, aby znaleźć dane.

Funkcję można opisać jako funkcję, na przykład f (x) = y. W przykładzie wypowiedź funkcja informuje, gdzie należy szukać cech odróżniających. Korzystając z tych informacji, można utworzyć schemat.

## <a name="types-of-features"></a>Typy funkcji

Funkcje są niezbędną częścią projektu schematu. LUIS obsługuje zarówno listę fraz, jak i modele jako funkcje:

* Funkcja listy fraz
* Model (cel lub jednostka) jako funkcja

## <a name="find-features-in-your-example-utterances"></a>Znajdź funkcje w Twoim przykładowym wyrażenia długości

Ponieważ LUIS jest aplikacją opartą na języku, funkcje są oparte na tekście. Wybierz tekst, który wskazuje cechę, którą chcesz rozróżnić. Dla LUIS najmniejsza jednostka jest *tokenem*. W przypadku języka angielskiego token jest ciągłym zakresem liter i cyfr, które nie zawierają spacji ani interpunkcji.

Ponieważ spacje i znaki interpunkcyjne nie są tokenami, należy skoncentrować się na tekście, którego można użyć jako funkcji. Pamiętaj o uwzględnieniu odmian wyrazów, takich jak:

* formularze w liczbie mnogiej
* częstotliwość zleceń
* skróty
* pisownia i błędy pisowni

Określ, czy tekst, ponieważ odróżnia cechę, musi:

* Dopasowuje dokładne słowo lub frazę: rozważ dodanie jednostki wyrażenia regularnego lub jednostki listy jako funkcji do obiektu lub zamiaru.
* Dopasowuje dobrze znaną koncepcję, takie jak daty, godziny lub nazwiska osób: Użyj wstępnie skompilowanej jednostki jako funkcji dla jednostki lub zamiaru.
* Poznaj nowe przykłady z biegiem czasu: Użyj listy frazy niektórych przykładów koncepcji jako funkcji dla jednostki lub zamiaru.

## <a name="create-a-phrase-list-for-a-concept"></a>Tworzenie listy fraz dla koncepcji

Lista fraz jest listą słów lub fraz, które opisują koncepcję. Lista fraz jest stosowana jako dopasowanie bez uwzględniania wielkości liter na poziomie tokenu.

Podczas dodawania listy fraz można ustawić tę funkcję jako **[globalną](#global-features)**. Globalna funkcja ma zastosowanie do całej aplikacji.

### <a name="when-to-use-a-phrase-list"></a>Kiedy używać listy fraz

Użyj listy fraz, gdy potrzebujesz aplikacji LUIS do uogólniania i identyfikowania nowych elementów dla koncepcji. Listy fraz są podobne do słownictwa specyficznego dla domeny. Zwiększają one jakość wiedzy dotyczącej intencji i jednostek.

### <a name="how-to-use-a-phrase-list"></a>Jak używać listy fraz

Za pomocą listy fraz LUIS traktuje kontekst i generalizacje w celu zidentyfikowania elementów, które są podobne do, ale nie dokładnego dopasowania tekstu. Wykonaj następujące kroki, aby użyć listy fraz:

1. Rozpocznij pracę z jednostką uczenia maszynowego:
    1. Dodaj przykład wyrażenia długości.
    1. Oznacz za pomocą jednostki uczenia maszynowego.
1. Dodaj listę fraz:
    1. Dodaj wyrazy z podobnym znaczeniem. Nie dodawaj każdego możliwego wyrazu lub frazy. Zamiast tego Dodaj kilka wyrazów lub fraz jednocześnie. Następnie Przeszkol i Opublikuj.
    1. Przeglądanie i Dodawanie sugerowanych słów.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Typowy scenariusz dla listy fraz

Typowym scenariuszem dla listy fraz jest zwiększenie wyrazów związanych z konkretnym pomysłem.

Warunki medyczne są dobrym przykładem wyrazów, które mogą potrzebować listy fraz, aby zwiększyć ich znaczenie. Warunki te mogą mieć specjalne znaczenie fizyczne, chemiczne, terapeutyczne lub abstrakcyjne. LUIS nie wie, że warunki są ważne dla domeny podmiotu bez listy fraz.

Aby wyodrębnić warunki medyczne:

1. Utwórz przykład wyrażenia długości i Oznacz warunki medyczne w tych wyrażenia długości.
2. Utwórz listę fraz z przykładami warunków w domenie podmiotu. Ta lista wyrażeń powinna zawierać rzeczywisty termin oznaczony etykietą i inne terminy opisujące te same koncepcje.
3. Dodaj listę fraz do jednostki lub podjednostki, która wyodrębnia koncepcję używaną na liście fraz. Najbardziej typowym scenariuszem jest składnik (podrzędny) jednostki uczenia maszynowego. Jeśli lista fraz powinna być stosowana dla wszystkich intencji lub jednostek, Oznacz listę fraz jako listę globalną. Flaga **enabledForAllModels** kontroluje ten zakres modelu w interfejsie API.

### <a name="token-matches-for-a-phrase-list"></a>Dopasowanie tokenu dla listy fraz

Lista fraz jest zawsze stosowana na poziomie tokenu. W poniższej tabeli przedstawiono, w jaki sposób Lista wyrazów z słowem **Ann** ma zastosowanie do odmian tych samych znaków w tej kolejności.


| Odmiana tokenu **Ann** | Dopasowanie listy fraz po znalezieniu tokenu |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | Tak — token jest **Ann**                  |
| **Ann**                    | Tak — token jest **Ann**                  |
| **Anne**                     | Nie — token jest **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Model jako funkcja ułatwia inny model

Model (cel lub jednostka) można dodać jako funkcję do innego modelu (zamiaru lub jednostki). Dodanie istniejącego zamiaru lub jednostki jako funkcji oznacza dodanie dobrze zdefiniowanej koncepcji z etykietami przykładów.

Podczas dodawania modelu jako funkcji można ustawić tę funkcję jako:
* **[Wymagane](#required-features)**. Aby można było zwrócić model z punktu końcowego przewidywania, należy znaleźć wymaganą funkcję.
* **[Globalne](#global-features)**. Globalna funkcja ma zastosowanie do całej aplikacji.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Kiedy używać jednostki jako funkcji do zamiaru

Dodaj jednostkę jako funkcję do celu, gdy wykrycie tej jednostki jest istotne dla zamiaru.

Na przykład, jeśli zachodzi taka potrzeba w przypadku rezerwacji lotu, takiego jak **BookFlight**, a jednostka jest informacjami o biletach (np. liczbą miejsc, pochodzenia i miejsca docelowego), znalezienie jednostki informacji o biletach powinno zwiększyć znaczącą wagę do przewidywania zamiaru **BookFlight** .

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Kiedy używać jednostki jako funkcji dla innej jednostki

Jednostkę (A) należy dodać jako funkcję do innej jednostki (B), gdy wykrycie tej jednostki (A) jest istotne dla przewidywania jednostki (B).

Na przykład, jeśli jednostka adresu wysyłkowego jest zawarta w podjednostce adresu ulicy, a następnie znalezienie podjednostki adres ulica dodaje znaczącą wagę do prognozowania jednostki adresów wysyłkowych.

* Adres wysyłkowy (jednostka uczenia maszynowego):

    * Numer ulicy (podjednostka)
    * Ulica (podjednostka)
    * Miasto (podjednostka)
    * Województwo (podjednostka)
    * Kraj/region (podjednostka)
    * Kod pocztowy (podjednostka)

## <a name="nested-subentities-with-features"></a>Zagnieżdżone podjednostki z funkcjami

Podjednostka uczenia maszynowego wskazuje, że koncepcja jest obecna dla jednostki nadrzędnej. Element nadrzędny może być inną podobiektem lub jednostką najwyższego poziomu. Wartość podjednostki działa jako funkcja do jej elementu nadrzędnego.

Podjednostka może mieć zarówno listę wyrazów, jak i model (inną jednostkę) jako funkcję.

Gdy podjednostka ma listę fraz, zwiększa słownictwo koncepcji, ale nie dodaje żadnych informacji do odpowiedzi JSON w prognozie.

Gdy podjednostka ma funkcję innej jednostki, odpowiedź JSON zawiera wyodrębnione dane tej jednostki.


## <a name="required-features"></a>Wymagane funkcje

Aby można było zwrócić model z punktu końcowego przewidywania, należy znaleźć wymaganą funkcję. Użyj wymaganej funkcji, gdy wiesz, że dane przychodzące muszą być zgodne z funkcją.

Jeśli tekst wypowiedź nie jest zgodny z wymaganą funkcją, nie zostanie wyodrębniony.

Wymagana funkcja używa jednostki uczenia maszynowego:

* Jednostka wyrażenia regularnego
* Jednostka listy
* Wstępnie utworzona jednostka

Jeśli masz pewność, że Twój model zostanie znaleziony w danych, ustaw tę funkcję zgodnie z wymaganiami. Wymagana funkcja nie zwraca żadnej wartości, jeśli nie została znaleziona.

Kontynuując przykład adresu wysyłkowego:

Adres wysyłkowy (poznanie maszynowe)

 * Numer ulicy (podjednostka)
 * Ulica (podjednostka)
 * Nazwa ulicy (podjednostka)
 * Miasto (podjednostka)
 * Województwo (podjednostka)
 * Kraj/region (podjednostka)
 * Kod pocztowy (podjednostka)

### <a name="required-feature-using-prebuilt-entities"></a>Wymagana funkcja przy użyciu wstępnie utworzonych jednostek

Miasto, Województwo i kraj/region są zwykle zamkniętym zestawem list, co oznacza, że nie zmieniają się znacznie w czasie. Te jednostki mogą mieć odpowiednie zalecane funkcje, a te funkcje mogą być oznaczone jako wymagane. Oznacza to, że cały adres wysyłkowy nie jest zwracany, jeśli nie można znaleźć jednostek, które mają wymagane funkcje.

Co się stanie, jeśli miasto, Województwo lub kraj/region znajdują się w wypowiedź, ale znajdują się one w lokalizacji lub żargonu, że LUIS nie jest oczekiwane? Jeśli chcesz udostępnić przetwarzanie końcowe, aby pomóc w rozwiązaniu jednostki, ze względu na wynik o niskim poziomie pewności od LUIS, nie zaznaczaj tej funkcji zgodnie z potrzebami.

Innym przykładem wymaganej funkcji adresu wysyłkowego jest numer ulicy wymagany, [wstępnie zbudowany](luis-reference-prebuilt-entities.md) numer. Dzięki temu użytkownik może wprowadzić wartość "1 Microsoft" lub "jeden Microsoft". Oba elementy są rozpoznawane jako cyfry "1" dla podjednostki numeru ulicy.

### <a name="required-feature-using-list-entities"></a>Wymagana funkcja przy użyciu jednostek listy

[Jednostka listy](reference-entity-list.md) jest używana jako lista nazw kanonicznych wraz z ich synonimami. Jako wymaganą funkcję, jeśli wypowiedź nie zawiera nazwy kanonicznej ani synonimu, jednostka nie jest zwracana jako część punktu końcowego przewidywania.

Załóżmy, że firma tylko napływa wyłącznie do ograniczonego zestawu krajów/regionów. Można utworzyć jednostkę listy, która zawiera kilka sposobów odwoływania się do kraju/regionu przez klienta. Jeśli LUIS nie znajdzie dokładnego dopasowania w tekście wypowiedź, jednostka (która ma wymaganą funkcję jednostki listy) nie jest zwracana w prognozie.

|Nazwa kanoniczna|Synonimy|
|--|--|
|Stany Zjednoczone|U.S.<br>U. S. A<br>USA<br>USA<br>0|

Aplikacja kliencka, taka jak rozmowa bot, może zadawać pytania dotyczące monitu, aby pomóc. Dzięki temu Klient może zrozumieć, że wybór kraju/regionu jest ograniczony i *wymagany*.

### <a name="required-feature-using-regular-expression-entities"></a>Wymagana funkcja przy użyciu jednostek wyrażenia regularnego

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) , która jest używana jako wymagana funkcja, zapewnia zaawansowane możliwości dopasowania tekstu.

W przykładzie adres wysyłkowy można utworzyć wyrażenie regularne, które przechwytuje reguły składni kodów pocztowych kraju/regionu.

## <a name="global-features"></a>Funkcje globalne

Chociaż najbardziej typowym zastosowaniem jest zastosowanie funkcji do określonego modelu, można skonfigurować funkcję jako **funkcję globalną** , aby zastosować ją do całej aplikacji.

Najbardziej typowym zastosowaniem funkcji globalnej jest dodanie do aplikacji dodatkowego słownika. Na przykład jeśli klienci korzystają z języka podstawowego, ale oczekuje się, że będzie można użyć innego języka w ramach tego samego wypowiedź, można dodać funkcję, która zawiera wyrazy z języka pomocniczego.

Ponieważ użytkownik oczekuje na użycie języka pomocniczego w dowolnym zamiarze lub jednostce, Dodaj wyrazy z języka dodatkowego do listy fraz. Skonfiguruj listę fraz jako funkcję globalną.

## <a name="combine-features-for-added-benefit"></a>Łączenie funkcji w celu dodania korzyści

Aby opisać cechę lub koncepcję, można użyć więcej niż jednej funkcji. Typowym parowaniem jest użycie:

* Funkcja listy fraz: można użyć wielu list fraz jako funkcji dla tego samego modelu.
* Model jako funkcja: [prekompilowana](luis-reference-prebuilt-entities.md)jednostka, [wyrażenie regularne](reference-entity-regular-expression.md), [Lista jednostek](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Przykład: funkcje jednostki rezerwacji biletów dla aplikacji podróży  

Jako podstawowy przykład rozważmy aplikację do rezerwacji lotu z _zamiarem_ rezerwacji lotu i _jednostką_rezerwacji biletów. Jednostka rezerwacja biletów przechwytuje informacje w celu zaksięgowania biletu samolotowego w systemie rezerwacji. 

Jednostka uczenia maszynowego dla księgi biletów ma dwie podjednostki, aby przechwycić lokalizację początkową i docelową. Funkcje należy dodać do każdej podjednostki, a nie jednostki najwyższego poziomu.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Schemat jednostki Ticketbooking":::

Jednostka rezerwacji biletów jest jednostką uczenia maszynowego, z podjednostkami, w tym _źródła_ i _miejsca docelowego_. Te podjednostki wskazują lokalizację geograficzną. Aby ułatwić wyodrębnienie lokalizacji i odróżnienie między elementami _początkowymi_ i _docelowymi_, każda podjednostka powinna mieć funkcje.

|Type|Podjednostka pochodzenie |Podjednostka docelowa|
|--|--|--|
|Model jako funkcja|wstępnie utworzona jednostka [geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3)|wstępnie utworzona jednostka [geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3)|
|Lista fraz|**Słowa źródłowe**: `start at` , `begin from` , `leave`|**Słowa docelowe**: `to` ,,,,, `arrive` `land at` `go` `going` `stay` , `heading`|
|Lista fraz|Kody portów lotniczych — ta sama lista dla źródła i miejsca docelowego|Kody portów lotniczych — ta sama lista dla źródła i miejsca docelowego|
|Lista fraz|Nazwy portów lotniczych — ta sama lista dla źródła i miejsca docelowego|Kody portów lotniczych — ta sama lista dla źródła i miejsca docelowego|

Jeśli przewidujesz, że osoby używają kodów portów lotniczych i nazw portów lotniczych, niż LUIS powinny mieć listy fraz używające obu typów fraz. Kody portów lotniczych mogą być bardziej typowe w przypadku tekstu wprowadzonego w chatbot, podczas gdy nazwy portów lotniczych mogą być bardziej popularne przy użyciu konwersacji mówionych, takich jak chatbot z obsługą mowy.

Pasujące Szczegóły funkcji są zwracane tylko dla modeli, nie dla list fraz, ponieważ w kodzie JSON przewidywania są zwracane tylko modele.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Rezerwacja biletów w zamiarach

Po utworzeniu jednostki uczenia maszynowego należy dodać przykład wyrażenia długości do zamiaru i oznaczyć jednostkę nadrzędną oraz wszystkie podjednostki.

W przypadku przykładowego rezerwacji biletu etykieta przykład wyrażenia długości w zamierzeniu z `TicketBooking` jednostką i podjednostkami w tekście.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Schemat jednostki Ticketbooking":::

### <a name="example-pizza-ordering-app"></a>Przykład: Pizza Porządkowanie aplikacji

W drugim przykładzie Rozważmy aplikację dla restauracji Pizza, która otrzymuje zamówienia Pizza, w tym szczegóły typu innej osoby. Każda Szczegółowa Pizza powinna zostać wyodrębniona, jeśli to możliwe, w celu ukończenia przetwarzania zamówień.

Jednostka uczenia maszynowego w tym przykładzie jest bardziej złożona z zagnieżdżonymi podjednostkami, listami fraz, wstępnie skompilowanymi jednostkami i jednostkami niestandardowymi.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Schemat jednostki Ticketbooking":::

W tym przykładzie są stosowane funkcje na poziomie podjednostki i element podrzędny elementu podrzędnego poziomu. Na poziomie tego, jakiego rodzaju Lista wyrazów lub model jako funkcja jest istotną częścią projektu jednostki.

Chociaż jednostki podjednostek mogą mieć wiele list fraz jako funkcje, które ułatwiają wykrycie jednostki, każda podjednostka ma tylko jeden model jako funkcję. W tej [aplikacji Pizza](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)te modele są wymieniane głównie.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Schemat jednostki Ticketbooking":::

Prawidłowo oznaczony przykład wyrażenia długości jest wyświetlany w sposób, aby pokazać, jak jednostki są zagnieżdżone. 


## <a name="best-practices"></a>Najlepsze rozwiązania

Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

* [Zwiększaj](schema-change-prediction-runtime.md) modele aplikacji w środowisku uruchomieniowym przewidywania.
* Zobacz [Dodawanie funkcji](luis-how-to-add-features.md) , aby dowiedzieć się więcej na temat dodawania funkcji do aplikacji Luis.
