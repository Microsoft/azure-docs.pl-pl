---
title: 'Szybki start: tworzenie profilu wysokiej jakości aplikacji — Azure Portal — Azure Traffic Manager'
description: W tym artykule Szybki start opisano sposób tworzenia profilu Traffic Manager w celu utworzenia aplikacji internetowej o wysokiej Azure Portal.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 13b5925310c615461424f78d90ba9849c9bf58c5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727982"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Szybki start: tworzenie profilu Traffic Manager przy użyciu Azure Portal

W tym przewodniku Szybki start opisano tworzenie profilu usługi Traffic Manager, który zapewni wysoką dostępność aplikacji internetowej.

W tym przewodniku Szybki start przeczytasz o dwóch wystąpieniach aplikacji internetowej. Każde z nich jest uruchamiane w innym regionie świadczenia usługi Azure. Utworzysz profil usługi Traffic Manager na podstawie [priorytetu punktu końcowego](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profil kieruje ruch użytkowników do lokacji głównej, w której działa aplikacja internetowa. Usługa Traffic Manager stale monitoruje aplikację internetową. Jeśli lokacja główna jest niedostępna, usługa zapewnia automatyczne przejście w tryb failover w lokacji zapasowej.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagram przedstawiający Traffic Manager wdrożenia." border="false":::

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start będą potrzebne dwa wystąpienia aplikacji internetowej wdrożone w dwóch różnych regionach świadczenia usługi Azure (*Wschodnie stany USA* i *Europa Zachodnia*). Każda będzie służyć jako podstawowy punkt końcowy i punkt końcowy trybu failover dla usługi Traffic Manager.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób.** Wyszukaj pozycję **Aplikacja internetowa i** wybierz pozycję **Utwórz.**

1. W **obszarze Tworzenie aplikacji internetowej** wpisz lub wybierz następujące wartości na **karcie** Podstawy:

    | Ustawienie                 | Wartość |
    | ---                     | --- |
    | Subskrypcja            | Wybierz subskrypcję. |    
    | Grupa zasobów          | Wybierz **pozycję Utwórz nową** i *wprowadź myResourceGroupTM1* w polu tekstowym.|
    | Nazwa                    | Wprowadź **unikatową nazwę** aplikacji internetowej. W tym przykładzie *użyto myWebAppEastUS*. |
    | Publikowanie                 | Wybierz pozycję **Kod**. |
    | Stos środowiska uruchomieniowego           | Wybierz **ASP.NET wersji 4.7.** |
    | System operacyjny        | Wybierz **pozycję Windows**. |
    | Region (Region)                  | Wybierz pozycję **Wschodnie stany USA**. |
    | Plan systemu Windows            | Wybierz **pozycję Utwórz nową** i *wprowadź myAppServicePlanEastUS* w polu tekstowym. |
    | Jednostka SKU i rozmiar            | Wybierz **pozycję Standard S1 100 total ACU, 1,75 GB pamięci.** |
   
1. Wybierz **kartę Monitorowanie** lub wybierz pozycję **Dalej: Monitorowanie.**  W **obszarze Monitorowanie** ustaw **Application Insights** włącz  >  **Application Insights** na wartość **Nie.**

1. Wybierz **pozycję Przejrzyj i utwórz .**

1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.  Po pomyślnym wdrożeniu aplikacji internetowej zostanie utworzona domyślna witryna internetowa.

1. Wykonaj kroki 1–6, aby utworzyć drugą aplikację internetową o *nazwie myWebAppWestEurope.* Nazwa **grupy zasobów** to *myResourceGroupTM2* z regionem Europa Zachodnia *i* App Service **nazwa** planu **myAppServicePlanWestEurope.**  Wszystkie inne ustawienia są takie same jak *myWebAppEastUS.*

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager kierujący ruch użytkowników na podstawie priorytetu punktu końcowego.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób.** Następnie wyszukaj pozycję **Traffic Manager i** wybierz pozycję **Utwórz.**
1. W sekcji **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz następujące ustawienia:

    | Ustawienie | Wartość |
    | --------| ----- |
    | Nazwa | Wprowadź unikatową nazwę profilu usługi Traffic Manager.|
    | Metoda routingu | Wybierz pozycję **Priorytet**.|
    | Subskrypcja | Wybierz subskrypcję, do której ma należeć profil usługi Traffic Manager. |
    | Grupa zasobów | Wybierz pozycję *myResourceGroupTM1*.|
    | Lokalizacja |To ustawienie dotyczy lokalizacji grupy zasobów. Nie ma ono wpływu na profil usługi Traffic Manager, który zostanie wdrożony globalnie.|

1. Wybierz przycisk **Utwórz**.

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj witrynę internetową w regionie *Wschodnie stany USA* jako podstawowy punkt końcowy w celu kierowania całego ruchu użytkowników. Dodaj witrynę internetową w regionie *Europa Zachodnia* jako punkt końcowy trybu failover. Gdy podstawowy punkt końcowy będzie niedostępny, ruch będzie automatycznie kierowany do punktu końcowego trybu failover.

1. Na pasku wyszukiwania portalu wprowadź nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji.
1. Wybierz profil w wynikach wyszukiwania.
1. W bloku **Profil usługi Traffic Manager** w sekcji **Ustawienia** wybierz pozycję **Punkty końcowe**, a następnie wybierz pozycję **Dodaj**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Zrzut ekranu przedstawiający ustawienia punktu końcowego Traffic Manager profilu.":::

1. Wprowadź lub wybierz następujące ustawienia:

    | Ustawienie | Wartość |
    | ------- | ------|
    | Typ | Wybierz pozycję **Punkt końcowy platformy Azure**. |
    | Nazwa | Wprowadź nazwę *myPrimaryEndpoint*. |
    | Typ zasobu docelowego | Wybierz **App Service**. |
    | Zasób docelowy | Wybierz **pozycję Wybierz usługę App Service** Wschodnie  >  **usa.** |
    | Priorytet | Wybierz pozycję **1**. Cały ruch jest kierowany do tego punktu końcowego, gdy jest on w dobrej kondycji. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Zrzut ekranu pokazujący, gdzie należy dodać punkt końcowy do profilu usługi Traffic Manager.":::
    
1. Wybierz przycisk **OK**.
1. Aby utworzyć punkt końcowy trybu failover dla drugiego regionu świadczenia usługi Azure, powtórz kroki 3 i 4 przy użyciu tych ustawień:

    | Ustawienie | Wartość |
    | ------- | ------|
    | Typ | Wybierz pozycję **Punkt końcowy platformy Azure**. |
    | Nazwa | Wprowadź nazwę *myFailoverEndpoint*. |
    | Typ zasobu docelowego | Wybierz **App Service**. |
    | Zasób docelowy | Wybierz **pozycję Wybierz usługę aplikacji Europa**  >  **Zachodnia.** |
    | Priorytet | Wybierz wartość **2**. Cały ruch jest kierowany do tego punktu końcowego trybu failover, jeśli podstawowy punkt końcowy jest w złej kondycji. |

1. Wybierz przycisk **OK**.

Po zakończeniu dodawania tych dwóch punktów końcowych zostaną one wyświetlone w obszarze **Profil usługi Traffic Manager**. Zauważ, że ich stan monitorowania ma teraz wartość **Online**.

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji sprawdzisz nazwę domeny profilu usługi Traffic Manager. Skonfigurujesz również podstawowy punkt końcowy tak, aby był niedostępny. Na końcu zobaczysz, że aplikacja internetowa jest nadal dostępna. Dzieje się tak, ponieważ usługa Traffic Manager wysyła ruch do punktu końcowego trybu failover.

### <a name="check-the-dns-name"></a>Sprawdzanie nazwy DNS

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji.
1. Wybierz profil usługi Traffic Manager. Zostanie wyświetlona strona **Przegląd**.
1. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Zrzut ekranu przedstawiający lokalizację Traffic Manager DNS.":::

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager

1. W przeglądarce internetowej wprowadź nazwę DNS profilu usługi Traffic Manager, aby wyświetlić domyślną witrynę internetową aplikacji internetowej.

    > [!NOTE]
    > W tym scenariuszu Szybki start wszystkie żądania są kierowane do podstawowego punktu końcowego. Jest on ustawiony na wartość **Priorytet 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Zrzut ekranu przedstawiający stronę internetową w celu potwierdzenia Traffic Manager profilu.":::

1. Aby zobaczyć, jak działa przełączanie do trybu failover w usłudze Traffic Manager, wyłącz lokację główną:
    1. Na stronie profilu usługi Traffic Manager w sekcji **Przegląd** wybierz pozycję **myPrimaryEndpoint**.
    1. W obszarze *myPrimaryEndpoint* wybierz pozycję **Wyłączone** > **Zapisz**.
    1. Zamknij okno **myPrimaryEndpoint**. Zwróć uwagę, że teraz stan ma wartość **Wyłączone**.
1. Skopiuj nazwę DNS profilu usługi Traffic Manager z poprzedniego kroku, aby wyświetlić witrynę internetową w nowej sesji przeglądarki internetowej.
1. Sprawdź, czy aplikacja internetowa jest nadal dostępna.

Podstawowy punkt końcowy nie jest dostępny, dlatego nastąpiło przekierowanie do punktu końcowego trybu failover.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz pracę, usuń grupy zasobów, aplikacje internetowe i wszystkie powiązane zasoby. Aby to zrobić, wybierz poszczególne elementy na pulpicie nawigacyjnym i wybierz pozycję **Usuń** u góry każdej strony.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono profil usługi Traffic Manager. Pozwala on na kierowanie ruchu użytkowników dla aplikacji internetowej o wysokiej dostępności. Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
