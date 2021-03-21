---
title: 'Szybki Start: kierowanie pamięci podręcznej platformy Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu Azure Portal'
description: Użyj Azure Event Grid, aby subskrybować usługę Azure cache dla zdarzeń Redis, wysyłać zdarzenia do elementu webhook i obsługiwać zdarzenia w aplikacji sieci Web
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055592"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Szybki Start: kierowanie pamięci podręcznej platformy Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu Azure Portal

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym przewodniku szybki start użyjesz Azure Portal do tworzenia wystąpienia usługi Azure cache for Redis, subskrybowania zdarzeń dla tego wystąpienia, wyzwalania zdarzenia i wyświetlania wyników. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Aby uprościć ten przewodnik Szybki Start, będziesz wysyłać zdarzenia do aplikacji sieci Web, która będzie zbierać i wyświetlać komunikaty. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Po zakończeniu zobaczysz, że dane zdarzenia zostały wysłane do aplikacji sieci Web.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Skalowanie Azure Event Grid przeglądarki w formacie JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Tworzenie pamięci podręcznej platformy Azure dla wystąpienia pamięci podręcznej Redis 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń dla wystąpienia pamięci podręcznej Utwórzmy punkt końcowy dla komunikatu o zdarzeniu. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdróż [wstępnie utworzoną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer) , która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w pliku Readme usługi GitHub, aby wdrożyć rozwiązanie w ramach subskrypcji. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Przycisk Wdróż na platformie Azure.":::

2. Na stronie **wdrożenie niestandardowe** wykonaj następujące czynności: 
    1. W obszarze **Grupa zasobów** wybierz grupę zasobów, która została utworzona podczas tworzenia wystąpienia pamięci podręcznej. Ułatwi to oczyszczenie po zakończeniu pracy z samouczkiem przez usunięcie grupy zasobów.  
    2. W polu **Nazwa lokacji** wprowadź nazwę aplikacji sieci Web.
    3. W polu **Nazwa planu hostingu** wpisz nazwę planu App Service, który będzie używany do hostowania aplikacji sieci Web.
    4. Zaznacz pole wyboru **Zgadzam się na powyższe warunki i postanowienia**. 
    5. Wybierz pozycję **Kup**. 
    
    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzona aplikacja internetowa. | 
    | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
    | **Nazwa witryny** | Wprowadź nazwę aplikacji sieci Web. | Ta wartość nie może być pusta. | 
    | **Nazwa planu hostingu** | Wprowadź nazwę planu App Service, który będzie używany do hostowania aplikacji sieci Web. | Ta wartość nie może być pusta. | 

1. Wybierz pozycję alerty (ikona dzwonka) w portalu, a następnie wybierz pozycję **Przejdź do grupy zasobów**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal powiadomienia o wdrożeniu.":::

4. Na stronie **Grupa zasobów** na liście zasobów wybierz utworzoną aplikację sieci Web. Zobaczysz również plan App Service i wystąpienie pamięci podręcznej na tej liście. 

5. Na stronie **App Service** aplikacji sieci Web wybierz adres URL, aby przejść do witryny sieci Web. Adres URL powinien mieć następujący format: `https://<your-site-name>.azurewebsites.net` .

6. Upewnij się, że zobaczysz witrynę, ale żadne zdarzenia nie zostały jeszcze ogłoszone.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Pusta witryna Event Grid Viewer.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Subskrybowanie wystąpienia usługi Azure cache for Redis

W tym kroku zasubskrybujesz temat, aby poinformować Event Grid, które zdarzenia mają być śledzone, i miejsce, w którym mają być wysyłane zdarzenia.

1. W portalu przejdź do utworzonego wcześniej wystąpienia pamięci podręcznej. 
2. Na stronie **pamięć podręczna Azure dla Redis** wybierz pozycję **zdarzenia** w menu po lewej stronie. 
3. Wybierz **element webhook**. Wysyłasz zdarzenia do aplikacji przeglądarki przy użyciu elementu webhook dla punktu końcowego. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Strona zdarzeń Azure Portal.":::

