---
title: 'Samouczek: integracja z dodatkami do zaawansowanych agentów — QnA Maker'
titleSuffix: Azure Cognitive Services
description: W tym samouczku poprawisz jakość bazy wiedzy o aktywnej uczeniu. Przejrzyj, zaakceptuj lub Odrzuć lub Dodaj bez usuwania lub zmiany istniejących pytań.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81402810"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Samouczek: Dodawanie bazy wiedzy do zaawansowanych agentów
Utwórz i zwiększ [możliwości bot agentów wirtualnych](https://powervirtualagents.microsoft.com/) w celu zapewnienia odpowiedzi z bazy wiedzy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie zaawansowanych agentów bot
> * Tworzenie tematu rezerwowego systemu
> * Dodawanie QnA Maker jako akcji do tematu jako przepływu automatyzacji
> * Tworzenie rozwiązania do automatyzowania
> * Dodawanie przepływu automatyzacji w rozwiązaniu do rozwiązania
> * Publikowanie agentów wirtualnych
> * Przetestuj agentów wirtualnych i odbieraj odpowiedzi z bazy wiedzy QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrowanie agenta z bazą wiedzy

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) Dodatek umożliwia zespołom tworzenie zaawansowanych botów przy użyciu interfejsu graficznego z przewodnikiem bez kodu. Nie potrzebujesz naukowców lub deweloperów danych.

W usłudze Virtual Machine Agents można utworzyć agenta z serią tematów (obszarów tematu), aby odpowiedzieć na pytania użytkowników przez wykonywanie akcji. Jeśli nie można znaleźć odpowiedzi, rezerwa systemowa może zwrócić odpowiedź.

Skonfiguruj agenta do wysyłania pytania do bazy wiedzy w ramach akcji tematu lub jako część *rezerwowej* ścieżki tematu systemu. Oba te elementy używają akcji do nawiązywania połączenia z bazą wiedzy i zwracają odpowiedź.

## <a name="power-automate-connects-to-generateanswer-action"></a>Automatyzacja łączy z `GenerateAnswer` akcją

Aby połączyć agenta z bazą wiedzy, użyj automatyzacji, aby utworzyć akcję. Automatyzacja zapewnia przepływ procesu, który nawiązuje połączenie z `GenerateAnswer` interfejsem API QNA Maker.

Po zaprojektowaniu i zapisaniu przepływu jest on dostępny z rozwiązania do automatyzacji. Użyj tego rozwiązania jako akcji w agencie.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Połącz agenta z bazą wiedzy

Poniżej znajduje się omówienie czynności służących do łączenia agenta w usłudze Virtual Machine Agents z bazą wiedzy w QnA Maker.

