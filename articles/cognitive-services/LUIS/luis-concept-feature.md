---
title: Funkcje — LUIS
description: Dodaj funkcje do modelu języka, aby przedstawić wskazówki dotyczące sposobu rozpoznawania danych wejściowych, które mają być oznaczone etykietami lub klasyfikacją.
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 906876e39eb7ff31c2e6b954d1514d8afc50bf3a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591900"
---
# <a name="machine-learning-ml-features"></a>Funkcje uczenia maszynowego (ML)

W uczeniu maszynowym **Funkcja**   jest odróżnianą cechą lub atrybutem danych, które są przestrzegane przez system.

Funkcje uczenia maszynowego zapewniają LUIS ważne wskaźniki dotyczące lokalizacji, w których należy odróżnić koncepcje. Są to wskazówki, których LUIS mogą używać, ale nie są trudne.  Te wskazówki są używane w połączeniu z etykietami w celu znalezienia danych.

 LUIS obsługuje obie listy fraz i używa innych jednostek jako funkcji:
* Funkcja listy fraz
* Model (cel lub jednostka) jako funkcja

Funkcje powinny być uważane za niezbędne części projektu schematu.

## <a name="a-phrase-list-for-a-particular-concept"></a>Lista fraz dla konkretnej koncepcji

Lista fraz jest listą słów lub fraz, które hermetyzują konkretną koncepcję.

