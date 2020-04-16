---
title: Konfigurowanie zapór i sieci wirtualnych usługi Azure Key Vault — Usługa Azure Key Vault
description: Instrukcje krok po kroku dotyczące konfigurowania zapór i sieci wirtualnych usługi Key Vault
services: key-vault
author: amitbapat
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 93fb718c28d015162d3c43d0936bbf4de9cad2fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422578"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór usługi Azure Key Vault i sieci wirtualnych

Ten artykuł zawiera instrukcje krok po kroku, aby skonfigurować zapory usługi Azure Key Vault i sieci wirtualne, aby ograniczyć dostęp do magazynu kluczy. [Punkty końcowe usługi sieci wirtualnej dla usługi Key Vault](overview-vnet-service-endpoints.md)) umożliwiają ograniczenie dostępu do określonej sieci wirtualnej i zestawu zakresów adresów IPv4 (protokół internetowy w wersji 4).

> [!IMPORTANT]
> Po wprowadzeniu reguł zapory użytkownicy mogą wykonywać operacje [na płaszczyznach danych](secure-your-key-vault.md#data-plane-access-control) usługi Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do usługi Key Vault z witryny Azure portal. Mimo że użytkownicy mogą przeglądać do magazynu kluczy z witryny Azure portal, mogą nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli ich komputer kliencki nie znajduje się na liście dozwolonych. Dotyczy to również selektora magazynu kluczy przez inne usługi platformy Azure. Użytkownicy mogą być w stanie zobaczyć listę magazynów kluczy, ale nie klucze listy, jeśli reguły zapory uniemożliwiają ich komputera klienckiego.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Poniżej opisano, jak skonfigurować zapory usługi Key Vault i sieci wirtualne przy użyciu witryny Azure portal:

1. Przejdź do magazynu kluczy, który chcesz zabezpieczyć.
2. Wybierz **pozycję Sieć**, a następnie wybierz kartę **Zapory i sieci wirtualne.**
3. W obszarze **Zezwalaj na dostęp z**wybierz pozycję **Wybrane sieci**.
4. Aby dodać istniejące sieci wirtualne do zapór i reguł sieci wirtualnej, wybierz **+ Dodaj istniejące sieci wirtualne**.
5. W nowym bloku, który zostanie otwarty, wybierz subskrypcję, sieci wirtualne i podsieci, które chcesz zezwolić na dostęp do tego magazynu kluczy. Jeśli wybrane sieci wirtualne i podsieci nie mają włączonych punktów końcowych usługi, upewnij się, że chcesz włączyć punkty końcowe usługi, a następnie wybierz pozycję **Włącz**. Może upłynąć do 15 minut.
6. W obszarze **Sieci IP**dodaj zakresy adresów IPv4, wpisując zakresy adresów IPv4 w [notacji CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) lub poszczególnych adresach IP.
7. Wybierz pozycję **Zapisz**.

Można również dodać nowe sieci wirtualne i podsieci, a następnie włączyć punkty końcowe usługi dla nowo utworzonych sieci wirtualnych i podsieci, wybierając **+ Dodaj nową sieć wirtualną**. Następnie postępuj zgodnie z instrukcjami.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure 

Oto jak skonfigurować zapory usługi Key Vault i sieci wirtualne przy użyciu interfejsu wiersza polecenia platformy Azure

1. [Zainstaluj platformę Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i [zaloguj się](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Lista dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Włącz punkt końcowy usługi dla usługi Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Dodaj regułę sieci dla sieci wirtualnej i podsieci.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Dodaj zakres adresów IP, z którego można zezwolić na ruch.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być `bypass` dostępny `AzureServices`dla zaufanych usług, ustaw na .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Włącz reguły sieciowe, ustawiając `Deny`domyślną akcję na .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Oto jak skonfigurować zapory usługi Key Vault i sieci wirtualne przy użyciu programu PowerShell:

1. Zainstaluj najnowszą usługę [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)i [zaloguj się](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Lista dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Włącz punkt końcowy usługi dla usługi Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Dodaj regułę sieci dla sieci wirtualnej i podsieci.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Dodaj zakres adresów IP, z którego można zezwolić na ruch.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być `bypass` dostępny `AzureServices`dla zaufanych usług, ustaw na .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Włącz reguły sieciowe, ustawiając `Deny`domyślną akcję na .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Dokumentacja

* Polecenia interfejsu wiersza polecenia platformy Azure: [reguła sieci az keyvault](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Polecenia cmdlet programu Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Następne kroki

* [Punkty końcowe usługi sieci wirtualnej dla usługi Key Vault](overview-vnet-service-endpoints.md))
* [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md))
