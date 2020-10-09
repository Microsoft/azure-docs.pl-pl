---
title: Samouczek — Konfigurowanie routingu ruchu geograficznego za pomocą usługi Azure Traffic Manager
description: W tym samouczku wyjaśniono, jak skonfigurować metodę routingu ruchu geograficznego za pomocą usługi Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: duau
ms.openlocfilehash: 53773d7c616edec067e1ed1778b7ce6b500ee936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462619"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Samouczek: Konfigurowanie metody routingu ruchu geograficznego za pomocą Traffic Manager

Metoda routingu ruchu geograficznego umożliwia kierowanie ruchu do określonych punktów końcowych na podstawie lokalizacji geograficznej, w której pochodzą żądania. W tym samouczku pokazano, jak utworzyć profil Traffic Manager przy użyciu tej metody routingu i skonfigurować punkty końcowe do odbierania ruchu z określonych lokalizacje geograficzne.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

1. Z poziomu przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com). Jeśli jeszcze nie masz konta, możesz skorzystać z [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free/).
2. Kliknij kolejno pozycje **Utwórz zasób**  >  **Sieć**  >  **Traffic Manager**  >  **Utwórz**profil.
4. W **profilu tworzenia Traffic Manager**:
    1. Podaj nazwę profilu. Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Aby uzyskać dostęp do profilu Traffic Manager, należy użyć nazwy DNS `<profilename>.trafficmanager.net` .
    2. Wybierz metodę routingu **geograficznego** .
    3. Wybierz subskrypcję, w ramach której chcesz utworzyć ten profil.
    4. Użyj istniejącej grupy zasobów lub Utwórz nową grupę zasobów, w której ma zostać umieszczony ten profil. Jeśli zdecydujesz się utworzyć nową grupę zasobów, Użyj listy rozwijanej **Lokalizacja grupy zasobów** , aby określić lokalizację grupy zasobów. To ustawienie odnosi się do lokalizacji grupy zasobów i nie ma wpływu na profil Traffic Manager, który został wdrożony globalnie.
    5. Po kliknięciu przycisku **Utwórz**profil Traffic Manager zostanie utworzony i wdrożony globalnie.

![Tworzenie profilu usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Dodaj punkty końcowe

1. Wyszukaj nazwę profilu Traffic Manager utworzoną na pasku wyszukiwania portalu i kliknij wynik, gdy jest wyświetlany.
2. Przejdź do **ustawień**  ->  **punkty końcowe** w Traffic Manager.
3. Kliknij przycisk **Dodaj** i w wyświetlonym okienku **Dodaj punkt końcowy** wykonaj następujące czynności:
4. Wybierz **Typ** w zależności od typu punktu końcowego, który chcesz dodać. W przypadku profilów routingu geograficznego używanych w środowisku produkcyjnym zdecydowanie zalecamy użycie zagnieżdżonych typów punktów końcowych zawierających profil podrzędny z więcej niż jednym punktem końcowym. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące metod routingu ruchu geograficznego](traffic-manager-FAQs.md).
5. W polu **Nazwa** podaj nazwę dla tego punktu końcowego.
6. Niektóre pola na tej stronie zależą od typu dodawanego punktu końcowego:
    1. W przypadku dodawania punktu końcowego platformy Azure wybierz **Typ zasobu docelowego** i **docelowy** na podstawie zasobu, do którego chcesz skierować ruch
    2. Jeśli dodajesz **zewnętrzny** punkt końcowy, podaj w **pełni kwalifikowaną nazwę domeny (FQDN)** dla punktu końcowego.
    3. Jeśli dodajesz **zagnieżdżony punkt końcowy**, wybierz **zasób docelowy** odpowiadający profilowi podrzędnemu, którego chcesz użyć, i określ **minimalną liczbę punktów końcowych elementów podrzędnych**.
7. W sekcji Mapowanie geograficzne Użyj listy rozwijanej, aby dodać regiony, z których ma być wysyłany ruch do tego punktu końcowego. Należy dodać co najmniej jeden region i można mieć zamapowane wiele regionów.
8. Powtórz tę czynność dla wszystkich punktów końcowych, które chcesz dodać w ramach tego profilu

![Dodawanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Użyj profilu Traffic Manager
1.  Na pasku wyszukiwania portalu Wyszukaj nazwę **profilu Traffic Manager** utworzonego w poprzedniej sekcji, a następnie kliknij profil usługi Traffic Manager w wyświetlonych wynikach.
2. Kliknij pozycję **Przegląd**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. Może to być używane przez dowolnego klienta (na przykład przez przechodzenie do niego przy użyciu przeglądarki sieci Web) do kierowania do prawego punktu końcowego określonego przez typ routingu.  W przypadku routingu geograficznego Traffic Manager sprawdza źródłowy adres IP żądania przychodzącego i określa region, z którego pochodzi. Jeśli ten region jest mapowany na punkt końcowy, ruch jest kierowany do tego miejsca. Jeśli ten region nie jest zamapowany na punkt końcowy, Traffic Manager zwraca odpowiedź na zapytanie NoData.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metodzie routingu ruchu geograficznego](traffic-manager-routing-methods.md#geographic).
- Dowiedz się, jak [testować Traffic Manager ustawienia](traffic-manager-testing-settings.md).
