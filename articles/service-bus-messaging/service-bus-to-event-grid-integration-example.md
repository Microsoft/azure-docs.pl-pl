---
title: Obsługa zdarzeń Service Bus za pośrednictwem Event Grid przy użyciu Azure Logic Apps
description: W tym artykule przedstawiono procedurę obsługi zdarzeń Service Bus za pośrednictwem Event Grid przy użyciu Azure Logic Apps.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999071"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Samouczek: reagowanie na zdarzenia Azure Service Bus odbierane za pośrednictwem Azure Event Grid przy użyciu Azure Logic Apps
W tym samouczku dowiesz się, jak odpowiedzieć na zdarzenia Azure Service Bus, które są odbierane za pośrednictwem Azure Event Grid przy użyciu Azure Logic Apps. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Odbieranie komunikatów za pomocą usługi Logic Apps
W tym kroku utworzysz aplikację logiki platformy Azure, która odbiera zdarzenia Service Bus za pośrednictwem Azure Event Grid. 

1. Utwórz aplikację logiki w Azure Portal.
    1. Wybierz pozycję **+ Utwórz zasób**, wybierz pozycję **integracja**, a następnie wybierz pozycję **aplikacja logiki**. 
    2. Na stronie **aplikacja logiki — tworzenie** wprowadź **nazwę** aplikacji logiki.
    3. Wybierz swoją **subskrypcję** platformy Azure. 
    4. Wybierz pozycję **Użyj istniejącej** dla **grupy zasobów**, a następnie wybierz grupę zasobów, która została użyta do innych zasobów (takich jak funkcja platformy Azure, Service Bus przestrzeń nazw), która została utworzona wcześniej. 
    5. Wybierz **lokalizację** aplikacji logiki. 
    6. Wybierz pozycję **Recenzja + Utwórz**. 
    1. Na stronie **Recenzja i tworzenie** wybierz pozycję **Utwórz** , aby utworzyć aplikację logiki. 
