---
title: Integracja z usługą Azure Private Link
description: Dowiedz się, jak zintegrować Azure Key Vault z usługą Azure Private Link Service
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: e5ddffb17c8f5acf16cf89dd58c634b6e404bf7b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749549"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrowanie usługi Key Vault z usługą Azure Private Link

Usługa Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault, Azure Storage i Azure Cosmos DB) oraz usług klienta/partnera hostowanych na platformie Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być przekierowyny przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Możesz połączyć się z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, [zobacz Co to jest Azure Private Link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować magazyn kluczy z Azure Private Link, potrzebne są następujące elementy:

- Magazyn kluczy.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora dla magazynu kluczy i sieci wirtualnej.

Prywatny punkt końcowy i sieć wirtualna muszą znajdować się w tym samym regionie. Wybranie regionu dla prywatnego punktu końcowego przy użyciu portalu spowoduje automatyczne filtrowanie tylko sieci wirtualnych, które znajdują się w tym regionie. Magazyn kluczy może być w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Nawiązywanie połączenia z połączeniem prywatnym z Key Vault przy użyciu Azure Portal 

Najpierw utwórz sieć wirtualną, korzystając z kroków instrukcje z tematu [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../../virtual-network/quick-create-portal.md)

Następnie możesz utworzyć nowy magazyn kluczy lub ustanowić połączenie prywatnego połączenia z istniejącym magazynem kluczy.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Tworzenie nowego magazynu kluczy i nawiązywanie połączenia z połączeniem prywatnym

Nowy magazyn kluczy można utworzyć przy użyciu interfejsu [Azure Portal](../general/quick-create-portal.md), [interfejsu wiersza polecenia](../general/quick-create-cli.md)platformy Azure lub [Azure PowerShell](../general/quick-create-powershell.md).

Po skonfigurowaniu podstaw magazynu kluczy wybierz kartę Sieć i wykonaj następujące kroki:

1. Wybierz przycisk radiowy Prywatny punkt końcowy na karcie Sieć.
1. Kliknij przycisk "+ Dodaj", aby dodać prywatny punkt końcowy.

    ![Zrzut ekranu przedstawiający kartę "Sieć" na stronie "Tworzenie magazynu kluczy".](../media/private-link-service-1.png)
 
1. W polu "Lokalizacja" bloku Tworzenie prywatnego punktu końcowego wybierz region, w którym znajduje się sieć wirtualna. 
1. W polu "Nazwa" utwórz opisową nazwę, która umożliwi zidentyfikowanie tego prywatnego punktu końcowego. 
1. Z menu rozwijanego wybierz sieć wirtualną i podsieć, w której ma zostać utworzony ten prywatny punkt końcowy. 
1. Pozostaw niezmienioną opcję "Integracja z usługą DNS w strefie prywatnej".  
1. Wybierz pozycję "OK".

    ![Zrzut ekranu przedstawiający stronę "Tworzenie prywatnego punktu końcowego" z wybranymi ustawieniami.](../media/private-link-service-8.png)
 
Teraz będzie można zobaczyć skonfigurowany prywatny punkt końcowy. Masz teraz możliwość usunięcia i edytowania tego prywatnego punktu końcowego. Wybierz przycisk "Przejrzyj i utwórz" i utwórz magazyn kluczy. Ukończenie wdrożenia potrwa 5–10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Nawiązywanie połączenia prywatnego z istniejącym magazynem kluczy

Jeśli masz już magazyn kluczy, możesz utworzyć połączenie z łączem prywatnym, wykonać następujące kroki:

1. Zaloguj się w witrynie Azure Portal. 
1. Na pasku wyszukiwania wpisz "magazyny kluczy"
1. Wybierz magazyn kluczy z listy, do którego chcesz dodać prywatny punkt końcowy.
1. Wybierz kartę "Sieć" w obszarze Ustawienia
1. Wybierz kartę Połączenia z prywatnym punktem końcowym w górnej części strony
1. Wybierz przycisk "+ Prywatny punkt końcowy" w górnej części strony.

    ![Zrzut ekranu przedstawiający przycisk "+ Prywatny punkt końcowy" na stronie "Sieć".](../media/private-link-service-3.png)
    ![Zrzut ekranu przedstawiający kartę "Podstawowe" na stronie "Tworzenie prywatnego punktu końcowego (wersja zapoznawcza).](../media/private-link-service-4.png)

Korzystając z tego bloku, możesz utworzyć prywatny punkt końcowy dla dowolnego zasobu platformy Azure. Możesz użyć menu rozwijanych, aby wybrać typ zasobu i wybrać zasób w katalogu, lub połączyć się z dowolnym zasobem platformy Azure przy użyciu identyfikatora zasobu. Pozostaw niezmienioną opcję "integracja ze strefą prywatną DNS".  

![Zrzut ekranu przedstawiający dodawanie prywatnego punktu końcowego przy użyciu bieżącego bloku. ](../media/private-link-service-3.png)
 ![ Zrzut ekranu przedstawiający przykład strony "Tworzenie prywatnego punktu końcowego (wersja zapoznawcza).](../media/private-link-service-4.png)

Podczas tworzenia prywatnego punktu końcowego połączenie musi zostać zatwierdzone. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy, znajduje się w katalogu, możesz zatwierdzić żądanie połączenia, o ile masz wystarczające uprawnienia. Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi żądanie połączenia.

Istnieją cztery stany aprowizowania:

| Akcja świadczenia usługi | Stan prywatnego punktu końcowego konsumenta usługi | Opis |
|--|--|--|
| Brak | Oczekiwanie | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie Private Link właściciela zasobu. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu linku prywatnego. |
| Usuń | Odłączony | Połączenie zostało usunięte przez właściciela zasobu linku prywatnego. Prywatny punkt końcowy staje się wartością informacji i należy go usunąć w celu oczyszczenia. |

### <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Jak zarządzać połączeniem prywatnego punktu końcowego w celu Key Vault przy użyciu Azure Portal 

1. Zaloguj się w witrynie Azure Portal.
1. Na pasku wyszukiwania wpisz "magazyny kluczy"
1. Wybierz magazyn kluczy, którym chcesz zarządzać.
1. Wybierz kartę "Sieć".
1. Jeśli istnieją oczekujące połączenia, w stanie aprowizowania będzie wyświetlane połączenie z stanem "Oczekujące". 
1. Wybierz prywatny punkt końcowy, który chcesz zatwierdzić
1. Wybierz przycisk Zatwierdź.
1. Jeśli istnieją jakiekolwiek połączenia prywatnego punktu końcowego, które chcesz odrzucić , niezależnie od tego, czy jest to oczekujące żądanie, czy istniejące połączenie, wybierz połączenie i kliknij przycisk "Odrzuć".

    ![Obraz](../media/private-link-service-7.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli-initial-setup"></a>Nawiązywanie połączenia z połączeniem prywatnym z usługą Key Vault interfejsu wiersza polecenia (konfiguracja początkowa)

```azurecli
az login                                                         # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                  # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                  # Create a new Resource Group
az provider register -n Microsoft.KeyVault                       # Register KeyVault as a provider
az keyvault create -n {VAULT NAME} -g {RG} -l {REGION}           # Create a Key Vault
az keyvault update -n {VAULT NAME} -g {RG} --default-action deny # Turn on Key Vault Firewall
az network vnet create -g {RG} -n {vNet NAME} -location {REGION} # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="create-a-private-endpoint-automatically-approve"></a>Tworzenie prywatnego punktu końcowego (automatycznie zatwierdzaj) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

### <a name="create-a-private-endpoint-manually-request-approval"></a>Tworzenie prywatnego punktu końcowego (ręczne żądanie zatwierdzenia) 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/{KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>Zarządzanie Private Link sieciowymi

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>Dodawanie Prywatna strefa DNS rekordów
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.vaultcore.azure.net" -n {KEY VAULT NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {KEY VAULT NAME}.vault.azure.net
nslookup {KEY VAULT NAME}.privatelink.vaultcore.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdzanie, czy połączenie z łączem prywatnym działa

Należy sprawdzić, czy zasoby w tej samej podsieci zasobu prywatnego punktu końcowego są połączone z magazynem kluczy za pośrednictwem prywatnego adresu IP i czy mają poprawną integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, korzystając z procedury opisanej w temacie Tworzenie maszyny wirtualnej z systemem [Windows w Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

Na karcie "Sieć":

1. Określ sieć wirtualną i podsieć. Możesz utworzyć nową sieć wirtualną lub wybrać istniejącą. W przypadku wybrania istniejącego regionu upewnij się, że jest to region.
1. Określ zasób publicznego adresu IP.
1. W "Sieciowej grupie zabezpieczeń kart sieciowych" wybierz pozycję "Brak".
1. W elektorze "Równoważenie obciążenia" wybierz pozycję "Nie".

Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Po uruchomieniu polecenia ns lookup w celu rozpoznania adresu IP magazynu kluczy za pośrednictwem publicznego punktu końcowego zostanie wyświetlony wynik podobny do tego:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Po uruchomieniu polecenia ns lookup w celu rozpoznania adresu IP magazynu kluczy za pośrednictwem prywatnego punktu końcowego zostanie wyświetlony wynik podobny do tego:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

* Upewnij się, że prywatny punkt końcowy jest w stanie zatwierdzonym. 
    1. Można to sprawdzić i poprawić w witrynie Azure Portal. Otwórz zasób usługi Key Vault i kliknij opcję Sieć. 
    2. Następnie wybierz kartę Połączenia z prywatnym punktem końcowym. 
    3. Upewnij się, że stan połączenia to Zatwierdzone, a stan inicjowania obsługi to Powodzenie. 
    4. Możesz również przejść do zasobu prywatnego punktu końcowego i przejrzeć tam te same właściwości, a następnie sprawdzić, czy sieć wirtualna jest taka sama jak ta, z których korzystasz.

* Upewnij się, że masz zasób Prywatna strefa DNS Zone. 
    1. Musisz mieć zasób Prywatna strefa DNS Zone o dokładnej nazwie: privatelink.vaultcore.azure.net. 
    2. Aby dowiedzieć się, jak to skonfigurować, zobacz poniższy link. [Prywatna strefa DNS stref](../../dns/private-dns-privatednszone.md)
    
* Sprawdź, czy prywatna strefa DNS nie jest połączona z siecią wirtualną. Ten problem może wystąpić, jeśli nadal jest zwracany publiczny adres IP. 
    1. Jeśli usługa DNS w strefie prywatnej nie jest połączona z siecią wirtualną, zapytanie DNS pochodzące z sieci wirtualnej zwróci publiczny adres IP magazynu kluczy. 
    2. Przejdź do zasobu Prywatna strefa DNS Zone w Azure Portal i kliknij opcję linków sieci wirtualnej. 
    4. Sieć wirtualna, która będzie wykonywać wywołania do magazynu kluczy, musi być wymieniona. 
    5. Jeśli jej tam nie ma, dodaj ją. 
    6. Aby uzyskać szczegółowe instrukcje, zobacz następujący [dokument Link Virtual Network to Prywatna strefa DNS Zone](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* Upewnij się, że Prywatna strefa DNS strefie kluczy nie brakuje rekordu A dla magazynu kluczy. 
    1. Przejdź do strony Prywatna strefa DNS Strefy czasowej. 
    2. Kliknij pozycję Przegląd i sprawdź, czy znajduje się tam rekord A zawierający prostą nazwę magazynu kluczy („fabrikam”). Nie określaj żadnego sufiksu.
    3. Pamiętaj o sprawdzeniu pisowni i utwórz lub popraw rekord A. Jako czas wygaśnięcia możesz wprowadzić wartość 3600 (1 godz.). 
    4. Upewnij się, że określono prawidłowy prywatny adres IP. 
    
* Upewnij się, że rekord A ma poprawny adres IP. 
    1. Adres IP można potwierdzić, otwierając zasób prywatnego punktu końcowego w Azure Portal.
    2. Przejdź do zasobu Microsoft.Network/privateEndpoints w witrynie Azure Portal (nie do zasobu usługi Key Vault)
    3. Na stronie przeglądu odszukaj pozycję Interfejs sieciowy i kliknij ten link. 
    4. Link będzie zawierał przegląd zasobu karty sieciowej, który zawiera właściwość Prywatny adres IP. 
    5. Sprawdź, czy jest to poprawny adres IP określony w rekordzie A.

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

> [!NOTE]
> Liczba magazynów kluczy z włączonymi prywatnymi punktami końcowymi na subskrypcję to dostosowywany limit. Przedstawiony poniżej limit jest limitem domyślnym. Jeśli chcesz poprosić o zwiększenie limitu dla swojej usługi, wyślij wiadomość e-mail na adres akv-privatelink@microsoft.com . Te żądania będą zatwierdzane dla sprawa.

**Cennik:** aby uzyskać informacje o cenach, [zobacz Azure Private Link cennik.](https://azure.microsoft.com/pricing/details/private-link/)

**Ograniczenia:** Prywatny punkt końcowy dla Azure Key Vault jest dostępny tylko w publicznych regionach platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na Key Vault:** 64.

**Domyślna liczba magazynów kluczy z prywatnymi punktami końcowymi na subskrypcję:** 400.

Aby uzyskać więcej informacji, [zobacz Azure Private Link service: Limitations (Usługa internetowa: ograniczenia)](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Azure Private Link](../../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [Azure Key Vault](overview.md)
