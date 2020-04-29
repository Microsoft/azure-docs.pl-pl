---
title: 'Samouczek: samouczek jednostek złożonych — LUIS'
description: W tym samouczku Dodaj jednostkę złożoną, aby powiązać wyodrębnione dane różnych typów w jedną zawierającą ją jednostkę. Poprzez zgrupowanie danych aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typach danych.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: 5b8185a56c54ec92ce8ceaf1cd029dd31f6e709c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478685"
---
# <a name="tutorial-group-and-extract-related-data"></a>Samouczek: grupowanie i wyodrębnianie powiązanych danych
W tym samouczku Dodaj jednostkę złożoną, aby powiązać wyodrębnione dane różnych typów w jedną zawierającą ją jednostkę. Poprzez zgrupowanie danych aplikacja kliencka może łatwo wyodrębnić powiązane dane w różnych typach danych.

Celem jednostki złożonej jest pogrupowanie powiązanych jednostek z nadrzędną jednostką kategorii. Informacje istnieją jako osobne jednostki przed utworzeniem projektu złożonego.

Jednostka złożona jest odpowiednia dla tego typu danych, ponieważ dane:

* Są ze sobą powiązane.
* Używaj różnych typów jednostek.
* Te informacje należy grupować i przetwarzać jako całość w aplikacji klienckiej.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Tworzenie intencji
> * Dodawanie jednostki złożonej
> * Szkolenie
> * Publikowanie
> * Pobieranie intencji i jednostek z punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

1.  Pobierz i Zapisz [plik JSON aplikacji](
https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json?raw=true) z samouczka listy.

