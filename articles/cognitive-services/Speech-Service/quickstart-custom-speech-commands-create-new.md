---
title: 'Szybki Start: Tworzenie niestandardowych poleceń podgląd aplikacji App-Speech Service'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano tworzenie i testowanie hostowanych poleceń niestandardowych aplikacji w wersji zapoznawczej. Aplikacja będzie przetwarzać wyrażenia długości.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 5b54923bb667053ffc886b335ab9d1b0cf4cb754
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509359"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Szybki Start: Tworzenie aplikacji niestandardowych poleceń w wersji zapoznawczej

W tym przewodniku szybki start dowiesz się, jak utworzyć i przetestować aplikację poleceń niestandardowych.
Aplikacja będzie przetwarzać wyrażenia długości, takie jak "Włączanie telewizora" i Odpowiedz przy użyciu prostego komunikatu, takiego jak "OK, Włączanie telewizora".

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Tworzenie zasobu usługi Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > W tej chwili polecenia niestandardowe obsługują tylko subskrypcje mowy w regionach Zachodnie, westus2 i neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Przejdź do programu Speech Studio dla poleceń niestandardowych

1. W przeglądarce internetowej przejdź do pozycji [Speech Studio](https://speech.microsoft.com/).
1. Wprowadź swoje poświadczenia, aby zalogować się do portalu.

   Widok domyślny to lista subskrypcji mowy.
    > [!NOTE]
    > Jeśli nie widzisz strony Wybieranie subskrypcji, możesz ją uzyskać, wybierając pozycję **zasoby mowy** w menu Ustawienia w górnej części ekranu.

1. Wybierz swoją subskrypcję mowy, a następnie wybierz pozycję **Przejdź do Studio**.
1. Wybierz **polecenia niestandardowe**.

     Widok domyślny to lista aplikacji poleceń niestandardowych, które znajdują się w wybranej subskrypcji.

## <a name="create-a-custom-commands-project"></a>Tworzenie projektu poleceń niestandardowych

1. Wybierz pozycję **Nowy projekt** , aby utworzyć projekt.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie projektu](media/custom-speech-commands/create-new-project.png)

1. W polu **Nazwa** wprowadź nazwę projektu.
1. Z listy **Język** wybierz język.
1. Z listy **zasobów tworzenie Luis** wybierz zasób tworzenia. Jeśli nie ma prawidłowych zasobów tworzenia, utwórz je, wybierając pozycję **Utwórz nowy zasób tworzenia Luis**.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie zasobu](media/custom-speech-commands/create-new-resource.png)

   1. W polu **nazwa zasobu** wprowadź nazwę zasobu.
   1. Z listy **Grupa zasobów** wybierz grupę zasobów.
   1. Z listy **Lokalizacja** wybierz lokalizację.
   1. Na liście **warstwa cenowa** wybierz warstwę.

      > [!NOTE]
      > Grupę zasobów można utworzyć, wprowadzając nazwę grupy zasobów w polu **Grupa zasobów** . Po wybraniu opcji **Utwórz**zostanie utworzona grupa zasobów.

1. Wybierz przycisk **Utwórz**.
1. Po utworzeniu projektu zaznacz go.

    Teraz powinien zostać wyświetlony przegląd nowej aplikacji poleceń niestandardowych.

## <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów LUIS (opcjonalnie)

Możesz zaktualizować zasób tworzenia wybrany w oknie **Nowy projekt** i ustawić zasób przewidywania. Zasób predykcyjny jest używany do rozpoznawania, gdy aplikacja poleceń niestandardowych jest publikowana. Nie potrzebujesz zasobów predykcyjnych w fazach tworzenia i testowania.

