---
title: 'Szybki start: przekieruj zdarzenia Azure Cache for Redis do internetowego punktu końcowego za pomocą Azure Portal'
description: Używanie Azure Event Grid do subskrybowania Azure Cache for Redis zdarzeń, wysyłania zdarzeń dohook i obsługi zdarzeń w aplikacji internetowej
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529309"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Szybki start: przekieruj zdarzenia Azure Cache for Redis do internetowego punktu końcowego za pomocą Azure Portal

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym przewodniku Szybki start użyjesz usługi Azure Portal, aby utworzyć wystąpienie usługi Azure Cache for Redis, zasubskrybować zdarzenia dla tego wystąpienia, wyzwolić zdarzenie i wyświetlić wyniki. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Jednak aby uprościć ten przewodnik Szybki start, wyślesz zdarzenia do aplikacji internetowej, która będzie zbierać i wyświetlać komunikaty. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Po zakończeniu zobaczysz, że dane zdarzenia zostały wysłane do aplikacji internetowej.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Podgląd w formacie JSON.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Tworzenie wystąpienia Azure Cache for Redis pamięci podręcznej 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed subskrybowanie zdarzeń dla wystąpienia pamięci podręcznej utwórzmy punkt końcowy dla komunikatu zdarzenia. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki start, wdrożysz wstępnie sbudowaną aplikację [internetową,](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz **pozycję Deploy to Azure (Wd wdrażaj** na platformie Azure) w witrynie GitHub README, aby wdrożyć rozwiązanie w subskrypcji. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Przycisk Wd wdrażaj na platformie Azure.":::

2. Na stronie **Wdrożenie niestandardowe** wykonaj następujące czynności: 
    1. W **przypadku grupy** zasobów wybierz grupę zasobów utworzoną podczas tworzenia wystąpienia pamięci podręcznej. Po zakończeniu pracy z samouczkiem łatwiej będzie wyczyścić grupę zasobów.  
    2. W **polach** Nazwa witryny wprowadź nazwę aplikacji internetowej.
    3. W **przypadku nazwy planu hostingu** wprowadź nazwę planu App Service do hostowania aplikacji internetowej.
    4. Zaznacz pole wyboru **wyrażam zgodę na powyższe warunki i postanowienia.** 
    5. Wybierz pozycję **Kup**. 
    
    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Subskrypcja** | Rozwijaj i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma być tworzyć ta aplikacja internetowa. | 
    | **Grupa zasobów** | Rozwijaj i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów. | Dzięki umieszczeniu wszystkich zasobów aplikacji w jednej grupie zasobów można łatwo nimi zarządzać lub usuwać je razem. | 
    | **Nazwa witryny** | Wprowadź nazwę aplikacji internetowej. | Ta wartość nie może być pusta. | 
    | **Nazwa planu hostingu** | Wprowadź nazwę planu App Service do hostowania aplikacji internetowej. | Ta wartość nie może być pusta. | 

1. Wybierz pozycję Alerty (ikona dzwonka) w portalu, a następnie wybierz **pozycję Przejdź do grupy zasobów.** 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal powiadomienia o wdrożeniu.":::

4. Na **stronie Grupa** zasobów na liście zasobów wybierz utworzoną aplikację internetową. Na tej liście zobaczysz również App Service pamięci podręcznej. 

5. Na **App Service** aplikacji internetowej wybierz adres URL, aby przejść do witryny internetowej. Adres URL powinien mieć ten format: `https://<your-site-name>.azurewebsites.net` .

6. Upewnij się, że widzisz witrynę, ale żadne zdarzenia nie zostały jeszcze opublikowane w tej witrynie.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Pusta Event Grid podglądu.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Subskrybowanie Azure Cache for Redis subskrypcji

W tym kroku zasubskrybujesz temat, aby poinformować Event Grid, które zdarzenia chcesz śledzić i gdzie mają być wysyłane zdarzenia.

1. W portalu przejdź do utworzonego wcześniej wystąpienia pamięci podręcznej. 
2. Na stronie **Azure Cache for Redis** wybierz pozycję **Zdarzenia w** menu po lewej stronie. 
3. Wybierz pozycję **Web Hook**. Zdarzenia są wysyłane do aplikacji przeglądarki przy użyciu punktu końcowego web hook. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure Portal strony Zdarzenia.":::

4. Na stronie **Tworzenie subskrypcji zdarzeń** wprowadź następujące informacje: 

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Wprowadź nazwę subskrypcji zdarzeń. | Wartość musi mieć od 3 do 64 znaków. Może zawierać tylko litery, cyfry i łączniki. | 
    | **Typy zdarzeń** | Rozwijaj i wybierz typy zdarzeń, które chcesz wypchnąć do miejsca docelowego. W tym przewodniku Szybki start będziemy skalować wystąpienie pamięci podręcznej. | Dostępne opcje to stosowanie poprawek, skalowanie, importowanie i eksportowanie. | 
    | **Typ punktu końcowego** | Wybierz **pozycję Web Hook**. | Program obsługi zdarzeń do odbierania zdarzeń. | 
    | **Punkt końcowy** | Kliknij **pozycję Wybierz punkt końcowy**, wprowadź adres URL aplikacji internetowej i dodaj element do adresu URL strony głównej (na przykład : ), a następnie wybierz pozycję `api/updates` `https://cache.azurewebsites.net/api/updates` **Potwierdź wybór.** | Jest to adres URL utworzonej wcześniej aplikacji internetowej. | 

5. Teraz na stronie **Tworzenie subskrypcji zdarzeń** wybierz pozycję **Utwórz,** aby utworzyć subskrypcję zdarzeń. 

6. Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Będziemy skalować Twoje Azure Cache for Redis wystąpienie.

1. W okienku Azure Portal przejdź do swojego Azure Cache for Redis i wybierz pozycję **Skaluj** w menu po lewej stronie.

1. Wybierz odpowiednią warstwę cenową na stronie **Skala** i kliknij pozycję **Wybierz**. 

    Można skalować do innej warstwy cenowej z następującymi ograniczeniami:
    
    * Nie można skalować z wyższej warstwy cenowej do niższej warstwy cenowej.
      * Nie można skalować z pamięci podręcznej **Premium** w dół **do** standardowej lub podstawowej pamięci **podręcznej.**
      * Nie można skalować z **standardowej** pamięci podręcznej w dół do podstawowej **pamięci podręcznej.**
    * Można skalować z podstawowej  **pamięci** podręcznej do standardowej pamięci podręcznej, ale nie można zmienić rozmiaru w tym samym czasie. Jeśli potrzebujesz innego rozmiaru, możesz wykonać kolejną operację skalowania do żądanego rozmiaru.
    * Nie można skalować bezpośrednio z **podstawowej** pamięci podręcznej do pamięci **podręcznej Premium.** Najpierw przeskaluj **z podstawowej** do standardowej w jednej operacji skalowania, a następnie ze **Standardowa** do **Premium** w kolejnej operacji skalowania. 
    * Nie można skalować od większego rozmiaru do **rozmiaru C0 (250 MB).**
 
    Gdy pamięć podręczna jest skalowana do  nowej warstwy cenowej, w bloku Azure Cache for Redis jest **wyświetlany stan skalowania.** Po zakończeniu skalowania stan zmieni się z **Skalowanie** na **Uruchomione.**

1. Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Komunikat jest w formacie JSON i zawiera tablicę z co najmniej jednym zdarzeniami. W poniższym przykładzie komunikat JSON zawiera tablicę z jednym zdarzeniem. Wyświetl aplikację internetową i zwróć uwagę, że odebrano zdarzenie **ScalingCompleted.** 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer w formacie JSON.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z tym zdarzeniem, nie czyść zasobów utworzonych w tym przewodniku Szybki start. W przeciwnym razie usuń zasoby utworzone w tym przewodniku Szybki start.

Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Wiesz już, jak tworzyć tematy niestandardowe i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co może Ci ułatwić usługa Event Grid:

- [Reagowanie na zdarzenia Azure Cache for Redis zdarzeń](cache-event-grid.md)
- [Event Grid — informacje](../event-grid/overview.md)
