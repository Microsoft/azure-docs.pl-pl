---
title: Konfigurowanie zapory IP dla tematów Azure Event Grid lub domen
description: W tym artykule opisano sposób konfigurowania ustawień zapory dla Event Grid tematów lub domen.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 178b9d84ea8b2e0f764f7584526db8dbcf5284f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031841"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Konfigurowanie zapory IP dla tematów Azure Event Grid lub domen 
Domyślnie temat i domena są dostępne z Internetu, o ile żądanie zawiera prawidłowe uwierzytelnianie i autoryzację. Za pomocą zapory IP można ograniczyć ją tylko do zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (bez klas Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Wydawcy pochodzące z dowolnego innego adresu IP będą odrzucani i otrzymają odpowiedź 403 (zabroniony). Aby uzyskać więcej informacji na temat funkcji zabezpieczeń sieci obsługiwanych przez Event Grid, zobacz [zabezpieczenia sieci dla Event Grid](network-security.md).

W tym artykule opisano sposób konfigurowania ustawień zapory adresów IP dla tematów Azure Event Grid lub domen.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą Azure Portal utworzyć reguły zapory dla ruchu przychodzącego IP. Kroki przedstawione w tej sekcji dotyczą tematów. Możesz użyć podobnych kroków, aby utworzyć reguły protokołu IP dla ruchu przychodzącego dla **domen**. 

1. W [Azure Portal](https://portal.azure.com)przejdź do tematu lub domeny w usłudze Event Grid i przejdź do karty **Sieć** .
2. Wybierz opcję **sieci publiczne** , aby zezwolić na dostęp do zasobu do wszystkich sieci, w tym Internetu. 

    Ruch można ograniczyć przy użyciu reguł zapory opartych na protokole IP. Określ pojedynczy adres IPv4 lub zakres adresów IP w notacji CIDR (Classless Inter-Domain Routing). 

    ![Zrzut ekranu przedstawiający stronę "dostęp do sieci publicznej" z wybraną opcją "sieci publiczne".](./media/configure-firewall/public-networks-page.png)
3. Wybierz **tylko prywatne punkty końcowe** , aby zezwolić na dostęp do tego zasobu tylko prywatnym połączeniem punktu końcowego. Za pomocą karty **połączenia prywatne punktu końcowego** na tej stronie można zarządzać połączeniami. 

    ![Strona sieci publiczne](./media/configure-firewall/private-endpoints-page.png)
4. Wybierz pozycję **Zapisz** na pasku narzędzi. 



## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W tej sekcji pokazano, jak za pomocą poleceń interfejsu wiersza polecenia platformy Azure utworzyć tematy z regułami adresów IP dla ruchu przychodzącego. Kroki przedstawione w tej sekcji dotyczą tematów. Możesz użyć podobnych kroków, aby utworzyć reguły protokołu IP dla ruchu przychodzącego dla **domen**. 


### <a name="enable-or-disable-public-network-access"></a>Włącz lub wyłącz dostęp do sieci publicznej
Domyślnie dostęp do sieci publicznej jest włączony dla tematów i domen. Możesz również włączyć ją jawnie lub wyłączyć. Ruch można ograniczyć przez skonfigurowanie reguł zapory adresów IP dla ruchu przychodzącego. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Włącz dostęp do sieci publicznej podczas tworzenia tematu

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Wyłącz dostęp do sieci publicznej podczas tworzenia tematu

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Gdy dostęp do sieci publicznej jest wyłączony dla tematu lub domeny, ruch przez publiczny Internet nie jest dozwolony. Tylko połączenia z prywatnymi punktami końcowymi będą mogły uzyskiwać dostęp do tych zasobów. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Włącz dostęp do sieci publicznej dla istniejącego tematu

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Wyłącz dostęp do sieci publicznej dla istniejącego tematu 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Tworzenie tematu z pojedynczą regułą przychodzącego adresu IP
Następujące przykładowe polecenie interfejsu wiersza polecenia tworzy temat siatki zdarzeń z regułami adresów IP dla ruchu przychodzącego. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Tworzenie tematu z wieloma regułami adresów IP dla ruchu przychodzącego

Następujące przykładowe polecenie interfejsu wiersza polecenia tworzy temat dotyczący dwóch reguł protokołu IP dla ruchu przychodzącego w jednym kroku: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Aktualizowanie istniejącego tematu w celu dodania reguł protokołu IP dla ruchu przychodzącego
Ten przykład tworzy najpierw temat usługi Event Grid, a następnie dodaje reguły adresów IP dla ruchu przychodzącego dla tematu w osobnym poleceniu. Aktualizuje także reguły protokołu IP dla ruchu przychodzącego, które zostały ustawione w drugim poleceniu. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Usuń regułę protokołu IP dla ruchu przychodzącego
Następujące polecenie usuwa drugą regułę utworzoną w poprzednim kroku przez określenie tylko pierwszej reguły podczas aktualizowania ustawienia. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>Korzystanie z programu PowerShell
W tej sekcji pokazano, jak za pomocą poleceń Azure PowerShell utworzyć tematy Azure Event Grid z regułami zapory protokołu IP dla ruchu przychodzącego. Kroki przedstawione w tej sekcji dotyczą tematów. Możesz użyć podobnych kroków, aby utworzyć reguły protokołu IP dla ruchu przychodzącego dla **domen**. 

### <a name="prerequisites"></a>Wymagania wstępne
Postępuj zgodnie z instrukcjami, [Aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md) w celu utworzenia aplikacji Azure Active Directory i zanotować następujące wartości:

- Identyfikator katalogu (dzierżawcy)
- Identyfikator aplikacji (klienta)
- Wpis tajny aplikacji (klienta)

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Przygotuj token i nagłówki dla wywołań interfejsu API REST 
Uruchom następujące polecenia dotyczące wymagań wstępnych, aby uzyskać token uwierzytelniania do użycia z wywołaniami interfejsu API REST oraz informacje o autoryzacji i innych nagłówkach. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Włącz dostęp do sieci publicznej dla istniejącego tematu
Domyślnie dostęp do sieci publicznej jest włączony dla tematów i domen. Ruch można ograniczyć przez skonfigurowanie reguł zapory adresów IP dla ruchu przychodzącego. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Wyłącz dostęp do sieci publicznej dla istniejącego tematu
Gdy dostęp do sieci publicznej jest wyłączony dla tematu lub domeny, ruch przez publiczny Internet nie jest dozwolony. Tylko połączenia z prywatnymi punktami końcowymi będą mogły uzyskiwać dostęp do tych zasobów. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Tworzenie tematu siatki zdarzeń z regułami ruchu przychodzącego w jednym kroku

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Utwórz najpierw temat z siatką zdarzeń, a następnie Dodaj reguły przychodzącego adresu IP

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby rozwiązać problemy z łącznością sieciową, zobacz [Rozwiązywanie problemów z łącznością sieciową](troubleshoot-network-connectivity.md)
