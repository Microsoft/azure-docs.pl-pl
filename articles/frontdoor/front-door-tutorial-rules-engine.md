---
title: 'Samouczek: konfigurowanie aparatu reguł — Azure Front Door'
description: Ten artykuł zawiera samouczek dotyczący sposobu konfigurowania aparatu reguł w interfejsie wiersza Azure Portal interfejsie wiersza polecenia.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: e64f27480126fdf444f8dd72abc2d66c78b30407
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868811"
---
# <a name="tutorial-configure-your-rules-engine"></a>Samouczek: konfigurowanie aparatu reguł

W tym samouczku pokazano, jak utworzyć konfigurację aparatu reguł i pierwszą regułę w interfejsie Azure Portal wiersza polecenia. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Skonfiguruj aparat reguł przy użyciu portalu.
> - Konfigurowanie aparatu reguł przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

* Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz temat [Quickstart: Create a Front Door for a highly available global web application](quickstart-create-front-door.md) (Szybki start: tworzenie usługi Front Door na potrzeby łatwo dostępnej globalnej aplikacji internetowej).

## <a name="configure-rules-engine-in-azure-portal"></a>Konfigurowanie aparatu reguł w programie Azure Portal
1. W ramach zasobu usługi Front Door przejdź do **ustawień** i wybierz pozycję Konfiguracja **aparatu reguł.** Kliknij **pozycję** Dodaj , nadaj nazwę konfiguracji i rozpocznij tworzenie pierwszej konfiguracji aparatu reguł.

    ![Front Door menu ustawień](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Kliknij **pozycję Dodaj regułę,** aby utworzyć pierwszą regułę. Następnie, klikając pozycję **Dodaj warunek** lub **Dodaj akcję,** możesz zdefiniować regułę.
    
    > [!NOTE]
    >- Aby usunąć warunek lub akcję z reguły, użyj kosza po prawej stronie określonego warunku lub akcji.
    > - Aby utworzyć regułę, która ma zastosowanie do całego ruchu przychodzącego, nie należy określać żadnych warunków.
    > - Aby zatrzymać ocenianie reguł po pierwszym spełnionym warunku dopasowania, zaznacz pole **zatrzymać ocenę pozostałej reguły**. Jeśli to pole zostanie zaznaczone i zostaną spełnione wszystkie warunki dopasowania określonej reguły, pozostałe reguły w konfiguracji nie zostaną wykonane.  

    ![Konfiguracja aparatu reguł](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Określ priorytet reguł w konfiguracji przy użyciu przycisków Przenieś w górę, Przenieś w dół i Przenieś do góry. Priorytet jest w kolejności rosnącej, co oznacza, że reguła pierwsza na liście jest najważniejszą regułą.

1. Po utworzeniu co najmniej jednej reguły naciśnij przycisk **Zapisz**. Ta akcja powoduje utworzenie konfiguracji aparatu reguł.

1. Po utworzeniu co najmniej jednej konfiguracji należy skojarzyć konfigurację aparatu reguł z regułą trasy. Chociaż pojedynczą konfigurację można zastosować do wielu reguł tras, reguła trasy może zawierać tylko jedną konfigurację aparatu reguł. Aby skojarzenia, przejdź do **swojego Front Door trasy**  >  **projektanta**. Wybierz regułę trasy, do których chcesz dodać konfigurację aparatu reguł, przejdź do opcji Szczegóły trasy Konfiguracja aparatu reguł i wybierz konfigurację, do skojarzenia.  >  

    ![Konfigurowanie reguły rozsyłania](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurowanie aparatu reguł w interfejsie wiersza polecenia platformy Azure

1. Jeśli nie zostało to jeszcze zainstalowane, zainstaluj interfejs wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli) Dodaj rozszerzenie "front-door": az extension add --name front-door. Następnie zaloguj się i przejdź do subskrypcji az account set --subscription <name_or_Id>.

1. Rozpocznij od utworzenia aparatu reguł — w tym przykładzie przedstawiono jedną regułę z jedną akcją opartą na nagłówku i jednym warunkiem dopasowania. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Lista wszystkich reguł. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Dodaj akcję przesłonięcia trasy przekazywania. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Lista wszystkich akcji w regułę. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Łączenie konfiguracji aparatu reguł z regułą routingu.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Odłącz aparat reguł. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Aby uzyskać więcej informacji, pełną listę poleceń aparatu reguł AFD można [znaleźć tutaj.](/cli/azure/network/front-door/rules-engine)   

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach skonfigurowano i skojarzoną konfigurację aparatu reguł z regułami routingu. Jeśli nie chcesz już skojarzyć konfiguracji aparatu reguł Front Door, możesz usunąć konfigurację, wykonując następujące czynności:

1. Skojarz wszystkie reguły routingu z konfiguracją aparatu reguł, klikając trzy kropki obok nazwy aparatu reguł.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Kojarzenie reguł routingu":::

1. Usuń zaznaczenie wszystkich reguł rozsyłania, z których jest skojarzona konfiguracja aparatu reguł, a następnie kliknij przycisk Zapisz.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Skojarzenie reguły routingu":::

1. Teraz możesz usunąć konfigurację aparatu reguł z Front Door.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Usuwanie konfiguracji aparatu reguł":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie konfiguracji aparatu reguł
* Kojarzenie konfiguracji z regułami Front Door routingu.

Aby dowiedzieć się, jak dodać nagłówki zabezpieczeń za pomocą aparatu reguł, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Nagłówki zabezpieczeń z aparatem reguł](front-door-security-headers.md)