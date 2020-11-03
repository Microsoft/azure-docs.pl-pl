---
title: Konfigurowanie Azure Key Vault zapór i sieci wirtualnych — Azure Key Vault
description: Instrukcje krok po kroku dotyczące konfigurowania zapór Key Vault i sieci wirtualnych
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: d1b1c27fe0136220d5a1851af4a5c24102a37da1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288624"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór Azure Key Vault i sieci wirtualnych

W tym artykule przedstawiono wskazówki dotyczące konfigurowania zapory Azure Key Vault. W tym dokumencie przedstawiono szczegółowe informacje o różnych konfiguracjach zapory Key Vault i przedstawiono instrukcje krok po kroku dotyczące konfigurowania Azure Key Vault do pracy z innymi aplikacjami i usługami platformy Azure.

## <a name="firewall-settings"></a>Ustawienia zapory

Ta sekcja obejmuje różne sposoby konfigurowania zapory Azure Key Vault.

### <a name="key-vault-firewall-disabled-default"></a>Zapora Key Vault wyłączona (wartość domyślna)

Domyślnie podczas tworzenia nowego magazynu kluczy Zapora Azure Key Vault jest wyłączona. Wszystkie aplikacje i usługi platformy Azure mogą uzyskiwać dostęp do magazynu kluczy i wysyłać żądania do magazynu kluczy. Należy pamiętać, że ta konfiguracja nie oznacza, że każdy użytkownik będzie mógł wykonywać operacje w magazynie kluczy. Magazyn kluczy nadal ogranicza do wpisów tajnych, kluczy i certyfikatów przechowywanych w magazynie kluczy, wymagając Azure Active Directory uprawnień do uwierzytelniania i dostępu. Aby bardziej szczegółowo zrozumieć uwierzytelnianie magazynu kluczy, [zobacz dokument podstawowe](./authentication-fundamentals.md)uwierzytelnianie magazynu kluczy.

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault włączona Zapora (tylko zaufane usługi)

Po włączeniu Zapory Key Vault zostanie nadana opcja "Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory". Lista zaufanych usług nie obejmuje każdej pojedynczej usługi platformy Azure. Na przykład usługa Azure DevOps nie znajduje się na liście zaufanych usług. **Nie oznacza to, że usługi, które nie znajdują się na liście zaufanych usług, nie są zaufane ani niezabezpieczone.** Lista zaufanych usług obejmuje usługi, w których firma Microsoft kontroluje cały kod, który jest uruchamiany w usłudze. Ponieważ użytkownicy mogą pisać kod niestandardowy w usługach platformy Azure, takich jak Azure DevOps, firma Microsoft nie udostępnia opcji tworzenia zbiorczego zatwierdzenia dla usługi. Ponadto, tylko ponieważ usługa pojawia się na liście zaufanych usług, nie oznacza to, że jest ona dozwolona dla wszystkich scenariuszy.

