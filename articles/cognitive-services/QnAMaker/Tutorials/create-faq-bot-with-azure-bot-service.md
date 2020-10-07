---
title: 'Samouczek: Tworzenie bot często zadawanych pytań za pomocą Azure Bot Service'
description: W tym samouczku utworzysz nieBote często zadawane pytania dotyczące QnA Maker i Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab78ac41be2f900d84e82c21e566f5250b2afd18
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777398"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Samouczek: Tworzenie bot często zadawanych pytań za pomocą Azure Bot Service
Utwórz bot często zadawanych pytań z usługami QnA Maker i Azure [bot Service](https://azure.microsoft.com/en-us/services/bot-service/) bez kodu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

<!-- green checkmark -->
> [!div class="checklist"]
> * Łączenie bazy wiedzy QnA Maker z Azure Bot Service
> * Wdróż bot
> * Czat z botem w rozmowie w sieci Web
> * Lekkie bot w obsługiwanych kanałach

## <a name="create-and-publish-a-knowledge-base"></a>Tworzenie i publikowanie bazy wiedzy

Postępuj zgodnie z [przewodnikiem Szybki Start](../Quickstarts/create-publish-knowledge-base.md) , aby utworzyć bazę wiedzy. Po pomyślnym opublikowaniu bazy wiedzy uzyskasz dostęp do poniższej strony.

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
        * Podczas tworzenia bot Zmień poniższe ustawienia w Azure Portal. Są one wstępnie wypełnione dla istniejącej bazy wiedzy:
           * Klucz uwierzytelniania QnA
           * Plan i lokalizacja usługi App Service


1. Po utworzeniu bot Otwórz zasób **usługi bot** .
1. W obszarze **Zarządzanie bot**wybierz pozycję **Testuj w rozmowie w sieci Web**.
1. Po wyświetleniu monitu czatu o **wpisanie komunikatu**wpisz:

    `Azure services?`

    Bot rozmowy reaguje na odpowiedź z bazy wiedzy.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Wprowadź zapytanie użytkownika do programu testowego rozmowy w sieci Web.":::
1. PoBot się z dodatkowymi [obsługiwanymi kanałami](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true).