4. Na stronie **Tworzenie subskrypcji zdarzeń** wpisz następujące polecenie: 

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Wprowadź nazwę subskrypcji zdarzeń. | Wartość musi mieć długość od 3 do 64 znaków. Może zawierać tylko litery, cyfry i łączniki. | 
    | **Typy zdarzeń** | Lista rozwijana i wybierz typy zdarzeń, które mają zostać wypchnięte do miejsca docelowego. W tym przewodniku szybki start będziemy skalować nasze wystąpienie pamięci podręcznej. | Dostępne opcje to stosowanie poprawek, skalowanie, importowanie i eksportowanie. | 
    | **Typ punktu końcowego** | Wybierz **element webhook**. | Obsługa zdarzeń do odbierania zdarzeń. | 
    | **Punkt końcowy** | Kliknij pozycję **Wybierz punkt końcowy** i wprowadź adres URL aplikacji sieci Web, a następnie Dodaj adres `api/updates` URL strony głównej (na przykład: `https://cache.azurewebsites.net/api/updates` ), a następnie wybierz pozycję **Potwierdź wybór**. | Jest to adres URL utworzonej wcześniej aplikacji sieci Web. | 

5. Teraz na stronie **Tworzenie subskrypcji zdarzeń** wybierz pozycję **Utwórz** , aby utworzyć subskrypcję zdarzeń. 

6. Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Przeglądarka Azure Event Grid.":::

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Będziemy skalować pamięć podręczną platformy Azure dla wystąpienia Redis.

1. W Azure Portal przejdź do wystąpienia usługi Azure cache for Redis i wybierz pozycję **Skaluj** w menu po lewej stronie.

1. Na stronie **skalowanie** wybierz żądaną warstwę cenową, a następnie kliknij pozycję **Wybierz**. 

    Możesz skalować do innej warstwy cenowej z następującymi ograniczeniami:
    
    * Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
      * Nie można skalować z pamięci podręcznej **Premium** do warstwy **standardowa** lub **podstawowa** .
      * Nie można skalować z poziomu **standardowej** pamięci podręcznej do **podstawowej** pamięci podręcznej.
    * Możesz skalować od **podstawowej** pamięci podręcznej do **standardowej** pamięci podręcznej, ale nie możesz zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
    * Nie można skalować z **podstawowej** pamięci podręcznej bezpośrednio do pamięci podręcznej **Premium** . Najpierw Skaluj od warstwy **podstawowa** do **standardowa** w ramach jednej operacji skalowania, a następnie z warstwy **standardowa** do **Premium** w kolejnej operacji skalowania.
    * Nie można skalować od większego rozmiaru w dół do rozmiaru **C0 (250 MB)** .
 
    Podczas skalowania pamięci podręcznej do nowej warstwy cenowej w bloku **usługi Azure cache for Redis** jest wyświetlany stan **skalowania** . Po zakończeniu skalowania stan zmieni się ze **skalowanie** na **uruchomione**.

1. Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Komunikat jest w formacie JSON i zawiera tablicę z co najmniej jednym zdarzeniem. W poniższym przykładzie komunikat JSON zawiera tablicę z jednym zdarzeniem. Wyświetl aplikację sieci Web i zwróć uwagę na to, że odebrano zdarzenie **ScalingCompleted** . 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Skalowanie Azure Event Grid przeglądarki w formacie JSON.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z tym zdarzeniem, nie czyść zasobów utworzonych w tym przewodniku Szybki Start. W przeciwnym razie Usuń zasoby utworzone w tym przewodniku Szybki Start.

Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy niestandardowe i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Oddziałanie do usługi Azure cache dla zdarzeń Redis](cache-event-grid.md)
- [Event Grid — informacje](../event-grid/overview.md)

