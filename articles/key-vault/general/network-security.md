---
title: Konfigurowanie Azure Key Vault sieci wirtualnych i zapory sieci wirtualnych — Azure Key Vault
description: Instrukcje krok po kroku dotyczące konfigurowania Key Vault i sieci wirtualnych
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1f2df113b855ef85906e00a0316b09870ff20fb7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814374"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie Azure Key Vault sieci wirtualnych i zapory sieci wirtualnych

Ten artykuł zawiera wskazówki dotyczące konfigurowania zapory Azure Key Vault zapory. W tym dokumencie opisano szczegółowo różne konfiguracje zapory usługi Key Vault oraz instrukcje krok po kroku dotyczące sposobu konfigurowania usługi Azure Key Vault do pracy z innymi aplikacjami i usługami platformy Azure.

Aby uzyskać więcej informacji, zobacz [Virtual network service endpoints for Azure Key Vault](overview-vnet-service-endpoints.md)(Punkty końcowe usługi dla sieci Azure Key Vault ).

## <a name="firewall-settings"></a>Ustawienia zapory

Ta sekcja obejmuje różne sposoby konfigurowania Azure Key Vault zapory.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault zapora sieciowa jest wyłączona (ustawienie domyślne)

Domyślnie podczas tworzenia nowego magazynu kluczy zapora Azure Key Vault wyłączona. Wszystkie aplikacje i usługi platformy Azure mogą uzyskać dostęp do magazynu kluczy i wysyłać żądania do magazynu kluczy. Pamiętaj, że ta konfiguracja nie oznacza, że żaden użytkownik będzie mógł wykonywać operacje na magazynie kluczy. Magazyn kluczy nadal ogranicza się do wpisów tajnych, kluczy i certyfikatów przechowywanych w magazynie kluczy, wymagając uprawnień zasad uwierzytelniania Azure Active Directory dostępu. Aby bardziej szczegółowo poznać uwierzytelnianie magazynu kluczy, zobacz dokument Podstawy uwierzytelniania magazynu kluczy [w tym miejscu.](./authentication-fundamentals.md) Aby uzyskać więcej informacji, zobacz [Azure Key Vault dostępu za zaporą.](./access-behind-firewall.md)

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault Zapora sieciowa (tylko usługi zaufane)

Po włączeniu zapory Key Vault będzie dostępna opcja "Zezwalaj zaufanym usługom firmy Microsoft na obejście tej zapory". Lista zaufanych usług nie obejmuje każdej pojedynczej usługi platformy Azure. Na przykład Azure DevOps nie ma na liście zaufanych usług. **Nie oznacza to, że usługi, które nie są wyświetlane na liście zaufanych usług, nie są zaufane ani niezabezpieczone.** Lista zaufanych usług obejmuje usługi, w których firma Microsoft kontroluje cały kod uruchamiany w usłudze. Ponieważ użytkownicy mogą pisać kod niestandardowy w usługach platformy Azure, takich jak Azure DevOps, firma Microsoft nie zapewnia możliwości utworzenia zatwierdzenia dla usługi. Ponadto to, że usługa pojawia się na liście zaufanych usług, nie oznacza, że jest dozwolona we wszystkich scenariuszach. 

