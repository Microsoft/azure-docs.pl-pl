---
title: 'Szybki Start: Konfigurowanie wysokiej dostępności dzięki usłudze Azure front-drzwi'
description: W tym przewodniku szybki start opisano, jak korzystać z usługi Azure predrzwiczkj dla aplikacji sieci Web o wysokiej dostępności i wysokiej wydajności.
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2020
ms.author: duau
ms.openlocfilehash: ab59de49b7cf625220f8a803aab9d2b9a2ae5937
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398771"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Szybki start: tworzenie usługi Front Door na potrzeby globalnej aplikacji internetowej o wysokiej dostępności

Zacznij korzystać z usług Azure front-drzwi przy użyciu Azure Portal, aby skonfigurować wysoką dostępność dla aplikacji sieci Web.

W tym przewodniku szybki start wszystkie wystąpienia aplikacji sieci Web, które działają w różnych regionach świadczenia usługi Azure, są pule drzwi platformy Azure. Konfiguracja drzwi do przodu jest tworzona na podstawie równości wag i tych samych priorytetów. Ta konfiguracja kieruje ruch do najbliższej lokacji, na której działa aplikacja. Moje drzwi platformy Azure stale monitorują aplikację sieci Web. Usługa umożliwia automatyczne przejście w tryb failover do następnej dostępnej lokacji, gdy Najbliższa lokacja jest niedostępna.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Tworzenie dwóch wystąpień aplikacji sieci Web

Ten przewodnik Szybki Start wymaga dwóch wystąpień aplikacji sieci Web, które działają w różnych regionach świadczenia usługi Azure. Oba wystąpienia aplikacji sieci Web działają w trybie *aktywny/aktywny* , więc jeden może przyjmować ruch. Ta konfiguracja różni się od konfiguracji *aktywnej/gotowości* , która działa jako tryb failover.

Jeśli nie masz jeszcze aplikacji sieci Web, wykonaj następujące kroki, aby skonfigurować przykładowe aplikacje sieci Web.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

1. Na stronie głównej lub w menu platformy Azure wybierz pozycję **Utwórz zasób**.

1. Wybierz pozycję **Internet** > **Aplikacja internetowa**.

   ![Tworzenie aplikacji internetowej w witrynie Azure Portal](media/quickstart-create-front-door/create-web-app-azure-front-door.png)

1. W obszarze **aplikacja internetowa**wybierz **subskrypcję** do użycia.

1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**. Wprowadź *FrontDoorQS_rg1* dla **nazwy** i wybierz **przycisk OK**.

1. W obszarze **szczegóły wystąpienia**wprowadź unikatową **nazwę** aplikacji sieci Web. W tym przykładzie zastosowano *WebAppContoso-1*.

1. Wybierz **stos środowiska uruchomieniowego**, w tym przykładzie *.NET Core 2,1 (LTS)*.

1. Wybierz region, taki jak *środkowe stany USA*.

1. W przypadku **planu Windows**wybierz pozycję **Utwórz nowy**. Wprowadź *myAppServicePlanCentralUS* jako **nazwę** i wybierz **przycisk OK**.

1. Upewnij się, że **jednostka SKU i rozmiar** to **standardowa S1 100 ACU, 1,75 GB pamięci**.

1. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj **Podsumowanie**, a następnie wybierz pozycję **Utwórz**. Ukończenie wdrożenia może potrwać kilka minut.

   ![Przejrzyj podsumowanie dla aplikacji sieci Web](media/quickstart-create-front-door/web-app-summary-azure-front-door.png)

Po zakończeniu wdrażania Utwórz drugą aplikację sieci Web. Użyj tej samej procedury z tymi samymi wartościami, z wyjątkiem następujących wartości:

| Ustawienie          | Wartość     |
| ---              | ---  |
| **Grupa zasobów**   | Wybierz pozycję **Nowy** i wprowadź *FrontDoorQS_rg2* |
| **Nazwa**             | Wprowadź unikatową nazwę aplikacji sieci Web, w tym przykładzie *WebAppContoso-2*  |
| **Region**           | Inny region, w tym przykładzie południowo- *środkowe stany USA* |
| **Plan App Service**  >  **Plan systemu Windows**         | Wybierz pozycję **Nowy** i wprowadź *myAppServicePlanSouthCentralUS*, a następnie wybierz przycisk **OK** . |

## <a name="create-a-front-door-for-your-application"></a>Tworzenie usługi Front Door dla aplikacji

Skonfiguruj drzwiczki frontonu platformy Azure, aby skierować ruch użytkowników na podstawie najmniejszego opóźnienia między dwoma serwerami usługi Web Apps. Aby rozpocząć, Dodaj hosta frontonu dla drzwi frontonu platformy Azure.

1. Na stronie głównej lub w menu platformy Azure wybierz pozycję **Utwórz zasób**. Wybierz pozycję **Sieć**  >  **przód drzwi**.

1. W obszarze **Tworzenie czołowych drzwi**wybierz **subskrypcję**.

1. W obszarze **Grupa zasobów**wybierz pozycję **Nowy**, a następnie wprowadź *FrontDoorQS_rg0* a następnie wybierz **przycisk OK**.  Zamiast tego można użyć istniejącej grupy zasobów.

1. Jeśli utworzono grupę zasobów, wybierz **lokalizację grupy zasobów**, a następnie wybierz kolejno pozycje **Dalej: Konfiguracja**.

1. W obszarze **frontony/domeny**wybierz opcję **+** otwarcia **Dodaj hosta frontonu**.

