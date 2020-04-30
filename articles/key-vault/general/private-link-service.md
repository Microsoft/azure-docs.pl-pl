---
title: Integracja z usługą Azure Private Link
description: Dowiedz się, jak zintegrować Azure Key Vault z usługą Azure Private Link Service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 678e91126c04d5b299d9234a1602580260c5aee6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81425091"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integracja Key Vault z prywatnym łączem platformy Azure

Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Key Vault, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować Magazyn kluczy z linkiem prywatnym platformy Azure, potrzebne są następujące elementy:

- Magazyn kluczy.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora dla magazynu kluczy i sieci wirtualnej.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. W przypadku wybrania regionu dla prywatnego punktu końcowego przy użyciu portalu program automatycznie odfiltruje tylko te sieci wirtualne, które znajdują się w tym regionie. Magazyn kluczy może znajdować się w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Nawiąż połączenie prywatne z Key Vault przy użyciu Azure Portal 

Najpierw Utwórz sieć wirtualną, wykonując czynności opisane w temacie [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../../virtual-network/quick-create-portal.md)

Następnie można utworzyć nowy magazyn kluczy lub nawiązać połączenie prywatne z istniejącym magazynem kluczy.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Tworzenie nowego magazynu kluczy i nawiązywanie połączenia prywatnego

Nowy magazyn kluczy można utworzyć, wykonując kroki z [zestawu i pobierając klucz tajny z Azure Key Vault przy użyciu Azure Portal](../secrets/quick-create-portal.md)

Po skonfigurowaniu podstawy magazynu kluczy wybierz kartę Sieć i wykonaj następujące czynności:

1. Wybierz przycisk radiowy prywatny punkt końcowy na karcie Sieć.
1. Kliknij przycisk "+ Dodaj", aby dodać prywatny punkt końcowy.

    ![Obraz](../media/private-link-service-1.png)
 
1. W polu "lokalizacja" bloku Utwórz prywatny punkt końcowy wybierz region, w którym znajduje się Twoja sieć wirtualna. 
1. W polu "name" (nazwa) Utwórz opisową nazwę, która umożliwi zidentyfikowanie tego prywatnego punktu końcowego. 
1. Wybierz sieć wirtualną i podsieć, dla której ma zostać utworzony ten prywatny punkt końcowy z menu rozwijanego. 
1. Pozostaw opcję "Zintegruj z usługą DNS strefy prywatnej" bez zmian.  
1. Wybierz pozycję "OK".

    ![Obraz](../media/private-link-service-8.png)
 
Teraz będzie można zobaczyć skonfigurowany prywatny punkt końcowy. Masz teraz możliwość usunięcia i edytowania tego prywatnego punktu końcowego. Wybierz przycisk "Przejrzyj + Utwórz" i Utwórz magazyn kluczy. Ukończenie wdrożenia zajmie 5-10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Ustanów połączenie prywatne z istniejącym magazynem kluczy

Jeśli masz już Magazyn kluczy, możesz utworzyć połączenie prywatne, wykonując następujące czynności:

1. Zaloguj się do witryny Azure Portal. 
1. Na pasku wyszukiwania wpisz ciąg "magazyny kluczy".
1. Z listy wybierz magazyn kluczy, do którego chcesz dodać prywatny punkt końcowy.
1. Wybierz kartę "Sieć" w obszarze Ustawienia
1. Wybierz kartę połączenia prywatnego punktu końcowego w górnej części strony
1. Wybierz przycisk "+ prywatny punkt końcowy" w górnej części strony.

    ![](../media/private-link-service-3.png) ![Obraz obrazu](../media/private-link-service-4.png)

Za pomocą tego bloku można utworzyć prywatny punkt końcowy dla dowolnego zasobu platformy Azure. Możesz użyć menu rozwijanych, aby wybrać typ zasobu i wybrać zasób w katalogu, lub połączyć się z dowolnym zasobem platformy Azure przy użyciu identyfikatora zasobu. Pozostaw opcję "Zintegruj z usługą DNS strefy prywatnej" bez zmian.  

![](../media/private-link-service-3.png)
![Obraz obrazu](../media/private-link-service-4.png)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Nawiązywanie połączenia prywatnego z Key Vault przy użyciu interfejsu wiersza polecenia

