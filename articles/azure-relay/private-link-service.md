---
title: Integracja Azure Relay z usługą konsolidacji prywatnej platformy Azure
description: Dowiedz się, jak zintegrować Azure Relay z usługą Azure Private Link Service
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 10d82fe8e272ed18dcc339830dfef0f71d4b2ddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263883"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>Integracja Azure Relay z prywatnym łączem platformy Azure (wersja zapoznawcza)
**Usługa link prywatny** platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem prywatnego punktu końcowego w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link (wersja zapoznawcza)?](../private-link/private-link-overview.md)

**Prywatny punkt końcowy** to interfejs sieciowy, który pozwala obciążom uruchomionym w sieci wirtualnej łączenie się prywatnie i bezpiecznie z usługą, która ma **prywatny zasób linku** (na przykład przestrzeń nazw przekaźnika). Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, ExpressRoute, połączenia sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą przechodzi przez sieć szkieletową firmy Microsoft w celu wyeliminowania zagrożeń z publicznego Internetu. Możesz zapewnić poziom szczegółowości w kontroli dostępu, zezwalając na połączenia z określonymi przestrzeniami nazw Azure Relay. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Dodawanie prywatnego punktu końcowego przy użyciu Azure Portal

### <a name="prerequisites"></a>Wymagania wstępne
Aby zintegrować Azure Relay przestrzeń nazw z prywatnym łączem platformy Azure (wersja zapoznawcza), potrzebne są następujące jednostki lub uprawnienia:

- Przestrzeń nazw Azure Relay.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej.
- Uprawnienia właściciela lub współautora w sieci wirtualnej.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. W przypadku wybrania regionu dla prywatnego punktu końcowego przy użyciu portalu program automatycznie odfiltruje tylko te sieci wirtualne, które znajdują się w tym regionie. Przestrzeń nazw może znajdować się w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

