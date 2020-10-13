---
title: Konfigurowanie warstwy PremiumV3
description: Dowiedz się, jak ulepszyć wydajność aplikacji sieci Web, mobilnych i interfejsu API w Azure App Service przez skalowanie do nowej warstwy cenowej PremiumV3.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 0030a9340d874d94b9876e23f372e97655c145da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742706"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Konfigurowanie warstwy PremiumV3 dla Azure App Service

Nowa warstwa cenowa **PremiumV3** zapewnia szybszy procesor, magazyn SSD i czterokrotny stosunek pamięci do rdzenia istniejących warstw cenowych (Podwójna warstwa **PremiumV2** ). Korzystając z wydajności, możesz zaoszczędzić pieniądze, uruchamiając aplikacje na mniejszej liczbie wystąpień. Ten artykuł zawiera informacje na temat tworzenia aplikacji w warstwie **PremiumV3** lub skalowania aplikacji do warstwy **PremiumV3** .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skalować aplikację w górę do **PremiumV3**, musisz mieć aplikację Azure App Service działającą w warstwie cenowej niższą niż **PremiumV3**, a aplikacja musi działać w ramach wdrożenia App Service, które obsługuje PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Dostępność PremiumV3

Warstwa **PremiumV3** jest dostępna dla aplikacji macierzystych i kontenerów, w tym kontenerów systemu Windows i Linux.

> [!NOTE]
> Wszelkie kontenery systemu Windows działające w warstwie **Premium kontenera** w okresie zapoznawczym nadal działają w taki sam sposób, ale warstwa **kontenera Premium** nadal pozostaje w wersji zapoznawczej. Warstwa **PremiumV3** jest oficjalną wymianą dla warstwy **kontenera Premium** . 

**PremiumV3** jest dostępna w niektórych regionach świadczenia usługi Azure i dostępność w dodatkowych regionach jest ciągle dodawana. Aby sprawdzić, czy jest on dostępny w Twoim regionie, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure w [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Tworzenie aplikacji w warstwie PremiumV3

Warstwa cenowa aplikacji App Service jest definiowana w [planie App Service](overview-hosting-plans.md) , w którym jest uruchomiona. W ramach tworzenia aplikacji można utworzyć App Service plan samodzielny.

Podczas konfigurowania planu App Service w <a href="https://portal.azure.com" target="_blank">Azure Portal</a>wybierz pozycję **warstwa cenowa**. 

Wybierz pozycję **produkcja**, a następnie wybierz pozycję **P1V3**, **P2V3**lub **P3V3**, a następnie kliknij pozycję **Zastosuj**.

![Zrzut ekranu przedstawiający zalecane warstwy cenowe dla aplikacji.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Jeśli nie widzisz opcji **P1V3**, **P2V3**i **P3V3** jako opcje lub jeśli opcje są wyszarzone, to **PremiumV3** prawdopodobnie nie jest dostępna w podstawowym wdrożeniu App Service, które zawiera plan App Service. Aby uzyskać więcej informacji [, zobacz Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu](#unsupported) .

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Skalowanie istniejącej aplikacji do warstwy PremiumV3

Przed przeskalowaniem istniejącej aplikacji do warstwy **PremiumV3** upewnij się, że **PremiumV3** jest dostępna. Aby uzyskać więcej informacji, zobacz [PremiumV3 Availability (dostępność](#availability)). Jeśli nie jest dostępny, zobacz [skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu](#unsupported).

Skalowanie w górę może wymagać wykonania dodatkowych czynności w zależności od środowiska hostingu. 

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>Otwórz stronę aplikacji App Service.

W lewym panelu nawigacyjnym strony aplikacji App Service wybierz pozycję **Skaluj w górę (plan App Service)**.

![Zrzut ekranu przedstawiający sposób skalowania planu usługi App Service.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wybierz pozycję **produkcja**, a następnie wybierz pozycję **P1V3**, **P2V3**lub **P3V3**, a następnie kliknij pozycję **Zastosuj**.

![Zrzut ekranu przedstawiający zalecane warstwy cenowe dla aplikacji.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Jeśli operacja zakończyła się pomyślnie, na stronie Przegląd aplikacji zostanie wyświetlona, że znajduje się ona teraz w warstwie **PremiumV3** .

![Zrzut ekranu przedstawiający warstwę cenową PremiumV3 na stronie przeglądowej aplikacji.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Jeśli wystąpi błąd

Niektóre plany App Service nie mogą skalować w górę do warstwy PremiumV3, jeśli podstawowe wdrożenie App Service nie obsługuje PremiumV3. Aby uzyskać więcej informacji [, zobacz Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu](#unsupported) .

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu

Jeśli aplikacja działa w ramach wdrożenia App Service, w którym **PremiumV3** jest niedostępna, lub jeśli aplikacja działa w regionie, który obecnie nie obsługuje **PremiumV3**, należy ponownie wdrożyć aplikację, aby skorzystać z funkcji **PremiumV3**.  Dostępne są dwie opcje:

- Utwórz aplikację w nowej grupie zasobów i z nowym planem App Service. Podczas tworzenia planu App Service wybierz warstwę **PremiumV3** . Ten krok zapewnia, że plan App Service jest wdrażany w jednostce wdrożenia, która obsługuje **PremiumV3**. Następnie ponownie Wdróż kod aplikacji w nowo utworzonej aplikacji. Nawet w przypadku skalowania App Service planu w dół do niższej warstwy w celu oszczędności kosztów można zawsze skalować kopię zapasową do **PremiumV3** , ponieważ jest ona obsługiwana przez jednostkę wdrażania.
- Jeśli aplikacja jest już uruchomiona w istniejącej warstwie **Premium** , można sklonować aplikację ze wszystkimi ustawieniami aplikacji, parametrami połączeń i konfiguracją wdrożenia do nowego planu usługi App Service korzystającego z **PremiumV3**.

    ![Zrzut ekranu przedstawiający sposób klonowania aplikacji.](media/app-service-configure-premium-tier/clone-app.png)

    Na stronie **klonowanie aplikacji** można utworzyć plan App Service przy użyciu **PremiumV3** w wybranym regionie i określić ustawienia i konfigurację aplikacji, które mają zostać sklonowane.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Przechodzenie z kontenera Premium do jednostki SKU w warstwie Premium v3

Jeśli masz aplikację, która korzysta z jednostki SKU kontenera wersji zapoznawczej Premium, a chcesz przenieść ją do nowej jednostki SKU w wersji 3 Premium, musisz ponownie wdrożyć aplikację, aby skorzystać z **PremiumV3**. Aby to zrobić, zobacz pierwszą opcję w obszarze [skalowanie w górę od nieobsługiwanej grupy zasobów i regionu](#scale-up-from-an-unsupported-resource-group-and-region-combination) .

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Tworzenie aplikacji można zautomatyzować w warstwie **PremiumV3** za pomocą skryptów, korzystając z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie tworzy App Service plan w _P1V2_. Można uruchomić ją w Cloud Shell. Dostępne opcje `--sku` to P1V3, _P2V3_i _P3V3_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie tworzy App Service plan w _P1V3_. Opcje dla `-WorkerSize` są _małe_, _średnie_i _duże_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Dodatkowe zasoby

[Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md) 
 [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/platform/autoscale-get-started.md)