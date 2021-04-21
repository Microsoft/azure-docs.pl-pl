---
title: Konfigurowanie warstwy PremiumV3
description: Dowiedz się, jak poprawić wydajność aplikacji internetowej, mobilnej i interfejsu API na Azure App Service przez skalowanie do nowej warstwy cenowej PremiumV3.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fbd841626e2a074bc0a35cd1b4ac094e267b34a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833350"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Konfigurowanie warstwy PremiumV3 dla Azure App Service

Nowa warstwa **cenowa PremiumV3** zapewnia szybsze procesory, magazyn SSD i czterokrotnie większą ilość pamięci niż w przypadku istniejących warstw cenowych (dwukrotnie więcej niż warstwa **PremiumV2).** Dzięki mniejszej wydajności możesz zaoszczędzić pieniądze, uruchamiając aplikacje w mniejszej liczbie wystąpień. Z tego artykułu dowiesz się, jak utworzyć aplikację w warstwie **PremiumV3** lub skalować aplikację w górę do warstwy **PremiumV3.**

## <a name="prerequisites"></a>Wymagania wstępne

Aby skalować aplikację w górę do warstwy **PremiumV3,** musisz mieć aplikację usługi Azure App Service, która działa w warstwie cenowej niższej niż **PremiumV3,** a aplikacja musi być uruchomiona we wdrożeniu usługi App Service, które obsługuje warstwę PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Dostępność PremiumV3

Warstwa **PremiumV3** jest dostępna zarówno dla aplikacji natywnych, jak i kontenerów, w tym kontenerów systemu Windows i kontenerów systemu Linux.

> [!NOTE]
> Wszystkie kontenery systemu Windows uruchomione w warstwie **Premium Container** w okresie zapoznawczym będą nadal działać w niezmienionej wersji, ale warstwa **Kontener Premium** będzie nadal w wersji zapoznawczej. Warstwa **PremiumV3** jest oficjalnym zamiennikiem warstwy **Premium Container.** 

