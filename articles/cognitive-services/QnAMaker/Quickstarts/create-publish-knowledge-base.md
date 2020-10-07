---
title: 'Szybki Start: Tworzenie, uczenie i publikowanie bazy wiedzy — QnA Maker'
description: Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Ten artykuł zawiera przykład tworzenia bazy wiedzy QnA Makerej na podstawie prostej strony internetowej często zadawanych pytań i odpowiedzi na nie QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 1646666244eb2247d6e7127c4d4e8d39cb0af5ff
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777692"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Szybki Start: Tworzenie, uczenie i publikowanie bazy wiedzy QnA Maker

Na podstawie własnej zawartości, takiej jak często zadawane pytania lub podręczniki produktów, możesz utworzyć bazę wiedzy usługi QnA Maker. Ten artykuł zawiera przykład tworzenia bazy wiedzy QnA Makerej na podstawie prostej strony internetowej często zadawanych pytań i odpowiedzi na nie QnA Maker.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).
> * Utworzono [zasób](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker w Azure Portal. Pamiętaj o IDENTYFIKATORze Azure Active Directory, subskrypcji, nazwie zasobu QnA wybranym podczas tworzenia zasobu.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Tworzenie pierwszej QnA Maker bazy wiedzy

1. Zaloguj się do portalu [QnAMaker.AI](https://QnAMaker.ai) przy użyciu poświadczeń platformy Azure.

1. W portalu QnA Maker wybierz pozycję **Utwórz bazę wiedzy**.

1. Na stronie **Tworzenie** przejdź do **kroku 1** , jeśli masz już zasób QNA Maker.

    Jeśli jeszcze nie utworzono zasobu, wybierz pozycję **Utwórz usługę QNA**. Nastąpi przekierowanie do witryny [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) w celu skonfigurowania usługi QnA Maker w ramach subskrypcji. Pamiętaj o IDENTYFIKATORze Azure Active Directory, subskrypcji, nazwie zasobu QnA wybranym podczas tworzenia zasobu.

    Po zakończeniu tworzenia zasobu w Azure Portal Wróć do portalu QnA Maker, Odśwież stronę przeglądarki i przejdź do **kroku 2**.

1. W **kroku 3**wybierz pozycję Active Directory, subskrypcję, usługę (zasób) i język dla wszystkich baz wiedzy utworzonych w usłudze.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service":::

1. W **kroku 3**nazwij swoją bazę wiedzy **QNA KB**.

1. W **kroku 4**Skonfiguruj ustawienia w następującej tabeli:

    |Ustawienie|Wartość|
    |--|--|
    |**Włącz wyodrębnianie wieloskładnikowe z adresów URL, plików PDF lub DOCX.**|Zaznaczono|
    |**Domyślny tekst odpowiedzi**| `Quickstart - default answer not found.`|
    |**+ Dodaj adres URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Pogawędka**|Wybierz **profesjonalne**|

1. W **kroku 5**wybierz pozycję **Utwórz swoją KB**.

    Proces wyodrębniania zajmuje kilka chwil, aby odczytać dokument i zidentyfikować pytania i odpowiedzi.

    Po pomyślnym utworzeniu bazy wiedzy przez QnA Maker zostanie otwarta strona **bazy wiedzy** . Zawartość bazy wiedzy można edytować na tej stronie.

## <a name="add-a-new-question-and-answer-set"></a>Dodaj nowe pytanie i zestaw odpowiedzi

1. W portalu QnA Maker na stronie **Edycja** wybierz pozycję **+ Dodaj parę QNA** z poziomu paska narzędzi kontekstu.
1. Dodaj następujące pytanie:

    `How many Azure services are used by a knowledge base?`

1. Dodaj odpowiedź sformatowaną z uwzględnieniem _promocji_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service":::

    Symbol promocji, `*` ,, jest używany dla punktów punktorów. `\n`Służy do nowego wiersza.

    Na stronie **Edytuj** zostanie wyświetlona wartość promocji. Gdy używasz panelu **testowego** później, zobaczysz poprawność wyświetlana w promocji.

## <a name="save-and-train"></a>Zapisywanie i szkolenie

W prawym górnym rogu wybierz pozycję **Zapisz i pouczenie** , aby zapisać zmiany i QNA Maker uczenia. Zmiany nie są przechowywane, o ile nie zostaną zapisane.

## <a name="test-the-knowledge-base"></a>Testowanie bazy wiedzy

1. W portalu QnA Maker w prawym górnym rogu wybierz pozycję **test** , aby sprawdzić, czy wprowadzone zmiany zostały wprowadzone.
1. Wprowadź przykładowe zapytanie użytkownika w polu tekstowym.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service":::

1. Wybierz pozycję **Zbadaj**, aby bardziej szczegółowo sprawdzić odpowiedź. Okno testowe służy do testowania zmian w bazie wiedzy przed opublikowaniem bazy wiedzy.

1. Ponownie wybierz **test** , aby zamknąć panel **testu** .

## <a name="publish-the-knowledge-base"></a>Publikowanie bazy wiedzy

Po opublikowaniu bazy wiedzy zawartość bazy wiedzy jest przenoszona z `test` indeksu do `prod` indeksu w usłudze Azure Search.

![Zrzut ekranu przedstawiający przemieszczenie zawartości bazy wiedzy](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. W portalu QnA Maker wybierz pozycję **Publikuj**. Aby potwierdzić, wybierz na stronie pozycję **Opublikuj**.

    Usługa QnA Maker została teraz pomyślnie opublikowana. Możesz użyć punktu końcowego w swojej aplikacji lub kodu bota.

    ![Zrzut ekranu przedstawiający pomyślne opublikowanie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Utwórz bot

Po opublikowaniu można utworzyć Bot na stronie **publikowania** :

* Możesz szybko utworzyć kilka botów, wskazując tę samą bazę wiedzy dla różnych regionów lub planów cenowych dla poszczególnych botówów.
* Jeśli potrzebujesz tylko jednego bot dla bazy wiedzy, użyj **widoku wszystkie Twoje botów w** łączu Azure Portal, aby wyświetlić listę bieżącego botów.

Po wprowadzeniu zmian w bazie wiedzy i ponownym opublikowaniu nie trzeba podejmować dalszych działań z bot. Jest już skonfigurowana do pracy z bazą wiedzy i współpracuje ze wszystkimi przyszłymi zmianami w bazie wiedzy. Za każdym razem, gdy publikujesz bazę wiedzy, wszystkie połączone z nią botów są automatycznie aktualizowane.

1. W portalu QnA Maker na stronie **Publikowanie** wybierz pozycję **Utwórz bot**. Ten przycisk jest wyświetlany dopiero po opublikowaniu bazy wiedzy.

    ![Zrzut ekranu przedstawiający tworzenie bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Na stronie tworzenia Azure Bot Service zostanie otwarta nowa karta przeglądarki dla Azure Portal. Skonfiguruj usługę Azure bot. Bot i QnA Maker mogą korzystać z planu usługi aplikacji sieci Web, ale nie mogą udostępniać aplikacji sieci Web. Oznacza to, że **Nazwa aplikacji** dla bot musi być inna niż nazwa aplikacji dla usługi QNA Maker.

    * **Nie**
        * Zmień obsługę bot — Jeśli nie jest unikatowa.
        * Wybierz pozycję język zestawu SDK. Po utworzeniu bot można pobrać kod do lokalnego środowiska deweloperskiego i kontynuować proces opracowywania.
    * **Zakazy**
        * podczas tworzenia bot Zmień poniższe ustawienia w Azure Portal. Są one wstępnie wypełnione dla istniejącej bazy wiedzy:
           * Klucz uwierzytelniania QnA
           * Plan i lokalizacja usługi App Service


1. Po utworzeniu bot Otwórz zasób **usługi bot** .
1. W obszarze **Zarządzanie bot**wybierz pozycję **Testuj w rozmowie w sieci Web**.
1. Po wyświetleniu monitu czatu o **wpisanie komunikatu**wpisz:

    `Azure services?`

    Bot rozmowy reaguje na odpowiedź z bazy wiedzy.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Zrzut ekranu przedstawiający wybór bazy wiedzy usługi QnA Maker Service":::

## <a name="what-did-you-accomplish"></a>Co zostało wykonane?

Utworzono nową bazę wiedzy, dodano publiczny adres URL do bazy wiedzy, dodano własną parę QnA, przeszkolony, przetestowany i opublikowany w bazie wiedzy.

Po opublikowaniu bazy wiedzy utworzono bot i przetestowano bot.

Zostało to wykonane w ciągu kilku minut bez konieczności pisania kodu lub czyszczenia zawartości.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz kontynuować korzystania z następnego przewodnika Szybki Start, Usuń QnA Maker i zasoby struktury bot w Azure Portal.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie pytań za pomocą metadanych](add-question-metadata-portal.md)

Więcej informacji:

* [Format promocji w odpowiedziach](../reference-markdown-format.md)
* QnA Maker [źródła danych](../concepts/knowledge-base.md).


