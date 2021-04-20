---
title: 'Szybki start: konfigurowanie wysokiej dostępności za pomocą Azure Front Door Service — Azure Portal'
description: W tym przewodniku Szybki start pokazano, jak używać Azure Front Door Service dla globalnej aplikacji internetowej o wysokiej Azure Portal.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 74b7c16904c86751076d40056027999fe44cb868
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727871"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Szybki start: tworzenie usługi Front Door na potrzeby globalnej aplikacji internetowej o wysokiej dostępności

Rozpoczynanie pracy z Azure Front Door przy użyciu Azure Portal do skonfigurowania wysokiej dostępności dla aplikacji internetowej.

W tym przewodniku Szybki Azure Front Door pule dwóch wystąpień aplikacji internetowej, które działają w różnych regionach platformy Azure. Tworzysz konfigurację Front Door na podstawie za zaplecza o takim samym priorytecie i takim samym priorytecie. Ta konfiguracja kieruje ruch do najbliższej lokacji, w których działa aplikacja. Azure Front Door stale monitoruje aplikację internetową. Usługa zapewnia automatyczne tryb failover do następnej dostępnej lokacji, gdy najbliższa lokacja jest niedostępna.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagram przedstawiający Front Door wdrażania przy użyciu Azure Portal." border="false":::

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Tworzenie dwóch wystąpień aplikacji internetowej

Ten przewodnik Szybki start wymaga dwóch wystąpień aplikacji internetowej, które działają w różnych regionach świadczenia usługi Azure. Oba wystąpienia aplikacji internetowej działają w *trybie aktywny/aktywny,* więc jedno z nich może przyjmować ruch. Ta konfiguracja różni się od konfiguracji *aktywnej/autonomicznej,* w której działa jako tryb failover.

Jeśli nie masz jeszcze aplikacji internetowej, użyj następujących kroków, aby skonfigurować przykładowe aplikacje internetowe.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

1. W lewej górnej części ekranu wybierz pozycję **Utwórz zasób**  >   **WebApp.**

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Tworzenie aplikacji internetowej w witrynie Azure Portal":::

1. Na karcie **Podstawowe** na **stronie Tworzenie aplikacji internetowej** wprowadź lub wybierz poniższe informacje.

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | **Subskrypcja**               | Wybierz subskrypcję. |    
    | **Grupa zasobów**       | Wybierz **pozycję Utwórz nowy** i *FrontDoorQS_rg1* w polu tekstowym.|
    | **Nazwa**                   | Wprowadź **unikatową nazwę** aplikacji internetowej. W tym przykładzie *użyto sieci WebAppContoso-1.* |
    | **Publikowanie** | Wybierz pozycję **Kod**. |
    | **Stos środowiska uruchomieniowego**         | Wybierz **pozycję .NET Core 2.1 (LTS)**. |
    | **System operacyjny**          | Wybierz **pozycję Windows**. |
    | **Region**           | Wybierz **pozycję Środkowe usa.** |
    | **Plan systemu Windows** | Wybierz **pozycję Utwórz nową** i *wprowadź myAppServicePlanCentralUS* w polu tekstowym. |
    | **Jednostka SKU i rozmiar** | Wybierz **pozycję Standard S1 100 total ACU, 1,75 GB pamięci.** |

1. Wybierz **pozycję Przejrzyj i utwórz,** przejrzyj **podsumowanie,** a następnie wybierz pozycję **Utwórz.** Ukończenie wdrożenia może potrwać kilka minut.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Podsumowanie przeglądu aplikacji internetowej":::

Po zakończeniu wdrażania utwórz drugą aplikację internetową. Użyj tej samej procedury z tą samą wartością, z wyjątkiem następujących wartości:

| Ustawienie          | Wartość     |
| ---              | ---  |
| **Grupa zasobów**   | Wybierz **pozycję Utwórz nową** i *wprowadź FrontDoorQS_rg2* |
| **Nazwa**             | Wprowadź unikatową nazwę aplikacji internetowej, w tym przykładzie *WebAppContoso-2*  |
| **Region**           | Inny region, w tym przykładzie *Wschodnie usa* |
| **App Service planu**  >  **Plan systemu Windows**         | Wybierz **pozycję Nowy,** wprowadź *myAppServicePlanEastUS,* a następnie wybierz przycisk **OK.** |

## <a name="create-a-front-door-for-your-application"></a>Tworzenie usługi Front Door dla aplikacji

Skonfiguruj Azure Front Door, aby kierować ruch użytkowników na podstawie najmniejszego opóźnienia między dwoma serwerami aplikacji internetowych. Aby rozpocząć, dodaj hosta frontonia dla Azure Front Door.

1. Na stronie głównej lub w menu platformy Azure wybierz pozycję **Utwórz zasób.** Wybierz **pozycję Sieć** Zobacz  >  **wszystkie**  >  **Front Door.**

1. Na karcie **Podstawowe** na stronie **Tworzenie Front Door** wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Dalej: Konfiguracja.**

    | Ustawienie | Wartość |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |    
    | **Grupa zasobów** | Wybierz **pozycję Utwórz nowy** i *FrontDoorQS_rg0* w polu tekstowym.|
    | **Lokalizacja grupy zasobów** | Wybierz **pozycję Środkowe usa**. |

1. W **menu Frontends/domains (Frontends/domeny)** wybierz pozycję **+** , aby otworzyć pozycję Add a **frontend host (Dodaj hosta frontonia).**

1. W **polach Nazwa hosta** wprowadź globalnie unikatową nazwę hosta. W tym przykładzie *użyto contoso-frontend*. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Dodawanie hosta frontonia dla Azure Front Door":::

Następnie utwórz pulę zaplecza zawierającą dwie aplikacje internetowe.

1. Nadal w **daniu Tworzenie Front Door** w **pulach zaplecza** wybierz pozycję Dodaj **+** **pulę zaplecza.**

1. W **sekcji Nazwa** wprowadź wartość *myBackendPool,* a następnie wybierz pozycję Dodaj **zaplecza.**

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Dodawanie puli zaplecza":::

1. W bloku **Dodawanie zaplecza wybierz** poniższe informacje i wybierz pozycję **Dodaj**.

    | Ustawienie | Wartość |
    | --- | --- |
    | **Typ hosta zaplecza** | Wybierz **pozycję App Service.** |   
    | **Subskrypcja** | Wybierz subskrypcję. |    
    | **Nazwa hosta zaplecza** | Wybierz pierwszą utworzoną aplikację internetową. W tym przykładzie aplikacja internetowa to *WebAppContoso-1.* |

    **Pozostaw wszystkie pozostałe pola domyślne.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Dodawanie hosta zaplecza do bazy Front Door":::

1. Wybierz **ponownie pozycję Dodaj zaplecza.** Wybierz poniższe informacje, a następnie wybierz pozycję **Dodaj**.

    | Ustawienie | Wartość |
    | --- | --- |
    | **Typ hosta zaplecza** | Wybierz **pozycję App Service.** |   
    | **Subskrypcja** | Wybierz subskrypcję. |    
    | **Nazwa hosta zaplecza** | Wybierz drugą utworzoną aplikację internetową. W tym przykładzie aplikacja internetowa to *WebAppContoso-2.* |

    **Pozostaw wszystkie inne pola domyślne.*

1. Wybierz **pozycję** Dodaj w **bloku Dodawanie puli zaplecza,** aby ukończyć konfigurację puli zaplecza.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Dodawanie puli zaplecza dla Azure Front Door":::

Na koniec dodaj regułę rozsyłania. Reguła routingu mapuje hosta frontonia na pulę zaplecza. Reguła przekazuje żądanie do puli `contoso-frontend.azurefd.net` **myBackendPool.**