1. W obszarze **Nazwa hosta**wprowadź globalnie unikatową nazwę hosta. W tym przykładzie używamy *contoso-frontonu*. Wybierz pozycję **Dodaj**.

   ![Dodawanie hosta frontonu dla drzwi platformy Azure](media/quickstart-create-front-door/add-frontend-host-azure-front-door.png)

Następnie Utwórz pulę zaplecza zawierającą dwie aplikacje sieci Web.

1. Nadal w obszarze **Tworzenie czołowych drzwi**w obszarze **Pule zaplecza**wybierz pozycję **+** Otwórz **Dodaj pulę zaplecza**.

1. W obszarze **Nazwa**wprowadź *myBackendPool*.

1. Wybierz pozycję **Dodaj zaplecze**. W obszarze **typ hosta zaplecza**wybierz pozycję *App Service*.

1. Wybierz swoją subskrypcję, a następnie wybierz pierwszą aplikację sieci Web utworzoną na podstawie **nazwy hosta zaplecza**. W tym przykładzie aplikacja sieci Web była *WebAppContoso-1*. Wybierz pozycję **Dodaj**.

1. Ponownie wybierz pozycję **Dodaj wewnętrzną bazę danych** . W obszarze **typ hosta zaplecza**wybierz pozycję *App Service*.

1. Wybierz swoją subskrypcję, a następnie wybierz drugą aplikację sieci Web utworzoną na podstawie **nazwy hosta zaplecza**. Wybierz pozycję **Dodaj**.

   ![Dodawanie hosta zaplecza do drzwi przednich](media/quickstart-create-front-door/add-backend-host-pool-azure-front-door.png)

Na koniec Dodaj regułę routingu. Reguła routingu mapuje hosta frontonu do puli zaplecza. Reguła przekazuje żądanie do `contoso-frontend.azurefd.net` **myBackendPool**.

1. W obszarze **reguły routingu**można nadal **tworzyć drzwi z przodu**, a następnie wybrać opcję **+** konfigurowania reguły routingu.

1. W polu **Dodaj regułę**, dla **nazwy**wpisz *LocationRule*. Zaakceptuj wszystkie wartości domyślne, a następnie wybierz pozycję **Dodaj** , aby dodać regułę routingu.

   >[!WARNING]
   > **Musisz** się upewnić, że każdy host frontonu w swoich drzwiach frontonu ma regułę routingu ze `\*` skojarzoną z nią ścieżką domyślną (). Oznacza to, że we wszystkich regułach routingu musi istnieć co najmniej jedna reguła routingu dla każdego z hostów frontonu zdefiniowanych w ścieżce domyślnej ( `\*` ). Niewykonanie tej czynności może spowodować, że ruch użytkowników końcowych nie zostanie prawidłowo przekierowany.

1. Wybierz kolejno pozycje **Przegląd + Utwórz**i **Utwórz**.

   ![Skonfigurowane drzwi frontonu platformy Azure](media/quickstart-create-front-door/configuration-azure-front-door.png)

## <a name="view-azure-front-door-in-action"></a>Wyświetl działania z przodu platformy Azure w działaniu

Po utworzeniu drzwi czołowych konfiguracja zostanie wdrożona globalnie przez kilka minut. Po zakończeniu uzyskaj dostęp do utworzonego hosta frontonu. W przeglądarce przejdź do `contoso-frontend.azurefd.net` . Twoje żądanie zostanie automatycznie przekazane do najbliższego serwera z określonych serwerów w puli zaplecza.

Jeśli te aplikacje zostały utworzone w tym przewodniku Szybki Start, zobaczysz stronę z informacjami.

Aby przetestować natychmiastową globalną pracę w trybie failover, spróbuj wykonać następujące czynności:

1. Otwórz przeglądarkę, zgodnie z powyższym opisem, i przejdź do adresu frontonu: `contoso-frontend.azurefd.net` .

1. W Azure Portal Wyszukaj i wybierz pozycję *App Services*. Przewiń w dół, aby znaleźć jedną z aplikacji sieci Web, w tym przykładzie **WebAppContoso-1** .

1. Wybierz aplikację internetową, a następnie wybierz pozycję **Zatrzymaj**i **przycisk tak** , aby sprawdzić.

1. Odśwież przeglądarkę. Powinna zostać wyświetlona ta sama strona informacji.

   >[!TIP]
   >Istnieje nieco kilka opóźnień dla tych działań. Może być konieczne ponowne odświeżenie.

1. Znajdź inną aplikację sieci Web i zatrzymaj ją.

1. Odśwież przeglądarkę. Tym razem powinien zostać wyświetlony komunikat o błędzie.

   ![Oba wystąpienia aplikacji sieci Web zostały zatrzymane](media/quickstart-create-front-door/web-app-stopped-message.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu możesz usunąć wszystkie utworzone elementy. Usunięcie grupy zasobów spowoduje również usunięcie jej zawartości. Jeśli nie zamierzasz korzystać z tych drzwi, Usuń zasoby, aby uniknąć niepotrzebnych opłat.

1. W Azure Portal Wyszukaj i wybierz **grupy zasobów**lub wybierz pozycję **grupy zasobów** z menu Azure Portal.

1. Odfiltruj lub przewiń w dół, aby znaleźć grupę zasobów, np. **FrontDoorQS_rg0**.

1. Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

   >[!WARNING]
   >Ta akcja to irreversable.

1. Wpisz nazwę grupy zasobów do zweryfikowania, a następnie wybierz pozycję **Usuń**.

Powtórz procedurę dla pozostałych dwóch grup.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak dodać domenę niestandardową do swoich drzwi.
> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](front-door-custom-domain.md)

Aby dowiedzieć się więcej o ruchu routingu, zobacz [metody routingu front-drzwi](front-door-routing-methods.md).