### <a name="login-to-azure-cli"></a>Zaloguj się do interfejsu wiersza polecenia platformy Azure
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Wybierz swoją subskrypcję platformy Azure 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Tworzenie nowej grupy zasobów 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>Zarejestrujmy magazyn Microsoft. kluczy jako dostawcę 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Utwórz nowy Key Vault
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>Włączanie zapory Key Vault
```console
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION} --default-action deny
```
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
```console
az network vnet create --resource-group {RG} --name {vNet NAME} --location {AZURE REGION}
```
### <a name="add-a-subnet"></a>Dodawanie podsieci
```console
az network vnet subnet create --resource-group {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}
```
### <a name="disable-virtual-network-policies"></a>Wyłącz zasady Virtual Network 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>Dodawanie strefy Prywatna strefa DNS 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>Połącz strefę Prywatna strefa DNS z Virtual Network 
```console
az network private-dns link vnet create --resoruce-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Tworzenie prywatnego punktu końcowego (automatyczne zatwierdzanie) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Tworzenie prywatnego punktu końcowego (żądanie zatwierdzenia ręcznie) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Pokaż stan połączenia 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Zarządzaj połączeniem prywatnego linku

Podczas tworzenia prywatnego punktu końcowego należy zatwierdzić połączenie. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy, znajduje się w katalogu, będzie można zatwierdzić żądanie połączenia, pod warunkiem że masz wystarczające uprawnienia; Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi Twoje żądanie połączenia.

Istnieją cztery Stany aprowizacji:

| Akcja udostępniania usługi | Stan prywatnego punktu końcowego klienta usługi | Opis |
|--|--|--|
| Brak | Oczekiwanie | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu link prywatny. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu link prywatny. |
| Remove | Odłączony | Połączenie zostało usunięte przez właściciela zasobu link prywatny, a prywatny punkt końcowy zmieni się na format i powinien zostać usunięty do oczyszczenia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Jak zarządzać połączeniem prywatnego punktu końcowego w celu Key Vault przy użyciu Azure Portal 

1. Zaloguj się w witrynie Azure Portal.
1. Na pasku wyszukiwania wpisz ciąg "magazyny kluczy".
1. Wybierz magazyn kluczy, którym chcesz zarządzać.
1. Wybierz kartę Sieć.
1. W przypadku braku oczekujących połączeń zostanie wyświetlone połączenie z opcją "oczekiwanie" w stanie aprowizacji. 
1. Wybierz prywatny punkt końcowy, który chcesz zatwierdzić
1. Wybierz przycisk Zatwierdź.
1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, niezależnie od tego, czy jest to oczekujące żądanie, czy istniejące połączenie, wybierz połączenie i kliknij przycisk Odrzuć.

    ![Obraz](../media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Jak zarządzać połączeniem prywatnego punktu końcowego w celu Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="approve-a-private-link-connection-request"></a>Zatwierdź żądanie połączenia prywatnego linku
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Odmów żądania połączenia z linkiem prywatnym
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Usuwanie żądania połączenia prywatnego linku
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdź, czy połączenie z linkiem prywatnym działa

Należy sprawdzić, czy zasoby znajdujące się w tej samej podsieci zasobu prywatnego punktu końcowego są połączone z magazynem kluczy za pośrednictwem prywatnego adresu IP i czy mają poprawną integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując czynności opisane w sekcji [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

Na karcie "Sieć":

1. Określ sieć wirtualną i podsieć. Można utworzyć nową sieć wirtualną lub wybrać istniejącą. Jeśli wybierzesz jeden z istniejących, upewnij się, że region jest zgodny.
1. Określ zasób publicznego adresu IP.
1. W obszarze "sieciowa Grupa zabezpieczeń kart sieciowych" Wybierz pozycję "Brak".
1. W obszarze "Równoważenie obciążenia" Wybierz pozycję "nie".

Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

W przypadku uruchomienia polecenia wyszukiwania NS w celu rozpoznania adresu IP magazynu kluczy za pośrednictwem publicznego punktu końcowego zobaczysz wynik, który wygląda następująco:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

W przypadku uruchomienia polecenia wyszukiwania NS w celu rozpoznania adresu IP magazynu kluczy za pośrednictwem prywatnego punktu końcowego zobaczysz wynik, który wygląda następująco:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia**: prywatny punkt końcowy dla Azure Key Vault jest dostępny tylko w publicznych regionach platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na Key Vault**: 64.

**Maksymalna liczba magazynów kluczy z prywatnymi punktami końcowymi na subskrypcję**: 64.

Aby uzyskać więcej informacji, zobacz [usługa Azure Private Link Service: ograniczenia](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnym łączu platformy Azure](../../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [Azure Key Vault](overview.md)