Aby ustalić, czy usługa, której próbujesz użyć, znajduje się na liście zaufanych usług, zapoznaj się z [poniższym dokumentem poniżej.](./overview-vnet-service-endpoints.md#trusted-services)

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault włączona Zapora (adresy IPv4 i zakresy — statyczne adresy IP)

Jeśli chcesz autoryzować określoną usługę do uzyskiwania dostępu do magazynu kluczy za pomocą zapory Key Vault, możesz dodać adres IP do listy dozwolonych zapór magazynu kluczy. Ta konfiguracja jest Najlepsza w przypadku usług korzystających ze statycznych adresów IP lub dobrze znanych zakresów.

Aby zezwolić na adres IP lub zakres zasobów platformy Azure, takich jak aplikacja sieci Web lub aplikacja logiki, wykonaj następujące czynności.

1. Logowanie do witryny Azure Portal
1. Wybierz zasób (określone wystąpienie usługi)
1. Kliknij blok "właściwości" w obszarze "Ustawienia"
1. Wyszukaj pole "adres IP".
1. Skopiuj tę wartość lub zakres i wprowadź ją na liście dozwolonych zapór magazynu kluczy.

Aby zezwolić na całą usługę platformy Azure, za pomocą zapory Key Vault, użyj publicznie udokumentowanych adresów IP centrów danych [dla platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Znajdź adresy IP skojarzone z usługą, które chcesz umieścić w wybranym regionie, i Dodaj te adresy IP do zapory magazynu kluczy za pomocą powyższych kroków.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault włączona Zapora (sieci wirtualne — dynamiczne adresy IP)

Jeśli próbujesz zezwolić na zasoby platformy Azure, takie jak maszyna wirtualna, za pomocą magazynu kluczy, możesz nie być w stanie używać statycznych adresów IP i możesz nie zezwalać na dostęp do magazynu kluczy wszystkim adresom IP dla usługi Azure Virtual Machines.

W takim przypadku należy utworzyć zasób w sieci wirtualnej, a następnie zezwolić na ruch z określonej sieci wirtualnej i podsieci w celu uzyskania dostępu do magazynu kluczy. Aby to zrobić, wykonaj następujące czynności.

1. Logowanie do witryny Azure Portal
1. Wybierz magazyn kluczy, który chcesz skonfigurować
1. Wybieranie bloku "Sieć"
1. Wybierz pozycję "+ Dodaj istniejącą sieć wirtualną"
1. Wybierz sieć wirtualną i podsieć, dla której chcesz zezwolić za pomocą zapory magazynu kluczy.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault włączona Zapora (link prywatny)

Aby dowiedzieć się, jak skonfigurować połączenie z linkiem prywatnym w magazynie kluczy, zobacz dokument w [tym miejscu](./private-link-service.md).

> [!IMPORTANT]
> Po zastosowaniu reguł zapory użytkownicy mogą wykonywać Key Vault operacje [płaszczyzny danych](secure-your-key-vault.md#data-plane-access-control) tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do Key Vault z Azure Portal. Mimo że użytkownicy mogą przechodzić do magazynu kluczy z Azure Portal, mogą nie być w stanie wyświetlać kluczy, wpisów tajnych ani certyfikatów, jeśli ich maszyny klienckie nie znajdują się na liście dozwolonych. Ma to również wpływ na wybór Key Vault przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają ich komputerom klienckim.

> [!NOTE]
> Należy pamiętać o następujących ograniczeniach konfiguracji:
> * Dozwolone są maksymalnie 127 reguły sieci wirtualnej i 127. 
> * Reguły sieci IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od **10.** , **172.16-31** i **192,168.** 
> * W tej chwili są obsługiwane tylko adresy IPv4.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Poniżej przedstawiono sposób konfigurowania zapór Key Vault i sieci wirtualnych przy użyciu Azure Portal:

1. Przejdź do magazynu kluczy, który chcesz zabezpieczyć.
2. Wybierz pozycję **Sieć** , a następnie wybierz kartę **zapory i sieci wirtualne** .
3. W obszarze **Zezwalaj na dostęp z** , wybierz opcję **wybrane sieci**.
4. Aby dodać istniejące sieci wirtualne do zapór i reguł sieci wirtualnej, wybierz pozycję **+ Dodaj istniejące sieci wirtualne**.
5. W nowym bloku, który zostanie otwarty, wybierz subskrypcję, sieci wirtualne i podsieci, dla których chcesz zezwolić na dostęp do tego magazynu kluczy. Jeśli wybrane sieci wirtualne i podsieci nie mają włączonych punktów końcowych usługi, potwierdź, że chcesz włączyć punkty końcowe usługi, a następnie wybierz pozycję **Włącz**. Wprowadzenie zmian może potrwać do 15 minut.
6. W obszarze **sieci IP** Dodaj zakresy adresów IPv4, wpisując zakresy adresów IPv4 w [notacji CIDR (bezklasowe Routing między domenami)](https://tools.ietf.org/html/rfc4632) lub pojedynczych adresów IP.
7. Jeśli chcesz zezwolić usłudze Microsoft Trusted Services na ominięcie zapory Key Vault, wybierz pozycję "tak". Aby zapoznać się z pełną listą bieżących Key Vault zaufanych usług, Skorzystaj z następującego linku. [Azure Key Vault zaufanych usług](./overview-vnet-service-endpoints.md#trusted-services)
7. Wybierz pozycję **Zapisz**.

Możesz również dodać nowe sieci wirtualne i podsieci, a następnie włączyć punkty końcowe usługi dla nowo utworzonych sieci wirtualnych i podsieci, wybierając pozycję **+ Dodaj nową sieć wirtualną**. Następnie postępuj zgodnie z monitami.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure 

Poniżej przedstawiono sposób konfigurowania zapór Key Vault i sieci wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Zaloguj się](/cli/azure/authenticate-azure-cli).

2. Wyświetl listę dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Włącz punkt końcowy usługi dla Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Dodaj zakres adresów IP, z którego ma być dozwolony ruch.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być dostępny dla wszystkich zaufanych usług, ustaw opcję `bypass` na `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Włącz reguły sieci, ustawiając domyślną akcję na `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Poniżej przedstawiono sposób konfigurowania zapór Key Vault i sieci wirtualnych przy użyciu programu PowerShell:

1. Zainstaluj najnowszą [Azure PowerShell](/powershell/azure/install-az-ps)i [Zaloguj się](/powershell/azure/authenticate-azureps).

2. Wyświetl listę dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Włącz punkt końcowy usługi dla Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Dodaj regułę sieciową dla sieci wirtualnej i podsieci.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Dodaj zakres adresów IP, z którego ma być dozwolony ruch.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być dostępny dla wszystkich zaufanych usług, ustaw opcję `bypass` na `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Włącz reguły sieci, ustawiając domyślną akcję na `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Odwołania
* Dokumentacja szablonu ARM: [Dokumentacja szablonu Azure Key Vault ARM](/azure/templates/Microsoft.KeyVault/vaults)
* Polecenie interfejsu wiersza polecenia platformy Azure: [AZ datamagazyn Network-Rule](/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Polecenia cmdlet Azure PowerShell: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Następne kroki

* [Punkty końcowe usługi sieci wirtualnej dla Key Vault](overview-vnet-service-endpoints.md)
* [Zabezpieczanie magazynu kluczy](secure-your-key-vault.md)