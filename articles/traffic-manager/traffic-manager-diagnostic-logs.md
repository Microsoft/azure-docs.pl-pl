---
title: Włącz rejestrowanie zasobów na platformie Azure Traffic Manager
description: Dowiedz się, jak włączyć rejestrowanie zasobów dla profilu Traffic Manager i uzyskać dostęp do plików dziennika, które są tworzone w wyniku.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 4cf3709574e2055f40759fd2d7026c93ac9db098
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608023"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Włącz rejestrowanie zasobów na platformie Azure Traffic Manager

W tym artykule opisano sposób włączania zbierania dzienników zasobów diagnostycznych i uzyskiwania dostępu do danych dziennika dla profilu Traffic Manager.

Dzienniki zasobów usługi Azure Traffic Manager mogą zapewnić wgląd w zachowanie zasobu profilu Traffic Manager. Na przykład można użyć danych dziennika profilu, aby określić, dlaczego poszczególne sondy przekroczyły limit czasu względem punktu końcowego.

## <a name="enable-resource-logging"></a>Włączanie rejestrowania zasobów

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić następujące polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera potrzebny jest moduł Azure PowerShell, 1.0.0 lub nowszy. Można uruchomić, `Get-Module -ListAvailable Az` Aby znaleźć zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie, `Login-AzAccount` Aby zalogować się do platformy Azure.

1. **Pobierz profil Traffic Manager:**

    Aby włączyć rejestrowanie zasobów, wymagany jest identyfikator Traffic Manager profilu. Pobierz profil Traffic Manager, dla którego chcesz włączyć rejestrowanie zasobów w programie [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). Dane wyjściowe zawierają informacje o IDENTYFIKATORze profilu Traffic Manager.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Włącz rejestrowanie zasobów dla profilu Traffic Manager:**

    Włącz rejestrowanie zasobów dla profilu Traffic Manager przy użyciu identyfikatora uzyskanego w poprzednim kroku przy użyciu polecenia [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Następujące polecenie przechowuje pełne dzienniki dla profilu Traffic Manager na określonym koncie usługi Azure Storage. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Sprawdź ustawienia diagnostyczne:**

      Sprawdź ustawienia diagnostyczne profilu Traffic Manager przy użyciu polecenia [Get-AzDiagnosticSetting](/powershell/module/az.monitor/get-azdiagnosticsetting). Następujące polecenie wyświetla kategorie, które są rejestrowane dla zasobu.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Upewnij się, że wszystkie kategorie dzienników skojarzone z zasobem profilu Traffic Manager są wyświetlane jako włączone. Sprawdź również, czy konto magazynu zostało prawidłowo ustawione.

## <a name="access-log-files"></a>Pliki dziennika dostępu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Przejdź do konta usługi Azure Storage w portalu.
2. Na stronie **Przegląd** konta usługi Azure Storage w obszarze **usługi** wybierz pozycję **obiekty blob**.
3. W przypadku **kontenerów** wybierz pozycję **Insights-Logs-probehealthstatusevents** i przejdź do PT1H.jsw pliku i kliknij pozycję **Pobierz** , aby pobrać i zapisać kopię tego pliku dziennika.

    ![Uzyskiwanie dostępu do plików dziennika profilu Traffic Manager z magazynu obiektów BLOB](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager schematu dziennika

Wszystkie dzienniki zasobów dostępne za pomocą Azure Monitor współdzielą wspólny schemat najwyższego poziomu, z elastycznością dla każdej usługi do emitowania unikatowych właściwości dla własnych zdarzeń. Aby uzyskać schemat dzienników zasobów najwyższego poziomu, zobacz [obsługiwane usługi, schematy i kategorie dla dzienników zasobów platformy Azure](../azure-monitor/essentials/resource-logs-schema.md).

W poniższej tabeli przedstawiono schemat dzienników specyficzny dla zasobu profilu usługi Azure Traffic Manager.

|Nazwa pola|Typ pola|Definicja|Przykład|
|----|----|---|---|
|Nazwapunktukoncowego|Ciąg|Nazwa punktu końcowego Traffic Manager, którego stan kondycji jest rejestrowany.|*myPrimaryEndpoint*|
|Stan|Ciąg|Stan kondycji punktu końcowego Traffic Manager, który był sondowany. Stan może być w **górę** lub **w dół**.|**W górę**|
|||||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [monitorowaniu Traffic Manager](traffic-manager-monitoring.md)