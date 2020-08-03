---
title: Tworzenie wewnętrznego Load Balancer — szablon platformy Azure
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak utworzyć wewnętrzny moduł równoważenia obciążenia przy użyciu szablonu w usłudze Resource Manager
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: fc64f4b3d338704568cb70a542a82fe58d08d4d9
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502311"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia przy użyciu szablonu

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [Program PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Szablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Wdrażanie szablonu przy użyciu metody „kliknij, aby wdrożyć”

Przykładowy szablon dostępny w repozytorium publicznym korzysta z pliku parametrów zawierającego wartości domyślne używane do generowania scenariusza opisanego powyżej. Aby wdrożyć ten szablon przy użyciu metody „kliknij, aby wdrożyć”, kliknij [ten link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), kliknij przycisk **Deploy to Azure** (Wdróż na platformie Azure), w razie potrzeby zastąp wartości domyślne parametrów i postępuj zgodnie z instrukcjami podanymi w portalu.

## <a name="deploy-the-template-by-using-powershell"></a>Wdrażanie szablonu przy użyciu programu PowerShell

Aby wdrożyć pobrany szablon przy użyciu programu PowerShell, wykonaj poniższe kroki.

1. Jeśli jeszcze nie korzystasz z Azure PowerShell, zobacz [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/) i postępować zgodnie z instrukcjami, aby zalogować się do platformy Azure i wybrać subskrypcję.
2. Pobierz plik parametrów na lokalny dysk twardy.
3. Edytuj plik i zapisz go.
4. Uruchom polecenie cmdlet **New-AzResourceGroupDeployment** , aby utworzyć grupę zasobów przy użyciu szablonu.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Wdrażanie szablonu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć szablon przy użyciu interfejsu wiersza polecenia platformy Azure, wykonaj poniższe kroki.

1. Jeśli nigdy nie korzystasz z interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md) , a następnie postępuj zgodnie z instrukcjami w punkcie, w którym wybierasz konto i subskrypcję platformy Azure.
2. Przejdź do [https://shell.azure.com](https://shell.azure.com) okna, aby otworzyć Cloud Shell w przeglądarce. Uruchom polecenie **azure config mode**, aby włączyć tryb Resource Manager, jak pokazano poniżej.

    ```console
    azure config mode arm
    ```

    Oto oczekiwane dane wyjściowe po wprowadzeniu powyższego polecenia:

    ```output
    info:    New mode is arm
    ```

3. Otwórz plik parametrów, wybierz jego zawartość i zapisz ją w pliku na komputerze. W tym przykładzie zapisaliśmy plik parametrów w pliku *parameters.json*.
4. Uruchom polecenie **azure group deployment create**, aby wdrożyć nowy wewnętrzny moduł równoważenia obciążenia przy użyciu plików parametrów i szablonu pobranych i zmodyfikowanych w powyższych krokach. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>Następne kroki

[Configure a load balancer distribution mode using source IP affinity](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia przy użyciu koligacji źródłowych adresów IP)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)

Aby poznać składnię i właściwości JSON modułu równoważenia obciążenia w szablonie, zobacz [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
