---
title: Zarządzaj planem App Service
description: Dowiedz się, jak wykonywać różne zadania związane z zarządzaniem planem App Service, takim jak tworzenie, przenoszenie, skalowanie i usuwanie.
keywords: App Service, Azure App Service, skala, plan usługi App Service, zmiana, tworzenie, zarządzanie, zarządzanie
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: bb0765a1d7934d60f787ed277dd3bd1f9bc1359b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962948"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Zarządzanie planem App Service na platformie Azure

[Plan Azure App Service](overview-hosting-plans.md) zapewnia zasoby, które aplikacja App Service musi uruchomić. W tym przewodniku pokazano, jak zarządzać planem App Service.

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

> [!TIP]
> Jeśli masz App Service Environment, zobacz [Tworzenie planu App Service w App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Można utworzyć pusty plan App Service lub utworzyć plan w ramach tworzenia aplikacji.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.

   ![Utwórz zasób w Azure Portal.][createResource] 

1. Wybierz pozycję **Nowa**  >  **aplikacja internetowa** lub inna aplikacja usługi App Service.

   ![Utwórz aplikację w Azure Portal.][createWebApp] 

2. Skonfiguruj sekcję **szczegóły wystąpienia** przed skonfigurowaniem planu App Service. Takie ustawienia jak **Publikowanie** i **systemy operacyjne** mogą zmieniać dostępne warstwy cenowe dla planu App Service. **Region** określa miejsce utworzenia planu App Service. 
   
3. W sekcji **Plan App Service** wybierz istniejący plan lub Utwórz plan, wybierając pozycję **Utwórz nowy**.

   ![Utwórz plan App Service.][createASP] 

4. Podczas tworzenia planu można wybrać warstwę cenową nowego planu. W obszarze **jednostka SKU i rozmiar**wybierz pozycję **Zmień rozmiar** , aby zmienić warstwę cenową. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Przenoszenie aplikacji do innego planu App Service

Możesz przenieść aplikację do innego planu App Service, o ile plan źródłowy i plan docelowy znajdują się w _tej samej grupie zasobów i regionie geograficznym_.

> [!NOTE]
> Platforma Azure wdraża każdy nowy plan App Service w jednostce wdrożenia wewnętrznie nazywanej przestrzenią sieci Web. Każdy region może mieć wiele przestrzeni sieci Web, ale aplikacja może przenosić się tylko między planami, które są tworzone w tym samym obszarze sieci Web. App Service Environment jest izolowanym miejscem sieci Web, dzięki czemu aplikacje mogą być przenoszone między planami w tym samym App Service Environment, ale nie między planami w różnych środowiskach App Service.
>
> Nie można określić miejsca sieci Web, które ma być używane podczas tworzenia planu, ale istnieje możliwość upewnienia się, że plan jest tworzony w tym samym obszarze sieci Web co istniejący plan. We wszystkich planach utworzonych za pomocą tej samej kombinacji grupy zasobów i regionu są wdrażane w tym samym obszarze sieci Web. Na przykład jeśli utworzono plan w grupie zasobów a i w regionie B, to każdy plan, który następnie utworzysz w grupie zasobów a i region B jest wdrażany w tym samym obszarze sieci Web. Należy pamiętać, że plany nie mogą przenosić obszarów sieci Web po ich utworzeniu, dlatego nie można przenieść planu do "tego samego obszaru sieci Web" jako innego planu przez przeniesienie go do innej grupy zasobów.
> 

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **App Services** , a następnie wybierz aplikację, którą chcesz przenieść.

2. Z menu po lewej stronie wybierz pozycję **zmień App Service plan**.

3. Na liście rozwijanej **plan App Service** wybierz istniejący plan, do którego chcesz przenieść aplikację. Lista rozwijana zawiera tylko plany, które znajdują się w tej samej grupie zasobów i regionie geograficznym co bieżący plan App Service. Jeśli taki plan nie istnieje, umożliwia utworzenie planu domyślnie. Możesz również utworzyć nowy plan ręcznie, wybierając pozycję **Utwórz nowy**.

4. W przypadku utworzenia planu można wybrać warstwę cenową nowego planu. W obszarze **warstwa cenowa**wybierz istniejącą warstwę, aby ją zmienić. 
   
   > [!IMPORTANT]
   > Jeśli przenosisz aplikację z planu wyższego poziomu do planu niższego poziomu, takiego jak od **D1** do **F1**, aplikacja może utracić pewne możliwości w planie docelowym. Na przykład jeśli aplikacja używa certyfikatów TLS/SSL, może zostać wyświetlony następujący komunikat o błędzie:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Po zakończeniu wybierz przycisk **OK**.
   
   ![Selektor planu App Service.][change] 

## <a name="move-an-app-to-a-different-region"></a>Przenoszenie aplikacji do innego regionu

Region, w którym jest uruchomiona aplikacja, jest regionem App Service planu. Nie można jednak zmienić regionu planu App Service. Jeśli chcesz uruchomić aplikację w innym regionie, jedną alternatywą jest klonowanie aplikacji. Klonowanie tworzy kopię aplikacji w nowym lub istniejącym planie App Service w dowolnym regionie.

**Klonowanie aplikacji** można znaleźć w sekcji **Narzędzia programistyczne** w menu.

> [!IMPORTANT]
> Klonowanie ma pewne ograniczenia. Informacje o nich można znaleźć w [Azure App Service klonowania aplikacji](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skalowanie planu App Service

Aby skalować w górę warstwę cenową planu App Service, zobacz [skalowanie w górę aplikacji na platformie Azure](manage-scale-up.md).

Aby skalować liczbę wystąpień aplikacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/platform/autoscale-get-started.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Usuwanie planu App Service

Aby uniknąć nieoczekiwanych opłat, po usunięciu ostatniej aplikacji w planie App Service, App Service również domyślnie usuwa plan. Jeśli wybierzesz opcję zachowania planu, należy zmienić plan na **bezpłatna** , aby nie naliczać opłat.

> [!IMPORTANT]
> Plany App Service, do których nie są skojarzone żadne aplikacje, nadal będą naliczane opłaty, ponieważ nadal rezerwują skonfigurowane wystąpienia maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png