1. W lewym okienku wybierz pozycję **Ustawienia** , a następnie w środkowym okienku wybierz pozycję **zasoby Luis** .
1. Wybierz zasób predykcyjny lub utwórz go, wybierając pozycję **Utwórz nowy zasób**.
1. Wybierz pozycję **Zapisz**.
    
    > [!div class="mx-imgBorder"]
    > ![Ustawianie zasobów LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Ponieważ zasób tworzenia obsługuje tylko żądania punktu końcowego przewidywania 1 000 miesięcznie, należy ustawić zasób przewidywania LUIS przed opublikowaniem aplikacji poleceń niestandardowych.


## <a name="create-a-command"></a>Utwórz polecenie

Utwórzmy proste polecenie, które będzie miało pojedynczy wypowiedź, `turn on the tv` i odpowiedzieć na wiadomość `Ok, turning on the tv` .

1. Utwórz polecenie, wybierając pozycję **nowe** w górnej części okienka po lewej stronie. Zostanie otwarte **nowe okno polecenia** .
1. W polu **Nazwa** wprowadź wartość **wlaczaj**.
1. Wybierz przycisk **Utwórz**.

Środkowe okienko zawiera listę właściwości polecenia:


| Konfiguracja            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Przykładowe zdania** | Przykłady wyrażenia długości, które użytkownik może powiedzieć, aby wyzwolić polecenie.                                                                 |
| **Parametry**       | Informacje wymagane do ukończenia tego polecenia.                                                                                |
| **Reguły uzupełniania** | Akcje, które zostaną wykonane, aby zrealizować polecenie. Na przykład odpowiadanie użytkownikowi lub nawiązywanie komunikacji z inną usługą sieci Web. |
| **Reguły interakcji**   | Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji.                                                              |


> [!div class="mx-imgBorder"]
> ![Utwórz polecenie](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Dodaj przykładowe zdania

Zacznijmy od sekcji **przykładowe zdania** . Udostępnimy przykład tego, co użytkownik może powiedzieć.

1. Wybierz **przykładowe zdania** w środkowym okienku. 
1. W okienku po prawej stronie Dodaj przykłady:

    ```
    turn on the tv
    ```

1. Wybierz pozycję **Zapisz** w górnej części okienka.

Na razie nie mamy parametrów, więc można przejść do sekcji **reguł ukończenia** .

### <a name="add-a-completion-rule"></a>Dodawanie reguły uzupełniania

Teraz Dodaj regułę uzupełniania, która ma następującą konfigurację. Ta reguła instruuje użytkownika, że jest wykonywana akcja realizacji.


| Ustawienie    | Sugerowana wartość                          | Opis                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Nazwa**  | **ConfirmationResponse**                  | Nazwa opisująca przeznaczenie reguły.          |
| **Warunki** | Brak                                     | Warunki określające, kiedy można uruchomić regułę.    |
| **Akcje**    | **Wyślij odpowiedź na mowę — > OK, Włączanie telewizora** | Akcja, która ma zostać podjęta, gdy warunek reguły ma wartość true. |

1. Utwórz nową regułę uzupełniania, wybierając pozycję **Dodaj** w górnej części środkowego okienka.
1. W polu **Nazwa** wprowadź nazwę.
1. Dodaj akcję.
   1. Utwórz akcję, wybierając pozycję **Dodaj akcję** w sekcji **Akcje** .
   1. W oknie **Edytowanie akcji** na liście **Typ** wybierz pozycję **Wyślij odpowiedź na mowę**.
   1. W obszarze **odpowiedź**wybierz pozycję **prosty edytor**. W **pierwszym polu wariacja** wprowadź **OK, włączając telewizor**.

   > [!div class="mx-imgBorder"]
   > ![Utwórz odpowiedź](media/custom-speech-commands/create-speech-response-action.png)

1. Wybierz pozycję **Zapisz** , aby zapisać regułę.
1. Wróć do sekcji **reguły uzupełniania** , wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany. 

> [!div class="mx-imgBorder"]
> ![Tworzenie reguły uzupełniania](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Wypróbowywanie działania

Przetestuj zachowanie przy użyciu panelu rozmowa testowa.
1. Wybierz pozycję **uczenie** w górnej części okienka po prawej stronie.
1. Po zakończeniu szkolenia wybierz pozycję **test**. Zostanie wyświetlone nowe okno **Test aplikacji** .
    - Wprowadzenie **do włączenia telewizora**
    - Oczekiwana odpowiedź: **OK, Włączanie telewizora**


> [!div class="mx-imgBorder"]
> ![Testowanie zachowania](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie poleceń niestandardowych aplikacji w wersji zapoznawczej przy użyciu parametrów](./quickstart-custom-speech-commands-create-parameters.md)