2. Zaimportuj plik JSON do nowej aplikacji przy użyciu [portalu Luis](https://www.luis.ai).

3. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `composite`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="composite-entity"></a>Jednostka złożona

W tej aplikacji Nazwa działu jest definiowana w jednostce listy **działów** i zawiera synonimy.

Celem **TransferEmployeeToDepartment** jest przykład wyrażenia długości, aby zażądać przeniesienia pracownika do nowego działu.

Przykład wyrażenia długości dla tego celu to:

|Przykładowe wypowiedzi|
|--|
|Move John W. Smith to the accounting department (Przenieś Johna W. Smitha do działu księgowości)|
|Transfer Jill Jones from to R&D (Przenieś Jill Jones z do zespołu badawczo-rozwojowego)|

Żądanie Move powinno zawierać nazwę działu i nazwę pracownika.

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Dodaj wbudowaną jednostkę PersonName, aby pomóc w utworzeniu typowej wyodrębniania typów danych

Usługa LUIS zawiera kilka wstępnie utworzonych jednostek na potrzeby typowych działań związanych z wyodrębnianiem danych.

1. Wybierz opcję **Kompiluj** z górnego okienka nawigacji, a następnie wybierz pozycję **jednostki** w menu nawigacji po lewej stronie.

1. Naciśnij przycisk **Manage prebuilt entity** (Zarządzaj wstępnie utworzoną jednostką).

1. Wybierz **[osobę](luis-reference-prebuilt-person.md)** z listy wstępnie utworzonych jednostek, a następnie wybierz pozycję **gotowe**.

    ![Zrzut ekranu przedstawiający pozycję number (liczba) wybraną w oknie dialogowym wstępnie skompilowanych jednostek](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Ta jednostka ułatwia dodawanie rozpoznawania nazw do aplikacji klienckiej.

## <a name="create-composite-entity-from-example-utterances"></a>Utwórz jednostkę złożoną na podstawie przykładu wyrażenia długości

1. Wybierz pozycję **Intents** (Intencje) w obszarze nawigacji po lewej stronie.

1. Wybierz pozycję **TransferEmployeeToDepartment** z listy intencje.

1. W wypowiedź `place John Jackson in engineering`wybierz jednostkę PersonName, `John Jackson`a następnie wybierz pozycję **Otocz w jednostce złożonej** na liście rozwijanej menu dla poniższego wypowiedź.

    ![Zrzut ekranu przedstawiający Wybieranie zawijania złożonego w oknie dialogowym rozwijanym](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Następnie natychmiast Wybierz ostatnią jednostkę `engineering` w wypowiedź. Zielony pasek jest rysowany pod zaznaczonymi wyrazami wskazującymi jednostkę złożoną. W menu podręcznym wprowadź nazwę `TransferEmployeeInfo` złożoną, a następnie wybierz klawisz ENTER.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy złożonej w oknie dialogowym listy rozwijanej](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. W **jakim typie obiektu chcesz utworzyć?** wszystkie wymagane pola znajdują się na liście: `personName` i. `Department` Wybierz pozycję **Gotowe**. Zwróć uwagę, że wstępnie utworzona jednostka, osobaname, została dodana do jednostki złożonej. Jeśli można utworzyć wstępnie utworzoną jednostkę między początkowym i końcowym tokenem jednostki złożonej, jednostka złożona musi zawierać te wstępnie skompilowane jednostki. Jeśli wstępnie skompilowane jednostki nie są uwzględnione, jednostka złożona nie jest prawidłowo przewidywalna, ale każdy pojedynczy element to.

    ![Zrzut ekranu przedstawiający wprowadzanie nazwy złożonej w oknie dialogowym listy rozwijanej](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Etykieta przykład wyrażenia długości z jednostką złożoną

1. W każdym przykładzie wypowiedź wybierz jednostkę z lewej strony, która powinna znajdować się w ramach projektu złożonego. Następnie wybierz pozycję **Zawijanie w jednostce złożonej**.

1. Zaznacz ostatni wyraz w jednostce złożonej, a następnie wybierz pozycję **TransferEmployeeInfo** z menu podręcznego.

1. Sprawdź, czy wszystkie wyrażenia długości w zamierzeniu są oznaczone jednostką złożoną.

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trenowanie aplikacji w celu umożliwienia testowania zmian w intencji

Aby nauczyć aplikację, wybierz pozycję **pouczenie**. Szkolenie stosuje zmiany, takie jak nowe jednostki i etykiety wyrażenia długości, do aktywnego modelu.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Opublikuj aplikację, aby uzyskać do niej dostęp z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Pobieranie przewidywania intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Przejdź na koniec tego adresu URL i wprowadź ciąg `Move Jill Jones to DevOps`. Ostatni parametr ciągu zapytania to `q`, czyli query (zapytanie) wypowiedzi.

    Ponieważ ten test polega na sprawdzeniu, czy złożone jest prawidłowo wyodrębnione, test może obejmować istniejącą przykładową wypowiedź lub nową wypowiedź. Dobrym testem jest uwzględnienie wszystkich jednostek podrzędnych w jednostce złożonej.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Ta wypowiedź zwraca tablicę jednostek złożonych. Każda jednostka ma typ i wartość. Aby znaleźć większą precyzję dla każdej jednostki podrzędnej, użyj kombinacji typu i wartości z elementu złożonej tablicy, aby znaleźć odpowiadający element w tablicy jednostek.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* [Samouczek jednostki listy](luis-quickstart-intents-only.md)
* Informacje koncepcyjne [jednostki złożonej](luis-concept-entity-types.md)
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono jednostkę złożoną w celu hermetyzacji istniejących jednostek. Dzięki temu aplikacja kliencka może znaleźć grupę powiązanych danych w różnych typach, aby kontynuować konwersację. Aplikacja kliencka dla tej aplikacji Human Resources może zażądać, jaki dzień i kiedy przeniesienie musi rozpocząć się i zakończyć. Może również poprosił o inne logistyke przenoszenia, takie jak telefon fizyczny.

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z niepewnymi przewidywaniami przez przeglądanie wypowiedzi punktu końcowego](luis-tutorial-review-endpoint-utterances.md)
