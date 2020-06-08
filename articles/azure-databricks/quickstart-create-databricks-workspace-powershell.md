---
title: Szybki Start — Tworzenie obszaru roboczego Azure Databricks przy użyciu programu PowerShell
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia obszaru roboczego Azure Databricks przy użyciu programu PowerShell.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485730"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Szybki Start: Tworzenie obszaru roboczego Azure Databricks przy użyciu programu PowerShell

W tym przewodniku szybki start opisano, jak utworzyć obszar roboczy Azure Databricks przy użyciu programu PowerShell. Program PowerShell umożliwia interakcyjne tworzenie zasobów platformy Azure i zarządzanie nimi w skryptach.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Gdy moduł programu PowerShell AZ. datakosteks jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.Databricks -AllowPrerelease` .
> Gdy moduł PowerShell AZ. datakostks jest ogólnie dostępny, stał się częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli używasz usługi Azure Databricks po raz pierwszy, musisz zarejestrować dostawcę zasobów **Microsoft. datacegły** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określony identyfikator subskrypcji za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o nazwie Moja **zasobów** w regionie **zachodnie stany USA 2** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy Azure Databricks przy użyciu programu PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Podaj następujące wartości:

|       **Właściwość**       |                                                                                **Opis**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Nazwa                     | Podaj nazwę obszaru roboczego usługi Databricks.                                                                                                                                   |
| ResourceGroupName        | Określ nazwę istniejącej grupy zasobów                                                                                                                                        |
| Lokalizacja                 | Wybierz pozycję **Zachodnie stany USA 2**. W przypadku innych dostępnych regionów zobacz [usługi platformy Azure dostępne według regionów](https://azure.microsoft.com/regions/services/)                                     |
| ManagedResourceGroupName | Określ, czy chcesz utworzyć nową zarządzaną grupę zasobów, czy użyć istniejącej.                                                                                        |
| SKU                      | Wybierz warstwę **standardowa**, **Premium**lub **wersja próbna**. Aby uzyskać więcej informacji na temat tych warstw, zobacz [Cennik usługi datacegły](https://azure.microsoft.com/pricing/details/databricks/) |

Tworzenie obszaru roboczego trwa kilka minut. Po zakończeniu tego procesu konto użytkownika zostanie automatycznie dodane jako administrator w obszarze roboczym.

W przypadku niepowodzenia wdrożenia obszaru roboczego jest on nadal tworzony w stanie niepowodzenia. Usuń niepowodzenie obszaru roboczego i Utwórz nowy obszar roboczy, który rozwiązuje błędy wdrożenia. Po usunięciu obszaru roboczego niepowodzenie zostanie również usunięta zarządzana Grupa zasobów i wszystkie pomyślnie wdrożone zasoby.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Określanie stanu aprowizacji obszaru roboczego elementów datakostki

Aby określić, czy pomyślnie zainicjowano Inicjowanie obsługi obszaru roboczego dla obiektów datakostki, możesz użyć `Get-AzDatabricksWorkspace` polecenia cmdlet.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zasoby utworzone w tym przewodniku Szybki Start nie są potrzebne do korzystania z innego przewodnika Szybki start lub samouczka, możesz je usunąć, uruchamiając Poniższy przykład.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego przewodnika Szybki Start istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Aby usunąć tylko serwer utworzony w tym przewodniku Szybki Start bez usuwania grupy zasobów, użyj `Remove-AzDatabricksWorkspace` polecenia cmdlet.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie klastra Spark w usłudze Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
