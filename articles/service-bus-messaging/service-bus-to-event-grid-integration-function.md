---
title: Obsługa zdarzeń Service Bus za pośrednictwem Event Grid przy użyciu Azure Functions
description: W tym artykule przedstawiono procedurę obsługi zdarzeń Service Bus za pośrednictwem Event Grid przy użyciu Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95819412"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Samouczek: reagowanie na zdarzenia Azure Service Bus odbierane za pośrednictwem Azure Event Grid przy użyciu Azure Functions
W tym samouczku dowiesz się, jak odpowiedzieć na zdarzenia Azure Service Bus, które są odbierane za pośrednictwem Azure Event Grid przy użyciu Azure Functions i Azure Logic Apps. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Service Bus
> * Przygotowanie przykładowej aplikacji do wysyłania wiadomości
> * Wysyłanie komunikatów do tematu Service Bus
> * Odbieranie komunikatów za pomocą usługi Logic Apps
> * Skonfiguruj funkcję testową na platformie Azure
> * Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
> * Odbieranie komunikatów przy użyciu usługi Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Dodatkowe wymagania wstępne
Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs) i Uwzględnij obciążenie **Programowanie na platformie Azure** . To obciążenie obejmuje **Narzędzia usługi Azure Functions** , które są potrzebne do tworzenia, kompilowania i wdrażania projektów Azure Functions w programie Visual Studio. 

## <a name="deploy-the-function-app"></a>Wdrażanie aplikacji funkcji 

>[!NOTE]
> Aby dowiedzieć się więcej o tworzeniu i wdrażaniu aplikacji Azure Functions, zobacz [tworzenie Azure Functions przy użyciu programu Visual Studio](../azure-functions/functions-develop-vs.md)

1. Otwórz plik **ReceiveMessagesOnEvent. cs** z projektu **FunctionApp1** rozwiązania **SBEventGridIntegration. sln** . 
1. Zamień na `<SERCICE BUS NAMESPACE - CONNECTION STRING>` Parametry połączenia z przestrzenią nazw Service Bus. Powinna być taka sama jak ta, która była używana w pliku **program. cs** projektu **MessageSender** w tym samym rozwiązaniu. 
1. Kliknij prawym przyciskiem myszy pozycję **FunctionApp1**, a następnie wybierz pozycję **Publikuj**. 
1. Na stronie **Publikowanie** wybierz pozycję **Rozpocznij**. Te kroki mogą się różnić od tego, co widzisz, ale proces publikowania powinien wyglądać podobnie. 
1. W kreatorze **publikowania** na stronie **Target** wybierz pozycję **Azure** for **Target**. 
1. Na stronie **określony cel** wybierz pozycję **Azure aplikacja funkcji (Windows)**. 
1. Na stronie **wystąpienie funkcji** wybierz pozycję **Utwórz nową funkcję platformy Azure**. 
1. Na stronie **aplikacja funkcji (Windows)** wykonaj następujące kroki:
    1. Wprowadź **nazwę** aplikacji funkcji.
    1. Wybierz **subskrypcję** platformy Azure.
    1. Wybierz istniejącą **grupę zasobów** lub Utwórz nową grupę zasobów. Na potrzeby tego samouczka wybierz grupę zasobów, która ma Service Bus przestrzeni nazw. 
    1. Wybierz **Typ planu** dla App Service.
    1. Wybierz **lokalizację**. Wybierz tę samą lokalizację co Service Bus przestrzeni nazw. 
    1. Wybierz istniejącą **usługę Azure Storage** lub wybierz pozycję **Nowy** , aby utworzyć nowe konto magazynu, które będzie używane przez aplikację funkcji. 
    1. Wybierz pozycję **Utwórz** , aby utworzyć aplikację funkcji. 
