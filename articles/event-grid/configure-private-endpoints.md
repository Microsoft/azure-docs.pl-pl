---
title: Konfigurowanie prywatnych punktów końcowych dla Azure Event Grid lub domen
description: W tym artykule opisano sposób konfigurowania prywatnych punktów końcowych dla Azure Event Grid lub domeny.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770511"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Konfigurowanie prywatnych punktów końcowych dla Azure Event Grid lub domen
Za pomocą prywatnych [punktów końcowych można](../private-link/private-endpoint-overview.md) bezpiecznie zezwalać na ruch przychodzący zdarzeń bezpośrednio [](../private-link/private-link-overview.md) z sieci wirtualnej do tematów i domen za pośrednictwem łącza prywatnego bez konieczności korzystania z publicznego Internetu. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla tematu lub domeny. Aby uzyskać więcej informacji koncepcyjnych, zobacz [Zabezpieczenia sieci](network-security.md).

W tym artykule opisano sposób konfigurowania prywatnych punktów końcowych dla tematów lub domen.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal 
W tej sekcji pokazano, jak za pomocą Azure Portal utworzyć prywatny punkt końcowy dla tematu lub domeny.

> [!NOTE]
> Kroki przedstawione w tej sekcji są głównie związane z tematami. Podobne kroki można wykonać, aby utworzyć prywatne punkty końcowe dla **domen**. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do tematu lub domeny.
2. Przejdź do **karty Sieć** na stronie tematu. Wybierz **pozycję + Prywatny punkt końcowy** na pasku narzędzi.

    ![Dodawanie prywatnego punktu końcowego](./media/configure-private-endpoints/add-button.png)
2. Na **stronie Podstawowe** wykonaj następujące kroki: 
    1. Wybierz **subskrypcję platformy Azure,** w której chcesz utworzyć prywatny punkt końcowy. 
    2. Wybierz **grupę zasobów platformy Azure** dla prywatnego punktu końcowego. 
    3. Wprowadź **nazwę punktu** końcowego. 
    4. Wybierz **region punktu** końcowego. Prywatny punkt końcowy musi znajdować się w tym samym regionie co sieć wirtualna, ale może znajdować się w innym regionie niż zasób łącza prywatnego (w tym przykładzie temat usługi Event Grid). 
    5. Następnie wybierz **przycisk Dalej: >** zasobów w dolnej części strony. 

      ![Prywatny punkt końcowy — strona podstawowe](./media/configure-private-endpoints/basics-page.png)