### <a name="steps"></a>Kroki
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia nowej przestrzeni nazw Azure Relay i w niej jednostek, zobacz [Tworzenie przestrzeni nazw Azure Relay przy użyciu Azure Portal](relay-create-namespace-portal.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Na pasku wyszukiwania wpisz ciąg **przekaźników**.
3. Wybierz **przestrzeń nazw** z listy, do której chcesz dodać prywatny punkt końcowy.
4. Wybierz kartę **Sieć** w obszarze **Ustawienia**.
5. Wybierz kartę **prywatne połączenia punktu końcowego (wersja zapoznawcza)** w górnej części strony
6. Wybierz przycisk **+ prywatny punkt końcowy** w górnej części strony.

    ![Przycisk dodawania prywatnego punktu końcowego](./media/private-link-service/add-private-endpoint-button.png)
7. Na stronie **podstawowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję platformy Azure** , w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów** dla prywatnego zasobu punktu końcowego.
    3. Wprowadź **nazwę** prywatnego punktu końcowego. 
    5. Wybierz **region** dla prywatnego punktu końcowego. Prywatny punkt końcowy musi znajdować się w tym samym regionie, w którym znajduje się sieć wirtualna, ale może znajdować się w innym regionie niż przestrzeń nazw Azure Relay, z którą nawiązujesz połączenie. 
    6. Wybierz pozycję **Dalej: przycisk >zasobu ** w dolnej części strony.

        ![Tworzenie prywatnego punktu końcowego — Strona podstawy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stronie **zasób** wykonaj następujące kroki:
    1. W przypadku metody połączenia, jeśli wybierzesz opcję **Połącz z zasobem platformy Azure w moim katalogu**, masz uprawnienia właściciela lub współautora do przestrzeni nazw i że przestrzeń nazw znajduje się w tym samym katalogu, co prywatny punkt końcowy, wykonaj następujące kroki: 
        1. Wybierz **subskrypcję platformy Azure** , w której znajduje się **przestrzeń nazw Azure Relay** . 
        2. W polu **Typ zasobu**wybierz pozycję **Microsoft. Relay/przestrzenie nazw** dla **typu zasobu**.
        3. W obszarze **zasób**wybierz przestrzeń nazw usługi Relay z listy rozwijanej. 
        4. Upewnij się, że **docelowy podzasób** jest ustawiony na **przestrzeń nazw**.
        5. Wybierz pozycję **Dalej: przycisk >konfiguracji ** w dolnej części strony. 
        
            ![Tworzenie prywatnego punktu końcowego — Strona zasobów](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. W przypadku wybrania opcji **Połącz z zasobem platformy Azure według identyfikatora zasobu lub aliasu** , ponieważ przestrzeń nazw nie znajduje się w tym samym katalogu co prywatny punkt końcowy, wykonaj następujące kroki:
        1. Wprowadź **Identyfikator zasobu** lub **alias**. Może to być identyfikator zasobu lub alias, który ktoś udostępni Tobie. Najprostszym sposobem uzyskania identyfikatora zasobu jest przejście do przestrzeni nazw Azure Relay w Azure Portal i skopiowanie fragmentu identyfikatora URI rozpoczynającego się od `/subscriptions/` . Oto przykład: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. W przypadku **docelowego zasobu podrzędnego**wprowadź **przestrzeń nazw**. Jest to typ zasobu podrzędnego, do którego Twój prywatny punkt końcowy może uzyskać dostęp.
        3. obowiązkowe Wprowadź **komunikat żądania**. Właściciel zasobu widzi ten komunikat podczas zarządzania połączeniem prywatnego punktu końcowego.
        4. Następnie wybierz pozycję **Dalej: przycisk >konfiguracji ** w dolnej części strony.

            ![Tworzenie prywatnego punktu końcowego — Łączenie przy użyciu identyfikatora zasobu](./media/private-link-service/connect-resource-id.png)
9. Na stronie **Konfiguracja** wybierz podsieć w sieci wirtualnej, w której chcesz wdrożyć prywatny punkt końcowy. 
    1. Wybierz **sieć wirtualną**. Na liście rozwijanej są wyświetlane tylko sieci wirtualne w aktualnie wybranej subskrypcji i lokalizacji. 
    2. Wybierz **podsieć** w wybranej sieci wirtualnej. 
    3. Włącz **integrację z prywatną strefą DNS** , jeśli chcesz zintegrować prywatny punkt końcowy z prywatną strefą DNS. 
    
        Aby połączyć się prywatnie z prywatnym punktem końcowym, potrzebny jest rekord DNS. Zalecamy integrację prywatnego punktu końcowego z **prywatną strefą DNS**. Możesz również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych. Aby uzyskać więcej informacji, zobacz [Konfiguracja DNS prywatnego punktu końcowego platformy Azure](../private-link/private-endpoint-dns.md). W tym przykładzie wybrano opcję **integracja z prywatną strefą DNS** i dla Ciebie zostanie utworzona prywatna strefa DNS. 
    3. Wybierz pozycję **Dalej: tagi >** przycisk w dolnej części strony. 

        ![Tworzenie prywatnego punktu końcowego — Strona konfiguracji](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stronie **Tagi** Utwórz dowolne Tagi (nazwy i wartości), które chcesz skojarzyć z prywatnym punktem końcowym i prywatną strefą DNS (jeśli została włączona opcja). Następnie wybierz przycisk **Przegląd + Utwórz** u dołu strony. 
11. Na stronie **Przegląd i tworzenie**Przejrzyj wszystkie ustawienia, a następnie wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.
    
    ![Tworzenie prywatnego punktu końcowego — przegląd i Tworzenie strony](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Na stronie **prywatny punkt końcowy** można zobaczyć stan połączenia z prywatnym punktem końcowym. Jeśli jesteś właścicielem przestrzeni nazw przekaźnika lub masz dostęp do usługi zarządzania nad nią i wybrano opcję **Połącz z zasobem platformy Azure w katalogu my** dla **metody połączenia**, połączenie punktu końcowego powinno być **zatwierdzane**domyślnie. Jeśli jest w stanie **oczekiwania** , zobacz sekcję [zarządzanie prywatnymi punktami końcowymi przy użyciu Azure Portal](#manage-private-endpoints-using-azure-portal) .

    ![Strona prywatnego punktu końcowego](./media/private-link-service/private-endpoint-page.png)
13. Wróć do strony **sieci** **obszaru nazw**i przejdź do karty **Private Endpoint Connections (wersja zapoznawcza)** . Powinien zostać wyświetlony utworzony prywatny punkt końcowy. 

    ![Utworzono prywatny punkt końcowy](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Dodawanie prywatnego punktu końcowego przy użyciu programu PowerShell
W poniższym przykładzie pokazano, jak za pomocą Azure PowerShell utworzyć połączenie prywatnego punktu końcowego z przestrzenią nazw Azure Relay.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. Przestrzeń nazw Azure Relay może znajdować się w innym regionie. Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Zarządzanie prywatnymi punktami końcowymi przy użyciu Azure Portal

Podczas tworzenia prywatnego punktu końcowego należy zatwierdzić połączenie. Jeśli zasób (przestrzeń nazw przekaźnika), dla którego tworzysz prywatny punkt końcowy, znajduje się w katalogu, możesz zatwierdzić żądanie połączenia, pod warunkiem, że masz uprawnienia do zarządzania uprawnieniami za pośrednictwem przestrzeni nazw usługi Relay. Jeśli łączysz się z przestrzenią nazw usługi Relay, dla której nie masz dostępu do zarządzania, musisz poczekać, aż właściciel tego zasobu zatwierdzi żądanie połączenia.

Istnieją cztery Stany aprowizacji:

| Akcja usługi | Stan prywatnego punktu końcowego klienta usługi | Opis |
|--|--|--|
| Brak | Oczekiwanie | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela przestrzeni nazw Azure Relay. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela przestrzeni nazw Azure Relay. |
| Usuń | Odłączony | Połączenie zostało usunięte przez właściciela przestrzeni nazw Azure Relay, prywatny punkt końcowy zmieni się na format i powinien zostać usunięty do oczyszczenia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Zatwierdź, Odrzuć lub Usuń połączenie prywatnego punktu końcowego

1. Zaloguj się do Portalu Azure.
1. Na pasku wyszukiwania wpisz w **Relay**.
1. Wybierz **przestrzeń nazw** , którą chcesz zarządzać.
1. Wybierz kartę **Sieć** .
5. Przejdź do odpowiedniej sekcji poniżej w zależności od operacji, którą chcesz wykonać: Zatwierdź, Odrzuć lub Usuń. 

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdź połączenie prywatnego punktu końcowego

1. Jeśli istnieją oczekujące połączenia, w stanie aprowizacji zostanie wyświetlone połączenie z **oczekującą** . 
2. Wybierz **prywatny punkt końcowy** , który chcesz zatwierdzić
3. Wybierz przycisk **Zatwierdź** .

    ![Zatwierdź prywatny punkt końcowy](./media/private-link-service/private-endpoint-approve.png)
4. Na stronie **zatwierdzanie połączenia** wprowadź opcjonalny **komentarz**, a następnie wybierz pozycję **tak**. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 

    ![Zatwierdź stronę połączenia](./media/private-link-service/approve-connection-page.png)
5. Powinien zostać wyświetlony stan połączenia na liście zmieniono na **zatwierdzone**.

### <a name="reject-a-private-endpoint-connection"></a>Odrzuć połączenie prywatnego punktu końcowego

1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, czy jest to oczekujące żądanie lub istniejące połączenie, które zostało zatwierdzone wcześniej, wybierz połączenie punktu końcowego, a następnie kliknij przycisk **Odrzuć** .

    ![Przycisk Odrzuć](./media/private-link-service/private-endpoint-reject.png)
2. Na stronie **Odrzuć połączenie** wprowadź opcjonalny komentarz, a następnie wybierz pozycję **tak**. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 

    ![Odrzuć stronę połączenia](./media/private-link-service/reject-connection-page.png)
3. Stan połączenia powinien zostać wyświetlony na liście zmieniono **odrzucone**.


### <a name="remove-a-private-endpoint-connection"></a>Usuń połączenie prywatnego punktu końcowego

1. Aby usunąć połączenie z prywatnym punktem końcowym, wybierz je z listy i wybierz pozycję **Usuń** na pasku narzędzi. 

    ![Przycisk Usuń](./media/private-link-service/remove-endpoint.png)
2. Na stronie **Usuwanie połączenia** wybierz pozycję **tak** , aby potwierdzić usunięcie prywatnego punktu końcowego. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 

    ![Usuń stronę połączenia](./media/private-link-service/delete-connection-page.png)
3. Powinien zostać wyświetlony stan zmieniono na **rozłączony**. Następnie punkt końcowy zostanie wyświetlony na liście. 

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdź, czy połączenie z linkiem prywatnym działa
Należy sprawdzić, czy zasoby w sieci wirtualnej prywatnego punktu końcowego nawiązują połączenie z przestrzenią nazw Azure Relay przy użyciu swojego prywatnego adresu IP.

W przypadku tego testu Utwórz maszynę wirtualną, wykonując czynności opisane w sekcji [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na karcie **Sieć** : 

1. Określ **sieć wirtualną** i **podsieć**. Musisz wybrać Virtual Network, na którym został wdrożony prywatny punkt końcowy.
2. Określ zasób **publicznego adresu IP** .
3. Dla **sieciowej grupy zabezpieczeń karty sieciowej**wybierz **Brak**.
4. W obszarze **równoważenie obciążenia**wybierz pozycję **nie**.

Połącz się z maszyną wirtualną i Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

Powinien pojawić się wynik podobny do poniższego. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

### <a name="design-considerations"></a>Zagadnienia dotyczące projektowania
- Prywatny punkt końcowy dla Azure Relay jest w **publicznej wersji zapoznawczej**. 
- Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link (wersja zapoznawcza)](https://azure.microsoft.com/pricing/details/private-link/).

### <a name="limitations"></a>Ograniczenia 
- Maksymalna liczba prywatnych punktów końcowych na Azure Relay przestrzeń nazw: 64.
- Maksymalna liczba Azure Relay przestrzeni nazw z prywatnymi punktami końcowymi na subskrypcję: 64.
- Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) i trasy User-Defined nie mają zastosowania do prywatnego punktu końcowego. Aby uzyskać więcej informacji, zobacz [usługa Azure Private Link Service: ograniczenia](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Private link (wersja zapoznawcza)](../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [Azure Relay](relay-what-is-it.md)