**Wersja PremiumV3** jest dostępna w niektórych regionach świadczenia usługi Azure, a dostępność w dodatkowych regionach jest stale dodawana. Aby sprawdzić, czy jest ona dostępna w Twoim regionie, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell [:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Tworzenie aplikacji w warstwie PremiumV3

Warstwa cenowa aplikacji App Service jest zdefiniowana w [planie App Service,](overview-hosting-plans.md) w których jest uruchamiana. Możesz utworzyć plan App Service samodzielnie lub w ramach tworzenia aplikacji.

Podczas konfigurowania planu App Service w warstwie <a href="https://portal.azure.com" target="_blank">Azure Portal</a>wybierz **pozycję Warstwa cenowa**. 

Wybierz **pozycję Produkcja,** a **następnie wybierz pozycję P1V3,** **P2V3** lub **P3V3,** a następnie kliknij przycisk **Zastosuj.**

![Zrzut ekranu przedstawiający zalecane warstwy cenowe dla aplikacji.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Jeśli nie widzisz opcji **P1V3,** **P2V3** i **P3V3** lub jeśli opcje są wyszzarone, wersja **PremiumV3** prawdopodobnie nie jest dostępna w bazowym wdrożeniu usługi App Service, które zawiera plan App Service. Aby [uzyskać więcej informacji, zobacz](#unsupported) Skalowanie w górę z nieobsługiwanej kombinacji grup zasobów i regionów.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Skalowanie istniejącej aplikacji w górę do warstwy PremiumV3

Przed skalowaniem istniejącej aplikacji do warstwy **PremiumV3** upewnij się, że jest dostępna **wersja PremiumV3.** Aby uzyskać więcej informacji, zobacz [PremiumV3 availability (Dostępność PremiumV3).](#availability) Jeśli nie jest dostępna, zobacz [Scale up from an unsupported resource group and region combination](#unsupported)(Skalowanie w górę z nieobsługiwanej kombinacji grup zasobów i regionów).

W zależności od środowiska hostingu skalowanie w górę może wymagać dodatkowych kroków. 

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>otwórz stronę aplikacji App Service aplikacji.

Na lewym pasku nawigacyjnym App Service aplikacji wybierz pozycję Skaluj w górę **(App Service planu).**

![Zrzut ekranu przedstawiający sposób skalowania w górę planu usługi App Service.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wybierz **pozycję Produkcja,** a **następnie wybierz pozycję P1V3,** **P2V3** lub **P3V3,** a następnie kliknij przycisk **Zastosuj.**

![Zrzut ekranu przedstawiający zalecane warstwy cenowe dla aplikacji.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Jeśli operacja zakończy się pomyślnie, na stronie przeglądu aplikacji będzie widać, że jest ona teraz w **warstwie PremiumV3.**

![Zrzut ekranu przedstawiający warstwę cenową PremiumV3 na stronie przeglądu aplikacji.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Jeśli wystąpi błąd

Niektórych App Service nie można skalować w górę do warstwy PremiumV3, jeśli bazowe App Service nie obsługuje warstwy PremiumV3. Aby [uzyskać więcej informacji, zobacz](#unsupported) Skalowanie w górę z nieobsługiwanej kombinacji grup zasobów i regionów.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu

Jeśli aplikacja działa we wdrożeniu usługi App Service, w którym **wersja PremiumV3** nie jest dostępna, lub jeśli aplikacja działa w regionie, który obecnie nie obsługuje wersji **PremiumV3,** należy ponownie wdrożyć aplikację, aby skorzystać z wersji **PremiumV3.**  Dostępne są dwie opcje:

- Utwórz aplikację w nowej grupie zasobów z nowym planem App Service zasobów. Podczas tworzenia App Service wybierz warstwę **PremiumV3.** Ten krok zapewnia wdrożenie App Service wdrożenia w jednostce wdrożenia, która obsługuje wersję **PremiumV3.** Następnie ponownie wdychaj kod aplikacji w nowo utworzonej aplikacji. Nawet w przypadku skalowania planu App Service do niższej warstwy w celu obniżenia kosztów zawsze można skalować z powrotem do warstwy **PremiumV3,** ponieważ obsługuje to jednostka wdrażania.
- Jeśli aplikacja działa już w istniejącej warstwie **Premium,** możesz sklonować aplikację ze wszystkimi ustawieniami aplikacji, parametry połączenia i konfiguracją wdrożenia do nowej grupy zasobów w nowym planie usługi App Service, która używa warstwy **PremiumV3.**

    ![Zrzut ekranu przedstawiający sposób klonowania aplikacji.](media/app-service-configure-premium-tier/clone-app.png)

    Na stronie **Klonuj** aplikację możesz utworzyć plan usługi App Service **premiumV3** w innym regionie, a także określić ustawienia i konfigurację aplikacji, które chcesz sklonować.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Przenoszenie z kontenera Premium do wersji Premium V3

Jeśli masz aplikację korzystającą z wersji zapoznawczej wersji premium kontenera SKU i chcesz przejść do nowej wersji Premium V3 SKU, musisz ponownie wdedytować aplikację, aby skorzystać z wersji **PremiumV3.** Aby to zrobić, zobacz pierwszą opcję w obszarze Skalowanie w górę z nieobsługiwanej kombinacji [grupy zasobów i regionu](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Możesz zautomatyzować tworzenie aplikacji w warstwie **PremiumV3** za pomocą skryptów, przy użyciu interfejsu wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) [lub Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie tworzy plan App Service _P1V3._ Możesz uruchomić go w Cloud Shell. Dostępne opcje `--sku` to P1V3, _P2V3_ i _P3V3._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie tworzy plan App Service _P1V3._ Dostępne opcje `-WorkerSize` to _Small (Mały),_ _Medium (Średni)_ _i Large (Duży)._

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Więcej zasobów

[Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md) 
 [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/autoscale/autoscale-get-started.md)
