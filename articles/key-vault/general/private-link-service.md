---
title: Integracja z usługą Azure Private Link
description: Dowiedz się, jak zintegrować Azure Key Vault z usługą Azure Private Link Service
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 03/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07be83527fa781f87ed1de06fa41bd6d08ee9dc4
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426586"
---
# <a name="integrate-key-vault-with-azure-private-link"></a>Integrowanie usługi Key Vault z usługą Azure Private Link

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

    ![Zrzut ekranu, który pokazuje pierwszy ekran podczas tworzenia magazynu kluczy.](../media/private-link-service-1.png)
 
1. W polu "lokalizacja" bloku Utwórz prywatny punkt końcowy wybierz region, w którym znajduje się Twoja sieć wirtualna. 
1. W polu "name" (nazwa) Utwórz opisową nazwę, która umożliwi zidentyfikowanie tego prywatnego punktu końcowego. 
1. Wybierz sieć wirtualną i podsieć, dla której ma zostać utworzony ten prywatny punkt końcowy z menu rozwijanego. 
1. Pozostaw opcję "Zintegruj z usługą DNS strefy prywatnej" bez zmian.  
1. Wybierz pozycję "OK".

    ![Zrzut ekranu pokazujący pola, które są ważne w przypadku konfigurowania prywatnego punktu końcowego.](../media/private-link-service-8.png)
 
Teraz będzie można zobaczyć skonfigurowany prywatny punkt końcowy. Masz teraz możliwość usunięcia i edytowania tego prywatnego punktu końcowego. Wybierz przycisk "Przejrzyj + Utwórz" i Utwórz magazyn kluczy. Ukończenie wdrożenia zajmie 5-10 minut. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Ustanów połączenie prywatne z istniejącym magazynem kluczy

Jeśli masz już Magazyn kluczy, możesz utworzyć połączenie prywatne, wykonując następujące czynności:

1. Zaloguj się do Portalu Azure. 
1. Na pasku wyszukiwania wpisz ciąg "magazyny kluczy".
1. Z listy wybierz magazyn kluczy, do którego chcesz dodać prywatny punkt końcowy.
1. Wybierz kartę "Sieć" w obszarze Ustawienia
1. Wybierz kartę połączenia prywatnego punktu końcowego w górnej części strony
1. Wybierz przycisk "+ prywatny punkt końcowy" w górnej części strony.

    ![Zrzut ekranu, na którym jest wyświetlany przycisk + prywatny punkt końcowy.](../media/private-link-service-3.png)
    ![Zrzut ekranu przedstawiający ekran tworzenia prywatnego punktu końcowego.](../media/private-link-service-4.png)

Za pomocą tego bloku można utworzyć prywatny punkt końcowy dla dowolnego zasobu platformy Azure. Możesz użyć menu rozwijanych, aby wybrać typ zasobu i wybrać zasób w katalogu, lub połączyć się z dowolnym zasobem platformy Azure przy użyciu identyfikatora zasobu. Pozostaw opcję "Zintegruj z usługą DNS strefy prywatnej" bez zmian.  

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
az keyvault update --name {KEY VAULT NAME} --resource-group {RG} --default-action deny
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
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.vaultcore.azure.net --name {dnsZoneLinkName} --registration-enabled true
```
### <a name="add-private-dns-records"></a>Dodawanie Prywatna strefa DNS rekordów
```console
# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g $rg_name
az network private-dns record-set a add-record -g $rg_name -z "privatelink.vaultcore.azure.net" -n $vault_name -a $kv_network_interface_private_ip
az network private-dns record-set list -g $rg_name -z "privatelink.vaultcore.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup $vault_name.vault.azure.net
nslookup $vault_name.privatelink.vaultcore.azure.net
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
| Usuń | Odłączony | Połączenie zostało usunięte przez właściciela zasobu link prywatny, a prywatny punkt końcowy zmieni się na format i powinien zostać usunięty do oczyszczenia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault-using-the-azure-portal"></a>Jak zarządzać połączeniem prywatnego punktu końcowego w celu Key Vault przy użyciu Azure Portal 

1. Zaloguj się w witrynie Azure Portal.
1. Na pasku wyszukiwania wpisz ciąg "magazyny kluczy".
1. Wybierz magazyn kluczy, którym chcesz zarządzać.
1. Wybierz kartę Sieć.
1. W przypadku braku oczekujących połączeń zostanie wyświetlone połączenie z opcją "oczekiwanie" w stanie aprowizacji. 
1. Wybierz prywatny punkt końcowy, który chcesz zatwierdzić
1. Wybierz przycisk Zatwierdź.
1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, niezależnie od tego, czy jest to oczekujące żądanie, czy istniejące połączenie, wybierz połączenie i kliknij przycisk Odrzuć.

    ![Image (Obraz)](../media/private-link-service-7.png)

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

