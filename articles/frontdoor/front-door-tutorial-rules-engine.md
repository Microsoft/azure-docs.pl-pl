---
title: 'Samouczek: Konfigurowanie aparatu reguł — drzwi platformy Azure'
description: Ten artykuł zawiera samouczek dotyczący konfigurowania aparatu reguł zarówno w Azure Portal, jak i w interfejsie wiersza polecenia.
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
ms.openlocfilehash: 3366f1a16777ecf46951296e4a1c2c28aed75feb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90031929"
---
# <a name="tutorial-configure-your-rules-engine"></a>Samouczek: Konfigurowanie aparatu reguł

W tym samouczku pokazano, jak utworzyć konfigurację aparatu reguł i pierwszą regułę zarówno w Azure Portal, jak i w interfejsie wiersza polecenia. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Skonfiguruj aparat reguł przy użyciu portalu.
> - Konfigurowanie aparatu reguł przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

* Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz temat [Quickstart: Create a Front Door for a highly available global web application](quickstart-create-front-door.md) (Szybki start: tworzenie usługi Front Door na potrzeby łatwo dostępnej globalnej aplikacji internetowej).

## <a name="configure-rules-engine-in-azure-portal"></a>Skonfiguruj aparat reguł w Azure Portal
1. W obszarze zasób z drzwiami wstępnymi przejdź do pozycji **Ustawienia** i wybierz pozycję **Konfiguracja aparatu reguł**. Kliknij przycisk **Dodaj**, nadaj konfiguracji nazwę i Rozpocznij tworzenie pierwszej konfiguracji aparatu reguł.

    ![Menu Ustawienia drzwi przednich](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Kliknij pozycję **Dodaj regułę** , aby utworzyć pierwszą regułę. Następnie klikając pozycję **Dodaj warunek** lub **Dodaj akcję** , można zdefiniować regułę.
    
    > [!NOTE]
    >- Aby usunąć warunek lub akcję z reguły, użyj kosza po prawej stronie określonego warunku lub akcji.
    > - Aby utworzyć regułę, która ma zastosowanie do całego ruchu przychodzącego, nie określaj żadnych warunków.
    > - Aby zatrzymać ocenianie reguł po spełnieniu pierwszego warunku dopasowania, zaznacz pole **Zatrzymaj szacowanie pozostałych reguł**. Jeśli ta opcja jest zaznaczona i spełnione są wszystkie warunki dopasowania określonej reguły, pozostałe reguły w konfiguracji nie zostaną wykonane.  

    ![Konfiguracja aparatu reguł](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Określ priorytet reguł w konfiguracji za pomocą przycisków Przenieś w górę, Przenieś w dół i Przenieś do góry. Priorytet znajduje się w kolejności rosnącej, co oznacza, że reguła pierwsza na liście jest najważniejszym regułą.

1. Po utworzeniu co najmniej jednej reguły naciśnij pozycję **Zapisz**. Ta akcja powoduje utworzenie konfiguracji aparatu reguł.

1. Po utworzeniu co najmniej jednej konfiguracji Skojarz konfigurację aparatu reguł z regułą trasy. Podczas gdy jedna konfiguracja może być stosowana do wielu reguł trasy, reguła trasy może zawierać tylko jedną konfigurację aparatu reguł. Aby utworzyć skojarzenie, przejdź do **Front Door designer**  >  **reguły tras**projektanta drzwi. Wybierz regułę trasy, do której chcesz dodać konfigurację aparatu reguł, przejdź do pozycji **Route details**  >  **Konfiguracja aparatu reguły**szczegółów trasy, a następnie wybierz konfigurację, którą chcesz skojarzyć.

    ![Konfigurowanie do reguły routingu](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurowanie aparatu reguł w interfejsie wiersza polecenia platformy Azure

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Dodaj rozszerzenie "Front-drzwiczks":-AZ Extension Add--Name-drzwi. Następnie zaloguj się i przejdź do subskrypcji AZ Account Set--Subscription <name_or_Id>.

1. Zacznij od utworzenia aparatu reguł — ten przykład pokazuje jedną regułę z jedną akcją opartą na nagłówkach i jeden warunek dopasowywania. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Wyświetl listę wszystkich reguł. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Dodaj akcję przesłonięcia trasy przesyłania dalej. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Wyświetl listę wszystkich akcji w regule. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Połącz konfigurację aparatu reguł z regułą routingu.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Odłącz aparat reguł. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Aby uzyskać więcej informacji, należy zapoznać się z pełną listą poleceń aparatu AFD [rules.](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest&preserve-view=true)   

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach zostały skonfigurowane i skojarzone z konfiguracją aparatu reguł dla reguł routingu. Jeśli nie chcesz już, aby konfiguracja aparatu reguł była skojarzona z przednim Drzwiem, możesz usunąć konfigurację, wykonując następujące czynności:

1. Usuń skojarzenie wszystkich reguł routingu z konfiguracją aparatu reguł, klikając trzy kropki obok nazwy aparatu reguł.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Kojarzenie reguł routingu":::

1. Usuń zaznaczenie wszystkich reguł routingu, do których jest skojarzona ta konfiguracja aparatu reguły, a następnie kliknij przycisk Zapisz.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Kojarzenie reguł routingu":::

1. Teraz można usunąć konfigurację aparatu reguł z drzwi z przodu.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Kojarzenie reguł routingu":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie konfiguracji aparatu reguł
* Skojarz konfigurację z regułami routingu na początku drzwi.

Aby dowiedzieć się, jak dodać nagłówki zabezpieczeń z aparatem reguł, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Nagłówki zabezpieczeń z aparatem reguł](front-door-security-headers.md)
