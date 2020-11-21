---
title: Użyj niestandardowego serwera DNS
titleSuffix: Azure Machine Learning
description: Jak skonfigurować niestandardowy serwer DNS do pracy z obszarem roboczym Azure Machine Learning i prywatnym punktem końcowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c67dcbbe2ca6dea533260f59831556c4338374ba
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012999"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Jak używać obszaru roboczego z niestandardowym serwerem DNS

W przypadku korzystania z obszaru roboczego Azure Machine Learning z prywatnym punktem końcowym istnieje [kilka sposobów obsługi rozpoznawania nazw DNS](../private-link/private-endpoint-dns.md). Domyślnie platforma Azure automatycznie obsługuje rozpoznawanie nazw dla obszaru roboczego i prywatnego punktu końcowego. Jeśli zamiast tego _używasz własnego niestandardowego serwera DNS_ _, musisz ręcznie utworzyć wpisy DNS dla obszaru roboczego.

> [!IMPORTANT]
> W tym artykule opisano, jak znaleźć w pełni kwalifikowaną nazwę domeny (FQDN) i adresy IP dla tych wpisów, które nie zawierają informacji na temat konfigurowania rekordów DNS dla tych elementów. Zapoznaj się z dokumentacją oprogramowania DNS, aby uzyskać informacje na temat dodawania rekordów.

## <a name="prerequisites"></a>Wymagania wstępne

- Virtual Network platformy Azure, która używa [własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Obszar roboczy Azure Machine Learning z prywatnym punktem końcowym. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- Znajomość korzystania z [izolacji sieci podczas szkoleń & wnioskowania](./how-to-network-security-overview.md).

- Znajomość [konfiguracji strefy DNS prywatnego punktu końcowego platformy Azure](../private-link/private-endpoint-dns.md)

- Opcjonalnie [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Znajdowanie adresów IP

Poniższa lista zawiera w pełni kwalifikowane nazwy domen (FQDN) używane przez obszar roboczy i prywatny punkt końcowy:

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* W przypadku tworzenia wystąpienia obliczeniowego należy również dodać wpis `<instance-name>.<region>.instances.azureml.ms` z prywatnym adresem IP prywatnego punktu końcowego obszaru roboczego.

    > [!NOTE]
    > Wystąpienia obliczeniowe są dostępne tylko w sieci wirtualnej.

Aby znaleźć wewnętrzne adresy IP dla nazw FQDN w sieci wirtualnej, należy użyć jednej z następujących metod:

> [!NOTE]
> W pełni kwalifikowane nazwy domen i adresy IP będą się różnić w zależności od konfiguracji. Na przykład wartość identyfikatora GUID w nazwie domeny będzie określona dla Twojego obszaru roboczego.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)wybierz __obszar roboczy__ Azure Machine Learning.
1. W sekcji __Ustawienia__ wybierz pozycję __połączenia prywatne punktów końcowych__.
1. Wybierz link w wyświetlanej kolumnie __prywatny punkt końcowy__ .
1. Lista w pełni kwalifikowanych nazw domen (FQDN) i adresów IP dla prywatnego punktu końcowego obszaru roboczego znajduje się u dołu strony.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lista nazw FQDN w portalu":::

---

Informacje zwracane przez wszystkie metody są takie same; Lista nazw FQDN i prywatnych adresów IP zasobów.

| Nazwa FQDN | Adres IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Niektóre nazwy FQDN nie są wyświetlane na liście przez prywatny punkt końcowy, ale są wymagane przez obszar roboczy. Te nazwy FQDN są wymienione w poniższej tabeli i również muszą zostać dodane do serwera DNS:
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Jeśli masz wystąpienie obliczeniowe, użyj `<instance-name>.<region>.instances.azureml.ms` , gdzie `<instance-name>` jest nazwą wystąpienia obliczeniowego. Użyj prywatnego adresu IP prywatnego punktu końcowego obszaru roboczego. Uwaga do wystąpienia obliczeniowego można uzyskać dostęp tylko z poziomu sieci wirtualnej.
>
> Dla wszystkich tych adresów IP Użyj tego samego adresu jako `*.api.azureml.ms` wpisów zwróconych z poprzednich kroków.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat używania Azure Machine Learning z siecią wirtualną, zobacz [Omówienie sieci wirtualnej](how-to-network-security-overview.md).