Podczas dodawania listy fraz można ustawić tę funkcję jako:
* **[Globalne](#global-features)**. Globalna funkcja ma zastosowanie do całej aplikacji.

### <a name="when-to-use-a-phrase-list"></a>Kiedy używać listy fraz

Jeśli potrzebujesz aplikacji LUIS, aby móc uogólniać i identyfikować nowe elementy koncepcji, Użyj listy fraz. Listy fraz są podobne do słownictwa specyficznego dla domeny, który ułatwia zwiększenie jakości wiedzy o intencjach i jednostkach.

### <a name="how-to-use-a-phrase-list"></a>Jak używać listy fraz

Za pomocą listy fraz LUIS traktuje kontekst i generalizacje w celu zidentyfikowania elementów, które są podobne do, ale nie dokładnego dopasowania tekstu.

Procedura używania listy fraz:
* Rozpocznij pracę z jednostką uczenia maszynowego
    * Dodawanie przykładowych wypowiedzi
    * Etykieta z jednostką uczenia maszynowego
* Dodawanie listy fraz
    * Dodaj wyrazy z podobnym znaczeniem — **nie** dodawaj każdego wyrazu lub frazy. Zamiast tego Dodaj kilka wyrazów lub fraz jednocześnie, a następnie ponów próbę i Opublikuj.
    * Przeglądanie i Dodawanie sugerowanych wyrazów

### <a name="a-typical-scenario-for-a-phrase-list"></a>Typowy scenariusz dla listy fraz

Typowym scenariuszem dla listy fraz jest zwiększenie wyrazów związanych z konkretnym pomysłem.

Przykład słów, które mogą potrzebować listy frazy, aby zwiększyć ich znaczenie, to warunki medyczne. Warunki mogą mieć określone znaczenie fizyczne, chemiczne, terapeutyczne lub abstrakcyjne. LUIS nie wie, że warunki są ważne dla domeny podmiotu bez listy fraz.

Jeśli chcesz wyodrębnić warunki medyczne:
* Najpierw utwórz przykład wyrażenia długości i Oznacz warunki medyczne w tych wyrażenia długości.
* Następnie utwórz listę fraz z przykładami warunków w domenie podmiotu. Ta lista wyrażeń powinna zawierać rzeczywisty termin oznaczony etykietą i inne terminy opisujące te same koncepcje.
* Dodaj listę fraz do jednostki lub podjednostki, która wyodrębnia koncepcję używaną na liście fraz. Najbardziej typowym scenariuszem jest składnik (podrzędny) jednostki, która jest obsługiwana przez maszynę. Jeśli lista fraz powinna być stosowana dla wszystkich intencji lub jednostek, Oznacz listę fraz jako globalną listę fraz. `enabledForAllModels`Flaga steruje zakresem tego modelu w interfejsie API.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Model jako funkcja ułatwia inny model

Model (cel lub jednostka) można dodać jako funkcję do innego modelu (zamiaru lub jednostki). Dodanie istniejącego zamiaru lub jednostki jako funkcji oznacza dodanie dobrze zdefiniowanej koncepcji z przykładami z etykietami.

Podczas dodawania modelu jako funkcji można ustawić tę funkcję jako:
* **[Wymagane](#required-features)**. Aby można było zwrócić model z punktu końcowego przewidywania, należy znaleźć wymaganą funkcję.
* **[Globalne](#global-features)**. Globalna funkcja ma zastosowanie do całej aplikacji.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Kiedy używać jednostki jako funkcji do zamiaru

Dodaj jednostkę jako funkcję do celu, gdy wykrycie tej jednostki jest istotne dla zamiaru.

Na przykład, jeśli zachodzi taka potrzeba w przypadku rezerwacji, `BookFlight` a jednostka jest informacji o biletach (takich jak liczba miejsc, pochodzenia i miejsce docelowe), znalezienie jednostki informacji o biletach powinno zwiększyć znaczącą wagę do przewidywania `BookFlight` zamiaru.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Kiedy używać jednostki jako funkcji dla innej jednostki

Jednostkę (A) należy dodać jako funkcję do innej jednostki (B), gdy wykrycie tej jednostki (A) jest istotne dla przewidywania jednostki (B).

Na przykład, jeśli n jednostka adresu wysyłkowego zawiera podjednostkę adresu ulicy, a następnie znalezienie podjednostki ulica adresu dodaje znaczącą wagę do prognozowania jednostki adres wysyłkowy.

* Adres wysyłkowy (poznanie maszynowe)
    * Numer ulicy (podjednostka)
    * Ulica (podjednostka)
    * Miasto (podjednostka)
    * Województwo (podjednostka)
    * Kraj (podjednostka)
    * Kod pocztowy (podjednostka)

## <a name="required-features"></a>Wymagane funkcje

Aby można było zwrócić model z punktu końcowego przewidywania, należy znaleźć wymaganą funkcję. Użyj wymaganej funkcji, gdy wiesz, że dane przychodzące muszą być zgodne z funkcją.

**Wymagana funkcja używa jednostki, która nie jest zastosowana przez maszynę**:
* Jednostka wyrażenia regularnego
* Jednostka listy
* Wstępnie utworzona jednostka

Jakie są dobre funkcje do oznaczenia jako wymagane? Jeśli masz pewność, że Twój model zostanie znaleziony w danych, ustaw tę funkcję zgodnie z wymaganiami. Wymagana funkcja nie zwraca żadnego elementu, jeśli nie zostanie znaleziona.

Kontynuując przykład adresu wysyłkowego:
* Adres wysyłkowy (poznanie maszynowe)
    * Numer ulicy (podjednostka)
    * Ulica (podjednostka)
    * Nazwa ulicy (podjednostka)
    * Miasto (podjednostka)
    * Województwo (podjednostka)
    * Kraj (podjednostka)
    * Kod pocztowy (podjednostka)

### <a name="required-feature-using-prebuilt-entities"></a>Wymagana funkcja przy użyciu wstępnie utworzonych jednostek

Miasto, Województwo i kraj są zwykle zamkniętym zestawem list, co oznacza, że nie zmieniają się znacznie w czasie. Te jednostki mogą mieć odpowiednie zalecane funkcje, a te funkcje mogą być oznaczone jako wymagane. Oznacza to, że cały adres wysyłkowy nie jest zwracany, ponieważ nie znaleziono jednostek z wymaganymi funkcjami.

Co się stanie, jeśli miasto, Województwo lub kraj znajdują się w wypowiedź, ale w lokalizacji lub żargonu, które nie są oczekiwane przez LUIS? Jeśli chcesz udostępnić przetwarzanie końcowe, aby pomóc w rozwiązaniu jednostki, ze względu na wynik o niskim poziomie pewności od LUIS, nie zaznaczaj tej funkcji zgodnie z potrzebami.

Innym przykładem wymaganej funkcji adresu dostawy jest numer [ulicy wymagany jako numer domu](luis-reference-prebuilt-entities.md) . Dzięki temu użytkownik może wprowadzić wartość "1 Microsoft" lub "jeden Microsoft". Oba te rozpoznają się z liczbą "1" dla podjednostki z numerem ulicy.

### <a name="required-feature-using-list-entities"></a>Wymagana funkcja przy użyciu jednostek listy

[Jednostka listy](reference-entity-list.md) jest używana jako lista nazw kanonicznych wraz z ich synonimami. Jako wymaganą funkcję, jeśli wypowiedź nie zawiera nazwy kanonicznej ani synonimu, jednostka nie jest zwracana jako część punktu końcowego przewidywania.

Kontynuując na przykład adres wysyłkowy, Załóżmy, że firma tylko napływa wyłącznie do ograniczonego zestawu krajów. Można utworzyć jednostkę listy, która zawiera kilka sposobów, w których klient może odwoływać się do kraju. Jeśli LUIS nie znajdzie dokładnego dopasowania w tekście wypowiedź, jednostka (która ma wymaganą funkcję jednostki listy) nie jest zwracana w prognozie.

|Nazwa kanoniczna|Synonimy|
|--|--|
|Stany Zjednoczone|U.S.<br>U. S. A<br>USA<br>Stany Zjednoczone<br>0|

Aplikacja kliencka, taka jak rozmowa bot, może zadawać pytania, aby Klient wiedział, że zaznaczenie kraju jest ograniczone i _wymagane_.

### <a name="required-feature-using-regular-expression-entities"></a>Wymagana funkcja przy użyciu jednostek wyrażenia regularnego

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) używana jako wymagana funkcja zapewnia zaawansowane możliwości dopasowania tekstu.

Kontynuując adres wysyłkowy, można utworzyć wyrażenie regularne, które przechwytuje reguły składni kodów pocztowych kraju.

## <a name="global-features"></a>Funkcje globalne

Chociaż najbardziej typowym zastosowaniem jest zastosowanie funkcji do określonego modelu, można skonfigurować funkcję jako **funkcję globalną** , aby zastosować ją do całej aplikacji.

Najbardziej typowym zastosowaniem funkcji globalnej jest dodanie do aplikacji dodatkowego słownika, takiego jak wyrazy z innego języka. Jeśli klienci korzystają z języka podstawowego, ale oczekujesz, że będzie można użyć innego języka w ramach tego samego wypowiedź, możesz dodać funkcję, która zawiera wyrazy z języka pomocniczego.

Ponieważ użytkownik oczekiwał na użycie drugiego języka w dowolnym zamiarze lub jednostce, należy dodać go do listy fraz z listą fraz skonfigurowany jako funkcja globalna.

## <a name="best-practices"></a>Najlepsze rozwiązania
Poznaj [najlepsze rozwiązania](luis-concept-best-practices.md).

## <a name="next-steps"></a>Następne kroki

* [Zwiększanie](schema-change-prediction-runtime.md) modeli aplikacji w środowisku uruchomieniowym przewidywania
* Zobacz [Dodawanie funkcji](luis-how-to-add-features.md) , aby dowiedzieć się więcej na temat dodawania funkcji do aplikacji Luis.