## <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

* Upewnij się, że prywatny punkt końcowy jest w stanie zatwierdzone. 
    1. Można to sprawdzić i poprawić w witrynie Azure Portal. Otwórz zasób usługi Key Vault i kliknij opcję Sieć. 
    2. Następnie wybierz kartę połączenia prywatnego punktu końcowego. 
    3. Upewnij się, że stan połączenia to zatwierdzone, a stan aprowizacji zakończył się pomyślnie. 
    4. Możesz również przejść do prywatnego zasobu punktu końcowego i przejrzeć te same właściwości, a następnie sprawdzić, czy sieć wirtualna jest zgodna z tą, której używasz.

* Upewnij się, że masz zasób strefy Prywatna strefa DNS. 
    1. Musisz mieć Prywatna strefa DNS zasób strefy o dokładnej nazwie: privatelink.vaultcore.azure.net. 
    2. Aby dowiedzieć się, jak skonfigurować to ustawienie, zobacz następujący link. [Strefy Prywatna strefa DNS](https://docs.microsoft.com/azure/dns/private-dns-privatednszone)
    
* Sprawdź, czy prywatna strefa DNS nie jest połączona z siecią wirtualną. Może to być problem, jeśli nadal jest wyświetlany publiczny adres IP. 
    1. Jeśli usługa DNS strefy prywatnej nie jest połączona z siecią wirtualną, zapytanie DNS pochodzące z sieci wirtualnej zwróci publiczny adres IP magazynu kluczy. 
    2. Przejdź do zasobu strefy Prywatna strefa DNS w Azure Portal i kliknij opcję linki sieci wirtualnej. 
    4. Należy wymienić sieć wirtualną, która będzie wykonywać wywołania do magazynu kluczy. 
    5. Jeśli tak nie jest, Dodaj go. 
    6. Aby uzyskać szczegółowe instrukcje, zobacz następujący link do dokumentu [Virtual Network prywatna strefa DNS Zone](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Upewnij się, że strefa Prywatna strefa DNS nie zawiera rekordu A dla magazynu kluczy. 
    1. Przejdź do strony strefy Prywatna strefa DNS. 
    2. Kliknij pozycję Przegląd i sprawdź, czy znajduje się tam rekord A zawierający prostą nazwę magazynu kluczy („fabrikam”). Nie określaj żadnego sufiksu.
    3. Pamiętaj o sprawdzeniu pisowni i utwórz lub popraw rekord A. Jako czas wygaśnięcia możesz wprowadzić wartość 3600 (1 godz.). 
    4. Upewnij się, że określono poprawny prywatny adres IP. 
    
* Upewnij się, że rekord A ma prawidłowy adres IP. 
    1. Adres IP można potwierdzić, otwierając zasób prywatnego punktu końcowego w witrynie Azure Portal 
    2. Przejdź do zasobu Microsoft.Network/privateEndpoints w witrynie Azure Portal (nie do zasobu usługi Key Vault)
    3. Na stronie Przegląd Wyszukaj interfejs sieciowy i kliknij ten link. 
    4. Link zostanie wyświetlony przegląd zasobu karty sieciowej zawierającego prywatny adres IP właściwości. 
    5. Sprawdź, czy jest to poprawny adres IP określony w rekordzie A.

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

> [!NOTE]
> Liczba magazynów kluczy z włączonymi prywatnymi punktami końcowymi na subskrypcję jest przystosowanym limitem. Limit przedstawiony poniżej jest domyślnym limitem. Jeśli chcesz poprosić o zwiększenie limitu dla usługi, Wyślij wiadomość e-mail na adres akv-privatelink@microsoft.com . Te żądania będą zatwierdzane w przypadku poszczególnych przypadków.

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia**: prywatny punkt końcowy dla Azure Key Vault jest dostępny tylko w publicznych regionach platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na Key Vault**: 64.

**Domyślna liczba magazynów kluczy z prywatnymi punktami końcowymi na subskrypcję**: 400.

Aby uzyskać więcej informacji, zobacz [usługa Azure Private Link Service: ograniczenia](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnym łączu platformy Azure](../../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [Azure Key Vault](overview.md)
