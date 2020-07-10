---
title: Konfigurowanie warstwy PremiumV2
description: Dowiedz się, jak ulepszyć wydajność aplikacji sieci Web, mobilnych i interfejsu API w Azure App Service przez skalowanie do nowej warstwy cenowej PremiumV2.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: 4fa64562f8d26297d2b2c8a13cd8b6a513c2c630
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170024"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Konfigurowanie warstwy PremiumV2 dla Azure App Service

Nowa warstwa cenowa **PremiumV2** zapewnia szybszy procesor, magazyn SSD i podwaja współczynnik pamięci na rdzeń istniejących warstw cenowych. Korzystając z wydajności, możesz zaoszczędzić pieniądze, uruchamiając aplikacje na mniejszej liczbie wystąpień. Ten artykuł zawiera informacje na temat tworzenia aplikacji w warstwie **PremiumV2** lub skalowania aplikacji do warstwy **PremiumV2** .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skalować aplikację w górę do **PremiumV2**, musisz mieć aplikację Azure App Service działającą w warstwie cenowej niższą niż **PremiumV2**, a aplikacja musi działać w ramach wdrożenia App Service, które obsługuje PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Dostępność PremiumV2

Warstwa **PremiumV2** jest dostępna dla App Service zarówno w _systemie Windows_ , jak i _Linux_.

**PremiumV2** jest dostępna w większości regionów świadczenia usługi Azure. Aby sprawdzić, czy jest on dostępny w Twoim regionie, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure w [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Tworzenie aplikacji w warstwie PremiumV2

Warstwa cenowa aplikacji App Service jest definiowana w [planie App Service](overview-hosting-plans.md) , w którym jest uruchomiona. W ramach tworzenia aplikacji można utworzyć App Service plan samodzielny.

Podczas konfigurowania planu App Service w <a href="https://portal.azure.com" target="_blank">Azure Portal</a>wybierz pozycję **warstwa cenowa**. 

Wybierz pozycję **produkcja**, a następnie wybierz pozycję **P1V2**, **P2V2**lub **P3V2**, a następnie kliknij pozycję **Zastosuj**.

![Zrzut ekranu przedstawiający zalecane warstwy cenowe dla aplikacji.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Jeśli nie widzisz opcji **P1V2**, **P2V2**i **P3V2** jako opcje lub jeśli opcje są wyszarzone, to **PremiumV2** prawdopodobnie nie jest dostępna w podstawowym wdrożeniu App Service, które zawiera plan App Service. Aby uzyskać więcej informacji [, zobacz Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu](#unsupported) .

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Skalowanie istniejącej aplikacji do warstwy PremiumV2

Przed przeskalowaniem istniejącej aplikacji do warstwy **PremiumV2** upewnij się, że **PremiumV2** jest dostępna. Aby uzyskać więcej informacji, zobacz [PremiumV2 Availability (dostępność](#availability)). Jeśli nie jest dostępny, zobacz [skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu](#unsupported).

Skalowanie w górę może wymagać wykonania dodatkowych czynności w zależności od środowiska hostingu. 

W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>Otwórz stronę aplikacji App Service.

W lewym panelu nawigacyjnym strony aplikacji App Service wybierz pozycję **Skaluj w górę (plan App Service)**.

![Zrzut ekranu przedstawiający sposób skalowania planu usługi App Service.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Wybierz pozycję **produkcja**, a następnie wybierz pozycję **P1V2**, **P2V2**lub **P3V2**, a następnie kliknij pozycję **Zastosuj**.

![Zrzut ekranu przedstawiający zalecane warstwy cenowe dla aplikacji.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Jeśli operacja zakończyła się pomyślnie, na stronie Przegląd aplikacji zostanie wyświetlona, że znajduje się ona teraz w warstwie **PremiumV2** .

![Zrzut ekranu przedstawiający warstwę cenową PremiumV2 na stronie przeglądowej aplikacji.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Jeśli wystąpi błąd

Niektóre plany App Service nie mogą skalować w górę do warstwy PremiumV2, jeśli podstawowe wdrożenie App Service nie obsługuje PremiumV2.  Aby uzyskać więcej informacji [, zobacz Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu](#unsupported) .

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skalowanie w górę z nieobsługiwanej kombinacji grupy zasobów i regionu

Jeśli aplikacja działa w ramach wdrożenia App Service, w którym **PremiumV2** jest niedostępna, lub jeśli aplikacja działa w regionie, który obecnie nie obsługuje **PremiumV2**, należy ponownie wdrożyć aplikację w celu skorzystania z **PremiumV2**.  Dostępne są dwie opcje:

- Utwórz **nową** grupę zasobów, a następnie Utwórz **nową** aplikację i App Service plan w **nowej** grupie zasobów, wybierając żądany region świadczenia usługi Azure w trakcie procesu tworzenia.  W momencie tworzenia nowego planu usługi App Service **musisz** wybrać plan **PremiumV2** .  Dzięki temu kombinacja grupy zasobów, planu App Service i regionu platformy Azure spowoduje utworzenie planu App Service w ramach wdrożenia App Service, które obsługuje **PremiumV2**.  Następnie ponownie Wdróż kod aplikacji w nowo utworzonej aplikacji i planie usługi App Service. W razie potrzeby można później skalować plan App Service w dół od **PremiumV2** , aby zaoszczędzić koszty. nadal będzie można pomyślnie skalować ponownie kopię zapasową w przyszłości przy użyciu **PremiumV2**.
- Jeśli aplikacja jest już uruchomiona w istniejącej warstwie **Premium** , można sklonować aplikację ze wszystkimi ustawieniami aplikacji, parametrami połączeń i konfiguracją wdrożenia do nowego planu usługi App Service korzystającego z **PremiumV2**.

    ![Zrzut ekranu przedstawiający sposób klonowania aplikacji.](media/app-service-configure-premium-tier/clone-app.png)

    Na stronie **klonowanie aplikacji** można utworzyć plan App Service przy użyciu **PremiumV2** w wybranym regionie i określić ustawienia i konfigurację aplikacji, które mają zostać sklonowane.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Tworzenie aplikacji można zautomatyzować w warstwie **PremiumV2** za pomocą skryptów, korzystając z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie tworzy App Service plan w _P1V2_. Można uruchomić ją w Cloud Shell. Dostępne opcje `--sku` to P1V2, _P2V2_i _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie tworzy App Service plan w _P1V2_. Opcje dla `-WorkerSize` są _małe_, _średnie_i _duże_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Dodatkowe zasoby

[Skalowanie aplikacji w górę na platformie Azure](manage-scale-up.md)  
[Ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md)