1. Na stronie **projektant Logic Apps** wybierz pozycję **pusta aplikacja logiki** w obszarze **Szablony**. 
1. W projektancie wykonaj następujące czynności:
    1. Wyszukaj **Event Grid**. 
    2. Wybierz **, kiedy wystąpi zdarzenie zasobu — Azure Event Grid**. 

        ![Projektant Logic Apps — wybierz wyzwalacz Event Grid](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Wybierz pozycję **Zaloguj** i wprowadź swoje poświadczenia platformy Azure, a następnie wybierz pozycję **Zezwalaj na dostęp**. 
5. Na stronie **gdy wystąpi zdarzenie zasobu** wykonaj następujące czynności:
    1. Wybierz swoją subskrypcję platformy Azure. 
    2. W obszarze **Typ zasobu** wybierz pozycję **Microsoft. ServiceBus. Namespaces**. 
    3. W obszarze **nazwa zasobu** wybierz przestrzeń nazw Service Bus. 
    4. Wybierz pozycję **Dodaj nowy parametr**, a następnie wybierz pozycję **Filtr sufiksów**. 
    5. W polu **Filtr sufiksu** wprowadź nazwę drugiej subskrypcji tematu Service Bus. 
        ![Projektant Logic Apps — Konfigurowanie zdarzenia](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Wybierz pozycję **+ nowy krok** w projektancie, a następnie wykonaj następujące czynności:
    1. Wyszukaj **Service Bus**.
    2. Na liście wybierz pozycję **Service Bus** . 
    3. Wybierz pozycję dla opcji **Pobierz komunikaty** na liście **Akcje** . 
    4. Wybierz pozycję **Pobierz komunikaty z subskrypcji tematu (Zablokuj)**. 

        ![Logic Apps Designer — pobieranie komunikatów](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Wprowadź **nazwę połączenia**. Na przykład: **Pobierz komunikaty z subskrypcji tematu** i wybierz Service Bus przestrzeni nazw. 

        ![Projektant Logic Apps — wybierz Service Bus przestrzeń nazw](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Wybierz pozycję **RootManageSharedAccessKey**, a następnie wybierz pozycję **Utwórz**.

        ![Projektant Logic Apps — wybierz klucz dostępu współdzielonego](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Wybierz swój **temat** i **subskrypcję**. 
    
        ![Zrzut ekranu pokazujący, gdzie wybierasz swój temat i subskrypcję.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Wybierz pozycję **+ nowy krok** i wykonaj następujące czynności: 
    1. Wybierz pozycję **Service Bus**.
    2. Wybierz pozycję **Zakończ wiadomość w subskrypcji tematu** z listy akcji. 
    3. Wybierz **temat** Service Bus.
    4. Wybierz drugą **subskrypcję** w temacie.
    5. W **polu token blokady wiadomości** wybierz opcję **Zablokuj token** z **zawartości dynamicznej**. 

        ![Logic Apps Designer — Ukończ ten komunikat](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Wybierz pozycję **Zapisz** na pasku narzędzi w projektancie Logic Apps, aby zapisać aplikację logiki. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Zapisz aplikację logiki":::
1. Jeśli wiadomości testowe nie zostały jeszcze wysłane do tematu, postępuj zgodnie z instrukcjami w sekcji [wysyłanie komunikatów do Service Bus tematu](#send-messages-to-the-service-bus-topic) , aby wysłać komunikaty do tematu. 
1. Przejdź do strony **Przegląd** aplikacji logiki. Zostanie wyświetlona aplikacja logiki uruchamiana w **historii uruchamiania** dla wysłanych komunikatów. Uruchomienie aplikacji logiki może potrwać kilka minut. Na pasku narzędzi wybierz pozycję **Odśwież** , aby odświeżyć stronę. 

    ![Logic Apps Designer — uruchomienia aplikacji logiki](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Wybierz przebieg aplikacji logiki, aby wyświetlić szczegóły. Zwróć uwagę, że Przetworzono 5 komunikatów w pętli for. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Szczegóły przebiegu aplikacji logiki":::    

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie widzisz żadnych wywołań po odczekaniu i odświeżeniu w jakiś sposób, wykonaj następujące kroki: 

1. Upewnij się, że komunikaty osiągnęły temat Service Bus. Zobacz licznik **komunikaty przychodzące** na stronie **tematu Service Bus** . W tym przypadku uruchomiono aplikację **MessageSender** dwa razy, więc widzę 10 komunikatów (5 wiadomości dla każdego przebiegu).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Strona tematu Service Bus — komunikaty przychodzące":::    
1. Upewnij się, że w subskrypcji Service Bus **nie ma aktywnych komunikatów** . 
    Jeśli nie widzisz żadnych zdarzeń na tej stronie, sprawdź, czy na stronie **subskrypcja Service Bus** nie jest wyświetlana żadna **Liczba aktywnych komunikatów**. Jeśli liczba dla tego licznika jest większa od zera, komunikaty w subskrypcji nie są przekazywane do funkcji obsługi (obsługi subskrypcji zdarzeń) z jakiegoś powodu. Sprawdź, czy subskrypcja zdarzeń została prawidłowo skonfigurowana. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Liczba aktywnych komunikatów w subskrypcji Service Bus":::    
1. Na stronie **zdarzenia** w przestrzeni nazw Service Bus są widoczne także **zdarzenia dostarczone** . 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Zdarzenia dostarczone przez stronę zdarzeń" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Możesz również sprawdzić, czy zdarzenia są dostarczane na stronie **subskrypcji zdarzeń** . Możesz uzyskać dostęp do tej strony, wybierając subskrypcję zdarzeń na stronie **zdarzenia** . 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Strona subskrypcji zdarzeń — dostarczone zdarzenia":::
## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat usługi [Azure Event Grid](../event-grid/index.yml).
* Dowiedz się więcej na temat usługi [Azure Functions](../azure-functions/index.yml).
* Dowiedz się więcej o [funkcji Logic Apps usługi Azure App Service](../logic-apps/index.yml).
* Dowiedz się więcej na temat usługi [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png