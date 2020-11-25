---
title: Konfigurowanie wielowartościowego routingu ruchu-Traffic Manager platformy Azure
description: W tym artykule opisano sposób konfigurowania Traffic Manager w celu kierowania ruchu do punktów końcowych/AAAA.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994931"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurowanie metody routingu z wieloma wartościami w Traffic Manager

W tym artykule opisano sposób konfigurowania wielowartościowej metody routingu ruchu. **Wielowartościowa** Metoda routingu ruchu umożliwia zwracanie wielu prawidłowych punktów końcowych i pozwala zwiększyć niezawodność aplikacji, ponieważ klienci mają więcej opcji do ponawiania prób bez konieczności wykonywania kolejnego wyszukiwania DNS. Routing z wieloma wartościami jest włączony tylko dla profilów, dla których wszystkie punkty końcowe są określone przy użyciu adresów IPv4 lub IPv6. Po odebraniu zapytania dla tego profilu wszystkie zdrowe punkty końcowe są zwracane na podstawie podanej maksymalnej liczby zwracanych wartości. 

>[!NOTE]
> W tym momencie Dodawanie punktów końcowych przy użyciu adresów IPv4 lub IPv6 jest obsługiwane tylko dla punktów końcowych typu **zewnętrznego** , dlatego Routing z wieloma wartościami jest również obsługiwany tylko dla tych punktów końcowych.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów dla profilu Traffic Manager.
1. W lewym okienku Azure Portal wybierz pozycję **grupy zasobów**.
2. W obszarze **grupy zasobów** w górnej części strony wybierz pozycję **Dodaj**.
3. W polu **Nazwa grupy zasobów** wpisz nazwę *myResourceGroupTM1*. W obszarze **Lokalizacja grupy zasobów** wybierz pozycję **Wschodnie stany USA**, a następnie wybierz przycisk **OK**.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Utwórz profil Traffic Manager, który kieruje ruchem użytkownika przez wysłanie ich do punktu końcowego z najniższym opóźnieniem.

1. W lewej górnej części ekranu wybierz kolejno pozycje **Utwórz zasób**  >  **Sieć**  >  **Traffic Manager**  >  **Utwórz** profil.
2. W obszarze **Utwórz profil Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:
    
    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę routingu z **wieloma wartościami** .                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję *myResourceGroupTM1*. |
    | Lokalizacja                | To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.                              |
   |        |           | 
  
   ![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwa adresy IP jako zewnętrzne punkty końcowe do wielowartościowego profilu Traffic Manager, który został utworzony w poprzednim kroku.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Zewnętrzny punkt końcowy                                   |
    | Nazwa           | myEndpoint1                                        |
    | W pełni kwalifikowana nazwa domeny (FQDN) lub adres IP           | Wpisz publiczny adres IP punktu końcowego, który chcesz dodać do tego profilu Traffic Manager                         |
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać kolejny punkt końcowy o nazwie *myEndpoint2*, dla w **pełni kwalifikowanej nazwy domeny (FQDN) lub adres IP**, wprowadź publiczny adres IP drugiego punktu końcowego.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

   ![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [metodzie routingu wydajności](traffic-manager-configure-performance-routing-method.md)
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).


