---
title: Skonfiguruj limit czasu bezczynności resetowania protokołu TCP modułu równoważenia obciążenia
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak skonfigurować Azure Load Balancer przekroczenie limitu czasu bezczynności protokołu TCP i resetowanie.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747206"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Skonfiguruj limit czasu resetowania i bezczynności protokołu TCP dla Azure Load Balancer

Azure Load Balancer ma następujący zakres limitów czasu bezczynności:

* 4 minuty do 100 minut dla reguł ruchu wychodzącego
* 4 minuty do 30 minut dla reguł Load Balancer i reguł NAT dla ruchu przychodzącego

Wartość domyślna to 4 minuty. Jeśli okres braku aktywności jest dłuższy niż wartość limitu czasu, nie ma gwarancji, że sesja TCP lub HTTP jest utrzymywana między klientem a usługą. 

W poniższych sekcjach opisano, jak zmienić limit czasu bezczynności i ustawienia resetowania protokołu TCP dla zasobów modułu równoważenia obciążenia.

## <a name="set-tcp-reset-and-idle-timeout"></a>Ustaw limit czasu resetowania i bezczynności protokołu TCP
---
# <a name="portal"></a>[**Portal**](#tab/tcp-reset-idle-portal)

Aby ustawić limit czasu bezczynności i Resetowanie protokołu TCP dla modułu równoważenia obciążenia, Edytuj regułę równoważenia obciążenia. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu po lewej stronie wybierz pozycję **grupy zasobów**.

3. Wybierz grupę zasobów dla modułu równoważenia obciążenia. W tym przykładzie grupa zasobów jest nazywana grupą **zasobów.**

4. Wybierz swój moduł równoważenia obciążenia. W tym przykładzie moduł równoważenia obciążenia nosi nazwę **myLoadBalancer**.

5. W obszarze **Ustawienia** wybierz pozycję **reguły równoważenia obciążenia**.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Edytuj reguły modułu równoważenia obciążenia." border="true":::

6. Wybierz regułę równoważenia obciążenia. W tym przykładzie reguła równoważenia obciążenia ma nazwę **myLBrule**.

7. W regule równoważenia obciążenia przesuń suwak w polu **limit czasu bezczynności (minuty)** do wartości limitu czasu.  

8. W obszarze **Resetowanie protokołu TCP** wybierz pozycję **włączone**.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Ustaw limit czasu bezczynności i Resetowanie protokołu TCP." border="true":::

9. Wybierz pozycję **Zapisz**.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Aby ustawić limit czasu bezczynności i Resetowanie protokołu TCP, ustaw wartości w następujących parametrach reguły równoważenia obciążenia z [poleceniem Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Zastąp następujące przykłady wartościami z zasobów:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Interfejs wiersza polecenia platformy Azure**](#tab/tcp-reset-idle-cli)

Aby ustawić limit czasu bezczynności i Resetowanie protokołu TCP, użyj następujących parametrów dla [aktualizacji dla reguły AZ Network lb](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--Idle-Timeout**
* **--Enable-TCP-Reset**

Sprawdź poprawność środowiska przed rozpoczęciem:

* Zaloguj się do Azure Portal i sprawdź, czy Twoja subskrypcja jest aktywna, uruchamiając `az login` .
* Sprawdź wersję interfejsu wiersza polecenia platformy Azure w terminalu lub oknie poleceń, uruchamiając polecenie `az --version` . Aby uzyskać najnowszą wersję, zapoznaj się z [najnowszymi informacjami o wersji](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Jeśli nie masz najnowszej wersji, zaktualizuj instalację, postępując zgodnie z [instrukcją instalacji systemu operacyjnego lub platformy](/cli/azure/install-azure-cli).

Zastąp następujące przykłady wartościami z zasobów:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat limitu czasu bezczynności protokołu TCP i resetowania, zobacz [Load Balancer Resetowanie protokołu TCP i limit czasu bezczynności](load-balancer-tcp-reset.md)

Aby uzyskać więcej informacji na temat konfigurowania trybu dystrybucji modułu równoważenia obciążenia, zobacz [Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia](load-balancer-distribution-mode.md).
