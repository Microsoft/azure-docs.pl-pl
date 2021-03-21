---
title: Integracja Azure Service Bus z usługą konsolidacji prywatnej platformy Azure
description: Dowiedz się, jak zintegrować Azure Service Bus z usługą Azure Private Link Service
author: spelluru
ms.author: spelluru
ms.date: 10/07/2020
ms.topic: article
ms.openlocfilehash: 66de9a4ff65c73264257cb6f7f215fc15820c95f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94427151"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>Zezwalaj na dostęp do przestrzeni nazw Azure Service Bus za pośrednictwem prywatnych punktów końcowych
Usługa link prywatny platformy Azure umożliwia dostęp do usług platformy Azure (na przykład Azure Service Bus, Azure Storage i Azure Cosmos DB) oraz hostowanych usług klienta i partnerskich platformy Azure za pośrednictwem **prywatnego punktu końcowego** w sieci wirtualnej.

> [!IMPORTANT]
> Ta funkcja jest obsługiwana w warstwie **premium** Azure Service Bus. Aby uzyskać więcej informacji na temat warstwy Premium, zobacz artykuł [Service Bus warstwy Premium i Standard Messaging](service-bus-premium-messaging.md) .

Prywatny punkt końcowy to interfejs sieciowy, który nawiązuje połączenie prywatnie i bezpiecznie z usługą obsługiwanej przez link prywatny platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, co skutecznie doprowadza usługę do sieci wirtualnej. Cały ruch do usługi może być kierowany przez prywatny punkt końcowy, dlatego nie są konieczne żadne bramy, urządzenia NAT, połączenia ExpressRoute lub sieci VPN ani publiczne adresy IP. Ruch między siecią wirtualną a usługą odbywa się za pośrednictwem sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Można nawiązać połączenie z wystąpieniem zasobu platformy Azure, zapewniając najwyższy poziom szczegółowości kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Private link?](../private-link/private-link-overview.md)

