---
title: 'Samouczek: Konfigurowanie routingu ruchu geograficznego za pomocą usługi Azure Traffic Manager'
description: W tym samouczku wyjaśniono, jak skonfigurować metodę routingu ruchu geograficznego za pomocą usługi Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96188670"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu geograficznego za pomocą Traffic Manager

Metoda routingu ruchu geograficznego umożliwia kierowanie ruchu do określonych punktów końcowych na podstawie lokalizacji geograficznej, w której pochodzą żądania. W tym samouczku pokazano, jak utworzyć profil Traffic Manager przy użyciu tej metody routingu i skonfigurować punkty końcowe do odbierania ruchu z określonych lokalizacje geograficzne.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Utwórz profil Traffic Manager przy użyciu routingu geograficznego.
> - Skonfiguruj zagnieżdżony punkt końcowy.
> - Użyj profilu Traffic Manager.
> - Usuń profil Traffic Manager.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **+ Utwórz zasób** po lewej stronie. Wyszukaj **profil Traffic Manager** i wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Tworzenie profilu usługi Traffic Manager":::

1. Na stronie *Tworzenie profilu Traffic Manager* Zdefiniuj następujące ustawienia:

    | Ustawienie         | Wartość                                              |
    | ---             | ---                                                |
    | Nazwa            | Podaj nazwę profilu. Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Aby uzyskać dostęp do profilu Traffic Manager, należy użyć nazwy DNS `<profilename>.trafficmanager.net` . |    
    | Metoda routingu  | Wybierz pozycję **geograficzne**. |
    | Subskrypcja    | Wybierz subskrypcję. |
    | Grupa zasobów   | Użyj istniejącej grupy zasobów lub Utwórz nową grupę zasobów, w której ma zostać umieszczony ten profil. Jeśli zdecydujesz się utworzyć nową grupę zasobów, Użyj listy rozwijanej *Lokalizacja grupy zasobów* , aby określić lokalizację grupy zasobów. To ustawienie odnosi się do lokalizacji grupy zasobów i nie ma wpływu na profil Traffic Manager, który został wdrożony globalnie. |

1. Wybierz pozycję **Utwórz** , aby wdrożyć profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Tworzenie strony profilu Traffic Manager":::

## <a name="add-endpoints"></a>Dodaj punkty końcowe

1. Wybierz z listy profil Traffic Manager.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Managerna lista geograficzna":::

1. Wybierz **punkty końcowe** w obszarze *Ustawienia* , a następnie wybierz pozycję **+ Dodaj** , aby dodać nowy punkt końcowy.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Dodaj punkty końcowe":::

1. Wybierz lub wprowadź następujące ustawienia: 

    | Ustawienie                | Wartość                                              |
    | ---                    | ---                                                |
    | Typ                   | Wybierz typ punktu końcowego. W przypadku profilów routingu geograficznego używanych w środowisku produkcyjnym zdecydowanie zalecamy użycie zagnieżdżonych typów punktów końcowych zawierających profil podrzędny z więcej niż jednym punktem końcowym. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące metod routingu ruchu geograficznego](traffic-manager-FAQs.md). |    
    | Nazwa                   | Nadaj nazwę identyfikującą ten punkt końcowy. |
    | Typ zasobu docelowego   | Wybierz typ zasobu dla elementu docelowego. |
    | Zasób docelowy        | Wybierz zasób z listy. |

    > [!Note]
    > Niektóre pola na tej stronie zależą od typu dodawanego punktu końcowego:
    > 1. W przypadku dodawania punktu końcowego platformy Azure wybierz **Typ zasobu docelowego** i **docelowy** na podstawie zasobu, do którego chcesz skierować ruch
    > 1. Jeśli dodajesz **zewnętrzny** punkt końcowy, podaj w **pełni kwalifikowaną nazwę domeny (FQDN)** dla punktu końcowego.
    > 1. Jeśli dodajesz **zagnieżdżony punkt końcowy**, wybierz **zasób docelowy** odpowiadający profilowi podrzędnemu, którego chcesz użyć, i określ **minimalną liczbę punktów końcowych elementów podrzędnych**.

1. W sekcji *Mapowanie geograficzne* Użyj listy rozwijanej, aby dodać regiony, w których ma być wysyłany ruch do tego punktu końcowego. Należy dodać co najmniej jeden region. Można zamapować wiele regionów.

1. Powtórz ostatni krok dla wszystkich punktów końcowych, które chcesz dodać w ramach tego profilu, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Dodawanie punktu końcowego usługi Traffic Manager":::

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu Traffic Manager

1.  Na pasku wyszukiwania portalu Wyszukaj nazwę **profilu Traffic Manager** utworzonego w poprzedniej sekcji i wybierz profil usługi Traffic Manager w wyświetlonych wynikach.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Profil Menedżera ruchu wyszukiwania":::

1. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Nazwa może być używana przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu. Przy użyciu routingu geograficznego Traffic Manager sprawdza źródłowy adres IP żądania przychodzącego i określa region, z którego pochodzi. Jeśli ten region jest mapowany na punkt końcowy, ruch jest kierowany do tego miejsca. Jeśli ten region nie jest zamapowany na punkt końcowy, Traffic Manager zwraca odpowiedź na zapytanie NoData.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Przegląd Traffic Manager geograficznych":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli profil Traffic Manager nie jest już potrzebny, zlokalizuj profil i wybierz pozycję **Usuń profil**.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Usuwanie profilu Traffic Manager":::

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodzie routingu geograficznego, zobacz:

> [!div class="nextstepaction"]
> [Metoda routingu ruchu geograficznego](traffic-manager-routing-methods.md#geographic)
