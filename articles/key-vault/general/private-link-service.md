---
title: Integracja z usługą Azure Private Link
description: Dowiedz się, jak zintegrować usługę Azure Key Vault z usługą Azure Private Link Service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.openlocfilehash: 678e91126c04d5b299d9234a1602580260c5aee6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425091"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integracja usługi Key Vault z usługą Azure Private Link

Usługa Azure Private Link Service umożliwia dostęp do usług platformy Azure (na przykład usługi Azure Key Vault, Azure Storage i Azure Cosmos DB) oraz usługi hostowane przez usługę Azure klient/partner za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy Cię prywatnie i bezpiecznie z usługą obsługiwaną przez usługę Azure Private Link. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, skutecznie wprowadzając usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, więc nie są potrzebne żadne bramy, urządzenia NAT, połączenia usługi ExpressRoute lub VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można połączyć się z wystąpieniem zasobu platformy Azure, co zapewnia najwyższy poziom szczegółowości w kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Co to jest łącze prywatne platformy Azure?](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować magazyn kluczy z usługą Azure Private Link, potrzebne będą następujące elementy:

- Przechowalnia kluczy.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora zarówno dla magazynu kluczy, jak i sieci wirtualnej.

Twój prywatny punkt końcowy i sieć wirtualna muszą znajdować się w tym samym regionie. Po wybraniu regionu dla prywatnego punktu końcowego za pomocą portalu, automatycznie filtruje tylko sieci wirtualne, które znajdują się w tym regionie. Magazyn kluczy może znajdować się w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

## <a name="establish-a-private-link-connection-to-key-vault-using-the-azure-portal"></a>Ustanawianie połączenia łącza prywatnego z usługą Key Vault za pomocą portalu Azure 

Najpierw utwórz sieć wirtualną, wykonując kroki opisane w [obszarze Tworzenie sieci wirtualnej przy użyciu portalu Azure](../../virtual-network/quick-create-portal.md)

Następnie można utworzyć nowy magazyn kluczy lub ustanowić połączenie łącza prywatnego z istniejącym magazynem kluczy.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Tworzenie nowego magazynu kluczy i ustanawianie połączenia łącza prywatnego

Możesz utworzyć nowy magazyn kluczy, wykonując kroki opisane w [obszarze Ustaw i pobierając klucz tajny z usługi Azure Key Vault za pomocą portalu Azure](../secrets/quick-create-portal.md)

Po skonfigurowaniu podstaw magazynu kluczy wybierz kartę Sieć i wykonaj następujące czynności:

1. Wybierz przycisk opcji Prywatny punkt końcowy na karcie Sieć.
1. Kliknij przycisk "+ Dodaj", aby dodać prywatny punkt końcowy.

    ![Image (Obraz)](../media/private-link-service-1.png)
 
1. W polu "Lokalizacja" bloku Utwórz prywatny punkt końcowy wybierz region, w którym znajduje się sieć wirtualna. 
1. W polu "Nazwa" utwórz opisową nazwę, która pozwoli ci zidentyfikować ten prywatny punkt końcowy. 
1. Wybierz sieć wirtualną i podsieć, w której ma zostać utworzony ten prywatny punkt końcowy z menu rozwijanego. 
1. Pozostaw opcję "integruj się z systemem DNS strefy prywatnej" bez zmian.  
1. Wybierz "Ok".

    ![Image (Obraz)](../media/private-link-service-8.png)
 
Teraz będzie można zobaczyć skonfigurowany prywatny punkt końcowy. Teraz masz możliwość usunięcia i edycji tego prywatnego punktu końcowego. Wybierz przycisk "Recenzja + Utwórz" i utwórz przechowalnię kluczy. Wdrożenie potrwa od 5 do 10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Ustanawianie połączenia łącza prywatnego z istniejącym magazynem kluczy

Jeśli masz już magazyn kluczy, możesz utworzyć połączenie łącza prywatnego, wykonując następujące kroki:

1. Zaloguj się do Portalu Azure. 
1. Na pasku wyszukiwania wpisz "magazyny kluczy"
1. Wybierz magazyn kluczy z listy, do której chcesz dodać prywatny punkt końcowy.
1. Wybierz kartę "Sieć" w obszarze Ustawienia
1. Wybierz kartę Prywatne połączenia punktów końcowych u góry strony
1. Wybierz przycisk "+ Prywatny punkt końcowy" u góry strony.

    ![Obraz](../media/private-link-service-3.png) ![](../media/private-link-service-4.png)

Można utworzyć prywatny punkt końcowy dla dowolnego zasobu platformy Azure przy użyciu tego bloku. Menu rozwijane można użyć, aby wybrać typ zasobu i wybrać zasób w katalogu lub połączyć się z dowolnym zasobem platformy Azure przy użyciu identyfikatora zasobu. Pozostaw opcję "integruj się z systemem DNS strefy prywatnej" bez zmian.  

![Obraz](../media/private-link-service-3.png)
![](../media/private-link-service-4.png)

## <a name="establish-a-private-link-connection-to-key-vault-using-cli"></a>Ustanawianie połączenia prywatnego z usługą Key Vault przy użyciu interfejsu wiersza polecenia

### <a name="login-to-azure-cli"></a>Zaloguj się do interfejsu wiersza polecenia platformy Azure
```console
az login 
```
### <a name="select-your-azure-subscription"></a>Wybierz subskrypcję platformy Azure 
```console
az account set --subscription {AZURE SUBSCRIPTION ID}
```
### <a name="create-a-new-resource-group"></a>Tworzenie nowej grupy zasobów 
```console
az group create -n {RG} -l {AZURE REGION}
```
### <a name="register-microsoftkeyvault-as-a-provider"></a>Zarejestruj usługę Microsoft.KeyVault jako dostawcę 
```console
az provider register -n Microsoft.KeyVault
```
### <a name="create-a-new-key-vault"></a>Tworzenie nowej przechowalni kluczy
```console
az keyvault create --name {KEY VAULT NAME} --resource-group {RG} --location {AZURE REGION}
```
### <a name="turn-on-key-vault-firewall"></a>Włączanie Zapory magazynu kluczy
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
### <a name="disable-virtual-network-policies"></a>Wyłącz zasady sieci wirtualnej 
```console
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true
```
### <a name="add-a-private-dns-zone"></a>Dodawanie prywatnej strefy DNS 
```console
az network private-dns zone create --resource-group {RG} --name privatelink.vaultcore.azure.net
```
### <a name="link-private-dns-zone-to-virtual-network"></a>Połącz prywatną strefę DNS z siecią wirtualną 
```console
az network private-dns link vnet create --resoruce-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="create-a-private-endpoint-automatically-approve"></a>Tworzenie prywatnego punktu końcowego (automatyczne zatwierdzanie) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION}
```
### <a name="create-a-private-endpoint-manually-request-approval"></a>Tworzenie prywatnego punktu końcowego (ręczne żądanie zatwierdzenia) 
```console
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/vaults/ {KEY VAULT NAME}" --group-ids vault --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```
### <a name="show-connection-status"></a>Pokaż stan połączenia 
```console
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}
```
## <a name="manage-private-link-connection"></a>Zarządzanie połączeniem łącza prywatnego

Podczas tworzenia prywatnego punktu końcowego połączenie musi zostać zatwierdzone. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy znajduje się w katalogu, będzie można zatwierdzić żądanie połączenia, pod warunkiem, że masz wystarczające uprawnienia; Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi żądanie połączenia.

Istnieją cztery stany inicjowania obsługi administracyjnej:

| Usługa zapewnia działanie | Stan prywatnego punktu końcowego usługi konsumenta | Opis |
|--|--|--|
| Brak | Oczekujące | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu Łącza prywatnego. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu łącza prywatnego. |
| Remove | Odłączony | Połączenie zostało usunięte przez właściciela zasobu łącza prywatnego, prywatny punkt końcowy staje się informacyjny i powinien zostać usunięty w celu oczyszczenia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Jak zarządzać połączeniem prywatnego punktu końcowego z usługą Key Vault za pomocą witryny Azure portal 

1. Zaloguj się w witrynie Azure Portal.
1. Na pasku wyszukiwania wpisz "magazyny kluczy"
1. Wybierz magazyn kluczy, którymi chcesz zarządzać.
1. Wybierz kartę "Sieć".
1. Jeśli istnieją jakieś połączenia, które są oczekujące, zobaczysz połączenie na liście "Oczekujące" w stanie inicjowania obsługi administracyjnej. 
1. Wybierz prywatny punkt końcowy, który chcesz zatwierdzić
1. Wybierz przycisk Zatwierdź.
1. Jeśli istnieją jakieś prywatne połączenia z punktem końcowym, które chcesz odrzucić, niezależnie od tego, czy jest to żądanie oczekujące, czy istniejące połączenie, wybierz połączenie i kliknij przycisk "Odrzuć".

    ![Image (Obraz)](../media/private-link-service-7.png)

##  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-azure-cli"></a>Jak zarządzać połączeniem prywatnego punktu końcowego z usługą Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="approve-a-private-link-connection-request"></a>Zatwierdzanie żądania połączenia łącza prywatnego
```console
az keyvault private-endpoint-connection approve --approval-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="deny-a-private-link-connection-request"></a>Odmów żądania połączenia łącza prywatnego
```console
az keyvault private-endpoint-connection reject --rejection-description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --vault-name {KEY VAULT NAME} –name {PRIVATE LINK CONNECTION NAME}
```

### <a name="delete-a-private-link-connection-request"></a>Usuwanie żądania połączenia łącza prywatnego
```console
az keyvault private-endpoint-connection delete --resource-group {RG} --vault-name {KEY VAULT NAME} --name {PRIVATE LINK CONNECTION NAME}
```

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdzanie, czy połączenie łącza prywatnego działa

Należy sprawdzić, czy zasoby w tej samej podsieci zasobu prywatnego punktu końcowego łączą się z magazynem kluczy za pośrednictwem prywatnego adresu IP i że mają one prawidłową integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując kroki opisane w [obszarze Tworzenie maszyny wirtualnej systemu Windows w portalu Azure](../../virtual-machines/windows/quick-create-portal.md)

W zakładce "Sieć":

1. Określ sieć wirtualną i podsieć. Można utworzyć nową sieć wirtualną lub wybrać istniejącą. Jeśli wybierzesz istniejący, upewnij się, że region jest zgodny.
1. Określ publiczny zasób IP.
1. W grupie zabezpieczeń sieciowej karty sieciowej wybierz opcję "Brak".
1. W "Równoważeniu obciążenia" wybierz "Nie".

Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Po uruchomieniu polecenia wyszukiwania ns w celu rozpoznania adresu IP magazynu kluczy nad publicznym punktem końcowym zostanie wyświetlony wynik, który wygląda następująco:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Po uruchomieniu polecenia wyszukiwania ns w celu rozpoznania adresu IP magazynu kluczy nad prywatnym punktem końcowym zostanie wyświetlony wynik, który wygląda następująco:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia projektowe

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik łącza prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia:** Prywatny punkt końcowy usługi Azure Key Vault jest dostępny tylko w regionach publicznych platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na przechowalnię kluczy:** 64.

**Maksymalna liczba magazynów kluczy z prywatnymi punktami końcowymi na subskrypcję:** 64.

Aby uzyskać więcej informacji, zobacz [Usługa Azure Private Link: Ograniczenia](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [łączu prywatnym platformy Azure](../../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [usłudze Azure Key Vault](overview.md)
