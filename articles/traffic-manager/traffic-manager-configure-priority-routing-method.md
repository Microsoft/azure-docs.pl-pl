---
title: 'Samouczek: Konfigurowanie routingu ruchu priorytetowego za pomocą usługi Azure Traffic Manager'
description: W tym samouczku wyjaśniono, jak skonfigurować metodę routingu ruchu priorytetowego w Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1be507f3676a5531855e3a8deb6801b1a5cb8e74
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166719"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu priorytetowego w Traffic Manager

W tym samouczku opisano, jak za pomocą usługi Azure Traffic Manager kierować ruchem użytkowników do określonych punktów końcowych przy użyciu metody routingu priorytet. W tej metodzie routingu zdefiniujesz kolejność każdego punktu końcowego, który przechodzi do konfiguracji profilu Traffic Manager. Ruch od użytkowników zostanie skierowany do punktu końcowego w kolejności, w jakiej są wyświetlane. Ta metoda routingu jest przydatna, gdy chcesz skonfigurować usługę do przełączania do trybu failover. Podstawowy punkt końcowy otrzymuje numer priorytetu "1" i będzie obsługiwać wszystkie żądania przychodzące. Punkty końcowe o niższym priorytecie będą pełnić rolę kopii zapasowych.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> - Utwórz profil Traffic Manager z użyciem priorytetowego routingu.
> - Dodaj punkty końcowe.
> - Skonfiguruj priorytet punktów końcowych.
> - Użyj profilu Traffic Manager.
> - Usuń profil Traffic Manager.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="to-configure-the-priority-traffic-routing-method"></a>Aby skonfigurować metodę routingu ruchu priorytetowego
1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **+ Utwórz zasób** po lewej stronie. Wyszukaj **profil Traffic Manager** i wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1. Na stronie *Tworzenie profilu Traffic Manager* Zdefiniuj następujące ustawienia:

    | Ustawienie         | Wartość                                              |
    | ---             | ---                                                |
    | Nazwa            | Podaj nazwę profilu. Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Aby uzyskać dostęp do profilu Traffic Manager, należy użyć nazwy DNS `<profilename>.trafficmanager.net` . |    
    | Metoda routingu  | Wybierz pozycję **Priorytet**. |
    | Subskrypcja    | Wybierz subskrypcję. |
    | Grupa zasobów   | Użyj istniejącej grupy zasobów lub Utwórz nową grupę zasobów, w której ma zostać umieszczony ten profil. Jeśli zdecydujesz się utworzyć nową grupę zasobów, Użyj listy rozwijanej *Lokalizacja grupy zasobów* , aby określić lokalizację grupy zasobów. To ustawienie odnosi się do lokalizacji grupy zasobów i nie ma wpływu na profil Traffic Manager, który został wdrożony globalnie. |

1. Wybierz pozycję **Utwórz** , aby wdrożyć profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

## <a name="add-endpoints"></a>Dodaj punkty końcowe

1. Wybierz z listy profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1. Wybierz **punkty końcowe** w obszarze *Ustawienia* , a następnie wybierz pozycję **+ Dodaj** , aby dodać nowy punkt końcowy.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1. Wybierz lub wprowadź następujące ustawienia: 

    | Ustawienie                | Wartość                                              |
    | ---                    | ---                                                |
    | Typ                   | Wybierz typ punktu końcowego. |    
    | Nazwa                   | Nadaj nazwę identyfikującą ten punkt końcowy. |
    | Typ zasobu docelowego   | Wybierz typ zasobu dla elementu docelowego. |
    | Zasób docelowy        | Wybierz zasób z listy. |
    | Priorytet               | Nadaj numer priorytetu dla tego punktu końcowego. 1 to najwyższy priorytet. |


1. Wybierz pozycję **Dodaj** , aby dodać punkt końcowy. Powtórz kroki 2 i 3, aby dodać dodatkowe punkty końcowe. Pamiętaj, aby ustawić odpowiedni numer priorytetu.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1. Na stronie **punkty końcowe** Sprawdź kolejność dla punktów końcowych. W przypadku wybrania metody routingu ruchu **priorytetowego** kolejność wybranych punktów końcowych jest ważna. Sprawdź kolejność priorytetów punktów końcowych.  Podstawowy punkt końcowy znajduje się na górze. Podwójne sprawdzenie kolejności wyświetlania. Wszystkie żądania będą kierowane do pierwszego punktu końcowego, a jeśli Traffic Manager wykryje, że jest w złej kondycji, ruch zostanie automatycznie przekierowany do następnego punktu końcowego. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1. Aby zmienić kolejność priorytetów punktu końcowego, wybierz punkt końcowy, Zmień wartość priorytetu i wybierz pozycję **Zapisz** , aby zapisać ustawienia punktu końcowego.

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu Traffic Manager

1.  Na pasku wyszukiwania portalu Wyszukaj nazwę **profilu Traffic Manager** utworzonego w poprzedniej sekcji i wybierz profil usługi Traffic Manager w wyświetlonych wynikach.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1.  Na stronie Przegląd **profilu Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu Traffic Manager. Może to być używane przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu. W takim przypadku wszystkie żądania są kierowane do pierwszego punktu końcowego, a jeśli Traffic Manager wykryje, że jest w złej kondycji, ruch automatycznie przejdzie w tryb failover do następnego punktu końcowego.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

1. Po zakończeniu działania profilu Traffic Manager Edytuj rekord DNS na autorytatywnym serwerze DNS, aby wskazywał nazwę domeny firmowej na nazwę domeny Traffic Manager.

## <a name="clean-up-resource"></a>Wyczyść zasób

Jeśli profil Traffic Manager nie jest już potrzebny, zlokalizuj profil i wybierz pozycję **Usuń profil**.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Tworzenie profilu Traffic Manager priorytetu":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodzie routingu priorytetów, zobacz:

> [!div class="nextstepaction"]
> [Priorytetowa Metoda routingu](traffic-manager-routing-methods.md#priority-traffic-routing-method)
