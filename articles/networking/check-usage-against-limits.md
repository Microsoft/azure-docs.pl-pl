---
title: Sprawdź użycie zasobów platformy Azure pod kątem limitów | Microsoft Docs
description: Dowiedz się, jak sprawdzić użycie zasobów platformy Azure pod kątem limitów subskrypcji platformy Azure.
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
ms.openlocfilehash: 5c53eb65f31e32d3edebcbf31d48d166f5464a92
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233359"
---
# <a name="check-resource-usage-against-limits"></a>Porównywanie użycia zasobów z limitami

W tym artykule dowiesz się, jak wyświetlić liczbę wszystkich typów zasobów sieciowych wdrożonych w ramach subskrypcji i jakie są [limity subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) . Możliwość wyświetlania użycia zasobów na podstawie limitów jest przydatna do śledzenia bieżącego użycia i planowania do użycia w przyszłości. Do śledzenia użycia można użyć witryny [Azure Portal](#azure-portal), [programu PowerShell](#powershell)lub [interfejsu wiersza polecenia platformy Azure](#azure-cli) .

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com).
2. W lewym górnym rogu Azure Portal wybierz pozycję **wszystkie usługi**.
3. Wprowadź *subskrypcje* w polu **Filtr** . Gdy pozycja **Subskrypcje** pojawi się w wynikach wyszukiwania, wybierz ją.
4. Wybierz nazwę subskrypcji, dla której chcesz wyświetlić informacje o użyciu.
5. W obszarze **Ustawienia** wybierz pozycję **użycie + przydział**.
6. Można wybrać jedną z następujących opcji:
   - **Typy zasobów**: możesz wybrać opcję Wszystkie typy zasobów lub wybrać konkretne typy zasobów, które chcesz wyświetlić.
   - **Dostawcy**: wybierz pozycję Wszyscy dostawcy zasobów lub wybierz pozycję **obliczenia**, **Sieć** lub **Magazyn**.
   - **Lokalizacje**: możesz wybrać wszystkie lokalizacje platformy Azure lub wybrać określone lokalizacje.
   - Można wybrać wyświetlanie wszystkich zasobów lub tylko zasobów, w których wdrożono co najmniej jeden z nich.

     Przykład na poniższej ilustracji przedstawia wszystkie zasoby sieciowe z co najmniej jednym zasobem wdrożonym w regionie Wschodnie stany USA:

       ![Wyświetlanie danych użycia](./media/check-usage-against-limits/view-usage.png)

     Kolumny można sortować, wybierając nagłówek kolumny. Podane limity to limity dla Twojej subskrypcji. Jeśli musisz zwiększyć domyślny limit, wybierz pozycję **Powiększ żądania**, a następnie Zakończ i prześlij żądanie pomocy technicznej. Wszystkie zasoby mają maksymalny limit na liście [limitów](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)platformy Azure. Jeśli bieżący limit jest już osiągnięty przez maksymalną liczbę, nie można zwiększyć limitu.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić następujące polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. W przypadku uruchomienia programu PowerShell z komputera potrzebny jest moduł Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` polecenie na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie, `Login-AzAccount` Aby zalogować się do platformy Azure.

Wyświetlanie użycia względem limitów przy użyciu metody [Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). Poniższy przykład pobiera użycie dla zasobów, w których w lokalizacji Wschodnie stany USA wdrożono co najmniej jeden zasób:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Otrzymasz dane wyjściowe sformatowane tak samo jak następujące przykładowe dane wyjściowe:

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

W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby zalogować się do platformy Azure.

Wyświetlanie użycia względem limitów za pomocą [AZ Network list-Usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). Poniższy przykład pobiera użycie zasobów w lokalizacji Wschodnie stany USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Otrzymasz dane wyjściowe sformatowane tak samo jak następujące przykładowe dane wyjściowe:

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