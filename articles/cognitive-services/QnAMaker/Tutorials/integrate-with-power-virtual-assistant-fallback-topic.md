---
title: 'Samouczek: Integracja z wirtualnymi agentami zasilania - QnA Maker'
titleSuffix: Azure Cognitive Services
description: W tym samouczku popraw jakość swojej bazy wiedzy dzięki aktywnemu uczeniu się. Przeglądaj, akceptuj, odrzucaj lub dodawaj bez usuwania lub zmieniania istniejących pytań.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402810"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Samouczek: Dodaj swoją bazę wiedzy do agentów wirtualnych zasilania
Tworzenie i rozszerzanie [bota agentów wirtualnych zasilania,](https://powervirtualagents.microsoft.com/) aby dostarczać odpowiedzi z bazy wiedzy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

<!-- green checkmark -->
> [!div class="checklist"]
> * Tworzenie bota agentów wirtualnych zasilania
> * Tworzenie tematu rezerwowego systemu
> * Dodawanie programu QnA Maker jako akcji do tematu jako przepływu automatyki do automatyki zasilania
> * Tworzenie rozwiązania Power Automate
> * Dodawanie przepływu automatyzacji zasilania do rozwiązania
> * Publikuj agentów wirtualnych zasilania
> * Przetestuj wirtualnych agentów mocy i otrzymaj odpowiedź z bazy wiedzy QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integracja agenta z bazą wiedzy

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) umożliwia zespołom tworzenie potężnych botów za pomocą interfejsu graficznego z przewodnikiem bez kodu. Nie potrzebujesz analityków danych ani deweloperów.

W power virtual agentów, można utworzyć agenta z serii tematów (obszarów tematycznych), aby odpowiedzieć na pytania użytkownika, wykonując akcje. Jeśli nie można znaleźć odpowiedzi, rezerwowy system może zwrócić odpowiedź.

Skonfiguruj agenta, aby wysłać pytanie do bazy wiedzy w ramach działania tematu lub jako część ścieżki tematu *rezerwowego systemu.* Obaj używają akcji, aby połączyć się z bazą wiedzy i zwrócić odpowiedź.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate łączy `GenerateAnswer` się z działaniem

Aby połączyć agenta z bazą wiedzy, użyj power automate, aby utworzyć akcję. Power Automate zapewnia przepływ procesu, który łączy się `GenerateAnswer` z api QnA Maker.

Po zaprojektowaniu i zapisaniu przepływu jest on dostępny w rozwiązaniu Power Automate. Użyj tego rozwiązania jako akcji w agencie.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Łączenie agenta z bazą wiedzy

Oto omówienie kroków, aby połączyć agenta w power virtual agentów do bazy wiedzy w QnA Maker.