* W portalu [QNA Maker](https://www.qnamaker.ai/) :
    * Kompiluj i Publikuj bazę wiedzy.
    * Skopiuj szczegóły bazy wiedzy, w tym identyfikator, klucz punktu końcowego środowiska uruchomieniowego i hosta punktu końcowego środowiska uruchomieniowego.
* W portalu dla [agentów programu PowerShell](https://powerva.microsoft.com/) :
    * Tworzenie tematu agenta.
    * Wywoływanie akcji (w celu automatyzacji przepływu).
* W portalu [automatyzacji](https://us.flow.microsoft.com/) :
    * Kompiluj przepływ za pomocą łącznika, aby [QNA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker opublikowane informacje bazy wiedzy:
            * Identyfikator bazy wiedzy
            * Host punktu końcowego zasobów QnA Maker
            * Klucz punktu końcowego zasobu QnA Maker
        * Zapytanie użytkownika danych wejściowych
        * Dane wyjściowe — odpowiedź z bazy wiedzy
    * Utwórz rozwiązanie i Dodaj przepływ.
* Wróć do agentów wirtualnych:
    * Wybierz dane wyjściowe rozwiązania jako komunikat dla tematu.

## <a name="create-and-publish-a-knowledge-base"></a>Tworzenie i publikowanie bazy wiedzy

1. Postępuj zgodnie z [przewodnikiem Szybki Start](../Quickstarts/create-publish-knowledge-base.md) , aby utworzyć bazę wiedzy. Nie Dokończ ostatniej sekcji o tworzeniu bot. Zamiast tego Skorzystaj z tego samouczka, aby utworzyć bot za pomocą zaawansowanych agentów.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opublikowane ustawienia bazy wiedzy](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Wprowadź informacje o opublikowanych ustawieniach bazy wiedzy, które znajdują się na stronie **Ustawienia** w portalu [QNA Maker](https://www.qnamaker.ai/) . [Te informacje będą potrzebne do](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) skonfigurowania połączenia QNA Maker `GenerateAnswer` .

1. W portalu QnA Maker na stronie **Ustawienia** Znajdź klucz punktu końcowego, hosta punktu końcowego i identyfikator bazy wiedzy.

## <a name="create-an-agent-in-power-virtual-agents"></a>Tworzenie agenta w usłudze Virtual Machine Agents

1. [Zaloguj się do agentów z możliwością zarządzania](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Użyj konta służbowego poczty e-mail.
1. Jeśli jest to Twój pierwszy bot, zobaczysz stronę **główną** agenta. Jeśli nie jest to pierwsze bot, zaznacz bot w prawym górnym rogu strony i wybierz pozycję **+ nowy Bot**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę główną agentów programu PowerShell](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Wprowadź informacje o opublikowanych ustawieniach bazy wiedzy, które znajdują się na stronie **Ustawienia** w portalu [QNA Maker](https://www.qnamaker.ai/) .

## <a name="topics-provided-in-the-bot"></a>Tematy dostępne w bot

Agent używa kolekcji tematów do odpowiedzi na pytania w obszarze tematu. W tym samouczku agent ma wiele tematów dostępnych dla Ciebie, podzielony na tematy użytkownika i tematy systemowe.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu tematów znajdujących się w agencie](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Tworzenie tematu rezerwowego systemu

Mimo że Agent może połączyć się z bazą wiedzy z dowolnego tematu, w tym samouczku jest używany temat *rezerwy systemowej* . Temat rezerwowy jest używany, gdy Agent nie może znaleźć odpowiedzi. Agent przekazuje tekst użytkownika do `GenerateAnswer` interfejsu API QNA Maker, otrzymuje odpowiedź z bazy wiedzy i wyświetla go użytkownikowi jako wiadomość.

1. W portalu dla [agentów programu PowerShell](https://powerva.microsoft.com/#/) w prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego). Następnie wybierz pozycję **rezerwa systemu**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający element menu agentów wirtualnych dodatku dla powrotu systemu](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Wybierz pozycję **+ Dodaj** , aby dodać temat rezerwowy systemu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający dodawanie tematu powrotu.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Po dodaniu tematu wybierz pozycję **Przejdź do rezerwowego tematu** , aby utworzyć temat rezerwowy na kanwie tworzenia.

    > [!TIP]
    > Jeśli musisz powrócić do tematu rezerwowego, jest on dostępny w sekcji **Tematy** jako część tematów **systemowych** .

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Dodawanie akcji przy użyciu kanwy tworzenia

Użyj kanwy tworzenie agentów wirtualnych, aby połączyć temat rezerwowy z bazą wiedzy. Temat rozpoczyna się od nierozpoznanego tekstu użytkownika. Dodaj akcję, która przekazuje ten tekst do QnA Maker, a następnie wyświetli odpowiedź jako wiadomość. Ostatni krok wyświetlania odpowiedzi jest obsługiwany jako [osobny krok](#add-your-solutions-flow-to-power-virtual-agents)w dalszej części tego samouczka.

Ta sekcja tworzy przepływ konwersacji tematu rezerwowego.

1. Nowa akcja rezerwowa może już mieć elementy przepływu konwersacji. Usuń element **eskalacji** , wybierając menu **Opcje** .

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający temat rezerwowych agentów wirtualnych](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Wybierz z **+** okna **komunikatu** łącznik, a następnie wybierz polecenie **Wywołaj akcję**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu wywołującego akcję](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Wybierz pozycję **Utwórz przepływ**. Ten proces przenosi do portalu automatyzacji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający tworzenie przepływu](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Tworzenie przepływu automatyzacji w celu nawiązania połączenia z bazą wiedzy

Poniższa procedura umożliwia utworzenie przepływu automatyzacji, który:
* Przyjmuje przychodzący tekst użytkownika i wysyła go do QnA Maker.
* Przypisuje QnA Maker największą odpowiedź do zmiennej i wysyła zmienną (największą odpowiedź) jako odpowiedź z powrotem do agenta.

1. W programie **energia automatyzuje** **szablon przepływu** jest uruchamiany. W obszarze przepływ **agentów usługi Virtual Machines** wybierz pozycję **Edytuj** , aby skonfigurować zmienną wejściową pochodzącą od agenta do bazy wiedzy. Zmienna wejściowa oparta na tekście to pytanie tekstowe przesłane przez użytkownika z agenta.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu opcji automatyzacji w celu skonfigurowania zmiennej wejściowej jako ciągu tekstowego](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Dodaj tekst wejściowy i nadaj nazwę zmiennej `InputText`, z opisem. `IncomingUserQuestion` Ta nazwa ułatwia odróżnienie tekstu wejściowego od utworzonego tekstu wyjściowego.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu opcji automatyzacji zaawansowanej, aby skonfigurować nazwę i opis zmiennej wejściowej](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Wybierz **+** łącznik przepływający z usługi **PowerShell Virtual Agents** , aby wstawić nowy krok przepływu (przed **wartością zwracaną do agenta wirtualnego**). Następnie wybierz pozycję **Dodaj akcję**.

1. Wyszukaj, `Qna` aby znaleźć **QNA Maker** akcje, a następnie wybierz pozycję **Generuj odpowiedź**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający generowanie odpowiedzi](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Wymagane ustawienia połączenia dla QnA Maker są wyświetlane w akcji i ustawieniach pytania agenta.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wymagane ustawienia połączenia](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Skonfiguruj akcję z IDENTYFIKATORem bazy wiedzy, hostem punktu końcowego i kluczem punktu końcowego. Są one dostępne na stronie **Ustawienia** bazy wiedzy w portalu QNA Maker.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opublikowane ustawienia bazy wiedzy](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Aby skonfigurować **pytanie**, zaznacz pole tekstowe, a następnie wybierz `InputText` z listy.

1. Aby wstawić nowy krok w przepływie, wybierz **+** łącznik z pola **Generuj odpowiedź** . Następnie wybierz pozycję **Dodaj akcję**.

1. Aby dodać zmienną do przechwytywania tekstu odpowiedzi zwróconego z `GenerateAnswer`, Wyszukaj i wybierz `Initialize variable` akcję.

    Ustaw nazwę zmiennej na `OutgoingQnAAnswer`, a następnie wybierz typ jako **ciąg**. Nie ustawiaj **wartości**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu inicjującego inicjowanie zmiennej wyjściowej](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Aby wstawić nowy krok w przepływie, wybierz pozycję **+** łącznik przepływający z pola Akcja **zainicjuj zmienną** . Następnie wybierz pozycję **Dodaj akcję**.

1. Aby ustawić dla zmiennej całą odpowiedź JSON bazy wiedzy, Wyszukaj i wybierz`Apply to each` akcję. `GenerateAnswer` `answers`Wybierz pozycję.

1. Aby zwrócić tylko górną odpowiedź, w tej samej tabeli **Zastosuj do każdego** pola wybierz pozycję **Dodaj akcję**. Wyszukaj i wybierz pozycję **Ustaw zmienną**.

    W polu **Ustaw zmienną** zaznacz pole tekstowe **Nazwa**, a następnie na liście wybierz pozycję **OutgoingQnAAnswer** .

    Zaznacz pole tekstowe **wartość**, a następnie wybierz pozycję **odpowiedzi** z listy.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Ustawianie nazwy i wartości dla zmiennej](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Aby zwrócić zmienną (i jej wartość), wybierz **wartość zwracaną (s) dla elementu przepływu agenta wirtualnego** . Następnie wybierz pozycję **Edytuj** > **Dodaj dane wyjściowe**. Wybierz typ **danych wyjściowych** , a następnie wprowadź **tytuł** `FinalAnswer`. Zaznacz pole tekstowe dla **wartości**, a następnie wybierz `OutgoingQnAAnswer` zmienną.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający ustawienie wartości zwracanej](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Aby zapisać zmiany, wybierz opcję **Zapisz**.

## <a name="create-a-solution-and-add-the-flow"></a>Tworzenie rozwiązania i Dodawanie przepływu

Aby Agent mógł znaleźć i połączyć się z przepływem, przepływ musi być uwzględniony w rozwiązaniu do automatyzacji.

1. Mimo że w portalu usługi Automatyzowanie, wybierz pozycję **rozwiązania** z nawigacji po lewej stronie.

1. Wybierz pozycję **+ Nowe rozwiązanie**.

1. Podaj nazwę wyświetlaną. Lista rozwiązań obejmuje każde rozwiązanie w organizacji lub szkole. Wybierz konwencję nazewnictwa, która pomaga w filtrowaniu do tylko twoich rozwiązań. Na przykład możesz poprzedzić swoją wiadomość e-mail do nazwy rozwiązania: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Wybierz wydawcę z listy opcji.

1. Zaakceptuj wartości domyślne dla nazwy i wersji.

1. Wybierz pozycję **Utwórz** , aby zakończyć proces.

## <a name="add-your-flow-to-the-solution"></a>Dodawanie przepływu do rozwiązania

1. Z listy rozwiązań wybierz właśnie utworzone rozwiązanie. Powinien znajdować się w górnej części listy. Jeśli tak nie jest, Wyszukaj według nazwy e-mail, która jest częścią nazwy rozwiązania.

1. W rozwiązaniu wybierz pozycję **+ Dodaj istniejące**, a następnie wybierz pozycję **przepływ** z listy.

1. Znajdź przepływ, a następnie wybierz pozycję **Dodaj** , aby zakończyć proces. Jeśli istnieje wiele przepływów, sprawdź **zmodyfikowaną** kolumnę, aby znaleźć najnowszy przepływ.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Dodawanie przepływu rozwiązania do agentów wirtualnych

1. Wróć do karty przeglądarki z agentem w usłudze Virtual Machine Agents. Kanwa tworzenia powinna być nadal otwarta.

1. Aby wstawić nowy krok w przepływie, w polu Akcja **komunikat** wybierz **+** łącznik. Następnie wybierz pozycję **Wywołaj akcję**.

1. W obszarze Nowa Akcja wybierz wartość wejściową **UnrecognizedTriggerPhrase**. Spowoduje to przekazanie tekstu z agenta do przepływu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu opcji agentów wirtualnych z możliwością wybrania nierozpoznanej frazy wyzwalacza](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Aby wstawić nowy krok w przepływie, w polu **Akcja** wybierz **+** łącznik. Następnie wybierz pozycję **Pokaż komunikat**.

1. Wprowadź tekst komunikatu `Your answer is:`. Wybierz `FinalAnswer` jako zmienną kontekstową przy użyciu funkcji na pasku narzędzi w miejscu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu opcji agentów wirtualnych w celu wprowadzenia tekstu komunikatu](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Na pasku narzędzi kontekstu wybierz pozycję **Zapisz**, aby zapisać szczegóły kanwy tworzenia dla tematu.

Oto jak wygląda Ostatnia Kanwa agenta.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający końcową kanwę agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testowanie agenta

1. W okienku test Przełącz opcję **Śledź między tematami**. Pozwala to obserwować postęp między tematami, a także w pojedynczym temacie.

1. Przetestuj agenta, wprowadzając tekst użytkownika w następującej kolejności. Kanwa tworzenia raportuje pomyślne kroki z zielonym znacznikiem wyboru.

    |Kolejność pytań|Pytania testowe|Przeznaczenie|
    |--|--|--|
    |1|Hello|Rozpocznij KONWERSACJĘ|
    |2|Godziny przechowywania|Przykładowy temat. Jest to skonfigurowane dla Ciebie bez żadnej dodatkowej pracy z Twoją częścią.|
    |3|Tak|W odpowiedzi na`Did that answer your question?`|
    |4|Excellent (Doskonały)|W odpowiedzi na`Please rate your experience.`|
    |5|Tak|W odpowiedzi na`Can I help with anything else?`|
    |6|Co to jest baza wiedzy?|To pytanie wyzwala akcję rezerwową, która wysyła do odpowiedzi tekst do bazy wiedzy. Następnie zostanie wyświetlona odpowiedź. |

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający końcową kanwę agenta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Opublikuj swój bot

Aby udostępnić agenta wszystkim członkom Twojej szkoły lub organizacji, musisz ją opublikować.

1. W obszarze nawigacji po lewej stronie wybierz pozycję **Publikuj**. Następnie wybierz pozycję **Publikuj** na stronie.

1. Wypróbuj bot w witrynie sieci Web demonstracyjnej (Poszukaj linku w obszarze **Publikowanie**).

    Zostanie otwarta nowa strona sieci Web z Twoim bot. Podawaj bot tego samego pytania testowego:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający końcową kanwę agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Udostępnianie bot

Aby udostępnić witrynę internetową demonstracyjną, skonfiguruj ją jako kanał.

1. W obszarze nawigacji po lewej stronie wybierz pozycję **Zarządzaj** > **kanałami**.

1. Wybierz pozycję **demonstracyjna witryna sieci Web** na liście kanały.

1. Skopiuj link i wybierz pozycję **Zapisz**. Wklej link do witryny internetowej demonstracyjnej w wiadomości e-mail do członków Twojej szkoły lub organizacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z bazą wiedzy Usuń QnA Maker zasoby z Azure Portal.

## <a name="next-step"></a>Następny krok

[Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy](../How-To/get-analytics-knowledge-base.md)

Dowiedz się więcej o usługach:
* [Agenci Power Virtual Agent](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QNA Maker łącznika](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) i [Ustawienia łącznika](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)