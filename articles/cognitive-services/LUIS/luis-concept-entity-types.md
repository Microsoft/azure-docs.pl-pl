---
title: Typy jednostek — LUIS
description: Jednostka wyodrębnia dane z wypowiedzi użytkownika w czasie wykonywania przewidywania. _Opcjonalnym_ celem pomocniczym jest zwiększenie przewidywania intencji lub innych jednostek przy użyciu jednostki jako funkcji.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500332"
---
# <a name="entities-in-luis"></a>Jednostki w u usługi LUIS

Jednostka jest elementem lub elementem, który jest istotny dla intencji użytkownika. Jednostki definiują dane, które można wyodrębnić z wypowiedzi i są niezbędne do wykonania wymaganej akcji użytkownika. Na przykład:

|Wypowiedź|Przewidywana intencja|Wyodrębnione jednostki|Wyjaśnienie|
|--|--|--|--|
|Witaj jak się masz?|Powitanie|-|Nic do wyodrębnienia.|
|Chcę zamówić małą pizzę|orderPpunga| "small" (mały) | Jednostka "Size" jest wyodrębniona jako "mała".|
|Wyłącz światło światła|Zjazd| "więzło" | Jednostka "Room" jest wyodrębniona jako "więzło".|
|Sprawdzanie salda na koncie oszczędnościowym kończącym się na 4406|checkBalance| "oszczędności", "4406" | Jednostka "accountType" jest wyodrębniona jako "oszczędności", a jednostka "accountNumber" jako "4406".|
|Kup 3 bilety do Nowego Jorku|buyTickets| "3", "New York" | Jednostka "ticketsCount" jest wyodrębniona jako "3", a jednostka "Destination" jako "Nowy Jork".|

Jednostki są opcjonalne, ale zalecane. Nie musisz tworzyć jednostek dla każdej koncepcji w aplikacji, tylko dla tych, w których:

* Aplikacja kliency potrzebuje danych lub 
* Jednostka działa jako wskazówka lub sygnał dla innej jednostki lub intencji. Aby dowiedzieć się więcej na temat jednostek jako funkcji, przejdź do [tematu Jednostki jako funkcje.](#entities-as-features)

## <a name="entity-types"></a>Typy jednostek

Aby utworzyć jednostkę, musisz nadać jej nazwę i typ. Usługa LUIS ma kilka typów jednostek. 

### <a name="list-entity"></a>Lista jednostek

Jednostka listy reprezentuje stały, zamknięty zestaw powiązanych wyrazów wraz z ich synonimami. Za pomocą jednostek listy można rozpoznawać wiele synonimów lub odmian i wyodrębniać dla nich znormalizowane dane wyjściowe. Użyj opcji *rekomendacji,* aby wyświetlić sugestie dotyczące nowych wyrazów na podstawie bieżącej listy. 

Jednostka listy nie jest uczony maszynowo, co oznacza, że usługa LUIS nie odnajduje dodatkowych wartości dla jednostek listy. Usługa LUIS oznacza dowolne dopasowanie do elementu na dowolnej liście jako jednostkę w odpowiedzi.

W dopasowywaniu w jednostkach listy jest rozróżniana wielkość liter i musi być dokładnie dopasowana do wyodrębnienia. Znormalizowane wartości są również używane podczas dopasowywania jednostki listy. Na przykład:

|Znormalizowana wartość|Synonimy|
|--|--|
|Mały|sm, sml, tiny, smallest|
|Śred.|md, mdm, regular, average, middle|
|Duży|lg, lrg, big|

Aby uzyskać więcej informacji, zobacz artykuł z [informacjami o](reference-entity-list.md) jednostkach listy.

### <a name="regex-entity"></a>Jednostka wyrażenia regularnego

Jednostka wyrażenia regularnego wyodrębnia jednostkę na podstawie owego wzorca wyrażenia regularnego. Ignoruje on przypadek i ignoruje wariant kulturowy. Wyrażenie regularne jest najlepsze w przypadku tekstu strukturalnego lub wstępnie zdefiniowanej sekwencji wartości alfanumerycznych, które są oczekiwane w określonym formacie. Na przykład:

|Jednostka|Wyrażenie regularne|Przykład|
|--|--|--|
|Numer lotu|flight [A–Z] {2} [0–9]{4}| flight AS 1234|
|Numer karty kredytowej|[0-9]{16}|5478789865437632|

Aby uzyskać więcej informacji, zobacz artykuł [referencyjny jednostki wyrażenia](reference-entity-regular-expression.md) regularnego.

### <a name="prebuilt-entity"></a>Wstępnie utworzona jednostka

Usługa LUIS oferuje zestaw wstępnie utworzonych jednostek do rozpoznawania typowych typów danych, takich jak nazwa, data, liczba i waluta.  Zachowanie wstępnie utworzonych jednostek jest stałe. Obsługa wstępnie utworzonych jednostek różni się w zależności od kultury aplikacji LUIS. Na przykład:

|Wstępnie sprecyzowany obiekt|Przykładowa wartość|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2 maja 8:00 w dniu 2 maja 2019 r.|

Aby uzyskać [więcej informacji,](./luis-reference-prebuilt-entities.md) zobacz artykuł referencyjny na temat wstępnie utworzonych jednostek.

### <a name="patternany-entity"></a>Pattern.Any, jednostka

Wzorzec. Każda jednostka jest symbolem zastępczym o zmiennej długości używanym tylko w wypowiedzi szablonu wzorca do oznaczania, gdzie jednostka rozpoczyna się i kończy. Jest ona zgodna z określoną regułą lub wzorcem i najlepiej jest jej używać w przypadku zdań o stałej strukturze leksykacznej. Na przykład:

|Przykładowa wypowiedź|Wzorce|Jednostka|
|--|--|--|
|Czy mogę mieć problem?|Czy mogę mieć {jedzenie} [please][?]| Burger
|Czy mogę mieć pizzę?|Czy mogę mieć {jedzenie} [please][?]| Pizza
|Gdzie mogę znaleźć doskonałego Gatsby?|Gdzie mogę znaleźć {bookName}?| The Great Gatsby|

Aby uzyskać więcej informacji, zobacz artykuł z informacjami o jednostkach [Pattern.Any.](./reference-entity-pattern-any.md)

### <a name="machine-learned-ml-entity"></a>Jednostka uczenia maszynowego

Jednostka uczenia maszynowego używa kontekstu do wyodrębniania jednostek na podstawie przykładów oznaczonych etykietami. Jest to preferowana jednostka do tworzenia aplikacji usługi LUIS. Opiera się on na algorytmach uczenia maszynowego i wymaga pomyślnego dostosowania etykietowania do aplikacji. Użyj jednostki ML, aby zidentyfikować dane, które nie zawsze są dobrze sformatowane, ale mają takie samo znaczenie. 

|Przykładowa wypowiedź|Wyodrębniona *jednostka* produktu|
|--|--|
|Chcę kupić książkę.|"book"|
|Czy mogę uzyskać te buty?|"shoes"|
|Dodaj te krótkie szorty do koszyka.|"shorts"|

Więcej informacji na temat jednostek uczonych przez maszynę można [znaleźć tutaj.](./reference-entity-machine-learned-entity.md)

Aby uzyskać więcej informacji, zobacz artykuł [z informacjami](./reference-entity-pattern-any.md) o jednostkach uczenia maszynowego.

#### <a name="ml-entity-with-structure"></a>Jednostka ML ze strukturą

Jednostka ML może składać się z mniejszych jednostek podrzędnych, z których każda może mieć własne właściwości. Na przykład *adres może* mieć następującą strukturę:

* Adres: 4567 Main Street, NY, 98052, USA
    * Numer budynku: 4567
    * Nazwa ulicy: Main Street
    * Stan: NY
    * Kod pocztowy: 98052
    * Kraj: USA


### <a name="building-effective-ml-entities"></a>Tworzenie skutecznych jednostek uczenia maszynowego

Aby skutecznie tworzyć jednostki uczonych maszynami, postępuj zgodnie z tymi najlepszymi rozwiązaniami:

* Jeśli masz jednostkę uczenia maszynowego z jednostkami podrzędnym, upewnij się, że różne zamówienia i warianty jednostki i jednostek podrzędnych są przedstawione w oznaczonych wypowiedziach. Oznaczone przykładowe wypowiedzi powinny zawierać wszystkie prawidłowe formularze i zawierać jednostki, które pojawiają się i są nieobecne, a także zmieniać kolejność w wypowiedzi.

* Unikaj nadpasowania jednostek do bardzo stałego zestawu. Overfitting występuje, gdy model nie generalizuje się dobrze i jest powszechnym problemem w modelach uczenia maszynowego. Oznacza to, że aplikacja nie będzie odpowiednio działać w przypadku nowych typów przykładów. Z kolei należy różnić przykładowe wypowiedzi oznaczone etykietami, aby aplikacja może uogólniać poza ograniczonymi liczbami przykładów, które podajesz.

* Etykietowanie powinno być spójne w intencjach. Obejmuje to nawet wypowiedzi zapewniane w intencji *None,* która zawiera tę jednostkę. W przeciwnym razie model nie będzie mógł efektywnie określić sekwencji.

## <a name="entities-as-features"></a>Jednostki jako funkcje

Inną ważną funkcją jednostek jest używanie ich jako cech lub cech wyróżniających dla innych intencji lub jednostek, tak aby system obserwował je i poznał.

### <a name="entities-as-features-for-intents"></a>Jednostki jako funkcje dla intencji

Jednostek można używać jako sygnału dla intencji. Na przykład obecność określonej jednostki w wypowiedzi może rozróżnić intencję, do której należy.

|Przykładowa wypowiedź|Jednostka|Zamiar|
|--|--|--|
|Zarezerwuj mi *walkę do Nowego Jorku.*|City (Miasto)|Book Flight|
|Zarezerwuj mi główną *salę konferencyjną.*|Pokojach|Zarezerwowany pokój|

### <a name="entities-as-feature-for-entities"></a>Jednostki jako funkcja dla jednostek

Możesz również użyć jednostek jako wskaźnika obecności innych jednostek. Częstym przykładem jest użycie wstępnie utworzonej jednostki jako funkcji dla innej jednostki uczenia maszynowego.
Jeśli używasz systemu rezerwacji lotów i Twoja wypowiedź wygląda następująco: "Book  me a  flight from Seattle to Seattle" (Zarezerwuj mi lot z Seattle do Seattle), będziesz mieć miasto początkowe i docelowe jako jednostki uczenia maszynowego. Dobrym rozwiązaniem jest użycie wstępnie utworzonej jednostki `GeographyV2` jako funkcji dla obu jednostek.

Aby uzyskać więcej informacji, zobacz artykuł [GeographyV2 entities reference](./luis-reference-prebuilt-geographyv2.md) (Informacje o jednostkach GeographyV2).

Możesz również użyć jednostek jako wymaganych funkcji dla innych jednostek. Pomaga to w rozwiązywaniu wyodrębnianych jednostek. Jeśli na przykład tworzysz aplikację do zamawiania pizzy i masz jednostkę ML, możesz utworzyć jednostkę listy i użyć jej jako funkcji wymaganej `Size` `SizeList` dla `Size` jednostki. Aplikacja zwróci znormalizowaną wartość jako wyodrębnione jednostki z wypowiedzi. 

Zobacz [funkcje,](luis-concept-feature.md) aby [](./luis-reference-prebuilt-entities.md) uzyskać więcej informacji, oraz wstępnie utworzone jednostki, aby dowiedzieć się więcej na temat rozpoznawania wstępnie utworzonych jednostek dostępnych w Twojej kulturze. 


## <a name="entity-prediction-status-and-errors"></a>Stan i błędy przewidywania jednostek

Portal usługi LUIS pokazuje następujące dane, gdy jednostka ma inne przewidywanie jednostki niż jednostka oznaczona jako przykładowa wypowiedź. Ten inny wynik jest oparty na bieżącym wytrenowany model. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Portal usługi LUIS pokazuje, kiedy jednostka ma inne przewidywanie jednostki niż jednostka wybrana dla przykładowej wypowiedzi":::

Tekst powodujący błąd jest wyróżniany w przykładowej wypowiedzi, a przykładowa linia wypowiedzi ma wskaźnik błędu po prawej stronie, pokazany jako czerwony trójkąt. 

Aby usunąć błędy jednostek, spróbuj wykonać co najmniej jedną z następujących czynności:

* Wyróżniony tekst ma błędną etykietę. Aby rozwiązać ten problem, przejrzyj etykietę, popraw ją i ponownie przeszkolij aplikację. 
* Utwórz [funkcję dla](luis-concept-feature.md) jednostki, aby ułatwić identyfikację koncepcji jednostki.
* Dodaj więcej [przykładowych wypowiedzi i etykiety](luis-concept-utterance.md) za pomocą jednostki .
* [Przejrzyj sugestie dotyczące uczenia aktywnego](luis-concept-review-endpoint-utterances.md) dla wszystkich wypowiedzi odebranych w punkcie końcowym przewidywania, które mogą pomóc w zidentyfikowaniu koncepcji jednostki.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [dobrych przykładowych wypowiedziach.](luis-concept-utterance.md)
* Zobacz [Dodawanie jednostek,](luis-how-to-add-entities.md) aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji usługi LUIS.
* Dowiedz się więcej o [limitach aplikacji usługi](./luis-limits.md)LUIS. 
* Skorzystaj z [samouczka,](tutorial-machine-learned-entity.md) aby dowiedzieć się, jak wyodrębnić ustrukturyzowane dane z wypowiedzi przy użyciu jednostki uczenia maszynowego.