>[!WARNING]
> Implementacja prywatnych punktów końcowych może uniemożliwić innym usługom platformy Azure współdziałanie z Service Bus. Jako wyjątek, można zezwolić na dostęp do Service Bus zasobów z niektórych zaufanych usług nawet wtedy, gdy są włączone prywatne punkty końcowe. Aby zapoznać się z listą zaufanych usług, zobacz temat [usługi zaufane](#trusted-microsoft-services).
>
> Następujące usługi firmy Microsoft muszą znajdować się w sieci wirtualnej
> - Azure App Service
> - Azure Functions



## <a name="add-a-private-endpoint-using-azure-portal"></a>Dodawanie prywatnego punktu końcowego przy użyciu Azure Portal

### <a name="prerequisites"></a>Wymagania wstępne

Aby zintegrować Service Bus przestrzeń nazw z linkiem prywatnym platformy Azure, potrzebne są następujące jednostki lub uprawnienia:

- Przestrzeń nazw Service Bus.
- Sieć wirtualna platformy Azure.
- Podsieć w sieci wirtualnej. Możesz użyć podsieci **domyślnej** . 
- Uprawnienia właściciela lub współautora dla przestrzeni nazw Service Bus i sieci wirtualnej.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. W przypadku wybrania regionu dla prywatnego punktu końcowego przy użyciu portalu program automatycznie odfiltruje tylko te sieci wirtualne, które znajdują się w tym regionie. Przestrzeń nazw Service Bus może znajdować się w innym regionie. Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

### <a name="steps"></a>kroki

Jeśli masz już istniejącą przestrzeń nazw, możesz utworzyć prywatny punkt końcowy, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Na pasku wyszukiwania wpisz w **Service Bus**.
3. Wybierz **przestrzeń nazw** z listy, do której chcesz dodać prywatny punkt końcowy.
2. W menu po lewej stronie wybierz opcję **Sieć** w obszarze **Ustawienia**. 

    > [!NOTE]
    > Karta **Sieć** zawiera tylko obszary nazw w **warstwie Premium** .  
    
    Domyślnie wybrana jest opcja **wybrane sieci** . Jeśli nie dodasz co najmniej jednej reguły zapory IP lub sieci wirtualnej na tej stronie, można uzyskać dostęp do przestrzeni nazw za pośrednictwem publicznej sieci Internet (przy użyciu klucza dostępu).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Strona sieci — domyślna" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    W przypadku wybrania opcji **wszystkie sieci** Service Bus przestrzeń nazw akceptuje połączenia z dowolnego adresu IP (przy użyciu klucza dostępu). To ustawienie domyślne jest równoważne z regułą akceptującą zakres adresów IP 0.0.0.0/0. 

    ![Zapora — wybrana opcja Wszystkie sieci](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. Aby zezwolić na dostęp do przestrzeni nazw za pośrednictwem prywatnych punktów końcowych, wybierz kartę **połączenia prywatnego punktu końcowego** w górnej części strony.
6. Wybierz przycisk **+ prywatny punkt końcowy** w górnej części strony.

    ![Przycisk dodawania prywatnego punktu końcowego](./media/private-link-service/private-link-service-3.png)
7. Na stronie **podstawowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję platformy Azure** , w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów** dla prywatnego zasobu punktu końcowego.
    3. Wprowadź **nazwę** prywatnego punktu końcowego. 
    5. Wybierz **region** dla prywatnego punktu końcowego. Prywatny punkt końcowy musi znajdować się w tym samym regionie, w którym znajduje się sieć wirtualna, ale może znajdować się w innym regionie niż zasób link prywatny, z którym nawiązujesz połączenie. 
    6. Wybierz pozycję **Dalej: przycisk >zasobu** w dolnej części strony.

        ![Tworzenie prywatnego punktu końcowego — Strona podstawy](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Na stronie **zasób** wykonaj następujące kroki:
    1. W przypadku metody połączenia w przypadku wybrania opcji **Połącz z zasobem platformy Azure w katalogu my** wykonaj następujące czynności:   
        1. Wybierz **subskrypcję platformy Azure** , w której znajduje się **przestrzeń nazw Service Bus** . 
        2. W polu **Typ zasobu** wybierz pozycję **Microsoft. ServiceBus/Namespaces** dla **typu zasobu**.
        3. W obszarze **zasób** wybierz Service Bus przestrzeń nazw z listy rozwijanej. 
        4. Upewnij się, że **docelowy podzasób** jest ustawiony na **przestrzeń nazw**.
        5. Wybierz pozycję **Dalej: przycisk >konfiguracji** w dolnej części strony. 
        
            ![Tworzenie prywatnego punktu końcowego — Strona zasobów](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. W przypadku wybrania opcji **Połącz z zasobem platformy Azure według identyfikatora zasobu lub aliasu** wykonaj następujące kroki:
        1. Wprowadź **Identyfikator zasobu** lub **alias**. Może to być identyfikator zasobu lub alias, który ktoś udostępni Tobie. Najprostszym sposobem uzyskania identyfikatora zasobu jest przejście do przestrzeni nazw Service Bus w Azure Portal i skopiowanie fragmentu identyfikatora URI rozpoczynającego się od `/subscriptions/` . Przykład można znaleźć na poniższym obrazie. 
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
12. Upewnij się, że został utworzony prywatny punkt końcowy. Jeśli jesteś właścicielem zasobu i wybrano opcję **Połącz z zasobem platformy Azure w katalogu my** dla **metody połączenia**, połączenie punktu końcowego powinno być **zatwierdzane** domyślnie. Jeśli jest w stanie **oczekiwania** , zobacz sekcję [zarządzanie prywatnymi punktami końcowymi przy użyciu Azure Portal](#manage-private-endpoints-using-azure-portal) .

    ![Utworzono prywatny punkt końcowy](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="add-a-private-endpoint-using-powershell"></a>Dodawanie prywatnego punktu końcowego przy użyciu programu PowerShell
W poniższym przykładzie pokazano, jak za pomocą Azure PowerShell utworzyć połączenie prywatnego punktu końcowego z przestrzenią nazw Service Bus.

Prywatny punkt końcowy i Sieć wirtualna muszą znajdować się w tym samym regionie. Przestrzeń nazw Service Bus może znajdować się w innym regionie. Prywatny punkt końcowy używa prywatnego adresu IP w sieci wirtualnej.

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

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
1. Na pasku wyszukiwania wpisz w **Service Bus**.
1. Wybierz **przestrzeń nazw** , którą chcesz zarządzać.
1. Wybierz kartę **Sieć**.
5. Przejdź do odpowiedniej sekcji poniżej w zależności od operacji, którą chcesz wykonać: Zatwierdź, Odrzuć lub Usuń. 

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdź połączenie prywatnego punktu końcowego

1. Jeśli istnieją oczekujące połączenia, w stanie aprowizacji zostanie wyświetlone połączenie z **oczekującą** . 
2. Wybierz **prywatny punkt końcowy** , który chcesz zatwierdzić
3. Wybierz przycisk **Zatwierdź** .

    ![Zatwierdź prywatny punkt końcowy](./media/private-link-service/private-endpoint-approve.png)
4. Na stronie **zatwierdzanie połączenia** wprowadź opcjonalny **komentarz**, a następnie wybierz pozycję **tak**. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 

    ![Zatwierdź stronę połączenia](./media/private-link-service/approve-connection-page.png)
5. Powinien zostać wyświetlony stan połączenia na liście zmieniono na **zatwierdzone**. 

    ![Stan połączenia — zatwierdzone](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Odrzuć połączenie prywatnego punktu końcowego

1. Jeśli istnieją jakieś połączenia prywatnego punktu końcowego, które chcesz odrzucić, czy jest to oczekujące żądanie lub istniejące połączenie, które zostało zatwierdzone wcześniej, wybierz połączenie punktu końcowego, a następnie kliknij przycisk **Odrzuć** .

    ![Przycisk Odrzuć](./media/private-link-service/private-endpoint-reject.png)
2. Na stronie **Odrzuć połączenie** wprowadź opcjonalny komentarz, a następnie wybierz pozycję **tak**. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 

    ![Odrzuć stronę połączenia](./media/private-link-service/reject-connection-page.png)
3. Stan połączenia powinien zostać wyświetlony na liście zmieniono **odrzucone**. 

    ![Odrzucono punkt końcowy](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Usuń połączenie prywatnego punktu końcowego

1. Aby usunąć połączenie z prywatnym punktem końcowym, wybierz je z listy i wybierz pozycję **Usuń** na pasku narzędzi. 

    ![Przycisk Usuń](./media/private-link-service/remove-endpoint.png)
2. Na stronie **Usuwanie połączenia** wybierz pozycję **tak** , aby potwierdzić usunięcie prywatnego punktu końcowego. Jeśli wybierzesz opcję **nie**, nic się nie dzieje. 

    ![Usuń stronę połączenia](./media/private-link-service/delete-connection-page.png)
3. Powinien zostać wyświetlony stan zmieniono na **rozłączony**. Następnie punkt końcowy zostanie wyświetlony na liście. 

## <a name="validate-that-the-private-link-connection-works"></a>Sprawdź, czy połączenie z linkiem prywatnym działa

Należy sprawdzić, czy zasoby w sieci wirtualnej prywatnego punktu końcowego nawiązują połączenie z przestrzenią nazw Service Bus przy użyciu prywatnego adresu IP i czy mają poprawną integrację prywatnej strefy DNS.

Najpierw utwórz maszynę wirtualną, wykonując czynności opisane w sekcji [Tworzenie maszyny wirtualnej z systemem Windows w Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Na karcie **Sieć** : 

1. Określ **sieć wirtualną** i **podsieć**. Musisz wybrać Virtual Network, na którym został wdrożony prywatny punkt końcowy.
2. Określ zasób **publicznego adresu IP** .
3. Dla **sieciowej grupy zabezpieczeń karty sieciowej** wybierz **Brak**.
4. W obszarze **równoważenie obciążenia** wybierz pozycję **nie**.

Połącz się z maszyną wirtualną, Otwórz wiersz polecenia i uruchom następujące polecenie:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

Powinien pojawić się wynik podobny do poniższego. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

**Cennik**: Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).

**Ograniczenia**: Ta funkcja jest dostępna we wszystkich regionach publicznych platformy Azure.

**Maksymalna liczba prywatnych punktów końcowych na Service Bus przestrzeń nazw**: 120.

Aby uzyskać więcej informacji, zobacz [usługa Azure Private Link Service: ograniczenia](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [prywatnym łączu platformy Azure](../private-link/private-link-service-overview.md)
- Dowiedz się więcej o [Azure Service Bus](service-bus-messaging-overview.md)
