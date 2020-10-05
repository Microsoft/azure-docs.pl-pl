---
title: 'Szybki Start: Tworzenie nowej aplikacji w portalu LUIS'
description: W tym przewodniku szybki start utworzysz podstawowe elementy aplikacji, intencje i jednostki, a także test z przykładową wypowiedźą w portalu LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 38fffd7793e1f5bd59ac6dde4499b2eb25009b52
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91303866"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Szybki Start: Tworzenie nowej aplikacji w portalu LUIS

W tym przewodniku szybki start utworzysz nową aplikację w portalu LUIS. Najpierw Utwórz podstawowe elementy aplikacji, **intencje**i **jednostki**. Następnie przetestuj aplikację, dostarczając przykładowej wypowiedź użytkownika w panelu testów interakcyjnych w celu uzyskania przewidywanych zamierzeń.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Tworzenie aplikacji

1. Wybierz pozycję **+ Nowa aplikacja dla konwersacji** z poziomu paska narzędzi kontekstu, a następnie ponownie wybierz pozycję **+ Nowa aplikacja do konwersacji** .

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający tworzenie nowej aplikacji w portalu LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. W oknie podręcznym Skonfiguruj aplikację przy użyciu następujących ustawień, a następnie wybierz pozycję **gotowe**.

   |Nazwa ustawienia| Wartość | Przeznaczenie|
   |--|--|--|
   |Nazwa|`myEnglishApp`|Unikatowa nazwa aplikacji LUIS<br>wymagane|
   |Kultura|**Angielski**|Język wyrażenia długości od użytkowników, **en-us**<br>wymagane|
   |Opis (opcjonalnie)|`App made with LUIS Portal`|Opis aplikacji<br>optional|
   |Zasób predykcyjny (opcjonalnie) |-  |Nie wybieraj. LUIS udostępnia klucz początkowy do użycia bezpłatnie na potrzeby tworzenia i 1 000 żądań punktu końcowego przewidywania. |

   ![Zrzut ekranu przedstawiający wprowadzanie nowych ustawień aplikacji](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Tworzenie intencji

Po utworzeniu aplikacji LUIS należy utworzyć intencje. Intencje są sposobem na klasyfikowanie tekstu od użytkowników. Na przykład aplikacja kadrs może mieć dwie funkcje. Aby ułatwić użytkownikom:

 1. Znajdź i Zastosuj do zadań
 1. Znajdowanie formularzy do zastosowania w przypadku zadań

Dwie różne _intencje_ dotyczące aplikacji są wyrównane do następujących intencji:

|Zamiar|Przykładowy tekst od użytkownika<br>znane jako _wypowiedź_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Aby utworzyć intencje, wykonaj następujące czynności:

1. Po utworzeniu aplikacji znajduje się na stronie **intencje** w sekcji **kompilacja** . Wybierz przycisk **Utwórz**.

   [![Zrzut ekranu przedstawiający Wybieranie opcji "Utwórz", aby utworzyć nowy cel](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Wprowadź nazwę celu, `FindForm` a następnie wybierz pozycję **gotowe**.

## <a name="add-an-example-utterance"></a>Dodawanie przykładu wypowiedź

Po utworzeniu zamiarów należy dodać przykład wyrażenia długości. Przykład wyrażenia długości jest tekstem, który użytkownik wprowadza w programie Chat bot lub innej aplikacji klienckiej. Umożliwiają one mapowanie zamiaru tekstu użytkownika na LUIS zamiar.

Na potrzeby tego przykładowej aplikacji `FindForm` Przykładowa wyrażenia długości będzie zawierać numer formularza. Aplikacja kliencka wymaga, aby numer formularza spełniał żądanie użytkownika, więc należy go uwzględnić w wypowiedź.

> [!div class="mx-imgBorder"]
> [![Zrzut ekranu przedstawiający przykład wyrażenia długości dla zamiaru FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Dodaj do zamiaru 15-przykładową wyrażenia długości `FindForm` .

|#|Przykładowe wypowiedzi|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Zgodnie z projektem, te przykładowe wyrażenia długości różnią się w następujący sposób:

* Długość wypowiedź
* [interpunkcyjny](luis-reference-application-settings.md#punctuation-normalization)
* wybór wyrazów
* intensywność czasownika (is, jest, będzie)
* kolejność wyrazów


## <a name="create-a-regular-expression-entity"></a>Tworzenie jednostki wyrażenia regularnego

Aby zwrócić numer formularza w odpowiedzi przewidywania czasu wykonywania, należy wyodrębnić numer formularza jako jednostkę. Ponieważ tekst z numerem formularza jest wysoce strukturalny, można użyć jednostki wyrażenia regularnego. Utwórz jednostkę wyrażenia regularnego, wykonując następujące czynności:

1. Wybierz pozycję **jednostki** z menu po lewej stronie.

1. Na stronie **jednostki** wybierz pozycję **Utwórz** .

1. Wprowadź nazwę `FormNumber` , wybierz typ jednostki **wyrażenia regularnego** .

1. Wprowadź wyrażenie regularne `hrf-[0-9]{6}` w polu **wyrażenie** regularne. Ten wpis jest zgodny ze znakami literału, `hrf-` i zezwala na dokładnie sześć cyfr, a następnie wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający tworzenie jednostki wyrażenia regularnego](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Ta jednostka wyodrębnia wszystkie teksty, które pasują do wyrażenia regularnego w dowolnym z intencji.

## <a name="add-example-utterances-to-the-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None

Opcja **Brak** jest zamiarem rezerwowym i nie powinna być pusta. Ta metoda powinna zawierać jeden wypowiedź na każdy 10 przykład wyrażenia długości, który został dodany do innych intencji aplikacji.

Przykładowa metoda wyrażenia długości **nie** powinna być poza domeną aplikacji klienckiej.

1. Wybierz pozycję **intencje** w menu po lewej stronie, a następnie wybierz pozycję **Brak** z listy intencje.

1. Dodaj następujący przykład wyrażenia długości do celu:

   |Brak przykładu zamiaru wyrażenia długości|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   W przypadku tej aplikacji te przykładowe wyrażenia długości znajdują się poza domeną. Jeśli domena obejmuje zwierzęta lub ocean, należy użyć innego przykładu wyrażenia długości dla opcji **Brak** .

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Przyjrzyj się jednostce wyrażenia regularnego w przykładzie wyrażenia długości

1. Sprawdź, czy jednostka została znaleziona w **FindForm**  zamiaru, wybierając pozycję **intencje** w menu po lewej stronie. Następnie wybierz pozycję **FindForm** zamierzenia.

   Jednostka jest oznaczona, gdzie pojawia się w przykładzie wyrażenia długości.

   > [!div class="mx-imgBorder"]
   > [![Zrzut ekranu przedstawiający wszystkie przykładowe wyrażenia długości oznaczone obiektami](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testowanie nowej aplikacji za pomocą interaktywnego okienka testów

Użyj interaktywnego okienka **testowego** w portalu Luis, aby sprawdzić, czy jednostka została wyodrębniona z nowej wyrażenia długości, która nie była jeszcze widoczna dla aplikacji.

1. Wybierz pozycję **Testuj** w prawym górnym menu.

1. Dodaj nowy wypowiedź, a następnie naciśnij klawisz ENTER:

   ```Is there a form named hrf-234098```

    Wybierz pozycję **Sprawdź** , aby zobaczyć przewidywania jednostek.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający Testowanie nowego wypowiedź w okienku testów](./media/get-started-portal-build-app/test-new-utterance.png)

   Najbardziej przewidywany cel jest prawidłowo **FindForm** z ponad 90% pewnością (0,977). Jednostka **FormNumber** jest wyodrębniana z wartością hrf-234098.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu korzystania z tego przewodnika Szybki Start i przechodzenia do następnego przewodnika Szybki Start wybierz pozycję **Moje aplikacje** w górnym menu nawigacji. Następnie zaznacz pole wyboru po lewej stronie z listy i wybierz pozycję  **Usuń** z paska narzędzi kontekstu powyżej listy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [2. Wdróż aplikację](get-started-portal-deploy-app.md)
