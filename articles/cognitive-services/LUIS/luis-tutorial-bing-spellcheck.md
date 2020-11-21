---
title: Popraw błędne słowa — LUIS
titleSuffix: Azure Cognitive Services
description: Popraw błędne słowa w wyrażenia długości przez dodanie sprawdzanie pisowni Bing API wersji 7 do zapytań LUIS Endpoint.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.openlocfilehash: 9d2a1702ea131e9b1b4bf5e586f4290db3aff7ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018773"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Popraw błędne słowa przy użyciu sprawdzanie pisowni Bing

Aplikację LUIS można zintegrować sprawdzanie pisowni Bing z [interfejsem API wersji 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) w celu poprawienia błędnie napisanych wyrazów w wyrażenia długości przed Luis przewidywania wyników i jednostek wypowiedź.

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>Utwórz klucz punktu końcowego

Aby utworzyć zasób sprawdzanie pisowni Bing w Azure Portal, wykonaj następujące instrukcje:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

2. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

3. W polu wyszukiwania wpisz `Bing Spell Check API V7`.

    ![Wyszukaj sprawdzanie pisowni Bing API wersji 7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wybierz usługę.

5. Panel Informacje pojawia się z prawej strony zawierającej informacje, takie jak Uwaga prawna. Wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia subskrypcji.

6. W następnym panelu wprowadź ustawienia usługi. Poczekaj na zakończenie procesu tworzenia usługi.

    ![Wprowadź ustawienia usługi](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wybierz pozycję **wszystkie zasoby** pod tytułem **Ulubione** po lewej stronie.

8. Wybierz nową usługę. Jego typem jest **Cognitive Services** , a lokalizacja jest **globalna**.

9. W panelu głównym wybierz pozycję **klucze** , aby wyświetlić nowe klucze.

    ![Przechwyć klucze](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Skopiuj pierwszy klucz. Potrzebny jest tylko jeden z tych dwóch kluczy.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Dodawanie klucza do adresu URL punktu końcowego
Zapytanie punktu końcowego wymaga klucza przesłanego w parametrach ciągu zapytania dla każdego zapytania, dla którego ma zostać zastosowana korekta pisowni. Może być chatbot, który wywołuje LUIS, lub może bezpośrednio wywołać interfejs API punktu końcowego LUIS. Niezależnie od tego, jak jest wywoływany punkt końcowy, każdy i każde wywołanie musi zawierać informacje wymagane do poprawnego działania poprawek pisowni.

Adres URL punktu końcowego ma kilka wartości, które muszą zostać pomyślnie przesłane. Klucz wersji 7 interfejsu API sprawdzanie pisowni Bing jest innym jednym z nich. Należy ustawić parametr **sprawdzania pisowni** na wartość true, a dla wartości klucza należy ustawić wartość opcji **Bing-test-Check-Subscription-Key** :

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Wyślij błędnie wpisane wypowiedź do LUIS
1. W przeglądarce internetowej Skopiuj poprzedni ciąg i Zastąp `region` wartości,, `appId` `luisKey` , i `bingKey` własnymi wartościami. Upewnij się, że używasz regionu punktu końcowego, jeśli jest inny niż [region](luis-reference-regions.md)publikacji.

2. Dodaj błędny wypowiedź, na przykład "jak daleko jest mountainn?". W języku angielskim, `mountain` , z jednym `n` , jest poprawną pisownią.

3. Wybierz klawisz ENTER, aby wysłać zapytanie do LUIS.

4. LUIS reaguje na wynik JSON dla `How far is the mountain?` . Jeśli sprawdzanie pisowni Bing API wersji 7 wykrywa błąd pisowni, `query` pole w odpowiedzi JSON aplikacji Luis zawiera oryginalne zapytanie, a `alteredQuery` pole zawiera poprawione zapytanie wysyłane do Luis.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignoruj błędy pisowni

Jeśli nie chcesz używać sprawdzanie pisowni Bing API wersji 7, musisz dodać poprawną i niepoprawną pisownię.

Dwa rozwiązania:

* Etykieta przykład wyrażenia długości, która ma wszystkie inne pisownie, dzięki czemu LUIS może poznać poprawną pisownię oraz literówki. Ta opcja wymaga więcej nakładów na etykietowanie niż przy użyciu modułu sprawdzania pisowni.
* Utwórz listę fraz ze wszystkimi odmianami wyrazu. W tym rozwiązaniu nie trzeba etykietować odmian wyrazów w przykładzie wyrażenia długości.

## <a name="publishing-page"></a>Strona publikowania
Na stronie [Publikowanie](luis-how-to-publish-app.md) jest zaznaczone pole wyboru **Włącz sprawdzanie pisowni Bing** . Jest to wygoda do utworzenia klucza i zrozumienia, jak zmienia się adres URL punktu końcowego. Nadal musisz użyć prawidłowych parametrów punktu końcowego, aby poprawić pisownię dla każdego wypowiedźu.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat przykładu wyrażenia długości](./luis-how-to-add-entities.md)