1. W nadal **w Front Door**, w opcji **Reguły routingu** wybierz **+** opcję, aby skonfigurować regułę rozsyłania.

1. W **pozycji Dodaj regułę** w **pozycji Nazwa** wprowadź *LocationRule*. Zaakceptuj wszystkie wartości domyślne, a następnie wybierz **pozycję Dodaj,** aby dodać regułę rozsyłania.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Dodawanie reguły do Front Door":::

   >[!WARNING]
   > Należy **upewnić** się, że każdy z hostów frontonu w Front Door ma regułę routingu z skojarzoną domyślną ścieżką ( `\*` ). Oznacza to, że we wszystkich regułach routingu musi być co najmniej jedna reguła rozsyłania dla każdego z hostów frontendu zdefiniowanych w ścieżce domyślnej ( `\*` ). Nie można tego zrobić, może to spowodować, że ruch użytkowników końcowych nie będzie prawidłowo przekierowynyny.

1. Wybierz **pozycję Przejrzyj i utwórz,** a następnie pozycję **Utwórz.**

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Skonfigurowane Azure Front Door":::

## <a name="view-azure-front-door-in-action"></a>Wyświetlanie Azure Front Door w akcji

Po utworzeniu Front Door globalne wdrożenie konfiguracji może potrwać kilka minut. Po zakończeniu uzyskaj dostęp do utworzonego hosta frontonia. W przeglądarce przejdź do strony `contoso-frontend.azurefd.net` . Twoje żądanie zostanie automatycznie kierowane do najbliższego serwera z określonych serwerów w puli zaplecza.

Jeśli te aplikacje zostały utworzone w tym przewodniku Szybki start, zobaczysz stronę z informacjami.

Aby przetestować natychmiastowe działanie globalnego trybu failover, spróbuj wykonać następujące czynności:

1. Otwórz przeglądarkę, jak opisano powyżej, i przejdź do adresu frontonia: `contoso-frontend.azurefd.net` .

1. W polu Azure Portal wyszukaj i wybierz pozycję *App Services.* Przewiń w dół, aby znaleźć jedną z aplikacji internetowych **WebAppContoso-1** w tym przykładzie.

1. Wybierz aplikację internetową, a następnie wybierz pozycję **Zatrzymaj** i wybierz pozycję **Tak, aby** zweryfikować.

1. Odśwież przeglądarkę. Powinna zostać wyświetlony ta sama strona informacji.

   >[!TIP]
   >Istnieje niewielkie opóźnienie dla tych akcji. Może być konieczne odświeżenie ponownie.

1. Znajdź drugą aplikację internetową i zatrzymaj ją.

1. Odśwież przeglądarkę. Tym razem powinien zostać wyświetlony komunikat o błędzie.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Oba wystąpienia aplikacji internetowej zatrzymane":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu możesz usunąć wszystkie utworzone elementy. Usunięcie grupy zasobów powoduje również usunięcie jej zawartości. Jeśli nie zamierzasz używać tego Front Door, usuń zasoby, aby uniknąć niepotrzebnych opłat.

1. W Azure Portal wyszukaj i wybierz pozycję **Grupy zasobów** lub wybierz pozycję **Grupy zasobów** z Azure Portal zasobów.

1. Przefiltruj lub przewiń w dół, aby znaleźć grupę zasobów, na **przykład FrontDoorQS_rg0**.

1. Wybierz grupę zasobów, a następnie wybierz **pozycję Usuń grupę zasobów.**

   >[!WARNING]
   >Ta akcja jest nieodwracalna.

1. Wpisz nazwę grupy zasobów do zweryfikowania, a następnie wybierz pozycję **Usuń**.

Powtórz procedurę dla dwóch pozostałych grup.

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak dodać domenę niestandardową do Front Door.
> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](front-door-custom-domain.md)
