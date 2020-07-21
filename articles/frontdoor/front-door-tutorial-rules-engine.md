---
title: 'Samouczek: Konfigurowanie aparatu reguł — drzwi platformy Azure'
description: Ten artykuł zawiera samouczek dotyczący konfigurowania aparatu reguł zarówno w Azure Portal, jak i w interfejsie wiersza polecenia.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: b40bb0e426571acc66d4f5f1b992fb4c6b67494a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536956"
---
# <a name="configure-your-rules-engine"></a>Skonfiguruj aparat reguł

W tym artykule przedstawiono procedurę tworzenia konfiguracji aparatu reguł oraz pierwszej reguły w obu Azure Portal i interfejsie wiersza polecenia. 

## <a name="configure-rules-engine-in-azure-portal"></a>Skonfiguruj aparat reguł w Azure Portal
1. Przed utworzeniem konfiguracji aparatu reguł [Utwórz drzwi z przodu](quickstart-create-front-door.md).

2. W obszarze zasób z drzwiami wstępnymi przejdź do pozycji **Ustawienia** i wybierz pozycję **Konfiguracja aparatu reguł**. Kliknij przycisk **Dodaj**, nadaj konfiguracji nazwę i Rozpocznij tworzenie pierwszej konfiguracji aparatu reguł.

    ![Znajdź aparat reguł](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Kliknij pozycję **Dodaj regułę** , aby utworzyć pierwszą regułę. Następnie klikając pozycję **Dodaj warunek** lub **Dodaj akcję** , można zdefiniować regułę.
    
    > [!NOTE]
    >- Aby usunąć warunek lub akcję z reguły, użyj kosza po prawej stronie określonego warunku lub akcji.
    > - Aby utworzyć regułę, która ma zastosowanie do całego ruchu przychodzącego, nie określaj żadnych warunków.
    > - Aby zatrzymać ocenianie reguł po spełnieniu pierwszego warunku dopasowania, zaznacz pole **Zatrzymaj szacowanie pozostałych reguł**. Jeśli ta opcja jest zaznaczona i spełnione są wszystkie warunki dopasowania określonej reguły, pozostałe reguły w konfiguracji nie zostaną wykonane.  

    ![Znajdź aparat reguł](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

4. Określ priorytet reguł w konfiguracji za pomocą przycisków Przenieś w górę, Przenieś w dół i Przenieś do góry. Priorytet znajduje się w kolejności rosnącej, co oznacza, że reguła pierwsza na liście jest najważniejszym regułą.

5. Po utworzeniu co najmniej jednej reguły naciśnij pozycję **Zapisz**. Ta akcja powoduje utworzenie konfiguracji aparatu reguł.

6. Po utworzeniu co najmniej jednej konfiguracji Skojarz konfigurację aparatu reguł z regułą trasy. Podczas gdy jedna konfiguracja może być stosowana do wielu reguł trasy, reguła trasy może zawierać tylko jedną konfigurację aparatu reguł. Aby utworzyć skojarzenie, przejdź do **Front Door designer**  >  **reguły tras**projektanta drzwi. Wybierz regułę trasy, do której chcesz dodać konfigurację aparatu reguł, przejdź do pozycji **Route details**  >  **Konfiguracja aparatu reguły**szczegółów trasy, a następnie wybierz konfigurację, którą chcesz skojarzyć.

    ![Znajdź aparat reguł](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurowanie aparatu reguł w interfejsie wiersza polecenia platformy Azure

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Dodaj rozszerzenie "Front-drzwiczks":-AZ Extension Add--Name-drzwi. Następnie zaloguj się i przejdź do subskrypcji AZ Account Set--Subscription <name_or_Id>.

2. Zacznij od utworzenia aparatu reguł — ten przykład pokazuje jedną regułę z jedną akcją opartą na nagłówkach i jeden warunek dopasowywania. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

3. Wyświetl listę wszystkich reguł. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

4. Dodaj akcję przesłonięcia trasy przesyłania dalej. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

5. Wyświetl listę wszystkich akcji w regule. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

6. Połącz konfigurację aparatu reguł z regułą routingu.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

7. Odłącz aparat reguł. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

Aby uzyskać więcej informacji, należy zapoznać się z pełną listą poleceń aparatu AFD [rules.](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)   

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [aparacie reguł AFD](front-door-rules-engine.md). 
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
- Więcej informacji znajduje się w [dokumentacji interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)aparatu AFD. 
- Zapoznaj się z informacjami w temacie AFD Rules Engine [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
