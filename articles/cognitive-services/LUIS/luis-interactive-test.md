---
title: Testowanie aplikacji w portalu LUIS
description: Użyj Language Understanding (LUIS), aby w sposób ciągły pracować w aplikacji, aby udoskonalić ją i ulepszyć jej zrozumienie.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: cb4559e6d3582a337cbc32ef986bec5db5940ef9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591866"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testowanie aplikacji LUIS w portalu LUIS

[Testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po przekształceniu aplikacji LUIS przetestuj ją z przykładową wyrażenia długości, aby sprawdzić, czy zamiary i jednostki są rozpoznawane poprawnie. Jeśli tak nie jest, wprowadź ponownie aktualizacje aplikacji LUIS, uczenia i testowania.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Uczenie przed testowaniem

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Aby przeprowadzić test względem najnowszej wersji aktywnej aplikacji, wybierz opcję **uczenie** z górnego menu przed przetestowaniem.

## <a name="test-an-utterance"></a>Testowanie wypowiedź

Test wypowiedź nie powinien być dokładnie taki sam, jak w przypadku każdego przykładu wyrażenia długości w aplikacji. Test wypowiedź powinien obejmować wybór wyrazów, Długość frazy oraz użycie jednostek, których oczekujesz dla użytkownika.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .

1. Aby uzyskać dostęp do panelu slajdu **testu** , wybierz opcję **test** w górnym panelu aplikacji.

    > [!div class="mx-imgBorder"]
    > ![Strona uczenie & aplikacji testowej](./media/luis-how-to-interactive-test/test.png)

1. Wprowadź wypowiedź w polu tekstowym, a następnie wybierz klawisz ENTER. W **teście**można wpisać dowolną liczbę wyrażenia długości testowych, ale tylko jeden wypowiedź jednocześnie.

1. Wypowiedź, jego górny cel i Ocena są dodawane do listy wyrażenia długości w polu tekstowym.

    ![Testowanie interaktywne identyfikuje niewłaściwy cel](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Sprawdź ocenę

Sprawdzasz szczegóły wyniku testu w panelu **Inspekcja** .

1. Po otwarciu panelu slajdu **testowego** zaznacz opcję **Sprawdź** , czy wypowiedź chcesz porównać.

    ![Wybierz przycisk Sprawdź, aby zobaczyć więcej szczegółów na temat wyników testu](./media/luis-how-to-interactive-test/inspect.png)

1. Zostanie wyświetlony panel **Inspekcja** . Panel zawiera górne przeznaczenie oceny, a także wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedźu.

    ![Panel zawiera górne przeznaczenie oceny, a także wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedźu.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Popraw najważniejsze przeznaczenie oceny

1. Jeśli najważniejsze przeznaczenie oceny jest nieprawidłowe, wybierz przycisk **Edytuj** .

1.  Z listy rozwijanej wybierz odpowiedni cel dla wypowiedź.

    ![Wybierz odpowiedni cel](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Wyświetl wyniki tonacji

Jeśli na stronie **[Publikowanie](luis-how-to-publish-app.md#enable-sentiment-analysis)** jest skonfigurowana **tonacji Analysis** , wyniki testu obejmują tonacji znaleziony w wypowiedź.

![Obraz okienka testowania z analizą tonacji](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Popraw dopasowanie dopasowanego wzorca

Jeśli używasz [wzorców](luis-concept-patterns.md) i wypowiedź dopasowane do wzorca, ale przewidywalno niewłaściwy cel, wybierz łącze **Edytuj** przez wzorzec, a następnie wybierz odpowiedni cel.

## <a name="compare-with-published-version"></a>Porównaj z opublikowaną wersją

Aktywną wersję aplikacji można przetestować przy użyciu opublikowanej wersji [punktu końcowego](luis-glossary.md#endpoint) . W panelu **Inspekcja** wybierz pozycję **Porównaj z opublikowanym**. Wszystkie testy dotyczące opublikowanego modelu są odejmowane od salda przydziału subskrypcji platformy Azure.

![Porównaj z opublikowanym](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Wyświetl kod JSON punktu końcowego w panelu testów
Możesz wyświetlić kod JSON punktu końcowego zwrócony do porównania, wybierając **Widok Pokaż JSON**.

![Opublikowana odpowiedź JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Ustawienia dodatkowe w panelu testu

### <a name="luis-endpoint"></a>Punkt końcowy LUIS

Jeśli masz kilka punktów końcowych LUIS, Użyj linku **Ustawienia dodatkowe** w okienku opublikowanym test, aby zmienić punkt końcowy używany do testowania. Jeśli nie masz pewności, który punkt końcowy jest używany, wybierz domyślną **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Panel testowy z wyróżnionymi dodatkowymi linkami ustawień](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Testowanie wsadowe
Zobacz [koncepcje](luis-concept-batch-test.md) testowania partii i Dowiedz się, [jak](luis-how-to-batch-test.md) przetestować partię wyrażenia długości.

## <a name="next-steps"></a>Następne kroki

Jeśli testowanie wskazuje, że aplikacja LUIS nie rozpoznaje poprawnych intencji i jednostek, możesz obejść, aby poprawić dokładność aplikacji LUIS, dodając etykiety więcej wyrażenia długości lub dodając funkcje.

* [Etykieta sugerowanych wyrażenia długości z LUIS](luis-how-to-review-endpoint-utterances.md)
* [Korzystaj z funkcji, aby zwiększyć wydajność aplikacji LUIS](luis-how-to-add-features.md)
