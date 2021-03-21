---
title: Skalowanie funkcji i pojemności
description: Dowiedz się, jak skalować aplikację w górę w Azure App Service. Uzyskaj więcej informacji na temat procesora CPU, pamięci, miejsca na dysku i dodatkowych funkcji.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582491"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Skalowanie aplikacji w górę w Azure App Service

W tym artykule przedstawiono sposób skalowania aplikacji w Azure App Service. Istnieją dwa przepływy pracy umożliwiające skalowanie, skalowanie w górę i w poziomie, a w tym artykule opisano przepływ pracy skalowania w górę.

* [Skalowanie w górę](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Uzyskaj więcej informacji na temat procesora CPU, pamięci, miejsca na dysku i dodatkowych funkcji, takich jak dedykowane maszyny wirtualne, domeny niestandardowe i certyfikaty, gniazda tymczasowe, Skalowanie automatyczne i nie tylko. Możesz skalować w górę, zmieniając warstwę cenową planu App Service, do którego należy aplikacja.
* [Skalowanie w poziomie](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Zwiększ liczbę wystąpień maszyn wirtualnych, na których działa aplikacja.
  Możesz skalować w poziomie do maksymalnie 30 wystąpień, w zależności od warstwy cenowej. [Środowiska App Service](environment/intro.md) w warstwie **izolowanej** dodatkowo zwiększają liczbę wystąpień skalowania do 100. Aby uzyskać więcej informacji na temat skalowania w poziomie, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/autoscale/autoscale-get-started.md). W tym miejscu możesz dowiedzieć się, jak używać funkcji automatycznego skalowania, która polega na automatycznym skalowaniu liczby wystąpień na podstawie wstępnie zdefiniowanych reguł i harmonogramów.

Zastosowanie ustawień skalowania trwa tylko kilka sekund i ma wpływ na wszystkie aplikacje w [planie App Service](../app-service/overview-hosting-plans.md).
Nie wymagają one zmiany kodu ani ponownego wdrożenia aplikacji.

Aby uzyskać informacje na temat cen i funkcji poszczególnych planów App Service, zobacz [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Przed przełączeniem planu App Service z warstwy **bezpłatna** należy najpierw usunąć [limity wydatków](https://azure.microsoft.com/pricing/spending-limits/) dotyczące subskrypcji platformy Azure. Aby wyświetlić lub zmienić opcje subskrypcji App Service Microsoft Azure, zobacz [Microsoft Azure subskrypcje][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skalowanie w górę warstwy cenowej

> [!NOTE]
> Aby skalować w górę do warstwy **PremiumV3** , zobacz [Konfigurowanie warstwy PremiumV3 dla App Service](app-service-configure-premium-tier.md).
>

1. W przeglądarce otwórz witrynę [Azure Portal][portal].

1. Na stronie aplikacji App Service z menu po lewej wybierz pozycję Skaluj w **górę (plan App Service)**.
   
3. Wybierz warstwę, a następnie wybierz pozycję **Zastosuj**. Wybierz różne kategorie (na przykład **produkcyjne**), a także **dodatkowe opcje** , aby wyświetlić więcej warstw.
   
    ![Przejdź do skalowania w górę aplikacji platformy Azure.][ChooseWHP]

    Po zakończeniu operacji zobaczysz okno podręczne powiadomień z zielonym znacznikiem wyboru.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Zasoby dotyczące skalowania
Jeśli aplikacja zależy od innych usług, takich jak Azure SQL Database lub usługa Azure Storage, możesz osobno skalować te zasoby. Te zasoby nie są zarządzane przez plan App Service.

1. Na stronie **Przegląd** aplikacji wybierz łącze **Grupa zasobów** .
   
    ![Skalowanie zasobów związanych z aplikacją platformy Azure w górę](./media/web-sites-scale/RGEssentialsLink.png)

2. Na stronie **podsumowującej** **grupy zasobów** wybierz zasób, który chcesz skalować. Poniższy zrzut ekranu przedstawia zasób SQL Database.
   
    ![Przejdź do strony grupy zasobów, aby skalować w górę aplikację platformy Azure](./media/web-sites-scale/ResourceGroup.png)

    Aby skalować w górę powiązane zasoby, zapoznaj się z dokumentacją dla określonego typu zasobu. Na przykład aby skalować w górę pojedyncze SQL Database, zobacz [skalowanie zasobów pojedynczej bazy danych w Azure SQL Database](../azure-sql/database/single-database-scale.md). Aby skalować zasób Azure Database for MySQL, zobacz [skalowanie zasobów MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Porównaj warstwy cenowe

Aby uzyskać szczegółowe informacje, takie jak rozmiary maszyn wirtualnych dla każdej warstwy cenowej, zobacz [App Service szczegóły cennika](https://azure.microsoft.com/pricing/details/app-service).

Aby zapoznać się z tabelą limity usług, przydziałów i ograniczeń oraz obsługiwane funkcje w poszczególnych warstwach, zobacz [limity App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Więcej zasobów

[Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/autoscale/autoscale-get-started.md)  
[Konfigurowanie warstwy PremiumV3 dla App Service](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png