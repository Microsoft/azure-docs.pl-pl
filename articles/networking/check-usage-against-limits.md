---
title: Sprawdzanie użycia zasobów platformy Azure pod względem limitów | Microsoft Docs
description: Dowiedz się, jak sprawdzić użycie zasobów platformy Azure względem limitów subskrypcji platformy Azure.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: d629e65106145a4af364cd9dd489250c8910c25d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778575"
---
# <a name="check-resource-usage-against-limits"></a>Porównywanie użycia zasobów z limitami

Z tego artykułu dowiesz się, jak wyświetlić liczbę poszczególnych typów zasobów sieciowych wdrożonych w ramach subskrypcji oraz [limity](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) subskrypcji. Możliwość wyświetlania użycia zasobów z limitami jest przydatna do śledzenia bieżącego użycia i planowania do użycia w przyszłości. Do śledzenia użycia możesz użyć witryny [Azure Portal,](#azure-portal) [programu PowerShell](#powershell)lub [interfejsu wiersza polecenia](#azure-cli) platformy Azure.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do witryny Azure [Portal.](https://portal.azure.com)
2. W lewym górnym rogu okna usługi Azure Portal wybierz **pozycję Wszystkie usługi.**
3. Wprowadź *wartość Subskrypcje* w **polu** Filtr. Gdy pozycja **Subskrypcje** pojawi się w wynikach wyszukiwania, wybierz ją.
4. Wybierz nazwę subskrypcji, dla której chcesz wyświetlić informacje o użyciu.
5. W **obszarze USTAWIENIA** wybierz pozycję Użycie i limit **przydziału.**
6. Można wybrać jedną z następujących opcji:
   - **Typy** zasobów: możesz wybrać wszystkie typy zasobów lub wybrać określone typy zasobów, które chcesz wyświetlić.
   - **Dostawcy:** możesz wybrać wszystkich dostawców zasobów lub wybrać pozycję **Obliczenia,** **Sieć** lub **Magazyn.**
   - **Lokalizacje:** możesz wybrać wszystkie lokalizacje platformy Azure lub wybrać określone lokalizacje.
   - Możesz wybrać opcję pokazywania wszystkich zasobów lub tylko tych zasobów, w których wdrożono co najmniej jeden.

     Przykład na poniższej ilustracji przedstawia wszystkie zasoby sieciowe z co najmniej jednym zasobem wdrożonym w regionach Wschodnie usa:

       ![Wyświetlanie danych użycia](./media/check-usage-against-limits/view-usage.png)

     Kolumny można sortować, wybierając nagłówek kolumny. Pokazane limity to limity subskrypcji. Jeśli chcesz zwiększyć limit domyślny, wybierz pozycję **Zwiększ** żądanie, a następnie ukończ i prześlij wniosek o pomoc techniczną. Wszystkie zasoby mają maksymalny limit wymieniony w tesłudze Limity [platformy](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)Azure. Jeśli bieżący limit jest już w maksymalnej liczbie, nie można go zwiększyć.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [Azure Cloud Shell](https://shell.azure.com/powershell), lub uruchamiając program PowerShell z komputera. Ta Azure Cloud Shell jest bezpłatną interaktywną powłoką. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli uruchamiasz program PowerShell z komputera, potrzebujesz modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz również uruchomić program , aby `Login-AzAccount` zalogować się na platformie Azure.

Wyświetl użycie względem limitów za pomocą [get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). Poniższy przykład pobiera użycie zasobów, w których co najmniej jeden zasób jest wdrożony w lokalizacji Wschodnie usa:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Otrzymasz dane wyjściowe w formacie takim samym jak w następujących przykładowych danych wyjściowych:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Jeśli do wykonywania zadań w tym artykule używasz poleceń interfejsu wiersza polecenia platformy Azure, uruchom polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić polecenia , aby `az login` zalogować się na platformie Azure.

Wyświetl użycie względem limitów za pomocą [az network list-usages](/cli/azure/network#az_network_list_usages). Poniższy przykład pobiera użycie zasobów w lokalizacji Wschodnie usa:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Otrzymasz dane wyjściowe w formacie takim samym jak w następujących przykładowych danych wyjściowych:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```