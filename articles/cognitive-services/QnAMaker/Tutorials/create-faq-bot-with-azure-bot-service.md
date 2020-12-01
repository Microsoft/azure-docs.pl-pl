---
title: 'Samouczek: Tworzenie bot często zadawanych pytań za pomocą Azure Bot Service'
description: W tym samouczku utworzysz nieBote często zadawane pytania dotyczące QnA Maker i Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: 356b954c65086ff65264ce7a62b038cd7a8eac3d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350963"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Samouczek: Tworzenie bot często zadawanych pytań za pomocą Azure Bot Service
Utwórz bot często zadawanych pytań z usługami QnA Maker i Azure [bot Service](https://azure.microsoft.com/services/bot-service/) bez kodu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

<!-- green checkmark -->
> [!div class="checklist"]
> * Łączenie bazy wiedzy QnA Maker z Azure Bot Service
> * Wdróż bot
> * Czat z botem w rozmowie w sieci Web
> * Lekkie bot w obsługiwanych kanałach

## <a name="create-and-publish-a-knowledge-base"></a>Tworzenie i publikowanie bazy wiedzy

Postępuj zgodnie z [przewodnikiem Szybki Start](../Quickstarts/create-publish-knowledge-base.md) , aby utworzyć bazę wiedzy. Po pomyślnym opublikowaniu bazy wiedzy uzyskasz dostęp do poniższej strony.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

![Zrzut ekranu przedstawiający pomyślne opublikowanie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

![Zrzut ekranu przedstawiający pomyślne opublikowanie](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint-managed.png)

---

## <a name="create-a-bot"></a>Utwórz bot

Po opublikowaniu można utworzyć Bot na stronie **publikowania** :

* Możesz szybko utworzyć kilka botów, wskazując tę samą bazę wiedzy dla różnych regionów lub planów cenowych dla poszczególnych botówów.
* Jeśli potrzebujesz tylko jednego bot dla bazy wiedzy, użyj **widoku wszystkie Twoje botów w** łączu Azure Portal, aby wyświetlić listę bieżącego botów.

Po wprowadzeniu zmian w bazie wiedzy i ponownym opublikowaniu nie trzeba podejmować dalszych działań z bot. Jest już skonfigurowana do pracy z bazą wiedzy i współpracuje ze wszystkimi przyszłymi zmianami w bazie wiedzy. Za każdym razem, gdy publikujesz bazę wiedzy, wszystkie połączone z nią botów są automatycznie aktualizowane.

1. W portalu QnA Maker na stronie **Publikowanie** wybierz pozycję **Utwórz bot**. Ten przycisk jest wyświetlany dopiero po opublikowaniu bazy wiedzy.

     # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

    ![Zrzut ekranu przedstawiający tworzenie bot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

    ![Zrzut ekranu przedstawiający tworzenie bot Managed Preview](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page-managed.png)

    ---
    

1. Na stronie tworzenia Azure Bot Service zostanie otwarta nowa karta przeglądarki dla Azure Portal. Skonfiguruj usługę Azure bot. Bot i QnA Maker mogą korzystać z planu usługi aplikacji sieci Web, ale nie mogą udostępniać aplikacji sieci Web. Oznacza to, że **Nazwa aplikacji** dla bot musi być inna niż nazwa aplikacji dla usługi QNA Maker.

    * **Nie**
        * Zmień obsługę bot — Jeśli nie jest unikatowa.
        * Wybierz pozycję język zestawu SDK. Po utworzeniu bot można pobrać kod do lokalnego środowiska deweloperskiego i kontynuować proces opracowywania.
    * **Zakazy**
        * Podczas tworzenia bot Zmień poniższe ustawienia w Azure Portal. Są one wstępnie wypełnione dla istniejącej bazy wiedzy:
           * Klucz uwierzytelniania QnA
           * Plan i lokalizacja usługi App Service


1. Po utworzeniu bot Otwórz zasób **usługi bot** .
1. W obszarze **Zarządzanie bot** wybierz pozycję **Testuj w rozmowie w sieci Web**.
1. Po wyświetleniu monitu czatu o **wpisanie komunikatu** wpisz:

    `Azure services?`

    Bot rozmowy reaguje na odpowiedź z bazy wiedzy.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Wprowadź zapytanie użytkownika do programu testowego rozmowy w sieci Web.":::
1. PoBot się z dodatkowymi [obsługiwanymi kanałami](/azure/bot-service/bot-service-manage-channels?preserve-view=true&view=azure-bot-service-4.0).