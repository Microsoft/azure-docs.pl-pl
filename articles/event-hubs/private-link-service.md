---
title: Integrowanie Event Hubs platformy Azure z usługą prywatnych linków platformy Azure
description: Dowiedz się, jak zintegrować usługę Azure Event Hubs z usługą Azure Private Link Service
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 996779e103dae2d2d950f447d2ac72667fc9e754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94427755"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Zezwalaj na dostęp do przestrzeni nazw platformy Azure Event Hubs za pośrednictwem prywatnych punktów końcowych 
Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Event Hubs, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

Prywatny punkt końcowy to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link?](../private-link/private-link-overview.md)

> [!WARNING]
> Włączenie prywatnych punktów końcowych może uniemożliwić innym usługom platformy Azure współdziałanie z Event Hubs.  Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej. Jako wyjątek, można zezwolić na dostęp do Event Hubs zasobów z niektórych zaufanych usług nawet wtedy, gdy są włączone prywatne punkty końcowe. Aby zapoznać się z listą zaufanych usług, zobacz temat [usługi zaufane](#trusted-microsoft-services).

>[!NOTE]
> Ta funkcja jest obsługiwana dla warstw **standardowa** i **dedykowana** . Ta wartość nie jest obsługiwana w warstwie **podstawowa** .

## <a name="add-a-private-endpoint-using-azure-portal"></a>Dodawanie prywatnego punktu końcowego przy użyciu Azure Portal

### <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować Event Hubs przestrzeń nazw z linkiem prywatnym platformy Azure, potrzebne są następujące jednostki lub uprawnienia:

- Przestrzeń nazw Event Hubs.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej. Możesz użyć podsieci **domyślnej** . 
- Uprawnienia właściciela lub współautora dla przestrzeni nazw i sieci wirtualnej.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. W przypadku wybrania regionu dla prywatnego punktu końcowego przy użyciu portalu program automatycznie odfiltruje tylko te sieci wirtualne, które znajdują się w tym regionie. Przestrzeń nazw może znajdować się w innym regionie.

Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

### <a name="steps"></a>Kroki
Jeśli masz już Event Hubs przestrzeń nazw, możesz utworzyć połączenie prywatne, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Na pasku wyszukiwania wpisz w **centrach zdarzeń**.
3. Wybierz **przestrzeń nazw** z listy, do której chcesz dodać prywatny punkt końcowy.
4. Wybierz pozycję **Sieć** w obszarze **Ustawienia** w menu po lewej stronie.

    > [!NOTE]
    > Karta **Sieć** jest wyświetlana tylko dla **standardowych** lub **dedykowanych** przestrzeni nazw. 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Karta sieci — opcja wybrane sieci" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > Domyślnie wybrana jest opcja **wybrane sieci** . Jeśli nie określisz reguły zapory IP lub nie dodasz sieci wirtualnej, można uzyskać dostęp do przestrzeni nazw za pośrednictwem publicznej sieci Internet. 
1. Wybierz kartę **połączenia prywatnego punktu końcowego** w górnej części strony. 
1. Wybierz przycisk **+ prywatny punkt końcowy** w górnej części strony.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Strona sieci — karta połączenia prywatnego punktu końcowego — Dodawanie linku do prywatnego punktu końcowego":::
7. Na stronie **podstawowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję platformy Azure** , w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów** dla prywatnego zasobu punktu końcowego.
    3. Wprowadź **nazwę** prywatnego punktu końcowego. 
    5. Wybierz **region** dla prywatnego punktu końcowego. Prywatny punkt końcowy musi znajdować się w tym samym regionie, w którym znajduje się sieć wirtualna, ale może znajdować się w innym regionie niż zasób link prywatny, z którym nawiązujesz połączenie. 
    6. Wybierz pozycję **Dalej: przycisk >zasobu** w dolnej części strony.

        ![Tworzenie prywatnego punktu końcowego — Strona podstawy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stronie **zasób** wykonaj następujące kroki:
    1. W przypadku metody połączenia w przypadku wybrania opcji **Połącz z zasobem platformy Azure w katalogu my** wykonaj następujące czynności: 
        1. Wybierz **subskrypcję platformy Azure** , w której znajduje się **przestrzeń nazw Event Hubs** . 
        2. W polu **Typ zasobu** wybierz pozycję **Microsoft. EventHub/przestrzenie nazw** dla **typu zasobu**.
        3. W obszarze **zasób** wybierz Event Hubs przestrzeń nazw z listy rozwijanej. 
        4. Upewnij się, że **docelowy podzasób** jest ustawiony na **przestrzeń nazw**.
        5. Wybierz pozycję **Dalej: przycisk >konfiguracji** w dolnej części strony. 
        
            ![Tworzenie prywatnego punktu końcowego — Strona zasobów](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. W przypadku wybrania opcji **Połącz z zasobem platformy Azure według identyfikatora zasobu lub aliasu** wykonaj następujące kroki:
        1. Wprowadź **Identyfikator zasobu** lub **alias**. Może to być identyfikator zasobu lub alias, który ktoś udostępni Tobie. Najprostszym sposobem uzyskania identyfikatora zasobu jest przejście do przestrzeni nazw Event Hubs w Azure Portal i skopiowanie fragmentu identyfikatora URI rozpoczynającego się od `/subscriptions/` . Przykład można znaleźć na poniższym obrazie. 
        2. W przypadku **docelowego zasobu podrzędnego** wprowadź **przestrzeń nazw**. Jest to typ zasobu podrzędnego, do którego Twój prywatny punkt końcowy może uzyskać dostęp.
        3. obowiązkowe Wprowadź **komunikat żądania**. Właściciel zasobu widzi ten komunikat podczas zarządzania połączeniem prywatnego punktu końcowego.
        4. Następnie wybierz pozycję **Dalej: przycisk >konfiguracji** w dolnej części strony.

            ![Tworzenie prywatnego punktu końcowego — Łączenie przy użyciu identyfikatora zasobu](./media/private-link-service/connect-resource-id.png)
9. Na stronie **Konfiguracja** wybierz podsieć w sieci wirtualnej, w której chcesz wdrożyć prywatny punkt końcowy. 
    1. Wybierz **sieć wirtualną**. Na liście rozwijanej są wyświetlane tylko sieci wirtualne w aktualnie wybranej subskrypcji i lokalizacji. 
    2. Wybierz **podsieć** w wybranej sieci wirtualnej. 
    3. Wybierz pozycję **Dalej: tagi >** przycisk w dolnej części strony. 

        ![Tworzenie prywatnego punktu końcowego — Strona konfiguracji](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Na stronie **Tagi** Utwórz dowolne Tagi (nazwy i wartości), które chcesz skojarzyć z prywatnym zasobem punktu końcowego. Następnie wybierz przycisk **Przegląd + Utwórz** u dołu strony. 
11. Na stronie **Przegląd i tworzenie** Przejrzyj wszystkie ustawienia, a następnie wybierz pozycję **Utwórz** , aby utworzyć prywatny punkt końcowy.
    
    ![Tworzenie prywatnego punktu końcowego — przegląd i Tworzenie strony](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Upewnij się, że utworzone połączenie prywatnego punktu końcowego jest widoczne na liście punktów końcowych. W tym przykładzie prywatny punkt końcowy jest zaakceptowany, ponieważ nawiązano połączenie z zasobem platformy Azure w Twoim katalogu i masz wystarczające uprawnienia. 

    ![Utworzono prywatny punkt końcowy](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Aby umożliwić zaufanym usługom dostęp do przestrzeni nazw, przejdź do karty **zapory i sieci wirtualne** na stronie **Sieć** , a następnie wybierz opcję **tak** , aby **zezwolić zaufanym usługom firmy Microsoft na pominięcie tej zapory?**. 

## <a name="add-a-private-endpoint-using-powershell"></a>Dodawanie prywatnego punktu końcowego przy użyciu programu PowerShell
Poniższy przykład pokazuje, jak za pomocą Azure PowerShell utworzyć połączenie prywatnego punktu końcowego. Dla Ciebie nie jest tworzony dedykowany klaster. Wykonaj kroki opisane w [tym artykule](event-hubs-dedicated-cluster-create-portal.md) , aby utworzyć dedykowany klaster Event Hubs. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>Skonfiguruj prywatną strefę DNS
Utwórz prywatną strefę DNS dla domeny Event Hubs i Utwórz link skojarzenia z siecią wirtualną:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Zarządzanie prywatnymi punktami końcowymi przy użyciu Azure Portal

Podczas tworzenia prywatnego punktu końcowego należy zatwierdzić połączenie. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy, znajduje się w katalogu, możesz zatwierdzić żądanie połączenia pod warunkiem, że masz wystarczające uprawnienia. Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi Twoje żądanie połączenia.

Istnieją cztery Stany aprowizacji:

| Akcja w usłudze | Stan prywatnego punktu końcowego klienta usługi | Opis |
|--|--|--|
| Brak | Oczekiwanie | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie przez właściciela zasobu link prywatny. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało automatycznie lub ręcznie zatwierdzone i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu link prywatny. |
| Usuń | Odłączony | Połączenie zostało usunięte przez właściciela zasobu link prywatny, a prywatny punkt końcowy zmieni się na format i powinien zostać usunięty do oczyszczenia. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Zatwierdź, Odrzuć lub Usuń połączenie prywatnego punktu końcowego

1. Zaloguj się w witrynie Azure Portal.
2. Na pasku wyszukiwania wpisz w **centrach zdarzeń**.
3. Wybierz **przestrzeń nazw** , którą chcesz zarządzać.
4. Wybierz kartę **Sieć**.
5. Przejdź do odpowiedniej sekcji poniżej w zależności od operacji, którą chcesz wykonać: Zatwierdź, Odrzuć lub Usuń.

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdź połączenie prywatnego punktu końcowego
1. Jeśli istnieją oczekujące połączenia, w stanie aprowizacji zostanie wyświetlone połączenie z **oczekującą** . 
2. Wybierz **prywatny punkt końcowy** , który chcesz zatwierdzić
3. Wybierz przycisk **Zatwierdź** .

    ![Zatwierdź prywatny punkt końcowy](./media/private-link-service/approve-private-endpoint.png)
4. Na stronie **zatwierdzanie połączenia** Dodaj komentarz (opcjonalnie), a następnie wybierz pozycję **tak**. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 
5. Stan połączenia prywatnego punktu końcowego powinien zostać wyświetlony na liście zmieniono na **zatwierdzone**. 

### <a name="reject-a-private-endpoint-connection"></a>Odrzuć połączenie prywatnego punktu końcowego

1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, niezależnie od tego, czy jest to oczekujące żądanie, czy istniejące połączenie, wybierz połączenie i kliknij przycisk **Odrzuć** .

    ![Odrzuć prywatny punkt końcowy](./media/private-link-service/private-endpoint-reject-button.png)
2. Na stronie **Odrzuć połączenie** Wprowadź komentarz (opcjonalnie), a następnie wybierz pozycję **tak**. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 
3. Stan połączenia prywatnego punktu końcowego powinien zostać wyświetlony na liście zmieniono na **odrzucony**. 

### <a name="remove-a-private-endpoint-connection"></a>Usuń połączenie prywatnego punktu końcowego

1. Aby usunąć połączenie z prywatnym punktem końcowym, wybierz je z listy i wybierz pozycję **Usuń** na pasku narzędzi.
2. Na stronie **Usuwanie połączenia** wybierz pozycję **tak** , aby potwierdzić usunięcie prywatnego punktu końcowego. Jeśli wybierzesz opcję **nie**, nic się nie dzieje.
3. Powinien zostać wyświetlony stan zmieniono na **rozłączony**. Następnie punkt końcowy zostanie wyświetlony na liście.

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdź, czy połączenie z linkiem prywatnym działa

Należy sprawdzić, czy zasoby w sieci wirtualnej prywatnego punktu końcowego nawiązują połączenie z przestrzenią nazw Event Hubs przy użyciu prywatnego adresu IP i czy mają poprawną integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując czynności opisane w sekcji [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na karcie **Sieć** : 

1. Określ **sieć wirtualną** i **podsieć**. Musisz wybrać Virtual Network, na którym został wdrożony prywatny punkt końcowy.
2. Określ zasób **publicznego adresu IP** .
3. Dla **sieciowej grupy zabezpieczeń karty sieciowej** wybierz **Brak**.
4. W obszarze **równoważenie obciążenia** wybierz pozycję **nie**.

Połącz się z maszyną wirtualną, Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

Powinien pojawić się wynik podobny do poniższego. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia**: Ta funkcja jest dostępna we wszystkich regionach publicznych platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na Event Hubs przestrzeń nazw**: 120.

Aby uzyskać więcej informacji, zobacz [usługa Azure Private Link Service: ograniczenia](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnym łączu platformy Azure](../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [usłudze Azure Event Hubs](event-hubs-about.md)