3. Na stronie **Zasób** wykonaj następujące kroki: 
    1. W przypadku metody połączenia, jeśli wybierzesz pozycję Połącz z **zasobem platformy Azure w** moim katalogu, wykonaj następujące kroki. W tym przykładzie pokazano, jak nawiązać połączenie z zasobem platformy Azure w katalogu. 
        1. Wybierz **subskrypcję platformy Azure,** w której znajduje **się temat/domena.** 
        1. W **przypadku opcji Typ** zasobu wybierz pozycję **Microsoft.EventGrid/topics** lub **Microsoft.EventGrid/domains** dla **opcji Typ zasobu.**
        2. W **przypadku** opcji Zasób wybierz temat/domenę z listy rozwijanej. 
        3. Upewnij się, **że docelowy podźródło** jest ustawione na **temat** **lub** domenę (na podstawie wybranego typu zasobu).    
        4. Wybierz **przycisk >** konfiguracji w dolnej części strony. 

            ![Zrzut ekranu przedstawiający stronę "Tworzenie prywatnego punktu końcowego — zasób".](./media/configure-private-endpoints/resource-page.png)
    2. Jeśli wybierzesz **pozycję Połącz z zasobem przy użyciu identyfikatora zasobu** lub aliasu, wykonaj następujące kroki:
        1. Wprowadź identyfikator zasobu. Na przykład: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. W **przypadku zasobu** wprowadź temat **lub** **domenę**. 
        3. (opcjonalnie) Dodaj komunikat żądania. 
        4. Wybierz **przycisk >** konfiguracji w dolnej części strony. 

            ![Prywatny punkt końcowy — strona zasobu](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Na stronie **Konfiguracja** wybierz podsieć w sieci wirtualnej, w której chcesz wdrożyć prywatny punkt końcowy. 
    1. Wybierz sieć **wirtualną**. Na liście rozwijanej są wyświetlane tylko sieci wirtualne w aktualnie wybranej subskrypcji i lokalizacji. 
    2. Wybierz **podsieć** w wybranej sieci wirtualnej. 
    3. Wybierz **pozycję Dalej: >** tagów w dolnej części strony. 

    ![Prywatny punkt końcowy — strona konfiguracji](./media/configure-private-endpoints/configuration-page.png)
5. Na stronie **Tagi** utwórz wszystkie tagi (nazwy i wartości), które chcesz skojarzyć z zasobem prywatnego punktu końcowego. Następnie wybierz **przycisk Przejrzyj i utwórz** w dolnej części strony. 
6. Na stronie **Przeglądanie + tworzenie** przejrzyj wszystkie ustawienia i wybierz pozycję **Utwórz,** aby utworzyć prywatny punkt końcowy. 

    ![Prywatny punkt końcowy — przejrzyj & tworzenia](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Zarządzanie połączeniem z linkami prywatnymi

Podczas tworzenia prywatnego punktu końcowego połączenie musi zostać zatwierdzone. Jeśli zasób, dla którego tworzysz prywatny punkt końcowy, znajduje się w katalogu, możesz zatwierdzić żądanie połączenia, jeśli masz wystarczające uprawnienia. Jeśli łączysz się z zasobem platformy Azure w innym katalogu, musisz poczekać, aż właściciel tego zasobu zatwierdzi żądanie połączenia.

Istnieją cztery stany aprowizowania:

| Akcja w usłudze | Stan prywatnego punktu końcowego konsumenta usługi | Opis |
|--|--|--|
| Brak | Oczekiwanie | Połączenie jest tworzone ręcznie i oczekuje na zatwierdzenie od właściciela zasobu prywatnego linku. |
| Zatwierdzenie | Approved (Zatwierdzono) | Połączenie zostało zatwierdzone automatycznie lub ręcznie i jest gotowe do użycia. |
| Reject | Odrzucone | Połączenie zostało odrzucone przez właściciela zasobu linku prywatnego. |
| Usuń | Odłączony | Połączenie zostało usunięte przez właściciela zasobu linku prywatnego, prywatny punkt końcowy staje się wartościowy i powinien zostać usunięty w celu oczyszczenia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Jak zarządzać połączeniem prywatnego punktu końcowego
W poniższych sekcjach przedstawiono sposób zatwierdzania lub odrzucania połączenia prywatnego punktu końcowego. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania wpisz Event Grid **tematach** **lub Event Grid domen.**
1. Wybierz **temat lub** **domenę,** którą chcesz zarządzać.
1. Wybierz kartę **Sieć**.
1. Jeśli istnieją oczekujące połączenia, zobaczysz, że połączenie  jest wyświetlane ze stanem Oczekujące w stanie inicjowania obsługi. 

### <a name="to-approve-a-private-endpoint"></a>Aby zatwierdzić prywatny punkt końcowy
Możesz zatwierdzić prywatny punkt końcowy, który jest w stanie oczekiwania. Aby zatwierdzić, wykonaj następujące kroki: 

> [!NOTE]
> Kroki przedstawione w tej sekcji są przede wszystkim związane z tematami. Podobne kroki można wykonać, aby zatwierdzić prywatne punkty końcowe dla **domen**. 

1. Wybierz prywatny **punkt końcowy,** który chcesz zatwierdzić, a następnie wybierz pozycję **Zatwierdź** na pasku narzędzi.

    ![Prywatny punkt końcowy — stan oczekiwania](./media/configure-private-endpoints/pending.png)
1. W **oknie dialogowym Zatwierdzanie** połączenia wprowadź komentarz (opcjonalnie) i wybierz pozycję **Tak.** 

    ![Prywatny punkt końcowy — zatwierdzanie](./media/configure-private-endpoints/approve.png)
1. Upewnij się, że stan punktu końcowego to **Zatwierdzone.** 

    ![Prywatny punkt końcowy — stan zatwierdzony](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Aby odrzucić prywatny punkt końcowy
Możesz odrzucić prywatny punkt końcowy, który jest w stanie oczekiwania lub zatwierdzonym. Aby odrzucić, wykonaj następujące kroki: 

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby odrzucić prywatne punkty końcowe dla **domen**. 

1. Wybierz prywatny **punkt końcowy,** który chcesz odrzucić, a następnie wybierz **pozycję Odrzuć** na pasku narzędzi.

    ![Zrzut ekranu przedstawiający "Sieć — połączenia prywatnego punktu końcowego" z wybraną opcję "Odrzuć".](./media/configure-private-endpoints/reject-button.png)
1. W **oknie dialogowym Odrzucanie** połączenia wprowadź komentarz (opcjonalnie), a następnie wybierz pozycję **Tak.** 

    ![Prywatny punkt końcowy — odrzucanie](./media/configure-private-endpoints/reject.png)
1. Upewnij się, że stan punktu końcowego to **Odrzucono.** 

    ![Prywatny punkt końcowy — stan odrzucony](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Nie można zatwierdzić prywatnego punktu końcowego w Azure Portal po jego odrzuceniu. 


## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Aby utworzyć prywatny punkt końcowy, użyj metody [az network private-endpoint create,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Opisy parametrów używanych w przykładzie można znaleźć w dokumentacji polecenia [az network private-endpoint create.](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) W tym przykładzie należy zwrócić uwagę na kilka punktów: 

- W `private-connection-resource-id` przypadku usługi określ identyfikator zasobu **tematu** lub **domeny**. W poprzednim przykładzie użyto typu: temat.
- dla `group-ids` , określ lub `topic` `domain` . W poprzednim przykładzie jest `topic` używany . 

Aby usunąć prywatny punkt końcowy, użyj metody [az network private-endpoint delete,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby utworzyć prywatne punkty końcowe dla **domen**. 



### <a name="prerequisites"></a>Wymagania wstępne
Zaktualizuj rozszerzenie Azure Event Grid interfejsu wiersza polecenia, uruchamiając następujące polecenie: 

```azurecli-interactive
az extension update -n eventgrid
```

Jeśli rozszerzenie nie jest zainstalowane, uruchom następujące polecenie, aby je zainstalować: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
Aby utworzyć prywatny punkt końcowy, użyj metody [az network private-endpoint create,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Opisy parametrów używanych w przykładzie można znaleźć w dokumentacji polecenia [az network private-endpoint create.](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) W tym przykładzie należy zwrócić uwagę na kilka punktów: 

- W `private-connection-resource-id` przypadku usługi określ identyfikator zasobu **tematu** lub **domeny**. W poprzednim przykładzie użyto typu: temat.
- dla `group-ids` , określ lub `topic` `domain` . W poprzednim przykładzie jest `topic` używany . 

Aby usunąć prywatny punkt końcowy, użyj metody [az network private-endpoint delete,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) jak pokazano w poniższym przykładzie:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby utworzyć prywatne punkty końcowe dla **domen**. 

#### <a name="sample-script"></a>Przykładowy skrypt
Oto przykładowy skrypt, który tworzy następujące zasoby platformy Azure:

- Grupa zasobów
- Sieć wirtualna
- Podsieć w sieci wirtualnej
- Azure Event Grid tematu
- Prywatny punkt końcowy tematu

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby utworzyć prywatne punkty końcowe dla domen.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Zatwierdzanie prywatnego punktu końcowego
Poniższy przykładowy fragment kodu interfejsu wiersza polecenia pokazuje, jak zatwierdzić połączenie prywatnego punktu końcowego. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Odrzucanie prywatnego punktu końcowego
Poniższy przykładowy fragment kodu interfejsu wiersza polecenia pokazuje, jak odrzucić połączenie prywatnego punktu końcowego. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Wyłączanie dostępu do sieci publicznej
Domyślnie dostęp do sieci publicznej jest włączony dla Event Grid tematu lub domeny. Aby zezwolić na dostęp tylko za pośrednictwem prywatnych punktów końcowych, wyłącz dostęp do sieci publicznej, uruchamiając następujące polecenie:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Korzystanie z programu PowerShell
W tej sekcji przedstawiono sposób tworzenia prywatnego punktu końcowego dla tematu lub domeny przy użyciu programu PowerShell. 

### <a name="prerequisite"></a>Wymaganie wstępne
Postępuj zgodnie z instrukcjami z tematu How [to:](../active-directory/develop/howto-create-service-principal-portal.md) Use the portal to create an Azure AD application and service principal that can access resources to create an Azure Active Directory application (Instrukcje: Używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi), które mogą uzyskać dostęp do zasobów w celu utworzenia aplikacji usługi Azure Active Directory, i zanotuj wartości identyfikatorów katalogu **(dzierżawy),** identyfikatora aplikacji **(klienta)** i **tajnego (klienta) aplikacji**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Przygotowywanie tokenu i nagłówków dla wywołań interfejsu API REST 
Uruchom następujące polecenia wymagań wstępnych, aby uzyskać token uwierzytelniania do użycia z wywołaniami interfejsu API REST i autoryzacją oraz innymi informacjami nagłówka. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Tworzenie podsieci z wyłączonymi zasadami sieci punktu końcowego

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Tworzenie tematu usługi Event Grid z prywatnym punktem końcowym

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby utworzyć prywatne punkty końcowe dla **domen**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Po sprawdzeniu, czy punkt końcowy został utworzony, powinien zostać wyświetlony wynik podobny do następującego:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Zatwierdzanie połączenia z prywatnym punktem końcowym
Poniższy przykładowy fragment kodu programu PowerShell przedstawia sposób zatwierdzania prywatnego punktu końcowego. 

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby zatwierdzić prywatne punkty końcowe dla **domen**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Odrzucanie połączenia z prywatnym punktem końcowym
W poniższym przykładzie pokazano, jak odrzucić prywatny punkt końcowy przy użyciu programu PowerShell. Identyfikator GUID prywatnego punktu końcowego można uzyskać z wyniku poprzedniego polecenia GET. 

> [!NOTE]
> Kroki przedstawione w tej sekcji są związane z tematami. Podobne kroki można wykonać, aby odrzucić prywatne punkty końcowe dla **domen**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Połączenie można zatwierdzić nawet po jego odrzuceniu za pośrednictwem interfejsu API. Jeśli używasz Azure Portal, nie możesz zatwierdzić punktu końcowego, który został odrzucony. 

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat konfigurowania ustawień zapory adresów IP, zobacz Konfigurowanie zapory adresów IP dla [Azure Event Grid tematach lub domenach.](configure-firewall.md)
* Aby rozwiązać problemy z łącznością sieciową, zobacz [Rozwiązywanie problemów z łącznością sieciową](troubleshoot-network-connectivity.md)
