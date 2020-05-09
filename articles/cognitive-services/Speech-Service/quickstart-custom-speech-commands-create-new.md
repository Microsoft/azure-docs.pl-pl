---
title: 'Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano tworzenie i testowanie aplikacji hostowanych poleceń niestandardowych.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872526"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Szybki Start: Tworzenie aplikacji poleceń niestandardowych (wersja zapoznawcza)

W tym przewodniku szybki start dowiesz się, jak utworzyć i przetestować aplikację poleceń niestandardowych.
Utworzona aplikacja będzie przetwarzać wyrażenia długości na przykład "Włącz telewizor" i Odpowiedz przy użyciu prostego komunikatu "OK", włączając telewizor ".

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > W tej chwili polecenia niestandardowe obsługują tylko subskrypcje mowy w regionach Zachodnie, westus2 i neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Przejdź do programu Speech Studio dla poleceń niestandardowych

1. Otwórz przeglądarkę internetową i przejdź do programu [Speech Studio](https://speech.microsoft.com/)
1. Wprowadź swoje poświadczenia, aby zalogować się do portalu.

   - Widok domyślny to lista subskrypcji mowy.
     > [!NOTE]
     > Jeśli nie widzisz strony Wybieranie subskrypcji, możesz tam przejść przez wybranie opcji "zasoby mowy" z menu Ustawienia na górnym pasku.

1. Wybierz swoją subskrypcję mowy, a następnie wybierz pozycję **Przejdź do Studio**.
1. Wybierz **polecenia niestandardowe**.

     - Widok domyślny to lista aplikacji poleceń niestandardowych, które znajdują się w ramach wybranej subskrypcji.

## <a name="create-a-custom-commands-project"></a>Tworzenie projektu poleceń niestandardowych

1. Wybierz pozycję **Nowy projekt** , aby utworzyć nowy projekt.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie projektu](media/custom-speech-commands/create-new-project.png)

1. Wprowadź nazwę projektu.
1. Wybierz pozycję język z listy rozwijanej.
1. Wybierz zasób tworzenia z listy rozwijanej. Jeśli nie ma prawidłowych zasobów tworzenia, utwórz je, klikając pozycję **Utwórz nowy zasób tworzenia Luis**.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie zasobu](media/custom-speech-commands/create-new-resource.png)

   - Wprowadź nazwę zasobu, grupę zasobów.
   - Wybierz pozycję wartość lokalizacji i warstwy cenowej z listy rozwijanej.

      > [!NOTE]
      > Grupy zasobów można utworzyć, wprowadzając odpowiednią nazwę grupy zasobów w polu "Grupa zasobów". Grupa zasobów zostanie utworzona, gdy zostanie wybrana wartość **Utwórz** .

1. Następnie wybierz pozycję **Utwórz** , aby utworzyć projekt.
1. Po utworzeniu wybierz projekt.

    - Widok powinien teraz być przeglądem nowo utworzonych aplikacji poleceń niestandardowych.

## <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów LUIS (opcjonalnie)

Możesz zaktualizować zasób tworzenia, który został ustawiony w oknie Nowy projekt, i ustawić zasób przewidywania. Zasób predykcyjny jest używany do rozpoznawania po opublikowaniu aplikacji poleceń niestandardowych. Nie potrzebujesz zasobów predykcyjnych dla faz tworzenia i testowania.

