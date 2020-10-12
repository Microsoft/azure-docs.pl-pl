---
title: 'Samouczek: integracja z dodatkami do zaawansowanych agentów — QnA Maker'
description: W tym samouczku poprawisz jakość bazy wiedzy o aktywnej uczeniu. Przejrzyj, zaakceptuj lub Odrzuć lub Dodaj bez usuwania lub zmiany istniejących pytań.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: 42b50fcf0df27ddbc3e587a7d8e038e4979935ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777420"
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
    * Wyszukaj w celu _wygenerowania odpowiedzi przy użyciu szablonu QNA Maker_
    * Użyj szablonu, aby skonfigurować przepływ do korzystania z [GenerateAnswer QNA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker opublikowane informacje bazy wiedzy:
            * Identyfikator bazy wiedzy
            * Host punktu końcowego zasobów QnA Maker
            * Klucz punktu końcowego zasobu QnA Maker
        * Zapytanie użytkownika danych wejściowych
        * Dane wyjściowe — odpowiedź z bazy wiedzy
    * Utwórz rozwiązanie i Dodaj przepływ lub Dodaj przepływ do istniejącego rozwiązania.
* Wróć do agentów wirtualnych:
    * Wybierz dane wyjściowe rozwiązania jako komunikat dla tematu.

## <a name="create-and-publish-a-knowledge-base"></a>Tworzenie i publikowanie bazy wiedzy

1. Postępuj zgodnie z [przewodnikiem Szybki Start](../Quickstarts/create-publish-knowledge-base.md) , aby utworzyć bazę wiedzy. Nie Dokończ ostatniej sekcji o tworzeniu bot. Zamiast tego Skorzystaj z tego samouczka, aby utworzyć bot za pomocą zaawansowanych agentów.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający opublikowane ustawienia bazy wiedzy](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Wprowadź informacje o opublikowanych ustawieniach bazy wiedzy, które znajdują się na stronie **Ustawienia** w portalu [QNA Maker](https://www.qnamaker.ai/) . [Te informacje będą potrzebne do](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) skonfigurowania `GenerateAnswer` połączenia QNA Maker.

1. W portalu QnA Maker na stronie **Ustawienia** Znajdź klucz punktu końcowego, hosta punktu końcowego i identyfikator bazy wiedzy.

## <a name="create-an-agent-in-power-virtual-agents"></a>Tworzenie agenta w usłudze Virtual Machine Agents

1. [Zaloguj się do agentów z możliwością zarządzania](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Użyj konta służbowego poczty e-mail.

1. Jeśli jest to Twój pierwszy bot, zobaczysz stronę **główną** agenta. Jeśli nie jest to pierwsze bot, zaznacz bot w prawym górnym rogu strony i wybierz pozycję **+ nowy Bot**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę główną agentów programu PowerShell](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Tematy dostępne w bot

Agent używa kolekcji tematów do odpowiedzi na pytania w obszarze tematu. W tym samouczku agent ma wiele tematów dostępnych dla Ciebie, podzielony na tematy użytkownika i tematy systemowe.

Wybierz **Tematy** z okienka nawigacji po lewej stronie, aby wyświetlić tematy udostępniane przez bot.

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

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

1. Wybierz **+** przepływanie łącznika do okna **komunikatu** , a następnie wybierz pozycję **Wywołaj akcję**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

1. Wybierz pozycję **Utwórz przepływ**. Ten proces przenosi do portalu automatyzacji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający tworzenie przepływu](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Zostanie otwarty nowy szablon. Ten nowy szablon nie zostanie użyty.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Tworzenie przepływu automatyzacji w celu nawiązania połączenia z bazą wiedzy

Poniższa procedura umożliwia utworzenie przepływu automatyzacji, który:
* Przyjmuje przychodzący tekst użytkownika i wysyła go do QnA Maker.
* Zwraca najwyższą odpowiedź z powrotem do agenta.

1. W obszarze **Automatyzacja**naciśnij pozycję **Szablony** na lewym pasku nawigacyjnym. Jeśli zostanie wyświetlony monit, czy chcesz opuścić stronę przeglądarki, zaakceptuj opuszczenie.

1. Na stronie Szablony Wyszukaj szablon **generowanie odpowiedzi przy użyciu QNA Maker** następnie wybierz szablon. Ten szablon zawiera wszystkie kroki, które należy wywołać QnA Maker z ustawieniami bazy wiedzy i zwrócić największą odpowiedź.

1. Na nowym ekranie przepływu QnA Maker wybierz pozycję **Kontynuuj**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

1. Wybierz pole **Generuj akcję odpowiedzi** i wypełnij ustawienia QNA Maker w poprzedniej sekcji zatytułowanej [Tworzenie i publikowanie bazy wiedzy](#create-and-publish-a-knowledge-base). Twój **host usługi** na poniższej ilustracji odwołuje się do Twojego **hosta** hosta bazy wiedzy i ma format `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` .


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

1. Aby zapisać zmiany, wybierz opcję **Zapisz**.

## <a name="create-a-solution-and-add-the-flow"></a>Tworzenie rozwiązania i Dodawanie przepływu

Aby Agent mógł znaleźć i połączyć się z przepływem, przepływ musi być uwzględniony w rozwiązaniu do automatyzacji.

1. Mimo że w portalu usługi Automatyzowanie, wybierz pozycję **rozwiązania** z nawigacji po lewej stronie.

1. Wybierz pozycję **+ Nowe rozwiązanie**.

1. Podaj nazwę wyświetlaną. Lista rozwiązań obejmuje każde rozwiązanie w organizacji lub szkole. Wybierz konwencję nazewnictwa, która pomaga w filtrowaniu do tylko twoich rozwiązań. Na przykład możesz poprzedzić swoją wiadomość e-mail do nazwy rozwiązania: `jondoe-power-virtual-agent-qnamaker-fallback` .

1. Wybierz wydawcę z listy opcji.

1. Zaakceptuj wartości domyślne dla nazwy i wersji.

1. Wybierz pozycję **Utwórz** , aby zakończyć proces.

## <a name="add-your-flow-to-the-solution"></a>Dodawanie przepływu do rozwiązania

1. Z listy rozwiązań wybierz właśnie utworzone rozwiązanie. Powinien znajdować się w górnej części listy. Jeśli tak nie jest, Wyszukaj według nazwy e-mail, która jest częścią nazwy rozwiązania.

1. W rozwiązaniu wybierz pozycję **+ Dodaj istniejące**, a następnie wybierz pozycję **przepływ** z listy.

1. Znajdź swój przepływ z listy **rozwiązań zewnętrznych** , a następnie wybierz pozycję **Dodaj** , aby zakończyć proces. Jeśli istnieje wiele przepływów, sprawdź **zmodyfikowaną** kolumnę, aby znaleźć najnowszy przepływ.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Dodawanie przepływu rozwiązania do agentów wirtualnych

1. Wróć do karty przeglądarki z agentem w usłudze Virtual Machine Agents. Kanwa tworzenia powinna być nadal otwarta.

1. Aby wstawić nowy krok w przepływie, powyżej pola Akcja **komunikat** wybierz **+** łącznik. Następnie wybierz pozycję **Wywołaj akcję**.

1. W oknie podręcznym **przepływ** wybierz nowy przepływ o nazwie **Generuj odpowiedzi przy użyciu QNA Maker baza wiedzy..**.. Nowa akcja zostanie wyświetlona w przepływie.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

1. Aby prawidłowo ustawić zmienną wejściową dla akcji QnA Maker, wybierz pozycję **Wybierz zmienną**, a następnie wybierz pozycję **bot. UnrecognizedTriggerPhrase**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::


1. Aby prawidłowo ustawić zmienną wyjściową dla akcji QnA Maker, w akcji **komunikat** wybierz pozycję **UnrecognizedTriggerPhrase**, a następnie wybierz ikonę, aby wstawić zmienną, `{x}` a następnie wybierz pozycję **FinalAnswer**.

1. Na pasku narzędzi kontekstu wybierz pozycję **Zapisz**, aby zapisać szczegóły kanwy tworzenia dla tematu.

Oto jak wygląda Ostatnia Kanwa agenta.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawia kanwę agenta końcowego z frazami wyzwalacza, akcją, a następnie sekcjami komunikatów.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testowanie agenta

1. W okienku test Przełącz opcję **Śledź między tematami**. Pozwala to obserwować postęp między tematami, a także w pojedynczym temacie.

1. Przetestuj agenta, wprowadzając tekst użytkownika w następującej kolejności. Kanwa tworzenia raportuje pomyślne kroki z zielonym znacznikiem wyboru.

    |Kolejność pytań|Pytania testowe|Przeznaczenie|
    |--|--|--|
    |1|Hello|Rozpocznij KONWERSACJĘ|
    |2|Godziny przechowywania|Przykładowy temat. Jest to skonfigurowane dla Ciebie bez żadnej dodatkowej pracy z Twoją częścią.|
    |3|Tak|W odpowiedzi na `Did that answer your question?`|
    |4|Doskonałe|W odpowiedzi na `Please rate your experience.`|
    |5|Tak|W odpowiedzi na `Can I help with anything else?`|
    |6|Jak można poprawić przepływność dla prognoz zapytania?|To pytanie wyzwala akcję rezerwową, która wysyła do odpowiedzi tekst do bazy wiedzy. Następnie zostanie wyświetlona odpowiedź. zielone znaczniki wyboru dla poszczególnych akcji wskazują na powodzenie dla każdej akcji.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Częściowy zrzut ekranu przepływu konwersacji z wyróżnioną opcją usuwania.":::

## <a name="publish-your-bot"></a>Opublikuj swój bot

Aby udostępnić agenta wszystkim członkom Twojej szkoły lub organizacji, musisz ją opublikować.

1. W obszarze nawigacji po lewej stronie wybierz pozycję **Publikuj**. Następnie wybierz pozycję **Publikuj** na stronie.

1. Wypróbuj bot w witrynie sieci Web demonstracyjnej (Poszukaj linku w obszarze **Publikowanie**).

    Zostanie otwarta nowa strona sieci Web z Twoim bot. Podawaj bot tego samego pytania testowego: `How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający końcową kanwę agenta](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Udostępnianie bot

Aby udostępnić witrynę internetową demonstracyjną, skonfiguruj ją jako kanał.

1. W obszarze nawigacji po lewej stronie wybierz pozycję **Zarządzaj**  >  **kanałami**.

1. Wybierz pozycję **demonstracyjna witryna sieci Web** na liście kanały.

1. Skopiuj link i wybierz pozycję **Zapisz**. Wklej link do witryny internetowej demonstracyjnej w wiadomości e-mail do członków Twojej szkoły lub organizacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z bazą wiedzy Usuń QnA Maker zasoby z Azure Portal.

## <a name="next-step"></a>Następny krok

[Uzyskiwanie danych analitycznych na potrzeby bazy wiedzy](../How-To/get-analytics-knowledge-base.md)

Dowiedz się więcej na następujące tematy:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QNA Maker łącznika](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) i [Ustawienia łącznika](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)