1. Na stronie **wystąpienie funkcji** kreatora **publikacji** wybierz pozycję **Zakończ**. 
1. Na stronie **Publikowanie** w programie Visual Studio wybierz pozycję **Publikuj** , aby opublikować aplikację Functions na platformie Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Publikowanie aplikacji funkcji":::    
1. W oknie **dane wyjściowe** zapoznaj się z komunikatami z sekcji Kompilacja i publikowanie, a następnie upewnij się, że oba obiekty zostały wykonane pomyślnie. 
1. Teraz na stronie **Publikowanie** wybierz pozycję **Zarządzaj w Azure Portal**. 
1. Na Azure Portal na stronie **aplikacja funkcji** wybierz opcję **funkcje** w menu po lewej stronie, a następnie upewnij się, że widzisz dwie funkcje: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Dwie funkcje do obsługi wyzwalacza Event Grid i wyzwalacza HTTP":::

    > [!NOTE]
    > `EventGridTriggerFunction`Używa [wyzwalacza Event Grid](../azure-functions/functions-bindings-event-grid-trigger.md) i `HTTPTriggerFunction` używa [wyzwalacza http](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Z listy wybierz pozycję **EventGridTriggerFunction** . Zalecamy używanie wyzwalacza Event Grid z Azure Functions, ponieważ ma on kilka korzyści niż korzystanie z wyzwalacza HTTP. Aby uzyskać szczegółowe informacje, zobacz [funkcję platformy Azure jako procedurę obsługi zdarzeń dla zdarzeń Event Grid](../event-grid/handler-functions.md).
1. Na stronie **funkcji** **EventGridTriggerFunction** wybierz pozycję **Monitoruj** w menu po lewej stronie. 
1. Wybierz pozycję **Konfiguruj** , aby skonfigurować Application Insights do przechwytywania dziennika wywołania. Ta strona służy do monitorowania wykonań funkcji po odebraniu zdarzeń Service Bus z Event Grid. 
1. Na stronie **Application Insights** wprowadź nazwę zasobu, wybierz **lokalizację** dla zasobu, a następnie wybierz przycisk **OK**. 
1. Wybierz funkcję **EventGridTriggerFunction** w górnej części (menu podłączane), aby przejść z powrotem do strony **funkcji** . 
1. Potwierdź, że na stronie **monitorowanie** . 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Monitoruj stronę funkcji przed wywołaniami funkcji":::
    
    Ta strona powinna być otwarta na karcie w przeglądarce sieci Web. Ta strona zostanie odświeżona w celu późniejszego wyświetlenia wywołań tej funkcji.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Łączenie funkcji i przestrzeni nazw za pomocą usługi Event Grid
W tej sekcji należy powiązać funkcję z przestrzenią nazw Service Bus przy użyciu Azure Portal. 

Aby utworzyć subskrypcję Azure Event Grid, wykonaj następujące kroki:

1. W Azure Portal przejdź do przestrzeni nazw, a następnie w okienku po lewej stronie wybierz pozycję **zdarzenia**. Zostanie otwarte okno przestrzeni nazw z dwiema subskrypcjami usługi Event Grid wyświetlonymi w okienku po prawej stronie. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Strona Service Bus — zdarzenia":::
2. Wybierz pozycję **+ subskrypcja zdarzeń** na pasku narzędzi. 
3. Na stronie **Tworzenie subskrypcji zdarzeń** wykonaj następujące czynności:
    1. Wprowadź **nazwę** subskrypcji. 
    2. Wprowadź **nazwę** **tematu systemowego**. Tematy systemowe są tematami utworzonymi dla zasobów platformy Azure, takich jak konto usługi Azure Storage i Azure Service Bus. Aby dowiedzieć się więcej na temat tematów systemowych, zobacz [Tematy systemowe — Omówienie](../event-grid/system-topics.md).
    2. Wybierz pozycję **Funkcja platformy Azure** dla **typu punktu końcowego**, a następnie kliknij pozycję **Wybierz punkt końcowy**. 

        ![Subskrypcja Event Grid Service Bus](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Na stronie **Wybierz funkcję platformy Azure** wybierz subskrypcję, grupę zasobów, aplikację funkcji, gniazdo i funkcję, a następnie wybierz pozycję **Potwierdź wybór**. 

        ![Funkcja — Wybieranie punktu końcowego](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Na stronie **Tworzenie subskrypcji zdarzeń** przejdź do karty **filtry** i wykonaj następujące zadania:
        1. Wybierz pozycję **Włącz filtrowanie podmiotu**
        2. Wprowadź nazwę subskrypcji do tematu Service Bus (**S1**) utworzonego wcześniej.
        3. Wybierz przycisk **Utwórz**. 

            ![Filtr subskrypcji zdarzeń](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Przejdź do karty **subskrypcje zdarzeń** na stronie **zdarzenia** i sprawdź, czy na liście znajduje się subskrypcja zdarzeń.

    ![Subskrypcja zdarzeń na liście](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Monitorowanie aplikacji funkcji
Komunikaty wysłane do tematu Service Bus wcześniej są przekazywane do subskrypcji (S1). Event Grid przekazuje komunikaty z subskrypcji do funkcji platformy Azure. W tym kroku samouczka potwierdzasz, że funkcja została wywołana i zarejestrowano zarejestrowane komunikaty informacyjne. 

1. Na stronie aplikacji funkcji platformy Azure przejdź do karty **monitorowanie** , jeśli nie jest jeszcze aktywna. Powinien zostać wyświetlony wpis dla każdej wiadomości ogłoszonej w temacie Service Bus. Jeśli ich nie widzisz, Odśwież stronę po odczekaniu przez kilka minut. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Strona monitorowania dla funkcji po wywołaniu":::
2. Wybierz wywołanie z listy, aby wyświetlić szczegóły. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Szczegóły wywołania funkcji" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    Możesz również użyć karty **dzienniki** na stronie **Monitoruj** , aby wyświetlić informacje o rejestrowaniu w miarę wysyłania komunikatów. Może to opóźnić kilka minut, aby wyświetlić zarejestrowane komunikaty. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Dzienniki funkcji" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Jeśli nie widzisz żadnych wywołań funkcji po odczekaniu i odświeżeniu w jakiś sposób, wykonaj następujące kroki: 

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