1. W lewym okienku wybierz pozycję **Ustawienia** , a następnie przejdź do sekcji **Luis Resources (zasoby** ) w środkowym okienku.
1. Wybierz zasób predykcyjny lub utwórz go, wybierając pozycję **Utwórz nowy zasób**.
1. Wybierz pozycję **Zapisz**.
    
    > [!div class="mx-imgBorder"]
    > ![Ustawianie zasobów LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Ponieważ zasób tworzenia obsługuje tylko żądania punktów końcowych przewidywania 1 000 miesięcznie, mandatorily będzie trzeba ustawić zasób przewidywania LUIS przed opublikowaniem aplikacji poleceń niestandardowych.


## <a name="create-a-new-command"></a>Utwórz nowe polecenie

Utwórzmy proste polecenie, które będzie miało pojedynczy wypowiedź, `turn on the tv`i odpowiedzieć na wiadomość. `Ok, turning on the tv`

1. Utwórz nowe polecenie, wybierając ikonę znajdującą się `+ New command` w górnej części okienka po lewej stronie. Nowe wyskakujące okienko zostanie wyświetlone zatytułowane **nowe polecenie**.
1. Podaj wartość pola **Nazwa** jako `TurnOn`.
1. Wybierz przycisk **Utwórz**.

W środkowym okienku są rejestrowane różne właściwości polecenia:


| Konfigurowanie            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Przykładowe zdania | Przykład wyrażenia długości, że użytkownik może powiedzieć, aby wyzwolić to polecenie                                                                 |
| Parametry       | Informacje wymagane do wykonania polecenia                                                                                |
| Reguły uzupełniania | Akcje, które należy wykonać, aby zrealizować polecenie. Na przykład, aby odpowiedzieć użytkownikowi lub komunikować się z inną usługą sieci Web. |
| Reguły interakcji   | Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji                                                              |


> [!div class="mx-imgBorder"]
> ![Utwórz polecenie](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Dodaj przykładowe zdania

Zacznijmy od sekcji przykładowe zdania i podaj przykład, co użytkownik może powiedzieć.

1. Wybierz sekcję **przykładowe zdania** w środkowym okienku i w większości okienka po prawej stronie Dodaj przykłady:

    ```
    turn on the tv
    ```

1. Wybierz `Save` ikonę znajdującą się na górze tego okienka.

Na razie nie mamy żadnych parametrów, aby można było przejść do sekcji **reguły uzupełniania** .

### <a name="add-a-completion-rule"></a>Dodawanie reguły uzupełniania

Teraz Dodaj regułę uzupełniania o poniższej konfiguracji. Ta reguła wskazuje użytkownika, że jest wykonywana akcja realizacji.


| Ustawienie    | Sugerowana wartość                          | Opis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nazwa reguły  | ConfirmationResponse                  | Nazwa opisująca przeznaczenie reguły          |
| Warunki | Brak                                     | Warunki określające, kiedy można uruchomić regułę    |
| Akcje    | SpeechResponse "— OK, Włączanie telewizora" | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

1. Utwórz nową regułę uzupełniania, wybierając `+Add` ikonę w górnej części środkowego okienka.
1. Podaj wartość w sekcji **Nazwa** .
1. Dodawanie akcji
   1. Utwórz nową akcję, wybierając pozycję **+ Dodaj akcję** w sekcji **Akcje** .
   1. W oknie podręcznym **Nowa akcja** wybierz opcję `Send speech response` z listy rozwijanej **Typ**.
   1. Wybierz `Simple editor` pole **odpowiedzi** .
       - W **pierwszym polu wariacja** podaj wartość odpowiedzi jako`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Tworzenie odpowiedzi na mowę](media/custom-speech-commands/create-speech-response-action.png)

1. Kliknij przycisk **Zapisz** , aby zapisać regułę.
1. Wróć do sekcji **reguły uzupełniania** , wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany. 

> [!div class="mx-imgBorder"]
> ![Tworzenie reguły uzupełniania](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Wypróbowywanie działania

Testowanie zachowania przy użyciu panelu rozmowy testowej
1. Wybierz `Train` ikonę znajdującą się w prawym okienku.
1. Po zakończeniu szkolenia wybierz pozycję `Test`. Zostanie wyświetlony nowy **test okna aplikacji** .
    - Wpisywanie: Włączanie telewizora
    - Oczekiwana odpowiedź: OK, Włączanie telewizora


> [!div class="mx-imgBorder"]
> ![Testowanie za pomocą rozmowy w sieci Web](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