* W portalu [QnA Maker:](https://www.qnamaker.ai/)
    * Tworzenie i publikowanie bazy wiedzy.
    * Skopiuj szczegóły bazy wiedzy, w tym identyfikator, klucz punktu końcowego środowiska uruchomieniowego i host punktu końcowego środowiska wykonawczego.
* W portalu [Power Virtual Agents:](https://powerva.microsoft.com/)
    * Tworzenie tematu agenta.
    * Wywołanie akcji (do przepływu Automatyzacja zasilania).
* W portalu [Power Automate:](https://us.flow.microsoft.com/)
    * Zbuduj przepływ za pomocą łącznika do [programu QnA Maker GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Firma QnA Maker opublikowała informacje o bazie wiedzy:
            * Identyfikator bazy wiedzy
            * Host punktu końcowego zasobu programu QnA Maker
            * Klucz punktu końcowego zasobu programu QnA Maker
        * Dane wejściowe — zapytanie użytkownika
        * Wyjście - odpowiedź bazy wiedzy
    * Utwórz rozwiązanie i dodaj przepływ.
* Powrót do agentów wirtualnych zasilania:
    * Wybierz dane wyjściowe rozwiązania jako komunikat dla tematu.

## <a name="create-and-publish-a-knowledge-base"></a>Tworzenie i publikowanie bazy wiedzy

1. Postępuj zgodnie z [przewodnikiem Szybki start,](../Quickstarts/create-publish-knowledge-base.md) aby utworzyć bazę wiedzy. Nie wypełniaj ostatniej sekcji na temat tworzenia bota. Zamiast tego użyj tego samouczka, aby utworzyć bota z agentami wirtualnymi zasilania.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opublikowane ustawienia bazy wiedzy](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Wprowadź opublikowane ustawienia bazy wiedzy znajdujące się na stronie **Ustawienia** w portalu [QnA Maker.](https://www.qnamaker.ai/) Te informacje będą potrzebne do [kroku Automatyzacja zasilania,](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) aby `GenerateAnswer` skonfigurować połączenie QnA Maker.

1. W portalu QnA Maker na stronie **Ustawienia** znajdź klucz punktu końcowego, hosta punktu końcowego i identyfikator bazy wiedzy.

## <a name="create-an-agent-in-power-virtual-agents"></a>Tworzenie agenta w power virtual agentów

1. [Zaloguj się do Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Użyj szkolnego lub służbowego konta e-mail.
1. Jeśli jest to twój pierwszy bot, będziesz na stronie **głównej** agenta. Jeśli nie jest to twój pierwszy bot, wybierz bota z prawego górnego obszaru strony i wybierz **+ Nowy Bot**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę główną power virtual agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Wprowadź opublikowane ustawienia bazy wiedzy znajdujące się na stronie **Ustawienia** w portalu [QnA Maker.](https://www.qnamaker.ai/)

## <a name="topics-provided-in-the-bot"></a>Tematy podane w bot

Agent używa kolekcji tematów, aby odpowiedzieć na pytania w obszarze tematu. W tym samouczku agent ma wiele tematów przewidzianych dla Ciebie, podzielony na tematy użytkownika i tematów systemowych.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający tematy podane w agencie](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Tworzenie tematu rezerwowego systemu

Mimo że agent może połączyć się z bazy wiedzy z dowolnego tematu, w tym samouczku używa *tematu rezerwowego systemu.* Temat rezerwowy jest używany, gdy agent nie może znaleźć odpowiedzi. Agent przekazuje tekst użytkownika do `GenerateAnswer` interfejsu API programu QnA Maker, otrzymuje odpowiedź z bazy wiedzy i wyświetla go użytkownikowi jako komunikat.

1. W portalu [Power Virtual Agents](https://powerva.microsoft.com/#/) w prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego). Następnie wybierz **opcję Rezerwa systemu**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający pozycję menu Power Virtual Agents dla rezerwowego systemu](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Wybierz **+ Dodaj,** aby dodać temat rezerwowy systemu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający dodawanie tematu rezerwowego.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Po dodaniu tematu wybierz **pozycję Przejdź do tematu rezerwowego,** aby zatworzeć temat rezerwowy na kanwie tworzenia.

    > [!TIP]
    > Jeśli chcesz powrócić do tematu rezerwowego, jest on dostępny w sekcji **Tematy,** jako część tematów **systemowych.**

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Dodawanie akcji za pomocą kanwy autora

Użyj kanwy tworzenia agentów wirtualnych zasilania, aby połączyć temat rezerwowy z bazą wiedzy. Temat rozpoczyna się od nierozpoznanego tekstu użytkownika. Dodaj akcję, która przekazuje ten tekst do programu QnA Maker, a następnie wyświetla odpowiedź jako wiadomość. Ostatni krok wyświetlania odpowiedzi jest traktowany jako [osobny krok,](#add-your-solutions-flow-to-power-virtual-agents)w dalszej części tego samouczka.

W tej sekcji tworzy przepływ konwersacji tematu rezerwowego.

1. Nowa akcja rezerwowa może już mieć elementy przepływu konwersacji. Usuń element **Eskalacja,** wybierając menu **Opcje.**

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający temat rezerwowy agentów wirtualnych zasilania](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Zaznacz **+** łącznik płynący z okna **Komunikat,** a następnie wybierz pozycję **Wywołaj akcję**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wywołanie akcji](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Wybierz pozycję **Utwórz przepływ**. Proces przenosi cię do portalu Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający tworzenie przepływu](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Tworzenie przepływu automatyzacji zasilania w celu połączenia się z bazą wiedzy

Poniższa procedura tworzy przepływ automatyzacji zasilania, który:
* Pobiera przychodzący tekst użytkownika i wysyła go do programu QnA Maker.
* Przypisuje pierwszą odpowiedź QnA Maker do zmiennej i wysyła zmienną (odpowiedź górną) jako odpowiedź z powrotem do agenta.

1. W **umięśna automatyzacja** **szablon przepływu** jest uruchamiany. W elemencie przepływu **agentów wirtualnych zasilania** wybierz pozycję **Edytuj,** aby skonfigurować zmienną wejściową pochodzącą z agenta do bazy wiedzy. Zmienna wejściowa oparta na tekście jest pytaniem tekstowym przesłanym przez użytkownika od agenta.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opcję Power Automate w celu skonfigurowania zmiennej wejściowej jako ciągu tekstowego](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Dodaj dane wejściowe tekstu `InputText`i nadaj `IncomingUserQuestion`zmienną z opisem . To nazewnictwo pomaga odróżnić tekst wejściowy od tekstu wyjściowego utworzonego później.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opcję Power Automate w celu skonfigurowania nazwy i opisu zmiennej wejściowej](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Wybierz **+** łącznik płynący z pola **Power Virtual Agents,** aby wstawić nowy krok w przepływie (przed **wartością zwracaną do agenta wirtualnego zasilania).** Następnie wybierz pozycję **Dodaj akcję**.

1. `Qna` Wyszukaj, aby znaleźć akcje **programu QnA Maker,** a następnie wybierz pozycję **Generuj odpowiedź**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający generowanie odpowiedzi](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Wymagane ustawienia połączenia dla programu QnA Maker są wyświetlane w akcji, a ustawienia pytań od agenta.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wymagane ustawienia połączenia](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Skonfiguruj akcję za pomocą identyfikatora bazy wiedzy, hosta punktu końcowego i klucza końcowego. Znajdują się one na stronie **Ustawienia** bazy wiedzy w portalu QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opublikowane ustawienia bazy wiedzy](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Aby skonfigurować **pytanie,** zaznacz pole tekstowe, `InputText` a następnie wybierz je z listy.

1. Aby wstawić nowy krok w **+** przepływie, wybierz łącznik płynący z pola **Akcji Generowanie odpowiedzi.** Następnie wybierz pozycję **Dodaj akcję**.

1. Aby dodać zmienną do przechwytywania `GenerateAnswer`tekstu odpowiedzi zwróconego z , wyszukaj `Initialize variable` i wybierz akcję.

    Ustaw nazwę zmiennej `OutgoingQnAAnswer`na , i wybierz typ jako **Ciąg**. Nie ustawiaj **wartości**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający zainicjowanie zmiennej wyjściowej](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Aby wstawić nowy krok w **+** przepływie, wybierz łącznik płynący z pola akcji **Inicjuj zmienną.** Następnie wybierz pozycję **Dodaj akcję**.

1. Aby ustawić całą odpowiedź JSON bazy wiedzy na zmienną, wyszukaj i wybierz`Apply to each` akcję. Wybierz `GenerateAnswer` `answers`plik .

1. Aby zwrócić tylko górną odpowiedź, w tym samym **polu Zastosuj do każdego** wybierz pozycję Dodaj **akcję**. Wyszukaj i wybierz **pozycję Ustaw zmienną**.

    W polu **Ustaw zmienną** zaznacz pole tekstowe **Nazwa**, a następnie wybierz z listy pozycję **OutgoingQnAAnswer.**

    Zaznacz pole tekstowe **dla wartości ,** a następnie wybierz pozycję Odpowiedź **odpowiedzi** z listy.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający ustawienie nazwy i wartości zmiennej](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Aby zwrócić zmienną (i jej wartość), wybierz wartość zwrotu do przepływu **agenta wirtualnego zasilania.** Następnie wybierz pozycję **Edytuj** > **Dodaj dane wyjściowe**. Zaznacz typ wyjścia **tekst,** a **Title** następnie `FinalAnswer`wprowadź tytuł . Zaznacz pole tekstowe **dla wartości**, `OutgoingQnAAnswer` a następnie wybierz zmienną.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający ustawienie wartości zwracanej](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Aby zapisać zmiany, wybierz opcję **Zapisz**.

## <a name="create-a-solution-and-add-the-flow"></a>Tworzenie rozwiązania i dodawanie przepływu

Aby agent znalazł przepływ i się z tym połączył, przepływ musi być dołączony do rozwiązania Power Automate.

1. Będąc jeszcze w portalu Power Automate, wybierz **rozwiązania** z nawigacji po lewej stronie.

1. Wybierz pozycję **+ Nowe rozwiązanie**.

1. Podaj nazwę wyświetlaną. Lista rozwiązań zawiera każde rozwiązanie w organizacji lub szkole. Wybierz konwencję nazewnictwa, która ułatwia filtrowanie tylko do rozwiązań. Na przykład możesz prefiksuj wiadomość `jondoe-power-virtual-agent-qnamaker-fallback`e-mail na nazwę rozwiązania: .

1. Wybierz wydawcę z listy opcji.

1. Zaakceptuj domyślne wartości nazwy i wersji.

1. Wybierz **pozycję Utwórz,** aby zakończyć proces.

## <a name="add-your-flow-to-the-solution"></a>Dodaj przepływ do rozwiązania

1. Na liście rozwiązań wybierz rozwiązanie, które właśnie utworzyłeś. Powinien znajdować się na szczycie listy. Jeśli tak nie jest, wyszukaj według nazwy e-mail, która jest częścią nazwy rozwiązania.

1. W rozwiązaniu wybierz + **Dodaj istniejące**, a następnie wybierz **pozycję Przepływ** z listy.

1. Znajdź swój przepływ, a następnie wybierz pozycję **Dodaj,** aby zakończyć proces. Jeśli istnieje wiele przepływów, spójrz na **Modified** kolumny, aby znaleźć najnowszy przepływ.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Dodaj przepływ rozwiązania do agentów wirtualnych zasilania

1. Wróć do karty przeglądarki z agentem w Power Virtual Agents. Kanwa do tworzenia treści powinna być nadal otwarta.

1. Aby wstawić nowy krok w **Message** przepływie, w **+** polu Akcji Wiadomość wybierz łącznik. Następnie wybierz **pozycję Zadzwoń do akcji**.

1. W nowej akcji wybierz wartość wejściową **NierozpoznanyFrzew .** Spowoduje to przepuszcze tekst z agenta do przepływu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opcję Power Virtual Agents, aby wybrać nierozpoznaną frazę wyzwalacza](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Aby wstawić nowy krok w **Action** przepływie, w **+** polu Akcja wybierz łącznik. Następnie wybierz pozycję **Pokaż wiadomość**.

1. Wprowadź tekst wiadomości, `Your answer is:`. Wybierz `FinalAnswer` jako zmienną kontekstową za pomocą funkcji paska narzędzi w miejscu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opcję Power Virtual Agents, aby wprowadzić tekst wiadomości](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Na pasku narzędzi kontekstu wybierz pozycję **Zapisz**, aby zapisać szczegóły kanwy autorskiej dla tematu.

Oto jak wygląda ostatnie płótno agenta.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający kanwę agenta końcowego](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Przetestuj agenta

1. W okienku testowym przełącz połącz **utwór między tematami**. Dzięki temu można obserwować postęp między tematami, a także w jednym temacie.

1. Przetestuj agenta, wprowadzając tekst użytkownika w następującej kolejności. Kanwa tworzenia raportuje pomyślne kroki z zielonym znacznikiem wyboru.

    |Kolejność pytań|Pytania testowe|Przeznaczenie|
    |--|--|--|
    |1|Hello|Rozpocznij konwersację|
    |2|Godziny przechowywania|Przykładowy temat. Jest to skonfigurowane dla Ciebie bez żadnej dodatkowej pracy z Twojej strony.|
    |3|Tak|W odpowiedzi na`Did that answer your question?`|
    |4|Excellent (Doskonały)|W odpowiedzi na`Please rate your experience.`|
    |5|Tak|W odpowiedzi na`Can I help with anything else?`|
    |6|Co to jest baza wiedzy?|To pytanie wyzwala akcję rezerwową, która wysyła tekst do bazy wiedzy, aby odpowiedzieć. Następnie wyświetlana jest odpowiedź. |

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający kanwę agenta końcowego](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Opublikuj swojego bota

Aby udostępnić agenta wszystkim członkom szkoły lub organizacji, musisz go opublikować.

1. Z nawigacji po lewej stronie wybierz pozycję **Publikuj**. Następnie wybierz pozycję **Publikuj** na stronie.

1. Wypróbuj bota na stronie demo (poszukaj linku w obszarze **Publikuj).**

    Otworzy się nowa strona internetowa z botem. Zadaj botowi to samo pytanie testowe:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający kanwę agenta końcowego](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Udostępnij swojego bota

Aby udostępnić witrynę demonstracyjną, skonfiguruj ją jako kanał.

1. Z nawigacji po lewej stronie wybierz pozycję **Zarządzaj** > **kanałami**.

1. Wybierz **pozycję Demonstruj witrynę sieci Web** z listy kanałów.

1. Skopiuj łącze i wybierz pozycję **Zapisz**. Wklej link do witryny demonstracyjnej w wiadomości e-mail do członków szkoły lub organizacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z bazy wiedzy, usuń zasoby QnA Maker w witrynie Azure portal.

## <a name="next-step"></a>Następny krok

[Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy](../How-To/get-analytics-knowledge-base.md)

Dowiedz się więcej o usługach:
* [Agenci Power Virtual Agent](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Złącze QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) i [ustawienia złącza](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)