---
title: 'Samouczek: wyodrębnianie danych strukturalnych za pomocą jednostki uczenia maszynowego — LUIS'
description: Wyodrębnij dane strukturalne z wypowiedź przy użyciu jednostki Uczenie maszynowe. Aby zwiększyć dokładność wyodrębniania, Dodaj podjednostki z funkcjami.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: 01262ee0271849793c4393b1ea8e18c4179ad4e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334734"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Samouczek: wyodrębnianie danych strukturalnych z wypowiedź użytkownika przy użyciu jednostek uczenia maszynowego w Language Understanding (LUIS)

W tym samouczku Wyodrębnij dane strukturalne z wypowiedź przy użyciu jednostki Uczenie maszynowe.

Jednostka uczenia maszynowego obsługuje [koncepcję rozkładu modelu](luis-concept-model.md#v3-authoring-model-decomposition) przez zapewnienie jednostkowych jednostek z [funkcjami](luis-concept-feature.md).

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Dodaj jednostkę uczenia maszynowego
> * Dodaj podjednostkę i funkcję
> * Uczenie, testowanie i publikowanie aplikacji
> * Pobierz prognozowanie jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Dlaczego warto używać jednostki uczenia maszynowego?

W tym samouczku dodano jednostkę uczenia maszynowego w celu wyodrębnienia danych z wypowiedź użytkownika.

Jednostka definiuje dane do wyodrębnienia z wypowiedź. Obejmuje to nadanie danych nazwy, typu (jeśli to możliwe), dowolnej rozdzielczości danych, jeśli występuje niejednoznaczność i dokładny tekst, który tworzy dane.

W celu zdefiniowania danych należy wykonać następujące:
* Tworzenie jednostki
* Oznacz tekst etykietą w przykładzie wyrażenia długości, reprezentującą jednostkę. Te przykładowe przykłady uczyją LUIS, czym jest jednostka i gdzie można ją znaleźć w wypowiedź.

## <a name="entity-decomposability-is-important"></a>Odtworzenie jednostek jest ważne

Odtworzenie jednostek jest ważne w przypadku prognozowania intencji oraz do wyodrębniania danych z jednostką.

Zacznij od jednostki uczenia maszynowego, która jest jednostką początkową i najwyższego poziomu na potrzeby wyodrębniania danych. Następnie rozłożyć jednostkę na podjednostki.

Mimo że użytkownik może nie wiedzieć, w jaki sposób chcemy, aby dana jednostka rozpoczęła swoją aplikację, najlepszym rozwiązaniem jest rozpoczęcie pracy z jednostką uczenia maszynowego, a następnie rozłożenia z podjednostkami w miarę dojrzewania aplikacji.

W tym samouczku utworzysz jednostkę uczenia maszynowego, która będzie reprezentować zamówienie aplikacji Pizza. Jednostka wyodrębni tekst związany z kolejnością, pobierając rozmiar i ilość.

Wypowiedź `Please deliver one large cheese pizza to me` powinien zostać wyodrębniony `one large cheese pizza` jako kolejność, a następnie wyodrębniony `1` dla ilości i `large` rozmiaru.

## <a name="download-json-file-for-app"></a>Pobierz plik JSON dla aplikacji

Pobierz i Zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>Importuj plik JSON dla aplikacji

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Tworzenie jednostki o Poznaniu maszyn

Aby wyodrębnić szczegółowe informacje o Pizza kolejności, Utwórz jednostkę uczenia maszynowego `Order` .

1. Na stronie **intencje** wybierz zamiar **OrderPizza** .

1. Na liście przykład wyrażenia długości wybierz następujące wypowiedź.

    |Przykład kolejności wypowiedź|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Zacznij wybierać tuż przed lewym tekstem `pickup` (#1), a następnie przejdź bezpośrednio poza prawym przyciskiem myszy `anchovies` (#2 — to kończy proces etykietowania). Zostanie wyświetlone menu podręczne. W oknie podręcznym wprowadź nazwę jednostki jako `Order` (#3). Następnie wybierz `Order Create new entity` z listy (#4).

    ![Etykieta początku i końca tekstu dla kompletnego zamówienia](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Jednostka nie zawsze będzie całą wypowiedź. W tym konkretnym przypadku `pickup` wskazuje, w jaki sposób zamówienie ma zostać odebrane. Z perspektywy koncepcyjnej `pickup` powinna być częścią oznaczonej jednostki dla zamówienia.

1. W polu **Wybierz typ jednostki** wybierz pozycję **Dodaj strukturę** , a następnie wybierz przycisk **dalej**. Struktura jest niezbędna do dodawania podjednostek, takich jak rozmiar i ilość.

    ![Zrzut ekranu przedstawia okno Wybierz typ jednostki z zaznaczoną opcją Dodaj strukturę.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. W polu **Dodaj podjednostki (opcjonalnie)** wybierz **+** `Order` wiersz, a następnie Dodaj `Size` i `Quantity` jako podjednostki, a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia okno Dodawanie podjednostek (opcjonalnie) z wyróżnionymi podjednostkami.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Edytuj podjednostki w celu usprawnienia ekstrakcji

Poprzednie kroki tworzą jednostkę i podjednostkę. Aby ulepszyć wyodrębnianie, Dodaj funkcje do podjednostek.

### <a name="improve-size-extraction-with-phrase-list"></a>Popraw rozmiar wyodrębniania z listą fraz

1. Z menu po lewej stronie wybierz pozycję **jednostki** , a następnie wybierz pozycję **Zamówienie** jednostki.

1. Na karcie **schemat i funkcje** wybierz podjednostkę **rozmiaru** , a następnie wybierz pozycję **+ Dodaj funkcję**.

1. Wybierz pozycję **Utwórz nową listę fraz** z menu rozwijanego.

1. W polu **Utwórz nową frazę** wprowadź nazwę, a `SizePhraselist` następnie wprowadź wartości: `small` , `medium` , i `large` . Po wypełnieniu pola **sugestie** wybierz pozycję `extra large` i `xl` . Wybierz pozycję **Utwórz** , aby utworzyć nową listę fraz.

    Ta funkcja listy frazy ułatwia `Size` znalezienie wyrazów związanych z rozmiarem, podając Przykładowe słowa. Ta lista fraz nie musi zawierać każdego wyrazu o rozmiarze, ale powinna zawierać słowa, które powinny wskazywać rozmiar.

### <a name="add-sizelist-entity"></a>Dodaj jednostkę SizeList

Dodawanie listy znanych rozmiarów rozpoznawanych przez aplikację kliencką ułatwi również wyodrębnianie.

1. Wybierz pozycję **jednostki** w menu po lewej stronie, a następnie wybierz pozycję **+ Utwórz**.

1. Ustaw nazwę jednostki jako `SizeListentity` i ustaw typ jako **listę** , tak aby można ją było łatwo identyfikować w porównaniu z definicją `SizePhraselist` utworzoną w poprzedniej sekcji.

1. Dodaj rozmiary oczekiwane przez aplikację kliencką: `Small` , `Medium` ,, `Large` a `XLarge` następnie Dodaj synonimy dla każdego z nich. Synonimami powinny być warunki wprowadzane przez użytkownika w bot rozmowy. Jednostka jest wyodrębniana z jednostki listy, gdy pasuje do znormalizowanej wartości lub synonimów.

    |Znormalizowana wartość|Synonimy|
    |--|--|
    |Mała|SM, SML, mały, najmniejszy|
    |Średniaa|MD, MDM, regularne, średnie, środkowe|
    |Duża|LG, LRG, Big|
    |XLarge|XL, największe, bardzo duże|


    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia okno SizeList i elementy listy z wybranym XLarge.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Dodawanie funkcji jednostki SizeList

1. Wybierz pozycję **jednostki** z menu po lewej stronie, aby powrócić do listy jednostek.

1. Wybierz pozycję **Zamówienie** z listy jednostek.

1. Na karcie **schemat i funkcje** wybierz jednostkę **rozmiaru** , a następnie wybierz pozycję **+ Dodaj funkcję**.

1. Z listy rozwijanej wybierz pozycję **@ SizeListentity** .

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Dodaj wstępnie utworzoną jednostkę Number

Dodanie wbudowanej jednostki numeru również pomoże w wyodrębnieniu.

1. Wybierz pozycję **jednostki** z menu po lewej stronie, a następnie wybierz pozycję **Dodaj prekompilowaną jednostkę**.

1. Z listy wybierz pozycję **Liczba** , a następnie wybierz pozycję **gotowe**.

1. Wybierz pozycję **jednostki** z menu po lewej stronie, aby powrócić do listy jednostek.

### <a name="add-feature-of-prebuilt-number-entity"></a>Dodaj funkcję wbudowanej jednostki numeru

1. Wybierz pozycję **Zamówienie** z listy jednostek.

1. Na karcie **schemat i funkcje** wybierz jednostkę **ilości** , a następnie wybierz pozycję **+ Dodaj funkcję**.

1. Z listy rozwijanej wybierz pozycję **@ Number** .

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Skonfiguruj wymagane funkcje

Na stronie szczegółów jednostki dla jednostki **Order (kolejność** ) Wybierz gwiazdkę, `*` dla funkcji **@ SizeList** i funkcji **@ Number** . Gwiazdka pojawia się w tej samej etykiecie co nazwa funkcji.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazuje @SizeList funkcję z gwiazdką i wymaga ostrzeżenia.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Przykład etykiety wyrażenia długości

Tworzona jest utworzona przez maszynę jednostka, a podjednostki mają funkcje. Aby ukończyć ulepszanie wyodrębniania, przykład wyrażenia długości musi być oznaczony przy użyciu podjednostek.

1. Wybierz pozycję **intencje** na lewym pasku nawigacyjnym, a następnie wybierz opcję **OrderPizza** .

1. Aby otworzyć **paletę Entity**, zaznacz **@** symbol na pasku narzędzi kontekstowych.

1. Zaznacz każdy wiersz jednostki w palecie, a następnie użyj kursora palety, aby wybrać jednostkę w każdym przykładowym wypowiedź. Po zakończeniu Lista jednostek powinna wyglądać podobnie do poniższej ilustracji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Konfigurowanie wymaganej funkcji](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Uczenie aplikacji

Aby nauczyć aplikację, wybierz pozycję **pouczenie**. Szkolenie stosuje zmiany, takie jak nowe jednostki i etykiety wyrażenia długości, do aktywnego modelu.

## <a name="add-a-new-example-utterance"></a>Dodawanie nowego przykładu wypowiedź

1. Po szkoleniu Dodaj nowy przykład wypowiedź do `OrderPizza` zamiaru, aby zobaczyć, jak dobrze Luis rozumie jednostkę uczenia maszynowego.

    |Przykład kolejności wypowiedź|
    |--|
    |`I need a large pepperoni pizza`|

    Ogólna Górna jednostka `Order` ma etykietę, a `Size` podjednostka jest również oznaczona linią kropkowaną.

    > [!div class="mx-imgBorder"]
    > ![Częściowy zrzut ekranu przedstawiający nowy przykład wypowiedź przewidywany w jednostce](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Linia kropkowana wskazuje prognozowanie na podstawie aktualnie przeszkolonej aplikacji.

1. Aby zmienić prognozowanie na jednostkę oznaczoną etykietą, zaznacz znacznik wyboru w tym samym wierszu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia przykład wypowiedź z wyróżnionym znacznikiem wyboru.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    W tym momencie jednostka uczenia maszynowego działa, ponieważ może znaleźć jednostkę w ramach nowego przykładu wypowiedź. Po dodaniu przykładu wyrażenia długości, jeśli jednostka nie jest przewidywalna prawidłowo, Oznacz jednostkę i podjednostki. Jeśli jednostka jest przewidywalna prawidłowo, upewnij się, że są one potwierdzone.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Uczenie aplikacji w celu zastosowania zmian jednostek w aplikacji

Wybierz pozycję **uczenie** , aby nauczyć aplikację przy użyciu tego nowego wypowiedź.

W tym momencie kolejność zawiera pewne szczegóły, które mogą zostać wyodrębnione (rozmiar, ilość i tekst zamówienia). Dalsze poprawianie `Order` jednostki, takiej jak Pizza toppings, Type of Crust i Orders. Każdy z tych elementów należy utworzyć jako podjednostki `Order` jednostki.

## <a name="test-the-app-to-validate-the-changes"></a>Przetestuj aplikację, aby zweryfikować zmiany

Przetestuj aplikację przy użyciu interaktywnego panelu **testów** . Ten proces umożliwia wprowadzenie nowego wypowiedź, a następnie wyświetlenie wyników przewidywania, aby zobaczyć, jak dobrze aktywna aplikacja przeszkolone działa. Przewidywanie intencji powinno być dość świadome (powyżej 60%) a ekstrakcja jednostek powinna pobrać co najmniej `Order` jednostkę. Szczegóły jednostki Order mogą nie być widoczne, ponieważ te wyrażenia długości nie wystarczają do obsługi każdego przypadku.

1. Wybierz opcję **Test** (Testuj) w górnym menu nawigacyjnym.
1. Wprowadź wypowiedź `2 small cheese pizzas for pickup` i wybierz klawisz ENTER. Aktywny model przewidział prawidłowy cel z ponad 60% pewnością.


1. Wybierz pozycję **Sprawdź** , aby zobaczyć prognozy jednostek.

    > [!div class="mx-imgBorder"]
    > ![Częściowy zrzut ekranu przedstawiający prognozowanie jednostek w interaktywnym panelu testów.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Opublikuj aplikację, aby uzyskać do niej dostęp z punktu końcowego HTTP

Aby uzyskać przewidywania usługi LUIS w czatbocie lub innej aplikacji klienckiej, należy opublikować aplikację w punkcie końcowym.

1. Wybierz pozycję **Publikuj** w prawym górnym rogu.

    ![Zrzut ekranu przedstawiający przycisk LUIS publikowanie do punktu końcowego w prawym górnym menu](./media/howto-publish/publish-button.png)

1. Wybierz miejsce **produkcyjne** , a następnie wybierz pozycję **Zmień ustawienia**, wybierz pozycję **Analiza tonacji**, a następnie wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający LUIS publikowanie w punkcie końcowym](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Wybierz link **dostęp do adresów URL punktu końcowego** w obszarze powiadomień, aby przejść do strony **zasobów platformy Azure** . Adresy URL punktów końcowych są wyświetlane jako **przykładowe zapytanie**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Pobierz zamierzenia i prognozowanie jednostek z punktu końcowego HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i Zastąp _YOUR_QUERY_HERE_ tym samym zapytaniem, które zostało wprowadzone w panelu testów interakcyjnych.

    `2 small cheese pizzas for pickup`

    Ostatnim parametrem QueryString jest `query` , wypowiedź **Query**.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Samouczek — intencje](luis-quickstart-intents-only.md)
* [Pojęcia —](luis-concept-entity-types.md) informacje koncepcyjne jednostek
* [Koncepcja —](luis-concept-feature.md) informacje o pojęciach
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Następne kroki

W tym samouczku aplikacja korzysta z jednostki uczenia maszynowego w celu znalezienia zamiaru wypowiedź użytkownika i wyodrębnienia szczegółów z tego wypowiedź. Użycie jednostki Uczenie maszynowe pozwala na rozłożyć szczegóły jednostki.

> [!div class="nextstepaction"]
> [Dodawanie wstępnie skompilowanej jednostki KeyPhrase](luis-quickstart-intent-and-key-phrase.md)