Aby ustalić, czy usługa, z których próbujesz korzystać, znajduje się na liście zaufanych usług, zapoznaj się z następującym dokumentem [tutaj.](./overview-vnet-service-endpoints.md#trusted-services)
Aby uzyskać instrukcje, postępuj zgodnie z instrukcjami w witrynie [Portal, interfejsie wiersza polecenia platformy Azure i programie PowerShell](https://docs.microsoft.com/azure/key-vault/general/network-security#use-the-azure-portal)

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault zaporę (adresy IPv4 i zakresy — statyczne adresy IP)

Jeśli chcesz autoryzować określoną usługę do uzyskiwania dostępu do magazynu kluczy za pośrednictwem zapory usługi Key Vault, możesz dodać jego adres IP do listy zezwalań zapory magazynu kluczy. Ta konfiguracja jest najlepsza w przypadku usług, które używają statycznych adresów IP lub dobrze znanych zakresów. W tym przypadku istnieje limit 1000 zakresów CIDR.

Aby zezwolić na adres IP lub zakres zasobów platformy Azure, takich jak aplikacja internetowa lub aplikacja logiki, wykonaj następujące kroki.

1. Logowanie do witryny Azure Portal
1. Wybierz zasób (określone wystąpienie usługi)
1. Kliknij blok "Właściwości" w obszarze "Ustawienia"
1. Poszukaj pola "Adres IP".
1. Skopiuj tę wartość lub zakres i wprowadź ją do listy zezwalań zapory magazynu kluczy.

Aby zezwolić całej usłudze platformy Azure za pośrednictwem zapory Key Vault, użyj listy publicznie udokumentowanych adresów IP centrum danych dla platformy Azure [tutaj.](https://www.microsoft.com/download/details.aspx?id=41653) Znajdź adresy IP skojarzone z usługą w regionie, który chcesz, i dodaj te adresy IP do zapory magazynu kluczy, korzystając z powyższych kroków.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault Zapora sieciowa (sieci wirtualne — dynamiczne ip)

Jeśli próbujesz zezwolić na zasoby platformy Azure, takie jak maszyna wirtualna, za pośrednictwem magazynu kluczy, możesz nie być w stanie używać statycznych adresów IP i nie chcesz zezwalać wszystkim adresom IP dla usługi Azure Virtual Machines na dostęp do magazynu kluczy.

W takim przypadku należy utworzyć zasób w sieci wirtualnej, a następnie zezwolić ruchowi z określonej sieci wirtualnej i podsieci na dostęp do magazynu kluczy. W tym celu wykonaj następujące czynności.

1. Logowanie do witryny Azure Portal
1. Wybierz magazyn kluczy, który chcesz skonfigurować
1. Wybierz blok "Sieć"
1. Wybierz pozycję "+ Dodaj istniejącą sieć wirtualną"
1. Wybierz sieć wirtualną i podsieć, na które chcesz zezwolić za pośrednictwem zapory magazynu kluczy.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault Zapora sieciowa (Private Link)

Aby dowiedzieć się, jak skonfigurować połączenie z linkiem prywatnym w magazynie kluczy, zapoznaj się z dokumentem [tutaj.](./private-link-service.md)

> [!IMPORTANT]
> Gdy reguły zapory obowiązują, użytkownicy mogą wykonywać [](security-features.md#privileged-access) operacje płaszczyzny danych Key Vault tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu Key Vault z Azure Portal. Mimo że użytkownicy mogą przejść do magazynu kluczy z witryny Azure Portal, mogą nie być w stanie wyświetlić listy kluczy, wpisów tajnych lub certyfikatów, jeśli ich komputera klienckiego nie ma na liście dozwolonych. Ma to również wpływ na Key Vault wyboru przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie klucze listy, jeśli reguły zapory uniemożliwiają ich komputer kliencki.

> [!NOTE]
> Należy pamiętać o następujących ograniczeniach konfiguracji:
> * Dozwolonych jest maksymalnie 127 reguł sieci wirtualnej i 127 reguł protokołu IPv4. 
> * Reguły sieci IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) są niedozwolone w zasadach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się **od 10.**, **172.16-31** i **192.168.**. 
> * Obecnie obsługiwane są tylko adresy IPv4.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Poniżej opisano sposób konfigurowania zapór Key Vault sieci wirtualnych przy użyciu Azure Portal:

1. Przejdź do magazynu kluczy, który chcesz zabezpieczyć.
2. Wybierz **pozycję Sieć,** a następnie wybierz **kartę Zapory i sieci** wirtualne.
3. W **obszarze Zezwalaj na dostęp z** programu wybierz pozycję Wybrane **sieci.**
4. Aby dodać istniejące sieci wirtualne do zapór i reguł sieci wirtualnej, wybierz **pozycję + Dodaj istniejące sieci wirtualne.**
5. W nowym bloku, który zostanie otwarty, wybierz subskrypcję, sieci wirtualne i podsieci, dla których chcesz zezwolić na dostęp do tego magazynu kluczy. Jeśli wybrane sieci wirtualne i podsieci nie mają włączonych punktów końcowych usługi, potwierdź, że chcesz włączyć punkty końcowe usługi, a następnie wybierz **pozycję Włącz**. Działanie może potrwać do 15 minut.
6. W **obszarze Sieci IP** dodaj zakresy adresów IPv4, wpisując zakresy adresów IPv4 w notacji [CIDR (Routing](https://tools.ietf.org/html/rfc4632) między domenami bez klas) lub poszczególnych adresów IP.
7. Jeśli chcesz zezwolić usługom zaufanym firmy Microsoft na pomijanie zapory Key Vault, wybierz pozycję "Tak". Aby uzyskać pełną listę bieżących usług Key Vault zaufanych usług, zobacz poniższy link. [Azure Key Vault zaufane usługi](./overview-vnet-service-endpoints.md#trusted-services)
7. Wybierz pozycję **Zapisz**.

Możesz również dodać nowe sieci wirtualne i podsieci, a następnie włączyć punkty końcowe usługi dla nowo utworzonych sieci wirtualnych i podsieci, wybierając pozycję **+ Dodaj nową sieć wirtualną.** Następnie postępuj zgodnie z monitami.

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure 

Poniżej opisano sposób konfigurowania zapór Key Vault sieci wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

1. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj [się.](/cli/azure/authenticate-azure-cli)

2. Lista dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Włącz punkt końcowy usługi dla Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Dodaj regułę sieci dla sieci wirtualnej i podsieci.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Dodaj zakres adresów IP, z którego ma być zezwalany ruch.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być dostępny dla dowolnych zaufanych usług, ustaw `bypass` wartość `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Włącz reguły sieciowe, ustawiając akcję domyślną na `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Poniżej opisano sposób konfigurowania zapór Key Vault sieci wirtualnych przy użyciu programu PowerShell:

1. Zainstaluj najnowszą [wersję Azure PowerShell](/powershell/azure/install-az-ps)i [zaloguj się.](/powershell/azure/authenticate-azureps)

2. Lista dostępnych reguł sieci wirtualnej. Jeśli nie ustawiono żadnych reguł dla tego magazynu kluczy, lista będzie pusta.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Włącz punkt końcowy usługi dla Key Vault w istniejącej sieci wirtualnej i podsieci.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Dodaj regułę sieci dla sieci wirtualnej i podsieci.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Dodaj zakres adresów IP, z którego ma być zezwalany ruch.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Jeśli ten magazyn kluczy powinien być dostępny dla dowolnych zaufanych usług, ustaw `bypass` wartość `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Włącz reguły sieciowe, ustawiając akcję domyślną na `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Odwołania
* Odwołanie do szablonu usługi ARM: [Azure Key Vault szablonu usługi ARM](/azure/templates/Microsoft.KeyVault/vaults)
* Polecenia interfejsu wiersza polecenia platformy Azure: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlet: [Get-AzKeyVault,](/powershell/module/az.keyvault/get-azkeyvault) [Add-AzKeyVaultNetworkRule,](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule) [Remove-AzKeyVaultNetworkRule,](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule) [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Następne kroki

* [Punkty końcowe usługi dla sieci wirtualnej dla Key Vault](overview-vnet-service-endpoints.md)
* [Azure Key Vault zabezpieczeń](security-features.md)
